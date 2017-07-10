



# 1. SurfaceFlinger
本文主要是对关键框架的提取，是对个人学习Surfaceflinger中产生的疑问的解答，是总结和提示性质的文档，具体细节还是要从文末参考连接中获取。

<!-- TOC -->

- [1. SurfaceFlinger](#1-surfaceflinger)
    - [1.1. Surfaceflinger是什么](#11-surfaceflinger是什么)
    - [1.2. Surfaceflinger有何用](#12-surfaceflinger有何用)
    - [1.3. 功能一：Vsync的产生和分发](#13-功能一vsync的产生和分发)
        - [1.3.1. Vsync产生](#131-vsync产生)
        - [1.3.2. SurfaceFlinger、HWComposwer](#132-surfaceflingerhwcomposwer)
        - [1.3.3. DispSync、DispSyncThread](#133-dispsyncdispsyncthread)
        - [1.3.4. DispSyncSource、EventThread、Connection](#134-dispsyncsourceeventthreadconnection)
    - [1.4. 功能二：图像的合成](#14-功能二图像的合成)
        - [1.4.1. HWC合成](#141-hwc合成)
        - [1.4.2. GPU合成](#142-gpu合成)
        - [1.4.3. BufferQueue](#143-bufferqueue)
    - [1.5. 功能三：设备的管理](#15-功能三设备的管理)
    - [1.6. 其他](#16-其他)
        - [1.6.1. 绘制性能](#161-绘制性能)
        - [1.6.2. 调试信息](#162-调试信息)
    - [1.7. 参考链接](#17-参考链接)

<!-- /TOC -->

## 1.1. Surfaceflinger是什么
Surfaceflinger是Android显示系统的核心服务，是整个显示过程承上启下的一环，这里先不讲Surfaceflinger有何作用，先看一下Surfaceflinger是怎么启动的。

Surfaceflinger由init进程创建，从pid可以验证：

```bash
E:\>adb shell ps  | grep surface
system    297   1     492636 10932 SyS_epoll_ 0000000000 S /system/bin/surfaceflinger

E:\>adb shell ps | grep init
root      1     0     29028  2032  SyS_epoll_ 0000000000 S /init

```

init启动surfaceflinger：
```
alps/frameworks/native/services/surfaceflinger/surfaceflinger.rc

service surfaceflinger /system/bin/surfaceflinger
    class core
    user system
    group graphics drmrpc readproc
    onrestart restart zygote
    writepid /sys/fs/cgroup/stune/foreground/tasks

```

Surfaceflinger的启动源码位于[main_surfaceflinger.cpp](http://mobileindex.rd.tp-link.net:8081/source/xref/xref_TP9XX_N/alps/frameworks/native/services/surfaceflinger/main_surfaceflinger.cpp)：
```cpp
int main(int, char**) {
    signal(SIGPIPE, SIG_IGN);
    // 1. 设置线程池容量为4，并启动
    // When SF is launched in its own process, limit the number of
    // binder threads to 4.
    ProcessState::self()->setThreadPoolMaxThreadCount(4);

    // start the thread pool
    sp<ProcessState> ps(ProcessState::self());
    ps->startThreadPool();

    // 2. 初始化surfaceflinger
    // instantiate surfaceflinger
    sp<SurfaceFlinger> flinger = new SurfaceFlinger();

    setpriority(PRIO_PROCESS, 0, PRIORITY_URGENT_DISPLAY);

    set_sched_policy(0, SP_FOREGROUND);

#ifdef ENABLE_CPUSETS
    // Put most SurfaceFlinger threads in the system-background cpuset
    // Keeps us from unnecessarily using big cores
    // Do this after the binder thread pool init
    set_cpuset_policy(0, SP_SYSTEM);
#endif

    // initialize before clients can connect
    flinger->init();

    // 3. 发布三个服务：surfaceflinger、GpuService、GuiExt service
    // publish surface flinger
    sp<IServiceManager> sm(defaultServiceManager());
    sm->addService(String16(SurfaceFlinger::getServiceName()), flinger, false);

    // publish GpuService
    sp<GpuService> gpuservice = new GpuService();
    sm->addService(String16(GpuService::SERVICE_NAME), gpuservice, false);

#ifdef MTK_AOSP_ENHANCEMENT
    // publish GuiExt service
    sp<GuiExtService> guiext = new GuiExtService();
    sm->addService(String16(GuiExtService::getServiceName()), guiext, false);
#endif

    // 4. 开始运行
    // run surface flinger in this thread
    flinger->run();

    return 0;
}

```

实际的surfaceflinger的init过程，以及具体实现的源码位置位于：alps/frameworks/native/services/surfaceflinger/SurfaceFlinger_hwc1.cpp，主要过程包括：
* 初始化OpenGL ES图形库。
* 创建显示设备的抽象代表，负责和显示设备打交道。
* 创建显示设备对象。
* 启动EventThread。监听和处理SurfaceFlinger中的事件。
* 设置软件VSync信号周期（如果没有hwc vsync）。
* 初始化显示设备，调用initializeDisplays完成。
* 启动开机动画。

## 1.2. Surfaceflinger有何用
Surfaceflinger作为Android显示系统的核心服务，承上启下的链接应用层和驱动层，完成绘制—合成—送显的过程。

以一个app界面的显示流程来看，是经过绘制-合成-送显三步，但如果从timeline的角度来看，这个过程是何时被触发、如何被组织的呢？
* HWC硬件发出Vsync信号
* Surfaceflinger收到Vsync信号，分发Vsync
* 应用进程通过Binder收到Surfaceflinger的Vsync信号，开始绘制图像
* Surfaceflinger主线程根据Vsync信号，开始合成图像

Vsync信号每隔16ms触发一次，绘制和合成都在收到Vsync同时开始做，因而：
* 第n次Vsync时，应用进程绘制的图像Gn
* 在第n+1次Vsync时，由Surfaceflinger将图像Gn合成并显示到屏幕

结合上述过程，Surfaceflinger角色和功能包括有：
* 节奏控制：Vsync信号产生、分发
* 合成图像：收集应用绘制好的Layer，合成送显
* 设备管理：DisplayDevice的管理

## 1.3. 功能一：Vsync的产生和分发


### 1.3.1. Vsync产生
**Vsync由HWC硬件产生，并由Surfaceflinger进程处理并分发，应用进程的Choreographer是被动接受Surfaceflinger传来的Vsync，开始进行绘制**，函数调用流程如下，详细可参见：[完整走一遍](http://blog.csdn.net/lewif/article/details/50573397#完整走一遍)

```
HWComposer::vsync
——SurfaceFlinger.onVSyncReceived()
——bool DispSync::addResyncSample(nsecs_t timestamp)
——void DispSync::updateModelLocked()
——mThread->updateModel(mPeriod, mPhase);——>mCond.signal();
——DispSyncThread::threadloop()
——fireCallbackInvocations(callbackInvocations);
——DispSyncSource::onDispSyncEvent(nsecs_t when)——>callback->onVSyncEvent(when);
——void EventThread::onVSyncEvent(nsecs_t timestamp)——>mCondition.broadcast();
——bool EventThread::threadLoop()——>signalConnections = waitForEvent(&event);
——EventThread::waitForEvent 解除阻塞  dispatch events to listeners...
——status_t err = conn->postEvent(event);
——MessageQueue::waitMessage()——>int32_t ret = mLooper->pollOnce(-1);
——int Looper::pollInner(int timeoutMillis)
——response.request.callback->handleEvent
——int MessageQueue::cb_eventReceiver(int fd, int events, void* data)；
——int MessageQueue::eventReceiver(int fd, int events)
——void SurfaceFlinger::onMessageReceived(int32_t what) 最终通过handler调用到surfaceflinger的主线程进行refresh
——void SurfaceFlinger::handleMessageRefresh()：

```

注：上述流程从EventThread::threadLoop()的处理开始，后面的调用是沿着Vsync传播到Surfaceflinger主线程进行的；另一条Vsync传播到 应用进程的路也差不多。



下面根据关键类和对象，详述该过程

### 1.3.2. SurfaceFlinger、HWComposwer 

* Surfaceflinger负责系统HWC硬件的启动，Surfaceflinger通过静态库方式加载HWC的HAL模块，达到控制HWC硬件的目的
* HWC模块则完成最原始的VSync信号的捕获，当硬件产生Vsync信号时，会调用SurfaceFlinger.onVSyncReceived()来处理Vsync信号。

```cpp
void SurfaceFlinger::onVSyncReceived(int type, nsecs_t timestamp) {
    bool needsHwVsync = false;

    { // Scope for the lock
        Mutex::Autolock _l(mHWVsyncLock);
        if (type == 0 && mPrimaryHWVsyncEnabled) {
            needsHwVsync = mPrimaryDispSync.addResyncSample(timestamp);
        }
    }

    if (needsHwVsync) {
        enableHardwareVsync();
    } else {
        disableHardwareVsync(false);
    }
}

```


mPrimaryDispSync.addResyncSample(timestamp)函数，将VSync信号的时间戳保存到数组mResyncSamples中。然后调用了updateModelLocked函数继续分发VSync信号。


### 1.3.3. DispSync、DispSyncThread 
* DispSync是对硬件Vsync信号的抽象，SF将捕获到的Vsync信号封装在DispSync里面（VSync信号源），并触发DispSyncThread对DispSync进行处理
* DispSyncThread对Vsync信号（封装为DispSync）进行第一次分发，通过fireCallbackInvocations将Vsync发送到各个DispSyncSource

```cpp
    void fireCallbackInvocations(const Vector<CallbackInvocation>& callbacks) {
        if (kTraceDetailedInfo) ATRACE_CALL();
        for (size_t i = 0; i < callbacks.size(); i++) {
            callbacks[i].mCallback->onDispSyncEvent(callbacks[i].mEventTime);
        }
    }

```
### 1.3.4. DispSyncSource、EventThread、Connection
* DispSyncSource代表一个虚拟的软件Vsync。
* EventThread与DispSyncSource一一对应，EventThread线程用于对DispSyncSource这个虚拟Vsync进行二次分发。

硬件Vsync只有一个，是HWC触发的。当Vsync到来时，有太多的工作要做，因此Android 4.4(KitKat)引入了VSync的虚拟化，即把硬件的VSync信号先同步到一个本地VSync模型中，再从中一分为二，引出两条VSync时间与之有固定偏移的线程。示意图如下：

![Vsync分支](http://img.blog.csdn.net/20131213012814218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluemh1b2p1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

[DispSyncSource与offset机制](http://blog.csdn.net/jinzhuojun/article/details/17293325)：DispSync根据特定屏幕硬件的vsync信号创建一个模型，利用这个模型去做定期的回调工作，回调的时间比vsync信号的到来稍晚一点，有个偏移量。为何要有个偏移量呢？因为这个模型会驱动多个事件，例如SF去合成图形，如果多个事件一起工作就会出现抢占cpu。所以对每个事件的偏移量设置为不同的，就能减缓这种问题的存在。
MTK平台上偏移量实际上是设置为0的，多核CPU上，不存在抢占的问题，两个EventThread会分配到多个CPU上同时执行 

Vsync被一分为二后，对应两个DispSyncSource：
* 一个用于触发所有应用进程的绘制，对应的EventThread为mEventThread；
* 一个用于触发Surfaceflinger的合成工作，对应的EventThread为mSFEventThread;
* 两个DispSyncSource与EventThread均由Surfaceflinger在init时创建

```cpp
void SurfaceFlinger::init() {
    ……
    // start the EventThread
    sp<VSyncSource> vsyncSrc = new DispSyncSource(&mPrimaryDispSync,
            vsyncPhaseOffsetNs, true, "app");
    mEventThread = new EventThread(vsyncSrc, *this);
    sp<VSyncSource> sfVsyncSrc = new DispSyncSource(&mPrimaryDispSync,
            sfVsyncPhaseOffsetNs, true, "sf");
    mSFEventThread = new EventThread(sfVsyncSrc, *this);
    mEventQueue.setEventThread(mSFEventThread);

```

现在总体来看Vsync信号的产生和传播如下：

![Vsync传播](http://img.blog.csdn.net/20160316223336218)

整个过程总结如下：
* HWC硬件模块产生Vsync
* Surfaceflinger进程是实际处理Vsync传播的进程
* DispSync是描述Vsync的类
* DispSyncSource是对虚拟Vsync的描述类，每一个DispSyncSource代表一个虚拟Vsync，虚拟Vsync由硬件Vsync信号分流而来
* DispSyncThread用于将DispSync实际分流成两个DispSyncSource（app-vsync和sf-vsync，一个传播到应用进程，一个传播到Surfaceflinger主线程）
* EventThread用于分发DisplaySyncSource这个虚拟Vsync（到应用进程和Surfaceflinger主线程）
* Vsync主要传播到两个地方：应用进程收到Vsync开始绘制、Surfaceflinger主线程收到Vsync开始合成图像


其他的关键类和对象：
* EventControlThread：控制Vsync信号的开关。Surfaceflinger通过其enable和disable vsync
* MessageQueue：消息分发，实例就是mEventQueue，接受mSFEventThread分发的Vsync，在Surfaceflinger主线程的handler-looper中合成过程。当然也用于接受处理其他消息。
* DisplayHardware/HWComposer_hwc1.cpp：HWComposer对象，硬件合成模块。用于产生vsync，图像合成过程最终也会调用到HWC中。
* DisplayDevice：是显示设备的抽象，保存了所有需要显示在本显示设备中显示的Layer对象，也保存了和显示设备相关的显示方向、显示区域坐标等信息



## 1.4. 功能二：图像的合成

从对Vsync信号的描述知道，Vsync信号被Surfaceflinger一分为二：
* vsync-app，通过binder传递到应用进程，触发应用进程的Choreographer完成界面绘制
* vsync-sf，传递到surfaceflinger主线程，触发主线程完成图像的合成

界面绘制过程是由应用进程完成的，不属于surfaceflinger的工作，这里不做描述，下面说明一下Surfaceflinger如何进行图像的合成。

Vsync信号产生后，经过mSFEventThread发送给surfaceflinger主线程用于触发每帧的图像合成。
SurfaceFlinger采用Commander设计模式，SurfaceFlinger主线程接受消息，形成消息队列，逐个处理消息队列中的信息。 
因此直接从onMessageReceived看起:

```cpp
void SurfaceFlinger::onMessageReceived(int32_t what) {
    ATRACE_CALL();
    switch (what) {
    case MessageQueue::TRANSACTION:
        handleMessageTransaction();
        break;
    case MessageQueue::INVALIDATE:
        handleMessageTransaction();
        handleMessageInvalidate();
        signalRefresh();
        break;
    case MessageQueue::REFRESH:
        handleMessageRefresh();
        break;
    }
}

```
整个过程会先后调用handleMessageInvalidate和handleMessageRefresh

```cpp

bool SurfaceFlinger::handleMessageTransaction() {
    uint32_t transactionFlags = peekTransactionFlags(eTransactionMask);
    if (transactionFlags) {
        handleTransaction(transactionFlags);
        return true;
    }
    return false;
}

bool SurfaceFlinger::handleMessageInvalidate() {
    ATRACE_CALL();
#ifdef MTK_AOSP_ENHANCEMENT
    // This mutex may be held by dump function, so stop monitor temporarily.
    mMessageWDT.delAnchor();
    Mutex::Autolock _l(mDumpLock);
    mMessageWDT.setAnchor(String8::format("reset anchor by [%s]", __func__));
#endif
    /*
    return handlePageFlip();
}

void SurfaceFlinger::handleMessageRefresh() {
    /*Systrace，打该函数时间*/
    ATRACE_CALL();
    ……
    /*调Layer的onPreComposition方法，主要是标志一下Layer已经被用于合成*/
    preComposition();
    /*若Layer的位置/先后顺序/可见性发生变化，重新计算Layer的目标合成区域和先后顺序*/
    rebuildLayerStacks();
    /*配置硬件合成器，调hwc的prepare方法*/
    setUpHWComposer();
    /*当打开开发者选项中的“显示Surface刷新”时，额外为产生变化的图层绘制闪烁动画*/
    doDebugFlashRegions();
    /*执行合成主体，对3D合成而言，调opengl的drawcall，对硬件合成而言，调hwc的set方法*/
    doComposition();
    /*主要用于调试，调Layer的onPostComposition方法*/
    postComposition();
    ……
}

```

handleMessageRefresh中的合成过程，这里不做细致描述，整个合成流程可以归纳到如下图示：

![图片合成](http://image87.360doc.com/DownloadImg/2015/07/1319/56005693_3)

图像合成分两种情况：GPU合成、HWC硬件合成。两者的关系在在[Android显示系统概述](http://mobileprj.rd.tp-link.net/redmine/boards/56/topics/50645)中有描述。根据图中的流程分别看一下两种合成方式的流程。

### 1.4.1. HWC合成
HWC合成（也叫Overlay方式），是图示中上方的一条路径：
* 应用进程作为生产者，调用queuebuffer，将绘制完的GraphicBuffer放回BufferQueue
* Surfaceflinger作为消费者，调用aquireBuffer，从BufferQueue中取出GraphicBuffer
* Surfaceflinger直接将取到的buffer handle放到LayerList
* HWC将LayerList中的Layer合成，送显

支持HWC硬件的系统，默认使用HWC合成（除非是超出HWC可处理的层数，或者有比较复杂变换的合成），抓取合成过程的systrace如下：

trace-hwc图

图中可以看到在handleMessageRefresh中doCompostion方法中，主要是postFramebuffer将buffer commit到HWC中，Surfaceflinger的合成工作就完成了。剩下的就是HWC硬件模块合成送显的过程。

### 1.4.2. GPU合成
GPU合成，是Surfaceflinger使用GPU完成图像合成的过程，此时Surfaceflinger既是消费者也是生产者：
* 应用进程作为生产者，调用queuebuffer，将绘制完的GraphicBuffer放回BufferQueue
* Surfaceflinger作为消费者，调用aquireBuffer，从BufferQueue中取出GraphicBuffer
* Surfaceflinger调用glEGLImageTexture2DOES()调用GPU将Buffer进行合成
* Surfaceflinger再作为生产者，将合成后的Buffer，通过queueBuffer放入BufferQueue
* onFrameAvailable方法被触发，FrameBufferSurface作为消费者从BufferQueue中取出GPU合成的Buffer
* 通过onFrameCommited方式将GPU合成的Buffer提交到HWC的LayerList
* HWC将LayerList中的Layer合成，送显
* 注意：GPU合成的Buffer在BufferQueue中的名字比较特殊，为FrameBufferTarget

在Developer-Options中，打开停用HW叠加层，可以强制使用GPU合成，此时抓取合成过程的systrace：

trace-gpu图

图中可以看到，doComposition方法更加复杂，doComposition中调用了eglSwapBuffersWithDamageKHR，由GPU进行图像合成，最后再调用postFrameBuffer交由HWC硬件处理。


### 1.4.3. BufferQueue
BufferQueue是连接绘制与合成，合成与送显的关键对象
* 在APP端，每个窗口被表示为Surface
* 在Surfaceflinger，每个窗口被表示为Layer
* Surface与Layer是一一对应的，描述的显示的窗口
* APP端的Surface，由WMS统一管理
* SurfaceControl是WMS与SurfaceFlinger交互的核心结构

确认了Surface、Layer的关系后，再来看Buffer。Buffer是实际承载显示内容的，当一个窗口内容在变化时（如滑动操作），其Surface和Layer仍然表示该窗口，但其对应的Buffer内容需要重新填充。因此一个Buffer是无法和一个窗口永久绑定的，Android采用了循环Buffer机制来实现对Buffer的高效利用。

一个Layer（或者Surface）可以对应多个Buffer：
* single-buffer，一个窗口只有一个Buffer，先将内容绘制到buffer中，再将Buffer内容显示到屏幕上。一旦显示的时候Buffer的内容还没有绘制完，就会出现撕裂现象。
* double-buffer，对于一个窗口（Layer/Surface）来说，一个buffer用于送显，一个Buffer用于绘制。当绘制完成后，两个Buffer的内容进行互换，防止出现撕裂现象。但如果绘制（fps）和屏幕的刷新速度（合成送显）频率不一致时，容易出现Janky
* triple-buffer，一个窗口有三个Buffer，两个用于绘制，一个用于显示，可以减缓Janky现象，具体可参见参考链接。Android在4.1版本引入triple-buffer机制。

通过adb shell dumpsys SurfaceFlinger 可以看到：
```
+ Layer 0x7ee95d8000 (StatusBar)
  Region transparentRegion (this=0x7ee95d82e0, count=1)
    [  0,   0,   0,   0]
  Region visibleRegion (this=0x7ee95d8010, count=1)
    [  0,   0, 720,  48]
  Region surfaceDamageRegion (this=0x7ee95d8088, count=1)
    [  0,   0,   0,   0]
      layerStack=   0, z=   161000, pos=(0,0), size=( 720,  48), crop=(   0,   0, 720,  48), finalCrop=(   0,   0,  -1,  -1), isOpaque=0, invalidate=0, alpha=0xff, flags=0x00000000, tr=[1.00, 0.00][0.00, 1.00]
      client=0x7eec424600
      format= 1, activeBuffer=[ 720x  48: 720,  1], queued-frames=0, mRefreshPending=0
      mSecure=0, mProtectedByApp=0, mFiltering=0, mNeedsFiltering=0
            mTexName=6 mCurrentTexture=2
            mCurrentCrop=[0,0,0,0] mCurrentTransform=0
            mAbandoned=0
            -BufferQueue mMaxAcquiredBufferCount=1, mMaxDequeuedBufferCount=2, mDequeueBufferCannotBlock=0 mAsyncMode=0, default-size=[720x48], default-format=1, transform-hint=00, FIFO(0)={}
             this=0x7ee95fb800 (mConsumerName=StatusBar, mConnectedApi=1, mConsumerUsageBits=0x900, mId=8, mPid=297, producer=[1230:com.android.systemui], consumer=[297:/system/bin/surfaceflinger])
             [00:0x7eec438800] state=DEQUEUED, 0x7ee8123ac0 [ 720x  48: 720,  1]
            >[02:0x7eec438600] state=ACQUIRED, 0x7ee7397540 [ 720x  48: 720,  1]
             [01:0x7eec438a00] state=FREE    , 0x7eec424540 [ 720x  48: 720,  1]
                *BufferQueueDump mIsBackupBufInited=0, mAcquiredBufs(size=1), mMode=TRACK_CONSUMER
                 [00] handle=0x7ee7397540, fence=0x7eec41a0b0, time=0xa97e46d54a31, xform=0x00
```
triple-buffer使每个Layer有三个buffer，一个用于显示（ACQUIRED）、一个空闲（FREE）、一个用于绘制（DEQUEUED）

Buffer对应的是共享内存，因此APP与Surfaceflinger，Surfaceflinger与HWC之间，在传递Buffer时都是传递Buffer handle，而不会真正的进行Buffer的拷贝，这样的通信方式是非常高效的。



## 1.5. 功能三：设备的管理
DisplayDevice是显示设备的抽象，定义了3中类型的显示设备。
* DISPLAY_PRIMARY：主显示设备，通常是LCD屏
* DISPLAY_EXTERNAL：扩展显示设备。通过HDMI输出的显示信号
* DISPLAY_VIRTUAL：虚拟显示设备，通过WIFI输出信号
这3钟设备，第一种是基本配置，另外两种需要硬件支持。默认情况下系统中是只有第一种的。

SurfaceFlinger中需要显示的图层（layer）将通过DisplayDevice对象传递到OpenGLES中进行合成，合成之后的图像再通过HWComposer对象传递到Framebuffer中显示。DisplayDevice对象中的成员变量mVisibleLayersSortedByZ保存了所有需要显示在本显示设备中显示的Layer对象，同时DisplayDevice对象也保存了和显示设备相关的显示方向、显示区域坐标等信息。

显示设备是在surfaceflinger的init函数中创建的：

```cpp
    // initialize our non-virtual displays
    for (size_t i=0 ; i<DisplayDevice::NUM_BUILTIN_DISPLAY_TYPES ; i++) {
        DisplayDevice::DisplayType type((DisplayDevice::DisplayType)i);
        // set-up the displays that are already connected
        if (mHwc->isConnected(i) || type==DisplayDevice::DISPLAY_PRIMARY) {
            // All non-virtual displays are currently considered secure.
            bool isSecure = true;
            createBuiltinDisplayLocked(type);//给显示设备分配一个token 
            wp<IBinder> token = mBuiltinDisplays[i];

            sp<IGraphicBufferProducer> producer;
            sp<IGraphicBufferConsumer> consumer;
            BufferQueue::createBufferQueue(&producer, &consumer,
                    new GraphicBufferAlloc());

            sp<FramebufferSurface> fbs = new FramebufferSurface(*mHwc, i,
                    consumer);
            int32_t hwcId = allocateHwcDisplayId(type);
            sp<DisplayDevice> hw = new DisplayDevice(this,
                    type, hwcId, mHwc->getFormat(hwcId), isSecure, token,
                    fbs, producer,
                    mRenderEngine->getEGLConfig());
            if (i > DisplayDevice::DISPLAY_PRIMARY) {
                // FIXME: currently we don't get blank/unblank requests
                // for displays other than the main display, so we always
                // assume a connected display is unblanked.
                ALOGD("marking display %zu as acquired/unblanked", i);
                hw->setPowerMode(HWC_POWER_MODE_NORMAL);
            }
            mDisplays.add(token, hw);//把显示设备对象保存在mDisplays列表中  
        }
    }

```

所有显示设备的输出都要通过HWComposer对象完成，因此上面这段代码先调用了HWComposer的isConnected来检查显示设备是否已连接，只有和显示设备连接的DisplayDevice对象才会被创建出来。即使没有任何物理显示设备被检测到，SurfaceFlinger都需要一个DisplayDevice对象才能正常工作，因此，DISPLAY_PRIMARY类型的DisplayDevice对象总是会被创建出来。
createBuiltinDisplayLocked函数就是为显示设备对象创建一个BBinder类型的Token而已。


## 1.6. 其他

### 1.6.1. 绘制性能
打开Develop Options——GPU呈现模式分析时，屏幕上会出现条形图，描述了每一帧的性能：
* Draw (蓝色) 代表着View#onDraw()方法。在这个环节会创建/刷新DisplayList中的对象，这些对象在后面会被转换成GPU可以明白的OpenGL命令。而这个值比较高可能是因为view比较复杂，需要更多的时间去创建他们的display list，或者是因为有太多的view在很短的时间内被创建。
* Prepare (紫色) – 在Lollipop版本中，一个新的线程被加入到了UI线程中来帮助UI的绘制。这个线程叫作RenderThread。它负责转换display list到OpenGL命令并且送至GPU。在这过程中，UI线程可以继续开始处理后面的帧。而在UI线程将所有资源传递给RenderThread过程中所消耗的时间，就是紫色阶段所消耗的时间。如果在这过程中有很多的资源都要进行传递，display list会变得过多过于沉重，从而导致在这一阶段过长的耗时。
* Process (红色) – 执行Display list中的内容并创建OpenGL命令。如果有过多或者过于复杂的display list需要执行的话，那么这阶段会消耗较长的时间，因为这样的话会有很多的view被重绘。而重绘往往发生在界面的刷新或是被移动出了被覆盖的区域。
* Execute (黄色) – 发送OpenGL命令到GPU。这个阶段是一个阻塞调用，因为CPU在这里只会发送一个含有一些OpenGL命令的缓冲区给GPU，并且等待GPU返回空的缓冲区以便再次传递下一帧的OpenGL命令。而这些缓冲区的总量是一定的，如果GPU太过于繁忙，那么CPU则会去等待下一个空缓冲区。所以，如果我们看到这一阶段耗时比较长，那可能是因为GPU过于繁忙的绘制UI，而造成这个的原因则可能是在短时间内绘制了过于复杂的view。

**这里要说明的是：这里的条形图描述的是应用绘制的性能，Draw-Prepare-Process—Execute都是应用进程里完成的，与Surfaceflinger的合成过程完全无关！！！**
* 如果页面元素很多很复杂，那么应用进程生成的DisplayList也会更大，GPU将繁多的内容绘制到最终参与合成的buffer耗时就会很长，所以可能出现性能问题的是绘制过程
* 合成仅仅是将绘制好的Layer合成最终显示结果，无论页面元素有多复杂，最终也就是绘制到几个Layer之中，合成的耗时基本是一样的，不会有性能问题

### 1.6.2. 调试信息
使用`adb shell dumpsys SurfaceFlinger`命令可以查看Surfaceflinger相关信息，包括：
* Buffer信息、Layer信息
* HWC信息、硬件设备信息
* Vsync信息等

```
……
Hardware Composer state (version 01050000):
  mDebugForceFakeVSync=0
  Display[0] configurations (* current):
    * 0: 720x1280, xdpi=294.967010, ydpi=295.562988, refresh=17193947, colorTransform=0
  numHwLayers=4, flags=00000000
    type   |  handle  | hint | flag | tr | blnd |   format    |     source crop (l,t,r,b)      |          frame         | name 
-----------+----------+------+------+----+------+-------------+--------------------------------+------------------------+------
       HWC | 7eec424840 | 0002 | 0000 | 00 | 0100 | RGBx_8888   |    0.0,    0.0,  720.0, 1280.0 |    0,    0,  720, 1280 | com.android.systemui.ImageWallpaper
       HWC | 7eec424900 | 0002 | 0000 | 00 | 0105 | RGBA_8888   |    0.0,    0.0,  720.0, 1280.0 |    0,    0,  720, 1280 | com.cyanogenmod.trebuchet/com.android.launcher3.Launcher
       HWC | 7eec424540 | 0002 | 0000 | 00 | 0105 | RGBA_8888   |    0.0,    0.0,  720.0,   48.0 |    0,    0,  720,   48 | StatusBar
 FB TARGET | 7eecbe5ac0 | 0000 | 0000 | 00 | 0105 | RGBA_8888   |    0.0,    0.0,  720.0, 1280.0 |    0,    0,  720, 1280 | HWC_FRAMEBUFFER_TARGET

[HWC Features Support]
  ext=0 vir=1 enhance=1 svp=0 s3d=1 s3d_debug=-1 s3d_depth=0 aod=0

……

```



## 1.7. 参考链接

* [Android Source Graphics](https://source.android.com/devices/graphics/)
* [Android垂直同步信号VSync的产生及传播结构详解](http://blog.csdn.net/houliang120/article/details/50908098)
* [android graphic(4)—surfaceflinger和Vsync](http://blog.csdn.net/lewif/article/details/50573397)
* [Android 4.4(KitKat)中VSync信号的虚拟化](http://blog.csdn.net/jinzhuojun/article/details/17293325)
* [Android6.0 显示系统（五） SurfaceFlinger服务](http://blog.csdn.net/kc58236582/article/details/52763534)
[Android6.0 显示系统（六） 图像的输出过程](http://blog.csdn.net/kc58236582/article/details/52778333)
* [Android中的graphic buffer同步机制](http://www.360doc.com/content/15/0713/19/10366845_484707701.shtm)
* [Android中的SurfaceFlinger和Choreographer](http://www.jianshu.com/p/c046fcd125d2)
* [Android的窗口管理系统](http://www.360doc.com/content/14/0329/00/10366845_364576441.shtml)
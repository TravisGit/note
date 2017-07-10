
继承和多态


基类
[platform_device](http://androidxref.com/kernel_3.18/xref/include/linux/platform_device.h)
struct platform_device {
	const char	*name;
	int		id;
	bool		id_auto;
	struct device	dev;
	u32		num_resources;
	struct resource	*resource;

	const struct platform_device_id	*id_entry;
	char *driver_override; /* Driver name to force a match */

	/* MFD cell pointer */
	struct mfd_cell *mfd_cell;

	/* arch specific additions */
	struct pdev_archdata	archdata;
}; 

[container_of](http://androidxref.com/kernel_3.18/xref/include/linux/kernel.h#795)

container_of(ptr, type, member)

这里member不能为指针，否则传值过程中就丢失了原有的member地址，无法正确使用container_of



Java 
class device{

}
class platform_device extends device{

}

device xx_device = new platform_device()




模板
参见内核list的实现（各种数据结构和算法也是在此基础上，能通用到各个模块）


最后，高级语言的特性，其根本原理是什么？虚表？编译？

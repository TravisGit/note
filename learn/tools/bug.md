

## shell脚本中空格很重要
```
if '[ -f ~/.tmux/.tmux.conf.local]' 'source ~/.tmux/.tmux.conf.local'         #wrong
if '[ -f ~/.tmux/.tmux.conf.local ]' 'source ~/.tmux/.tmux.conf.local'        #correct
```


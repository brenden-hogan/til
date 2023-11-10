Modifies the bind key in tmux to a new key in this case ctrl-space

```
unbind-key C-b
set-option -g prefix C-Space
bind-key C-Space send-prefix
```

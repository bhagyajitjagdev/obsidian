#### Create the `.inputrc` file:
```bash
touch ~/.inputrc
```

#### Add these lines to it:
```bash
"\e[A": history-search-backward
"\e[B": history-search-forward
set show-all-if-ambiguous on
set completion-ignore-case on
```

#### Make sure these lines are in your `~/.bashrc` file:
```bash
HISTSIZE=10000
HISTFILESIZE=20000
shopt -s histappend
```

#### Restart terminal:
```bash
source ~/.bashrc
```

___Note: Better to close the terminal and open a new ssh session___

___if you previously ran `sudo apt update` and type `sudo` then press the up arrow, it will show that command.___

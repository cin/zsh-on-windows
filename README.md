# a journey to powerline on zsh, tmux, and vim on windows in hyper terminal
<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [overview](#overview)
- [install zsh in bash on ubuntu](#install-zsh-in-bash-on-ubuntu)
- [set zsh as the default shell](#set-zsh-as-the-default-shell)
- [install powerline fonts](#install-powerline-fonts)
- [install hyper](#install-hyper)
- [install latest python](#install-latest-python)
- [ensure tmux and conf files are in their right place!!! it's weird!](#ensure-tmux-and-conf-files-are-in-their-right-place-its-weird)
- [hyper config](#hyper-config)
- [pip install powerline and the powerlevel9k theme](#pip-install-powerline-and-the-powerlevel9k-theme)
- [install Vundle](#install-vundle)
- [YAY...at this point things are basically working...but not YouCompleteMe](#yayat-this-point-things-are-basically-workingbut-not-youcompleteme)
- [build vim](#build-vim)
- [build vim with python bc that didn't work with ycm](#build-vim-with-python-bc-that-didnt-work-with-ycm)
	- [how it really went down...](#how-it-really-went-down)
- [need to start ycmd](#need-to-start-ycmd)
- [thanks!](#thanks)

<!-- /TOC -->

## overview
after undergoing a terminal/workflow setup shakeup on my mac, i decided to see how far windows has come in this regard. bash on ubunto on windows is getting better and better; but i just learned about [hyper](https://hyper.is) and it's cool so far. while many of the steps in here involve `hyper`, their effects can be seen in the regular `Bash on Ubunu on Windows` shells as well.

it is important to note that `/` can be found in windows explorer by navigating to `%localappdata%\lxss`. most configs will go under `/root` (except `.hyper.js` so far).

## install zsh in bash on ubuntu
`apt install -y zsh tmux`

## set zsh as the default shell
`chsh -s $(which zsh)`

## install powerline fonts
I did it in both windows system and through ps1 -- may not have been necessary...was thinking this would be required to get the `Bash on Ubuntu` shell to work.
```
git clone https://github.com/powerline/fonts.git
cd fonts
./install.sh
```

## install hyper
[hyper](https://hyper.is). install the [latest windows version](https://releases.hyper.is/download/win).

## install latest python
install the latest python if you don't already have it.
```
# optional: remove python3.4 stuff
apt remove python3.4 python3.4-minimal python3.4-dev python3-pip

add-apt-repository ppa:deadsnakes/ppa
apt update
apt install -y python3.6
# install pip3
wget https://bootstrap.pypa.io/get-pip.py
ln -s /usr/bin/python3.6 /usr/bin/python3
```

## ensure tmux and conf files are in their right place!!! it's weird!
[%homepath%\.hyper.js](.hyper.js)
please don't laugh at my .vimrc config, it's already bad enough that it's mostly/all from the Vundle example.
[%localappdata%\lxss\root\.vimrc](.vimrc)
my `.tmux.conf` file is mostly from [here](.tmux.conf). had to disable the mouse and maybe some other things.
[%localappdata%\lxss\root\.tmuf.conf](.tmux.conf)

## hyper config
Be mindful of how `shell` and `shellArgs` are set. This will change depending on what version of windows you have. The install guide has more information on this.

*TODO*: I picked Fira because I think it has font ligature support. Have not validated that it's working yet.

*TODO* would displaying a patch file here be better?
```
fontFamily: '"Fira Mono for Powerline", Menlo, "DejaVu Sans Mono", Consolas, "Lucida Console", monospace',

shell: 'C:\\Windows\\System32\\cmd.exe',
shellArgs: ['--login', '-i', '/c wsl'],

plugins: [
  'hyper-ligatures',
  'hyper-material-theme',
  'hyper-search',
  'hyper-color-command'
],
```

## pip install powerline and the powerlevel9k theme
```
pip3 install Markdown powerline-status
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

## install Vundle
```
mkdir -p ~/.vim/bundle
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

## YAY...at this point things are basically working...but not YouCompleteMe
[more info](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)

## build vim
you may want to uninstall all versions of `vim` already present on your system. if you do, just remove the vim related packages.
```
(optional) # is this even safe?
apt remove $(apt list --installed | grep vim)
```

vim needs a terminal, and hinted at ncurses as a default. *is this right?* saw another gtk2 ref and am going to try it out and see what happens. also note that Vundle is being used as a plugin package manager for `vim`.
```
apt install -y libncurses5-dev libncursesw5-dev
git clone https://github.com/vim/vim.git
pushd vim
make
make install
popd
vim +PluginInstall +qall
```

## build vim with python bc that didn't work with ycm
```
# export VIM_HOME=/mnt/e/code/vim
pushd $VIM_HOME
./configure --with-features=huge \
  --enable-multibyte \
  --enable-rubyinterp=yes \
  --enable-pythoninterp=yes \
  --with-python-config-dir=/usr/lib/python2.7/config \
  --enable-python3interp=yes \
  --with-python3-config-dir=/usr/lib/python3.6/config \
  --enable-perlinterp=yes \
  --enable-luainterp=yes \
  --enable-cscope \
  --prefix=/usr/local
make VIMRUNTIMEDIR=/usr/local/share/vim/vim80
make install
popd
```

### how it really went down...

instead of `make install`, i tried to use `checkinstall`.
```
apt install -y checkinstall
checkinstall
```

`Copying files to the temporary directory` hung for nearly 10 minutes before i killed it. after that, i just `make install`ed.

```
...
======================== Installation successful ==========================

Some of the files created by the installation are inside the build
directory: /mnt/e/code/vim

You probably don't want them to be included in the package,
especially if they are inside your home directory.
Do you want me to list them?  [n]:
Should I exclude them from the package? (Saying yes is a good idea)  [y]:

Copying files to the temporary directory...
^C
*** SIGINT received ***

Restoring overwritten files from backup...OK

Cleaning up...OK

Bye.

root@rush  /mnt/e/code/vim   master v8.0.1795 ?  make install                                       1 ↵  ⚡  594  01:45:27
```

## need to start ycmd
vim starts but ycm is having issues. need to fix this.

```
apt install cmake3
pushd ~/.vim/bundle/youcompleteme
./install.py --go-completer --rust-completer --java-completer
popd
```

## thanks!
many thanks to these projects:
+ [powerline](https://powerline.readthedocs.org/en/latest)
  + [Powerline GitHub](https://github.com/powerline/powerline)
+ [how-to-install-zsh-and-oh-my-zsh-on-windows-10](https://evdokimovm.github.io/windows/zsh/shell/syntax/highlighting/ohmyzsh/hyper/terminal/2017/02/24/how-to-install-zsh-and-oh-my-zsh-on-windows-10.html)
+ [tmux-and-vim](https://blog.bugsnag.com/tmux-and-vim/)

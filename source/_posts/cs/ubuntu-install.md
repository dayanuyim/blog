---
title: Ubuntu Install
date: 2024-06-11 11:06:14
categories:
tags:
  - ubuntu
  - ubuntu 24.04
---

(Update: 2024/05/29)

### Utilities

#### Common utils

```sh
sudo apt install -y net-tools software-properties-common openssh-server plocate moreutils curl git vim neovim tmux lm-sensors fonts-powerline build-essential xz-utils p7zip-full ripgrep bash-completion xclip copyq tree jq
sudo snap install yq
```
> `apt` only installs the old version 3.xx by default; add repository [ppa:rmescandon](https://launchpad.net/~rmescandon/+archive/ubuntu/yq) seems not not working anymore.

#### Develope utils

```sh
sudo apt install openjdk-21-jdk gcc-multilib g++-multilib manpages-dev manpages-posix-dev linux-doc splint cppcheck shellcheck linux-headers-`uname -r`

#boost
apt install libboost-dev libboost-doc libboost-dbg
# boot more
apt install libboost-all-dev
```

#### Additions

```sh
# chinese fonts
apt install fonts fonts-hanazono fonts-wqy-zenhei fonts-arphic-ukai fonts-arphic-uming
```

### SSH Server

#### Install and Enable

- install, enable, and start

    ```sh
    sudo apt install openssh-server
    sudo systemctl enable --now ssh
    systemctl status sshd
    ```

#### Enable Pubkey Authentication

- Add pubkey

    Edit `.ssh/authorized_keys` with permission set well,
    ```sh
    vim ~/.ssh/authorized_keys
    chmod 700 ~/.ssh
    chmod 500 ~/.ssh/authorized_keys
    ```
    or run the command `ssh-copy-id` in the client if the command is provided.
    ```sh
    ssh-copy-id account@192.168.x.x
    ```

- Disable passowrd authentication (optional)

    edit `/etc/ssh/sshd_config`,
    ```conf
    PasswordAuthentication no
    ```
    then reload ssh
    ```sh
    systemctl reload ssh
    ```
### Time

#### Time Zone

- set by command, or

     ```sh
     sudo dpkg-reconfigure tzdata
     ```

- set by manual, or
     ```sh
    sudo ln -snf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
    echo "Asia/Taipei" | sudo tee /etc/timezone
     ```

- set only in the local (user only)
     ```sh
     sudo tzselect
     cat >> ~/.profile <<EOT
     export TZ='Asia/Taipei'
     EOT
     ```

#### Time Sync

```
sudo systemctl status systemd-timesyncd
timedatectl status
```

### Vim

#### install
```sh
# for ubuntu 22 to use vim 9.0
sudo add-apt-repository ppa:jonathonf/vim && sudo apt update

sudo apt install vim
```

#### configuration
```sh
git clone https://github.com/dayanuyim/myconf .myconf
ln -s .myconf/vim/.vimrc .
cp -a .myconf/vim/.vim .
```

#### install vim-plug

- plug install
   ```sh
   curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
   ```

- install plugins. open vim, then execute in cmd mode
   ```vim
    :PlugInstall
   ```

#### Config for NeoVim (Optional)
```vim
~/.config/nvim/init.vim
set runtimepath^=~/.vim runtimepath+=~/.vim/after
let &packpath = &runtimepath
source ~/.vimrc
```

### Bash

#### bashrc

- Apply a customed `.bashrc`

    ```bash
    git clone https://github.com/dayanuyim/myconf .myconf
    git clone https://github.com/dayanuyim/bash-git-prompt.git ~/.bash-git-prompt --depth=1
    mv ~/.bashrc ~/.bashrc.bak
    ln -s .myconf/bash/bashrc .bashrc
    ```

#### profile

- Set `.profile`. A template can be found in `myconf` as an initial copy
    ```bash
    cp ~/.myconf/sh/profile ~/.profile
    ```

    ***<font color="red">NOTE: login in a new shell to test whether .bashrc and .profile are both all right.</font>***

#### locale

- Check locale

    ```bash
    locale
    ```

    > if failed, reconfig locals as below:
    ```bash
    sudo locale-gen $LANG
    sudo dpkg-reconfigure locales
    ```

#### Groups

- set groups
    ```bash
    sudo usermod -aG dialout $USER  # for uart connection
    ```

### Tmux

```bash
cd ~
git clone  https://github.com/gpakosz/.tmux.git
ln -s .tmux/.tmux.conf .
cp .tmux/.tmux.conf.local .

 # custom .tmux.conf
cd ~/.tmux
git remote add my https://github.com/dayanuyim/.tmux.git
git pull my master

# custom .tmux.conf.local
vimdiff ~/.tmux.conf.local ~/.tmux/.tmux.conf.local.my

# more utils
sudo apt install tmuxinator
```

### Docker

<font color="blue">Please reference [the offical steps](https://docs.docker.com/engine/install/ubuntu/) first.</font>

```bash
sudo apt remove docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo groupadd docker
sudo usermod -aG docker $USER   #relogin to check
```

### NodeJS

ref: [README](https://github.com/nodesource/distributions/blob/master/README.md#deb)

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - &&\ 
sudo apt install -y nodejs
```

### Input Method

*(Testing @Ubuntu 20.04)*

#### Install
```bash
apt install fcitx
cd /etc/X11/xinit/xinput.d && ln -s fcitx en_US  # if in the EN env
apt install fcitx-chewing fcitx-m17n fcitx-table-boshiamy
```
***<font color="red">relogin or reboot after the install</font>***
	
#### Add Input Methods

Add new input methods in the upper-right system tray:
```
* fcitx tray -> config -> Input Method
(Choose Boshiamy and Chewing)
```

#### Set Appearance Font for 日文、亂碼問題

```bash
apt install fonts fonts-hanazono fonts-wqy-zenhei fonts-arphic-ukai fonts-arphic-uming 
```

Set the appearance font
```
* fcitx tray -> config -> appearance -> font -> "HanaMinA Regular"
  (建議字型) 花園明朝體, 全字庫宋體, or "AR PL UKai TW MBE Book"
* fcitx tray -> restart
```

### Tarball

- Download and extract the tarball `xxx.tar.gz`

```bash
sudo tar xvf xxx.tar.gz -C /opt && sudo ln -s /opt/xxx-1.2.3 /opt/xxx
```

- Create `/etc/profile.d/xxx.sh` to add bin path

```sh
export XXX_HOME=/opt/xxx
path=$XXX_HOME/bin
case ":$PATH:" in
   *":$path:"*) :;;
   *) PATH="$path:$PATH"
esac
unset path
```

### Change the Apt Source

```sh
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo vim /etc/apt/sources.list
   # replace sources
   # :%s/us.archive.ubuntu.com/tw.archive.ubuntu.com/g
sudo apt update
```

```json
{
    "date":"2025.5.21 15:33",
    "tags": ["BLOG","Docker","Arch","WSL"],
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 缘由

在使用docker desktop的过程中时不时需要重启或者连接不到守护程序的折磨之下,便诞生了这个想法

那么现在来配置下吧

### 资源准备

1. Arch 的rootfs可以获取这两位大佬的

   [AzureZeng/wsl-arch-rootfs：使用 pacstrap 自动生成最小 Arch Linux WSL2 rootfs --- AzureZeng/wsl-arch-rootfs: Automatic build minimal Arch Linux WSL2 rootfs, using pacstrap (github.com)](https://github.com/AzureZeng/wsl-arch-rootfs)

   [yuk7/ArchWSL：基于 ArchLinux 的 WSL 发行版。支持多次安装。 --- yuk7/ArchWSL: ArchLinux based WSL Distribution. Supports multiple install. (github.com)](https://github.com/yuk7/ArchWSL)

2. WSL安装参考往期

   [Article - JiXieShi's Blog (starss.cc)](https://www.starss.cc/article?key=7qZIm8)

### 开始安装

1. 导入rootfs 和之前一样使用WSL Manager来创建不同的是选择下好的rootfs来创建

   ![image-20240521152019851](https://s2.loli.net/2024/05/21/YK5wyqZtcBbDkS1.png)

2. 创建后打开实例输入以下内容建立非root用户

   ```shell
   pacman -S sudo
   echo '%wheel ALL=(ALL:ALL) ALL' > /etc/sudoers.d/wheel
   useradd -m -G wheel jixieshi
   passwd jixieshi
   cat << EOF > /etc/wsl.conf
   [interop]
   appendWindowsPath=false
   
   [user]
   default=jixieshi
   EOF
   ```

3. 加入docker用户组免去sudo的麻烦

   ```shell
   sudo groupadd docker
   sudo usermod -aG docker jixieshi
   
   #可选加入systemd支持
   cat << EOF > /etc/wsl.conf
   
   [boot]
   systemd=true
   EOF
   ```

4. 重启wsl 可看到已经是普通用户了

   ![image-20240521161524042](https://s2.loli.net/2024/05/21/QjSG2i7ODKH5R4b.png)     

### 基础配置

1. 更新系统并安装基础工具包

   ```shell
   sudo pacman -Syu
   sudo pacman -Sy zsh git wget curl vim exa lua unzip
   ```

2. 安装zinit并配置zsh

   - 安装

      ```shell
      bash -c "$(curl -fsSL https://git.io/zinit-install)"  # https://raw.githubusercontent.com/zdharma-continuum/zinit/HEAD/scripts/install.sh
      ```

   - 配置zsh

      ```shell
      cat >> ~/.zshrc <<EOF
      # 补全、高亮、快速命令
      zinit light zsh-users/zsh-completions
      zinit light zsh-users/zsh-autosuggestions
      zinit light zdharma-continuum/fast-syntax-highlighting
      # powerlevel10k主题
      zinit ice depth"1" # git clone depth
      zinit light romkatv/powerlevel10k
      # 快速目录跳转
      zinit ice lucid wait='1'
      zinit light skywind3000/z.lua
      # fzf
      zinit ice from"gh-r" as"program"
      zinit load junegunn/fzf
      # sharkdp/bat
      zinit ice as"command" from"gh-r" mv"bat* -> bat" pick"bat/bat"
      zinit light sharkdp/bat
      # 加载补全
      zinit ice mv="*.zsh -> _fzf" as="completion"
      zinit snippet 'https://github.com/junegunn/fzf/blob/master/shell/completion.zsh'
      zinit snippet 'https://github.com/junegunn/fzf/blob/master/shell/key-bindings.zsh'
      zinit ice as="completion"
      zinit snippet 'https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/fd/_fd'
      zinit ice as="completion"
      zinit snippet 'https://github.com/ogham/exa/tree/master/completions/zsh/_exa'
      # OMZ基本模块
      zinit ice lucid wait='1'
      zinit snippet OMZ::lib/git.zsh
      zinit snippet OMZ::lib/history.zsh
      zinit snippet OMZ::lib/key-bindings.zsh
      zinit snippet OMZ::lib/clipboard.zsh
      zinit snippet OMZ::lib/completion.zsh
      # 环境变量
      export LC_ALL=en_US.UTF-8  
      export LANG=en_US.UTF-8
      export PATH="$PATH:/mnt/c/Users/lydxh/AppData/Local/Programs/Microsoft VS Code/bin" #(为环境加入vsc方便写交互,之前关闭了win的自动注入环境这样可以选择自己要那些来单独添加
      # 变量配置
      PROXY_IP="192.168.31.14" #(按需修改
      PROXY_PORT="10808"
      # 快捷命令
      alias spi="export https_proxy=http://: && export http_proxy=http://:"
      alias upi="unset http_proxy && unset https_proxy"
      alias cpi='echo http://127.0.0.1:7897 "<->" http://127.0.0.1:7897'
      alias edzsh="vim ~/.zshrc"
      alias upzsh="exec zsh"
      alias edvim="vim ~/.vimrc"
      alias cls="clear"
      alias la="exa -abghHliS"
      alias ll='exa -hlg -s=time -r'
      EOF
      ```

3. 切换默认终端为zsh

   ```shell
   cat /etc/shells
   # 找到zsh的行复制到下面
   chsh -s /usr/bin/zsh
   ```

4. 效果如下

   ![image-20240521161230973](https://s2.loli.net/2024/05/21/UGnLQIBfhDsVzgp.png)

5. 加入archlinuxcn源

   ```shell
   sudo cat << EOF > /etc/pacman.conf
   [archlinuxcn]
   Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
   EOF
   sudo pacman-key --lsign-key "farseerfc@archlinux.org"
   sudo pacman -Sy archlinuxcn-keyring
   ```

6. 安装yay

   ```shell
   git clone https://aur.archlinux.org/yay
   cd yay
   GOPROXY=https://goproxy.cn 
   makepkg -si
   cd ..&&rm -rf yay
   ```

### Docker配置

1. 安装Docker, compose和ctop

   ```shell
   sudo pacman -Syy docker docker-compose ctop
   ```

2. 安装并配置navidia运行时

   ```shell
   sudo pacman -Syy nvidia-container-toolkit
   
   cat << EOF > /etc/docker/daemon.json
   {
    "default-runtime": "nvidia",
    "runtimes": {
      "nvidia": {
        "path": "/usr/bin/nvidia-container-runtime",
        "runtimeArgs": []
      }
    }
   }
   EOF
   ```

3. 配置docker自启动服务

   ```shell
   sudo systemctl enable docker.service
   sudo systemctl start docker.service
   ```

4. 测试环境是否正常

   ```shell
   docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
   ```

   ![image-20240521160438480](https://s2.loli.net/2024/05/21/9i7udRmcW6oGMCJ.png)

## 关于win平台的wsl扩展配置

点击直接编辑.wslconfig

![image-20240521161736137](https://s2.loli.net/2024/05/21/SP1wcqxE8HYL4ae.png)

输入以下可选项

```ini
[experimental]
# 内存回收 dropcache/gradual/disabled，设置成gradual则发行版内不建议打开systemd功能
autoMemoryReclaim = dropcache 
# 网络配置
networkingMode = mirrored 
dnsTunneling = true
firewall = true
autoProxy = true
# 硬盘回收
sparseVhd = true
```


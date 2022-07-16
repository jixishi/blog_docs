```json
{
    "date":"2022.03.16 17:38",
    "tags": ["Quark","Linux"],
    "author": "JiXieShi", 
    "musicId":"5381722575"
}
```
## 烧录

1. 镜像获取

   [Quark-n-21-1-11](https://files.seeedstudio.com/wiki/Quantum-Mini-Linux-Dev-Kit/quark-n-21-1-11.zip)

2. 下载烧录软件

   - [BalenaEtcher](https://www.balena.io/etcher/)
   - [Rufus](http://rufus.ie/zh/)

3. 开始烧录

- ![BalenaEtcher](./assets/images/Quark/BalenaEtcher.png)
- ![Rufus](./assets/images/Quark/Rufus.png)

## 联网

```shell
# 切换用户为root
su root
# 开启WiFi
nmcli r wifi on
# 扫描附近WiFi信息
nmcli dev wifi
# 连接WiFi SSID为WiFi名 PASSWORD为WiFi密码
nmcli dev wifi connect "SSID" password "PASSWORD" ifname wlan0
```



## 更新系统到 18

1. 更新软件包

```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get autoremove
sudo apt dist-upgrade
```

2. 设置 python 软连接

```shell
sudo ln -sf /usr/bin/python2.7 /usr/bin/python
sudo ln -sf /usr/bin/python3.5 /usr/bin/python3
```

3. 执行系统升级，执行如下命令：

```shell
sudo do-release-upgrade
```

## 安装 zsh、zinit 并配置 powerlevel10k

1. 安装 zsh 和运行 zinit 自动安装脚本

```shell
sudo apt-get install zsh git wget curl vim -y
bash -c "$(curl -fsSL https://git.io/zinit-install)"
```

2. 配置 zsh

```shell
cat >> ~/.zshrc <<EOF
# 补全、高亮、快速命令
zinit light zsh-users/zsh-completions
zinit light zsh-users/zsh-autosuggestions
zinit light zdharma-continuum/fast-syntax-highlighting
# powerlevel10k主题
zinit ice depth"1" # git clone depth
zinit light romkatv/powerlevel10k
# OMZ基本模块
zinit snippet OMZ::lib/git.zsh
zinit snippet OMZ::lib/history.zsh
zinit snippet OMZ::lib/key-bindings.zsh
zinit snippet OMZ::lib/clipboard.zsh
zinit snippet OMZ::lib/completion.zsh
# 环境变量
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
# 快捷命令
alias edzsh="vim ~/.zshrc"
alias edvim="vim ~/.vimrc"
alias cls="clear"
alias la="ls -a"
alias ll="ls -l"
EOF
```

3. 打开 powerlevel10k 配置文件

```zsh
vim ~/.p10k.zsh
```

4. 加入温度显示函数

```shell
function prompt_my_cpu_temp(){
    integer cpu_temp="$(</sys/class/thermal/thermal_zone0/temp)/1000"
    if ((cpu_temp>=80)); then
        p10k segment -s HOT -f red  -i '' -t "${cpu_temp}°C"
    elif ((80>cpu_temp>40)); then
        p10k segment -s WARM -f yellow -i '' -t "${cpu_temp}°C"
    elif ((cpu_temp<=40)); then
        p10k segment -s WARM -f green -i '' -t "${cpu_temp}°C"
    fi
}
```

5. 修改显示部分

```zsh
typeset -g POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(
    # =========================[ Line #1 ]=========================
    ~~~
    load                    # CPU load
    disk_usage              # disk usage
    my_cpu_temp
    # ram                   # free RAM
    # swap                  # used swap
    todo                    # todo items (https://github.com/todotxt/todo.txt-cli)
    timewarrior             # timewarrior tracking status (https://timewarrior.net/)
    taskwarrior             # taskwarrior task count (https://taskwarrior.org/)
    # time                  # current time
    # =========================[ Line #2 ]=========================
    ~~~
  )
```

6.  显示效果如下

![zsh_show](./assets/images/Quark/zsh_show.png)


## 配置 vim


```
cat >> ~/.vimrc <<EOF
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 

" 一般设定 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 设定默认解码 
set fenc=utf-8 
set fencs=utf-8,usc-bom,euc-jp,gb18030,gbk,gb2312,cp936 

" 不要使用vi的键盘模式，而是vim自己的 
set nocompatible 

" history文件中需要记录的行数 
set history=100 

" 在处理未保存或只读文件的时候，弹出确认 
set confirm 

" 与windows共享剪贴板 
set clipboard+=unnamed 

" 侦测文件类型 
filetype on 

" 智能补全
set completeopt=longest,menu

" 载入文件类型插件 
filetype plugin on 

" 为特定文件类型载入相关缩进文件 
filetype indent on 

" 保存全局变量 
set viminfo+=! 

" 带有如下符号的单词不要被换行分割 
set iskeyword+=_,$,@,%,#,- 

" 语法高亮 
syntax enable
syntax on 

" 高亮字符，让其不受100列限制 
:highlight OverLength ctermbg=red ctermfg=white guibg=red guifg=white 
:match OverLength '\%101v.*' 

" 状态行颜色 
highlight StatusLine guifg=SlateBlue guibg=Yellow 
highlight StatusLineNC guifg=Gray guibg=White 

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 文件设置 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 不要备份文件（根据自己需要取舍） 
set nobackup 

" 不要生成swap文件，当buffer被丢弃的时候隐藏它 
setlocal noswapfile 
set bufhidden=hide 

" 字符间插入的像素行数目 
set linespace=0 

" 增强模式中的命令行自动完成操作 
set wildmenu 

" 在状态行上显示光标所在位置的行号和列号 
set ruler 
set rulerformat=%20(%2*%<%f%=\ %m%r\ %3l\ %c\ %p%%%) 

" 命令行（在状态行下）的高度，默认为1，这里是2 
set cmdheight=2 

" 使回格键（backspace）正常处理indent, eol, start等 
set backspace=2 

" 允许backspace和光标键跨越行边界 
set whichwrap+=<,>,h,l 

" 可以在buffer的任何地方使用鼠标（类似office中在工作区双击鼠标定位） 
set mouse=a 
set selection=exclusive 
set selectmode=mouse,key 

" 启动的时候不显示那个援助索马里儿童的提示 
set shortmess=atI 

" 通过使用: commands命令，告诉我们文件的哪一行被改变过 
set report=0 

" 不让vim发出讨厌的滴滴声 
set noerrorbells 

" 在被分割的窗口间显示空白，便于阅读 
set fillchars=vert:\ ,stl:\ ,stlnc:\ 

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 搜索和匹配 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 高亮显示匹配的括号 
set showmatch 

" 匹配括号高亮的时间（单位是十分之一秒） 
set matchtime=5 

" 在搜索的时候忽略大小写 
set ignorecase 

" 不要高亮被搜索的句子（phrases） 
set nohlsearch 

" 在搜索时，输入的词句的逐字符高亮（类似firefox的搜索） 
set incsearch 

" 输入:set list命令是应该显示些啥？ 
set listchars=tab:\|\ ,trail:.,extends:>,precedes:<,eol:$ 

" 光标移动到buffer的顶部和底部时保持3行距离 
set scrolloff=3 

" 不要闪烁 
set novisualbell 

" 我的状态行显示的内容（包括文件类型和解码） 
hi User1 cterm=none ctermfg=gray ctermbg=darkgray                               
hi User2 cterm=none ctermfg=darkgrey ctermbg=gray                               
hi User3 cterm=bold ctermfg=darkgrey ctermbg=gray                               
hi User4 cterm=bold ctermfg=green ctermbg=gray                                  
hi User5 cterm=none ctermfg=darkgrey ctermbg=gray                               
hi User6 cterm=none ctermfg=darkgrey ctermbg=gray  
set statusline=%1*%F%m%r%h%w%=\ %2*\ %Y\ %3*%{\"\".(\"\"?&enc:&fenc).((exists(\"+bomb\")\ &&\ &bomb)?\"+\":\"\").\"\"}\ %4*[%l,%v]\ %5*%p%%\ \|\ %6*%LL\
" 总是显示状态行 
set laststatus=2 

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 文本格式和排版 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 自动格式化 
set formatoptions=tcrqn 

" 继承前一行的缩进方式，特别适用于多行注释 
set autoindent 

" 为C程序提供自动缩进 
set smartindent 

" 使用C样式的缩进 
set cindent 

" 制表符为4 
set tabstop=4 

" 统一缩进为4 
set softtabstop=4 
set shiftwidth=4 

" 不要用空格代替制表符 
set noexpandtab 

" 不要换行 
set nowrap 

" 在行和段开始处使用制表符 
set smarttab 

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" CTags的设定 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 按照名称排序 
let Tlist_Sort_Type = "name" 

" 在右侧显示窗口 
let Tlist_Use_Right_Window = 1 

" 压缩方式 
let Tlist_Compart_Format = 1 

" 如果只有一个buffer，kill窗口也kill掉buffer 
let Tlist_Exist_OnlyWindow = 1 

" 不要关闭其他文件的tags 
let Tlist_File_Fold_Auto_Close = 0 

" 不要显示折迭树 
let Tlist_Enable_Fold_Column = 0 

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" Autocommands 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 
" 只在下列文件类型被侦测到的时候显示行号，普通文本文件不显示 

if has("autocmd") 
autocmd FileType xml,html,c,cs,java,perl,shell,bash,cpp,python,vim,php,ruby set number 
autocmd FileType xml,html vmap <C-o> <ESC>'<i<!--<ESC>o<ESC>'>o--> 
autocmd FileType java,c,cpp,cs vmap <C-o> <ESC>'<o 
autocmd FileType html,text,php,vim,c,java,xml,bash,shell,perl,python setlocal textwidth=100 
" autocmd Filetype html,xml,xsl source $VIMRUNTIME/plugin/closetag.vim 
autocmd BufReadPost * 
\ if line("'\"") > 0 && line("'\"") <= line("$") | 
\ exe " normal g`\"" | 
\ endif 
endif "has("autocmd") 

" 能够漂亮地显示.NFO文件 
set encoding=utf-8 
function! SetFileEncodings(encodings) 
let b:myfileencodingsbak=&fileencodings 
let &fileencodings=a:encodings 
endfunction 
function! RestoreFileEncodings() 
let &fileencodings=b:myfileencodingsbak 
unlet b:myfileencodingsbak 
endfunction 

au BufReadPre *.nfo call SetFileEncodings('cp437')|set ambiwidth=single au BufReadPost *.nfo call RestoreFileEncodings() 

" 高亮显示普通txt文件（需要txt.vim脚本） 
au BufRead,BufNewFile * setfiletype txt 

" 用空格键来开关折迭 
"set foldenable 
"set foldmethod=manual 
"nnoremap <space> @=((foldclosed(line('.')) < 0) ? 'zc':'zo')<CR> 

" minibufexpl插件的一般设置 
let g:miniBufExplMapWindowNavVim = 1 
let g:miniBufExplMapWindowNavArrows = 1 
let g:miniBufExplMapCTabSwitchBufs = 1 
let g:miniBufExplModSelTarget = 1
" 配色方案
colorscheme desert
" Taglist 配置
let Tlist_Show_One_File=1
let Tlist_Exit_OnlyWindow=1
" winmanager 配置
map wm :WMToggle<cr>
let g:winManagerWindowLayout='FileExplorer|TagList'
"cscope 配置
if has("cscope")
   set csprg=/usr/bin/cscope
   set csto=0
   set cst
   set nocsverb
   " add any database in current directory
   if filereadable("cscope.out")
       cs add cscope.out
        " else add database pointed to by environment
   elseif $CSCOPE_DB != ""
       cs add $CSCOPE_DB
   endif
   set csverb
endif

" cscope  快捷键
nmap<leader>sa :csadd cscope.out<cr>
nmap<leader>ss :cs find s<C-R>=expand("<cword>")<cr><cr>
nmap<leader>sg :cs find g <C-R>=expand("<cword>")<cr><cr>
nmap<leader>sc :cs find c <C-R>=expand("<cword>")<cr><cr>
nmap<leader>st :cs find t <C-R>=expand("<cword>")<cr><cr>
nmap<leader>se :cs find e <C-R>=expand("<cword>")<cr><cr>
nmap<leader>sf :cs find f<C-R>=expand("<cfile>")<cr><cr>
nmap<leader>si :cs find i<C-R>=expand("<cfile>")<cr><cr>
nmap<leader>sd :cs find d <C-R>=expand("<cword>")<cr><cr>
"MiniBufExplorer  配置
let g:miniBufExplMapWindowNavVim = 1
let g:miniBufExplMapWindowNavArrows = 1
let g:miniBufExplMapCTabSwitchBufs = 1
let g:miniBufExplModSelTarget = 1 
" grep 配置
nnoremap<F4>  /<C-R>=expand("<cword>")<cr><cr>
nnoremap<F3>  ?<C-R>=expand("<cword>")<cr><cr>
nnoremap <silent> <leader><F3> :Grep<CR> 
nnoremap <silent> <leader><F4> :Rgrep<CR> 
" SuperTab
let g:SuperTabRetainCompletionType=2
let g:SuperTabDefaultCompletionType="<C-X><C-O>"
" gvim字体
"set guifont=Courier\ Regular\ 20
"line
se nu

""
nnoremap <silent> <F12> :A<cr>
" Source a global configuration file if available
if filereadable("/etc/vim/vimrc.local")
  source /etc/vim/vimrc.local
endif
EOF
```
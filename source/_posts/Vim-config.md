---

title: 个人常用的 Vim 配置
date: 2023-01-29 1:37:48
updated: 2023-01-29 1:37:48
categories: Linux
tags: [Vim, Linux]
thumbnail:
banner_img:

---

# Vim 常用配置

经常在服务端上修改文件，苦于 vim 默认的界面很不友好，又不想重新手捏配置文件，就分享一下个人常用的简单配置吧。

## 解决乱码问题

Reference： [Ubuntu的中文乱码问题 [完美解决]\_lubuntu 终端乱码-CSDN博客](https://blog.csdn.net/weixin_39792252/article/details/80415550)

```shell
sudo apt-get install language-pack-zh-hans

export LANG="zh_CN.UTF-8"
export LANGUAGE="zh_CN:zh:en_US:en"
export LC_ALL="zh_CN.UTF-8"

sudo locale-gen

sudo apt-get install fonts-droid-fallback ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
```

## 配置

> **自用 ​**​**`.vimrc`**​**​ 已上传到 GitHub 仓库**  
> 参考：[WhaleFell/vimrc](https://github.com/WhaleFell/vimrc/blob/main/.vimrc)  
> raw: [https://raw.githubusercontent.com/WhaleFell/vimrc/main/.vimrc](https://raw.githubusercontent.com/WhaleFell/vimrc/main/.vimrc)

设置中文字符集

```shell
dpkg-reconfigure locales
```

在用户目录下创建配置。

```shell
vim ~/.vimrc
```

写入以下配置

```txt
" 打开语法高亮
syntax on

" 使用配色方案
colorscheme desert

" 打开文件类型检测功能
filetype on

" 不同文件类型采用不同缩进
filetype indent on

" 允许使用插件
filetype plugin on
filetype plugin indent on

" 允许区域选择
set selection=exclusive
set selectmode=mouse,key

" 高亮光标所在行
set cursorline

" 取消光标闪烁
set novisualbell

" 状态栏显示当前执行的命令
set showcmd

" 标尺功能，显示当前光标所在行列号
set ruler

" 粘贴时保持格式
set paste

" 高亮显示匹配的括号
set showmatch

" 在搜索的时候忽略大小写
set ignorecase
 
" 高亮被搜索的句子
set hlsearch
 
" 在搜索时，输入的词句的逐字符高亮（类似firefox的搜索）
set incsearch

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
set tabstop=4

" 允许使用退格键，或set backspace=2
set backspace=eol,start,indent
set whichwrap+=<,>,h,l

" 取消换行
set nowrap

" 启动的时候不显示那个援助索马里儿童的提示
set shortmess=atI

" 在被分割的窗口间显示空白，便于阅读
set fillchars=vert:\ ,stl:\ ,stlnc:\

" 光标移动到buffer的顶部和底部时保持3行距离, 或set so=3
set scrolloff=3

" 设定默认解码
set fenc=utf-8
set fencs=utf-8,usc-bom,euc-jp,gb18030,gbk,gb2312,cp936
 
" 设定编码
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8
set enc=utf-8
set langmenu=zh_CN.UTF-8
language message zh_CN.UTF-8
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim

" 自动补全
filetype plugin indent on
set completeopt=longest,menu

" 自动补全命令时候使用菜单式匹配列表
set wildmenu
autocmd FileType ruby,eruby set omnifunc=rubycomplete#Complete
autocmd FileType python set omnifunc=pythoncomplete#Complete
autocmd FileType javascript set omnifunc=javascriptcomplete#CompleteJS
autocmd FileType html set omnifunc=htmlcomplete#CompleteTags
autocmd FileType css set omnifunc=csscomplete#CompleteCSS
autocmd FileType xml set omnifunc=xmlcomplete#CompleteTags
autocmd FileType java set omnifunc=javacomplete#Complet

" 行号
set number

" 突出显示
set cursorline

" 关闭鼠标
set mouse=c

" 启动鼠标
" set mouse=a

set selection=exclusive
set selectmode=mouse,key

" 自动缩进
set autoindent

" 语法高亮
syntax enable

" 文件类型检查
filetype indent on

" status line
set laststatus=2
set statusline=\ %{HasPaste()}%F%m%r%h\ %w\ \ CWD:\ %r%{getcwd()}%h\ \ \ Line:\ %l\ \ Column:\ %c

" other

" Set to auto read when a file is changed from the outside
set autoread
au FocusGained,BufEnter * checktime

" When searching try to be smart about cases
set smartcase

" No annoying sound on errors
set noerrorbells
set novisualbell
set t_vb=
set tm=500

" Turn backup off
set nobackup
set nowb
set noswapfile

" Use spaces instead of tabs
set expandtab

" Be smart when using tabs ;)
set smarttab

" Linebreak on 500 characters
set lbr
set tw=500

set ai "Auto indent
set si "Smart indent
set wrap "Wrap lines

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" => Helper functions
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" Returns true if paste mode is enabled
function! HasPaste()
    if &paste
        return 'PASTE MODE  '
    endif
    return ''
endfunction

" Don't close window, when deleting a buffer
command! Bclose call <SID>BufcloseCloseIt()
function! <SID>BufcloseCloseIt()
    let l:currentBufNum = bufnr("%")
    let l:alternateBufNum = bufnr("#")

    if buflisted(l:alternateBufNum)
        buffer #
    else
        bnext
    endif

    if bufnr("%") == l:currentBufNum
        new
    endif

    if buflisted(l:currentBufNum)
        execute("bdelete! ".l:currentBufNum)
    endif
endfunction

function! CmdLine(str)
    call feedkeys(":" . a:str)
endfunction

function! VisualSelection(direction, extra_filter) range
    let l:saved_reg = @"
    execute "normal! vgvy"

    let l:pattern = escape(@", "\\/.*'$^~[]")
    let l:pattern = substitute(l:pattern, "\n$", "", "")

    if a:direction == 'gv'
        call CmdLine("Ack '" . l:pattern . "' " )
    elseif a:direction == 'replace'
        call CmdLine("%s" . '/'. l:pattern . '/')
    endif

    let @/ = l:pattern
    let @" = l:saved_reg
endfunction
```



## 使用现成的配置

利用了 [amix/vimrc](https://github.com/amix/vimrc) 这个开源项目。



## 参考 Reference

[Vim配置文件（.vimrc），Vim配置教程](http://c.biancheng.net/view/3024.html)  
[Vim配置-王遗风](https://www.ruanyifeng.com/blog/2018/09/vimrc.html)

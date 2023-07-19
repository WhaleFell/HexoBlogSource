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

经常在服务端上修改文件，苦于 vim 默认的界面很不友好，又不想重现手捏配置文件，就分享一下个人常用的简单配置吧。

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
syntax on
colorscheme desert
filetype on
filetype indent on
filetype plugin on
filetype plugin indent on
set selection=exclusive
set selectmode=mouse,key
set cursorline
set novisualbell
set showcmd
set ruler
set paste
set showmatch
set ignorecase
set hlsearch
set incsearch
set autoindent
set smartindent
set cindent
set tabstop=4
set softtabstop=4
set shiftwidth=4
set tabstop=4
set backspace=eol,start,indent
set whichwrap+=<,>,h,l
set nowrap
set shortmess=atI
set fillchars=vert:\ ,stl:\ ,stlnc:\
set scrolloff=3
set fenc=utf-8
set fencs=utf-8,usc-bom,euc-jp,gb18030,gbk,gb2312,cp936
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8
set enc=utf-8
set langmenu=zh_CN.UTF-8
language message zh_CN.UTF-8
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim
filetype plugin indent on
set completeopt=longest,menu
set wildmenu
autocmd FileType ruby,eruby set omnifunc=rubycomplete#Complete
autocmd FileType python set omnifunc=pythoncomplete#Complete
autocmd FileType javascript set omnifunc=javascriptcomplete#CompleteJS
autocmd FileType html set omnifunc=htmlcomplete#CompleteTags
autocmd FileType css set omnifunc=csscomplete#CompleteCSS
autocmd FileType xml set omnifunc=xmlcomplete#CompleteTags
autocmd FileType java set omnifunc=javacomplete#Complet
set number
set cursorline
set mouse=c
" set mouse=a
set selection=exclusive
set selectmode=mouse,key
set autoindent
syntax enable
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
```

## 使用现成的配置

利用了 [amix/vimrc](https://github.com/amix/vimrc) 这个开源项目。

## 参考 Reference

[Vim配置文件（.vimrc），Vim配置教程](http://c.biancheng.net/view/3024.html)  
[Vim配置-王遗风](https://www.ruanyifeng.com/blog/2018/09/vimrc.html)

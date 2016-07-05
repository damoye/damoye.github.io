---
layout: post
title:  "My Vim Config For C++ and Python"
date:   2016-07-04 18:55:19 +0800
categories: Vim
---
In my first blog, I want to share my vim config. This config is for coding Python and C++.
My colleague was surprise when he saw my config file. It's short, simple but full-powered.

```
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin 'Yggdroot/indentLine'
Plugin 'airblade/vim-gitgutter'
Plugin 'altercation/vim-colors-solarized'
Plugin 'kien/ctrlp.vim'
Plugin 'rking/ag.vim'
Plugin 'scrooloose/nerdtree'
Plugin 'scrooloose/syntastic'
Plugin 'tmhedberg/SimpylFold'
Plugin 'tpope/vim-fugitive'
Plugin 'Valloric/YouCompleteMe'
call vundle#end()
colorscheme solarized
set background=dark
set clipboard=unnamed
set colorcolumn=80
set expandtab
set list
set nofoldenable
set noswapfile
set shiftwidth=4
set statusline=%{fugitive#statusline()}%F%m%=%l/%L%{SyntasticStatuslineFlag()}
set wildignore=*.pyc
let mapleader=" "
let NERDTreeIgnore=['\.pyc$']
let g:solarized_termcolors=256
let g:ycm_global_ycm_extra_conf = '~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py'
map <C-H> <C-W><C-H>
map <C-J> <C-W><C-J>
map <C-K> <C-W><C-K>
map <C-L> <C-W><C-L>
map <C-n> :NERDTreeToggle<CR>
nnoremap <leader>g :YcmCompleter GoToDeclaration<CR>
nnoremap <leader>d :YcmCompleter GoTo<CR>
nnoremap <leader>k :YcmCompleter GetDoc<CR>
```

One important reason my config is short is [neovim](https://neovim.io/).

I will keep updating my config. Welcome to star it: [vim_mo](https://github.com/damoye1993/vim_mo).

Finaly, I love finding greate vim plugins from [VimAwesome](http://vimawesome.com/).

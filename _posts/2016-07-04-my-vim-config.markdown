---
layout: post
title:  "My Vim Config For Go"
date:   2016-07-04 18:55:19 +0800
categories: Vim
---
In my first blog, I want to share my vim config. This config is for coding Golang.
It's simple and full-powered.

```vim
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'vundlevim/vundle.vim'
Plugin 'airblade/vim-gitgutter'
Plugin 'altercation/vim-colors-solarized'
Plugin 'bling/vim-airline'
Plugin 'ctrlpvim/ctrlp.vim'
Plugin 'ervandew/supertab'
Plugin 'fatih/vim-go'
Plugin 'rking/ag.vim'
Plugin 'scrooloose/nerdtree'
Plugin 'sirver/ultisnips'
Plugin 'tpope/vim-fugitive'
Plugin 'vim-airline/vim-airline-themes'
call vundle#end()
colorscheme solarized
set autowrite
set clipboard=unnamedplus
set foldmethod=syntax
set foldnestmax=1
set list
set mouse=a
set nofoldenable
set noswapfile
set number
set shiftwidth=4
set tabstop=4
let g:SuperTabDefaultCompletionType="context"
let g:go_autodetect_gopath = 0
let g:go_fmt_command="goimports"
let g:go_fmt_experimental=1
let g:go_list_type="quickfix"
let g:go_metalinter_autosave=1
let mapleader=" "
nmap <C-H> <C-W><C-H>
nmap <C-J> <C-W><C-J>
nmap <C-K> <C-W><C-K>
nmap <C-L> <C-W><C-L>
nmap <C-N> :NERDTreeToggle<CR>
nmap <Leader>a :cclose<CR>
nmap <Leader>m :cprevious<CR>
nmap <Leader>n :cnext<CR>
nmap <Leader>c <Plug>(go-coverage-toggle)
nmap <Leader>d <Plug>(go-def-split)
nmap <Leader>e <Plug>(go-rename)
nmap <Leader>f <Plug>(go-referrers)
nmap <Leader>i <Plug>(go-info)
nmap <Leader>r <Plug>(go-run)
nmap <Leader>s <Plug>(go-implements)
nmap <Leader>t <Plug>(go-test)
nmap <Leader>b :<C-u>call <SID>build_go_files()<CR>
function! s:build_go_files()
  let l:file = expand('%')
  if l:file =~# '^\f\+_test\.go$'
    call go#test#Test(0, 1)
  elseif l:file =~# '^\f\+\.go$'
    call go#cmd#Build(0)
  endif
endfunction
```
![screenshot](https://raw.githubusercontent.com/damoye/vimo/master/screenshot.png)

One important reason why my config is short is [neovim](https://neovim.io/).

I will keep updating my config. Welcome to star it: [vimo](https://github.com/damoye/vimo).

Finaly, I love finding greate vim plugins from [VimAwesome](http://vimawesome.com/).
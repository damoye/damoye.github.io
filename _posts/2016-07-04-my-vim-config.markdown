---
layout: post
title:  "My Vim Config For Python And C++"
date:   2016-07-04 18:42
categories: Vim
---
Today I want to share my vim config. This config is for coding Python and C++.
My colleague was surprise when he saw my config file. It's short, simple and full-powered.

{% highlight python %}
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin 'Yggdroot/indentLine'                # Show indentation line
Plugin 'airblade/vim-gitgutter'             # Show git modification
Plugin 'altercation/vim-colors-solarized'   # Theme
Plugin 'jistr/vim-nerdtree-tabs'            # Extension for plugin nerdtree
Plugin 'kien/ctrlp.vim'                     # Jump among file
Plugin 'raimondi/delimitmate'               # Automatic closing of quotes, parenthesis, brackets
Plugin 'rking/ag.vim'                       # Search recursively
Plugin 'scrooloose/nerdtree'                # File tree
Plugin 'scrooloose/syntastic'               # Syntax checking
Plugin 'tmhedberg/SimpylFold'               # Code folding
Plugin 'tpope/vim-fugitive'                 # Git command
Plugin 'Valloric/YouCompleteMe'             # Autocomplete
call vundle#end()
colorscheme solarized
set background=dark
set clipboard=unnamed
set colorcolumn=80
set expandtab
set list
set nofoldenable
set noswapfile
set number
set shiftwidth=4
set showmatch
set softtabstop=4
set statusline+=%{fugitive#statusline()}%F%m%=%l/%L%{SyntasticStatuslineFlag()}
set tabstop=4
set wildignore=*.pyc
let mapleader=" "
let NERDTreeIgnore=['\.pyc$']
map <C-H> <C-W><C-H>
map <C-J> <C-W><C-J>
map <C-K> <C-W><C-K>
map <C-L> <C-W><C-L>
map tt :tabnew<CR>
map tn :tabnext<CR>
map <C-f> :Ag <cword><CR>
map <C-n> <plug>NERDTreeTabsToggle<CR>
let g:ycm_global_ycm_extra_conf = '~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py'
nnoremap <leader>g :YcmCompleter GoToDeclaration<CR>
nnoremap <leader>d :YcmCompleter GoTo<CR>
{% endhighlight %}

I will keep updating my config. Welcome to star it: [VIM_MO]

[vim_mo]: https://github.com/damoye1993/vim_mo

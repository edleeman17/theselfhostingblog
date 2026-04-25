---
author: ed
title: 'Configuring Vim for Node Js Development'
date: 2020-07-28
description: 'An overview of the ideal vim plugins to use for Node Js development, with config file examples'
cover:
  image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ'
  relative: false
tag:
  - 'Ramblings'
---

<h2 id="usingvim">Using Vim</h2>
<p>If you have come across this article, I probably don't need to explain why you need to use Vim as your development IDE.<br>
You're probably here because you want to use Vim, but not sure how to integrate it into your current workflow.</p>
<p>I was in the same position a few months ago.</p>
<p>The easiest way is to install a vim extension into your current IDE of choice, this will bring all of the features of vim into your current workflow.<br>
After getting used to Vim, in something that you're familiar with. You'll probably want to jump-ship into full-blown Vim.</p>
<p>That's what this blog post is for.</p>
<h3 id="theprimeagen">The Primeagen</h3>
<p>A lot of my configuration and interest in jumping over to Vim came from <a href="https://www.youtube.com/channel/UC8ENHE5xdFSwx71u3fDH5Xw">The Primeagen</a>.<br>
This guy is a machine with Vim.</p>
<p>He's currently a Netflix Engineer who only works in Vim for his day-to-day work. Regularly streaming over on <a href="https://www.twitch.tv/theprimeagen">Twitch</a> and uploading on <a href="https://www.youtube.com/channel/UC8ENHE5xdFSwx71u3fDH5Xw">YouTube</a>.</p>
<p>He has recently produced a series on <a href="https://www.youtube.com/playlist?list=PLm323Lc7iSW_wuxqmKx_xxNtJC_hJbQ7R">Vim As Your Editor</a>. I highly recommend that you go and watch it.</p>
<h2 id="plugins">Plugins</h2>
<p>Vim is highly configurable with user-created plugins.<br>
Here's a list that I recommend to use with Node.Js:</p>
<ul>
<li><a href="https://github.com/tpope/vim-fugitive">vim-fugitive</a>
<ul>
<li>This integrates Git seemlessly into Vim</li>
</ul>
</li>
<li><a href="https://github.com/neoclide/coc.nvim">coc.nvim</a>
<ul>
<li>This allows code completion and intellisense for Javascript in Vim</li>
</ul>
</li>
<li><a href="https://github.com/preservim/nerdcommenter">nerdcommenter</a>
<ul>
<li>A simple plugin to comment out lines of code using Vim bindings</li>
</ul>
</li>
<li><a href="https://github.com/dense-analysis/ale">ale</a>
<ul>
<li>A syntax highlighter for Vim</li>
</ul>
</li>
<li><a href="https://github.com/preservim/nerdtree">nerdtree</a>
<ul>
<li>A file tree view for selecting files and navigating folder structure in your projects</li>
</ul>
</li>
<li><a href="https://github.com/itchyny/lightline.vim">lightline</a>
<ul>
<li>A lightweight status bar to show progress in your buffer and other useful information</li>
</ul>
</li>
<li><a href="https://github.com/junegunn/fzf.vim">fzf</a>
<ul>
<li>A file-finding plugin to quickly search your project and swtich buffers</li>
</ul>
</li>
<li><a href="https://github.com/morhetz/gruvbox">gruvbox</a>
<ul>
<li>The only theme you should be using. Heavily influenced by The Primeagen</li>
</ul>
</li>
<li><a href="https://github.com/ap/vim-buftabline">vim-buftabline</a>
<ul>
<li>Introduces 'tabs' into Vim for keeping track of your open buffers</li>
</ul>
</li>
</ul>
<p>Installing plugins is easy in Vim. I use a plugin manager called <a href="https://github.com/junegunn/vim-plug">vim-plug</a>.</p>
<h2 id="vimrcplug">.vimrc.plug</h2>
<p>Your plugin installation lives in a file called <code>.vimrc.plug</code>.</p>
<p>Here's a copy of my <code>.vimrc.plug</code> as an example</p>
<pre><code>&quot; .vimrc.plug

call plug#begin('~/.vim/plugged')
&quot; Git
Plug 'tpope/vim-fugitive'

&quot; Code completion
Plug 'neoclide/coc.nvim', {'branch': 'release'}

&quot; Code commenter
Plug 'preservim/nerdcommenter'
&quot;
&quot; Syntax highlighting
Plug 'dense-analysis/ale'

&quot; NERDTree
Plug 'preservim/nerdtree'
&quot;
&quot; Statusbar
Plug 'itchyny/lightline.vim'
&quot;
&quot; Finder
Plug 'junegunn/fzf', { 'do': { -&gt; fzf#install() } }
Plug 'junegunn/fzf.vim'

&quot; File finder
Plug 'vifm/vifm.vim'

&quot; Theme
Plug 'morhetz/gruvbox'

&quot; Tabs
Plug 'ap/vim-buftabline'

call plug#end()

set background=dark
colorscheme gruvbox
</code></pre>
<p>Just install those plugins using <code>:PlugInstall</code></p>
<h2 id="vimrc">.vimrc</h2>
<p><code>.vimrc</code> is your main Vim config file. You probably know this already.</p>
<p>Here's mine:</p>
<pre><code>syntax on

set guicursor=
set noshowmatch
set relativenumber
set nohlsearch
set hidden
set noerrorbells
set tabstop=4 softtabstop=4
set shiftwidth=4
set expandtab
set smartindent
set nu
set nowrap
set smartcase
set noswapfile
set nobackup
set undodir=~/.vim/undodir
set undofile
set incsearch
set termguicolors
set scrolloff=8
set modifiable

&quot; Give more space for displaying messages.
set cmdheight=2

&quot; Having longer updatetime (default is 4000 ms = 4 s) leads to noticeable
&quot; delays and poor user experience.
set updatetime=50

&quot; Don't pass messages to |ins-completion-menu|.
set shortmess+=c

set colorcolumn=100
highlight ColorColumn ctermbg=0 guibg=lightgrey

map &lt;C-n&gt; :NERDTreeToggle&lt;CR&gt;
map &lt;C-p&gt; :Files&lt;CR&gt;
map &lt;C-f&gt; :Rg&lt;CR&gt;
map &lt;C-t&gt; :e &lt;cfile&gt;&lt;cr&gt;
map &lt;S-Tab&gt; :bn&lt;CR&gt;
map &lt;F5&gt; :setlocal spell! spelllang=en_gb&lt;CR&gt;

let loaded_matchparen = 1
let mapleader = &quot; &quot;

&quot; CoC
&quot; GoTo code navigation.
nmap &lt;leader&gt;gd &lt;Plug&gt;(coc-definition)
nmap &lt;leader&gt;gy &lt;Plug&gt;(coc-type-definition)
nmap &lt;leader&gt;gi &lt;Plug&gt;(coc-implementation)
nmap &lt;leader&gt;gr &lt;Plug&gt;(coc-references)
nmap &lt;leader&gt;rr &lt;Plug&gt;(coc-rename)
nmap &lt;leader&gt;g[ &lt;Plug&gt;(coc-diagnostic-prev)
nmap &lt;leader&gt;g] &lt;Plug&gt;(coc-diagnostic-next)
nmap &lt;silent&gt; &lt;leader&gt;gp &lt;Plug&gt;(coc-diagnostic-prev-error)
nmap &lt;silent&gt; &lt;leader&gt;gn &lt;Plug&gt;(coc-diagnostic-next-error)
nnoremap &lt;leader&gt;cr :CocRestart

&quot; Sweet Sweet FuGITive
nmap &lt;leader&gt;gj :diffget //3&lt;CR&gt;
nmap &lt;leader&gt;gf :diffget //2&lt;CR&gt;
nmap &lt;leader&gt;gs :G&lt;CR&gt;

&quot; Search and replace hotkey
nnoremap H :%s//gc&lt;left&gt;&lt;left&gt;&lt;left&gt;

&quot; Move highlighted text up and down
vnoremap J :m '&gt;+1&lt;CR&gt;gv=gv
vnoremap K :m '&lt;-2&lt;CR&gt;gv=gv

&quot; Import plugins
if filereadable(expand(&quot;~/.vimrc.plug&quot;))
    source ~/.vimrc.plug
endif

&quot; Status bar config
set statusline+=%#warningmsg#

&quot; Fix files automatically on save
let g:ale_fixers = {}
let g:ale_javascript_eslint_use_global = 1
let g:ale_linters = {
  \'javascript': ['eslint'],
  \'vue': ['eslint', 'stylelint', 'tsserver'],
\}

let g:ale_fixers = {
  \'javascript': ['prettier', 'eslint'],
  \'vue': ['eslint', 'stylelint'],
\}

let g:ale_linters_explicit = 1
let g:ale_sign_column_always = 1
let g:ale_sign_error = '&gt;&gt;'
let g:ale_sign_warning = '--'
let g:ale_fix_on_save = 1

&quot; Close NERDTree when closing the last buffer
autocmd bufenter * if (winnr(&quot;$&quot;) == 1 &amp;&amp; exists(&quot;b:NERDTree&quot;) &amp;&amp; b:NERDTree.isTabTree()) | q | endif

fun! TrimWhitespace()
    let l:save = winsaveview()
    keeppatterns %s/\s\+$//e
    call winrestview(l:save)
endfun

autocmd BufWritePre * :call TrimWhitespace()

command! -bang -nargs=* Rg
  \ call fzf#vim#grep(
  \   'rg --column --line-number --no-heading --color=always --smart-case -- '.shellescape(&lt;q-args&gt;), 1,
  \   fzf#vim#with_preview(), &lt;bang&gt;0)

</code></pre>
<p>There is some initial setup prior to using this configuration. Such as:</p>
<ul>
<li>Running <code>:CocInstall coc-tsserver</code></li>
<li>Installing fzf <code>sudo apt-get install fzf</code></li>
<li>Installing Rg <code>sudo apt-get install ripgrep</code></li>
</ul>
<h2 id="finalpoints">Final points</h2>
<p>Vim is a steep learning curve. Get used to Vim by using a plugin in your existing IDE, once comfortable, switch to Vim.</p>
<p>Let me know if this post has helped you in the comments below, I'd appreciate your feedback.</p>
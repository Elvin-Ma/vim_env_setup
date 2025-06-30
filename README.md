# 0 vim_env_setup

# 1 prepare base env

- vim_env.sh
```sh
#!/bin/bash
# create vim dir
cd && mkdir -p .vim && cd .vim

# install vim-plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# install vim 8.2(optional) 当vim版本较低时可选择性安装
#git clone git@github.com:vim/vim.git
#cd vim
#git checkout v8.2.0108
##./configure --prefix=$HOME/.local --enable-python3interp=yes
#./configure --enable-python3interp=yes
# 安装不成功可能是环境依赖没装：apt-get update && apt-get install libncurses5-dev libncursesw5-dev
# 默认安装到 : export PATH=/usr/local/bin:$PATH
#make -j16
#sudo make install


apt-get purge vim vim-runtime vim-common vim-tiny
apt-get autoremove
apt --fix-broken install
apt update
apt install software-properties-common
add-apt-repository ppa:jonathonf/vim
apt update
apt install vim-gtk3 -y

# ctags + taglist + cscope prepare
# sudo apt-get install -y exuberant-ctags
apt install universal-ctags
# sudo apt-get install cscope
# sudo apt install global # gtags 教程引用数据库

# install vim-jedi for python
pip install jedi
pip install pygments # python语法高亮
apt update
apt install ripgrep

# prepare LSP env (optional)
pip install python-lsp-server
apt install -y curl
# curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt install -y nodejs
apt install clangd-14  # 或最新版本, 已安装久不用再装了
update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-14 100
```

# 2 set .vimrc

```sh
set tabstop=2
set shiftwidth=2
set expandtab
set smartindent
set autoindent
set showmode
set cursorline
set nocompatible
set colorcolumn=0
set backspace=indent,eol,start
set encoding=utf-8
set nu
set updatetime=200
set list " set nolist to disable
set listchars=space:· "空格以点来显示
set splitright
"nnoremap <silent> <Esc> :nohlsearch<CR>
filetype plugin on
set clipboard=unnamedplus
set hlsearch
" 从 viminfo 中移除缓冲区列表记录 (% 标志)
set viminfo-=%

let mapleader=";"
"let g:python3_host_prog = '/usr/bin/python3'
highlight search term=standout ctermfg=0 ctermbg=3 guifg=Blue guibg=Yellow

"Restore cursor to file position in previous editing session
set viminfo='10,\"100,:20,%,n~/.viminfo "记录光标位置
au BufReadPost * if line("'\"") > 0|if line("'\"") <= line("$")|exe("norm '\"")|else|exe "norm $"|endif|endif "重新打开光标位置不变
autocmd BufNewFile,BufRead *.cu set filetype=cpp "将cuda文件视为cpp文件

" window cut and size control
noremap <Leader>g1 :call win_gotoid(win_getid(1))<CR>
noremap <Leader>g2 :call win_gotoid(win_getid(2))<CR>
noremap <Leader>g3 :call win_gotoid(win_getid(3))<CR>
noremap <Leader>g4 :call win_gotoid(win_getid(4))<CR>
noremap <Leader>g5 :call win_gotoid(win_getid(5))<CR>
noremap <Leader>g6 :call win_gotoid(win_getid(6))<CR>
noremap <Leader>g7 :call win_gotoid(win_getid(7))<CR>
noremap <Leader>g8 :call win_gotoid(win_getid(8))<CR>

" window size
nnoremap <silent> <C-left>  <C-w><   " 缩小宽度
nnoremap <silent> <C-right> <C-w>>   " 增大宽度
nnoremap <silent> <C-up>    <C-w>-   " 缩小高度
nnoremap <silent> <C-down>  <C-w>+   " 增大高度

" 状态栏设定
set laststatus=2 " 始终显示状态栏
highlight StatusLine ctermfg=Black ctermbg=Gray guifg=#FFFFFF guibg=#444444 "活动窗口
highlight StatusLineNC ctermfg=Black ctermbg=Gray guifg=#000000 guibg=#888888 "非活动窗口
set statusline=%f\ %m%r%h%w\ [类型:%y]\ [行:%l/%L,\ 列:%c]\ [%p%%]
command! File execute 'file ' . expand('%:p') | echo " :File 获取完整文件路径

autocmd VimEnter * if !argc() | silent! %bdelete | endif

set autoread     " 允许自动读取外部修改
au FocusGained * checktime  " 当 Vim 获得焦点时检查文件
au CursorHold * checktime   " 光标停留时检查文件（需配合 updatetime）
set updatetime=1000         " 设置检查间隔为 1 秒（单位毫秒）

nnoremap <Leader>q :q<CR>     " 按 ,q 退出当前窗口
nnoremap <Leader>w :w<CR>     " 按 ,e 重新加载当前文件（保留修改）
nnoremap <Leader>e :e<CR>     " 按 ,e 重新加载当前文件（保留修改）

""""""""""""""""""""""""""""third-party plugin""""""""""""""""""""""""""""""

" plugged ______________
call plug#begin('~/.vim/plugged')
Plug 'preservim/nerdtree'
Plug 'vim-scripts/taglist.vim'
Plug 'Yggdroot/LeaderF'
Plug 'davidhalter/jedi-vim'
Plug 'https://gitcode.com/gh_mirrors/pyt/python-syntax.git'
Plug 'bfrg/vim-cpp-modern'

Plug 'https://gitcode.com/gh_mirrors/vi/vim-lsp.git' " LSP 核心
Plug 'https://gitcode.com/gh_mirrors/vi/vim-lsp-settings.git'
Plug 'https://gitcode.com/gh_mirrors/as/asyncomplete.vim.git'
Plug 'https://gitcode.com/gh_mirrors/as/asyncomplete-lsp.vim.git'
call plug#end()

" ==================simple config ==========

let g:Tlist_Use_Right_Window = 1
let g:Tlist_File_Fold_Auto_Close=1
let g:Tlist_Show_One_File=1
let g:python_highlight_all = 1

" ================== nerdtree ===========
autocmd VimEnter * NERDTree
wincmd w
autocmd VimEnter * wincmd w

" 1. 使用独立的 NERDTree 映射
map <leader>t :NERDTreeToggle<CR>
nmap <leader>nf :NERDTreeFind<CR>
nmap <leader>nm :NERDTreeMirror<CR>

" 2. 只在打开目录时自动显示 NERDTree
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 1 && isdirectory(argv()[0]) && !exists("s:std_in") |
    \ exe 'NERDTree' argv()[0] | wincmd p | ene | exe 'cd '.argv()[0] | endif

" 3. 安全关闭命令
command! SafeNERDTreeClose let b:nerdtree_auto_find_disabled = 1 <bar> NERDTreeClose

" ====================   Leaderf  ================
let g:Lf_WindowPosition = 'popup'
"let g:Lf_WindowPosition = 'left'
"let g:Lf_PreviewWidth = 100
let g:Lf_WorkingDirectoryMode='AF'
let g:Lf_RootMarkers = ['.git', '.svn', '.hg', '.project', '.root']
" 搜索时候排除如下
let g:Lf_WildIgnore={
    \ 'dir':['.svn', '.git', '.cache', '.gitIgnore', 'tags'],
    \ 'file':['*.idx', '*.so', '*.o', '.bak']
    \}
let g:Lf_DefaultSearchDir = system('git rev-parse --show-toplevel 2>/dev/null') " find from project other than file path
let g:Lf_DefaultExternalTool='rg'
" 启用版本控制集成: 会自动检测当前项目使用的版本控制系统
let g:Lf_UseVersionControlTool = 1
" 同时启用以下优化:优化可能会导致问题
let g:Lf_UseMemoryCache = 0           " 启用缓存加速
let g:Lf_UseCache = 0                 " 使用缓存
let g:Lf_FollowLinks = 1              " 跟随符号链接：结果列表中显示链接目标的实际路径，而非链接本身的路径
let g:Lf_IgnoreCurrentBufferName = 1  " 搜索结果排除当前缓冲区

let g:Lf_ShortcutF = '<c-p>' " 打开文件
"let g:Lf_ShortcutB = '<c-l>' "无效: 快速打开缓冲区列表
nnoremap <silent> <leader>lt :Leaderf bufTag<CR>
noremap <leader>fb :<C-U><C-R>=printf("Leaderf! buffer %s", "")<CR><CR>
noremap <leader>fm :<C-U><C-R>=printf("Leaderf! mru %s", "")<CR><CR>
noremap <leader>ft :<C-U><C-R>=printf("Leaderf! bufTag %s", "")<CR><CR>
"noremap <leader>fl :<C-U><C-R>=printf("Leaderf! line %s", "")<CR><CR>
noremap <leader>ff :<C-U><C-R>=printf("Leaderf! function %s", "")<CR><CR>
noremap <leader>fw :<C-U><C-R>=printf("Leaderf! window  %s", "")<CR><CR>

" 设置 LeaderF 默认 grep 命令（如果未安装 rg 则用 ag 或 grep）
if executable('rg')
    let g:Lf_GrepCommand = 'rg --no-heading --color never --smart-case'
elseif executable('ag')
    let g:Lf_GrepCommand = 'ag --nogroup --nocolor --smart-case'
else
    let g:Lf_GrepCommand = 'grep -rn --exclude-dir={.git,.svn} --color never'
endif

"在当前缓存区搜索当前光标的单词等价于 --> :Leaderf! rg --current-buffer -e {当前单词}
"noremap <C-B> :<C-U><C-R>=printf("Leaderf! rg --current-buffer -e %s ", expand("<cword>"))<CR>
"整个项目（工作目录）中搜索光标所在的单词 --> Leaderf! rg -e {当前单词}
"noremap <C-F> :<C-U><C-R>=printf("Leaderf! rg -e %s ", expand("<cword>"))<CR>

" 搜索可视化选择的文本字面量
"xnoremap gf :<C-U><C-R>=printf("Leaderf! rg -F -e %s ", leaderf#Rg#visual())<CR>
" 重新打开上层搜索结果
"noremap go :<C-U>Leaderf! rg --recall<CR>

" find word for self defined
" 上一次查询结果
nnoremap <silent> <Leader>wo :<C-U>Leaderf! rg --recall<CR>
"nnoremap <Leader>s :Leaderf! rg -e <Left>
nmap <unique> <leader>ws <Plug>LeaderfRgPrompt
" 禁用正则+完整匹配+区分大小写
noremap <leader>ww :<C-U><C-R>=printf("Leaderf! rg -w  -s -e %s ", expand("<cword>"))<CR>
"查询光标选中的内容
noremap <leader>wf :<C-U><C-R>=printf("Leaderf! rg -F -e %s ", expand("<cword>"))<CR>
"全局搜索,自动匹配
noremap <leader>we :<C-U><C-R>=printf("Leaderf! rg -e %s ", expand("<cword>"))<CR>
"在当前buffer中搜索,expand 自动获取光标单词,用vim自带的就可以？
noremap <leader>wb :<C-U><C-R>=printf("Leaderf! rg --current-buffer -e %s ", expand("<cword>"))<CR>

"" ========================LCP smart=======================
" 当LSP不可用时回退到tags
function! SmartGoToDefinition()
  if exists('*vim.lsp.buf.definition') && luaeval('vim.lsp.buf.server_ready()')
    lua vim.lsp.buf.definition()
  else
    GscopeFind g <cword>
  endif
endfunction
nnoremap gd :call SmartGoToDefinition()<cr>

" 启用 LSP 和补全
let g:lsp_diagnostics_enabled = 1
let g:lsp_diagnostics_echo_cursor = 1
let g:asyncomplete_auto_completeopt = 1

" 基础 LSP 配置
let g:lsp_log_verbose = 1
let g:lsp_log_file = expand('~/.vim/lsp.log')
let g:lsp_diagnostics_enabled = 1
let g:lsp_document_highlight_enabled = 1

" 启用 C++ 语言服务器(clangd)
augroup LspCxxSettings
  autocmd!
  autocmd User lsp_setup call lsp#register_server({
        \ 'name': 'clangd',
        \ 'cmd': {server_info->['clangd']},
        \ 'whitelist': ['c', 'cpp', 'objc', 'objcpp'],
        \ })
augroup END

" 启用 python 语言服务器
"if executable('pylsp')
"    " pip install python-lsp-server
"    au User lsp_setup call lsp#register_server({
"        \ 'name': 'pylsp',
"        \ 'cmd': {server_info->['pylsp']},
"        \ 'allowlist': ['python'],
"        \ })
"endif

function! s:on_lsp_buffer_enabled() abort
    setlocal omnifunc=lsp#complete
    setlocal signcolumn=yes
    if exists('+tagfunc') | setlocal tagfunc=lsp#tagfunc | endif
    nmap <buffer> gd <plug>(lsp-definition)
    nmap <buffer> gs <plug>(lsp-document-symbol-search)
    nmap <buffer> gS <plug>(lsp-workspace-symbol-search)
    nmap <buffer> gr <plug>(lsp-references)
    nmap <buffer> gi <plug>(lsp-implementation)
    nmap <buffer> gt <plug>(lsp-type-definition)
    nmap <buffer> <leader>rn <plug>(lsp-rename)
    nmap <buffer> [g <plug>(lsp-previous-diagnostic)
    nmap <buffer> ]g <plug>(lsp-next-diagnostic)
    nmap <buffer> K <plug>(lsp-hover)
    nnoremap <buffer> <expr><c-f> lsp#scroll(+4)
    nnoremap <buffer> <expr><c-b> lsp#scroll(-4)

    "" LSP基础功能映射
    "nnoremap <silent><leader>d <Plug>(lsp-definition)       " 跳转到定义处
    "nnoremap <silent><leader>r <Plug>(lsp-references)      " 查找引用
    "nnoremap <silent><leader>i <Plug>(lsp-implementation)  " 查找实现
    "nnoremap <silent><leader>t <Plug>(lsp-type-definition) " 查看类型定义
    "nnoremap <silent><leader>h <Plug>(lsp-hover)            " 显示悬停信息
    "nnoremap <silent><leader>s <Plug>(lsp-signature-help)   " 显示函数签名帮助
    "nnoremap <silent><leader>n <Plug>(lsp-rename)           " 重命名符号
    "nnoremap <silent><leader>f <Plug>(lsp-format)           " 格式化代码
    "nnoremap <silent><leader>a <Plug>(lsp-code-actions)     " 显示代码操作选项
    "nnoremap <silent><leader>e <Plug>(lsp-show-line-diagnostics) " 显示当前行诊断信息
    "
    "" 诊断导航映射
    "nnoremap <silent><leader>dn <Plug>(lsp-next-diagnostic) " 下一个诊断
    "nnoremap <silent><leader>dp <Plug>(lsp-previous-diagnostic) " 上一个诊断
    "nnoremap <silent><leader>df <Plug>(lsp-first-diagnostic) " 第一个诊断
    "nnoremap <silent><leader>dl <Plug>(lsp-last-diagnostic) " 最后一个诊断
    "
    "" LSP服务器管理
    "nnoremap <silent><leader>ls <Plug>(lsp-start-server) " 启动LSP服务器
    "nnoremap <silent><leader>lq <Plug>(lsp-stop-server)  " 停止LSP服务器
    "nnoremap <silent><leader>lr <Plug>(lsp-restart-server) " 重启LSP服务器
    "nnoremap <silent><leader>lc <Plug>(lsp-show-connections) " 显示服务器连接状态
    "
    "" 工作区管理
    "nnoremap <silent><leader>wa <Plug>(lsp-workspace-add) " 添加工作区文件夹
    "nnoremap <silent><leader>wr <Plug>(lsp-workspace-remove) " 移除工作区文件夹
    "nnoremap <silent><leader>wl <Plug>(lsp-workspace-list) " 列出工作区文件夹
    "
    "" 其他功能
    "nnoremap <silent><leader>o <Plug>(lsp-document-symbol) " 显示文档符号列表
    "nnoremap <silent><leader>w <Plug>(lsp-workspace-symbol) " 显示工作区符号列表

    let g:lsp_format_sync_timeout = 1000
    autocmd! BufWritePre *.rs,*.go call execute('LspDocumentFormatSync')

    " 参考文档以添加更多命令
endfunction

augroup lsp_install
    au!
    " 仅对已注册服务器的语言调用 s:on_lsp_buffer_enabled
    autocmd User lsp_buffer_enabled call s:on_lsp_buffer_enabled()
augroup END

augroup FixLspHighlight
  autocmd!
  " 1. 完全重置LSP高亮组
  autocmd ColorScheme * hi! LspReferenceText guibg=NONE ctermbg=NONE guifg=NONE ctermfg=NONE
  autocmd ColorScheme * hi! LspReferenceRead guibg=NONE ctermbg=NONE guifg=NONE ctermfg=NONE
  autocmd ColorScheme * hi! LspReferenceWrite guibg=NONE ctermbg=NONE guifg=NONE ctermfg=NONE

  " 2. 使用更明显的下划线替代背景色
  autocmd ColorScheme * hi! link LspReferenceText Underlined
  autocmd ColorScheme * hi! link LspReferenceRead Underlined
  autocmd ColorScheme * hi! link LspReferenceWrite Underlined

  " 3. 禁用自动文档高亮（可选）
  autocmd VimEnter * let g:lsp_document_highlight_enabled = 0
augroup END
```

# 3 key words

|插件名称|快捷键|功能描述|说明|
|:--|:--|:--|:--|
|NerdTree| \<leader>t| 切换tree显示状态 |
|NerdTree| \<leader>nf| 打开当前文件路径 |
|Taglist| :Tlist| 打开taglist窗口 |
|QuickFix | v | 垂直分割窗口 |
|QuickFix | <待补充> | 水平分割窗口 | 暂时失效 |
|leaderF | \<leader>fb | 当前缓冲区的文件列表 |
|leaderF | \<leader>fm | 项目内最近打开的文件列表| Most Rencent files |
|leaderF | \<leader>ft | 在当前文件搜索标签 | 函数、类、变量等 |
|leaderF | \<leader>fl | 当前文件line搜索 | 当前有问题，不建议使用 |
|leaderF | \<leader>ff | 当前文件的所有函数 | 需ctags插件支持 |
|leaderF | \<leader>fw | 快速切换窗口 | 暂时不用，有更好快捷键 |
|leaderF | \<leader>ww | 准确搜索当前单词 | -F 禁用正则表达式， -w 完整单词，-s  忽略大小写
|leaderF | \<leader>ws | 自己输入单词 |
|leaderF | \<leader>wo | 上次搜索的结果 |
|leaderF | \<leader>wf | 搜索**光标选中**内容 | -F 禁用正则表达式 |
|leaderF | \<leader>we | 模糊搜索当前单词 | 不完整单词，大小不敏感 |
|leaderF | :Leaderf! rg -e "text" --path=src/components/| 在特定目录下搜索 |
|leaderF | :Leaderf! rg -e "function" --type=python | 搜索特定文件类型 |
|leaderF | :Leaderf! rg -e "api" --exclude=node_modules --exclude=*.min.js |排除目录 |
|leaderF | :Leaderf! rg -e "User" --case-sensitive | 大小写敏感搜索 | -s 也可以，默认行为：被搜索的单词是小写则忽略，大写敏感|
|jedi| **\<leader>d** | 跳转到定义 |
|jedi| **\<leader>g** | 在新窗口打开 |
|jedi| **\<leader>n** | 当前符号的所有引用 |
|jedi| **\<leader>r** | 重命名当前符号 | 整个项目重命名 |
|jedi| **K** | 显示帮助文档 | Norm 模式下 |
|jedi| <Ctrl + Space> | 手动触发补全提示 |
|jedi| <Ctrl + s> | 显示函数签名提示 |
|lsp | **cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 build/** | 生成compile_commands.json |
|lsp | gd | 跳转到定义 |
|lsp | gs | 文档内搜索 |
|lsp | gS | 工作区符号搜索 |
|lsp | gr | 查找引用 |
|lsp | gi | 查找实现 | 对接口类/抽象类 找到其实现 |
|lsp | gt | 跳转到类型定义 | 找到类型type定义之处 |
|lsp | \<leader>rn | 重命名符号 | 自动修改所有引用 |
|lsp | [g | 跳到上一个诊断错误 |
|lsp | ]g | 跳到下一个诊断错误 |
|lsp | K| 悬停文档 | 相关注释 | 同 jedi |
|lsp | <c-f> | 向上滚动悬停窗口 |
|lsp | <c-b> | 向下滚动悬停窗口 |


# 4 other configuration

```sh
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 build/

# 安装帮助：https://stackoverflow.com/questions/2817869/error-unable-to-find-vcvarsall-bat
# 运行任意LeaderF命令后，可通过echo g:Lf_fuzzyEngine_C检查值，如果结果是1，则表明C扩展成功加载
:LeaderfInstallCExtension # C 扩展，使得leaderf更快
:LeaderfUninstallCExtension # 卸载 C 扩展
```

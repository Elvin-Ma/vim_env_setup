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

# ctags + taglist + cscope prepare
# sudo apt-get install -y exuberant-ctags
sudo apt install universal-ctags
sudo apt-get install cscope
# sudo apt install global # gtags 教程引用数据库

# install vim-jedi for python
pip install jedi
pip install pygments # python语法高亮
sudo apt update
sudo apt install ripgrep

# prepare LSP env (optional)
pip install python-lsp-server
sudo apt update && sudo apt install -y curl
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt install clangd-14  # 或最新版本
sudo update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-14 100
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

let mapleader=";"
let g:python3_host_prog = '/usr/bin/python3'
set hlsearch
highlight search term=standout ctermfg=0 ctermbg=3 guifg=Blue guibg=Yellow

"Restore cursor to file position in previous editing session
set viminfo='10,\"100,:20,%,n~/.viminfo "记录光标位置
au BufReadPost * if line("'\"") > 0|if line("'\"") <= line("$")|exe("norm '\"")|else|exe "norm $"|endif|endif "重新打开光标位置不变
autocmd BufNewFile,BufRead *.cu set filetype=cpp "将cuda文件视为cpp文件


" plugged ______________
call plug#begin('~/.vim/plugged')
Plug 'vim-scripts/taglist.vim'
Plug 'preservim/nerdtree'
Plug 'vim-airline/vim-airline-themes'
Plug 'vim-airline/vim-airline' "增强 Vim 状态栏的外观和功能
Plug 'Yggdroot/LeaderF'
Plug 'bfrg/vim-cpp-modern' "c++ 函数名等会有颜色
Plug 'zivyangll/git-blame.vim'
Plug 'jiangmiao/auto-pairs' "括号自动补全
Plug 'azabiong/vim-highlighter'
Plug 'davidhalter/jedi-vim' "python 补全和跳转
Plug 'https://gitcode.com/gh_mirrors/vim/vim-autoformat.git'

Plug 'ludovicchabant/vim-gutentags'
"Plug 'skywind3000/gutentags_plus'

"" LSP 核心插件
"Plug 'prabirshrestha/vim-lsp'
"Plug 'mattn/vim-lsp-settings'       " 自动配置语言服务器
"Plug 'prabirshrestha/asyncomplete.vim'
"Plug 'prabirshrestha/asyncomplete-lsp.vim'
"
"" 可选：UI 增强
"Plug 'lightline.vim'                " 状态栏美化
"Plug 'ryanoasis/vim-devicons'       " 图标支持

"""lsp mirror image within the wall"""
"Plug 'https://gitcode.com/gh_mirrors/vi/vim-lsp.git' " LSP 核心
"Plug 'https://gitcode.com/gh_mirrors/vi/vim-lsp-settings.git'
"Plug 'https://gitcode.com/gh_mirrors/as/asyncomplete.vim.git'
"Plug 'https://gitcode.com/gh_mirrors/as/asyncomplete-lsp.vim.git'
"Plug 'https://gitcode.com/gh_mirrors/li/lightline.vim.git'
"Plug 'https://gitcode.com/gh_mirrors/vi/vim-devicons.git'

call plug#end()

" ================== nerdtree ===========
set laststatus=2
let g:airline_powerline_fonts = 1
let g:airline#extensions#tabline#enabled = 1
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

" ======================= Auto format ================
let g:autoformat_autoindent = 1
let g:autoformat_retab = 1
let g:autoformat_remove_trailing_spaces = 1
let g:autoformat_c_cpp_clangformat = 'clang-format'
let g:formatters_python = ['black']
" 设置保存时自动格式化
augroup autoformat_on_save
  autocmd!
  autocmd BufWrite *.cpp,*.h,*.hpp,*.cu,*.py,*.js,*.ts :Autoformat
augroup END

noremap <Leader>af :Autoformat<CR>

" ==================== git blame ===========
nnoremap <Leader>gb :<C-u>call gitblame#echo()<CR>

" ==================== tab list ============
let g:Tlist_Use_Right_Window = 1
let g:Tlist_File_Fold_Auto_Close=1
let g:Tlist_Show_One_File=1
nnoremap <silent> <leader>to : TlistOpen<CR>
nnoremap <silent> <leader>tc :TlistClose<CR>

" ====================   Leaderf  ================
"let g:Lf_WindowPosition = 'popup'
let g:Lf_WindowPosition = 'left'
let g:Lf_PreviewWidth = 100
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
" 同时启用以下优化：
let g:Lf_UseMemoryCache = 1           " 启用缓存加速
let g:Lf_UseCache = 1                 " 使用缓存
let g:Lf_FollowLinks = 1              " 跟随符号链接
let g:Lf_IgnoreCurrentBufferName = 1  " 搜索结果排除当前缓冲区

let g:Lf_ShortcutF = '<c-p>' " 打开文件
"let g:Lf_ShortcutB = '<c-l>' "无效: 快速打开缓冲区列表
nnoremap <silent> <leader>lt :Leaderf bufTag<CR>
noremap <leader>fb :<C-U><C-R>=printf("Leaderf! buffer %s", "")<CR><CR>   " 打开缓冲区列表搜索
noremap <leader>fm :<C-U><C-R>=printf("Leaderf! mru %s", "")<CR><CR>      " 打开最近编辑文件列表: Most recent list
noremap <leader>ft :<C-U><C-R>=printf("Leaderf! bufTag %s", "")<CR><CR>   " 在当前缓存区搜索标签(函数、类、变量等)，需ctags支持
"noremap <leader>fl :<C-U><C-R>=printf("Leaderf! line %s", "")<CR><CR>     " 当前文件line搜索, 类似当前文件的grep ATTENTION: 不太生效
noremap <leader>ff :<C-U><C-R>=printf("Leaderf! function %s", "")<CR><CR> " 当前文件的函数列表
noremap <leader>fw :<C-U><C-R>=printf("Leaderf! window  %s", "")<CR><CR>  " vim 窗口快速切换

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
nmap <unique> <leader>wg <Plug>LeaderfRgPrompt
" 禁用正则+完整匹配+区分大小写
noremap <leader>ww :<C-U><C-R>=printf("Leaderf! rg -w  -s -e %s ", expand("<cword>"))<CR>
"查询光标选中的内容
noremap <leader>wf :<C-U><C-R>=printf("Leaderf! rg -F -e %s ", expand("<cword>"))<CR>
"全局搜索,自动匹配
noremap <leader>ws :<C-U><C-R>=printf("Leaderf! rg -e %s ", expand("<cword>"))<CR>
"在当前buffer中搜索,expand 自动获取光标单词,用vim自带的就可以？
noremap <leader>wb :<C-U><C-R>=printf("Leaderf! rg --current-buffer -e %s ", expand("<cword>"))<CR>

"=========================== gutentags ctags auto generate =========================
" auto vim-gutentags method
let g:gutentags_enabled = 1
let g:gutentags_ctags_auto_set_tags = 1 " 如果禁用，需要手动设置 tags 路径: set tags=./tags;,tags;
let g:gutentags_modules = ['ctags']
let g:gutentags_cache_dir = expand('~/.cache/vim/tags')
let g:gutentags_project_root = ['.root', '.git', '.svn', '.hg', '.project']
" 创建缓存目录（如果不存在）
if !isdirectory(g:gutentags_cache_dir)
    silent! call mkdir(g:gutentags_cache_dir, 'p')
endif

" 核心排除配置
let g:gutentags_ctags_exclude = [
    \ '*.git*', '*.svn*', '*.hg*',
    \ '*.venv*', '*venv*', '*virtualenv*',
    \ '*.env*', 'env.bak', 'venv.bak',
    \ 'build*', 'dist*', 'bin*', 'out*',
    \ 'target*', '*.class', '*.jar',
    \ 'node_modules*', 'bower_components*',
    \ 'third-party*', 'third_party*',
    \ '3rdparty*', 'externals*', 'vendor*',
    \ '*.min.js', '*.min.css',
    \ '*.pyc', '*.pyo', '__pycache__',
    \ '*.o', '*.a', '*.so', '*.dll', '*.exe',
    \ '*.log', '*.cache', '*.bak', '*.swp',
    \ '*.png', '*.jpg', '*.jpeg', '*.gif',
    \ '*.pdf', '*.doc', '*.docx', '*.xls',
    \ '*.zip', '*.tar.gz', '*.7z', '*.rar',
    \ 'tags*', 'cscope.*', 'GTAGS', 'GRTAGS',
    \ 'CMakeFiles*', 'Makefile', 'makefile',
    \ '.idea', '.vscode',
    \ '.cache', '.tmp', '.temp',
    \ 'package-lock.json', 'yarn.lock',
    \ '.DS_Store', 'Thumbs.db'
\ ]
" 根据文件类型排除
let g:gutentags_ctags_exclude += [
    \ '*.min.js', '*.min.css',
    \ '*.json', '*.log',
    \ '*.md', '*.txt',
    \ '*.xml', '*.yml', '*.yaml',
    \ '*.svg', '*.ico',
    \ '*.sql', '*.dump',
    \ '*.spec', '*.test'
\ ]
" 排除大文件
let g:gutentags_ctags_max_file_size = 5 * 1024 * 1024  " 5MB
" 限制标签文件扫描深度
let g:gutentags_file_list_depth = 10


" 减少实时更新频率
let g:gutentags_generate_on_write = 1
let g:gutentags_generate_on_missing = 1
let g:gutentags_generate_on_new = 0

" 大型项目优化
let g:gutentags_pause_after_update = 500  " 500ms 延迟
let g:gutentags_background_update = 1     " 后台更新


nnoremap <leader>go <C-T>
set statusline+=%{gutentags#statusline()} "在状态栏显示状态

" =================== window cut and size control ===============
noremap <Leader>g1 :call win_gotoid(win_getid(1))<CR>
noremap <Leader>g2 :call win_gotoid(win_getid(2))<CR>
noremap <Leader>g3 :call win_gotoid(win_getid(3))<CR>
noremap <Leader>g4 :call win_gotoid(win_getid(4))<CR>
noremap <Leader>g5 :call win_gotoid(win_getid(5))<CR>
noremap <Leader>g6 :call win_gotoid(win_getid(6))<CR>
noremap <Leader>g7 :call win_gotoid(win_getid(7))<CR>
noremap <Leader>g8 :call win_gotoid(win_getid(8))<CR>

"窗口大小
nnoremap <silent> <C-left>  <C-w><   " 缩小宽度
nnoremap <silent> <C-right> <C-w>>   " 增大宽度
nnoremap <silent> <C-up>    <C-w>-   " 缩小高度
nnoremap <silent> <C-down>  <C-w>+   " 增大高度

" 在 Quickfix 窗口分割打开
autocmd FileType qf nnoremap <buffer> <leader>v <C-w><C-v><CR>  " 垂直分割
autocmd FileType qf nnoremap <buffer> <leader>s <C-w><C-s><CR>  " 水平分割 -- 不生效

"" ========================LCP smart=======================
"" 当LSP不可用时回退到tags
"function! SmartGoToDefinition()
"  if exists('*vim.lsp.buf.definition') && luaeval('vim.lsp.buf.server_ready()')
"    lua vim.lsp.buf.definition()
"  else
"    GscopeFind g <cword>
"  endif
"endfunction
"nnoremap gd :call SmartGoToDefinition()<cr>
"
"" 启用 LSP 和补全
"let g:lsp_diagnostics_enabled = 1
"let g:lsp_diagnostics_echo_cursor = 1
"let g:asyncomplete_auto_completeopt = 1
"
"" 基础 LSP 配置
"let g:lsp_log_verbose = 1
"let g:lsp_log_file = expand('~/.vim/lsp.log')
"let g:lsp_diagnostics_enabled = 1
"let g:lsp_document_highlight_enabled = 1
"
"" 启用 C++ 语言服务器（clangd）
"augroup LspCxxSettings
"  autocmd!
"  autocmd User lsp_setup call lsp#register_server({
"        \ 'name': 'clangd',
"        \ 'cmd': {server_info->['clangd']},
"        \ 'whitelist': ['c', 'cpp', 'objc', 'objcpp'],
"        \ })
"augroup END
"
"" 启用 python 语言服务器
"if executable('pylsp')
"    " pip install python-lsp-server
"    au User lsp_setup call lsp#register_server({
"        \ 'name': 'pylsp',
"        \ 'cmd': {server_info->['pylsp']},
"        \ 'allowlist': ['python'],
"        \ })
"endif
"
"function! s:on_lsp_buffer_enabled() abort
"    setlocal omnifunc=lsp#complete
"    setlocal signcolumn=yes
"    if exists('+tagfunc') | setlocal tagfunc=lsp#tagfunc | endif
"    nmap <buffer> gd <plug>(lsp-definition)
"    nmap <buffer> gs <plug>(lsp-document-symbol-search)
"    nmap <buffer> gS <plug>(lsp-workspace-symbol-search)
"    nmap <buffer> gr <plug>(lsp-references)
"    nmap <buffer> gi <plug>(lsp-implementation)
"    nmap <buffer> gt <plug>(lsp-type-definition)
"    nmap <buffer> <leader>rn <plug>(lsp-rename)
"    nmap <buffer> [g <plug>(lsp-previous-diagnostic)
"    nmap <buffer> ]g <plug>(lsp-next-diagnostic)
"    nmap <buffer> K <plug>(lsp-hover)
"    nnoremap <buffer> <expr><c-f> lsp#scroll(+4)
"    nnoremap <buffer> <expr><c-d> lsp#scroll(-4)
"
"    "" LSP基础功能映射
"    "nnoremap <silent><leader>d <Plug>(lsp-definition)       " 跳转到定义处
"    "nnoremap <silent><leader>r <Plug>(lsp-references)      " 查找引用
"    "nnoremap <silent><leader>i <Plug>(lsp-implementation)  " 查找实现
"    "nnoremap <silent><leader>t <Plug>(lsp-type-definition) " 查看类型定义
"    "nnoremap <silent><leader>h <Plug>(lsp-hover)            " 显示悬停信息
"    "nnoremap <silent><leader>s <Plug>(lsp-signature-help)   " 显示函数签名帮助
"    "nnoremap <silent><leader>n <Plug>(lsp-rename)           " 重命名符号
"    "nnoremap <silent><leader>f <Plug>(lsp-format)           " 格式化代码
"    "nnoremap <silent><leader>a <Plug>(lsp-code-actions)     " 显示代码操作选项
"    "nnoremap <silent><leader>e <Plug>(lsp-show-line-diagnostics) " 显示当前行诊断信息
"    "
"    "" 诊断导航映射
"    "nnoremap <silent><leader>dn <Plug>(lsp-next-diagnostic) " 下一个诊断
"    "nnoremap <silent><leader>dp <Plug>(lsp-previous-diagnostic) " 上一个诊断
"    "nnoremap <silent><leader>df <Plug>(lsp-first-diagnostic) " 第一个诊断
"    "nnoremap <silent><leader>dl <Plug>(lsp-last-diagnostic) " 最后一个诊断
"    "
"    "" LSP服务器管理
"    "nnoremap <silent><leader>ls <Plug>(lsp-start-server) " 启动LSP服务器
"    "nnoremap <silent><leader>lq <Plug>(lsp-stop-server)  " 停止LSP服务器
"    "nnoremap <silent><leader>lr <Plug>(lsp-restart-server) " 重启LSP服务器
"    "nnoremap <silent><leader>lc <Plug>(lsp-show-connections) " 显示服务器连接状态
"    "
"    "" 工作区管理
"    "nnoremap <silent><leader>wa <Plug>(lsp-workspace-add) " 添加工作区文件夹
"    "nnoremap <silent><leader>wr <Plug>(lsp-workspace-remove) " 移除工作区文件夹
"    "nnoremap <silent><leader>wl <Plug>(lsp-workspace-list) " 列出工作区文件夹
"    "
"    "" 其他功能
"    "nnoremap <silent><leader>o <Plug>(lsp-document-symbol) " 显示文档符号列表
"    "nnoremap <silent><leader>w <Plug>(lsp-workspace-symbol) " 显示工作区符号列表
"
"    let g:lsp_format_sync_timeout = 1000
"    autocmd! BufWritePre *.rs,*.go call execute('LspDocumentFormatSync')
"
"    " 参考文档以添加更多命令
"endfunction
"
"augroup lsp_install
"    au!
"    " 仅对已注册服务器的语言调用 s:on_lsp_buffer_enabled
"    autocmd User lsp_buffer_enabled call s:on_lsp_buffer_enabled()
"augroup END
```

# 3 key words

|插件名称|快捷键|功能描述|说明|
|:--|:--|:--|:--|
|NerdTree| \<leader>t| 切换tree显示状态 |
|NerdTree| \<leader>nf| 打开当前文件路径 |
|Taglist| \<leader>to| 打开taglist窗口 |
|Taglist| \<leader>tc| 关闭taglist窗口 |
|ctags | ctrl + ] | 跳转到定义 | 需ctags插件支持 |
|ctags | **g + ]** | 在预览窗口打开符号定义 | :GutentagsUpdate 可更新
|ctags | \<leader>go | 返回上次跳转位置 | ctrl + t 被其他快捷键占用了
|QuickFix | v | 垂直分割窗口 |
|QuickFix | <待补充> | 水平分割窗口 | 暂时失效 |
|leaderF | \<leader>fb | 当前缓冲区的文件 |
|leaderF | \<leader>fm | 项目内最近打开的文件| Most Rencent files |
|leaderF | \<leader>ft | 在当前缓冲区搜索标签 | 函数、类、变量等 |
|leaderF | \<leader>fl | 当前文件line搜索 | 当前有问题，不建议使用 |
|leaderF | \<leader>ff | 当前文件的所有函数 | 需ctags插件支持 |
|leaderF | \<leader>fw | 快速切换窗口 | 暂时不用，有更好快捷键 |
|leaderF | \<leader>ww | 准确搜索当前单词 | -F 禁用正则表达式， -w 完整单词，-s  忽略大小写
|leaderF | \<leader>wg | 自己输入单词 |
|leaderF | \<leader>wo | 上此搜索的结果 |
|leaderF | \<leader>wf | 搜索光标选中内容 | -F 禁用正则表达式 |
|leaderF | :Leaderf! rg -e "text" --path=src/components/| 在特定目录下搜索 |
|leaderF | :Leaderf! rg -e "function" --type=python | 搜索特定文件类型 |
|leaderF | :Leaderf! rg -e "api" --exclude=node_modules --exclude=*.min.js |排除目录 |
|leaderF | :Leaderf! rg -e "User" --case-sensitive | 大小写敏感搜索 | -s 也可以，默认行为：被搜索的单词是小写则忽略，大写敏感|
|jedi| **<leader>d** | 跳转到定义 |
|jedi| **<leader>g** | 在新窗口打开 |
|jedi| **<leader>n** | 当前符号的所有引用 |
|jedi| **<leader>r** | 重命名当前符号 | 整个项目重命名 |
|jedi| **K** | 显示帮助文档 | Norm 模式下 |
|jedi| <Ctrl + Space> | 手动触发补全提示 |
|jedi| <Ctrl + s> | 显示函数签名提示 |


# 4 other configuration

```sh
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 build/

# 安装帮助：https://stackoverflow.com/questions/2817869/error-unable-to-find-vcvarsall-bat
# 运行任意LeaderF命令后，可通过echo g:Lf_fuzzyEngine_C检查值，如果结果是1，则表明C扩展成功加载
:LeaderfInstallCExtension # C 扩展，使得leaderf更快
:LeaderfUninstallCExtension # 卸载 C 扩展
```

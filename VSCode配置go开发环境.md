## VSCode配置go开发环境

#### 打开GoMOD、再配置代理
    go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct
#### 在VSCode中安装Go插件
    code --install-extension golang.go
#### 安装插件
    go get -u -v golang.org/x/lint/golint
    go get -u -v github.com/sqs/goreturns
    go get -u -v github.com/nsf/gocode
    go get -u -v github.com/uudashr/gopkgs/cmd/gopkgs@latest
    go get -u -v github.com/ramya-rao-a/go-outline
    go get -u -v github.com/acroca/go-symbols
    go get -u -v golang.org/x/tools/cmd/gorename
    go get -u -v golang.org/x/tools/cmd/guru
    go get -u -v github.com/go-delve/delve/cmd/dlv    
    go get -u -v golang.org/x/tools
    go get -u -v github.com/josharian/impl
    go get -u -v github.com/cweill/gotests
    go get -u -v github.com/rogpeppe/godef
    //go get -u -v github.com/redefiance/go-find-references
    //go get -u -v github.com/redefiance/ident
    go get -u -v github.com/warrior21st/ident@v0.0.0-20210712074938-4979a2ba59cd
    go get -u -v github.com/warrior21st/go-find-references@v0.0.0-20210712075418-eab9e8e70737
    go get golang.org/x/tools/gopls@latest

#### 修改vscode settings.json,按ctrl+shift+p，输入settings，点击Open Settings(JSON)
    {        
        "go.formatTool": "gofmt",
        "go.gocodePackageLookupMode": "go",
        "go.gotoSymbol.includeImports": true,
        "go.useCodeSnippetsOnFunctionSuggest": true,
        "go.useCodeSnippetsOnFunctionSuggestWithoutType": true,
        "go.gotoSymbol.includeGoroot": true,
        "go.autocompleteUnimportedPackages": true,
        "go.useLanguageServer": true,
        "editor.quickSuggestions": true,
        "editor.suggest.snippetsPreventQuickSuggestions": false
    }

#### 修改vscode工作区设置(用于历史的项目)
- 文件 -> 首选项 -> 设置 -> 工作区
- 搜索 prevent,将"控制活动代码段是否阻止快速建议"去掉勾选


#### windows mingw-w64 install
- 打开 https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/ 点击 MinGW-W64 Online Installer 下载mingw-w64安装器
- 安装器文件地址: https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe
- 打开安装器，Version选择需要的版本，Architecture选择x84-64，一路点击NEXT直到完成
- 将安装路径下的 mingw64\bin 目录添加到系统环境变量的 PATH 中，默认安装路径：C:\Program Files\mingw-w64\x86_64-8.1.0-posix-seh-rt_v6-rev0
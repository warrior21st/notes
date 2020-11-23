## VSCode配置go开发环境

#### 打开GoMOD、再配置代理
    go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct
#### 在VSCode中安装Go插件
    code --install-extension golang.go
#### 安装插件
    go get -u -v golang.org/x/lint/golint
    go get -u -v github.com/sqs/goreturns
    go get -u -v github.com/mdempsky/gocode
    go get -u -v github.com/uudashr/gopkgs/cmd/gopkgs
    go get -u -v github.com/ramya-rao-a/go-outline
    go get -u -v github.com/acroca/go-symbols
    go get -u -v golang.org/x/tools/cmd/gorename
    go get -u -v golang.org/x/tools/cmd/guru
    go get -u -v github.com/derekparker/delve/cmd/dlv    
    go get -u -v github.com/golang/tools
    go get -u -v github.com/josharian/impl
    go get -u -v github.com/cweill/gotests
    go get -u -v github.com/rogpeppe/godef

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
## VSCode搭建go开发环境
### 设置GOPATH路径（GOPATH路径是我们的工作区）
    go env -w GOPATH=工作区路径
### 打开GoMOD、再配置代理
    go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct
### 在VSCode中安装Go插件
    code --install-extension golang.go
### 安装插件
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
### 设置fmt
- 切到vscode，按ctrl+shift+p,输入settings,点击Open Settings(JSON)
- 增加 "go.formatTool": "gofmt"
    
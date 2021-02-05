ARG GOVERSION="latest"
FROM golang:${GOVERSION}

ARG GROUPID=500
ARG GROUPNAME="develop"
ARG USERID=1000
ARG USERNAME="developer"

ARG GOPATH="/home/${USERNAME}/go"

# 配置开发环境依赖的工具和设置
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak && \
    echo " " > /etc/apt/sources.list && \
    echo "deb http://mirrors.aliyun.com/debian buster main" >> /etc/apt/sources.list && \
    echo "deb http://mirrors.aliyun.com/debian buster-updates main" >> /etc/apt/sources.list && \
    apt-get clean && apt-get update && apt-get autoremove -y && \
    apt-get install -y ripgrep vim sudo && \
    # 修改时区
    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    # 这里用projects目录作为gomodule项目的根目录
    mkdir -p ${GOPATH}/bin ${GOPATH}/pkg  ${GOPATH}/projects

# 为开发环境配置用户，避免使用root用户
# 同时为用户分配sudo，方便执行一些root命令
RUN groupadd -r -g ${GROUPID} ${GROUPNAME} && \
    useradd -r -m -g ${GROUPNAME} -u ${USERID} -s /bin/bash ${USERNAME} && \
    # Warning: 这里一定要将$HOME目录的的权限改成$USERNAE否则vscode中无法初始化容器
    chown -R ${USERID}:$GROUPID /home/${USERNAME} && \
    echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME}

ENV GO111MODULE=on \
    GOPATH=${GOPATH} \
    GOBIN=${GOPATH}/bin \
    PATH=$PATH:/usr/local/go/bin:${GOPATH}/bin \
    GOPROXY=https://goproxy.io,direct \
    GONOSUMDB=https://goproxy.io \
    LESSCHARSET=utf-8 \
    SHELL=/bin/bash8

WORKDIR ${GOPATH}

USER ${USERNAME}
EXPOSE 8080
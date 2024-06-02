FROM alpine

COPY package_list.txt /tmp/package_list.txt
COPY requirements.txt /tmp/requirements.txt 



ENV LANG="C.UTF-8" \
    TZ="Asia/Shanghai" \
    NASTOOL_CONFIG="/config/config.yaml" \
    NASTOOL_AUTO_UPDATE=false \
    NASTOOL_CN_UPDATE=false \
    NASTOOL_VERSION=master \
    PS1="\u@\h:\w \$ " \
    REPO_URL="https://github.com/zengxi131/nas-tools.git" \
    PYPI_MIRROR="https://pypi.tuna.tsinghua.edu.cn/simple" \
    ALPINE_MIRROR="mirrors.ustc.edu.cn" \
    PUID=0 \
    PGID=0 \
    UMASK=000 \
    WORKDIR="/nas-tools"

RUN echo "https://mirrors.tuna.tsinghua.edu.cn/alpine/v3.14/main" > /etc/apk/repositories 
RUN echo "https://mirrors.tuna.tsinghua.edu.cn/alpine/v3.14/community" >> /etc/apk/repositories 
RUN apk update 

RUN apk add --no-cache libffi-dev 
RUN apk add --no-cache git 
RUN apk add --no-cache gcc 
RUN apk add --no-cache musl-dev 
RUN apk add --no-cache python3-dev


RUN apk add --no-cache py3-pip  



RUN apk add --no-cache libxml2-dev 
RUN apk add --no-cache libxslt-dev
RUN apk add --no-cache tzdata
RUN apk add --no-cache su-exec
RUN apk add --no-cache zip
RUN apk add --no-cache curl
RUN apk add --no-cache bash
RUN apk add --no-cache fuse
RUN apk add --no-cache xvfb
RUN apk add --no-cache inotify-tools
RUN apk add --no-cache chromium-chromedriver
RUN apk add --no-cache npm
RUN apk add --no-cache dumb-init
RUN apk add --no-cache ffmpeg


RUN apk add ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime 
RUN echo "${TZ}" > /etc/timezone 
RUN ln -sf /usr/bin/python3 /usr/bin/python 
#RUN curl https://rclone.org/install.sh | bash 
RUN if [ "$(uname -m)" = "x86_64" ]; then ARCH=amd64; elif [ "$(uname -m)" = "aarch64" ]; then ARCH=arm64; fi 
RUN curl https://dl.min.io/client/mc/release/linux-$ARCH/mc --create-dirs -o /usr/bin/mc 
RUN chmod +x /usr/bin/mc 
RUN pip install --upgrade pip setuptools wheel -i ${PYPI_MIRROR} 
RUN pip install cython -i ${PYPI_MIRROR} 

RUN pip install -r /tmp/requirements.txt -i ${PYPI_MIRROR} 

RUN apk del libffi-dev 
RUN npm install pm2 -g 
RUN rm -rf /tmp/* /root/.cache /var/cache/apk/*





WORKDIR ${WORKDIR}

RUN python_ver=$(python3 -V | awk '{print $2}') \
    && echo "${WORKDIR}/" > /usr/lib/python${python_ver%.*}/site-packages/nas-tools.pth \
    && echo 'fs.inotify.max_user_watches=524288' >> /etc/sysctl.conf \
    && echo 'fs.inotify.max_user_instances=524288' >> /etc/sysctl.conf \
    && git config --global pull.ff only \
    && git clone -b nastoolv2 ${REPO_URL} ${WORKDIR} --depth=1 --recurse-submodule \
    && git config --global --add safe.directory ${WORKDIR}

EXPOSE 3000
VOLUME ["/config"]
ENTRYPOINT ["/nas-tools/docker/entrypoint.sh"]
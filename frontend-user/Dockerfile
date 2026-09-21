# 使用官方 nginx 镜像（跨平台支持 ARM 和 X86）
FROM --platform=$BUILDPLATFORM nginx:alpine

# 设置工作目录
WORKDIR /usr/share/nginx/html

# 删除默认页面
RUN rm -rf ./*

# 复制静态文件（含自定义错误页）
COPY index.html 404.html 50x.html ./
COPY css/ ./css/
COPY js/ ./js/

# 复制 nginx 配置与公共片段
COPY snippets/ /etc/nginx/snippets/
COPY nginx.conf /etc/nginx/conf.d/default.conf

# 暴露端口
EXPOSE 80

# 启动 nginx
CMD ["nginx", "-g", "daemon off;"]

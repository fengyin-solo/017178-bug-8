# 使用官方 nginx 镜像（跨平台支持 ARM 和 X86）
FROM --platform=$BUILDPLATFORM nginx:alpine

# 允许内嵌本页面的来源（CSP frame-ancestors）：
#   *                         允许任意页面内嵌（默认，保持现有内嵌集成可用）
#   'self'                    只允许同源内嵌
#   'self' https://a.example  只允许同源 + 指定站点
# 容器启动时由官方 20-envsubst-on-templates.sh 替换到 nginx 配置中。
# 输出根设为 /etc/nginx，使 templates/conf.d 与 templates/snippets
# 分别渲染到 /etc/nginx/conf.d 与 /etc/nginx/snippets（子目录会自动创建）。
ENV FRAME_ANCESTORS=* \
    NGINX_ENVSUBST_OUTPUT_DIR=/etc/nginx

# 设置工作目录
WORKDIR /usr/share/nginx/html

# 删除默认页面
RUN rm -rf ./*

# 复制静态文件与错误页
COPY index.html 404.html 50x.html ./
COPY css/ ./css/
COPY js/ ./js/

# 复制 nginx 配置模板（容器启动时经 envsubst 渲染到 /etc/nginx）
COPY templates/ /etc/nginx/templates/

# 暴露端口
EXPOSE 80

# 启动 nginx（官方 entrypoint 会自动渲染 templates/ 中的模板）
CMD ["nginx", "-g", "daemon off;"]

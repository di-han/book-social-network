# ============================
# Stage 1: 构建阶段 (Builder)
# ============================
# 使用 node.js 作为基础镜像，并将此阶段命名为 build
FROM node:22.13-alpine AS build
# 工作目录
WORKDIR /app
# 不仅要复制 package.json 用于查找依赖项，还有 package-lock.json 用于锁定项目依赖项的版本
COPY package*.json ./

RUN npm install
# 将前端项目里的所有复制到工作目录下
COPY . .
# 构建，会在 /app/dist 生成静态文件
RUN npm run build

# ============================
# Stage 2: 运行阶段 (Runtime)
# ============================
FROM nginx:alpine

# 复制自定义的 Nginx 配置
COPY nginx.conf /etc/nginx/nginx.conf

# 从构建阶段复制静态文件到 Nginx 目录
COPY --from=build /app/dist /usr/share/nginx/html

# 暴露端口
EXPOSE 80



# Maxun Docker 部署指南

本指南详细说明如何在 Windows 系统上使用 Docker 部署 [Maxun](https://github.com/getmaxun/maxun)，一个开源的无代码网页数据提取平台。Maxun 通过 Docker Compose 集成前端、后端、PostgreSQL、Redis 和 MinIO，简化了依赖管理。本指南适合熟悉 Anaconda 环境（`ocrmypdf`）并需要抓取中文网页的用户。

## 目录

1. [概述](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E6%A6%82%E8%BF%B0)
2. [系统要求](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E7%B3%BB%E7%BB%9F%E8%A6%81%E6%B1%82)
3. [安装 Docker](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%AE%89%E8%A3%85-docker)
4. [准备 Maxun 项目](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%87%86%E5%A4%87-maxun-%E9%A1%B9%E7%9B%AE)
5. [配置环境变量](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)
6. [部署 Maxun](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E9%83%A8%E7%BD%B2-maxun)
7. [基本用法](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
8. [优化中文抓取](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E4%BC%98%E5%8C%96%E4%B8%AD%E6%96%87%E6%8A%93%E5%8F%96)
9. [故障排查](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E6%95%85%E9%9A%9C%E6%8E%92%E6%9F%A5)
10. [与 OCRmyPDF 结合](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E4%B8%8E-ocrmypdf-%E7%BB%93%E5%90%88)

---

## 概述

**Maxun** 是一个无代码网页数据提取平台，支持通过 Web 界面配置抓取机器人，提取网页数据（如产品列表、文本、截图），并输出为 API、CSV 或 Google Sheets。Docker 部署使用 `docker-compose.yml` 启动以下服务：

- **前端**：React 应用（端口 5173）。
- **后端**：Node.js API（端口 8080）。
- **数据库**：PostgreSQL（元数据存储）。
- **队列**：Redis（任务调度）。
- **存储**：MinIO（文件存储）。

Docker 部署的优势：

- 自动管理依赖，无需单独安装 PostgreSQL、Redis 或 MinIO。
- 跨平台一致性，减少 Windows 环境配置问题。
- 支持快速扩展（如添加代理）。

---

## 系统要求

- **操作系统**：Windows 10/11（64 位，推荐启用 WSL2）。
- **Docker**：Docker Desktop 4.10 或更高，支持 WSL2 后端。
- **硬件**：
    - 内存：最低 4 GB，推荐 8 GB+（抓取大型网站需要更多）。
    - 磁盘空间：约 2 GB（Docker 镜像和数据）。
- **网络**：稳定互联网连接（下载镜像和抓取网页）。
- **工具**：
    - Git（克隆 Maxun 仓库）。
    - 文本编辑器（如 VS Code，编辑 `.env` 文件）。
- **可选**：代理服务（绕过反爬机制）。

**注意**：Windows 需启用 WSL2 以确保 Docker 性能和 Redis 兼容性。

---

## 安装 Docker

### 步骤 1：安装 Docker Desktop

1. 下载 [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)。
2. 运行安装程序，确保勾选“启用 WSL2”。
3. 安装完成后，重启系统。

### 步骤 2：配置 WSL2

1. 打开 PowerShell（以管理员身份）：
    
    ```powershell
    wsl --install
    ```
    
    - 安装 Ubuntu（默认发行版）。
2. 验证 WSL2：
    
    ```powershell
    wsl --version
    ```
    
3. 在 Docker Desktop 设置中：
    - 打开 Docker Desktop → Settings → Resources → WSL Integration。
    - 启用 WSL2 并选择 Ubuntu。

### 步骤 3：验证 Docker

1. 打开 PowerShell 或 Anaconda Prompt：
    
    ```bash
    docker --version
    docker-compose --version
    ```
    
    - 应输出 Docker 版本（如 `20.10.17`）和 Docker Compose 版本（如 `2.10.2`）。
2. 测试 Docker：
    
    ```bash
    docker run hello-world
    ```
    
    - 应输出欢迎消息，表示 Docker 正常运行。

---

## 准备 Maxun 项目

### 步骤 1：克隆 Maxun 仓库

1. 在 Anaconda Prompt 中：
    
    ```bash
    cd C:\Users\YourUsername\Projects
    git clone https://github.com/getmaxun/maxun.git
    cd maxun
    ```
    
    - 确保 Git 已安装（若未安装，运行 `winget install -e --id Git.Git`）。

### 步骤 2：检查项目结构

- 确认以下关键文件存在：
    - `docker-compose.yml`：定义 Docker 服务。
    - `ENVEXAMPLE`：环境变量模板。
    - `package.json`：Node.js 依赖。
- 项目根目录包含：
    - `maxun-core`：后端代码。
    - `frontend`：前端代码。

---

## 配置环境变量

### 步骤 1：创建 `.env` 文件

1. 复制 `ENVEXAMPLE` 为 `.env`：
    
    ```bash
    copy ENVEXAMPLE .env
    ```
    
2. 编辑 `.env` 文件（用 VS Code 或记事本），以下是必要配置：
    
    ```env
    NODE_ENV=development
    JWT_SECRET=a9Z$kLq7^f03GzNw!bP9dH4xV6sT2yXl3O8vR@uYq3
    DB_NAME=maxun
    DB_USER=postgres
    DB_PASSWORD=postgres
    DB_HOST=maxun-postgres
    DB_PORT=5432
    ENCRYPTION_KEY=f4d5e6a7b8c9d0e1f23456789abcdef01234567890abcdef123456789abcdef0
    MINIO_ENDPOINT=maxun-minio
    MINIO_PORT=9000
    MINIO_CONSOLE_PORT=9001
    MINIO_ACCESS_KEY=minioadmin
    MINIO_SECRET_KEY=minioadmin
    REDIS_HOST=maxun-redis
    REDIS_PORT=6379
    PUBLIC_URL=http://localhost:5173
    VITE_BACKEND_URL=http://localhost:8080
    VITE_PUBLIC_URL=http://localhost:5173
    MAXUN_TELEMETRY=true
    ```
    
3. **关键修改**：
    - `DB_HOST=maxun-postgres`：匹配 `docker-compose.yml` 中的 PostgreSQL 服务名。
    - `MINIO_ENDPOINT=maxun-minio`：匹配 MinIO 服务名。
    - `REDIS_HOST=maxun-redis`：匹配 Redis 服务名。
    - `MINIO_ACCESS_KEY` 和 `MINIO_SECRET_KEY`：可自定义，但需与 MinIO 配置一致。
    - 如果使用代理，添加：
        
        ```env
        PROXY_URL=http://your-proxy:port
        ```
        

### 步骤 2：检查 `docker-compose.yml`

- 打开 `docker-compose.yml`，确认以下服务：
    - `maxun-frontend`：前端（端口 5173）。
    - `maxun-backend`：后端（端口 8080）。
    - `maxun-postgres`：PostgreSQL 数据库。
    - `maxun-redis`：Redis 队列。
    - `maxun-minio`：MinIO 存储。
- 默认配置通常无需修改，除非需要自定义端口或卷。

---

## 部署 Maxun

### 步骤 1：启动 Docker 服务

1. 确保 Docker Desktop 正在运行。
2. 在 Maxun 项目目录运行：
    
    ```bash
    docker-compose up -d
    ```
    
    - `-d`：后台运行。
    - Docker 将拉取镜像（Node.js、PostgreSQL、Redis、MinIO）并启动容器。

### 步骤 2：验证服务

1. 检查容器状态：
    
    ```bash
    docker ps
    ```
    
    - 应看到 5 个容器（`maxun-frontend`、`maxun-backend` 等）处于 `Up` 状态。
2. 访问 Web 界面：
    - 浏览器打开 `http://localhost:5173`。
    - 注册用户并登录，应看到 Maxun 仪表板。
3. 验证 MinIO：
    - 打开 `http://localhost:9001`，使用 `.env` 中的 `MINIO_ACCESS_KEY` 和 `MINIO_SECRET_KEY` 登录。
4. 验证 PostgreSQL：
    
    ```bash
    docker exec -it maxun-postgres psql -U postgres -d maxun
    ```
    
    - 应进入 PostgreSQL 命令行。
5. 验证 Redis：
    
    ```bash
    docker exec -it maxun-redis redis-cli ping
    ```
    
    - 应返回 `PONG`。

### 步骤 3：停止和清理（可选）

- 停止服务：
    
    ```bash
    docker-compose down
    ```
    
- 删除卷（清理数据）：
    
    ```bash
    docker-compose down -v
    ```
    

---

## 基本用法

1. **创建抓取机器人**：
    
    - 登录 `http://localhost:5173`。
    - 点击“New Robot”，输入目标网页 URL（例如，中文新闻网站）。
    - 配置抓取动作：
        - **Capture List**：提取列表数据（如产品名称、价格）。
        - **Capture Text**：提取文章或段落。
        - **Capture Screenshot**：截取页面。
    - 设置分页或滚动（如点击“下一页”）。
    - 保存并运行。
2. **导出数据**：
    
    - 数据存储在 PostgreSQL 和 MinIO。
    - 导出 CSV：
        - 在界面选择机器人，点击“Export to CSV”。
    - 使用 API：
        - 访问 `http://localhost:8080/api-docs` 查看 API 文档。
    - 集成 Google Sheets（需配置 `.env` 中的 Google OAuth）。
3. **定时任务**：
    
    - 在界面设置定时抓取（如每天运行）。
    - 检查 Redis 队列状态：
        
        ```bash
        docker exec -it maxun-redis redis-cli
        ```
        
4. **中文网页支持**：
    
    - 确保目标网页使用 UTF-8 编码。
    - 测试抓取中文网站（如 `https://www.sina.com.cn`），检查输出 CSV 中的中文是否正确。

---

## 优化中文抓取

中文网页抓取可能受编码、反爬机制或 Playwright 配置影响。以下是优化建议：

1. **确保正确编码**：
    
    - 在 `docker-compose.yml` 中，确认后端服务环境变量：
        
        ```yaml
        environment:
          - NODE_ENV=development
          - LANG=C.UTF-8
        ```
        
    - 如果中文乱码，检查 Playwright 的响应：
        
        ```bash
        docker logs maxun-backend
        ```
        
2. **绕过反爬机制**：
    
    - 配置代理：
        
        ```env
        PROXY_URL=http://your-proxy:port
        ```
        
        - 推荐代理：Bright Data、Smartproxy。
    - 启用反爬插件：
        - Maxun 使用 `puppeteer-extra-plugin-stealth` 和 `adblocker-playwright`。
        - 如果遇到 Cloudflare 验证码，考虑 Maxun Cloud（付费版）。
3. **调试 Playwright**：
    
    - 启用调试模式：
        
        ```bash
        docker-compose.yml
        environment:
          - PWDEBUG=1
        ```
        
        - 重启服务：
            
            ```bash
            docker-compose up -d
            ```
            
    - 检查浏览器行为，确保中文内容正确加载。
4. **提高抓取准确性**：
    
    - 增加超时：
        
        ```env
        PLAYWRIGHT_TIMEOUT=60000
        ```
        
    - 使用 headless 模式：
        
        ```env
        PLAYWRIGHT_HEADLESS=true
        ```
        

---

## 故障排查

1. **容器启动失败**：
    
    - **问题**：`docker ps` 显示容器退出。
    - **解决**：
        - 查看日志：
            
            ```bash
            docker logs maxun-backend
            ```
            
        - 确认 `.env` 配置正确（尤其是 `DB_HOST`、`MINIO_ENDPOINT`）。
        - 确保端口未被占用：
            
            ```bash
            netstat -an | findstr "5173 8080 5432 6379 9000 9001"
            ```
            
2. **数据库连接错误**：
    
    - **问题**：后端日志显示 PostgreSQL 连接失败。
    - **解决**：
        - 确认 `docker-compose.yml` 中的 `maxun-postgres` 服务运行。
        - 检查 `.env` 中的 `DB_USER` 和 `DB_PASSWORD`。
3. **中文乱码**：
    
    - **问题**：抓取的 CSV 中中文显示为乱码。
    - **解决**：
        - 确保目标网页使用 UTF-8。
        - 在前端代码中添加编码：
            
            ```javascript
            // frontend/src/App.tsx
            document.characterSet = 'UTF-8';
            ```
            
4. **Playwright 失败**：
    
    - **问题**：抓取时浏览器崩溃。
    - **解决**：
        - 增加内存分配：
            
            ```yaml
            maxun-backend:
              mem_limit: 2g
            ```
            
        - 重新安装 Playwright：
            
            ```bash
            docker exec -it maxun-backend npx playwright install --with-deps chromium
            ```
            
5. **Redis 版本问题**：
    
    - **问题**：需要 Redis 6.2.0+。
    - **解决**：
        - 确认 `docker-compose.yml` 使用最新 Redis 镜像：
            
            ```yaml
            image: redis:6.2
            ```
            

---

## 与 OCRmyPDF 结合

如果你需要抓取 PDF 内容并进行 OCR，可以结合 OCRmyPDF：

1. **抓取 PDF**：
    - 配置 Maxun 机器人抓取包含 PDF 的网页。
    - 下载 PDF 到本地（如 `C:\Users\YourUsername\Projects\OCRmyPDF_Document`）。
2. **OCR 处理**：
    
    ```bash
    conda activate ocrmypdf
    cd C:\Users\YourUsername\Projects\OCRmyPDF_Document
    ocrmypdf --force-ocr -l chi_sim downloaded.pdf output.pdf
    ```
    
3. **提取文本**：
    - 使用 `pdf2docx` 转换为 Word：
        
        ```bash
        pip install pdf2docx
        pdf2docx output.pdf output.docx
        ```
        

---

## 总结

- **Docker 部署**：使用 `docker-compose.yml` 启动 Maxun，集成前端、后端和依赖服务。
- **中文抓取**：确保 UTF-8 编码，配置代理绕过反爬。
- **优势**：Docker 简化 Windows 环境配置，适合快速部署。
- **故障排查**：检查日志、端口和 `.env` 配置。
- **与 OCRmyPDF**：可结合处理 PDF 数据。

## 进一步帮助

- 若部署失败，提供 `docker logs` 输出或具体错误。
- 若中文抓取效果不佳，提供目标网页 URL 和输出示例。
- 若有其他 GitHub 项目需安装，提供链接以获取指导。
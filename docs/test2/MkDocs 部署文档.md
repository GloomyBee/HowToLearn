# 部署文档

本文档指导如何在本地环境设置 MkDocs 项目，并通过 GitHub Pages 部署为静态网站。项目位于 `C:\Users\game\Projects\HowToLearn`，远程仓库为 `https://github.com/GloomyBee/HowToLearn`。

## 前提条件

- **操作系统**：Windows
- **已安装**：
    - Anaconda 或 Miniconda（包含 Conda）
    - Python 3.8（通过 Conda 安装）
    - Git
    - VS Code（推荐）
- **GitHub 仓库**：`https://github.com/GloomyBee/HowToLearn` 已创建

## 1. 创建 Conda 虚拟环境

Conda 虚拟环境隔离项目依赖，避免污染全局环境。

1. **切换到项目目录**：
    
    ```
    cd C:\Users\game\Projects\HowToLearn
    ```
    
2. **创建 Conda 环境**：
    
    ```
    conda create -n HowToLearn python=3.8
    ```
    
    - 环境位置：`C:\Users\game\.conda\envs\HowToLearn`。
    - 按提示确认（输入 `y`）。
3. **激活 Conda 环境**：
    
    ```
    conda activate HowToLearn
    ```
    
    - 终端显示 `(HowToLearn)`，如：
        
        ```
        (HowToLearn) C:\Users\game\Projects\HowToLearn>
        ```
        
4. **验证 Python 版本**：
    
    ```
    python --version
    ```
    
    - 应显示 `Python 3.8.x`。

## 2. 安装 MkDocs 和 Material for MkDocs

1. **安装依赖**：
    
    ```
    pip install mkdocs mkdocs-material
    ```
    
2. **验证安装**：
    
    ```
    mkdocs --version
    ```
    
    - 应显示版本号，如 `mkdocs, version 1.5.3`。
3. **记录依赖（可选）**：
    
    ```
    pip freeze > requirements.txt
    ```
    
    - 用于复现环境。

## 3. 初始化 MkDocs 项目

1. **创建项目结构**：
    
    ```
    mkdocs new .
    ```
    
    - 生成 `mkdocs.yml` 和 `docs/` 文件夹。
2. **配置 mkdocs.yml**：
    
    - 编辑 `mkdocs.yml`，使用 Material for MkDocs 主题：
        
        ```yaml
        site_name: HowToLearn
        theme:
          name: material
          palette:
            primary: indigo
            scheme: default
          features:
            - content.code.copy
            - navigation.tabs
            - search.suggest
        nav:
          - 首页: index.md
          - Python:
              - 基础语法: Python/基础语法.md
              - 数据结构: Python/数据结构.md
          - JavaScript:
              - 事件循环: JavaScript/事件循环.md
        plugins:
          - search
        markdown_extensions:
          - pymdownx.highlight:
              anchor_linenums: true
          - pymdownx.superfences
          - attr_list
          - pymdownx.emoji
        ```
        
3. **创建 Markdown 文件**：
    
    - 示例：`docs/index.md`：
        
        ```markdown
        # 欢迎体验 HowToLearn
        这是一个编程学习笔记集合。
        ```
        
    - 示例：`docs/Python/基础语法.md`：
        
        ```markdown
        # Python 基础语法
        ## 学习目标
        - 掌握变量、循环和条件语句。
        ## 步骤
        1. **定义变量**:
           ```python
           x = 10
           print(x)
        ```
        
4. **本地预览**：
    
    ```
    mkdocs serve
    ```
    
    - 访问 `http://localhost:8000`。
    - 按 `Ctrl + C` 退出。

## 4. 推送项目到 GitHub

1. **初始化 Git**：
    
    ```
    git init
    ```
    
2. **创建 .gitignore**：
    
    ```
    # Conda environment
    .conda/
    # MkDocs build
    site/
    # Python cache
    __pycache__/
    *.pyc
    ```
    
3. **关联远程仓库**：
    
    ```
    git remote add origin https://github.com/GloomyBee/HowToLearn.git
    git branch -M main
    ```
    
4. **提交并推送**：
    
    ```
    git add .
    git commit -m "Initial commit with MkDocs project"
    git push -u origin main
    ```
    
    - 如果提示登录，使用 GitHub 用户名和 Personal Access Token。
5. **处理冲突（如果有）**：
    
    - 如果远程有 `README.md` 等文件：
        
        ```
        git pull origin main --rebase
        ```
        
    - 解决冲突后：
        
        ```
        git add .
        git rebase --continue
        git push origin main
        ```
        

## 5. 部署到 GitHub Pages

1. **构建并部署**：
    
    ```
    mkdocs gh-deploy --force
    ```
    
    - 推送静态文件到 `gh-pages` 分支。
2. **配置 GitHub Pages**：
    
    - 访问 `https://github.com/GloomyBee/HowToLearn` > “Settings” > “Pages”。
    - 设置：
        - Branch: `gh-pages`
        - Folder: `/ (root)`
    - 保存后，网站 URL 为：
        
        ```
        https://GloomyBee.github.io/HowToLearn
        ```
        
3. **自动化部署**：
    
    - 创建 `.github/workflows/deploy.yml`：
        
        ```yaml
        name: Deploy to GitHub Pages
        on:
          push:
            branches:
              - main
        jobs:
          deploy:
            runs-on: ubuntu-latest
            steps:
              - uses: actions/checkout@v3
              - uses: actions/setup-python@v4
                with:
                  python-version: '3.8'
              - run: pip install mkdocs mkdocs-material
              - run: mkdocs gh-deploy --force
        ```
        
    - 提交：
        
        ```
        git add .github/workflows/deploy.yml
        git commit -m "Add GitHub Actions for auto-deployment"
        git push origin main
        ```
        

## 6. 验证

- **gh-pages 分支**：包含 `index.html`、`css/` 等。
- **网站**：访问 `https://GloomyBee.github.io/HowToLearn`，确认内容正常。

## 调试

- **gh-pages 为空**：重新运行 `mkdocs gh-deploy --force`。
- **网站 404**：确认 GitHub Pages 设置为 `gh-pages` 和 `/ (root)`。
- **Actions 失败**：检查 Actions 日志，调整 `python-version`。
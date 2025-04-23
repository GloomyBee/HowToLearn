

# HowToLearn 文档简介

本项目旨在收集、整理并分享各种软件的使用与配置方法，帮助读者快速上手常用开发及生产力工具，提升工作效率与学习效果。文档涵盖安装、环境配置、常见问题及最佳实践，结构清晰、示例丰富。

MkDocs完整文档请访问 [mkdocs.org](https://www.mkdocs.org)。

---

## 1.文档主要内容（软件安装与环境配置）
### 🧠 编程与开发工具

- **Git**：版本控制工具，用于管理代码变更，协作开发必备。
- **Julia**：高性能的科学计算语言，适合数值模拟与数据处理。
- **VS Code**（Visual Studio Code）：轻量级但功能强大的代码编辑器，支持多种语言与扩展。
- **Markdown**：轻量级标记语言，常用于编写说明文档与技术笔记。

### 📊 可视化与后处理工具

- **Gmsh**：网格划分工具，常用于有限元前处理。
- **ParaView**：强大的开源可视化工具，适合处理模拟结果、后处理大规模数据。
- **OriginLab**（Origin）：专业的数据分析与科学绘图软件，用于生成出版级图表。

### ✍️ 笔记与写作工具

- **Obsidian**：基于 Markdown 的知识管理与笔记工具，支持双链、图谱视图等。
- **Inkscape**：矢量图绘图工具，适合制作学术图、流程图、SVG 编辑等。

### 📚 文献与资料管理

- **Zotero**：文献管理工具，可自动抓取文献元数据、管理引用格式，与 Word、Obsidian 等集成良好。

---

## 2.如何贡献

欢迎所有读者提出改进与补充。贡献流程建议如下：

1. **Fork 本仓库**  
   点击右上角 “Fork” 按钮，将项目复制到个人账户下。
2. **Clone 到本地**  
   ```bash
   git clone https://github.com/GloomyBee/HowToLearn.git
   ```

3. **新建分支**
    
    ```bash
    git checkout -b feature/your-topic
    ```
    
4. **编辑文档**  
    在 `docs/` 目录下新增或修改 `.md` 文件，遵循文档结构与风格规范（见下节）。
    
5. **提交并推送**
    
    ```bash
    git add .
    git commit -m "feat: 添加 XXX 软件 使用指南"
    git push origin feature/your-topic
    ```
    
6. **发起 Pull Request**  
    在 GitHub 页面点击 “Compare & pull request”，填写说明后发起合并请求。
    

---
## 3.文档结构、风格与标签

为保证整站风格统一，建议所有 Markdown 文档遵循以下规范：

1. **YAML Front Matter**
    
    ```yaml
    ---
    title: "示例文档标题"
    description: "一句话概述文档内容"
    tags: ["安装","配置","IDE"]
    ---
    ```
    
    - `title`：页面标题；
        
    - `description`：摘要，用于搜索及 SEO；
        
    - `tags`：关键词列表，帮助分类与检索。
        
2. **标题层级**
    
    - 一级标题（`#`）仅用于页面主标题；
        
    - 二级标题（`##`）区分功能模块；
        
    - 三级标题及更深层级用于细分步骤与注意事项。
        
3. **代码块与示例**
    
    - 使用三反引号标注语言（如 `bash`、`yaml`）；
        
    - 保持示例简洁，必要时添加注释。
        
4. **统一样式**
    
    - 保持行宽不超过 100 字符；
        
    - 中英文之间添加空格；
        
    - 引用、列表、表格等语法使用一致的缩进与风格。
        

---

## 4.本地如何测试 MkDocs 项目

1. **安装 MkDocs**
    
    ```bash
    pip install mkdocs
    ```
    
2. **本地预览**  
    在项目根目录运行：
    
    ```bash
    mkdocs serve
    ```
    
    访问 `http://127.0.0.1:8000/` 查看实时预览。
    
3. **构建静态文件**
    
    ```bash
    mkdocs build
    ```
    
    生成的 `site/` 目录即为可部署的静态站点。
    
4. **部署到 GitHub Pages**
    
    ```bash
    mkdocs gh-deploy
    ```
    
    会自动构建并推送到 `gh-pages` 分支，实现一键上线。
    


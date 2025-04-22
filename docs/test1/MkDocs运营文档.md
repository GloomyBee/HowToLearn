# 运营文档

本文档指导如何运营 `HowToLearn` MkDocs 静态网站，包括排版规范、新增文档、推送流程，以及他人如何贡献内容。网站部署在 GitHub Pages：`https://GloomyBee.github.io/HowToLearn`。

## 1. 项目结构

- **docs/**：存放 Markdown 文件。
    - `index.md`：首页。
    - `Python/`：Python 相关笔记（如 `基础语法.md`）。
    - `JavaScript/`：JavaScript 相关笔记。
    - `assets/`：存放图片等资源。
- **site/**：MkDocs 构建生成的静态文件（无需手动编辑）。
- **mkdocs.yml**：配置文件，定义导航和主题。

## 2. MkDocs 静态网站排版规范

为保持一致性和可读性，所有文档需遵循以下排版规范（参考 HowToCook 的风格）：

### 2.1 标题和结构

- **主标题**：使用一级标题（`#`），表示文档主题。
    - 示例：`# Python 基础语法`
- **章节标题**：使用二级标题（`##`），表示主要部分。
    - 必须包含以下章节：
        - `## 学习目标`：列出学习目标。
        - `## 材料`：列出所需工具或环境（可选）。
        - `## 步骤`：分步说明。
        - `## 小贴士`：提供额外建议（可选）。
- **子章节**：使用三级标题（`###`）或更低级别，补充细节。

### 2.2 内容格式

- **列表**：
    
    - 使用 `-` 表示无序列表，`1.` 表示有序列表。
        
    - 示例：
        
        ```markdown
        ## 学习目标
        - 掌握变量定义。
        - 理解循环语法。
        ## 步骤
        1. 定义变量：
           ```python
           x = 10
        ```
        
        2. 使用循环：
            
            ```python
            for i in range(5):
                print(i)
            ```
            
        
- **代码块**：
    
    - 使用 `指定语言（如`python）。
        
    - 示例：
        
        ````markdown
        ```python
        print("Hello, World!")
        ````
        
- **图片**：
    
    - 存放在 `docs/assets/`，使用相对路径引用。
        
    - 示例：
        
        ```markdown
        ![变量定义示例](assets/variable_example.png)
        ```
        
    - 支持属性设置（如调整大小）：
        
        ```markdown
        ![变量定义示例](assets/variable_example.png){ width=50% }
        ```
        
- **链接**：
    
    - 内部链接：指向其他 Markdown 文件。
        
        ```markdown
        参考 [基础语法](./Python/基础语法.md)。
        ```
        
    - 外部链接：指向官方文档等。
        
        ```markdown
        参考 [Python 官方文档](https://docs.python.org/)。
        ```
        

### 2.3 语言和风格

- 使用简洁、清晰的语言。
    
- 避免长段落，每段控制在 3-5 行。
    
- 代码块后添加简短说明。
    
- 使用 emoji 增强可读性（适度）：
    
    ```markdown
    ## 小贴士
    - 使用 `type()` 检查变量类型 🔍。
    ```
    

### 2.4 示例文档

```markdown
# Python 基础语法
## 学习目标
- 掌握变量、循环和条件语句。
## 材料
- Python 3.8+
## 步骤
1. **定义变量**：
   ```python
   x = 10
   print(x)
```

- 输出：`10`。

2. **使用循环**：
    
    ```python
    for i in range(5):
        print(i)
    ```
    
    - 输出 0 到 4。


## 3. 本地新增文档并推送
以下是在 VS Code 中新增文档并推送到 GitHub 的流程。

### 3.1 修改文档
1. **打开 VS Code**：
   - 打开 `C:\Users\game\Projects\HowToLearn` 文件夹。

2. **创建新文档**：
   - 在 `docs/` 下创建新文件，例如 `docs/Python/函数.md`：
     - 右键 `docs/Python/` > “New File” > 命名为 `函数.md`。
   - 编辑内容（遵循排版规范）：
     ```markdown
     # Python 函数
     ## 学习目标
     - 理解函数定义和调用。
     ## 材料
     - Python 3.8+
     ## 步骤
     1. **定义函数**:
        ```python
        def greet(name):
            return f"Hello, {name}!"
        print(greet("Alice"))
        ```
        - 输出：`Hello, Alice!`。
     ## 小贴士
     - 使用默认参数简化调用 🎯。
     ```

3. **更新导航**：
   - 编辑 `mkdocs.yml`，在 `nav` 中添加新文档：
     ```yaml
     nav:
       - 首页: index.md
       - Python:
           - 基础语法: Python/基础语法.md
           - 数据结构: Python/数据结构.md
           - 函数: Python/函数.md
       - JavaScript:
           - 事件循环: JavaScript/事件循环.md
     ```

### 3.2 本地预览
1. **打开 VS Code 终端**：
   - 点击 “Terminal” > “New Terminal”。
   - 确认目录：
     ```
     game@LAPTOP-8TPLLCRJ MINGW64 ~/Projects/HowToLearn (main)
     ```

2. **激活 Conda 环境**：
   - 运行：
     ```
     conda activate HowToLearn
     ```
     - 终端显示 `(HowToLearn)`。

3. **预览网站**：
   - 运行：
     ```
     mkdocs serve
     ```
     - 访问 `http://localhost:8000`，确认新文档显示正常。
     - 按 `Ctrl + C` 退出。

### 3.3 提交并推送
1. **暂存更改**：
   - 在 VS Code 的 “Source Control” 面板（或终端）：
     ```
     git add .
     ```

2. **提交**：
   - 使用 VS Code 图形界面：
     - 在 “Source Control” 面板输入提交消息（如 “Add Python 函数 notes”）。
     - 点击 “Commit” 按钮（勾图标）。
   - 或使用终端：
     ```
     git commit -m "Add Python 函数 notes"
     ```

3. **推送到 GitHub**：
   - 使用 VS Code：
     - 点击 “Sync Changes” 或 “...” > “Push”。
   - 或使用终端：
     ```
     git push origin main
     ```

4. **自动部署**：
   - GitHub Actions（`deploy.yml`）会自动将更改部署到 `gh-pages` 分支。
   - 访问 `https://GloomyBee.github.io/HowToLearn`，确认更新。

## 4. 他人如何贡献
1. **Fork 仓库**：
   - 访问 `https://github.com/GloomyBee/HowToLearn`。
   - 点击 “Fork” 创建副本。

2. **克隆到本地**：
```

git clone [https://github.com/你的用户名/HowToLearn.git](https://github.com/%E4%BD%A0%E7%9A%84%E7%94%A8%E6%88%B7%E5%90%8D/HowToLearn.git) cd HowToLearn

```

3. **设置 Conda 环境**：
```

conda create -n HowToLearn python=3.8 conda activate HowToLearn pip install -r requirements.txt

```

4. **新增或修改文档**：
- 在 `docs/` 下添加 Markdown 文件，遵循排版规范。
- 更新 `mkdocs.yml` 的 `nav`。

5. **提交更改**：
```

git add . git commit -m "Add new notes" git push origin main

```

6. **创建 Pull Request**：
- 访问你的 Fork 仓库。
- 点击 “Pull Requests” > “New Pull Request”。
- 目标分支：`GloomyBee/HowToLearn` 的 `main`。
- 提交 PR，等待审核。

## 5. 审核和合并
- **审核 PR**：
- 检查文档是否符合排版规范。
- 运行 `mkdocs serve` 本地预览。
- **合并**：
- 在 GitHub 点击 “Merge Pull Request”。
- GitHub Actions 自动部署更新。

## 6. 常见问题
- **site/ 文件夹是什么？**：
- 由 `mkdocs build` 生成，包含静态网站文件，不需手动编辑。
- **文档未显示**：
- 检查 `mkdocs.yml` 的 `nav` 路径。
- 确保文件在 `docs/` 下。
- **如何添加图片**：
- 放入 `docs/assets/`，用 `![描述](assets/文件名)` 引用。
```
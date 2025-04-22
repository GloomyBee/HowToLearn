# 项目推送文档

本文档指导如何向他人的 GitHub 项目提交更改，例如为 `HowToCook`（`https://github.com/Anduin2017/HowToCook`）贡献内容。流程包括 Fork 仓库、修改内容、提交 Pull Request（PR）。

## 前提条件

- **工具**：Git、VS Code
- **GitHub 账号**：已注册（如 `GloomyBee`）
- **目标项目**：`https://github.com/Anduin2017/HowToCook`

## 1. Fork 目标项目

1. **访问目标仓库**：
    
    - 打开 `https://github.com/Anduin2017/HowToCook`。
2. **Fork 仓库**：
    
    - 点击右上角“Fork”按钮。
    - 选择你的账号（`GloomyBee`）。
    - 完成后，你的账号下会出现 `https://github.com/GloomyBee/HowToCook`。

## 2. 克隆 Fork 的仓库到本地

1. **切换目录**：
    
    ```
    cd C:\Users\game\Projects
    ```
    
2. **克隆仓库**：
    
    ```
    git clone https://github.com/GloomyBee/HowToCook.git
    cd HowToCook
    ```
    
3. **验证**：
    
    ```
    git remote -v
    ```
    
    - 应显示：
        
        ```
        origin  https://github.com/GloomyBee/HowToCook.git (fetch)
        origin  https://github.com/GloomyBee/HowToCook.git (push)
        ```
        

## 3. 设置上游仓库（可选）

为保持与原仓库同步，添加上游远程仓库：

```
git remote add upstream https://github.com/Anduin2017/HowToCook.git
```

- 验证：
    
    ```
    git remote -v
    ```
    
    - 应显示 `upstream`。

## 4. 创建新分支

1. **创建分支**：
    
    ```
    git checkout -b feature/add-new-recipe
    ```
    
    - `feature/add-new-recipe` 是新分支名，描述你的更改。
2. **确认分支**：
    
    ```
    git branch
    ```
    
    - 应显示 `* feature/add-new-recipe`。

## 5. 修改内容

1. **新增文档**：
    
    - 例如，添加新菜谱 `docs/dishes/家常菜/鱼香肉丝.md`：
        
        ```
        echo. > docs\dishes\家常菜\鱼香肉丝.md
        ```
        
    - 编辑内容（参考目标项目的格式）：
        
        ```markdown
        # 鱼香肉丝
        ## 材料
        - 猪里脊肉 200g
        - 木耳 50g
        ## 步骤
        1. 猪肉切丝，腌制 10 分钟。
        2. 热锅凉油，炒肉丝至变色。
        ```
        
    - 遵循目标项目的排版规范。
2. **更新导航（如果需要）**：
    
    - 编辑 `mkdocs.yml`，添加新文档到 `nav`。
3. **本地预览（如果目标项目使用 MkDocs）**：
    
    - 安装依赖（参考目标项目的部署说明）：
        
        ```
        conda create -n HowToCook python=3.8
        conda activate HowToCook
        pip install mkdocs mkdocs-material
        ```
        
    - 预览：
        
        ```
        mkdocs serve
        ```
        
    - 访问 `http://localhost:8000`。

## 6. 提交更改

1. **暂存更改**：
    
    ```
    git add .
    ```
    
2. **提交**：
    
    ```
    git commit -m "Add 鱼香肉丝 recipe"
    ```
    
3. **推送到你的 Fork 仓库**：
    
    ```
    git push origin feature/add-new-recipe
    ```
    

## 7. 创建 Pull Request

1. **访问你的 Fork 仓库**：
    
    - 打开 `https://github.com/GloomyBee/HowToCook`。
2. **创建 PR**：
    
    - 点击“Pull Requests” > “New Pull Request”。
    - 设置：
        - Base repository: `Anduin2017/HowToCook`
        - Base branch: `main`（或目标项目的主分支）
        - Head repository: `GloomyBee/HowToCook`
        - Head branch: `feature/add-new-recipe`
    - 点击“Create Pull Request”。
3. **填写 PR 详情**：
    
    - 标题：`Add 鱼香肉丝 recipe`
    - 描述：说明更改内容，遵循目标项目的贡献指南（如 `CONTRIBUTING.md`）。
    - 提交 PR。

## 8. 等待审核

- 目标项目维护者会审核你的 PR。
- 如果有反馈（如需要修改），在本地对应分支（`feature/add-new-recipe`）上修改：
    
    ```
    git add .
    git commit -m "Update based on feedback"
    git push origin feature/add-new-recipe
    ```
    
- 修改会自动更新 PR。

## 9. 同步上游更改（可选）

如果原仓库有更新，同步到你的 Fork：

```
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

## 调试

- **推送失败**：确认使用 Personal Access Token 或 SSH。
- **PR 冲突**：按照维护者建议解决冲突。
- **格式问题**：参考目标项目的 `CONTRIBUTING.md`。
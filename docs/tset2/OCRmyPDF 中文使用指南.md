# OCRmyPDF 中文使用指南

本指南为 **OCRmyPDF** 提供详细的使用说明，帮助用户将扫描的 PDF 文件转换为可搜索、可复制的 PDF 文件，特别针对 Windows 系统、Anaconda 环境以及中文扫描件的 OCR 处理。本文档适合需要处理中文文档并优化 OCR 效果的用户。

## 目录

1. [概述](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E6%A6%82%E8%BF%B0)
2. [系统要求](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E7%B3%BB%E7%BB%9F%E8%A6%81%E6%B1%82)
3. [安装](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%AE%89%E8%A3%85)
4. [基本用法](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
5. [优化中文 OCR](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E4%BC%98%E5%8C%96%E4%B8%AD%E6%96%87-ocr)
6. [使用 GUI（OCRthyPDF-Essentials）](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E4%BD%BF%E7%94%A8-guiocrthypdf-essentials)
7. [高级功能](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E9%AB%98%E7%BA%A7%E5%8A%9F%E8%83%BD)
8. [故障排查](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E6%95%85%E9%9A%9C%E6%8E%92%E6%9F%A5)
9. [后续处理：重新排版 PDF](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E5%90%8E%E7%BB%AD%E5%A4%84%E7%90%86%E9%87%8D%E6%96%B0%E6%8E%92%E7%89%88-pdf)

---

## 概述

**OCRmyPDF** 是一个开源命令行工具，通过 **Tesseract OCR** 引擎为扫描的 PDF 文件添加可搜索的文本层，同时保留原始 PDF 的外观。主要功能包括：

- 将扫描件或图像 PDF 转换为可搜索、可复制的 PDF。
- 支持多语言，包括简体中文（`chi_sim`）和繁体中文（`chi_tra`）。
- 依赖 **Ghostscript** 处理 PDF，**Tesseract** 进行 OCR。
- **不会**重新排版或重组 PDF（如生成 Word 格式文档）；如需重新排版，需使用其他工具。

本指南假设你在 **Windows** 系统上使用 **Anaconda** 环境（名为 `ocrmypdf`，Python 3.9）。

---

## 系统要求

- **操作系统**：Windows 10/11（64 位）。可选使用 WSL2 以提高兼容性。
- **Python**：3.9 或更高（Anaconda 已包含）。
- **Tesseract OCR**：4.0 或更高，需包含中文语言包（`chi_sim`、`chi_tra`）。
- **Ghostscript**：9.54 或更高。
- **磁盘空间**：约 500 MB 用于工具，另加输入/输出 PDF 的空间。
- **内存**：最低 4 GB，推荐 8 GB+（处理大文件时）。
- **可选工具**：ImageMagick（图像预处理）、ChromeDriver（GUI 使用）。

---

## 安装

### 步骤 1：安装 Anaconda

1. 从 [Anaconda 官网](https://www.anaconda.com/download) 下载并安装 Anaconda，或选择轻量版 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)（约 400 MB）。
2. 验证安装：
    
    ```bash
    conda --version
    ```
    

### 步骤 2：创建并激活 Anaconda 环境

1. 创建名为 `ocrmypdf` 的环境：
    
    ```bash
    conda create -n ocrmypdf python=3.9
    ```
    
2. 激活环境：
    
    ```bash
    conda activate ocrmypdf
    ```
    

### 步骤 3：安装系统依赖

OCRmyPDF 依赖 **Tesseract OCR** 和 **Ghostscript**，需手动安装（无法通过 Conda 或 pip 安装）。

#### Tesseract OCR

1. 通过 `winget` 安装：
    
    ```powershell
    winget install -e --id UB-Mannheim.TesseractOCR
    ```
    
    或从 [Tesseract GitHub](https://github.com/UB-Mannheim/tesseract/wiki) 下载 64 位安装程序，安装到 `C:\Program Files\Tesseract-OCR`。
2. 安装中文语言包：
    - 从 [tessdata](https://github.com/tesseract-ocr/tessdata) 下载 `chi_sim.traineddata`（简体中文）和 `chi_tra.traineddata`（繁体中文）。
    - 放置到 `C:\Program Files\Tesseract-OCR\tessdata`。
3. 验证：
    
    ```bash
    tesseract --list-langs
    ```
    
    应列出 `chi_sim` 和 `chi_tra`。

#### Ghostscript

1. 从 [Ghostscript 下载页面](https://www.ghostscript.com/download/gsdnld.html) 下载最新 64 位版本（例如 `gs10031w64.exe`）。
2. 安装到默认路径（通常为 `C:\Program Files\gs\gs10.03.1`）。
3. 验证：
    
    ```bash
    gs --version
    ```
    
    应输出类似 `10.03.1`。

#### 可选：pngquant

用于优化输出 PDF 大小：

```powershell
choco install pngquant
```

（需先安装 [Chocolatey](https://chocolatey.org/install)）。

### 步骤 4：安装 OCRmyPDF

在 `ocrmypdf` 环境中安装：

```bash
conda install -c conda-forge ocrmypdf
```

若 Conda 失败，尝试：

```bash
pip install ocrmypdf
```

### 步骤 5：验证安装

检查 OCRmyPDF：

```bash
ocrmypdf --version
```

应输出类似 `ocrmypdf 17.0.0`。

---

## 基本用法

### 准备输入文件

1. 将输入 PDF（例如 `ocrtest.pdf`）放入工作目录，如：
    
    ```
    C:\Users\YourUsername\Projects\OCRmyPDF_Document
    ```
    
2. 确保 PDF 是扫描件或图像 PDF（可用 Adobe Acrobat 检查是否可搜索）。

### 运行 OCRmyPDF

1. 激活环境并切换目录：
    
    ```bash
    conda activate ocrmypdf
    cd C:\Users\YourUsername\Projects\OCRmyPDF_Document
    ```
    
2. 执行 OCR：
    
    ```bash
    ocrmypdf ocrtest.pdf output.pdf
    ```
    
    - 生成 `output.pdf`，包含可搜索文本。
    - 如果 PDF 已包含文本，会报 `PriorOcrFoundError`（见[故障排查](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E6%95%85%E9%9A%9C%E6%8E%92%E6%9F%A5)）。

### 验证输出

- 打开 `output.pdf`（用 Adobe Acrobat、Edge 浏览器等）。
- 选中并复制文本，粘贴到记事本，检查是否正确。

---

## 优化中文 OCR

中文 OCR 效果受语言设置、扫描件质量和参数影响。以下是优化步骤：

### 步骤 1：指定中文语言

默认语言为英文（`eng`），需明确指定中文：

```bash
ocrmypdf --force-ocr -l chi_sim ocrtest.pdf output.pdf
```

- `-l chi_sim`: 简体中文。
- `-l chi_tra`: 繁体中文。
- 中英文混合：
    
    ```bash
    ocrmypdf --force-ocr -l chi_sim+eng ocrtest.pdf output.pdf
    ```
    

### 步骤 2：检查扫描件质量

- **分辨率**：推荐 300 DPI，低分辨率（<150 DPI）会导致识别错误。
- **清晰度**：确保无模糊、阴影或噪点。
- **对比度**：黑字白底最佳。
- **检查方法**：
    - 打开 `ocrtest.pdf`，放大查看文字是否清晰。
    - 若质量差，重新扫描（设置高分辨率）或预处理。

### 步骤 3：图像预处理

提高扫描件质量可显著改善 OCR 效果：

- **使用 OCRmyPDF 内置选项**：
    
    ```bash
    ocrmypdf --force-ocr --clean --deskew -l chi_sim ocrtest.pdf output.pdf
    ```
    
    - `--clean`: 移除噪声和背景。
    - `--deskew`: 校正页面倾斜。
- **使用 ImageMagick**：
    1. 安装：
        
        ```powershell
        winget install -e --id ImageMagick.ImageMagick
        ```
        
    2. 预处理 PDF：
        
        ```bash
        magick ocrtest.pdf -density 300 -quality 100 ocrtest_processed.pdf
        ```
        
    3. 运行 OCR：
        
        ```bash
        ocrmypdf --force-ocr -l chi_sim ocrtest_processed.pdf output.pdf
        ```
        

### 步骤 4：调整 Tesseract 参数

- **提高准确性**：
    
    ```bash
    ocrmypdf --force-ocr --tesseract-timeout 300 -l chi_sim ocrtest.pdf output.pdf
    ```
    
    - `--tesseract-timeout 300`: 增加每页处理时间（秒）。
- **优化复杂布局**：
    
    ```bash
    ocrmypdf --force-ocr --output-type pdfa -l chi_sim ocrtest.pdf output.pdf
    ```
    
    - `--output-type pdfa`: 生成 PDF/A 格式，优化文本嵌入。

### 步骤 5：验证和调试

- 复制 `output.pdf` 中的文本，检查是否准确。
- 若识别错误，提供错误示例（如“测试”识别为“测武”），以便进一步优化。

---

## 使用 GUI（OCRthyPDF-Essentials）

若命令行操作复杂，可使用 [OCRthyPDF-Essentials](https://github.com/digidigital/OCRthyPDF-Essentials) 提供图形界面。

### 安装

1. 克隆仓库：
    
    ```bash
    cd C:\Users\YourUsername\Projects
    git clone https://github.com/digidigital/OCRthyPDF-Essentials.git
    cd OCRthyPDF-Essentials
    ```
    
2. 安装依赖：
    
    ```bash
    conda install -c conda-forge --file requirements.txt
    ```
    
    若失败，尝试：
    
    ```bash
    pip install -r requirements.txt
    ```
    
3. 运行 GUI：
    
    ```bash
    python main.py
    ```
    

### 使用

- 选择输入 PDF（例如 `ocrtest.pdf`）。
- 设置语言（`chi_sim` 或 `chi_tra`）和选项（如 `--force-ocr`）。
- 指定输出路径，点击“运行”。
- 查看日志检查错误。

### 注意

- 确保 `selenium` 和 `webdriver-manager` 正常（参考前文修复依赖冲突）。
- 若 GUI 报错，提供错误信息以排查。

---

## 高级功能

- **批量处理**：  
    处理多个 PDF：
    
    ```powershell
    cd C:\Users\YourUsername\Projects\OCRmyPDF_Document
    Get-ChildItem *.pdf | ForEach-Object { ocrmypdf --force-ocr -l chi_sim $_.Name "ocr_$($_.Name)" }
    ```
    
- **优化文件大小**：
    
    ```bash
    ocrmypdf --force-ocr --optimize 1 -l chi_sim ocrtest.pdf output.pdf
    ```
    
    - `--optimize 1`: 最大压缩（需安装 `pngquant`）。
- **处理多页 PDF**：
    
    ```bash
    ocrmypdf --force-ocr --jobs 4 -l chi_sim ocrtest.pdf output.pdf
    ```
    
    - `--jobs 4`: 使用 4 个并行进程（根据 CPU 调整）。

---

## 故障排查

### 问题 1：`PriorOcrFoundError: page already has text!`

- **原因**：输入 PDF 已包含文本，OCRmyPDF 默认拒绝处理。
- **解决**：
    - 强制 OCR：
        
        ```bash
        ocrmypdf --force-ocr -l chi_sim ocrtest.pdf output.pdf
        ```
        
    - 跳过有文本页面：
        
        ```bash
        ocrmypdf --skip-text ocrtest.pdf output.pdf
        ```
        

### 问题 2：中文识别错误或乱码

- **原因**：未指定中文语言、语言包缺失或扫描件质量差。
- **解决**：
    - 确保使用 `-l chi_sim`。
    - 验证语言包：
        
        ```bash
        tesseract --list-langs
        ```
        
    - 预处理图像（见[优化中文 OCR](https://grok.com/chat/ba0306d0-5da0-4cd6-8876-a5212bc1373f#%E4%BC%98%E5%8C%96%E4%B8%AD%E6%96%87-ocr)）。

### 问题 3：`Tesseract not found`

- **原因**：Tesseract 未安装或不在 PATH 中。
- **解决**：
    - 验证：
        
        ```bash
        tesseract --version
        ```
        
    - 确保 PATH 包含 `C:\Program Files\Tesseract-OCR`。

### 问题 4：`Ghostscript not found`

- **原因**：Ghostscript 未安装或不在 PATH 中。
- **解决**：
    - 验证：
        
        ```bash
        gs --version
        ```
        
    - 确保 PATH 包含 `C:\Program Files\gs\gs10.03.1\bin`。

### 问题 5：依赖冲突

- **原因**：Anaconda 和 pip 混合使用导致包不兼容。
- **解决**：
    - 优先使用 Conda：
        
        ```bash
        conda install -c conda-forge certifi urllib3 attrs idna requests
        ```
        
    - 检查依赖：
        
        ```bash
        pip check
        ```
        

---

## 后续处理：重新排版 PDF

OCRmyPDF 仅生成可搜索 PDF，不重新排版（如转换为 Word 格式）。若需重新排版，推荐以下工具：

### 工具 1：pdf2docx

将 OCR 后的 PDF 转换为 Word：

```bash
pip install pdf2docx
pdf2docx output.pdf output.docx
```

- 打开 `output.docx`，编辑排版。

### 工具 2：ABBYY FineReader

- 商业软件，支持高质量 OCR 和格式转换（PDF 到 Word、Excel）。
- 适合复杂文档（如表格、复杂排版）。

### 工具 3：Adobe Acrobat Pro

- 提供 OCR 和导出功能，可转换为可编辑格式。
- 需付费订阅。

### 注意

- 重新排版效果取决于 OCR 准确性和原始 PDF 结构。
- 对于复杂文档，可能需手动调整。

---

## 总结

- **OCRmyPDF** 是一个强大的工具，用于将扫描件转换为可搜索、可复制的 PDF，特别适合中文文档。
- **安装**：使用 Anaconda 环境，结合 Tesseract 和 Ghostscript。
- **优化中文 OCR**：指定 `-l chi_sim`，确保扫描件质量，必要时预处理。
- **GUI 选项**：OCRthyPDF-Essentials 提供简单界面。
- **故障排查**：针对常见错误（如语言包缺失、依赖冲突）提供解决方案。
- **后续处理**：如需重新排版，使用 `pdf2docx` 或商业软件。

## 进一步帮助

- 若 OCR 效果仍不理想，提供错误文本示例和扫描件信息（如分辨率、页面数）。
- 若需安装 GUI 或其他工具，请告知。
- 若有其他 GitHub 项目需安装，提供链接以获取指导。
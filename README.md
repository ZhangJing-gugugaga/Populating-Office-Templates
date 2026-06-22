# 🛠️ Populating Office Templates Custom Skill

这是一个专为 AI 编码助手（如 Google Antigravity、Codex 或 Claude Code）打造的自定义技能（Skill），用于实现 **PPT/Word 模板的精准页面重构与基于物理 ID 的文本/数据批量注入**。

该技能通过 OpenXML 稳定的唯一路径（如 `/slide[N]/shape[@id=XXXX]`）进行文本定位替换，完美保留模板原本的字体、色值与排版结构，避免字面值替换导致的格式损坏。

---

## 📋 依赖要求 (Dependencies)

在运行本技能脚本前，请确保您的系统中已安装以下依赖项：

1. **Python 3.x**
   - 脚本完全使用 Python 标准库实现，**无需安装任何第三方库**（如 `python-pptx` 或 `openpyxl` ），开箱即用。
2. **OfficeCLI**
   - **下载与项目地址**：[iOfficeAI/OfficeCLI](https://github.com/iOfficeAI/OfficeCLI.git)
   - **安装命令**：
     - **Windows (PowerShell)**:
       ```powershell
       irm https://d.officecli.ai/install.ps1 | iex
       ```
     - **macOS / Linux**:
       ```bash
       curl -fsSL https://d.officecli.ai/install.sh | bash
       ```
     安装后请确保命令行中能成功运行 `officecli --version`。

---

## 📂 结构说明 (Directory Structure)

```
populating-office-templates/
  ├── SKILL.md              # 技能的核心操作说明与逻辑约束流程
  ├── README.md             # 本说明文件
  ├── Templates/            # 存放原始模板，方便反复使用（已存入 0008, 0010 模板）
  ├── Result/               # 存放最终生成的 PPT 结果文件（已存入生息守护路演PPT）
  ├── Tmp/                  # 运行脚本期间生成临时文件（如占位符映射、批量更新等临时文件）的缓存区
  ├── images/               # 存放展示图片
  ├── examples/
  │   └── ppt_bp_mapping_example.json  # 数据填充 JSON 样例
  └── scripts/
      ├── extract_placeholders.py      # 深度优先提取占位符路径和ID的脚本
      └── batch_injector.py            # 读取JSON配置并执行单事务批量注入的脚本
```

> 💡 **PPT 模板资源下载**：
> 更多配套的精美商务 PPT 模板资源，可通过夸克网盘链接进行下载：
> 🔗 [夸克网盘下载地址 (https://pan.quark.cn/s/5c2b7d7dc8fb)](https://pan.quark.cn/s/5c2b7d7dc8fb)

---

## 🖼️ 效果示例 (Visual Examples)

以下为使用本工作流处理前后的幻灯片页面对比：

| 修改前（模板原始状态） | 修改后（使用本技能注入中文业务数据后） |
| :---: | :---: |
| ![修改前](images/模板示例.png) | ![修改后](images/skills后的PPT.png) |

---

## 🚀 快速开始 (Usage for Humans)

本技能的工具脚本经过了人性化设计，您只需三步即可完成任意 PPT 模版的快速数据注入：

### 1. 提取模版占位符 (Extract)
运行提取脚本，从 `Templates/` 文件夹中的模板提取所有可编辑的文本框、表格单元格至 `Tmp/` 缓存目录：
```bash
# 示例：提取 0008 模板
python scripts/extract_placeholders.py Templates/0008_template.pptx Tmp/my_mapping.json
```

### 2. 直接在 JSON 中修改文本 (Edit In-Place)
用任何文本编辑器（如 VS Code、Sublime Text 或记事本）打开刚生成的 `Tmp/my_mapping.json`。
您会看到形如以下的结构，**直接在原位置修改 `"text"` 后面的内容**为您所需的真实业务中文文字，然后保存即可：
```json
{
  "slide_1": [
    {
      "path": "/slide[1]/shape[@id=100002]",
      "text": "直接在原位置修改此处的文字（支持换行符\\n）",
      "id": 100002,
      "type": "textbox",
      "name": "文本框 3"
    }
  ]
}
```

### 3. 一键批量注入替换 (Inject)
将修改后的 JSON 数据和您的 PPT 目标文件传入注入脚本，一键完成无损替换：
```bash
# 示例：将修改后的 my_mapping.json 批量注入到 Result 中的目标 PPT
python scripts/batch_injector.py Result/生息守护_路演答辩.pptx Tmp/my_mapping.json
```
运行完成后，`Result/生息守护_路演答辩.pptx` 中的文字便会被完美替换，且字体、字号、色值与排版完全保持不变！

---

## ⚙️ 技能部署与激活 (How to Install as a Skill)

若要让您的 AI 编码助手在以后的对话中自动检索并加载该技能，您可以将整个 `populating-office-templates` 目录复制到以下路径之一：

* **全局生效（推荐）**：
  复制到 `C:\Users\<您的用户名>\.gemini\config\skills/populating-office-templates`
* **项目私有生效**：
  复制到当前项目根目录下的 `.agents/skills/populating-office-templates`

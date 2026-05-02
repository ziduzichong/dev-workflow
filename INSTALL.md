# dev-workflow 安装指南（V1 — Claude Code 插件版）

**版本：** 1.0.0
**适用：** Claude Code / WorkBuddy
**GitHub：** https://github.com/ziduzichong/dev-workflow

---

## 方式一：通过 Git Clone 安装（推荐）

> 克隆仓库后，将 skill 复制到 Claude Code 的 skills 目录。

### Bash / Git Bash

```bash
# 1. 克隆仓库
git clone https://github.com/ziduzichong/dev-workflow.git

# 2. 复制到全局 skills 目录
mkdir -p ~/.workbuddy/skills/
cp -r dev-workflow/skills/dev-workflow ~/.workbuddy/skills/
```

### PowerShell

```powershell
# 1. 克隆仓库
git clone https://github.com/ziduzichong/dev-workflow.git

# 2. 复制到全局 skills 目录
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills"
Copy-Item -Recurse "dev-workflow\skills\dev-workflow" "$env:USERPROFILE\.workbuddy\skills\dev-workflow"
```

### 后续更新

```bash
cd dev-workflow
git pull
# 然后重新复制 skills/ 到目标路径
cp -r skills/dev-workflow ~/.workbuddy/skills/
```

---

## 方式二：直接下载 ZIP 安装

> 不想用 Git？直接从 GitHub 下载 ZIP，解压后复制。

### 步骤

1. 打开 https://github.com/ziduzichong/dev-workflow
2. 点击绿色 **Code** 按钮 → 选择 **Download ZIP**
3. 解压 ZIP 文件
4. 将 `dev-workflow-main/skills/dev-workflow` 复制到目标路径

### Bash / Git Bash

```bash
cp -r ~/Downloads/dev-workflow-main/skills/dev-workflow ~/.workbuddy/skills/
```

### PowerShell

```powershell
Copy-Item -Recurse "$env:USERPROFILE\Downloads\dev-workflow-main\skills\dev-workflow" "$env:USERPROFILE\.workbuddy\skills\dev-workflow"
```

---

## 方式三：用 curl 直接下载（最简）

> 只需核心文件，一行命令搞定。

### Bash / Git Bash

```bash
# 下载 SKILL.md
mkdir -p ~/.workbuddy/skills/dev-workflow/references
curl -o ~/.workbuddy/skills/dev-workflow/SKILL.md \
  https://raw.githubusercontent.com/ziduzichong/dev-workflow/main/skills/dev-workflow/SKILL.md

# 下载 references 文件（V1 依赖这些外部文件）
for f in build-commands.md phase-1-planning.md phase-2-architecture.md phase-3-tdd-coding.md phase-4-debugging.md phase-5-review.md phase-6-optimization.md phase-7-documentation.md rules-global.md rules-hooks.md rules-telemetry.md; do
  curl -o ~/.workbuddy/skills/dev-workflow/references/$f \
    https://raw.githubusercontent.com/ziduzichong/dev-workflow/main/skills/dev-workflow/references/$f
done
```

### PowerShell

```powershell
# 下载 SKILL.md
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills\dev-workflow\references"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/ziduzichong/dev-workflow/main/skills/dev-workflow/SKILL.md" `
  -OutFile "$env:USERPROFILE\.workbuddy\skills\dev-workflow\SKILL.md"

# 下载 references 文件（逐个下载）
$refFiles = @("build-commands.md","phase-1-planning.md","phase-2-architecture.md","phase-3-tdd-coding.md","phase-4-debugging.md","phase-5-review.md","phase-6-optimization.md","phase-7-documentation.md","rules-global.md","rules-hooks.md","rules-telemetry.md")
foreach ($f in $refFiles) {
    Invoke-WebRequest -Uri "https://raw.githubusercontent.com/ziduzichong/dev-workflow/main/skills/dev-workflow/references/$f" `
      -OutFile "$env:USERPROFILE\.workbuddy\skills\dev-workflow\references\$f"
}
```

---

## 安装依赖 Skill

本 Skill 依赖以下 Superpowers Skill（已安装可跳过）：

```bash
# 安装 Superpowers（如未安装）
# 参考：https://github.com/anthropics/superpowers
```

---

## Skill 安装路径说明

| 类型 | 路径 | 说明 |
|------|------|------|
| 全局级（WorkBuddy） | `~/.workbuddy/skills/<name>/SKILL.md` | 所有项目生效 |
| 项目级 | `.workbuddy/skills/<name>/SKILL.md` | 仅当前项目生效 |
| Claude Code 兼容 | `~/.claude/skills/<name>/SKILL.md` | Claude Code 全局 |

> Windows 下 `~` 对应 `C:\Users\你的用户名\`。上方命令默认安装到 WorkBuddy 全局路径。

---

## 验证安装

启动 Claude Code / WorkBuddy，输入：

```
用户：启动工作流，我要开发一个 STM32 的串口命令解析器
```

**预期行为：**
1. AI 触发 dev-workflow
2. 执行苏格拉底追问（4个维度）
3. 输出设计文档 → 逐阶段推进

---

## 卸载

### Bash

```bash
# 全局卸载（WorkBuddy）
rm -rf ~/.workbuddy/skills/dev-workflow/

# 项目级卸载
rm -rf .workbuddy/skills/dev-workflow/

# Claude Code 兼容路径
rm -rf ~/.claude/skills/dev-workflow/
rm -rf .claude/skills/dev-workflow/
```

### PowerShell

```powershell
# 全局卸载（WorkBuddy）
Remove-Item -Recurse -Force "$env:USERPROFILE\.workbuddy\skills\dev-workflow\"

# 项目级卸载
Remove-Item -Recurse -Force .workbuddy\skills\dev-workflow\

# Claude Code 兼容路径
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\skills\dev-workflow\"
Remove-Item -Recurse -Force .claude\skills\dev-workflow\
```

---

## 目录结构

```
dev-workflow/
├── README.md              ← 项目说明
├── INSTALL.md             ← 本文档（安装指南）
├── LICENSE
└── skills/
    └── dev-workflow/
        ├── SKILL.md       ← 主文件（L1 入口，~1500字）
        └── references/    ← 外部文件（按需加载）
            ├── phase-1-planning.md
            ├── phase-2-architecture.md
            ├── phase-3-tdd-coding.md
            ├── phase-4-debugging.md
            ├── phase-5-review.md
            ├── phase-6-optimization.md
            ├── phase-7-documentation.md
            ├── build-commands.md
            ├── rules-global.md
            ├── rules-hooks.md
            └── rules-telemetry.md
```

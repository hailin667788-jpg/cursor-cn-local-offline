<div align="center">

# cursor-cn-local-offline

为 Cursor 补齐简体中文，让熟悉的界面更亲切；所有翻译均来自本地词典。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node.js 24+](https://img.shields.io/badge/Node.js-24%2B-5FA04E.svg)](https://nodejs.org/)

[快速上手](#快速上手) · [安全边界](#安全边界)

</div>

这是对 [`zhanpoint/cursor-cn`](https://github.com/zhanpoint/cursor-cn) 的本地优先修改版：结合官方简体中文语言包与本地翻译词典，补齐 Cursor 专有界面的中文文案。仅使用 Node.js 内置模块，无需安装 npm 依赖。

本版本删除了插件市场页面的 Google 在线翻译请求。词典未收录的文本会保持英文，不会离开本机。

## 快速上手

**准备环境**：安装 Cursor 和 [Node.js 24+](https://nodejs.org/)。无需运行 `npm install`。

1. 下载或克隆本项目并解压。
2. 保存 Cursor 中正在编辑的文件。
3. **Windows**：双击解压目录中的 `cursor-cn.bat`。**macOS**：先阅读下面的「macOS 安全副本」步骤；若 Cursor 安装在可写目录，也可在项目目录运行 `bash cursor-cn.sh`。

等待终端提示完成，Cursor 会自动重新启动。首次运行若提示安装路径，填写 Cursor 实际安装目录。

### macOS 安全副本

macOS 可能禁止改写 `/Applications/Cursor.app`。下面的方式创建独立副本，不触及原版 Cursor；请把 `项目目录` 替换成实际解压路径：

```bash
ditto /Applications/Cursor.app "$HOME/Documents/Cursor 全中文.app"
plutil -replace CFBundleIdentifier -string com.todesktop.230313mzl4w4u92.cursorcnlocal \
  "$HOME/Documents/Cursor 全中文.app/Contents/Info.plist"
cd "项目目录"
CURSOR_CN_SKIP_LANGUAGE_PACK=1 \
CURSOR_INSTALL_DIR="$HOME/Documents/Cursor 全中文.app" \
node cursor-cn.ts --no-restart
codesign --force --deep --sign - "$HOME/Documents/Cursor 全中文.app"
open "$HOME/Documents/Cursor 全中文.app"
```

`CURSOR_CN_SKIP_LANGUAGE_PACK=1` 仅适用于已经装好官方简体中文语言包的机器；否则删掉这一段环境变量，让工具下载官方语言包。以后从 `Documents/Cursor 全中文.app` 打开汉化版。若要重新制作副本，请先手动移走旧副本，再执行上述命令。

<details>
<summary>使用 Git 和命令行</summary>

```bash
git clone https://github.com/<你的用户名>/cursor-cn-local-offline.git
cd cursor-cn-local-offline
node cursor-cn.ts
```

不要双击 `.ts` 文件。工具运行时会关闭 Cursor，请先保存文件。

</details>

## 功能

- **界面汉化**：补齐设置、智能体、规则、命令、菜单、提示与插件市场等界面文案。
- **本地动态翻译**：跟随界面加载与文本更新处理本地词典翻译，跳过代码编辑区和可编辑内容。
- **语言包适配**：匹配 Cursor 内置的 VS Code 版本，安装官方简体中文语言包。
- **备份与恢复**：备份被修改的原始文件，支持恢复和文件校验值修复。
- **词典维护**：翻译词典与运行引擎分离，便于补充遗漏和适配新版本。

## 使用与配置

## 安全边界

- 不发送界面文字、代码或其他数据到翻译服务；本项目不含网络翻译请求。
- 应用汉化会关闭 Cursor，并修改其安装包中的 `workbench.html`、`main.js` 和 `product.json`；首次运行会建立 `.bak` 备份。
- Cursor 更新可能覆盖汉化。macOS 更新后需重新运行本工具；`node cursor-cn.ts --restore` 可恢复备份。
- 这不是 Cursor 官方项目。请先保存未保存的编辑内容。

**Windows 自动恢复**：安装汉化后启用当前用户的登录启动检查，保留原有快捷方式与任务栏入口。Cursor 更新覆盖汉化时，后台等待文件稳定后自动重新应用；若 Cursor 正在运行，会关闭并重新启动，通常需等待约 10–15 秒。请在更新前保存文件。

请保留本项目目录和 Node.js，自动检查依赖它们运行。使用 `--restore` 会同时停用后台检查与登录启动。macOS 更新后仍需手动重新运行工具。

通过 Git 获取项目的用户可执行 `git pull` 更新词典，再重新应用。

### 常用命令

| 命令 | 作用 |
| --- | --- |
| `node cursor-cn.ts` | 安装或更新汉化 |
| `node cursor-cn.ts --restore` | 恢复原始界面文件，完成后手动启动 Cursor |
| `node cursor-cn.ts --fix-checksum` | 修复文件校验值 |
| `node cursor-cn.ts --print-paths` | 查看检测到的安装与用户数据路径 |
| `node cursor-cn.ts --no-restart` | 应用汉化后不自动启动 Cursor |

`--restore` 不会卸载已安装的官方语言包。

### 自定义路径

优先读取 `CURSOR_INSTALL_DIR` / `CURSOR_ROOT` 环境变量，其次读取项目目录中的 `cursor-cn.config.json` 和系统默认安装目录。未检测到有效目录时，会提示输入安装路径并保存，后续无需重复填写。

Windows 默认安装目录为 `%LOCALAPPDATA%\Programs\cursor`；安装在其他位置时，输入实际目录，例如 `D:\App\cursor`。

| 环境变量 | 作用 |
| --- | --- |
| `CURSOR_INSTALL_DIR` / `CURSOR_ROOT` | 指定 Cursor 安装目录，优先于已有配置 |
| `CURSOR_USER_DATA_DIR` | 覆盖 Cursor 用户数据目录 |
| `CURSOR_LANGUAGE_PACK_VSIX` | 使用本地、版本匹配的简体中文语言包 VSIX |
| `CURSOR_CN_SKIP_LANGUAGE_PACK=1` | 跳过官方语言包下载；适合已经在 `~/.cursor/extensions` 安装中文包的机器 |

## 常见问题

**还有英文文案？** 更新项目并重新运行。若仍有遗漏，请在 Issue 中附上 Cursor 版本、界面截图和英文原文。

**提示文件损坏或校验异常？** 运行 `node cursor-cn.ts --fix-checksum`；需要撤销界面修改时使用 `--restore`。

**语言包下载失败？** 检查网络，或通过 `CURSOR_LANGUAGE_PACK_VSIX` 指定匹配版本的本地语言包。专有界面词典翻译仍可独立应用。

## 参与贡献

欢迎通过你自己的 GitHub 仓库 Issue 和 Pull Request 改进翻译。

- 通用文案：[`localization/core.json`](localization/core.json)
- 动态文案：[`localization/patterns.json`](localization/patterns.json)
- 场景词典与翻译引擎：[`localization/`](localization/)

修改时请沿用现有结构，保留产品名称、命令与参数。翻译词典修改后需重新运行工具才能应用。

```bash
node --test localization.test.js jsonc.test.ts update-watch.test.ts
```

## 许可

[MIT](LICENSE)；衍生自 [zhanpoint/cursor-cn](https://github.com/zhanpoint/cursor-cn)，保留原始 MIT 许可证和版权声明。

本项目由个人维护，与 Cursor 官方无关联；不同 Cursor 版本的文案覆盖情况可能有所差异。

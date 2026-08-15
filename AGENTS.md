<!-- gitnexus:start -->
# GitNexus — Code Intelligence

This project is indexed by GitNexus as **winUpdateMiniTool** (399 symbols, 994 relationships, 34 execution flows). Use the GitNexus MCP tools to understand code, assess impact, and navigate safely.

> Index stale? Run `node .gitnexus/run.cjs analyze` from the project root — it auto-selects an available runner. No `.gitnexus/run.cjs` yet? `npx gitnexus analyze` (npm 11 crash → `npm i -g gitnexus`; #1939).

## Always Do

- **MUST run impact analysis before editing any symbol.** Before modifying a function, class, or method, run `impact({target: "symbolName", direction: "upstream"})` and report the blast radius (direct callers, affected processes, risk level) to the user.
- **MUST run `detect_changes()` before committing** to verify your changes only affect expected symbols and execution flows. For regression review, compare against the default branch: `detect_changes({scope: "compare", base_ref: "master"})`.
- **MUST warn the user** if impact analysis returns HIGH or CRITICAL risk before proceeding with edits.
- When exploring unfamiliar code, use `query({search_query: "concept"})` to find execution flows instead of grepping. It returns process-grouped results ranked by relevance.
- When you need full context on a specific symbol — callers, callees, which execution flows it participates in — use `context({name: "symbolName"})`.
- For security review, `explain({target: "fileOrSymbol"})` lists taint findings (source→sink flows; needs `analyze --pdg`).

## Never Do

- NEVER edit a function, class, or method without first running `impact` on it.
- NEVER ignore HIGH or CRITICAL risk warnings from impact analysis.
- NEVER rename symbols with find-and-replace — use `rename` which understands the call graph.
- NEVER commit changes without running `detect_changes()` to check affected scope.

## Resources

| Resource | Use for |
|----------|---------|
| `gitnexus://repo/winUpdateMiniTool/context` | Codebase overview, check index freshness |
| `gitnexus://repo/winUpdateMiniTool/clusters` | All functional areas |
| `gitnexus://repo/winUpdateMiniTool/processes` | All execution flows |
| `gitnexus://repo/winUpdateMiniTool/process/{name}` | Step-by-step execution trace |

## CLI

| Task | Read this skill file |
|------|---------------------|
| Understand architecture / "How does X work?" | `.claude/skills/gitnexus/gitnexus-exploring/SKILL.md` |
| Blast radius / "What breaks if I change X?" | `.claude/skills/gitnexus/gitnexus-impact-analysis/SKILL.md` |
| Trace bugs / "Why is X failing?" | `.claude/skills/gitnexus/gitnexus-debugging/SKILL.md` |
| Rename / extract / split / refactor | `.claude/skills/gitnexus/gitnexus-refactoring/SKILL.md` |
| Tools, resources, schema reference | `.claude/skills/gitnexus/gitnexus-guide/SKILL.md` |
| Index, status, clean, wiki CLI commands | `.claude/skills/gitnexus/gitnexus-cli/SKILL.md` |
| Work in the WinUpdateMiniTool area (180 symbols) | `.claude/skills/generated/winupdateminitool/SKILL.md` |

<!-- gitnexus:end -->

# winUpdateMiniTool — 项目说明

## 项目定位

Windows 更新管理工具（WinForms 桌面应用），通过 **WUApiLib（Windows Update Agent API，COM 互操作）** 检查、下载、安装、卸载微软产品更新。支持 Windows 7–11 / Server 2012–2025。单项目解决方案：`winUpdateMiniTool.sln`，全部源码在 `winUpdateMiniTool/` 子目录。**界面显示文本已全部汉化为简体中文**（提交 `ef6f466`）。

## 构建与发布

- **双目标框架**：`net472`（主发布版，Costura.Fody 将依赖内嵌为单 exe）和 `net8.0-windows`（发布为 `winUpdateMiniTool.core`，单文件、非自包含）。
- **RID**：`win-x64`、`win-arm64`；net8 版本分别产出 `.core`（x64）和 `.arm64`（arm64）两个变体。
- **本地发布**：仓库根目录 `make.bat`（用 vswhere 定位 MSBuild，输出到 `winUpdateMiniTool/bin/publish/`）。
- **CI 构建**（GitHub Actions）：`msbuild winUpdateMiniTool.sln -p:Configuration=Release -m`。
- **无测试项目**，验证靠构建通过 + 手动运行。
- ⚠️ `clean.cmd` 除删除 bin/obj/.vs 外还会执行 `git reflog expire` + `git gc --prune=now`（**破坏性、不可逆**），不要主动运行。

## 关键源文件（均在 winUpdateMiniTool/ 下）

| 文件 | 职责 |
|------|------|
| `Program.cs` | 入口：单实例检查、UAC 自提升（runas）、`wumtNoUAC` 计划任务、`-console` 控制台模式 |
| `WuAgent.cs` | 核心：封装 WUApiLib 的检查/下载/安装/卸载流程，维护已装/待装/隐藏/历史更新列表 |
| `MsUpdate.cs` | 更新项数据模型 |
| `UpdateDownloader.cs` / `UpdateInstaller.cs` | 下载、安装的回调封装 |
| `UpdateErrors.cs` | WU 错误码映射 |
| `GPO.cs` | 组策略/注册表开关（更新策略、Defender 等） |
| `MainForm.cs` + `MainForm.Designer.cs` | 全部 UI（体量大，改动需谨慎） |
| `Common/MiscFunc.cs` | 解析辅助（int、颜色） |

## 重要依赖与边界

- `SergiyE.Common` / `SergiyE.Common.UI` 是作者自有的 NuGet 公共库，提供 `Updater`、`AppLog`、`OSHelper`、`WinConsole`、`WinApiHelper` 等——**源码不在本仓库**，在本仓库搜不到定义属正常。
- COM 引用：`WUApiLib` v2.0、`TaskScheduler` 1.0，均 `EmbedInteropTypes=true`（嵌入互操作类型，不产出互操作 DLL）。
- net472 的 Release 构建有 PostBuild 目标删除 exe 以外的输出文件（配合 Costura 单文件化），net472 输出目录只剩一个 exe 是预期行为。

## 代码风格

- 2 空格缩进；file-scoped namespace（`namespace winUpdateMiniTool;`）；类默认 `internal`。
- 私有实例字段用 `m` 前缀（如 `mDispatcher`、`mInstance`）；常量全大写（如 `MF_APP_TASK_NAME`）。
- 日志统一走 `AppLog.Line(...)`，不要引入其他日志框架。
- `LangVersion=latest`，可用新语法（如 `[]` 集合表达式、target-typed new）。
- csproj 中大量按 `$(TargetFramework)` 条件化的属性组/引用，改属性时先确认两个 TFM 下的行为。

## 本地化（汉化）约定

- **用户可见字符串一律用简体中文**：控件文本、消息框、气泡通知、托盘菜单、状态栏、`AppLog.Line` 日志、`UpdateErrors.cs` 错误描述。
- **只改字符串字面量，不改代码标识符/变量名**（用户明确要求过）。不要为汉化引入包装常量或替换 `Updater.ApplicationTitle` 之类的外部库属性引用。
- 有意保留英文的部分：`.ini` 配置键名、COM API 标识（如 `"Offline Sync Service"`、`"wuauserv"`）、`ClientApplicationID`、GPL 版权声明、以及来自微软服务器的更新标题/类别（无法本地化）。
- 消息框与窗体标题栏显示英文产品名 "Windows Update Mini Tool"（外部库 `SergiyE.Common` 的 `Updater.ApplicationTitle`，源码不在本仓库）——这是接受现状，勿反复尝试修改。
- 源文件均为 UTF-8 **带 BOM**，新增/修改含中文的文件时保持，否则 MSBuild 可能按错误码页编译。
- ComboBox 选项（星期、重启延迟等）已汉化，程序按 `SelectedIndex` 存取配置而非文本，改文案不影响兼容性。

## 运行时行为（改动时注意）

- 程序运行时需要管理员权限：manifest 为 `asInvoker`，由 `Program.Main` 自行检测并以 `runas` 重启提权；调试器附加状态下跳过。
- 配置存于 exe 同名 `.ini`（`Program.GetIniPath()`）；`Tools` 子目录放外部工具（`Program.GetToolsPath()`）。
- 主题系统：exe 旁 `themes/*.json` 自定义主题，支持 Light/Dark/Auto；颜色解析见 `Common/MiscFunc.cs`。

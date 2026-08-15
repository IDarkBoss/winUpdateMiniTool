---
name: winupdateminitool
description: "Skill for the WinUpdateMiniTool area of winUpdateMiniTool. 180 symbols across 10 files."
---

# WinUpdateMiniTool

180 symbols | 10 files | Cohesion: 81%

## When to Use

- Working with code in `winUpdateMiniTool/`
- Understanding how UpdatesArgs, ProgressArgs, IsActive work
- Modifying winupdateminitool-related functionality

## Key Files

| File | Symbols |
|------|---------|
| `winUpdateMiniTool/MainForm.cs` | OnTimedEvent, GetAutoUpdateDue, GetGraceDays, GetUpdates, UpdateState (+71) |
| `winUpdateMiniTool/WuAgent.cs` | IsActive, IsBusy, CurOperation, SetupOffline, SetOnline (+42) |
| `winUpdateMiniTool/Program.cs` | GetToolsPath, ExecOnStart, ExecOnClose, PrepExec, DoExec (+12) |
| `winUpdateMiniTool/UpdateInstaller.cs` | OnFinished, RunInstall, InstallExe, InstallMsi, InstallMsu (+10) |
| `winUpdateMiniTool/GPO.cs` | GetRespect, GetWinVersion, ConfigAu, DisableAu, ConfigSvc (+6) |
| `winUpdateMiniTool/UpdateDownloader.cs` | Download, DownloadNextFile, OnFinished, OnProgress, IsBusy (+1) |
| `winUpdateMiniTool/MsUpdate.cs` | GetUpdate, Invalidate, AddUpdates, AddUpdates |
| `winUpdateMiniTool/Common/MiscFunc.cs` | ParseInt, ParseColor |
| `winUpdateMiniTool/UpdateErrors.cs` | GetErrorStr |
| `winUpdateMiniTool/MainForm.Designer.cs` | MainForm |

## Entry Points

Start here when exploring this area:

- **`UpdatesArgs`** (Class) — `winUpdateMiniTool/WuAgent.cs:980`
- **`ProgressArgs`** (Class) — `winUpdateMiniTool/WuAgent.cs:965`
- **`IsActive`** (Method) — `winUpdateMiniTool/WuAgent.cs:117`
- **`IsBusy`** (Method) — `winUpdateMiniTool/WuAgent.cs:121`
- **`CurOperation`** (Method) — `winUpdateMiniTool/WuAgent.cs:269`

## Key Symbols

| Symbol | Type | File | Line |
|--------|------|------|------|
| `UpdatesArgs` | Class | `winUpdateMiniTool/WuAgent.cs` | 980 |
| `ProgressArgs` | Class | `winUpdateMiniTool/WuAgent.cs` | 965 |
| `IsActive` | Method | `winUpdateMiniTool/WuAgent.cs` | 117 |
| `IsBusy` | Method | `winUpdateMiniTool/WuAgent.cs` | 121 |
| `CurOperation` | Method | `winUpdateMiniTool/WuAgent.cs` | 269 |
| `Download` | Method | `winUpdateMiniTool/UpdateDownloader.cs` | 24 |
| `SearchForUpdates` | Method | `winUpdateMiniTool/WuAgent.cs` | 273 |
| `SearchForUpdates` | Method | `winUpdateMiniTool/WuAgent.cs` | 284 |
| `DownloadUpdatesManually` | Method | `winUpdateMiniTool/WuAgent.cs` | 379 |
| `DownloadUpdates` | Method | `winUpdateMiniTool/WuAgent.cs` | 555 |
| `ParseInt` | Method | `winUpdateMiniTool/Common/MiscFunc.cs` | 9 |
| `GetRespect` | Method | `winUpdateMiniTool/GPO.cs` | 373 |
| `GetWinVersion` | Method | `winUpdateMiniTool/GPO.cs` | 404 |
| `GetToolsPath` | Method | `winUpdateMiniTool/Program.cs` | 28 |
| `PrepExec` | Method | `winUpdateMiniTool/Program.cs` | 167 |
| `DoExec` | Method | `winUpdateMiniTool/Program.cs` | 207 |
| `EnableWuAuServ` | Method | `winUpdateMiniTool/WuAgent.cs` | 841 |
| `TestWuAuServ` | Method | `winUpdateMiniTool/WuAgent.cs` | 860 |
| `GetUpdate` | Method | `winUpdateMiniTool/MsUpdate.cs` | 255 |
| `GetErrorStr` | Method | `winUpdateMiniTool/UpdateErrors.cs` | 11 |

## Execution Flows

| Flow | Type | Steps |
|------|------|-------|
| `Main → WritePrivateProfileString` | cross_community | 7 |
| `Main → GetIniPath` | cross_community | 7 |
| `DownloadsFinished → WritePrivateProfileString` | cross_community | 6 |
| `DownloadsFinished → GetIniPath` | cross_community | 6 |
| `Main → RemoveService` | cross_community | 5 |
| `Main → GetErrorStr` | cross_community | 5 |
| `Main → GetCategory` | cross_community | 5 |
| `Main → UpdatesArgs` | cross_community | 5 |
| `BtnUnInstall_Click → ProgressArgs` | cross_community | 5 |
| `Main → Invalidate` | cross_community | 4 |

## How to Explore

1. `context({name: "UpdatesArgs"})` — see callers and callees
2. `query({search_query: "winupdateminitool"})` — find related execution flows
3. Read key files listed above for implementation details
4. `explain({target: "<file or symbol>"})` — persisted taint findings (source→sink data flows), when indexed with `--pdg`

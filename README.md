# VibeUE

Unreal Engine 5.8+ 的 MCP 扩展 + AI 编辑器工具集插件。上游项目：[kevinpbuckley/VibeUE](https://github.com/kevinpbuckley/VibeUE)（MIT）。

## 部署方式

**环境要求**：UE 5.8+（已在团队引擎 `E:/M2Game/ue_engine/Windows`，5.8.1 源码构建上验证）+ Visual Studio C++ 环境。

### 1. 获取并放入工程

```bash
git clone https://gitlab.h3d.com.cn/mug_m2/M2_Client_Misc/vibeue.git
```

两种方式任选：

- **软链（推荐，开发期）**——仓库与工程共用一份，改一处两边生效：

  ```cmd
  mklink /J E:\M2Game\game\client\Plugins\VibeUE <仓库检出路径>
  ```

- **拷贝**——把仓库目录复制为 `YourProject/Plugins/VibeUE`（升级时需手动同步）。

### 2. 编译

团队引擎（编辑器目标）：

```cmd
E:\M2Game\ue_engine\Windows\Engine\Build\BatchFiles\Build.bat M2_ClientEditor Win64 Development E:\M2Game\game\client\M2_Client.uproject -WaitMutex
```

产物：`Plugins/VibeUE/Binaries/Win64/UnrealEditor-VibeUE.dll`（Binaries/Intermediate 已 gitignore）。

标准引擎环境可用自带脚本 `BuildAndLaunchGame.ps1`（自动向上查找 .uproject、按注册表探测引擎，支持 `-StrictRebuild` / `-Map` 等参数）。

### 3. 启用插件

1. 编辑 → 插件 → 启用 **Unreal MCP**（自动带上 Toolset Registry）和 **Editor Tools**，按提示重启
2. 编辑 → 插件 → 启用 **VibeUE**，重启
3. 编辑器偏好设置 → Model Context Protocol → 开启 **Auto Start Server**（默认端点 `http://127.0.0.1:8000/mcp`，仅本机回环、无鉴权）

## 使用方式

### 接入 AI 客户端

编辑器控制台（`` ` `` 键）执行：

```
ModelContextProtocol.GenerateClientConfig ClaudeCode   // 生成 .mcp.json（支持 ClaudeCode/Cursor/VSCode/Gemini/Codex/All）
VibeUE.GenerateAgentConfig ClaudeCode                  // 生成 CLAUDE.md 等代理使用指南，可重复执行刷新
```

### 代理侧高效用法

- `ListSkills` / `GetSkills`——发现并懒加载 36 个领域技能包（蓝图、地形、Niagara、动画、BT、性能等）
- `discover_python_class('unreal.<Name>Service')`——查询服务的精确方法签名
- `execute_python_code`——**主力通道**，一次往返批量执行多步任务，覆盖全部 VibeUE 服务及整个 `unreal.*` API
- `call_tool`——仅用于技能加载和截图等无 Python 路径的工具

### Python 控制台直用

```python
import unreal
print(unreal.PerformanceService.frame_timing())   # CPU/GPU 瓶颈判定，性能分析第一步
```

### 说明

- 真实世界地形工具需在 编辑器偏好设置 → 插件 → VibeUE 中填写免费 API Key，其余功能无需 Key
- MCP 端点仅监听 `127.0.0.1`，只能本机使用

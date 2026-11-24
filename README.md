# MOSS ComfyUI Deploy Skill

让本机 AI 帮助新手部署 **ComfyUI 原生 MOSS-Image-SFT＋单份 LoRA 文生图**：检查电脑、准备兼容环境、按需下载模型、导入工作流、真实出图验收，并提供可操作的启动与使用说明。

不是一键安装器，不包含模型、Python 或 ComfyUI，也不保证任意显卡均可运行。安装 Skill 本身不会改动绘图环境。

## 给 AI 使用

把本仓库的 [`skills/moss-sft-lora-deploy`](skills/moss-sft-lora-deploy) 整个文件夹放进智能体的 Skill 目录。例如 Codex 使用用户的 `~/.codex/skills/`；其他工具按各自规范安装。文件夹必须保留 SKILL.md、references、assets 和 agents 的相对位置。

然后对智能体说：

> 使用 $moss-sft-lora-deploy，先检查我的电脑，再部署 MOSS SFT＋LoRA 文生图。不要覆盖已有环境；实际出图后教我使用。

没有 Skill 功能的智能体也可以读取 [`SKILL.md`](skills/moss-sft-lora-deploy/SKILL.md)，再按链接读取必要参考资料。需要本机文件与命令执行能力；涉及下载、安装和启动时遵守用户授权与工具权限。

## 内容

- `SKILL.md`：任务范围、部署决策、验收标准。
- `references/models.md`：四项权重的官方/作者来源、版本与校验办法。
- `references/troubleshooting.md`：缓存秒出、缺节点、依赖、显存和格式排错。
- `assets/models.json`：基准文件大小、SHA-256、下载地址与放置目录。
- `assets/workflow-ui.json`：可导入 ComfyUI 的界面模板。
- `assets/workflow-api.json`：供智能体提交本机后端验证的 API 模板。

## 范围与限制

默认基准为 SFT INT8 主模型、Qwen3.5 W4A8 编码器、FLUX.2 small VAE，以及原配社区 LoRA。可在确认兼容性后替换用户的人物 LoRA；不能根据文件名猜触发词。

原配社区 LoRA 的作者仓库带成人内容标记，下载前应告知用户并确认选择。功能测试使用普通穿着的成人虚构人物。不用于未成年人性内容、未经同意的私密影像或规避安全保护。

本 Skill 不负责 Base/SFT 融合、四步加速、参考脸、换脸或局部重绘。模板不是在新电脑上已验证的部署结果；必须由接手 AI 完成实际验收。

来源核对日期：2026-11-24。上游版本、同名模型内容和硬件支持可能变化，使用前重新核对。模型和软件遵循各自上游许可证；本仓库不重新分发权重或授予其额外使用权。

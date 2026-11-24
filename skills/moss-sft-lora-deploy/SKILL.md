---
name: moss-sft-lora-deploy
description: 在本机部署、恢复或排查 ComfyUI 原生 MOSS-Image-SFT＋单份 LoRA 文生图；检查硬件和现有环境，下载匹配权重并完成真实出图验收。适用于新手本地部署，不用于换脸、参考图编辑或 LoRA 训练。
---

# MOSS SFT＋LoRA 本地部署

目标是让用户能启动、修改提示词、生成新图并找到输出；不把“文件存在”或“任务提交成功”说成部署完成。使用当前智能体的本机工具，遵守用户授权；安装本 Skill 不代表用户已授权安装模型或修改环境。

## 检查与选择

先检查 OS/架构、GPU 厂商与型号、显存及占用、驱动、RAM/可用内存、磁盘及缓存盘空间、现有 ComfyUI/运行时/端口和网络。能查到的不反复问用户；安装位置、会影响旧环境的升级和带成人标记的原配 LoRA 选择需要向用户说清。

Windows NVIDIA 可优先使用兼容的官方 Portable；其他硬件查当前支持，不照抄 CUDA 命令。12GB 显存并不保证固定速度或成功；下载体积不等于运行内存需求。四项基准权重约 13.08GB，另留程序、缓存、解压和输出余量。

已有健康环境优先评估复用。需要隔离时新建目录，不覆盖旧安装；识别运行实例和队列，不随意杀 Python、删数据库或切换用户原来的工作流。

## 模型与环境

下载前阅读 [模型来源与兼容要求](references/models.md)，对照 [机器可读清单](assets/models.json)。仅准备 SFT 主模型、编码器、VAE 和用户选定的一份 LoRA。原生兼容版本不需要 MOSS 自定义插件；不要安装换脸、融合或万能节点包。

通过当前版本官方说明与实际加载器检查 MOSS、INT8、W4A8 GPTQ 和 MOSS LoRA 支持，记录软件版本/commit。不要只凭“最新”或版本号判断。下载大文件需检查完整性及 SHA-256，不能把错误页或 LFS 指针当权重；同名文件可能换版，哈希不符先确认来源，不静默跳过。

依赖安装到实际启动该 ComfyUI 的 Python。更新代码后同步评估 requirements，不用系统 pip 或照搬 Diffusers 的全量升级命令。可能破坏旧插件时先说明，优先隔离，不修改核心源码作为默认修法。

## 搭建工作流

从 [界面模板](assets/workflow-ui.json) 复制一份用户副本，按实际加载器枚举选模型。程序验证使用 [API 模板](assets/workflow-api.json)，不要混淆两种 JSON。

```text
MOSS SFT → LoraLoaderModelOnly → KSampler → VAEDecode → SaveImage
CLIPLoader(moss_image) → 正/负 CLIPTextEncode → KSampler
EmptyFlux2LatentImage → KSampler
VAELoader(small VAE) → VAEDecode
```

基准：768×1024、batch=1、30 步、CFG=3、er_sde/simple、denoise=1；原配 LoRA 强度 0.8，触发词 moss_xvstyle，负向为空。这是简化复现配置，不是官方最优画质保证。未经用户选择不加 Base 融合、SLG、shift 补丁、加速或第二份 LoRA。

用户改用人物 LoRA 时确认架构/训练底模、格式、真实触发词和推荐强度；不要凭文件名推测触发词，也不沿用旧 LoRA 的触发词。人物 LoRA 不等于换脸或绝对身份锁定。

## 启动与验收

识别已有服务，选择一个空闲端口并保持单一明确入口。12GB 类 NVIDIA 设备可评估 lowvram，不默认公网监听、CPU 模式或异常精度选项。检查该实例 `/system_stats` 和 `/object_info`，确认节点与模型枚举；不是只打开一个网页。

依次验证：
1. 静态：全部节点、文件选择、有效模型链路和参数匹配。
2. 基础：用户副本中 LoRA 强度置零、固定种子，用普通穿着成人虚构人像生成一张，检查模型/编码器/VAE。
3. LoRA：相同提示词、种子和参数恢复选定强度；检查未加载键与形状警告，并对照结果。无报错不自动证明全部 LoRA 模块生效。
4. 新图：更换种子再生成，确认采样执行、文件和像素改变，不复用缓存冒充新图。
5. 界面与视觉：在用户实际使用的 UI 导入副本、运行并打开真正 PNG，确认不是黑白图/噪声，尺寸与基本描述相符。单独说明审美和身份效果是否获用户认可。

API 提交结构是 `{"prompt": <API节点字典>}`，发送到本机 `/prompt`。记录 prompt_id，结合队列、执行事件、`/history/<prompt_id>` 和输出文件确认完成；history 可能稍晚可见，先短暂重试，避免重复提交。API 客户端自行更换 seed；前端种子控制不在 API 模板里。

遇到异常阅读 [排错参考](references/troubleshooting.md)。没有新的诊断依据时停止反复安装，报告实际阻塞；不为“能出图”而未经说明换模型、减质量或放弃用户选择。

## 交给新手

交付经过本机核验的双击启动入口、唯一网址、可导入工作流和输出目录。演示“启动→修改正向提示词→点一次运行→等完成→找图片”，后台窗口使用中不能关闭。

解释 fixed 用于比较；randomize/increment 用于连续新图。原模板保存 seed=42/fixed，日常用户副本需说明后改为随机或递增。CFG、步数、LoRA 强度和 denoise 不是同一个控制，文生图保持 denoise=1。

记录真实设备、Python/PyTorch、ComfyUI 版本、路径/端口、权重 revision/哈希、参数/种子、prompt ID、耗时、输出位置及视觉结论。对外说明“已下载/已加载/已实际出图/用户认可”分别做到哪一步。只分享 Skill 和通用模板，不包含私人图片、权重、密钥、个人绝对路径或终端历史。

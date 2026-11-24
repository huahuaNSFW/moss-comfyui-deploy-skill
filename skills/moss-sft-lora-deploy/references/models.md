# 模型来源与兼容要求

核对日期：2026-11-24。链接 main 可变；下载前重新核对 revision 和该机兼容性。精确字节数、SHA-256、直链和相对放置位置见 [models.json](../assets/models.json)。清单哈希来自基准文件，不代表未来同名文件必然相同。

## 来源

- [ComfyUI 官方发布](https://github.com/Comfy-Org/ComfyUI/releases)与[Windows Portable 说明](https://docs.comfy.org/installation/comfyui_portable_windows)。
- [MOSS 官方 ComfyUI 接入说明](https://github.com/OpenMOSS/MOSS-Image/blob/main/comfyui/README.md)。
- [ComfyUI 官方 MOSS 教程源文件](https://github.com/Comfy-Org/docs/blob/main/tutorials/image/moss-image/moss-image.mdx)。
- [原始 MOSS SFT 模型](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT)：了解架构与参考采样，不需同时下载整套 Diffusers 分片。
- [Comfy-Org/MOSS-Image](https://huggingface.co/Comfy-Org/MOSS-Image)：原生 ComfyUI 的单文件主模型、文本编码器、VAE。
- [原配社区 LoRA 作者说明](https://huggingface.co/axiomofmind/Moss-image-nsfw-uncensored-lora?not-for-all-audiences=true)：不是官方 OpenMOSS LoRA，带成人内容标记；用户确认后下载，遵守许可与同意要求，用普通穿着的成人虚构人物测试。

## 只下四项

| 文件 | ComfyUI 下的位置 | 约十进制大小 |
|---|---|---:|
| moss_image_sft_int8_convrot.safetensors | models/diffusion_models/ | 5.41GB |
| qwen3.5_9b_w4a8_gptq.safetensors | models/text_encoders/ | 7.34GB |
| flux2-vae_small.safetensors | models/vae/ | 250MB |
| MOSS-Image-NSFW-Uncensored-LoRA-ComfyUI.safetensors | models/loras/ | 74.4MB |

模型不是 SDXL checkpoint，不放 checkpoints。编码器类型是 moss_image。small VAE 不因文件名含 FLUX 就替换为普通 FLUX VAE；EmptyFlux2LatentImage 也不代表主模型是 FLUX。

不需要 Base、四步 LoRA、参考图编辑 LoRA、完整 BF16 编码器、GGUF 或额外融合插件。量化文件体积不是显存需求。35–50GB 空闲磁盘可作为安装与缓存工程余量的估计，不是官方最低值；结合实际下载包、已有缓存与 RAM 重算。

## 版本检查

核对日官方教程的文生图模板基线为 ComfyUI 0.42.0+，但后来的量化/LoRA 支持仍需真实验证。检查：

1. CLIPLoader 枚举有 moss_image，能加载 Qwen3.5 W4A8 GPTQ。
2. UNETLoader 能识别所选 MOSS INT8；不依赖 Diffusers 目录插件。
3. LoraLoaderModelOnly 能匹配 MOSS 的目标层，包括融合 Q/K/V；作者提到的加载器提交 b1a247c 或等价支持已包含。
4. 模板所需核心节点全部在本机后端 object_info 中。

原配 LoRA 默认 focused 1750 版的核对日哈希为 `4480E3154FD663D5EE6DAA86AC59D0FD211CE495B9DFEEDFB914C38F777F3A0B`。作者保留的 original-checkpoint-2000 是不同版本，不因同名混用。选 ComfyUI 版本，不把根目录 pytorch_lora_weights.safetensors 或 GGUF 随意当作等价文件。强度 0.8、触发词 moss_xvstyle 属于原配，不适用于所有人物 LoRA。

## 配置与权限

在 Windows NVIDIA Portable 安装根目录中可基于下列命令创建用户启动入口，先验证实际路径、参数和空闲端口；8189 仅示例，使用时保持一个明确入口。

```bat
@echo off
cd /d "%~dp0"
.\python_embeded\python.exe -s ComfyUI\main.py --windows-standalone-build --preview-method auto --lowvram --port 8189 --auto-launch
pause
```

依赖需要同步时，在授权范围内使用实际启动该后端的 Python 和对应 requirements，先评估会不会更换 PyTorch 或破坏旧插件。不要使用系统 pip，不照搬 Diffusers 的依赖升级。

官方 SFT 参考配方另有 50 步/Euler/负向第 22 层跳过；资产模板为 30 步 er_sde/simple、无显式 SLG 的简化配置。别把两者说成完全相同，也不在首次部署中暗改。模板固定 seed=42，面向连续出图的副本再明确改为 randomize/increment。

可通过浏览器下载、可信断点续传工具或现有下载能力获取文件。处理重定向、登录限制与限速，不关闭 TLS 校验，不向未知代理发送令牌。镜像须用户知情且有官方哈希对照。下载失败时给出缺失清单，不谎报完成。

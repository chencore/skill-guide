# Duix Avatar 安装与使用指南

> 开源 AI 数字人工具包 — 完全离线视频生成 + 真人形象克隆

## 项目简介

Duix Avatar 是由 Duix.com 开发的免费开源 AI 数字人项目，12,768 Stars，2,111 Forks。

七年前，一群年轻技术人选择了一条非传统的技术路径：用真人视频数据训练数字人模型。不同于传统昂贵的 3D 数字人方案，他们用 AI 生成技术创建超逼真的数字人，将制作成本从数十万美元降到仅 1,000 美元。

目前已赋能超过 10,000 家企业，为教育工作者、内容创作者、法律人士、医疗从业者、创业者等生成了超过 50 万个个性化数字人。

现在，他们将克隆技术和视频制作框架完全开源，任何人有一台电脑就可以免费制作自己的 AI Avatar，零成本生成视频。

---

## 核心特性

- **精准外观+声音克隆**：AI 算法高精度捕捉面部特征，构建逼真虚拟模型；声音克隆精确还原人声特质
- **文本/语音驱动虚拟数字人**：NLP 理解文本内容，文字转自然流畅语音驱动虚拟形象
- **高效视频合成**：数字人视频图像与声音高度同步，智能优化音视频同步效果
- **多语言支持**：英语、日语、韩语、中文、法语、德语、阿拉伯语、西班牙语

---

## 系统要求

### Windows 安装

| 配置项 | 要求 |
|--------|------|
| 系统 | Windows 10 19042.1526 或更高 |
| D 盘可用空间 | ≥ 30GB（存储数字人和项目数据） |
| C 盘可用空间 | ≥ 100GB（存储服务镜像文件） |
| 推荐 CPU | 13代 Intel Core i5-13400F |
| 推荐内存 | 32GB |
| 推荐显卡 | RTX 4070（需正确安装 NVIDIA 驱动） |

### Ubuntu 22.04 安装

| 配置项 | 要求 |
|--------|------|
| 系统 | Ubuntu 22.04（理论上支持所有桌面 Linux） |
| 内存 | ≥ 32GB（必须） |
| 显卡 | RTX 4070（需正确安装 NVIDIA 驱动） |
| 硬盘可用空间 | ≥ 100GB |

---

## 安装步骤

### 方式一：Windows 安装

#### 第一步：安装 WSL

打开 PowerShell，执行：

```powershell
wsl --list --verbose
```

如果未安装，执行：

```powershell
wsl --update
```

#### 第二步：安装 Docker Desktop

1. 前往 https://www.docker.com/ 下载 Windows 版 Docker
2. 根据 CPU 架构选择对应安装包
3. 安装成功后启动 Docker
4. 首次运行同意协议并跳过登录

#### 第三步：拉取 Docker 镜像

```powershell
docker pull guiji2025/fun-asr
docker pull guiji2025/fish-speech-ziming
docker pull guiji2025/duix.avatar
```

#### 第四步：部署服务

```powershell
cd deploy
docker-compose up -d
# 如需轻量版，执行：
docker-compose -f docker-compose-lite.yml up -d
```

> ⚠️ 首次部署需下载约 70GB 数据，请确保网络稳定，耐心等待约 30 分钟

#### 第五步：安装客户端

1. 前往 [Releases 页面](https://github.com/duixcom/Duix-Avatar/releases) 下载 Windows 安装包
2. 双击 `Duix.Avatar-x.x.x-setup.exe` 完成安装

---

### 方式二：Ubuntu 22.04 安装

#### 第一步：安装 Docker 和 Docker Compose

```bash
sudo apt update
sudo apt install docker.io
sudo apt install docker-compose
```

#### 第二步：安装 NVIDIA 显卡驱动

参考官方文档：https://www.nvidia.cn/drivers/lookup/

安装后执行 `nvidia-smi` 验证是否成功。

#### 第三步：安装 NVIDIA Container Toolkit

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
  && curl -s -L https://nvidia.github.io/libnvidia-container/gpgkey | sudo apt-key add - \
  && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

#### 第四步：部署服务

```bash
cd deploy
docker-compose -f docker-compose-linux.yml up -d
```

#### 第五步：安装客户端

1. 前往 [Releases 页面](https://github.com/duixcom/Duix-Avatar/releases) 下载 Linux 版 AppImage
2. 双击 `Duix.Avatar-x.x.x.AppImage` 启动（无需安装）

> ⚠️ Ubuntu 系统下以 root 用户登录时，直接双击 AppImage 可能无法运行，需在终端执行：
> `./Duix.Avatar-x.x.x.AppImage --no-sandbox`

---

## 使用方法

### 数字人克隆（模型训练）

1. 准备一段真人视频
2. 将视频分离为无声视频 + 音频
3. 将音频文件放入 `D:\duix_avatar_data\voice\data`
4. 调用模型训练接口（详见 API 文档）

### 视频生成

1. 打开 Duix Avatar 客户端
2. 选择已克隆的数字人模型
3. 输入文本或直接语音输入
4. 点击生成，等待视频合成完成
5. 导出视频文件

### API 调用

Docker 启动后，暴露以下本地端口，通过 `http://127.0.0.1` 访问：

| 服务 | 端口 | 说明 |
|------|------|------|
| 语音合成 | 18180 | 文本转语音 |
| 模型训练 | 参考源码 | 数字人训练 |

具体代码参考：
- `src/main/service/model.js` — 模型训练
- `src/main/service/video.js` — 视频合成
- `src/main/service/voice.js` — 语音克隆

---

## 关键优势

| 优势 | 说明 |
|------|------|
| **完全离线运行** | 无需网络连接，有效保护隐私，在安全独立环境中创作 |
| **用户友好** | 界面简洁直观，零技术背景也能快速上手 |
| **多模型支持** | 支持导入多个模型，一键切换管理 |

---

## 技术架构

- **声音克隆技术**：基于 AI 根据给定语音样本生成相似或相同声音
- **自动语音识别（ASR）**：将人类语音转换为计算机可读的文本格式
- **计算机视觉**：用于视频合成中的视觉处理，包括面部识别和唇动分析

---

## 常见问题

**Q: Windows 安装 Docker 失败？**
A: 确保系统版本 ≥ Windows 10 19042.1526，并正确安装 WSL

**Q: 磁盘空间不足？**
A: 如果 C 盘空间不足 100GB，可在 Docker 设置中更改镜像存储位置到其他磁盘

**Q: 50 系列显卡支持吗？**
A: 支持（同样适用于 30/40 系列 + CUDA 12.8），使用 PyTorch 官方预览版

**Q: 不想自己部署？**
A: 可以直接下载官方构建的安装包，无需自己搭建服务

---

## 相关资源

| 资源 | 链接 |
|------|------|
| GitHub 仓库 | https://github.com/duixcom/Duix-Avatar |
| 官方网站 | https://www.duix.com |
| Releases 下载 | https://github.com/duixcom/Duix-Avatar/releases |
| 官方演示 | https://hugohe3.github.io/ppt-master/ |

---

*本指南由 马龙 🏓 整理 | 2026-04-18*

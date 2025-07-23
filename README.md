# AuraLens Classroom Insight Assistant

**AuraLens** 是一款专为教育场景设计的开源多模态分析引擎。它能够将复杂的课堂录像转化为直观、深刻的教学洞察，成为教师的私人AI教学督导，赋能教师实现数据驱动的专业成长。

## 核心功能与亮点

* **多模态分析**:  
  * **视觉引擎**: 基于 **YOLOv8** 统一框架，实现高效的**多目标追踪**、**多人姿态估计**和**人脸检测**。结合 **MediaPipe** 进行精细化的**表情与手势分析**。  
  * **语音引擎**: 集成主流云服务API（如腾讯云、火山引擎）或OpenAI Whisper，实现高精度的**语音转写**和**多说话人识别**。  
* **深度教学洞察**:  
  * 通过大型语言模型（LLM）驱动，自动生成结构化的分析报告。  
  * **关键时刻识别**: 自动识别并标记课堂中的“高光时刻”（如精彩互动）和“关注时刻”（如普遍困惑）。  
  * **量化指标分析**: 提供学生专注度曲线、师生发言时长比、课堂情绪指数等数据驱动的反馈。  
* **专为开发者设计**:  
  * **异步架构**: 基于 **FastAPI** 和 **Celery** 构建，保证了高并发处理能力和系统响应性。  
  * **模块化设计**: 视觉、语音、融合、API等模块高度解耦，易于扩展和二次开发。  
  * **部署友好**: 提供完整的 **Docker & Docker Compose** 配置，实现一键部署。  
* **隐私与安全优先 (Privacy & Security First)**:  
  * 支持完全私有化部署，所有数据和分析过程均在您自己的服务器上完成，确保课堂数据的最高安全级别。

## **技术逻辑 (Technical Architecture)**

AuraLens 的核心是一个异步、事件驱动的分析管道。当用户上传视频后，任务被推送到Celery队列，由独立的Worker节点进行处理。整个流程清晰地分为预处理、并行分析、数据融合和报告生成四个阶段。


## 目录说明

项目采用模块化的结构，方便开发和维护。

/AuraLens  
├── /app                    \# 后端 FastAPI 应用  
│   ├── /api                \# API 路由定义  
│   ├── /core               \# 核心配置、中间件  
│   ├── /crud               \# 数据库增删改查操作  
│   ├── /db                 \# 数据库会话管理  
│   ├── /models             \# SQLAlchemy 数据模型  
│   ├── /schemas            \# Pydantic 数据模型  
│   └── main.py             \# FastAPI 应用入口  
├── /tasks                  \# Celery 异步任务  
│   ├── /analysis           \# 核心分析模块  
│   │   ├── /visual.py      \# 视觉分析管道  
│   │   ├── /audio.py       \# 语音分析管道  
│   │   └── /fusion.py      \# 多模态数据融合  
│   ├── /llm                \# LLM 报告生成  
│   └── worker.py           \# Celery worker 入口  
├── /web-ui               \# 前端 Web 应用 (React)  
│   └── ...  
├── docker-compose.yml      \# Docker Compose 部署文件  
├── Dockerfile              \# 后端应用 Dockerfile  
├── .env.example            \# 环境变量示例文件  
└── README.md               \# 项目说明

## 快速部署与使用

我们推荐使用 Docker 和 Docker Compose 进行一键部署，这能为您处理好所有复杂的环境依赖。

### **1\. 先决条件**

* [Docker](https://www.docker.com/products/docker-desktop/)  
* [Docker Compose](https://docs.docker.com/compose/install/)  
* NVIDIA GPU 及相应的 [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) (如果需要在GPU上运行)

### **2\. 安装与配置**

**第一步：克隆仓库**

git clone https://github.com/GitHubZhuMin/AuraLens.git  
cd AuraLens

第二步：配置环境变量  
复制环境变量示例文件，并根据您的实际情况填写。  
cp .env.example .env

您需要编辑 .env 文件，填入以下信息：

\# 数据库配置  
POSTGRES\_USER=your\_db\_user  
POSTGRES\_PASSWORD=your\_db\_password  
POSTGRES\_DB=auralens\_db

\# 语音云服务API密钥  
TENCENT\_SECRET\_ID=your\_tencent\_secret\_id  
TENCENT\_SECRET\_KEY=your\_tencent\_secret\_key

\# LLM API密钥  
OPENAI\_API\_KEY=your\_openai\_api\_key

### **3\. 运行应用**

使用 Docker Compose 一键启动所有服务（包括FastAPI后端、Celery Worker、PostgreSQL数据库和Redis）。

**使用GPU运行:**

docker-compose up \--build

*docker-compose.yml 文件已配置为默认使用NVIDIA GPU。*

仅使用CPU运行 (仅用于测试，性能较低):  
您需要修改 docker-compose.yml 中 celery-worker 服务的 deploy 部分，移除 gpu 相关的配置，然后运行 docker-compose up \--build。

### **4\. 访问服务**

* **后端API文档 (Swagger UI)**: http://localhost:8000/docs  
* **前端应用**: http://localhost:3000 (假设前端服务在3000端口)

现在，您可以通过API文档或前端界面，开始上传视频，体验AuraLens强大的多模态分析能力了！

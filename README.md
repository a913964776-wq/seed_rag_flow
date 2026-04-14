<div align="center">
<img src="web/src/assets/logo-with-text.svg" width="520" alt="logo">
</div>

<h4 align="center">
  <a href="#-系统简介">系统简介</a> |
  <a href="#-核心功能">核心功能</a> |
  <a href="#-系统架构">系统架构</a> |
  <a href="#-快速开始">快速开始</a>
</h4>

<details open>
<summary><b>📕 Table of Contents</b></summary>

- 💡 [What is RAGFlow?](#-what-is-ragflow)
- 🎮 [Demo](#-demo)
- 📌 [Latest Updates](#-latest-updates)
- 🌟 [Key Features](#-key-features)
- 🔎 [System Architecture](#-system-architecture)
- 🎬 [Get Started](#-get-started)
- 🔧 [Configurations](#-configurations)
- 🔧 [Build a Docker image](#-build-a-docker-image)
- 🔨 [Launch service from source for development](#-launch-service-from-source-for-development)
- 📚 [Documentation](#-documentation)
- 📜 [Roadmap](#-roadmap)
- 🏄 [Community](#-community)
- 🙌 [Contributing](#-contributing)

</details>

## 💡 What is RAGFlow?

本系统是一个基于检索增强生成（RAG）的智能文档处理与问答平台，融合深度学习文档解析技术与大语言模型能力，提供高质量的知识检索与智能问答服务。

## 🌟 Key Features

### 🍭 **"Quality in, quality out"**

- [Deep document understanding](./deepdoc/README.md)-based knowledge extraction from unstructured data with complicated
  formats.
- Finds "needle in a data haystack" of literally unlimited tokens.

### 🍱 **Template-based chunking**

- Intelligent and explainable.
- Plenty of template options to choose from.

### 🌱 **Grounded citations with reduced hallucinations**

- Visualization of text chunking to allow human intervention.
- Quick view of the key references and traceable citations to support grounded answers.

### 🍔 **Compatibility with heterogeneous data sources**

- Supports Word, slides, excel, txt, images, scanned copies, structured data, web pages, and more.

### 🛀 **Automated and effortless RAG workflow**

- Streamlined RAG orchestration catered to both personal and large businesses.
- Configurable LLMs as well as embedding models.
- Multiple recall paired with fused re-ranking.
- Intuitive APIs for seamless integration with business.

## 🔎 System Architecture

<div align="center" style="margin-top:20px;margin-bottom:20px;">
<!-- 待办：替换为内部架构图 -->
</div>

## 🎬 Get Started

### 📝 Prerequisites

- CPU >= 4 cores
- RAM >= 16 GB
- Disk >= 50 GB
- Docker >= 24.0.0 & Docker Compose >= v2.26.1
- gVisor: Required only if you intend to use the code executor (sandbox) feature.

> [!TIP]
> If you have not installed Docker on your local machine (Windows, Mac, or Linux), please refer to the official Docker documentation.

### 🚀 Start up the server

1. Ensure `vm.max_map_count` >= 262144:

   > To check the value of `vm.max_map_count`:
   >
   > ```bash
   > $ sysctl vm.max_map_count
   > ```
   >
   > Reset `vm.max_map_count` to a value at least 262144 if it is not.
   >
   > ```bash
   > # In this case, we set it to 262144:
   > $ sudo sysctl -w vm.max_map_count=262144
   > ```
   >
   > This change will be reset after a system reboot. To ensure your change remains permanent, add or update the
   > `vm.max_map_count` value in **/etc/sysctl.conf** accordingly:
   >
   > ```bash
   > vm.max_map_count=262144
   > ```
   >
2. 克隆代码仓库：

   ```bash
   $ git clone <内部仓库地址>
   ```
3. Start up the server using the pre-built Docker images:

> [!CAUTION]
> All Docker images are built for x86 platforms. We don't currently offer Docker images for ARM64.
> If you are on an ARM64 platform, please build a Docker image compatible with your system.

> The command below downloads the Docker image. See the following table for descriptions of different editions.

```bash
   $ cd ragflow/docker

   # git checkout v0.24.0
   # Optional: use a stable tag
   # This step ensures the **entrypoint.sh** file in the code matches the Docker image version.

   # Use CPU for DeepDoc tasks:
   $ docker compose -f docker-compose.yml up -d

   # To use GPU to accelerate DeepDoc tasks:
   # sed -i '1i DEVICE=gpu' .env
   # docker compose -f docker-compose.yml up -d
```

> Note: Prior to `v0.22.0`, we provided both images with embedding models and slim images without embedding models. Details as follows:

| RAGFlow image tag | Image size (GB) | Has embedding models? | Stable?        |
|-------------------|-----------------|-----------------------|----------------|
| v0.21.1           | &approx;9       | ✔️                    | Stable release |
| v0.21.1-slim      | &approx;2       | ❌                     | Stable release |

> Starting with `v0.22.0`, we ship only the slim edition and no longer append the **-slim** suffix to the image tag.

4. Check the server status after having the server up and running:

   ```bash
   $ docker logs -f docker-ragflow-cpu-1
   ```

   _The following output confirms a successful launch of the system:_

   ```bash

         ____   ___    ______ ______ __
        / __ \ /   |  / ____// ____// /____  _      __
       / /_/ // /| | / / __ / /_   / // __ \| | /| / /
      / _, _// ___ |/ /_/ // __/  / // /_/ /| |/ |/ /
     /_/ |_|/_/  |_|\____//_/    /_/ \____/ |__/|__/

    * Running on all addresses (0.0.0.0)
   ```

   > If you skip this confirmation step and directly log in to RAGFlow, your browser may prompt a `network abnormal`
   > error because, at that moment, your RAGFlow may not be fully initialized.
   >
5. In your web browser, enter the IP address of your server and log in to RAGFlow.

   > With the default settings, you only need to enter `http://IP_OF_YOUR_MACHINE` (**sans** port number) as the default
   > HTTP serving port `80` can be omitted when using the default configurations.
   >
6. In [service_conf.yaml.template](./docker/service_conf.yaml.template), select the desired LLM factory in `user_default_llm` and update
   the `API_KEY` field with the corresponding API key.

   > 请参考内部文档获取更多配置信息。
   >

   _The show is on!_

## 🔧 Configurations

When it comes to system configurations, you will need to manage the following files:

- [.env](./docker/.env): Keeps the fundamental setups for the system, such as `SVR_HTTP_PORT`, `MYSQL_PASSWORD`, and
  `MINIO_PASSWORD`.
- [service_conf.yaml.template](./docker/service_conf.yaml.template): Configures the back-end services. The environment variables in this file will be automatically populated when the Docker container starts. Any environment variables set within the Docker container will be available for use, allowing you to customize service behavior based on the deployment environment.
- [docker-compose.yml](./docker/docker-compose.yml): The system relies on [docker-compose.yml](./docker/docker-compose.yml) to start up.

> The [./docker/README](./docker/README.md) file provides a detailed description of the environment settings and service
> configurations which can be used as `${ENV_VARS}` in the [service_conf.yaml.template](./docker/service_conf.yaml.template) file.

To update the default HTTP serving port (80), go to [docker-compose.yml](./docker/docker-compose.yml) and change `80:80`
to `<YOUR_SERVING_PORT>:80`.

Updates to the above configurations require a reboot of all containers to take effect:

> ```bash
> $ docker compose -f docker-compose.yml up -d
> ```

### Switch doc engine from Elasticsearch to Infinity

系统默认使用 Elasticsearch 存储全文和向量数据。如需切换到 Infinity，请按以下步骤操作：

1. Stop all running containers:

   ```bash
   $ docker compose -f docker/docker-compose.yml down -v
   ```

> [!WARNING]
> `-v` will delete the docker container volumes, and the existing data will be cleared.

2. Set `DOC_ENGINE` in **docker/.env** to `infinity`.
3. Start the containers:

   ```bash
   $ docker compose -f docker-compose.yml up -d
   ```

> [!WARNING]
> Switching to Infinity on a Linux/arm64 machine is not yet officially supported.

## 🔧 Build a Docker image

This image is approximately 2 GB in size and relies on external LLM and embedding services.

```bash
git clone <内部仓库地址>
cd ragflow/
docker build --platform linux/amd64 -f Dockerfile -t ragflow:nightly .
```

Or if you are behind a proxy, you can pass proxy arguments:

```bash
docker build --platform linux/amd64 \
  --build-arg http_proxy=http://YOUR_PROXY:PORT \
  --build-arg https_proxy=http://YOUR_PROXY:PORT \
  -f Dockerfile -t ragflow:nightly .
```

## 🔨 Launch service from source for development

1. Install `uv` and `pre-commit`, or skip this step if they are already installed:

   ```bash
   pipx install uv pre-commit
   ```
2. 克隆代码仓库并安装 Python 依赖：

   ```bash
   git clone <内部仓库地址>
   cd ragflow/
   uv sync --python 3.12
   uv run python3 download_deps.py
   pre-commit install
   ```
3. Launch the dependent services (MinIO, Elasticsearch, Redis, and MySQL) using Docker Compose:

   ```bash
   docker compose -f docker/docker-compose-base.yml up -d
   ```

   Add the following line to `/etc/hosts` to resolve all hosts specified in **docker/.env** to `127.0.0.1`:

   ```
   127.0.0.1       es01 infinity mysql minio redis sandbox-executor-manager
   ```
4. 如需使用 HuggingFace 镜像站点，设置环境变量：

   ```bash
   export HF_ENDPOINT=https://hf-mirror.com
   ```
5. If your operating system does not have jemalloc, please install it as follows:

   ```bash
   # Ubuntu
   sudo apt-get install libjemalloc-dev
   # CentOS
   sudo yum install jemalloc
   # OpenSUSE
   sudo zypper install jemalloc
   # macOS
   sudo brew install jemalloc
   ```
6. Launch backend service:

   ```bash
   source .venv/bin/activate
   export PYTHONPATH=$(pwd)
   bash docker/launch_backend_service.sh
   ```
7. Install frontend dependencies:

   ```bash
   cd web
   npm install
   ```
8. Launch frontend service:

   ```bash
   npm run dev
   ```

   _The following output confirms a successful launch of the system:_

9. 停止前后端服务：

   ```bash
   pkill -f "ragflow_server.py|task_executor.py"
   ```

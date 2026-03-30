# 安装

## 目录
- [快速安装](#快速安装)
- [Docker设置](#docker设置)
- [Singularity设置（HPC）](#singularity设置hpc)
- [nf-core工具（可选）](#nf-core工具可选)
- [验证安装](#验证安装)
- [常见问题](#常见问题)

## 快速安装

```bash
# Nextflow
curl -s https://get.nextflow.io | bash
mv nextflow ~/bin/
export PATH="$HOME/bin:$PATH"

# 验证
nextflow -version
java -version  # 需要11+
```

## Docker设置

### Linux
```bash
sudo apt-get update && sudo apt-get install docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
# 注销后重新登录
```

### macOS
下载Docker Desktop：https://docker.com/products/docker-desktop

### 验证
```bash
docker run hello-world
```

## Singularity设置（HPC）

```bash
# Ubuntu/Debian
sudo apt-get install singularity-container

# 或通过conda安装
conda install -c conda-forge singularity
```

### 配置缓存
```bash
export NXF_SINGULARITY_CACHEDIR="$HOME/.singularity/cache"
mkdir -p $NXF_SINGULARITY_CACHEDIR
echo 'export NXF_SINGULARITY_CACHEDIR="$HOME/.singularity/cache"' >> ~/.bashrc
```

## nf-core工具（可选）

```bash
pip install nf-core
```

常用命令：
```bash
nf-core list                    # 可用流程列表
nf-core launch rnaseq           # 交互式参数选择
nf-core download rnaseq -r 3.14.0  # 下载以供离线使用
```

## 验证安装

```bash
nextflow run nf-core/demo -profile test,docker --outdir test_demo
ls test_demo/
```

## 常见问题

**Java版本错误：**
```bash
export JAVA_HOME=/path/to/java11
```

**Docker权限被拒绝：**
```bash
sudo usermod -aG docker $USER
# 注销后重新登录
```

**找不到Nextflow：**
```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

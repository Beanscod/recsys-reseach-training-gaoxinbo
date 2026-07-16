# 第三堂课演示步骤

## 一、网络检查与SSH登录

### 步骤1：测试服务器端口

在Windows PowerShell中执行：

```powershell
Test-NetConnection 222.198.156.91 -Port 22
```

检查点：确认`TcpTestSucceeded`为`True`。

### 步骤2：登录服务器

```powershell
ssh chenpengfan@222.198.156.91
```

首次连接时，核对服务器地址后输入：

```text
yes
```

学生将`chenpengfan`替换为自己的用户名，并输入自己的密码。

### 步骤3：确认登录位置

```bash
whoami
hostname
pwd
ls -lah
```

检查点：确认用户名、主机名和当前目录均来自远程服务器。

### 步骤4：创建个人课程目录

```bash
mkdir -p ~/research-training/lesson03
cd ~/research-training/lesson03
pwd
```

检查点：确认当前路径位于个人Home目录下的`research-training/lesson03`。

## 二、VS Code Remote SSH

### 步骤1：安装扩展

1. 打开VS Code扩展页面；
2. 搜索并安装`Remote - SSH`；
3. 打开命令面板；
4. 选择`Remote-SSH: Open SSH Configuration File`。

### 步骤2：添加服务器配置

```sshconfig
Host lab-server
    HostName 222.198.156.91
    User chenpengfan
    Port 22
```

将`User`替换为学生自己的用户名并保存。

### 步骤3：连接并打开课程目录

1. 执行`Remote-SSH: Connect to Host`；
2. 选择`lab-server`；
3. 打开`~/research-training/lesson03`；
4. 打开VS Code集成终端；
5. 执行：

```bash
pwd
```

检查点：确认VS Code左下角显示远程主机，终端路径为服务器路径。

## 三、Linux常用命令练习

### 步骤1：建立练习目录

```bash
mkdir -p ~/research-training/lesson03/linux_practice
cd ~/research-training/lesson03/linux_practice
pwd
ls -lah
```

检查点：确认当前位于`linux_practice`目录。

### 步骤2：创建并查看文本文件

```bash
touch notes.txt
printf "Linux command practice\nGPU environment check\nSASRec smoke demo\n" > notes.txt
ls -lah
cat notes.txt
head -n 2 notes.txt
tail -n 2 notes.txt
```

检查点：确认`notes.txt`存在，并能分别查看全部、开头和结尾内容。

### 步骤3：复制、移动和搜索文件

```bash
cp notes.txt notes_copy.txt
mv notes_copy.txt notes_backup.txt
grep -n "GPU" notes.txt
find . -maxdepth 1 -type f -print
```

检查点：确认目录中包含`notes.txt`和`notes_backup.txt`，并能定位包含`GPU`的行。

### 步骤4：查看磁盘、进程和程序位置

```bash
du -sh .
df -h ~
ps -u "$USER" -o pid,cmd --sort=pid | head
which bash
which python
```

检查点：确认能够看到练习目录大小、Home目录所在磁盘、个人进程和命令路径。

### 步骤5：创建并运行Shell脚本

```bash
printf '#!/usr/bin/env bash\necho "Hello from Linux"\n' > hello.sh
chmod u+x hello.sh
ls -l hello.sh
./hello.sh
```

检查点：确认`hello.sh`具有当前用户执行权限，并输出`Hello from Linux`。

### 步骤6：安全删除练习副本并查看历史

```bash
rm -i notes_backup.txt
history | tail -n 20
```

在删除确认提示中核对文件名后输入`y`。

检查点：确认只删除`notes_backup.txt`，`notes.txt`和`hello.sh`仍然存在。

## 四、安装Miniconda

### 步骤1：检查服务器架构

```bash
uname -m
```

检查点：确认服务器输出`x86_64`后再使用下面的安装包。

### 步骤2：下载安装包

```bash
cd ~
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
ls -lh Miniconda3-latest-Linux-x86_64.sh
```

如果没有`wget`，执行：

```bash
curl -O https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

检查点：确认安装包位于个人Home目录。

### 步骤3：执行安装

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

1. 按Enter阅读许可协议；
2. 输入`yes`接受协议；
3. 使用个人目录下的`~/miniconda3`作为安装路径；
4. 同意初始化Shell。

### 步骤4：初始化并检查Conda

```bash
~/miniconda3/bin/conda init bash
source ~/.bashrc
conda --version
conda env list
```

检查点：确认`conda --version`输出版本号，环境列表中包含`base`。

## 五、配置Conda与pip镜像

### 步骤1：检查并备份Conda配置

```bash
conda config --show-sources
test -f ~/.condarc && cp ~/.condarc ~/.condarc.lesson03.backup
```

### 步骤2：写入课程Conda配置

```bash
cat > ~/.condarc <<'EOF'
channels:
  - conda-forge
  - nodefaults
show_channel_urls: true
channel_priority: strict
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
EOF
```

### 步骤3：刷新并检查Conda配置

```bash
conda clean -i
conda config --show-sources
conda config --show channels
```

检查点：确认配置来源包含`~/.condarc`，频道包含`conda-forge`和`nodefaults`。

### 步骤4：配置并检查pip镜像

```bash
python -m pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
python -m pip config list
python -m pip config debug
```

检查点：确认`global.index-url`为TUNA的HTTPS地址。

## 六、创建lesson03环境并检查PyTorch与CUDA

### 步骤1：创建并激活环境

```bash
conda create -n lesson03 python=3.10 -y
conda activate lesson03
which python
python --version
python -m pip --version
```

检查点：确认Python路径位于`~/miniconda3/envs/lesson03/`，版本为3.10。

### 步骤2：检查服务器GPU

```bash
nvidia-smi
```

检查点：记录教师指定的空闲物理GPU编号，不结束其他用户的进程。

### 步骤3：安装课程指定的GPU版PyTorch

```bash
python -m pip install torch==2.12.1 --index-url https://download.pytorch.org/whl/cu126
```

### 步骤4：检查PyTorch与CUDA

```bash
python -c "import torch; print('PyTorch:', torch.__version__)"
python -c "import torch; print('PyTorch CUDA runtime:', torch.version.cuda)"
python -c "import torch; print('CUDA available:', torch.cuda.is_available())"
python -c "import torch; print('GPU:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'CPU only')"
```

检查点：确认PyTorch为2.12.1、CUDA运行时为12.6、CUDA可用且GPU型号正确。

## 七、本地CPU备用环境

### 步骤1：在Windows创建环境

在Miniconda Prompt或PowerShell中执行：

```powershell
conda create -n lesson03 python=3.10 -y
conda activate lesson03
python -m pip install torch==2.12.1 --index-url https://download.pytorch.org/whl/cpu
where.exe python
python --version
python -m pip --version
```

### 步骤2：检查CPU版PyTorch

```powershell
python -c "import torch; print(torch.__version__)"
python -c "import torch; print(torch.cuda.is_available())"
```

检查点：确认PyTorch能够导入，`torch.cuda.is_available()`输出`False`。

## 八、AutoDL操作

### 步骤1：创建实例

1. 登录AutoDL；
2. 进入算力市场或实例创建页面；
3. 查看地区、GPU型号、显存、价格和空闲状态；
4. 选择满足课程需要的GPU；
5. 选择带完整PyTorch环境的官方镜像；
6. 核对系统盘、数据盘和计费方式；
7. 创建实例并开机。

### 步骤2：检查实例环境

在实例终端中执行：

```bash
nvidia-smi
python -c "import torch; print(torch.__version__, torch.cuda.is_available())"
pwd
df -h
```

检查点：确认GPU、PyTorch、当前目录和可用磁盘空间符合预期。

### 步骤3：保存结果并关机

1. 保存需要保留的代码、日志和模型；
2. 返回实例列表；
3. 执行关机；
4. 确认实例状态与计费状态。

检查点：确认实例不再运行，重要文件不只保存在临时系统盘。

## 九、NAS操作

### 步骤1：登录NAS

1. 在校园网环境访问`http://10.242.0.50`；
2. 使用个人NAS账号登录；
3. 进入文件管理页面；
4. 打开`SharedFolder`；
5. 找到第三课课程材料目录。

检查点：确认使用个人账号，并且只浏览课程材料目录。

### 步骤2：下载课程代码

1. 找到`sasrec_smoke_demo`课程包；
2. 下载到服务器个人目录；
3. 解压到`~/research-training/lesson03/`；
4. 检查目录：

```bash
cd ~/research-training/lesson03/sasrec_smoke_demo
pwd
find . -maxdepth 2 -type f -print
```

检查点：确认代码和数据位于个人目录，不修改NAS公共文件。

### 步骤3：退出NAS

1. 关闭课程材料页面；
2. 退出个人NAS账号。

检查点：确认页面已回到登录状态。

## 十、Token安全操作

### 步骤1：在非投屏终端中隐藏输入Token

```bash
read -s LAB_AGENT_TOKEN
export LAB_AGENT_TOKEN
```

输入个人Token后按Enter，终端不显示输入内容。

### 步骤2：只检查Token是否已加载

```bash
python -c "import os; print('Token loaded:', bool(os.getenv('LAB_AGENT_TOKEN')))"
```

检查点：确认只输出`Token loaded: True`，不输出Token内容。

### 步骤3：清除当前终端中的Token

```bash
unset LAB_AGENT_TOKEN
python -c "import os; print('Token loaded:', bool(os.getenv('LAB_AGENT_TOKEN')))"
```

检查点：确认输出`Token loaded: False`。

## 十一、SASRec Smoke实验

### 步骤1：进入代码目录并检查文件

```bash
cd ~/research-training/lesson03/sasrec_smoke_demo
pwd
find . -maxdepth 2 -type f -print
```

检查点：确认目录中包含`sasrec_demo.py`、`generate_smoke_data.py`、`README.md`、数据文件和测试文件。

### 步骤2：检查固定Smoke数据

```bash
head -n 6 data/smoke_interactions.csv
wc -l data/smoke_interactions.csv
```

检查点：确认表头为`user_id,item_id,timestamp`，总行数为3073行。

### 步骤3：验证数据可以确定性重建

```bash
python generate_smoke_data.py --output /tmp/smoke_interactions_regenerated.csv
cmp data/smoke_interactions.csv /tmp/smoke_interactions_regenerated.csv
```

检查点：确认`cmp`没有输出，表示两个文件逐字节一致。

### 步骤4：运行自动测试

```bash
conda activate lesson03
python -m unittest discover -s tests -v
```

检查点：确认数据、模型、因果掩码和CPU训练测试全部通过。

### 步骤5：检查运行环境

教师先根据实时GPU状态指定物理GPU编号。以物理GPU 0为例：

```bash
nvidia-smi
CUDA_VISIBLE_DEVICES=0 python sasrec_demo.py --device cuda --check-only
```

检查点：确认程序选择`cuda:0`，并打印GPU名称、PyTorch版本和CUDA运行时。

### 步骤6：持续观察GPU

在第一个终端中执行：

```bash
watch -n 1 nvidia-smi
```

检查点：确认观察的是教师指定的物理GPU，不结束或暂停其他用户的进程。

### 步骤7：运行SASRec训练

在第二个终端中执行：

```bash
cd ~/research-training/lesson03/sasrec_smoke_demo
conda activate lesson03
CUDA_VISIBLE_DEVICES=0 python sasrec_demo.py \
  --device cuda \
  --epochs 10 \
  --batch-size 32 \
  --max-len 20 \
  --hidden-size 64 \
  --num-heads 2 \
  --num-blocks 2 \
  --allocator-limit-mib 700 \
  --gpu-budget-mib 1024
```

检查点：确认日志持续输出训练损失、验证集指标、测试集指标和显存采样结果。

### 步骤8：查看本次运行输出

```bash
RUN_DIR=$(ls -dt outputs/run_* | head -n 1)
echo "$RUN_DIR"
ls -lh "$RUN_DIR"
tail -n 20 "$RUN_DIR/train.log"
cat "$RUN_DIR/summary.json"
```

检查点：确认运行目录中包含`train.log`、`metrics.jsonl`、`summary.json`和`best_model.pt`，实际进程显存不超过1024 MiB。

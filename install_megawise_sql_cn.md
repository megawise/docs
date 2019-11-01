
# 安装 MegaWise

本文档主要介绍 MegaWise Docker 的安装和配置等操作。

- [**安装前提**](#安装前提)
  - [**安装 NVIDIA 驱动**](#安装-NVIDIA-驱动)
  - [**安装 Docker**](#安装-Docker)
  - [**安装 NVIDIA container toolkit**](#安装-NVIDIA-container-toolkit)
- [**安装 MegaWise**](#安装-MegaWise)
  - [**自动安装 MegaWise 并导入示例数据**](#自动安装-MegaWise-并导入示例数据)
  - [**手动安装 MegaWise**](#手动安装-MegaWise)



## 安装前提

### 硬件要求

| 组件                     | 配置                    |
|--------------------------|-------------------------|
| GPU |  NVIDIA Pascal 或以上            |
| CPU                 |Intel CPU Sandy Bridge 四核 1.8 GHz 或以上|
| 内存         | 16 GB 或以上           |
| 硬盘                  | 1 TB 或以上         |


### 软件要求

| 组件                     | 版本                    |
|--------------------------|-------------------------|
| 操作系统                 | Ubuntu 16.04 或以上 |
| NVIDIA 驱动          | 410 或以上，推荐最新版本          |
| Docker                   | 19.03 或以上         |
| NVIDIA Container Toolkit |  1.0.5-1 或以上            |


### 安装 NVIDIA 驱动


1. 禁用 Nouveau 驱动。

   安装 NVIDIA 驱动之前必须先禁用 Nouveau 驱动。请通过以下命令检查是否已启用 Nouveau 驱动：

   ```bash
   $ lsmod | grep nouveau  
   ```

   该命令执行后，如果终端打印了相关信息则说明 Nouveau 驱动已经被启用。如果启用了 Nouveau 驱动，则需执行后续的步骤将其禁用，否则请跳过以下步骤，开始安装 NVIDIA 驱动。

   1. 在以下路径创建文件 `/etc/modprobe.d/blacklist-nouveau.conf` 并在文件中写入如下内容：

      ```
      blacklist nouveau
      options nouveau modeset=0  
      ```

   2. 执行以下命令并重启系统：

      ```bash
      $ sudo update-initramfs -u
      $ sudo reboot  
      ```

   3. 确认禁用 Nouveau 驱动，执行该命令将不输出任何信息。

      ```bash
      $ lsmod | grep nouveau
      ```
   
      如果系统中未安装 lsmod 工具，则先安装 lsmod, 然后执行上述命令。

      ```bash
      $ sudo apt-get install lsmod
      ```

2. 从 [NVIDIA官方驱动下载链接](https://www.nvidia.com/Download/index.aspx?lang=en-us) 下载最新版本的驱动安装文件。

   > <font color='red'>注意：安装或更新 NVIDIA 驱动存在一定风险，有可能导致显示系统崩溃。在安装或更新 NVIDIA 驱动前，请在[NVIDIA官方驱动下载链接](https://www.nvidia.com/Download/index.aspx?lang=en-us)检查您的显卡是否适用最新版本的 NVIDIA 驱动。</font>

3. 安装NVIDIA驱动需要先关闭图形界面， 按 Ctrl+Alt+F1 进入命令行界面，并执行以下命令关闭图形界面。

   ```bash
   $ sudo service lightdm stop
   ```
   
4. 如果您已经安装 NVIDIA 驱动软件，请删除旧的驱动软件。

   ```bash
   $ sudo apt-get remove nvidia-*
   ```
   
5. 赋予安装文件执行权限并安装驱动软件。下面的示例假设安装文件下载在`/home`目录下。

   ```bash
   $ sudo chmod a+x NVIDIA-Linux-x86_64-430.50.run
   $ sudo ./NVIDIA-Linux-x86_64-430.50.run
   ```

6. 重启系统。

   ```bash
   $ sudo reboot  
   ```

7. 测试是否安装成功。

   ```bash
   $ sudo nvidia-smi  
   ```

   如果安装正确，终端打印的内容将包含类似如下信息：

   ```
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 430.34       Driver Version: 430.34       CUDA Version: 10.1     |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |===============================+======================+======================|
   |   0  GeForce GTX 1660    Off  | 00000000:01:00.0  On |                  N/A |
   | 28%   49C    P0    24W / 130W |   2731MiB /  5941MiB |      1%      Default |
   +-------------------------------+----------------------+----------------------+
   ```
### 安装 Docker

1. 更新源。

   ```bash
   $ sudo apt-get update
   ```

2. 使用 curl 工具下载最新版本的 Docker。

   ```bash
   $ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   Add Docker to your Apt repository.
   $ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   ```

   如果系统中未安装 curl 工具，则先安装 curl, 然后执行上述命令。

   ```bash
   $ sudo apt-get install curl
   ```

3. 更新 apt-get 仓库。

   ```bash
   $ sudo apt-get update
   ```

4. 安装 Docker 及其相关的命令行接口和 runtime 环境。

   ```bash
   $ sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

5. 重新执行以下命令验证 Docker 是否安装成功。如果能够打印 Docker 的版本信息，则说明已成功安装 Docker。

   ```bash
   $ sudo docker -v
   ```

### 安装 NVIDIA container toolkit

1. 使用 curl 添加 gpg key。

   ```bash
   $ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
   sudo apt-key add -
   distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
   ```

2. 更新下载源。

   ```bash
   $ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
   sudo tee /etc/apt/sources.list.d/nvidia-docker.list
   ```

3. 安装 NVIDIA runtime。

   ```bash
   $ sudo apt-get update
   $ sudo apt-get install -y nvidia-container-toolkit
   ```

4. 重启 Docker daemon。

   ```bash
   $ sudo systemctl restart docker
   ```

5. 验证 NVIDIA container toolkit 是否安装成功。

   ```bash
   $ sudo docker run --gpus all nvidia/cuda:9.0-base nvidia-smi
   ```


如果能够成功打印服务器 GPU 信息，则表示安装成功。


## 自动安装 MegaWise 并导入示例数据

1. 请确保当前用户对目录 `/tmp` 有读写权限。

2. 下载脚本 `install_megawise.sh` 和 `data_import.sh` 至同一目录，并确保当前用户对两个脚本有可执行权限。

   ```bash
   $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/script/data_import.sh \
   https://raw.githubusercontent.com/Infini-Analytics/infini/master/script/install_megawise.sh
   $ chmod a+x *.sh
   ```
   
3. 安装 MegaWise 并导入示例数据。

   ```bash
   $ ./install_megawise.sh [参数1，必选] [参数2，可选]
   ```

   > 参数1：MegaWise 安装目录的绝对路径，请确保该目录不存在
   
   > 参数2：MegaWise 镜像id，可选，默认'0.4.2'
   
   示例：
   
   ```bash
   $ ./install_megawise.sh  /home/$USER/megawise '0.4.2'
   ```
   
   该语句所执行的操作如下：

     1. 拉取 MegaWise Docker 镜像。
     2. 下载配置文件和示例数据。
     3. 启动 MegaWise。
     4. 准备示例数据并导入 MegaWise。
     5. 修改相关配置参数，重启 MegaWise 服务。

若出现 `Successfully installed MegaWise and imported test data` 则表示 MegaWise 成功安装且示例数据已导入。

## 手动安装 MegaWise

1. 在 [docker hub](https://hub.docker.com/r/zilliz/megawise/tags) 查询最新的 MegaWise 版本号。

2. 执行以下命令获得最新版本的 MegaWise 的 docker 镜像。

    ```bash
    $ sudo docker pull zilliz/megawise:$LATEST_VERSION
    ```

3. 安装 PostgreSQL 客户端。

    ```bash
    $ sudo apt-get install curl ca-certificates
    $ curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    $ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    $ sudo apt-get update
    $ sudo apt-get install postgresql-client-11
    ```

    PostgreSQL 客户端默认安装路径是/usr/lib/postgresql/11/bin/，安装完成后执行 `which psql` 命令，如果没有输出 PostgreSQL 客户端程序的正确路径，需要手动将安装路径加到环境变量中。

    ```bash
    $ export PATH=/usr/lib/postgresql/11/bin:$PATH
    ```

4. 新建一个目录作为工作目录。

    ```bash
    $ cd $WORK_DIR
    $ mkdir conf
    ```

5. 获取 MegaWise 配置文件。

    ```bash
    $ cd $WORK_DIR/conf
    $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/chewie_main.yaml
    $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/etcd.yaml
    $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/megawise_config_template.yaml
    $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/render_engine.yaml
    ```

6. 根据 MegaWise 所在的服务器环境修改配置文件。

   1. 打开 `conf` 目录下面的 `chewie_main.yaml` 配置文件，找到如下片段：

      ```yaml
      cache:  # size in GB
        cpu:
            physical_memory: 16
            partition_memory: 16
      
        gpu:
            gpu_num: 2
            physical_memory: 2
            partition_memory: 2 
      ```

      根据服务器的硬件配置，对上述的配置项进行设置（数值单位为 GB ）。

      `cpu` 部分，`physical_memory` 和 `partition_memory`分别表示 MegaWise 可用的内存总容量和数据缓存分区的内存容量。建议将 `partition_memory` 和 `physical_memory` 均设置为服务器物理内存总量的70%以上；
    
      `gpu` 部分，`gpu_num` 表示当前 MegaWise 使用的 GPU 数量，`physical_memory` 和 `partition_memory` 分别表示 MegaWise 可用的显存总容量和数据缓存分区的显存容量。建议预留 2GB 显存用于存储计算过程中的中间结果，即将 `partition_memory` 和 `physical_memory` 均设置为单张显卡显存容量的值减2。
   
    2. 打开 `conf` 目录下面的 `megawise_config_template.yaml` 配置文件。
    
        1. 定位到如下片段并设置相关参数。

            ```yaml
              worker_config:
                bitcode_lib: @bitcode_lib@
                precompile: true
                stage:
                    build_task_context_parallelism: 1
                    fetch_meta_parallelism: 1
                    compile_parallelism: 1
                    fetch_data_parallelism: 1
                    compute_parallelism: 1
                    output_parallelism: 1
                worker_num : 2
                gpu:
                    physical_memory: 2    # unit: GB
                    partition_memory: 2   # unit: GB
                cuda_profile_query_cnt: -1 #-1 means don't profile, positive integer means the number of queries to profile, other value invalid
            ```  

            依据下表设置以下参数的值：

            | 参数                     | 值                   |
            |--------------------------|-------------------------|
            | `worker_num` |  `chewie_main.yaml` 中的 `gpu_num` 值            |
            | `physical_memory` |  `chewie_main.yaml` 中的 `physical_memory` 值            |
            | `partition_memory` |  `chewie_main.yaml` 中的 `partition_memory` 值           |

        2. 定位到如下片段并设置相关参数。

            ```yaml
              string_config:
                dict_config:
                  cache_size: 21474836480  # 20G
                  split_threshold: 1000000
                  split_each: 100000
                  small_scale_num: 4000    # try not to use the temporary id
                hash_config:
                  cache_size: 21474836480  # 20G
                  bucket_num: 1999993      # prime number is a good choice
                  bucket_size: 500         # make sure that each string is shorter than bucket_size-5
                  file_size: 104857600     # 100M
            ```

            `dict_config`中的`cache_size`表示用于字符串字典编码的内存总量，单位为字节。

            `hash_config`中的`cache_size`表示用于字符串哈希编码的内存总量，单位为字节。


7. 启动 MegaWise。

    ```bash
    sudo docker run --gpus all --shm-size 17179869184 \ 
                            -v $WORK_DIR/conf:/megawise/conf \ 
                            -v $WORK_DIR/data:/megawise/data \ 
                            -v $WORK_DIR/server_data:/megawise/server_data \ 
                            -v /tmp:/tmp \ 
                            -v /home/$USER/.nv:/home/megawise/.nv
                            -p 5433:5432 \ 
                            $IMAGE_ID
    ```

    参数说明

    > `--shm-size`

      Docker image 运行时系统分配的内存大小，改值取 `chewie_main.yaml` 配置文件中 `cache` 配置项下的 `cpu` 配置项的 `physical_memory` 的值，单位为字节

    > `-v`

      宿主机和 image 之间的目录映射，用 `:` 隔开，前面是宿主机的目录，后面是 Docker image 的目录。

      在启动容器时可以通过 `-v` 将本地存储的数据文件映射到容器内，以实现本地文件导入 MegaWise 数据库。

    > `-p`

      宿主机和 image 之间的端口映射，用 `:` 隔开，前面是宿主机的端口，后面是 Docker image 的端口，宿主机的端口可以随意设置未被占用的端口，本教程设置为5433。

    容器启动后，将会启动日志，如果能找到如下日志内容，则说明 MegaWise server 已经启动成功。

    ```bash
    MegaWise server is running...
    ```

8. 操作 MegaWise。
  
    ```bash
    $ psql -U zilliz -p 5433 -h $IP_ADDR -d postgres
    ```

    MegaWise 的 docker 启动后会内置一个默认数据库 `postgres` ，在该数据库上会创建一个默认用户 `zilliz` ，接下来会提示输入密码，默认 `zilliz` 。
    如果出现以下信息：

    ```bash
    psql (11.1)
    Type "help" for help.

    postgres=>
    ```

    就说明成功连接上 MegaWise 了。

    >注意：如果连接超时，建议检查防火墙设置是否正确。

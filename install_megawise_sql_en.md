# Install MegaWise


This document introduces how to install and configure MegaWise Docker.

- [**Prerequisites**](#Prerequisites)
  - [**Install NVIDIA driver**](#Install-NVIDIA-driver)
  - [**Install Docker**](#Install-Docker)
  - [**Install NVIDIA container toolkit**](#Install-NVIDIA-container-toolkit)
- [**Install MegaWise**](#Install-MegaWise)
  - [**Automatically install**](#Automatically-install-MegaWise-and-import-sample-data)
  - [**Manually install**](#Manually-install-MegaWise)

## Prerequisites

### Hardware requirements


| Component                     | Configuration                   |
|--------------------------|-------------------------|
| GPU |  NVIDIA Pascal or higher            |
| CPU                 |Intel CPU Sandy Bridge or higher|
| RAM         | 16 GB or higher           |
| Hard disk                  | 1 TB or higher         |
|  |   |

### Software requirements


| Component                     | Version                    |
|--------------------------|-------------------------|
| Operating system                 | Ubuntu 16.04 or higher |
| NVIDIA driver          | 410 or higher. The latest version is recommended.          |
| Docker                   | 19.03 or higher         |
| NVIDIA Container Toolkit |  1.0.5-1 or higher            |
|  |   |

### Install NVIDIA driver

1. Disable the Nouveau driver.

   You must disable the Nouveau driver before installing the NVIDIA driver. Use the following command to check whether the Nouveau driver is enabled.

   ```bash
   $ lsmod | grep nouveau  
   ```

   If the command returns any information about the Nouveau driver, you need to complete the following steps to disable the Nouveau driver:

    1. Create the file `/etc/modprobe.d/blacklist-nouveau.conf` and add the following content:

        ```
        blacklist nouveau
        options nouveau modeset=0  
        ```

    2. Run the following command and reboot:

        ```bash
        $ sudo update-initramfs -u
        $ sudo reboot  
        ```

    3. Confirm the Nouveau driver is disabled. The terminal does not return any information if the Nouveau driver is disabled.

        ```bash
        $ lsmod | grep nouveau
        ```
        
        If lsmod is not installed, you need to install lsmod before running the previous command.

        ```bash
        $ sudo apt-get install lsmod
        ```

2. Download the latest NVIDIA driver installation file from [NVIDIA driver download page](https://www.nvidia.com/Download/index.aspx?lang=en-us).

   > <font color='red'>Note: Installing or updating NVIDIA drivers comes with certain risks and may cause operating system crash. Please check whether your graphics card is compatible with the latest NVIDIA driver by visiting the [NVIDIA driver download page](https://www.nvidia.com/Download/index.aspx?lang=en-us) in advance.</font>

3. You must shut down the GUI before installing the NVIDIA driver. Press Ctrl+Alt+F1 to enter the CLI and run the following command to shut down the GUI:

   ```bash
   $ sudo service lightdm stop
   ```
   
4. If you already have an NVIDIA driver installed, please remove the installed driver before installing a new one.

   ```bash
   $ sudo apt-get remove nvidia-*
   ```
   
5. Give execute permission to the installation file and install the driver software. The following example assumes the installation file is downloaded to the `/home` directory.

   ```bash
   $ sudo chmod a+x NVIDIA-Linux-x86_64-430.50.run
   $ sudo ./NVIDIA-Linux-x86_64-430.50.run
   ```

6. Restart the operating system.

   ```bash
   $ sudo reboot  
   ```

7. Check whether the installation is successful.

   ```bash
   $ sudo nvidia-smi  
   ```

   If the installation is successful, the terminal will return driver information which is similar to the following example:

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
### Install Docker

1. Update the package lists.

   ```bash
   $ sudo apt-get update
   ```

2. Use curl to download the latest Docker.

   ```bash
   $ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   $ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   ```
   
   If curl is not installed, you need to install curl before running the previous command.
   
   ```bash
   $ sudo apt-get install curl
   ```
   
3. Update the apt-get repository.

   ```bash
   $ sudo apt-get update
   ```

4. Install Docker with the corresponding command-line interface and runtime environment.

   ```bash
   $ sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

5. Run the following command again to check whether Docker is successfully installed. If the terminal returns version information about Docker, you can assume that Docker is successfully installed.

   ```bash
   $ sudo docker -v
   ```

### Install NVIDIA container toolkit

1. Use curl to add gpg key.

   ```bash
   $ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
   sudo apt-key add -
   $ distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
   ```

2. Update the package version to download.

   ```bash
   $ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
   sudo tee /etc/apt/sources.list.d/nvidia-docker.list
   ```

3. Install NVIDIA runtime.

   ```bash
   $ sudo apt-get update
   $ sudo apt-get install -y nvidia-container-toolkit
   ```

4. Restart Docker daemon.

   ```bash
   $ sudo systemctl restart docker
   ```

5. Validate whether NVIDIA container toolkit is successfully installed.

   ```bash
   $ sudo docker run --gpus all nvidia/cuda:9.0-base nvidia-smi
   ```

If the terminal returns version information about the GPU, you can assume that the NVIDIA container toolkit is successfully installed.


## Automatically install MegaWise and import sample data

1. Make sure you have read/write access to the `/tmp` directory.

2. Download `install_megawise.sh` and `data_import.sh` to the same directory and make sure that you have execution access.

   ```bash
   $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/script/data_import.sh \
   https://raw.githubusercontent.com/Infini-Analytics/infini/master/script/install_megawise.sh
   $ chmod a+x *.sh
   ```
   
3. Install MegaWise and import sample data.

   ```bash
   $ ./install_megawise.sh [parameter 1，required] [parameter 2，optional]
   ```

   > parameter 1：Absolute path of the installation folder of MegaWise. You must make sure that this folder does not exist.
   
   > parameter 2：MegaWise Docker image id. The default value is '0.4.2'.
   
   Example:
   
   ```bash
   $ ./install_megawise.sh  /home/$USER/megawise '0.4.2'
   ```
   
   The previous command performs the following operations:

     1. Pull MegaWise Docker image.
     2. Download config files and sample data.
     3. Launch MegaWise.
     4. Import sample data to MegaWise.
     5. Modify parameters to restart MegaWise.

If the terminal displays `Successfully installed MegaWise and imported test data`, you can assume that MegaWise is successfully installed and sample data is imported.

## Manually install MegaWise

1. Check the latest version number in [docker hub](https://hub.docker.com/r/zilliz/megawise/tags).

2. Get the latest docker image of MegaWise.

    ```bash
    $ sudo docker pull zilliz/megawise:$LATEST_VERSION
    ```

3. Install PostgreSQL client.

    ```bash
    $ sudo apt-get install curl ca-certificates
    $ curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    $ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    $ sudo apt-get update
    $ sudo apt-get install postgresql-client-11
    ```

    PostgreSQL client is installed to /usr/lib/postgresql/11/bin/ by default. After installation, run `which psql`. If the terminal does not return the correct location of the PostgreSQL client, please add the installation path to the environment variable.

    ```bash
    $ export PATH=/usr/lib/postgresql/11/bin:$PATH
    ```

4. Create a new folder as the working folder.

    ```bash
    $ cd $WORK_DIR
    $ mkdir conf
    ```

5. Get MegaWise config files.

    ```bash
    $ cd $WORK_DIR/conf
    $ wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/chewie_main.yaml \
    wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/etcd.yaml \
    wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/megawise_config_template.yaml \
    wget https://raw.githubusercontent.com/Infini-Analytics/infini/master/config/db/render_engine.yaml
    ```

6. Modify config files based on the hardware environment of MegaWise.

   1. Open `chewie_main.yaml` in the `conf` directory and navigate to the following code:

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

      Configure the parameters based on the hardware environment of the server (The numbers are in GBs).

      For the `cpu` part, `physical_memory` and `partition_memory` respectively represents the available memory size for MegaWise and the memory size for the data cache partition. It is recommended that you set both `partition_memory` and `physical_memory` to more than 70 percent of the server memory.
   
      For the `gpu` part, `gpu_num` represents the number of GPUs used by MegaWise. `physical_memory` and `partition_memory` respectively represents the available video memory size for MegaWise and the video memory size for the data cache partition. It is recommended that you reserve 2 GB of video memory to store the intermediate results during computation by setting `partition_memory` and `physical_memory` to a value that equals the video memory of a single GPU minus 2.
   
    2. Open `megawise_config_template.yaml` in the `conf` directory.
   
        1. Navigate to the following code and set parameter values:

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

            Set the values of some parameters per the following table:

            | Parameter                    | Value                   |
            |--------------------------|-------------------------|
            | `worker_num` | The value of `gpu_num` in `chewie_main.yaml`           |
            | `physical_memory` |   The value of `physical_memory` in `chewie_main.yaml`          |
            | `partition_memory` |   The value of `partition_memory` in `chewie_main.yaml`        |

        2. Navigate to the following code and set parameter values:

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

            `cache_size` in `dict_config` represents the memory size for encoding string dictionaries in bytes. 

            `cache_size` in `hash_config` represents the memory size for encoding string hashes in bytes.


7. Run MegaWise.

    ```bash
    sudo docker run --gpus all --shm-size 17179869184 \
                            -v $WORK_DIR/conf:/megawise/conf \
                            -v $WORK_DIR/data:/megawise/data \
                            -v $WORK_DIR/server_data:/megawise/server_data \
                            -v /tmp:/tmp \
                            -v /home/$USER/.nv:/home/megawise/.nv \
                            -p 5433:5432 \
                            $IMAGE_ID
    ```

    Parameter description

    > `--shm-size`

      The allocated memory size for a running Docker image in bytes. Use the value in the `physical_memory` parameter under `cpu`->`cache` in `chewie_main.yaml`.

    > `-v`

      Directory mapping between the host and the Docker image. Separated by `:`, the former part is the directory of the host and the latter part is the directory of the Docker image.

      When launching the container, you can use `-v` to map local data files to the container to import local files to the MegaWise database.

    > `-p`

      Port mapping between the host and the Docker image. Separated by `:`, the former part is the port of the host and the latter part is the port of the Docker image. You can set the host to use any unoccupied port. In this tutorial, we use 5433.

    Logging starts when the container starts running. If you can find the following content in the log, you can assume the MegaWise server is successfully running.

    ```bash
    MegaWise server is running...
    ```

8. Use MegaWise.
  
    ```bash
    $ psql -U zilliz -p 5433 -h $IP_ADDR -d postgres
    ```

    MegaWise Docker creates a built-in database `postgres` after launch. A default user `zilliz` is created in the database. You will then be prompted to enter the password. The default password is `zilliz` .
    
    If the terminal displays the following information, you can assume that the connection to MegaWise is successful.

    ```bash
    psql (11.1)
    Type "help" for help.

    postgres=>
    ```

    >Note：If the connection timeouts, check whether the firewall settings are correct.

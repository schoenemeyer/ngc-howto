# ngc-howto

browse to NGC
https://ngc.nvidia.com/catalog/containers/nvidia:k8s:cuda-sample/tags

(Store your API key)


nvidia-docker run -it -v `pwd`:`pwd` -w `pwd` nvcr.io/nvidia/k8s/cuda-sample:nbody


get Dockerfile from 
https://hub.docker.com/r/nvidia/cuda/

e.g. 
https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.1/base/Dockerfile

This is the base image.

```
docker build -t cuda9.1 .
```

```
setting up cuda-cudart-9-1 (9.1.85-1) ...
Processing triggers for libc-bin (2.23-0ubuntu11) ...
Removing intermediate container d1b9899f1b32
 ---> 016a00b8f520
Step 7/14 : LABEL com.nvidia.volumes.needed="nvidia_driver"
 ---> Running in 538d83a53428
Removing intermediate container 538d83a53428
 ---> c6324bf5292d
Step 8/14 : LABEL com.nvidia.cuda.version="${CUDA_VERSION}"
 ---> Running in 395ffd117db8
Removing intermediate container 395ffd117db8
 ---> 9ef6e480281c
Step 9/14 : RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf &&     echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf
 ---> Running in 4cd699fed922
Removing intermediate container 4cd699fed922
 ---> aef03f5291a4
Step 10/14 : ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
 ---> Running in ba760e4ac6cc
Removing intermediate container ba760e4ac6cc
 ---> ad88e916335c
Step 11/14 : ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64
 ---> Running in 12686e5493d7
Removing intermediate container 12686e5493d7
 ---> 480c76c256c1
Step 12/14 : ENV NVIDIA_VISIBLE_DEVICES all
 ---> Running in 23012f84bd4b
Removing intermediate container 23012f84bd4b
 ---> 28f78fdc538c
Step 13/14 : ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
 ---> Running in 45c192bf4c4f
Removing intermediate container 45c192bf4c4f
 ---> e137e9d6c2d9
Step 14/14 : ENV NVIDIA_REQUIRE_CUDA "cuda>=9.1"
 ---> Running in 02ac3f72beda
Removing intermediate container 02ac3f72beda
 ---> b36da56fc5b2
Successfully built b36da56fc5b2
Successfully tagged cuda9.1:latest
```

```
nvidia-docker run -it -v `pwd`:`pwd` -w `pwd` cuda9.1:latest
```

```
root@793100aa5520:/home/thomas/nvidia# nvidia-smi
Thu Jul 11 17:37:03 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 410.73       Driver Version: 410.73       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 105...  Off  | 00000000:01:00.0  On |                  N/A |
| 46%   65C    P0    N/A /  75W |    544MiB /  4036MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
root@793100aa5520:/home/thomas/nvidia# ^C
root@793100aa5520:/home/thomas/nvidia# 
```
Now you want to run a docker with the runtime environment
You dowload the runtime dockerfile
https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.1/runtime/Dockerfile

adjust the Dockerfile
```
FROM cuda9.1:latest
LABEL maintainer "NVIDIA CORPORATION <cudatools@nvidia.com>"

ENV NCCL_VERSION 2.2.12

RUN apt-get update && apt-get install -y --no-install-recommends \
        cuda-libraries-$CUDA_PKG_VERSION \
        libnccl2=$NCCL_VERSION-1+cuda9.1 && \
        apt-mark hold libnccl2 && \
        rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y libglu1-mesa-dev freeglut3-dev mesa-common-dev apt-utils


```
Then 
```
docker build  -t cuda91runtime .
```
```
Processing triggers for libc-bin (2.23-0ubuntu11) ...
libnccl2 set on hold.
Removing intermediate container f87c7d906f79
 ---> 02cf47b87036
Successfully built 02cf47b87036
Successfully tagged cuda91runtime:latest
```
nvidia-docker run -it -v `pwd`:`pwd` -w `pwd` cuda91runtime:latest
```
root@2a0eb7d0ddb8:/home/thomas# nvidia-smi
Thu Jul 11 18:06:09 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 410.73       Driver Version: 410.73       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 105...  Off  | 00000000:01:00.0  On |                  N/A |
| 30%   31C    P8    N/A /  75W |    529MiB /  4036MiB |      1%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
root@2a0eb7d0ddb8:/home/thomas# 
```

./nbody




failed to open display 'localhost:0.0'








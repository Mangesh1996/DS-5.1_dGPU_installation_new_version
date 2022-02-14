# DS-5.1_dGPU_installation_new_version
Deepstream installing on Ubuntu 18.04
Steps to install and run Deepstream-5.1, python bindings and running python test app3

**Needed nvidia driver 460 (if not present it will get installed during cuda 
installation it will install 455 which will also work)**

# check the graphic driver version
--> nvidia-smi

**Step 1: Disable the default Nouveau NVIDIA driver**

-->sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"

--> sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"

--> cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf

-->sudo update-initramfs -u


**Step 2: Install Cuda 11.1**

-->sudo apt-get install gcc

-->sudo apt-get install make

-->wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin

-->sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600

-->wget https://developer.download.nvidia.com/compute/cuda/11.1.0/local_installers/cuda-repo-ubuntu1804-11-1-local_11.1.0-455.23.05-1_amd64.deb

-->sudo dpkg -i cuda-repo-ubuntu1804-11-1-local_11.1.0-455.23.05-1_amd64.deb

-->sudo apt-key add /var/cuda-repo-ubuntu1804-11-1-local/7fa2af80.pub

-->sudo apt-get update

-->sudo apt-get -y install cuda

-->sudo nano ~/.bashrc

## paste below path in bashrc last line and also past on terminal

-->export PATH=/usr/local/cuda-11.1/bin:$PATH

-->export LD_LIBRARY_PATH=/usr/local/cuda-11.1/lib64:$LD_LIBRARY_PATH



**Step 3: Install cudnn 8.0.4**

#Download cudnn 8.0.5 tar file

https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.5/11.1_20201106/cudnn-11.1-linux-x64-v8.0.5.39.tgz

-->tar -xzvf cudnn-11.1-linux-x64-v8.0.5.39.tgz

-->sudo cp cuda/include/cudnn*.h /usr/local/cuda/include

-->sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64

-->sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*



**Step 4: Installing tensorrt 7.2.2**

# Download tensorrt 7.2.2. deb file

https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/7.2.2/local_repos/nv-tensorrt-repo-ubuntu1804-cuda11.1-trt7.2.2.3-ga-20201211_1-1_amd64.deb

-->os="ubuntu1804"

-->tag="cuda11.1-trt7.2.2.3-ga-20201211"

-->sudo dpkg -i nv-tensorrt-repo-${os}-${tag}_1-1_amd64.deb

-->sudo apt-key add /var/nv-tensorrt-repo-${tag}/7fa2af80.pub

-->sudo apt-get update

-->sudo apt-get install tensorrt

-->sudo apt-get install python3-libnvinfer-dev

-->sudo apt-get install uff-converter-tf

-->dpkg -l | grep TensorRT



**Step 5: Installing Deepstream 5.1**


#Uninstall any previous deepstream version

#Download the deepstream 5.1 tar file

https://developer.nvidia.com/deepstream-sdk-download-tesla-archived

-->sudo apt-get install libtool m4 automake

-->sudo apt install \

libssl1.0.0 \

libgstreamer1.0-0 \

gstreamer1.0-tools \

gstreamer1.0-plugins-good \

gstreamer1.0-plugins-bad \

gstreamer1.0-plugins-ugly \

gstreamer1.0-libav \

libgstrtspserver-1.0-0 \

libjansson4 \

libgstreamer1.0-dev

-->sudo tar -xvf deepstream_sdk_v5.1.0_x86_64.tbz2 -C /

-->cd /opt/nvidia/deepstream/deepstream-5.1

-->sudo ./install.sh

-->sudo ldconfig


**Step 6:Installing python binding**


-->sudo apt-get install python-gi-dev

--> export GST_LIBS="-lgstreamer-1.0 -lgobject-2.0 -lglib-2.0"

-->export GST_CFLAGS="-pthread -I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include"

# if showing permission error use sudo

-->git clone https://github.com/GStreamer/gst-python.git

-->cd gst-python

--> git checkout 1a8f48a

-->./autogen.sh PYTHON=python3

-->./configure PYTHON=python3

-->make

-->sudo make install

# go to souce directory

-->cd sources

-->sudo git clone https://github.com/NVIDIA-AI-IOT/deepstream_python_apps



**Step 7: Running testapp**

-->cd /opt/nvidia/deepstream/deepstream-5.1/sources/deepstream_python_apps/apps/deepstream-test3

-->python3 deepstream_test_3.py file:///opt/nvidia/deepstream/deepstream-5.1/samples/streams/sample_1080p_h264.mp4


---------------------------------File name ----------------------------------------------

1)cuda-repo-ubuntu1804-11-1-local_11.1.0-455.23.05-1_amd64.deb

2)cudnn-11.1-linux-x64-v8.0.5.39.tgz

3)nv-tensorrt-repo-ubuntu1804-cuda11.1-trt7.2.2.3-ga-20201211_1-1_amd64.deb

4)deepstream_sdk_v5.1.0_x86_64.tbz2


---------------------------------(Optional)-----------------------------------------------

To remove "/sbin/ldconfig.real: /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8 is not a symbolic link "

-->sudo ln -sf /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8.0.5 /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8


#his will link with the version and if multiple files has the same will have to 

execute on every one of them (Not mandatory deepstream apps work well without doing this also)


# if showing again error kinldy try this command to clean cache

-->rm ~/.cache/gstreamer-1.0/registry.x86_64.bin


--------------------------------------(Error when run cuda install.sh :- not a symbolic link)-------------------------------
**not a symbolic link

/sbin/ldconfig.real: /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8 is not a symbolic link
/sbin/ldconfig.real: /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_ops_train.so.8 is not a symbolic link
/sbin/ldconfig.real: /usr/local/cuda-11.1/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8 is not a symbolic link

Solve:--

1) cd /usr/local/cuda-11.1/targets/x86_64-linux/lib
2)ls -lha libcudnn*
3)

-->sudo ln -sf libcudnn_adv_infer.so.8.0.5 libcudnn_adv_infer.so.8
-->sudo ln -sf libcudnn_ops_train.so.8 libcudnn_ops_train.so.8


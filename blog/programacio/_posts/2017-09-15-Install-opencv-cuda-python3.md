---           
layout: post
title: Installing opencv with CUDA and python3
tags: machine-learning computer-vision
date: 2017-09-16
author: Pere Vilas
---
```
echo "Installing Nvidia drivers"
# Start clean
sudo apt purge nvidia-*
# Add the PPA
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt-get install nvidia-367

echo "Installing CUDA 8. Download it form https://developer.nvidia.com/cuda-downloads"
scp -i ...
sudo dpkg -i cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb
sudo apt update
sudo apt install cuda

echo "testing cuda"vi 
nvcc --version
nvidia-smi

echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib64" >> ~/.bashrc
echo "export PATH=$PATH:/usr/local/cuda-8.0/bin" >> ~/.bashrc
source ~/.bashrc


echo "Install libraries"
sudo apt-get install --assume-yes build-essential cmake git
sudo apt-get install --assume-yes pkg-config unzip ffmpeg qtbase5-dev python-dev python3-dev python-numpy python3-numpy
sudo apt-get install --assume-yes libopencv-dev libgtk-3-dev libdc1394-22 libdc1394-22-dev libjpeg-dev libpng12-dev libtiff5-dev libjasper-dev
sudo apt-get install --assume-yes libavcodec-dev libavformat-dev libswscale-dev libxine2-dev libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt-get install --assume-yes libv4l-dev libtbb-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev
sudo apt-get install --assume-yes libvorbis-dev libxvidcore-dev v4l-utils python-vtk
sudo apt-get install --assume-yes liblapacke-dev libopenblas-dev checkinstall
sudo apt-get install --assume-yes libgdal-dev
sudo apt-get install --assume-yes libeigen3-dev

echo "Install and setup a venv"
sudo pip install virtualenv virtualenvwrapper
sudo rm -rf ~/get-pip.py ~/.cache/pip

echo -e "\n# virtualenv and virtualenvwrapper" >> ~/.bashrc
echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
source ~/.bashrc

mkvirtualenv cv -p python3
workon cv

echo "Installing local cv libraries"
pip install numpy

mkdir install_opencv
cd install_opencv

echo "Downloading opencv"
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git


echo "Making"
cd opencv
mkdir build
cd build

cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D WITH_CUDA=ON \
    -D ENABLE_FAST_MATH=1 \
    -D CUDA_FAST_MATH=1 \
    -D WITH_CUBLAS=1 \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D BUILD_opencv_java=OFF \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON \
    -D INSTALL_C_EXAMPLES=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
    -D PYTHON_EXECUTABLE=~/.virtualenvs/cv/bin/python \
    -D WITH_CUFFT=ON -D WITH_EIGEN=ON \
    -D CUDA_GENERATION=Auto \
    -D WITH_GTK=OFF -D WITH_GTK_2_X=OFF -D WITH_MATLAB=OFF \
    -D WITH_QT=ON \
    -D WITH_OPENGL=ON \
    -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
    -D BUILD_EXAMPLES=ON ..

make -j $(($(nproc) + 1))

echo "Installing opencv on the system"
sudo make install
# sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
sudo apt-get update

echo "sym linking cv2.so to site-packages of venv"
ls -l /usr/local/lib/python3.5/site-packages
cd /usr/local/lib/python3.5/site-packages/
sudo mv cv2.cpython-35m-x86_64-linux-gnu.so cv2.so
echo "cd to the cv virtual environment"
cd ~/.virtualenvs/cv/lib/python3.5/site-packages/
ln -s /usr/local/lib/python3.5/site-packages/cv2.so cv2.so


echo "testing"
cd ~
workon cv
python3 -c "import cv2; print(cv2.__version__)"
```



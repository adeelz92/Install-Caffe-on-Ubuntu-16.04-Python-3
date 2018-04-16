# Install Caffe - Ubuntu-16.04 - Python 3.5
This is the guide to install Caffe with GPU support on Ubuntu 16.04 with Python 3.5. Change the commands accordingly to 
use this guide for other version of Python 3. This guide assumes that you have already installed CUDA and cuDNN.

For CUDA installation follow: http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4VZnqTJ2A.

For cuDNN follow: http://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html.

Step 1: Update and Upgrade the packages list.
1. sudo apt-get update
2. sudo apt-get upgrade

Step 2: Install general dependencies. Open terminal and type following commands in terminal.
1. sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
2. sudo apt-get install --no-install-recommends libboost-all-dev
3. sudo apt-get install libatlas-base-dev
4. sudo apt-get install libopenblas-dev
5. sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev

Step 3: Go to the installation folder using "cd /your/path" command. Download/Clone the latest git repository using following command and go to
the caffe directory in terminal.
1. git clone https://github.com/BVLC/caffe.git
2. cd caffe

Step 4: Install the python requirements for caffe. Use following commands in terminal.
1. cd python
2. for req in $(cat requirements.txt); do pip3 install --no-cache-dir $req; done (Wait for it to finish installation)
3. cd ..

Step 5: Add caffe python path to the $PYTHONPATH
1. export PYTHONPATH=/path/to/caffe/python

Example: export PYTHONPATH=/home/user/xyz/caffe/python

Step 6: Get the Python 3.5 USER_SITE path by typing following command in the terminal.
1. python3 -m site

We will need the path given in USER_SITE. e.g. USER_SITE: '/home/user/.local/lib/python3.5/site-packages'

Step 7: Generate the Makefile configuration file using following command in terminal.
1. cp Makefile.config.example Makefile.config

Step 8: We will adjust the Makefile.config file in order to install the caffe for Python 3. Edit the Makefile.config in your favourite editor.

Step 9: Perform the following changes in Makefile.config.
1. Comment the PYTHON_INCLUDE lines that refer to python2.7. They should look like this:

#PYTHON_INCLUDE := /usr/include/python2.7 \
#/usr/lib/python2.7/dist-packages/numpy/core/include

2. Uncomment PYTHON_LIBRARIES and PYTHON_INCLUDE for python 3. They should look like this at this point:

PYTHON_LIBRARIES := boost_python3 python3.5m

PYTHON_INCLUDE := /usr/include/python3.5m \
                 /usr/lib/python3.5/dist-packages/numpy/core/include
                 
3. Use the path copied in step 6 and add following path to the PYTHON_INCLUDE.

/home/user/.local/lib/python3.5/site-packages/numpy/core/include

PYTHON_INCLUDE should look like this at this point. 

PYTHON_INCLUDE := /usr/include/python3.5m \
                 /usr/lib/python3.5/dist-packages/numpy/core/include \
				 /home/nvme/.local/lib/python3.5/site-packages/numpy/core/include
         
4. Uncomment the line WITH_PYTHON_LAYER := 1.

5. Add '/usr/include/hdf5/serial' to the INCLUDE_DIRS variable. It should look like this:

INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial

6. Find the path to hdf5, use command: "find /usr/lib -name hdf5". Copy the path and add it to LIBRARY_DIRS after appending '/serial' after it. It should look like this at this point.

LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial

7. Find the path to libpython3.5m by using command "find /usr -name libpython3.5*". It will return multiple paths to the library but use 
'/usr/lib/x86_64-linux-gnu/'. Add this path to PYTHON_LIB and LIBRARY_DIRS. Both of these should look like this at this point. 

PYTHON_LIB := /usr/lib /usr/lib/x86_64-linux-gnu/

LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial /usr/lib/x86_64-linux-gnu/

8. Find the path to libboost_python-py35 using command "find /usr -name libboost_python-py35*". Usually the path is '/usr/lib/x86_64-linux-gnu/' 
which have already been added to the PYTHON_LIB and LIBRARY_DIRS in 7. If it is different then add it to both of these variables. 

9. Now rename boost_python3 in PYTHON_LIBRARIES to boost_python-py35. PYTHON_LIBRARIES should look like this.

PYTHON_LIBRARIES := boost_python-py35 python3.5m

10. Make sure CPU_ONLY := 1 is commented in order to install caffe with GPU support.

Step 10: Install caffe using following commands. Make sure you are in caffe home directory.

1. make all
2. make runtest
3. make pycaffe

Step 11: Check by importing caffe in python3 interpreter. Open python3 interpreter and import caffe.
1. python3
2. import caffe

At this point caffe should be imported and be able to use successfully.

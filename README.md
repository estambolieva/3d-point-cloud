## Installing Python Requirements

To install the packages we need to the lidar data exploration and ML training, it is only needed to install the `requirements.txt` file by typing in the terminal:

```sh
pip3 install -r requirements.txt

# Install PCLPY
conda install -c conda-forge -c davidcaron pclpy

# install PCL
sudo apt-get update -y
sudo apt-get install libpcl-dev -y

# conda install -c sirokujira pcl --channel conda-forge UNINSTALLED
# conda install -c sirokujira python-pcl --channel conda-forge WAS NEVER INSTALLED
```

`Python-pcl` works only with Python2.7, Python3.5 and Python3.6. I use Python3.8 so a virtual environment which runs python3.6 needs to be installed and used. 

```sh
# Install Python3.6
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.6
sudo apt autoremove

# Install virtual environment
sudo apt-get update
sudo apt-get install python3-virtualenv

# Create Python3.6 virtual environment called LIDAR
virtualenv -p /usr/bin/python3.6 lidar

# Work on this virtual environment
source lidar/bin/activate

# check the python version to verify that it is Python3.6
python --version
```


**Error**: when trying to `import pcl` in Python3.6, the following error occurs: `ImportError: libpcl_keypoints.so.1.7: cannot open shared object file: No such file or directory`


**a. Tried**
Installing `pcl v0.3.0rc1` from [source](https://files.pythonhosted.org/packages/8c/78/372264333493d53cf2cfc7427df3eb752a6cdf5b9f8ee7b53bc30780401d/python-pcl-0.3.0a1.tar.gz) works around this issue with `libpcl_keypoints.so.1.7` on Ubuntu 18.04. Let's do this then

1. Download `python-pcl-0.3.0a1.tar.gz` from source - [here](https://files.pythonhosted.org/packages/8c/78/372264333493d53cf2cfc7427df3eb752a6cdf5b9f8ee7b53bc30780401d/python-pcl-0.3.0a1.tar.gz)

2. Unpack and install

```sh
tar zxf python-pcl-0.3.0a1.tar.gz
cd python-pcl-0.3.0a1
python setup.py install

> running install
> running egg_info
> writing python_pcl.egg-info/PKG-INFO
> writing dependency_links to python_pcl.egg-info/dependency_links.txt
> writing requirements to python_pcl.egg-info/requires.txt
> writing top-level names to python_pcl.egg-info/top_level.txt
> reading manifest file 'python_pcl.egg-info/SOURCES.txt'
> writing manifest file 'python_pcl.egg-info/SOURCES.txt'
> installing library code to build/bdist.linux-x86_64/egg
> running install_lib
> running build_py
> running build_ext
> cythoning pcl/_pcl_180.pyx to pcl/_pcl_180.cpp
> error: /home/path/python-pcl-0.3.0a1/pcl/_pcl_180.pyx

```


**Tried**
`Python-pcl` works with `libpcl_keypoints.so.1.7` however Ubuntu installs the latest version of it which currently is `libpcl_keypoints.so.1.8`. 
Installing `libpcl_keypoints.so.1.7` downloaded as *.deb* from [here](https://ubuntu.pkgs.org/16.04/ubuntu-universe-amd64/libpcl-dev_1.7.2-14build1_amd64.deb.html)


```sh
sudo apt remove libpcl-dev # to remove installed version 1.8

# checked the installed version
sudo apt list libpcl-dev

> libpcl-dev/bionic-updates 1.8.1+dfsg1-2ubuntu2.18.04.1 amd64
> N: There is 1 additional version. Please use the '-a' switch to see it
```

In order to add `libpcl_keypoints.so.1.7` I needed to update the `source.list` of Ubuntu 18.04 (
[Ubuntu 16.04 source.list](https://gist.githubusercontent.com/rohitrawat/60a04e6ebe4a9ec1203eac3a11d4afc1/raw/fcdfde2ab57e455ba9b37077abf85a81c504a4a9/sources.list) ) and install [I read this here](https://github.com/strawlab/python-pcl/issues/317):

```sh
sudo apt-get install libogdi-dev libhdf5-openmpi-dev python-mpi4py libgdal-dev libhdf5-mpi-dev python-vtk6 libhdf5-openmpi-100 libvtk6.3 libvtk6.3-qt libvtk6-qt-dev libvtk6-dev libboost-all-dev libogdi3.2 libopenmpi1.10 libgdal1i libgdal.so.1-1.11.3 libvtk6.2 libpcl-visualization1.7 libpcl-surface1.7 libpcl-io1.7 libpcl-apps1.7 libpcl1.7 libpcl-dev=1.7.2-14build1 --fix-broken

```

if these do not work, I need to reverse the changes!

Test that `python-pcl` has been installed successfully:

```sh
python3

> 
```

https://vtk.org/download/
https://discourse.vtk.org/t/installing-vtk-in-ubuntu-18-04/2147/3


## Viewing the Data

**On Ubuntu**

Using the [displaz](http://c42f.github.io/displaz/) library.

To start displaz, type in the terminal the following independent of the folder in which you are right now:

```sh
displaz
```

### Visualizing .pcd data

```sh
sudo apt-get install pcl-tools 

pcl_viewer multiview 1 ./path_to.pcd 
```

## Labelling the Data

[Supervise.ly](https://supervise.ly/) is an online tool that has been developed to label data for Machine Learning applications. 

Luckily, they recently released their support for labelling 3D Point Cloud Data - [link here](https://supervise.ly/lidar-3d-cloud/). We will use this tool to label the data using their graphical interface to prepare it for the machine learning job. Here is a [Medium blog post](https://medium.com/deep-systems/releasing-first-online-3d-point-cloud-labeling-tool-in-supervisely-4faca42b5d6e) explaining how the labelling interface works.

It is needed to create an account in order to use Supervise.ly. 


### Supervise.ly allowed lidar data formats

Supervise.ly allows for 2 types of lidar data files to be uploaded to their platform: *.pcd* and *.kitti*.
I have chosen to convert the *.las* file we have into a *.pcd* file. 


### Converting *.las* to *.pcd*

**Note** PDAL failed to correctly convert the files. The resulting *.pcd* file has 0 point cloud points and no header. Will try converting using [open3d](http://www.open3d.org/docs/release/tutorial/Basic/pointcloud.html) - which is a Python library.

**Current Setup**
Operating System: Ubuntu 18.04

**Needed Package**
[PDAL](https://pdal.io/)

**PDAL Installation**
Instructions [here](https://zoomadmin.com/HowToInstall/UbuntuPackage/pdal)

```sh
 sudo apt-get update -y 
 sudo apt-get install -y pdal 
```

**File Convertion**
Instructions [here](https://gis.stackexchange.com/questions/250714/converting-las-to-pcd-file-using-pdal)

```sh
pdal translate input.las output.pcd
```

Result
![Converting Data](https://raw.githubusercontent.com/estambolieva/3d-point-cloud/master/images/las_to_pcd.png)

When this is finished, please upload the *.pcd* file to Supervise.ly and start labelling.
![Converting Data](https://raw.githubusercontent.com/estambolieva/3d-point-cloud/master/images/uploading_pcd.png)


### Other Labelling Resources Explored

1. [3D Lidar Annotator](https://github.com/songanz/3D-LiDAR-annotator)

2. []()

3. []()


## Additional Resources

1. [3D Object Detection - Papers with Code](https://paperswithcode.com/task/3d-object-detection)

2. [Blog - Python & Point Cloud Data: a simple clustering approach with visualization](https://towardsdatascience.com/point-cloud-data-simple-approach-f3855fdc08f5)


Installation Instructions

* Link with these instructoins [here](https://askubuntu.com/questions/1231543/problem-with-creating-python-3-6-virtual-environment-on-ubuntu-20-04).

* PCL installation instructions [here](https://python-pcl-fork.readthedocs.io/en/rc_patches4/install.html)
* PCLPY installation instructions [here](https://python-pcl-fork.readthedocs.io/en/rc_patches4/install.html) and [here on Github](https://github.com/strawlab/python-pcl)


Python Packages

1. Las2Pcd Github Page [here](https://github.com/murtiad/las2pcd)



sudo aptitude install libpcl-dev -y
pip3 install python-pcl

Error:
ImportError: libpcl_surface.so.1.7: cannot open shared object file: No such file or directory

sudo aptitude install libpcl-dev=1.7.2-14build1

download python-pcl tar.gz from there: https://github.com/strawlab/python-pcl/archive/v0.3.0rc1.tar.gz

image from GIMP

```sh
sudo aptitude install libpcl-dev=1.7.2-14build1
sudo apt install pcl-tools
sudo apt-get update -y 
sudo apt-get install -y cython
pip3 install cython
pip3 install numpy
sudo apt-get install libpq-dev python3-dev build-essential libxml2-dev libxslt1-dev libldap2-dev libsasl2-dev libffi-dev

>>>>>>>
>python3 setup.py install
>setup.py: error: cannot find PCL, tried
>    pkg-config pcl_common-1.8
>    pkg-config pcl_common-1.7
>    pkg-config pcl_common-1.6
>    pkg-config pcl_common


# check all libraries installed
pkg-config --list-all
```

https://github.com/PointCloudLibrary/pcl/archive/pcl-1.11.1.tar.gz
& how to compile: https://pcl-tutorials.readthedocs.io/en/latest/compiling_pcl_posix.html


metslib
```sh
# install instructions here: https://kezunlin.me/post/137aa5fc/
wget https://www.coin-or.org/download/source/metslib/metslib-0.5.3.tgz
tar xzvf metslib-0.5.3.tgz
cd metslib-0.5.3
./configure
make
sudo make install
```

[VTK](https://vtk.org/Wiki/VTK/Building/Linux)
```sh
git clone https://gitlab.kitware.com/vtk/vtk.git
mkdir VTK-build # this is at the same level as the vtk git repo dir - not a child of it!!
cd VTK-build
cmake -DCMAKE_BUILD_TYPE:STRING=Release ../vtk/

# update cmake # download page: https://cmake.org/download/
wget https://github.com/Kitware/CMake/releases/download/v3.18.2/cmake-3.18.2.tar.gz
tar xvf cmake-3.18.2.tar.gz
cd cmake-3.18.2
./configure
make
sudo make install
sudo ln -s /usr/local/bin/cmake /usr/bin # so that cmake --version can find cname in /usr/bin/cmake

# get Q5 https://vtk.org/Wiki/VTK/Building/Linux
wget http://download.qt-project.org/official_releases/qt/5.15/5.15.1/single/qt-everywhere-src-5.15.1.tar.xz
tar -xf qt-everywhere-src-5.15.1.tar.xz
cd qt-everywhere-src-5.15.1
./configure
make -j2
sudo make install

FINISHED HERE... STILL TASKS TO DO...
```

```sh
sudo apt-get install libeigen3-dev
tar xvf pcl-pcl-1.11.1.tar.gz
cd pcl-pcl-1.11.1 && mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DBUILD_visualization=ON #cmake .. NEED TO INSTALL VTK FOR VISUALIZATION
# -- Could NOT find Pcap (missing: PCAP_LIBRARIES PCAP_INCLUDE_DIRS) 
#  Could not find a package configuration file provided by "VTK" with any of
#  the following names:

#   VTKConfig.cmake
#   vtk-config.cmake
make -j2
sudo make -j2 install

> successfully installed.
```

```sh
conda install -c sirokujira python-pcl --channel conda-forge
```

UnsatisfiableError: The following specifications were found
to be incompatible with the existing python installation in your environment:

Specifications:

  - python-pcl -> python[version='>=2.7,<2.8.0a0|>=3.6,<3.7.0a0|>=3.5,<3.6.0a0']

Your python: python=3.8	



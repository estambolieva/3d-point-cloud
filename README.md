## Labelling the Data

[Supervise.ly](https://supervise.ly/) is an online tool that has been developed to label data for Machine Learning applications. 

Luckily, they recently released their support for labelling 3D Point Cloud Data - [link here](https://supervise.ly/lidar-3d-cloud/). We will use this tool to label the data using their graphical interface to prepare it for the machine learning job.

It is needed to create an account in order to use Supervise.ly. 


### Supervise.ly allowed lidar data formats

Supervise.ly allows for 2 types of lidar data files to be uploaded to their platform: *.pcd* and *.kitti*.
I have chosen to convert the *.las* file we have into a *.pcd* file. 


### Converting *.las* to *.pcd*

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
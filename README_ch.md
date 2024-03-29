# dnnDetector
![](https://img.shields.io/badge/TGeek-Projects-blue.svg)

![](https://img.shields.io/badge/linux%20build-pass-green.svg)

![](https://img.shields.io/badge/Opencv-4.5.1-green.svg?style=social&logo=opencv)

[English](README.md)

---

作者: Michael.Chen

网站: www.tgeek.tech

联系我: m.c.chen@outlook.com

---
## 描述
基于OpenCV DNN的目标识别与检测程序

## 依赖
### 依赖安装
```
依赖：
OpenCV >= 4.1	# install with dnn, opention: cuda, cublas 
OpenCV_contrib >= 4.1	# same version as OpenCV
```
依赖安装教程，以OpenCV 4.5.1为例
```bash
# 在下面链接下载对应版本opencv的Source Code包，https://github.com/opencv/opencv/releases
# 将压缩包解压缩到本地，比如~/apps
# 在下面链接下载和上面opencv版本相同的contrib包，https://github.com/opencv/opencv_contrib/releases
# 解压并放入opencv目录内部,比如 ~/apps/opencv-4.5.1
# 进入opencv文件夹进行操作
$ cd ~/apps/opencv-4.5.1
$ mkdir build
$ cd build
# 安装了cuda加速运行下面
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D WITH_CUDA=ON \
-D ENABLE_FAST_MATH=1 \
-D CUDA_FAST_MATH=1 \
-D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
-D WITH_CUBLAS=1 \
-D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib-4.5.1/modules \
-D OPENCV_DNN_CUDA=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON ..

# 无cuda用户运行下面
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib-4.5.1/modules \
-D OPENCV_GENERATE_PKGCONFIG=ON ..
```
### 模型下载
```bash
# SSD模型自行搜索
# YOLO模型 https://pjreddie.com/darknet/yolo/
# 新建文件夹 dnn_nets/yolo，将cfg和weights放入其中
# 并在dnn_nets/yolo新建文件coco.names填入
person
bicycle
car
motorbike
aeroplane
bus
train
truck
boat
traffic light
fire hydrant
stop sign
parking meter
bench
bird
cat
dog
horse
sheep
cow
elephant
bear
zebra
giraffe
backpack
umbrella
handbag
tie
suitcase
frisbee
skis
snowboard
sports ball
kite
baseball bat
baseball glove
skateboard
surfboard
tennis racket
bottle
wine glass
cup
fork
knife
spoon
bowl
banana
apple
sandwich
orange
broccoli
carrot
hot dog
pizza
donut
cake
chair
sofa
pottedplant
bed
diningtable
toilet
tvmonitor
laptop
mouse
remote
keyboard
cell phone
microwave
oven
toaster
sink
refrigerator
book
clock
vase
scissors
teddy bear
hair drier
toothbrush
```
## 文件结构

**src/:** 源码，示例程序- demo.cpp	

**include/:** 头文件 - detector.hpp

**lib/:** 库文件 - libdnnDetector.so

**param/:** 配置文件 - param.xml

**dnn_nets/:** 神经网络文件 - 网络结构，模型，标签文件

**video/:** 测试视频

## 使用

### 编译

#### 创建文件夹用来编译项目

```bash
mkdir build
```

#### 编译

如果有多版本OpenCV，去掉 ```CMakeLists.txt```第9行注释并修改

例如, OpenCV目录在 "/home/user/opencv_4.1". 修改 ```CMakeLists.txt``` 

```cmake
set(OpenCV_DIR "/home/user/opencv_4.1/build/")
```

运行 ``cmake``

```
cd build/
cmake ..
```

运行 ```make ``` 进行编译

```bash
make
```

## 运行 

```bash
./dnnDetector
```

### 参数修改

任何参数的修改 **不需要重新编译** 

修改 ```param/param.xml``` 改变参数

#### 预测参数

line5 - line7

```xml
<net_type>0</net_type>              <!-->0-ssd 1-yolo<-->
<thresh>0.35</thresh>               <!-->confidence threshold<-->
<nms_thresh>0.25</nms_thresh>       <!-->nms threshold<-->
```

#### Yolo 配置

line10 - line14, 可以选择任意yolo网络或模型

```xml
<Yolo_meanVal>1</Yolo_meanVal> 
<Yolo_scaleFactor>0.003921569</Yolo_scaleFactor>
<Yolo_config>../dnn_nets/yolo/yolov3-tiny.cfg</Yolo_config>
<Yolo_model>../dnn_nets/yolo/yolov3-tiny.weights</Yolo_model>
<coco_name>../dnn_nets/yolo/coco.names</coco_name>
```

#### SSD 配置

line17 - line22, 可以选择任意SSD网络或模型

```xml
<ssd_meanVal>127.5</ssd_meanVal> 
<ssd_scaleFactor>0.007843</ssd_scaleFactor>
<ssd_config>../dnn_nets/ssd/deploy.prototxt</ssd_config>
<ssd_model>../dnn_nets/ssd/mobilenet_iter_73000.caffemodel</ssd_model>
<ssd_name>../dnn_nets/ssd/ssd.names</ssd_name>
</opencv_storage>
```

## API使用Demo

对demo.cpp进行详解

### 头文件

添加头文件

```c++
#include "detector.hpp"
```

使用到的所有头文件均在```include/detector.hpp```中提到

```c++
#include <opencv2/dnn.hpp>
#include <opencv2/opencv.hpp>
#include <iostream>
#include <vector>
```

### 网络读取

实例化 ```Detector``` 类, 并且读取网络

```c++
	Detector detector;
	cv::dnn::Net net = detector.net;
```

网络初始化在 ```include/detector.hpp``` 中

### 视频或摄像头读取

```c++
cv::VideoCapture capture;
capture.open("../video/test.mp4");
if(capture.isOpened())
	std::cout<<"INFO: Video file load sucessfully"<<std::endl;
else
	std::cout<<"ERROR: Cannot find video file"<<std::endl;
cv::Mat frame, output;
while (capture.read(frame))
{
  // 检测
}
```

### 检测

```while``` 循环中写

#### 得到预测结果

执行 ```Detector.thePredictor(cv::Mat frame, cv::dnn::Net net)```

```c++
detector.thePredictor(frame, net);			// do detection
```

#### 画出结果以及显示

```c++
frame.copyTo(output);
// draw output
detector.drawResult(output,detector.out_names,detector.out_boxes,detector.out_confidences,detector.out_centers,true);
// display
cv::imshow("demo",output);
// read keyboard
char c = cv::waitKey(5);
if (c == 27)
{ 
	// ESC to exit
	break;
}
```

循环结束

#### 释放摄像头结束程序

```c++
capture.release();
return 0;
```

##Detector类 公共函数与变量

### 构造函数 Detector()

#### 读取配置文件

```c++
// Read configration file
cv::FileStorage setting_fs("../param/param.xml", cv::FileStorage::READ);       
```

#### 传入预测参数

网络类型，置信度阈值，非极大值抑制阈值

```c++
setting_fs["net_type"]>>net_type;                  
setting_fs["thresh"]>>thresh;
setting_fs["nms_thresh"]>>nms_thresh;
```

#### 如果为YOLO 读取YOLO配置

```c++
// If use YoloV3
if (net_type){
std::cout << "INFO: Found \"net_type==1\", using **YoloV3** network" << std::endl;
width = 416;
height = 416;
setting_fs["Yolo_config"] >> net_structure;
setting_fs["Yolo_model"] >> model;
setting_fs["coco_name"] >> name_file;
setting_fs["Yolo_scaleFactor"]>>scaleFactor;
setting_fs["Yolo_meanVal"]>>meanVal;
}
```

#### 如果为SSD读取SSD配置

```C++
// If use SSD
else{
std::cout << "INFO: Found \"net_type==0\", using **SSD** network" << std::endl;
width = 300;
height = 300;
setting_fs["ssd_config"] >> net_structure;
setting_fs["ssd_model"] >> model;
setting_fs["ssd_name"] >> name_file;
setting_fs["ssd_scaleFactor"]>>scaleFactor;
setting_fs["ssd_meanVal"]>>meanVal;
}
```

#### 读取网络结构

```c++
// Set network
net = cv::dnn::readNet(net_structure, model);
if (net.empty()){
std::cerr << "ERROR: Can't load network by using the following files: " << std::endl;
exit(-1);
}
else    std::cout<<"INFO: Load network sucessfully"<<std::endl;
```

#### 读取标签文件

```c++
// Read lable file
std::ifstream ifs(name_file.c_str());
std::string line;
while (std::getline(ifs, line)) classes.push_back(line);
```

### 公共变量

```c++
std::vectorcv::Rect out_boxes		// Bounding box

std::vectorcv::Point out_centers	// Bounding box 中心坐标

std::vectorstd::string out_names	// 检测到的物体名称

std::vector<float> out_confidences	// 检测到的物体置信度

```

### 公共函数

```c++
// 进行预测
// 输入参数为	-待预测图像
// 				-网络
void thePredictor(cv::Mat frame, cv::dnn::Net net);  
// 画出结果  
// 输入参数为	-输入/输出图像
//				-物体名称向量
//				-物体BBOX向量
//				-置信度向量
//				-物体中心点向量
//				-是否绘制FPS
void drawResult(cv::Mat& frame, std::vector<std::string> out_names, std::vector<cv::Rect> out_boxes,std::vector<float> confidences,std::vector<cv::Point> out_centers,bool if_fps); 
```


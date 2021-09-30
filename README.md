# Custom Object Detection Module using TensorFlow

[![TensorFlow 2.2](https://img.shields.io/badge/TensorFlow-2.2-FF6F00?logo=tensorflow)](https://github.com/tensorflow/tensorflow/releases/tag/v2.2.0)
![Python](https://img.shields.io/badge/Python-3.8-blueviolet)
![Framework](https://img.shields.io/badge/Framework-Tensorflow-blue)

This code performs a custom object detection on videos recorder by the user.

The algorithm used for object detection is `SSD MobileNet V2 FPNLite 640x640` which has been pre-trained on `MS COCO 2017` dataset. We will not be training the whole model, instead we have added a few layers in the end to make our job easier. \
More such models can be found in [TensorFlow 2 Detection Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)   

Interested users are encouraged to try various models. This can be done as follows:
<ol>
    <li>Right click on the Model name of the model you would like to use</li>
    <li>Click on Copy link address to copy the download link of the model</li>
    <li>Paste the link in a text editor of your choice. You should observe a link similar to download.tensorflow.org/models/object_detection/tf2/YYYYYYYY/XXXXXXXXX.tar.gz</li>
    <li>Copy the XXXXXXXXX part of the link and use it to replace the value of the MODEL_NAME variable in the code shown below</li>
    <li>Copy the YYYYYYYY part of the link and use it to replace the value of the MODEL_DATE variable in the code shown below</li>
</ol>

For example, the download link for the model used below is: `download.tensorflow.org/models/object_detection/tf2/20200711/ssd_mobilenet_v2_fpnlite_640x640_coco17_tpu-8.tar.gz`


## Computer Vision
![~](https://img.shields.io/badge/OpenCV-27338e?style=for-the-badge&logo=OpenCV&logoColor=white)

We shall be using OpenCV for capturing and processing video streams from our webcam.

Interested users are encouraged to go through [OpenCV Tuturials](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_gui/py_video_display/py_video_display.html#capture-video-from-camera)

## Data Augmentation 

Since the model expects only images of dimension `[1, None, None, 3]`, we shall expand the dimensions of the images.

In addition, we shall use various data augmentation techniques like:
1. Flipping the image horizontally
2. Converting the image into grayscale
   
## What is Label Map?
  
Label maps correspond index numbers to category names, so that when our convolution network predicts 5, we know that this corresponds to airplane. 
Here we use internal utility functions, but anything that returns a dictionary mapping integers to appropriate string labels would be fine.  

## Execution Instructions

### System Requirements
When it comes to training a model, your system can have a significant impact. 
The timeframes and other statistics I give later will be impacted by the specifics of your particular system. 
My system is powered by an AMD-Ryzen 7 3700X processor, as well as an NVIDIA GeForce RTX 2060 Super with 8GB DDR6 Graphics Card Memory and 32GB of Cosair Vengence LPX 3200 MHz System Memory. 
A CUDA-Enabled Graphics Card (NVIDIA GTX 650+) is required to train using TensorFlow GPU. Check out the NVIDIA CUDA [Website](https://developer.nvidia.com/cuda-gpus) for additional information on GPU requirements. 

### Installing TensorFlow GPU
The first step is to download and install TensorFlow-GPU. 
There are many great videos on YouTube that go into greater detail about how to do this, and I recommend watching mine above for a better visual representation of how to do so. Anaconda, CUDA, and cuDNN are required for TensorFlow-GPU. 
The last two, CUDA and cuDNN, are required to use the GPU's Graphics Memory and transfer the computation to GPU using it's `Tensor Cores`. 

### Setting up Anaconda
Anaconda will be used to set up a virtual environment in which we will install the required packages.

Create a virtual environment with this command

```
conda create -n tensorflow pip python=3.8
```

Then activate the environment with

```
conda activate tensorflow
```

### Installing TensorFlow

Since you now know the correct CUDA and cuDNN versions needed for TensorFlow, we can install them from the NVIDIA Website. 
For TensorFlow 2.2.0, I used [cuDNN 7.6.5](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/7.6.5.32/Production/10.1_20191031/cudnn-10.1-windows10-x64-v7.6.5.32.zip) and [CUDA 10.1](https://developer.nvidia.com/cuda-10.1-download-archive-base). 
Check the [CUDA Archive](https://developer.nvidia.com/cuda-toolkit-archive) and [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive) for other versions. 

After downloading both files, run the CUDA Installer and follow the setup wizard to install CUDA, there might be some MSBuild and Visual Studio conflicts which you should be able to resolve by installing the newest version of Visual Studio Community with MSBuild Tools. After you have successfully installed the CUDA Toolkit, find where it is installed (for me it was in C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1). Then extract the contents of the cuDNN library in to the CUDA Folder.

Once done with this we have everything needed to install TensorFlow-GPU (or TensorFlow CPU). So we can navigate back to our anaconda prompt, and issue the following command

```
pip install tensorflow-gpu
```

If you are installing TensorFlow CPU, instead use

```
pip install tensorflow
```

Once we are done with the installation, we can use the following code to check if everything installed properly
```
python
>>> import tensorflow as tf
>>> print(tf.__version__)
```
If everything has installed properly you should get the message, "2.2.0", or whatever version of TensorFlow you have. This means TensorFlow is up and running and we are ready to setup our workspace. We can now proceed to the next step!

If you have a Tensorflow version less than 2.x, it is recommended that you upgrade the Tensorflow using
```
pip install --upgrade tensorflow
```
  
**Note if there is an error with importing, you must install [Visual Studio 2019 with C++ Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16).**

### Preparing our Workspace and Anaconda Virtual Environment Directory Structure
For the TensorFlow Object Detection API, there is a certain directory structure that we must follow to train our model. To make the process a bit easier, I added most of the necessary files in this repository.

Firstly, create a folder directly in C: and name it "TensorFlow". It's up to you where you want to put the folder, but you will have to keep in mind this directory path will be needed later to align with the commands. Once you have created this folder, go back to the Anaconda Prompt and switch to the folder with

```
cd C:\TensorFlow
```
Once you are here, you will have to clone the [TensorFlow Model Garden](https://github.com/tensorflow/models) repository with

```
git clone https://github.com/tensorflow/models.git
```
This should clone all the files in a directory called models. 
After you've done so, stay inside `C:\TensorFlow` and download [this](https://github.com/debanjansaha-git/customobjectdetection/archive/main.zip) repository into a .zip file. 
Then extract the files directly in to the TensorFlow directory.


Then, your directory structure should look something like this

```
TensorFlow/
└─ models/
   ├─ community/
   ├─ official/
   ├─ orbit/
   ├─ research/
└─ scripts/
└─ workspace/
   ├─ training_demo/
```
After we have setup the directory structure, we must install the prequisites for the Object Detection API. 

First we need to install the protobuf compiler with

```
conda install -c anaconda protobuf
```
Then you should cd in to the TensorFlow\models\research directory with

```
cd models\research
```
Then compile the protos with

```
protoc object_detection\protos\*.proto --python_out=.
```
After you have done this, close the terminal and open a new Anaconda prompt. If you are using the virtual environment we created earlier, then use the following command to activate it

```
conda activate tensorflow
```
With TensorFlow 2, pycocotools is a dependency for the Object Detection API. To install it with Windows Support use

```
pip install cython
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
```
**Note that Visual C++ 2015 build tools must be installed and on your path, according to the installation instructions. If you do not have this package, then download it [here](https://go.microsoft.com/fwlink/?LinkId=691126).**

Go back to the models\research directory with 

```
cd C:\TensorFlow\models\research
```

Once here, copy and run the setup script with 

```
copy object_detection\packages\tf2\setup.py .
python -m pip install .
```
If there are any errors, report an issue, but they are most likely pycocotools issues meaning your installation was incorrect. But if everything went according to plan you can test your installation with

```
python object_detection\builders\model_builder_tf2_test.py
```
You should get a similar output to this

```
[       OK ] ModelBuilderTF2Test.test_create_ssd_models_from_config
[ RUN      ] ModelBuilderTF2Test.test_invalid_faster_rcnn_batchnorm_update
[       OK ] ModelBuilderTF2Test.test_invalid_faster_rcnn_batchnorm_update
[ RUN      ] ModelBuilderTF2Test.test_invalid_first_stage_nms_iou_threshold
[       OK ] ModelBuilderTF2Test.test_invalid_first_stage_nms_iou_threshold
[ RUN      ] ModelBuilderTF2Test.test_invalid_model_config_proto
[       OK ] ModelBuilderTF2Test.test_invalid_model_config_proto
[ RUN      ] ModelBuilderTF2Test.test_invalid_second_stage_batch_size
[       OK ] ModelBuilderTF2Test.test_invalid_second_stage_batch_size
[ RUN      ] ModelBuilderTF2Test.test_session
[  SKIPPED ] ModelBuilderTF2Test.test_session
[ RUN      ] ModelBuilderTF2Test.test_unknown_faster_rcnn_feature_extractor
[       OK ] ModelBuilderTF2Test.test_unknown_faster_rcnn_feature_extractor
[ RUN      ] ModelBuilderTF2Test.test_unknown_meta_architecture
[       OK ] ModelBuilderTF2Test.test_unknown_meta_architecture
[ RUN      ] ModelBuilderTF2Test.test_unknown_ssd_feature_extractor
[       OK ] ModelBuilderTF2Test.test_unknown_ssd_feature_extractor
----------------------------------------------------------------------
Ran 20 tests in 45.304s

OK (skipped=1)
```
This means we successfully set up the Anaconda Directory Structure and TensorFlow Object Detection API. We can now finally collect and label our dataset. So, let's go on to the next step!

### Gathering and Labeling our Dataset
Since the TensorFlow Object Detection API ready to go, we must collect and label pictures that the model will be trained and tested on. All the files that will be needed from
now on will be loacated in the workspace\training_demo directory. So take a second, look around, and get used to the structure of the directory. 

- ```annotations```: This is where we will store all our training data needed for our model. By this I mean the CSV and RECORD files needed for the training pipeline. There is also a PBTXT File with the labels for our model. If you are training your own dataset you can delete train.record and test.record, but if you are training my Pill Classifier model you can keep them.
- ```exported-models```: This is our output folder where we will export and store our finished inference graph.
- ```images```: This folder consists of a test and train folder. Here we will store the labelled images needed for training and testing as you can probably infer. The labelled images consist of the original image and an XML File. If you want to train the Pill Classifier model, you can keep the images and XML documents, otherwise delete the images and XML files.
- ```models```: In this folder we will store our training pipeline and checkpoint information from the training job as well as the CONFIG file needed for training.
- ```pre-trained-models```: Here we will store our pre-trained model that we will use as a starting checkpoint for training
- The rest of the scripts are just used for training and exporting the model, as well as a sample object detection scipt that performs inference on a test image.

If you want to train a model on your own custom dataset, you must first gather images. Ideally you would want to use 100 images for each class. Say for example, you are training a cat and dog detector. You would have to gather 100 images of cats and 100 images of dogs. For images of pills, I just looked on the internet and downloaded various images. But for your own dataset, I recommend taking diverse pictures with different backgrounds and angles.


After gathering some images, you must partition the dataset. By this I mean you must seperate the data in to a training set and testing set. You should put 80% of your images in to the images\training folder and put the remaining 20% in the images\test folder. After seperating your images, you can label them with [LabelImg](https://tzutalin.github.io/labelImg).


After Downloading LablelImg, configure settings such as the Open Dir and Save Dir. This let's you cycle through all the images and create bounding boxes and labels around the objects. Once you have labelled your image make sure to save and go on to the next image. Do
this for all the images in the images\test and images\train folders. 


We have now gathered our dataset. This means we are ready to generate training data. So onwards to the next step!


### Configuring the Training Pipeline
For this tutorial, we will use a CONFIG File from one of the TensorFlow pre-trained models. There are plenty of models in the [TensorFlow Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md), but we will use the [SSD MobileNet V2 FPNLite 640x640](http://download.tensorflow.org/models/object_detection/tf2/20200711/ssd_mobilenet_v2_fpnlite_640x640_coco17_tpu-8.tar.gz), as it is on the faster end of the spectrum with decent performance. If you want you can choose a different model, but you will have to alter the steps slightly.

To download the model you want, just click on the name in the TensorFlow Model Zoo. This should download a tar.gz file. Once it has downloaded, extracts the contents of the file to the ```pre-trained-models``` directory. The structure of that directory should now look something like this

```
training_demo/
├─ ...
├─ pre-trained-models/
│  └─ ssd_mobilenet_v2_fpnlite_640x640_coco17_tpu-8/
│     ├─ checkpoint/
│     ├─ saved_model/
│     └─ pipeline.config
└─ ...
```
Now, we must create a directory to store our training pipeline. Navigate to the ```models``` directory and create a folder called ```my_ssd_mobilenet_v2_fpnlite```. Then copy the ```pipeline.config``` from the pre-trained-model we downloaded earlier to our newly created directory. Your directory should now look something like this

```
training_demo/
├─ ...
├─ models/
│  └─ my_ssd_mobilenet_v2_fpnlite/
│     └─ pipeline.config
└─ ...
```

Then open up ```models\my_ssd_mobilenet_v2_fpnlite\pipeline.config``` in a text editor because we need to make some changes.
- Line 3. Change ```num_classes``` to the number of classes your model detects. For the basketball, baseball, and football, example you would change it to ```num_classes: 3```
- Line 135. Change ```batch_size``` according to available memory (Higher values require more memory and vice-versa). I changed it to:
  - ```batch_size: 6```
- Line 165. Change ```fine_tune_checkpoint``` to:
  - ```fine_tune_checkpoint: "pre-trained-models/ssd_mobilenet_v2_fpnlite_640x640_coco17_tpu-8/checkpoint/ckpt-0"```
- Line 171. Change ```fine_tune_checkpoint_type``` to:
  - ```fine_tune_checkpoint_type: "detection"```
- Line 175. Change ```label_map_path``` to:
  - ```label_map_path: "annotations/label_map.pbtxt"```
- Line 177. Change ```input_path``` to:
  - ```input_path: "annotations/train.record"```
- Line 185. Change ```label_map_path``` to:
  - ```label_map_path: "annotations/label_map.pbtxt"```
- Line 189. Change ```input_path``` to:
  - ```input_path: "annotations/test.record"```

Once we have made all the necessary changes, that means we are ready for training. So let's move on to the next step!
### Training the Model
Now you go back to your Anaconda Prompt. ```cd``` in to the ```training_demo``` with 

```
cd C:\TensorFlow\workspace\training_demo
```

I have already moved the training script in to the directory, so to run it just use 

```
python model_main_tf2.py --model_dir=models\my_ssd_mobilenet_v2_fpnlite --pipeline_config_path=models\my_ssd_mobilenet_v2_fpnlite\pipeline.config
```

When running the script, you should expect a few warnings but as long as they're not errors you can ignore them. Eventually when the training process starts you should see output similar to this

```
INFO:tensorflow:Step 100 per-step time 0.640s loss=0.454
I0810 11:56:12.520163 11172 model_lib_v2.py:644] Step 100 per-step time 0.640s loss=0.454
```

Congratulations! You have officially started training your model! Now you can kick back and relax as this will take a few hours depending on your system. With my specs that I mentioned earlier, training took about 2 hours. TensorFlow logs output similar to the one above every 100 steps of the process so if it looks frozen, don't worry about it. This output shows you two statistics: per-step time and loss. You're going to want to pay attention to the loss. In between logs, the loss tends to decrease. Your ideally going to want to stop the program when it's between 0.150 and 0.200. This prevents underfitting and overfitting. For me it took around 4000 steps before the loss entered that range. And then to stop the program just use CTRL+C.

### Monitoring Training with TensorBoard (Optional)

TensorFlow allows you to monitor training and visualize training metrics with TensorBoard! Keep in mind this is completely optional and wont affect the training process, so it's up to you whether you want to do it. 
First, open up a new Anaconda Prompt. Then activate the virtual environment we configured with

```
conda activate tensorflow
```

Then ```cd``` in to the ```training_demo``` directory with

```
cd C:\TensorFlow\workspace\training_demo
```
To start a TensorBoard Server, use 
 
```
tensorboard --logdir=models\my_ssd_mobilenet_v2_fpnlite
```
It should output something like this
 
```
Serving TensorBoard on localhost; to expose to the network, use a proxy or pass --bind_all
TensorBoard 2.2.2 at http://localhost:6006/ (Press CTRL+C to quit)
```

Then just open up a web browser and paste the URL given in to the search bar. This should take you to the TensorBoard Server where you can continuously monitor training!

### Exporting the Inference Graph

Once you have finished training and stopped the script, you are ready to export your finished model! You should still be in the ```training_demo``` directory but if not use

```
cd C:\TensorFlow\workspace\training_demo
```

I have already moved the script needed to export, so all you need to do is run this command

```
python .\exporter_main_v2.py --input_type image_tensor --pipeline_config_path .\models\my_ssd_mobilenet_v2_fpnlite\pipeline.config --trained_checkpoint_dir .\models\my_ssd_mobilenet_v2_fpnlite\ --output_directory .\exported-models\my_mobilenet_model
```

**Note that if you get an error similar to ```TypeError: Expected Operation, Variable, or Tensor, got block4 in exporter_main_v2.py``` look at [this](https://github.com/tensorflow/models/issues/8881) error topic**

But if this program finishes successfully, then congratulations because your model is finished! It should be located in the ```C:\TensorFlow\workspace\training_demo\exported-models\my_mobilenet_model\saved_model``` folder. There should be an PB File called ```saved_model.pb```. This is the inference graph! I also prefer to copy the ```label_map.pbtxt``` file in to this directory because it makes things a bit easier for testing. If you forgot where the labelmap is located it should be in ```C:\TensorFlow\workspace\training_demo\annotations\label_map.pbtxt```. Since the labelmap and inference graph are organized, we are ready to test! 

### Evaluating the Model (Optional)

If you want to measure model metrics such as IoU, mAP, Recall, and Precision, you'll want to complete this step. The most up-to-date TensorFlow Documentation for evaluating the model will be located [here](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_training_and_evaluation.md#evaluation)

You should still be in your ```TensorFlow/workspace/training_demo``` but if not cd into it with

```
cd C:\TensorFlow\workspace\training_demo
```

Now just run the following command to following command for evaluation

```
python model_main_tf2.py --pipeline_config_path models\my_ssd_mobilenet_v2_fpnlite\pipeline.config --model_dir models\my_ssd_mobilenet_v2_fpnlite --checkpoint_dir models\my_ssd_mobilenet_v2_fpnlite --alsologtostderr
```

**Note that if you get an error similar to ```TypeError: object of type <class 'numpy.float64'> cannot be safely interpreted as an integer```, just downgrade your NumPy version. For me, version 1.17.3 worked so you can install it with ```pip install numpy==1.17.3```**

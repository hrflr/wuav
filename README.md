# WildUAV: Monocular UAV Dataset for Depth Estimation Tasks

<img width="49%, text-align:center" src="res/0004.gif"/> <img width="49%, text-align:center" src="res/0130.gif"/>

-----

This repository contains details for the WildUAV dataset. The dataset was developed for enabling research on visual perception tasks in the aerial domain, with a focus on deep learning approaches for depth estimation. The data is comprised of high-resolution **RGB images**, **depth** information and accurate **positioning** information.

Before downloading and using this dataset please read [WildUAV_License.pdf](WildUAV_License.pdf) and make sure your use follows the terms of the dataset's license.

The data itself can be accessed at [this link](https://forms.office.com/r/teEEnkByr9).

If you use or find our dataset useful, please cite the associated :link: [paper](https://users.utcluj.ro/~horatiuflorea/private/dc/wuav_prefinal.pdf):

```bibtex
@article{florea2021wilduav,
  title={WildUAV: Monocular UAV Dataset for Depth Estimation Tasks},
  author={Florea, Horatiu and Miclea, Vlad-Cristian and Nedevschi, Sergiu},
  journal={2021 IEEE 17th International Conference on Intelligent Computer Communication and Processing (ICCP)},
  year={2021},
  organization={IEEE}
}
```

#### Update (January 2022): Semantic segmentations added

The dataset's scope has been extended to also cover **semantic segmentation** tasks by the inclusion of manual semantic annotations. If you use these segmentations, please also cite the :link: [paper]():

```bibtex
@article{blaga2022forrestinspection,
  title={Forest Inspection Dataset for Aerial Semantic Segmentation and Depth Estimation},
  author={B. C. Z. Blaga and Nedevschi, Sergiu},
  journal={Pre-Print},
  year={2022}
}
```


### Description

> Acquiring scene depth information remains a crucial step in most autonomous navigation applications, enabling advanced features such as obstacle avoidance and SLAM. In many situations, extracting this data from camera feeds is preferred to the alternative, active depth sensing hardware such as LiDARs. Like in many other fields, Deep Learning solutions for processing images and generating depth predictions have seen major improvements in recent years. In order to support further research of such techniques, we present a new dataset, WildUAV, consisting of high-resolution RGB imagery for which dense depth ground truth data has been generated based on 3D maps obtained through photogrammetry. Camera positioning information is also included, along with additional video sequences useful in self-supervised learning scenarios where ground truth data is not required. Unlike traditional, automotive datasets typically used for depth prediction tasks, ours is designed to support on-board applications for Unmanned Aerial Vehicles in unstructured, natural environments, which prove to be more challenging.


## Dataset contents

The shared folder contains separate sub-folders for the **Mapping** (with available depth ground truth) and **Video** sets. All files are archived using 7zip. The file *archives.txt* lists all the archive names.

#### Mapping set

Comprised of 4 sequences (*seq00* -> *seq03*). For each sequence *XX*, color images and corresponding metadata are packed into *seqXX_img+metadata.7z.YYY* archives while depth files are packed into archives *seqXX_depth.7z.YYY*. In both cases *YYY* refer to the part of the archive, as they are split for ease of transfer. The total unpacked size of the Mapping set is 175GB.

Frames in each sequence are identified by a 6-digit number (e.g. *000000*, *000001*, ...). After unpacking, each sequence folder should contain three sub-folders: *img*, *metadata* and *depth*. The *train.txt* and *val.txt* files define the training splits - each file listed in any of the two is ensured to have a predecessor and a successor, enabling the typical 3 file window training strategy for self-supervised methods.

#### Video set

Comprised of 11 sequences (*vid00* -> *vid10*). Each sequence contains one or more subsequences (frames where camera was stationary or moved with high speed between subsequences have been removed). Image filenames conform to the *vidXX_YYY_ZZZZZ.jpg*, where *XX* refers to the sequence, *YYY* to the subsequence and *ZZZZZ* is the image index. The folder structure reflects the sequence-subsequence organization, while the archives are again split. The total unpacked size of the Video set is 16GB.

## Data format

Mapping images are provided as 24-bit PNG files, with the resolution of 5280x3956. Video images are provided as JPG files at a resolution of 3840x2160.

#### Depth

Depth information is stored in *.npy* files which can be easily read using the *numpy.load()* function.

#### Semantic information

Semantic segmentation annotations are provided for the images in the Mapping set as *.png* files, packaged in separate archives. There are 16 possible class labels detailed in the *label_encoding.txt* file. Semantic segmentations are also provided for video sequence *vid02*, where labels from manually annotated keyframes were propagated automatically to other frames (see paper for more details about methodology).

Class               |    Label
-------------------:|:---------------------- 
Sky                 |    \[0, 255, 255\]
Deciduous trees     |    \[0, 127, 0\]
Coniferous trees    |    \[19, 132, 69\]
Fallen trees        |    \[0, 53, 65\]
Dirt ground         |    \[130, 76, 0\]
Ground vegetation   |    \[152, 251, 152\]
Rocks               |    \[151, 126, 171\]
Water plane         |    \[0, 0, 255\]
Building            |    \[250, 150, 0\]
Fence               |    \[115, 176, 195\]
Road                |    \[128, 64, 128\]
Sidewalk            |    \[255, 77, 228\]
Static car          |    \[123, 123, 123\]
Moving car          |    \[255, 255, 255\]
People              |    \[200, 0, 0\]
Empty               |    \[0, 0, 0\]

#### Metadata

The mapping set provides for each image an associated *.json* metadata file, which contains: 
 * image dimensions
 * camera intrinsic matrix 
 * camera rotation matrix
 * camera translation vector

 The matrices are saved in *column* order (i.e. {{Col1},{Col2},...}). The RGB images have been undistorted prior to annotation and depth computation. The translation is given in the form X,Y,HeightAboveSeaLevel. The correct way of applying the translation and rotation for getting the position of a world point in the local camera coordinate system is:
 ```
 point = [wX wY wZ] \\world point
 pLocal = (point-translation)*rotation \\local point
 ``` 

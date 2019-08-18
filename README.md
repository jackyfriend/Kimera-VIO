# SparkVIO: Open-Source Visual Inertial Odometry

[![Build Status](http://ci-sparklab.mit.edu:8080/buildStatus/icon?job=VIO/master)](http://ci-sparklab.mit.edu:8080/job/VIO/job/master/)

## What is SparkVIO?

SparkVIO is a Visual Inertial Odometry pipeline for accurate State Estimation from **Stereo** + **IMU** data. (Mono-only capabilities soon to be released).

We kindly ask to cite the papers below if you find this library useful:

 - **TODO:** add our latest paper.

Backend optimization is based on:

 - C. Forster, L. Carlone, F. Dellaert, and D. Scaramuzza. **On-Manifold Preintegration Theory for Fast and Accurate Visual-Inertial Navigation**. IEEE Trans. Robotics, 33(1):1-21, 2016.

 - L. Carlone, Z. Kira, C. Beall, V. Indelman, and F. Dellaert. **Eliminating Conditionally Independent Sets in Factor Graphs: A Unifying Perspective based on Smart Factors.** IEEE Intl. Conf. on Robotics and Automation (ICRA), 2014.

Alternatively, the `Regular VIO` backend, using structural regularities, is described in this paper:

- A. Rosinol, T. Sattler, M. Pollefeys, and L. Carlone. **Incremental Visual-Inertial 3D Mesh Generation with Structural Regularities**. IEEE Int. Conf. on Robotics and Automation (ICRA), 2019.

## Demo

<div style="text-align:center">
<img src="docs/media/spark_vio_release.gif"/>
</div>

# Installation

Tested on Mac, Ubuntu 14.04 & 16.04.


## Prerequisites:

- [GTSAM](https://github.com/borglab/gtsam) >= 4.0
- [OpenCV](https://github.com/opencv/opencv) >= 3.0
- [OpenGV](https://github.com/laurentkneip/opengv)
- [Glog](http://rpg.ifi.uzh.ch/docs/glog.html), [Gflags](https://gflags.github.io/gflags/), [Gtest](https://github.com/google/googletest/blob/master/googletest/docs/primer.md) (installed automagically).

> Note: if you want to avoid building all dependencies yourself, we provide a docker image that will install them for you. Check installation instructions in [docs/sparkvio_installation.md](./docs/sparkvio_installation.md).

## Installation Instructions

Find how to install SparkVIO and its dependencies here: [docs/sparkvio_installation.md](./docs/sparkvio_installation.md).

# Running examples

## [Euroc](http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets) Dataset

#### Download Euroc's dataset

- Download one of Euroc's datasets, for example [V1_01_easy.zip](http://robotics.ethz.ch/~asl-datasets/ijrr_euroc_mav_dataset/vicon_room1/V1_01_easy/V1_01_easy.zip).
- Unzip the dataset to your preferred directory, for example, in `~/Euroc/V1_01_easy`:
```bash
mkdir -p ~/Euroc/V1_01_easy
unzip -o ~/Downloads/V1_01_easy.zip -d ~/Euroc/V1_01_easy
```

#### Yamelize Euroc's dataset
Add `%YAML:1.0` at the top of each `.yaml` file inside Euroc.
You can do this manually or run the `yamelize.bash` script by indicating where the dataset is (it is assumed below to be in `~/Euroc`):
```bash
cd SparkVIO
bash ./scripts/euroc/yamelize.bash ~/Euroc
```

### Run SparkVIO in Euroc's dataset

Using a bash script bundling all command-line options and gflags:

```bash
cd SparkVIO
./scripts/stereoVIOEuroc.bash -p "PATH_TO_DATASET/V1_01_easy"
```

> Alternatively, one may directly use the executable in the build folder:
`./build/stereoVIOEuroc`. Nevertheless, check the script `./scripts/stereoVIOEuroc.bash` to understand what parameters are expected, or check the [parameters](#Parameters) section.

## [Kitti](http://www.cvlibs.net/datasets/kitti/raw_data.php) Dataset

#### Download Kitti's dataset

- Download raw data from [Kitti ](http://www.cvlibs.net/datasets/kitti/raw_data.php?type=residential) (in order to have IMU messages). For example:
  - Download unsynced + unrectified raw dataset (e.g. [2011\_09\_26\_drive\_0005\_extract](https://s3.eu-central-1.amazonaws.com/avg-kitti/raw_data/2011_09_26_drive_0005/2011_09_26_drive_0005_extract.zip)).
  - Download as well the calibration data (three files) from raw dataset (e.g. [2011\_09\_26\_calib](https://s3.eu-central-1.amazonaws.com/avg-kitti/raw_data/2011_09_26_calib.zip)), and save them inside the folder `2011_09_26`.

### Run SparkVIO in Kitti's dataset

- Run: `./scripts/stereoVIOEuroc.bash -p PATH_TO_DATASET -d 1` where you specify the path to the dataset (e.g. path to '2011_09_26_drive_0005_extract' folder).

# Parameters
SparkVIO accepts two sources of parameters:
- YAML files: contains parameters for Backend and Frontend.
- [gflags](https://gflags.github.io/gflags/) contains parameters for all the rest.

To get help on what each gflag parameter does, just run the executable with the `--help` flag: `./build/stereoVIOEuroc --help`. You should get a list of gflags similar to the ones [here](./docs/gflags_parameters.md).

  - Optionally, you can try the VIO using structural regularities, as in [Toni's thesis](https://www.research-collection.ethz.ch/handle/20.500.11850/297645), by specifying the option ```-r```: ```./stereoVIOEuroc.bash -p "PATH_TO_DATASET/V1_01_easy" -r```

Tips for usage
----------------------
- The 3D Visualization window implements the following keyboard shortcuts (you need to have the window in focus: click on it):
    - Press `t`: toggle freezing visualization.
    - Press `v`: prints pose of the current viewpoint of the 3D visualization window.
    - Press `w`: prints to the terminal the size of the 3D visualization window.

     > These last two shortcuts are useful if you want to programmatically set the initial viewpoint and size of the screen when launching the 3D visualization window (this is done at the constructor of the 3DVisualizer class).

    - Do not press `q` unless you want to terminate the pipeline in an abrupt way (see #74).
    - Press `s`: to get a screenshot of the 3D visualization window.
    - Press `0`, `1`, or `2`: to toggle the 3D mesh representation (only visible if the gflag `visualize_mesh` is set to true).
    - Press `a`: to toggle ambient light for the 3D mesh (gflag `visualize_mesh` has to be set to true).
    - Press `l`: to toggle lighting for the 3D mesh (gflag `visualize_mesh` has to be set to true).

- The 3D Visualization allows to load an initial `.ply` file.
This is useful for example for the Euroc dataset `V1_01_*` where we are given a point-cloud of the scene. Note that the `.ply` file must have the following properties in the header, and its corresponding entries:
  - A vertex element: `element vertex 3199068` (actual number depends on how many vertices has the ply file).
  - A face element: `element face 0`(actual number depends on how many faces has the ply file)
  - Properties may vary, as for now, any given `.ply` file will be displayed as a point-cloud, not a mesh (due to some weirdness in opencv).
  - For example, the ground-truth point-cloud provided in `V1_01_easy` dataset must be modified to have the following header:
      ```
      ply
      format ascii 1.0
      element vertex 3199068
      property float x
      property float y
      property float z
      property float intensity
      property uchar diffuse_red
      property uchar diffuse_green
      property uchar diffuse_blue
      element face 0
      property list uint8 int32 vertex_indices
      end_header
      ```

Tips for development
----------------------
- To make the pipeline deterministic:
    - Disable TBB in GTSAM (go to build folder in gtsam, use cmake-gui to unset ```GTSAM_WITH_TBB```).
    - Change ```ransac_randomize``` flag in ```params/trackerParameters.yaml``` to 0, to disable ransac randomization.

> Note: these changes are not sufficient to make the output repeatable between different machines.

> Note: remember that we are using ```-march=native``` compiler flag, which will be a problem if we ever want to distribute binaries of this code.

# Contributing:

We follow the branch, open PR, review, and merge workflow.

To contribute to this repo, ensure your commits pass the linter pre-commit checks.
 To enable these checks you will need to [install linter](./docs/linter_installation.md).

# BSD License

SparkVIO is open source under the BSD license, see the [LICENSE.BSD](LICENSE.BSD) file.

SketchesSketches# Sketch3D Toolkit

__Sketch3D Toolkit__ is a lightweight sketch-based shape retrieval framework designed to enable people to make use of hand-drawn sketches as queries to retrieve large scale 3D model datasets on lightweight devices without powerful GPGPUs, such as laptops, tablets and mobiles. This project is released under the [MIT license](https://github.com/garyzhao/Sketch3DToolkit/blob/master/LICENSE), and all code is public domain software. If you use the Sketch3D Toolbox, we appreciate it if you cite an appropriate subset of the following papers:

```
@article{ZhaoTVC15views,
  author    = {Long Zhao and Shuang Liang and Jinyuan Jia and Yichen Wei},
  title     = {Learning best views of 3D shapes from sketch contour},
  journal   = {The Visual Computer},
  year      = {2015},
  volume    = {31},
  number    = {6-8},
  pages     = {765--774}
}

@inproceedings{LiangPCM14hashing,
  author    = {Shuang Liang and Long Zhao and Yichen Wei and Jinyuan Jia},
  title     = {Sketch-Based Retrieval Using Content-Aware Hashing},
  booktitle = {Proceedings of the 15th Annual Pacific-Rim Conference on Multimedia (PCM)},
  year      = {2014},
  pages     = {133--142}
}
```

## Table of contents

* [Quick start](#quick-start)
* [Project dependency](#project-dependency)
* [What's included](#whats-included)
* [Working pipeline](#working-pipeline)
* [Related projects](#related-projects)

## Quick start

This toolkit contains the C++ implementation of the core sketch-based shape retrieval algorithm, MATLAB retrieval interfaces and a retrieval performance evaluation tool suite built upon the evaluation code provided by [SHREC'13](http://www.itl.nist.gov/iad/vug/sharp/contest/2013/SBR/) and [SHREC'14](http://www.itl.nist.gov/iad/vug/sharp/contest/2014/SBR/index.html). [CMake](https://cmake.org/) is utilized to build and package the whole project.

### Setup compiling environment

All code has been tested on Windows 10 (64-bit) with CMake v3.5.1, OpenCV v2.4.12 (the pre-compiled 64-bit binaries for Windows are included in `msvc` folder), Visual Studio 2015 and MATLAB 2015b. Other platforms with compilers supporting C++11 standard are excepted to work but not guaranteed. On Windows platforms, please make sure that `cmake.exe` and `MsBuild.exe` ([MSBuild](https://msdn.microsoft.com/en-us//library/0k6kkbsd.aspx) is the build system for Visual Studio) are included in the system `PATH` environment variable before compiling.

### Download and compile the code

Run the following command to download and compile the code. Note that the CMake parameter `CMAKE_GENERATOR_PLATFORM=x64` is specified to compile all binaries and libraries in 64-bit mode. You are free to compile the code in 32-bit mode; however, since the hashing functions employed are optimized in 64-bit mode, 32-bit binaries and libraries may result in performance loss. 

```
> git clone https://github.com/garyzhao/Sketch3DToolkit.git
> cd Sketch3DToolkit
> mkdir build
> cd build
> cmake -DCMAKE_GENERATOR_PLATFORM=x64 ..
> MsBuild Sketch3DToolkit.sln /p:Configuration=Release
```

Or you can also launch Visual Studio to open the solution configuration file `Sketch3DToolkit.sln` generated by CMake and build the `ALL_BUILD` subproject with the `Release` configuration to compile the whole solution. You can find the compiled binaries and runtime/static libraries in `build/bin` folder and MEX interfaces in `build/mex` folder.

### Install the MEX files (optional)

Go to `matlab` folder and run `install.m` under `matlab` folder in MATLAB to install compiled MEX files, as well as essential OpenGL and OpenCV DLL runtime libraries, into `matlab/util` folder. __Notice__: please make sure that the MATLAB installation has been properly detected by CMake, otherwise the MEX files won't be compiled and `install.m` will be failed.

## Project dependency

The following OpenGL and 3D mesh libraries are used in this project for reading, processing and rendering different 3D models. All these dependencies are included in this repository under the `lib` folder.

* [FreeGLUT](http://freeglut.sourceforge.net/): a free-software/open-source alternative to the OpenGL Utility Toolkit (GLUT) library
* [GLEW](http://glew.sourceforge.net/): a cross-platform open-source C/C++ extension loading library
* [GLUI](http://glui.sourceforge.net/): a GLUT-based C++ user interface library which provides different controls to OpenGL applications
* [Trimesh2](http://gfx.cs.princeton.edu/proj/trimesh2/): a C++ library and set of utilities for input, output, and basic manipulation of 3D triangle meshes

## What's included

Within the download you'll find the following directories and files, logically grouping C++ header files and definition files. You'll see main folders organized like this:

* `lib`: additional OpenGL and 3D mesh library dependencies
* `matlab`: MATLAB code for sketch/model retrieving and performance evaluating
* `mex`: MEX code of MATLAB interfaces
* `module`: core C++ modules for sketch/model feature computing and indexing
* `module/hashing`: C++ code for MinHash feature hashing
* `module/imgproc`: C++ code for sketch image processing
* `module/meshutil`: C++ code for processing and showing 3D models
* `module/retrieval`: C++ code for sketch-based shape/sketch retrieval
* `msvc`: Visual Studio configuration files and pre-compiled OpenCV binaries (x64 version) for Windows
* `src`: C++ example code showing how to use 3D model retrieval/display interfaces in this project

## Working pipeline

For example, evaluating the performance of our sketch-based 3D model retrieval algorithm on the [SHREC'13](http://www.itl.nist.gov/iad/vug/sharp/contest/2013/SBR/) dataset can be done as follows:

1. Set `DATASET_DIR` variable to `'SHREC13'` in `setupvars.m`
1. Download the [complete 3D target model dataset](http://www.itl.nist.gov/iad/vug/sharp/contest/2013/SBR/data.html) of SHREC'13 and put them into `matlab\SHREC13\Models`
2. Download the [training and testing sketches dataset](http://www.itl.nist.gov/iad/vug/sharp/contest/2013/SBR/data.html) of SHREC'13 and put them into `matlab\SHREC13\Sketches`

Organize these files so that directory structure looks like this:
```
SHREC13
|-- Models
    |-- m0.off
    |-- m1.off
    |-- ...
|-- Sketches
    |-- airplane
        |-- test
            |-- 2.png
            |-- ...
        |-- train
            |-- 1.png
            |-- ...
    |-- ant
    |-- ...
```

3. Run `sbsr_select_model_views.m` to generate candidate views for all 3D models into `matlab\SHREC13\Views`
4. Run `sbsr_retrieve_model.m` to retrieve 3D models for all query sketches, and results are saved into `matlab\SHREC13\RetrievalLists`
5. Run `eval_rank_lists.m` to evaluate the retrieval performance
6. Run `eval_draw_curves.m` to show Precision-Recall Curves (PR Curves) for evaluated methods

Note that we use a MEX file to speed up the evaluation process, which is 100x faster than the original evaluation code provided by SHREC'13.

## Related projects

* [SMHasher](https://github.com/aappleby/smhasher). A test suite offering various hash functions for feature hashing used in this project.
* [SketchBasedShapeRetrieval](https://github.com/mikeroberts3000/SketchBasedShapeRetrieval). A C++/Python implementation of the shape matching pipeline in the paper "Sketch-Based Shape Retrieval", which is adapted for mesh view selection in batch mode in this project.
* [Jeroen Baert's Real-time Suggestive Contour Rendering](http://www.forceflow.be/2013/05/22/real-time-suggestive-contour-rendering/). An efficient implementation of different contour rendering methods. The line rendering algorithm in this project is built upon it. 
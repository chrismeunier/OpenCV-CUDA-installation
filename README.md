# OpenCV CUDA installation
A save of the process needed to build manually OpenCV for Python 3 with CUDA bindings on Windows 10.

## Sources and troubleshooting references

The process is based on [this tutorial by Anindya](https://thinkinfi.com/install-opencv-gpu-with-cuda-for-windows-10/) for the step-by-step process with CMake GUI and [this tutorial by James Bowley](https://thinkinfi.com/install-opencv-gpu-with-cuda-for-windows-10/) for the process with command line CMake and some troubleshooting.

#### ImportError: DLL load failed while importing cv2: The specified module could not be found.

Both of these tutorials should be enough for most people. **But** in some cases, even if the module was successfully installed in your Python installation, you may end up with this `ImportError: DLL load failed while importing cv2: The specified module could not be found.` message when importing OpenCV with `import cv2`.

This case is detailed [at the end](README.md/#Check-install-and-troubleshooting) and is almost entirely based on [this quite extensive github issue](https://github.com/opencv/opencv/issues/19972). Basically your whole installation may be completely successful, it may just be Python failing to read your environment variables.

#### Hardware and software configuration used

Tested on a Windows 10 20H2 machine with i7-10700 CPU @ 2.90GHz and GeForce RTX 2080 Ti.

Python 3.8.10, OpenCV 4.5.5, NumPy 1.21.6, CUDA toolkit v11.6, cuDNN v8.3.3, Visual Studio Community 2019 v16.11.13, CMake 3.19.1, all of this in April 2022.

## Step-by-step installation process

### Prerequisites

- Visual Studio with C++ build tools
- [CUDA](https://developer.nvidia.com/cuda-downloads) according to your GPU
- [cuDNN](https://developer.nvidia.com/rdp/cudnn-archive) corresponding to CUDA (free account creation needed to download)
- Python 3 install
- NumPy
- OpenCV [from source](https://github.com/opencv/opencv/releases) and OpenCV [contrib modules from source](https://github.com/opencv/opencv_contrib/tags)
- [CMake](https://cmake.org/download/)

Check that your GPU is compatible with CUDA: [wikipedia.org/wiki/CUDA](https://en.wikipedia.org/wiki/CUDA#GPUs_supported).

### Python, NumPy and pip

#### Python install

Install a recent Python 3.x however you prefer (Python website, Anaconda, Windows store, create a virtual env...), but if you are here that's probably already done. This "tutorial" is done with a standard Python install, from the Python website with no virtual environment. For the particularities of an Anaconda installation look at [James Bowley's tutorial](https://thinkinfi.com/install-opencv-gpu-with-cuda-for-windows-10/).

Make sure you have NumPy installed or install it with `pip install numpy`. Uninstall any OpenCV python module `pip uninstall opencv-python` or `pip uninstall opencv-contrib-python`. Delete the `YOUR_PYTHON_PATH/Lib/site-packages/cv2` folder for cleaning purposes.

### Visual Studio

Download Visual Studio ([2019 version here](https://visualstudio.microsoft.com/fr/vs/older-downloads/#visual-studio-2019-and-other-products)) and install the build tools for C++ from the Visual Studio Installer.

![setup_586tzl0ZlK](https://user-images.githubusercontent.com/28230243/166432249-2315dc23-3806-4da8-a164-3a2f826b50e0.png)


### CUDA and cuDNN

Download and install the latest [CUDA toolkit](https://developer.nvidia.com/cuda-downloads) compatible with your GPU ([see here](https://en.wikipedia.org/wiki/CUDA#GPUs_supported) for compatibility as well) or check you already have it installed in `C:\Program Files\NVIDIA GPU Computing Toolkit`.

Idem for [cuDNN](https://developer.nvidia.com/rdp/cudnn-archive) with an intermediary step to create a NVIDIA developer account, fill up their survey etc.

Check in your environment variables that `CUDA_PATH` and `CUDA_PATH_Vxx_x` are here and pointing to your install path.

Copy the files in the cuDNN folders (under `C:\Program Files\NVIDIA\CUDNN\vX.X`) **bin**, **include** and **lib/x64** to the corresponding folders in your CUDA folder.

### OpenCV

Download and extract matching versions of OpenCV and OpenCV-contrib from the [links above](README.md#prerequisites).

### CMake configuration

#### Preparation

Prepare a **"build"** folder with your OpenCV extracted folders.

![explorer_bSMon2LQY0](https://user-images.githubusercontent.com/28230243/166434918-458aa3ae-8696-4cee-bb8e-5d9713401147.png)

Edit the end of the _OpenCVDetectPython.cmake_ file in _opencv-x.x.x/cmake_. Move the second _elseif_ above the first to get this:

![notepad++_Zwz4Lsl2kZ](https://user-images.githubusercontent.com/28230243/166435763-da8d2429-ba15-45ab-aede-ec61a6715cbc.png)

This will prioritize the use of your Python 3 installation for the build.

#### CMake GUI build configuration

Provide the paths to the OpenCV and target build folders:

![cmake-gui_IBQybmF6kh](https://user-images.githubusercontent.com/28230243/166436165-126efd0b-43e5-4d1a-9e8a-1d46dbec7f35.png)

Hit _Configure_ and select _x64_ as the _Optional platform for generator_, then hit finish to start the first round of configuration.

Once this is done edit the following parameters:
| Name | Value |
|---|:---:|
| ENABLE_FAST_MATH | ✅ |
| OPENCV_DNN_CUDA | ✅ |
| OPENCV_EXTRA_MODULES_PATH | path of **modules** directory in extracted opencv_contrib-x.x.x |
| OPENCV_PYTHON3_VERSION | ✅ |
| WITH_CUDA | ✅ |
<!--| BUILD_SHARED_LIBS | 🔳 |-->

Check the PYTHON3_... parameters so that the paths correspond to what you expect.
Note that the path separator in OPENCV_EXTRA_MODULES_PATH (or any other parameter value) has to be "/" and _not "\\"_.

Hit _Configure_ again.

Edit two more parameters:
| Name | Value |
|---|:---:|
| CUDA_FAST_MATH | ✅ |
| CUDA_ARCH_BIN | x.x |

The CUDA_ARCH_BIN corresponding to your GPU is the value found in the left column of the [GPU support table](https://en.wikipedia.org/wiki/CUDA#GPUs_supported). For instance "7.5" for the RTX 2080 Ti.

![firefox_pEBLFW3y2g](https://user-images.githubusercontent.com/28230243/166440728-1e63a0ed-6340-4b85-b350-448274c3d077.png)

Hit _Configure_ for the final configuration round.
Once the configuration is done you should not have any parameter left in red.
Now hit _Generate_. When generation is finished we are done with CMake.

### Build the project with Visual Studio

Open the `OpenCV.sln` just created in the build folder.

Go in Tools>Options... in "Projects and Solutions > Web Projects" uncheck the last parameter. Continue if it was already unchecked, otherwise close Visual Studio and reopen `OpenCV.sln`.

![devenv_TVsR0HP4yc](https://user-images.githubusercontent.com/28230243/166442847-060bb8cc-2333-4fc9-8f73-24749f233e60.png)

Change the "Debug" mode to "Release".

![debug2release](https://user-images.githubusercontent.com/28230243/166443402-9e1cdd4b-245e-4d0e-a202-6e1b3b9edac1.png)

In the solution explorer expand **CMakeTargets**, right-click **ALL_BUILD** and select **Build**. This will take about half an hour.

![devenv_vwYMW4osmJ](https://user-images.githubusercontent.com/28230243/166444051-d75deecb-eb99-42ff-a184-b2709e285f7b.png)

Then repeat the step for **INSTALL** (right below **ALL_BUILD**).
Check for errors in the two building steps, if everything is fine you are done.

### Check install and troubleshooting

First thing to do open your preferred way of executing some Python code and try this:
```python
import cv2
print(cv2.__version__)
print(cv2.cuda.getCudaEnabledDeviceCount())
```

If it works, congratulations you are good to go!

If not let's tackle the problem. The _problem_ being `ImportError: DLL load failed while importing cv2: The specified module could not be found.`. For other bugs and problems I refer you to the [troubleshooting section of James Bowley's tutorial](https://jamesbowley.co.uk/accelerate-opencv-4-4-0-build-with-cuda-and-python-bindings/#troubleshooting).

#### Is everything in place ?

You should have a "cv2" folder in your python installation (under `your_python_path/Lib/site-packages`). If not check if you have a "binding" folder in the Visual Studio solution.
Otherwise I suggest trying to change two parameters in the CMake configuration: BUILD_SHARED_LIBS 🔳 and OPENCV_FORCE_PYTHON_LIBS ✅. Then re-generate and re-build everything.

#### Is CV2 detected ?

In an IDE with code suggestion (VS Code for instance) try to type `import cv2`, then write `cv2.` and see if suggestions appear. If they do your Python installation can successfully access OpenCV.

![image](https://user-images.githubusercontent.com/28230243/166449328-477e9fb3-d192-4615-aa31-9b6c89231eec.png)

#### OpenCV libraries

Check that the libraries installed by your build are not causing the import error. To do this you can add manually the DLL files path to a script:
```python
import os
os.add_dll_directory('C:/path_to_opencv_build_folder/install/x64/vc16/bin')
import cv2
```
This import should be done by default in the `config.py` file in the `cv2` folder and should probably not solve the issue by itself.

#### External libraries

The problem is most likely linked to other libraries not loaded by Python _even if they are in your PATH environment variables_. You can troubleshoot this by adding all the PATH variables to the script with `os.add_dll_directory()` until it works or use the [Dependency walker](https://www.dependencywalker.com/) to find which DLLs you are missing.

A solution highlighted in the [github issue](https://github.com/opencv/opencv/issues/19972) mentioned in the intro of this README was that using an Anaconda Python install made it work, so having a Python 3.8 Anaconda install I added the `C:/Users/username/Anaconda3/Library/bin` path to my script and voilà, it works! (for me at least)

It turns out the only missing libraries were `hdf5.dll` and `zlib.dll` out of the >200 DLL files located there. So they are here in this repository if you do not want to install Anaconda to not use it.

Once you have located the folders containing your missing DLLs you have a few options to permanently solve the import error:

- copy the files to your `path_to_opencv_build_folder/install/x64/vc16/bin` folder (easy but not clean)
- add the `import os` and `os.add_dll_directory('...')` to any script using OpenCV (ok but not convenient)
- add all the needed `os.add_dll_directory()` in the `__init__.py` file of `cv2` right after the `__all__ = []` line (cleanest but make it clear!)

If some part of this solved your `ImportError: DLL load failed while importing cv2: The specified module could not be found.` then great! Otherwise I suggest going thoroughly through the [github issue](https://github.com/opencv/opencv/issues/19972) for more ideas. Feel free to make any remarks, I will update this page if need be.

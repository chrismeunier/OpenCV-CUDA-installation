# OpenCV-CUDA-installation
A save of the process needed to build manually OpenCV for Python 3 with CUDA bindings on Windows 10. Tested on a Windows 10 20H2 machine with i7-10700 CPU @ 2.90GHz and GeForce RTX 2080 Ti.

## Sources and troubleshooting references

The process is based on [this tutorial by Anindya](https://thinkinfi.com/install-opencv-gpu-with-cuda-for-windows-10/) for the step-by-step process with CMake GUI and [this tutorial by James Bowley](https://thinkinfi.com/install-opencv-gpu-with-cuda-for-windows-10/) for the process with command line CMake and some troubleshooting.

### ImportError: DLL load failed while importing cv2: The specified module could not be found.

Both of these tutorials should be enough for most people. **But** in some cases, even if the module was successfully installed in your Python installation, you may end up with this `ImportError: DLL load failed while importing cv2: The specified module could not be found.` message when importing OpenCV with `import cv2`.

This case is detailed at the end and is almost entirely based on [this quite extensive github issue](https://github.com/opencv/opencv/issues/19972). Basically your whole installation may be completely successful, it may just be Python failing to read your environment variables.

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

Install a recent Python 3.x (3.8.10 here) however you prefer (Python website, Anaconda, Windows store, create a virtual env...), but if you are here that's probably already done.

Make sure you have NumPy installed or install it with `pip install numpy`. Uninstall any OpenCV python module `pip uninstall opencv-python` or `pip uninstall opencv-contrib-python`. Delete the `YOUR_PYTHON_PATH/Lib/site-packages/cv2` folder for cleaning purposes.

### Visual Studio

Download Visual Studio ([2019 version here](https://visualstudio.microsoft.com/fr/vs/older-downloads/#visual-studio-2019-and-other-products)) and install the build tools for C++ from the Visual Studio Installer.

![setup_586tzl0ZlK](https://user-images.githubusercontent.com/28230243/166432249-2315dc23-3806-4da8-a164-3a2f826b50e0.png)


### CUDA and cuDNN


### OpenCV

### CMake configuration

### Build the project with Visual Studio

### Check install and troubleshooting




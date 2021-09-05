The source file is located at [here](https://monkeysaudio.com/files/MAC_SDK_661.zip).  
You can include this `CMakeLists.txt` in any project without any limitations.  
More info: https://monkeysaudio.com/developers.html  
# Usage
Put `CMakeLists.txt` in the directory of source tree.  
Then run cmake.  
## cmake options
| Options | Description | Default Value |
|:-------:|:-----------:|:-------------:|
| `LINK_STD_STATIC` | Link standard library staticly. | `OFF` |
| `LINK_ALL_STATIC` | Link everything staticly. <br> On MSVC, this option equals to `LINK_STD_STATIC`. | `OFF` |
| `BUILD_SHARED_LIBS` | Whether to build static or shared library. <br> This option don't work on WIN32 platform. | `OFF` |
| `CMAKE_BUILD_TYPE` | Whether to build in release or debug mode. | `Release` |

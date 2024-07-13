# Install ruby-opencv with RubyInstaller 3.1.6 on Windows 11

This document shows how to install ruby-opencv with RubyInstaller 3.1.6 on Windows 11.

Official OpenCV binary for MinGW32 is no longer distributed, so when you use ruby-opencv with RubyInstaller, you should build OpenCV yourself using Devkit.


## Requirement
- Windows 11 64bit
- [RubyInstaller devkit 3.1.6-1 x64](https://github.com/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.1.6-1/rubyinstaller-devkit-3.1.6-1-x64.exe)
- [OpenCV 2.4.13.7](https://github.com/opencv/opencv/archive/refs/tags/2.4.13.7.zip)
- [CMake 3.30.0 x64](https://github.com/Kitware/CMake/releases/download/v3.30.0/cmake-3.30.0-windows-x86_64.msi)


## How to install OpenCV and ruby-opencv
### Install Ruby 3.1.6 with RubyInstaller devkit 3.1.6-1 x64
- Download RubyInstaller devkit 3.1.6-1 x64 from https://github.com/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.1.6-1/rubyinstaller-devkit-3.1.6-1-x64.exe
- Open ```cmd.exe``` and run the following commands.
  - ridk install 1,3

### Download and extract OpenCV

Download OpenCV library from https://github.com/opencv/opencv/archive/refs/tags/2.4.13.7.zip and extract it.

### Create OpenCV Makefile with CMake

Open ```cmd.exe``` and run the following commands.

```
C:\>set DEVKIT_PATH=C:/Ruby31-x64/msys64/ucrt64/bin
C:\>set SOURCE_PATH=C:/opencv-2.4.13.7
C:\>set OPENCV_INSTALL_PATH=C:/opencv-2.4.13.7/install
C:\>mkdir C:\opencv-build
C:\>cd C:\opencv-build
C:\opencv-build>ridk enable
C:\opencv-build>cmake %SOURCE_PATH% -G"MinGW Makefiles" -DCMAKE_INSTALL_PREFIX=%OPENCV_INSTALL_PATH% -DCMAKE_MAKE_PROGRAM=%DEVKIT_PATH%/mingw32-make.exe -DCMAKE_C_COMPILER=%DEVKIT_PATH%/gcc.exe -DCMAKE_CXX_COMPILER=%DEVKIT_PATH%/g++.exe -DBUILD_PERF_TESTS=OFF -DCMAKE_CXX_STANDARD=14 -DCMAKE_CXX_STANDARD_REQUIRED=ON -DWITH_FFMPEG=OFF -DWITH_DSHOW=OFF
```

In this document, I assume that the OpenCV library is extracted to ```C:/opencv-2.4.13.7``` and the output will be installed in ```C:/opencv-2.4.13.7/install```.

Note that ```C:\>``` is a prompt of ```cmd.exe```, so you should not input it.


### Build OpenCV with DevKit

Run the following commands in ```cmd.exe```.

```
C:\>cd C:\opencv-build
C:\opencv-build>ridk enable
C:\opencv-build>mingw32-make.exe -j 16
C:\opencv-build>mingw32-make.exe install
C:\opencv-build>cd C:\
C:\>rm -rf C:\opencv-build
```


### Install ruby-opencv

Run the following commands in ```cmd.exe```.

```
C:\>git clone git@github.com:Youngv/ruby-opencv.git
C:\>cd ruby-opencv
C:\ruby-opencv>git checkout master
C:\ruby-opencv>bundle install
C:\ruby-opencv>rake gem:precompile CONFIG=config.yml --trace --verbose
C:\ruby-opencv>gem install ruby-opencv-0.0.18-mingw32.gem
```

And try to use ruby-opencv.

```
C:\>SET "RUBY_DLL_PATH=C:/opencv-2.4.13.7/install/x64/mingw/bin"
C:\>ruby -e 'require "opencv"; puts OpenCV::CV_VERSION'
=> "2.4.13.7"
```

or

```
RubyInstaller::Runtime.add_dll_directory('C:/opencv-2.4.13.7/install/x64/mingw/bin')
require 'opencv'
puts OpenCV::CV_VERSION
```

Note that the paths ```%OPENCV_INSTALL_PATH%/x64/mingw/bin``` must **ALWAYS** be set when using ```ruby-opencv```, or you may get the following error.

```
C:\>irb
irb(main):001:0> require 'opencv'
<internal:C:/Ruby31-x64/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require': 126: 找不到指定的模块。   - C:/Ruby31-x64/lib/ruby/site_ruby/3.1.0/x64-ucrt/opencv.so (LoadError)
        from <internal:C:/Ruby31-x64/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require'
        from (irb):1:in `<main>'
        from C:/Ruby31-x64/lib/ruby/gems/3.1.0/gems/irb-1.4.1/exe/irb:11:in `<top (required)>'
        from C:/Ruby31-x64/bin/irb:33:in `load'
        from C:/Ruby31-x64/bin/irb:33:in `<main>'
```

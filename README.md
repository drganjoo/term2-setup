# Visual Studio 2015/17 Setup for Term2

[//]: # (Image References)
[copy_to_ports]: ./images/copy_to_ports.png
[uws_include]: ./images/uws_include.png
[cmake_to_path]: ./images/cmake_to_path.png
[cmake_output]: ./images/cmake_output.png
[build_folder]: ./images/build_folder.png
[include_dir]: ./images/include_dir.png
[library_dir]: ./images/library_dir.png
[startup]: ./images/startup.png
[connected]: ./images/connected.png
[rmse]: ./images/rmse.png
[debug]: ./images/debug.png
[properties]: ./images/properties.png
[errors]: ./images/errors.png
[matrix]: ./images/matrix.png
[matirx_with_natvis]: ./images/matirx_with_natvis.png
[fixed_matirx]: ./images/fixed_matrix.png
[eigen_natvis_added]: ./images/eigen_natvis_added.png

## Install cmake

Download and install cmake:

https://cmake.org/files/v3.8/cmake-3.8.2-win64-x64.msi

Choose Add CMake to User Path so that you can access it from the command line:

![cmake_to_path]

## vcpkg installation

Clone vcpkg from https://github.com/Microsoft/vcpkg.git into c:\vcpkg (or any folder of your choice)

Run bootstrap

```
C:\vcpkg> .\bootstrap-vcpkg.bat
```

Open command prompt as **administrator** and run:

```
C:\vcpkg> .\vcpkg integrate install
```

Install pre-requisites:

```
C:\vcpkg> .\vcpkg install openssl zlib libuv
```

## Install uWebSockets commit e94b6e1

Since udacity's project are not using the latest master branch of uWebSockets, we need to install the same commit as is done for Ubuntu/Mac. I've already compiled the particular commit and have included the library/include files in [carnd-term2-libs-ports.zip](https://raw.githubusercontent.com/drganjoo/term2-setup/master/carnd-term2-libs-ports.zip). Just unzip and copy the 'carnd-term2-libs' folder from the zip file to c:\vcpkg\ports

![copy_to_ports]

Install carnd-term2-libs using:

```
C:\vcpkg> .\vcpkg install carnd-term2-libs
```

Make sure you have a uWS folder in C:\vcpkg\installed\x86-windows\include:

![uws_include]

## CarND-Exended-Kalman Project

```
git clone https://github.com/udacity/CarND-Extended-Kalman-Filter-Project.git
```

### Slight changes to CMakeLists.txt to accomodate Win32

Towards the very bottom where target_link_libraries are mentioned, change:

```
add_executable(ExtendedKF ${sources})

target_link_libraries(ExtendedKF z ssl uv uWS)
```

To:

```
if (WIN32)
    set(VCPKG_INSTALL ${_VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET})
    message("Using vcpkg_install ${VCPKG_INSTALL}")
    include_directories(${VCPKG_INSTALL}/include)
    link_directories(${VCPKG_INSTALL}/lib)
endif()

add_executable(ExtendedKF ${sources})


if (NOT WIN32)
    target_link_libraries(ExtendedKF z ssl uv uWS)
else()
    target_link_libraries(ExtendedKF ssleay32 libuv uWS ws2_32)
    target_link_libraries(ExtendedKF optimized zlib )
	target_link_libraries(ExtendedKF debug zlibd )
endif()
```

Or you can download the [CMakeLists.txt](CMakeLists.txt) and use that.

### Generate project files using cmake

```
cd CarND-Extended-Kalman-Filter-Project
mkdir build
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=c:\vcpkg\scripts\buildsystems\vcpkg.cmake -G "Visual Studio 14"
```

![cmake_output]
![build_folder]

Open Visual Studio 2015 and load ExtendedKF.sln from the build directory. Once loaded, make sure that the include directory is correctly set by right clicking on ExtendedKF Project in the solution explorer and choose properties:

![properties]

![include_dir]

Also, make sure Linker is set correctly:

![library_dir]

Build the project, you will get about 37 warnings related to code written inside uWS and two errors for our own project, which makes sense since we need to finish those functions before the code can compile.

![errors]

For testing purposes, open tools.cpp and complete those two functions. e.g. just return 1,2,3,4 as RMSE:

```
VectorXd Tools::CalculateRMSE(const vector<VectorXd> &estimations,
	const vector<VectorXd> &ground_truth) {
	/**
	TODO:
	* Calculate the RMSE here.
	*/

	VectorXd temp(4);
	temp << 1, 2, 3, 4;
	return temp;
}

MatrixXd Tools::CalculateJacobian(const VectorXd& x_state) {
	/**
	TODO:
	* Calculate a Jacobian here.
	*/

	MatrixXd temp(3,4);
	return temp;
}
```

Build the project again. Hopefully you should not get any errors.

### Specify 127.0.0.1 for simulator to connect

One minor change to main.cpp for the simulator to connect on windows:

Open main.cpp line #175:

Change From:

```
  if (h.listen(port))
```

to:

```
  if (h.listen("127.0.0.1", port))

```

Mark ExtendedKF as the startup project (Right click ExtendedKF in the solution explorer and choose "Set as Startup Project").

![startup]

**Copy Zlib1.dll** There is a bug some where and zlib1.dll is not automatically copied to the output folder. So please copy C:\vcpkg\installed\x86-windows\bin\zlib1.dll to CarND-Extended-Kalman-Filter-Project\build\Debug folder

Press F5 (or menu option Debug->Start Debugging)

## Launch Simulator

Download v1.4 from [term2_sim_windows](https://github.com/udacity/self-driving-car-sim/releases)

Start simulator and as soon as you click on "Select" button you should see "Connected!!!" on the output window.

![connected]

Click on start and you should see 1,2,3,4 as the RMSE:

![rmse]

## Debug

You can put a breakpoint on line # 69 of main.cpp and re-run the program (F5) and re-run the simulator.

The program should stop when it gets data from the simulator and you should be able to inspect values in the debugger local / auto window.

![debug]

## Using eigen.natvis

For easier debugging, download eigen.natvis and add that to the solution:

https://bitbucket.org/eigen/eigen/raw/246af84e462db0dd95ca68f8b5cca6e496b71cf3/debug/msvc/eigen.natvis

![eigen_natvis_added]

**Without eigen.natvis**:

![matrix]

**With eigen.natvis** added to the solution, Dynamic Matrix

![matirx_with_natvis]

**With eigen.natvis** added to the solution, Fixed Matrix

![fixed_matirx]

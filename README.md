# Visual Studio 2015/17 Setup for Term2

[//]: # (Image References)
[ports_folder]: ./images/ports_folder.png

## vcpkg installation

Clone vcpkg from https://github.com/Microsoft/vcpkg.git into c:\vcpkg (or any folder of your choice)

Run bootstrap

```
C:\src\vcpkg> .\bootstrap-vcpkg.bat
```

Open command prompt as administrator and run:

```
C:\src\vcpkg> .\vcpkg integrate install
```

Install pre-requisites:

```
C:\src\vcpkg> .\vcpkg install openssl zlib libuv
```

Download uws-term2.zip [uws-term2-port.zip](https://raw.githubusercontent.com/drganjoo/term2-setup/master/uws-term2-ports.zip) and copy the 'port' folder to c:\vcpkg


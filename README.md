
Build and/or simply download the Boost C++ Libraries for the Android platform, with Google's Ndk.

The [Boost C++ Libraries](http://www.boost.org/), are possibly *the* most popular and generally useful c++ libraries. It would be nice to be able to use them when developing (native c++ or hybrid java/c++ with Google's [Ndk](https://developer.android.com/ndk/)) apps and/or libraries for Android devices.
The Boost libraries are written to be cross platform, and are available in source code format. However, building the libraries for a given target platform is not a very simple or cross platform experience, at least in practice. Building the Boost libraries for Android can be very difficult and time consuming. This project aims to lower the barrier by offering a simple customizable build script you can use to build Boost for Android (abstracting away all the details of the underlying custom boost build system), and even providing standard prebuilt binaries to get you started fast.

Tested with **Boost 1.69.0** and **Google's Ndk 19c**  (current versions as of March 2019).

Building on a Linux/Docker machine is officially supported. Mac and Windows should work fine too but the details of setting up the relevant environments (eg. Cygwin or Homebrew) is beyond the scope of what this project tries to do.

If you want to use an operating system other than Linux/Docker when building, the easiest option is to use virtual machines. On your OS of choice install VirtualBox, and with it create a Linux virtual machine with where you do the building. No matter what OS you use to build with, the resulting binaries can then be copied to any other, and used from then on as if you had built on there to start with (theyre cross compiled *for* android and have no memory of *where* they were built).

Works with **clang** (llvm) 
*- as of ndk 16 google no longer supports gcc*.

Creates binaries for multiple abis (**armeabi-v7a**, **arm64-v8a**, **x86**, **x86_64**).


*Tested with a development machine running Ubuntu 18.04.*

## Prebuilt
You can just download a current set of standard prebuilt binaries [here](https://github.com/dec1/Boost-for-Android/releases) if you don't need to customize the build, or don't have access to a unix-like development machine. 
<!--- [here](http://silverglint.com/boost-for-android/) --->

## Build Yourself

### Docker
The easiest way to build is to use [docker](https://www.docker.com) with the [Dockerflile](./docker/Dockerfile) provided. This way you need not download or install anything (including android studio, ndk, boost source code ) on your host machine (except docker itself). See the top of the Dockerfile for instructions.

### Build on you linux host
* For prerequisites  also see the [Dockerflile](./docker/Dockerfile) (even though the rest of these instructions don't use docker)
* Download the [boost source](https://www.boost.org) and extract to a directory of the form *..../major.minor.patch* 
  eg */home/declan/Documents/zone/mid/lib/boost/1.69.0*
  
  *__Note__:* After the extarction *..../boost/1.69.0* should then be the direct parent dir of "bootstrap.sh", "boost-build.jam" etc


```
> ls /home/declan/Documents/zone/mid/lib/boost/1.69.0
boost  boost-build.jam  boostcpp.jam  boost.css  boost.png  ....
``` 

* Clone this repo:

```
> git clone https://github.com/dec1/Boost-for-Android.git ./boost_for_android
``` 


* Modify the paths (where the ndk is) and variables (which abis you want to build for, which compiler to use etc) in *doIt.sh*, and execute it. If the build succeeds then the boost binaries should then be available in the dir *boost_for_android/build*

```
> cd boost_for_android
> ./doIt.sh
```



* *__Note__:* If for some reason the build fails you may want to manually clear the */tmp/ndk-your_username* dir (which gets cleared automatically after a successful build).

  *__Issues__:* 
  - If you are using ndk 18 and boost <= 1.68.0, you may have to modify the boost source code according to [this](https://github.com/boostorg/asio/pull/91). Boost (<= 1.68.0) doesn't support clang 7 which is the default compiler with ndk 18. This workaround should solve the problem until boost adds support for clang 7, which it seems to have done in 1.69.0.
  - There seems to be a bug in boost 1.70.0. Workaround here: https://github.com/boostorg/boost/issues/258



## Test App 
Also included is a [test app](./example_app/) which can be opened by Android Studio. If you build and run this app it should show the date and time as calculated by boost *chrono*  (indicating that you have built, linked to and called the boost library correctly), as well as the ndk version used to build the boost library.
To use the test app make sure to adjust the values in the [local.properties](./example_app/local.properties) file.

*Note:* The test app uses [CMake for Android](https://developer.android.com/ndk/guides/cmake)


## *Header-only* Boost Libraries
Many of the boost libraries (eg. *algorithm*) can be used as "header only" ie do not require compilation . So you may get away with not building boost if you only
want to use these. To see which of the libraries do require building you can switch to the dir where you extracted the boost download and call:

```
> ./bootstrap.sh --show-libraries 
```

which for example with boost 1.69 produces the output:

```
The following Boost libraries have portions that require a separate build
and installation step. Any library not listed here can be used by including
the headers only.

The Boost libraries requiring separate building and installation are:
    - atomic
    - chrono
    - container
    - context
    - contract
    - coroutine
    - date_time
    - exception
    - fiber
    - filesystem
    - graph
    - graph_parallel
    - iostreams
    - locale
    - log
    - math
    - mpi
    - program_options
    - python
    - random
    - regex
    - serialization
    - stacktrace
    - system
    - test
    - thread
    - timer
    - type_erasure
    - wave
```
## Crystax
[Crystax](https://www.crystax.net/) is an excellent alternative to Google's Ndk. It ships with prebuilt boost binaries, and dedicated build scripts.
These binaries will however not work with Goolge's Ndk. If for some reason you can't or don't want to use Crystax then you can't use their boost binaries or build scripts, which is why this project exists.

## Contributions
- Many thanks to [crystax](https://github.com/crystax/android-platform-ndk/tree/master/build/tools) for their version of *build-boost.sh* which I adapted to make it work with the google ndk.
- Thanks to [google](https://android.googlesource.com/platform/ndk/+/master/build/tools) for the  files *dev-defaults.sh, ndk-common.sh, prebuilt-common.sh*.
- Thanks to [Ryan Pavlik](https://github.com/sensics/Boost-for-Android) for his fork with some improvements.

# reSIProcate-compile-for-Android

## Version
<pre>
  1.10.2
</pre>

## System
<pre>
  ubuntu 12.04 LTS Server 32bit
</pre>

## Android NDK
<pre>
  android-ndk-r9c
</pre>

## Dependent libraries

<pre>
$ sudo apt-get install git
$ git clone https://android.googlesource.com/platform/bionic
$ git clone https://github.com/madmanteam/Boost_Android.git
$ git clone https://github.com/eighthave/openssl-android.git
$ sudo apt-get install openjdk-7-jdk
</pre>

## SSL

<pre>
--with-ssl
</pre>

## 编译
### Configure
<pre>
$ ./build/configure-android.sh
</pre>

### Before Make
> delete '-lpthread' in Makefile
> > Object:  rutil/Makefile

> modify makefile only compile resip/rutil
> > Object: Makefile

### crt*.o设置
<pre><code class="shell">
$ ln -s ${NDK_HOME}/platforms/android-${ANDROID_VERSION}/arch-arm/usr/lib/crtbegin_so.o
$ ln -s ${NDK_HOME}/platforms/android-${ANDROID_VERSION}/arch-arm/usr/lib/crtend_so.o
Object: rutil & resip/stack & resip/dum
</code></pre>

# configure file configure-android.sh

<pre>
#!/bin/sh

# run this script with any normal configure options
# for reSIProcate

# Where the NDK is unpacked
# export ANDROID_NDK=${HOME}/android/android-ndk-r7
export ANDROID_NDK=/home/android-ndk-r9c
#export ANDROID_NDK=/home/android-ndk-r11c

# it may be necessary to have a full copy of the Android source tree
# or just take a copy of linker.h
# not currently needed at all for reSIProcate build
export ANDROID_SRC=${HOME}

export CROSS_COMPILE=arm-linux-androideabi
export CROSS_VERSION=4.8
export TOOLCHAIN_ROOT=${ANDROID_NDK}/toolchains/arm-linux-androideabi-${CROSS_VERSION}/prebuilt/linux-x86

export ANDROID_STL=${ANDROID_NDK}/sources/cxx-stl

export ANDROID_VERSION=19
export ANDROID_VERSION_S=android-${ANDROID_VERSION}

export SYSROOT=${ANDROID_NDK}/platforms/${ANDROID_VERSION_S}/arch-arm

export TOOLCHAIN_PREFIX=${TOOLCHAIN_ROOT}/bin/${CROSS_COMPILE}

# Declare any binutils we may need (there may be others)
export CPP=${TOOLCHAIN_PREFIX}-cpp
export AR=${TOOLCHAIN_PREFIX}-ar
export AS=${TOOLCHAIN_PREFIX}-as
export NM=${TOOLCHAIN_PREFIX}-nm
export CC=${TOOLCHAIN_PREFIX}-gcc
export CXX=${TOOLCHAIN_PREFIX}-g++
export LD=${TOOLCHAIN_PREFIX}-ld
export RANLIB=${TOOLCHAIN_PREFIX}-ranlib

# binutils
echo '**********************************'
echo ${CPP}
echo ${AR}
echo ${AS}
echo ${NM}
echo ${CC}
echo ${CXX}
echo ${LD}
echo ${RANLIB}
echo ${ANDROID_STL}
echo ${SYSROOT}/usr/lib
echo ${TOOLCHAIN_ROOT}/lib
echo ${ANDROID_STL}/gnu-libstdc++/${CROSS_VERSION}/libs
echo ${ANDROID_STL}/gnu-libstdc++/${CROSS_VERSION}/libs/armeabi-v7a/include
echo '**********************************'

# installation prefix
export PREFIX=${HOME}/resip_ssl_prefix
export PKG_CONFIG_PATH=${PREFIX}/lib/pkgconfig

export OPENSSL_PREFIX=${HOME}/openssl_prefix
export BOOST_PREFIX=${HOME}/Boost_Android

export CFLAGS="${CFLAGS} --sysroot=${SYSROOT} -I`pwd`/contrib/asio -I${BOOST_PREFIX} -I${OPENSSL_PREFIX}/include -I${SYSROOT}/usr/include -I${TOOLCHAIN_ROOT}/include -I${ANDROID_SRC}/bionic -I${ANDROID_STL}/gnu-libstdc++/${CROSS_VERSION}/include -I${ANDROID_STL}/gnu-libstdc++/${CROSS_VERSION}/libs/armeabi-v7a/include -march=armv7-a -mfloat-abi=softfp -mfpu=neon"
export CPPFLAGS="${CFLAGS}"
export LDFLAGS="${LDFLAGS} -L${OPENSSL_PREFIX}/lib -L${SYSROOT}/usr/lib -L${TOOLCHAIN_ROOT}/lib -L${ANDROID_STL}/gnu-libstdc++/${CROSS_VERSION}/libs/armeabi-v7a -march=armv7-a -Wl,--fix-cortex-a8"

./configure --enable-android --with-ssl --host=${CROSS_COMPILE} --with-sysroot=${SYSROOT} --prefix=${PREFIX} "$@"
</pre>

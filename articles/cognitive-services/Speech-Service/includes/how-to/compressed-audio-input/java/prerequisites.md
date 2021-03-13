---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 6f7e74a4e3a0ad208ea832798748adf7a15dfc89
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417728"
---
Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencjonowania GStreamer pliki binarne nie są kompilowane i połączone z zestawem Speech SDK. Zamiast tego należy użyć wstępnie skompilowanych plików binarnych dla systemu Android. Aby pobrać wstępnie skompilowane biblioteki, zobacz [Instalowanie aplikacji dla systemu Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

Ciąg `libgstreamer_android.so` jest wymagany. Upewnij się, że wszystkie wtyczki GStreamer (z pliku Android.mk poniżej) są połączone `libgstreamer_android.so` . W przypadku korzystania z najnowszego zestawu Speech SDK (1.16.0 i nowsze) z GStreamer w wersji 1.18.3, `libc++_shared.so` musi również być obecny w systemie Android NDK.

```makefile
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 \
    audioresample audioparsers ogg opusparse \
    opus wavparse alaw mulaw flac
```

Poniżej znajduje się przykład `Android.mk` i `Application.mk` plik. Wykonaj następujące kroki, aby utworzyć `gstreamer` obiekt współużytkowany: `libgstreamer_android.so` .

```makefile
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
include $(BUILD_SHARED_LIBRARY)

ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  $(GSTREAMER_PLUGINS_CORE) \ 
                              $(GSTREAMER_PLUGINS_CODECS) \ 
                              $(GSTREAMER_PLUGINS_PLAYBACK) \
                              $(GSTREAMER_PLUGINS_CODECS_GPL) \
                              $(GSTREAMER_PLUGINS_CODECS_RESTRICTED)
GSTREAMER_EXTRA_LIBS      := -liconv -lgstbase-1.0 -lGLESv2 -lEGL
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```makefile
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Można skompilować `libgstreamer_android.so` za pomocą następującego polecenia w systemie Ubuntu 16,04 lub 18,04. Następujące wiersze polecenia zostały przetestowane tylko dla [GStreamer Android Version 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) z [systemem Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# Assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Po skompilowaniu obiektu udostępnionego ( `libgstreamer_android.so` ) Deweloper aplikacji musi umieścić obiekt współużytkowany w aplikacji systemu Android, aby można go było załadować za pomocą narzędzia Speech SDK.

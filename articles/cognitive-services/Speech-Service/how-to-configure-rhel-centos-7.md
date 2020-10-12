---
title: Jak skonfigurować usługę RHEL/CentOS 7-mowę
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować RHEL/CentOS 7, aby można było użyć zestawu Speech SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: ba531164e024f96d3bdd23912f3f6e90275edda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83589741"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurowanie RHEL/CentOS 7 dla zestawu Speech SDK

Red Hat Enterprise Linux (RHEL) 8 x64 i CentOS 8 x64 są oficjalnie obsługiwane przez zestaw Speech SDK w wersji 1.10.0 i nowszych. Istnieje również możliwość użycia zestawu Speech SDK w systemie RHEL/CentOS 7 x64, ale wymaga to aktualizacji kompilatora języka C++ (dla programowania C++) i udostępnionej biblioteki środowiska uruchomieniowego języka C++ w systemie.

Aby sprawdzić wersję kompilatora języka C++, uruchom polecenie:

```bash
g++ --version
```

Jeśli kompilator jest zainstalowany, dane wyjściowe powinny wyglądać następująco:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Ten komunikat informuje o tym, że jest zainstalowana wersja główna programu w wersji 4. Ta wersja nie ma pełnej obsługi standardu C++ 11, która jest używany przez zestaw Speech SDK. Próba skompilowania programu C++ przy użyciu tej wersji i nagłówków zestawu Speech SDK spowoduje błędy kompilacji.

Ważne jest również, aby sprawdzić wersję udostępnionej biblioteki środowiska uruchomieniowego języka C++ (libstdc + +). Większość zestawu Speech SDK jest implementowana jako natywne biblioteki C++, co oznacza, że jest to zależne od libstdc + + niezależnie od języka używanego do tworzenia aplikacji.

Aby znaleźć lokalizację libstdc + + w systemie, uruchom polecenie:

```bash
ldconfig -p | grep libstdc++
```

Dane wyjściowe z RHEL/CentOS 7 (x64) są następujące:

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Na podstawie tej wiadomości należy sprawdzić definicje wersji za pomocą tego polecenia:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Dane wyjściowe powinny być następujące:

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Zestaw Speech SDK wymaga **CXXABI_1.3.9** i **GLIBCXX_3.4.21**. Te informacje można znaleźć, uruchamiając je `ldd libMicrosoft.CognitiveServices.Speech.core.so` w bibliotekach zestawu Speech SDK z pakietu systemu Linux.

> [!NOTE]
> Zaleca się, aby wersja programu w ramach konferencji w systemie była równa co najmniej **5.4.0**, z pasującymi bibliotekami środowiska uruchomieniowego.

## <a name="example"></a>Przykład

Jest to przykładowy zestaw poleceń, który ilustruje sposób konfigurowania RHEL/CentOS 7 x64 na potrzeby programowania (C++, C#, Java, Python) przy użyciu zestawu Speech SDK 1.10.0 lub nowszego:

### <a name="1-general-setup"></a>1. Konfiguracja ogólna

Najpierw zainstaluj wszystkie zależności ogólne:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. kompilatory i biblioteki środowiska uruchomieniowego języka C/C++

Zainstaluj wstępnie wymagane pakiety za pomocą tego polecenia:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> Pakiet libmpc-devel został uznany za przestarzały w aktualizacji RHEL 7,8. Jeśli dane wyjściowe poprzedniego polecenia zawierają komunikat
>
> ```bash
> No package libmpc-devel available.
> ```
>
> należy zainstalować wymagane pliki z oryginalnych źródeł. Uruchom następujące polecenia:
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

Następnie zaktualizuj biblioteki kompilatora i środowiska uruchomieniowego:

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Jeśli zaktualizowany kompilator i biblioteki muszą zostać wdrożone na kilku komputerach, można po prostu skopiować je z lokalizacji `/usr/local` do innych maszyn. Jeśli potrzebne są tylko biblioteki środowiska uruchomieniowego, pliki w programie `/usr/local/lib64` będą wystarczające.

### <a name="3-environment-settings"></a>3. Ustawienia środowiska

Uruchom następujące polecenia, aby zakończyć konfigurację:

```bash
# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)

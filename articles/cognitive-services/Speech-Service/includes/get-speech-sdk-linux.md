---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: cdcb61249f6cb8f7b60c891dd4899d2e5cedbae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612099"
---
:::row:::
    :::column span="3":::
        Zestaw Speech SDK obsługuje tylko **Ubuntu 16.04/18.04/20.04**, **Debian 9/10**, **Red Hat Enterprise Linux (RHEL) 7/8** i **CentOS 7/8** dla następujących architektur docelowych w przypadku używania z systemem Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) do programowania w języku C++
- x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) dla języka Java
- x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) dla platformy .NET Core
- x64 dla języka Python

> [!IMPORTANT]
> W przypadku języka C# w systemie Linux ARM64 wymagany jest program .NET Core 3. x (pakiet dotnet-SDK-3. x).

> [!NOTE]
> Aby skorzystać z zestawu Speech SDK w Alpine Linux, Utwórz środowisko Debian chroot, jak opisano w witrynie typu wiki Alpine Linux na [Uruchamianie programów glibc](https://wiki.alpinelinux.org/wiki/Running_glibc_programs), a następnie postępuj zgodnie z instrukcjami dotyczącymi debian.

### <a name="system-requirements"></a>Wymagania systemowe

W przypadku aplikacji natywnych zestaw Speech SDK opiera się na `libMicrosoft.CognitiveServices.Speech.core.so` . Upewnij się, że architektura docelowa (x86, x64) jest zgodna z aplikacją. W zależności od wersji systemu Linux mogą być wymagane dodatkowe zależności.

- Biblioteki udostępnione biblioteki GNU C (w tym biblioteki programowania wątków POSIX `libpthreads` )
- Biblioteka OpenSSL ( `libssl.so.1.0.0` lub `libssl.so.1.0.2` )
- Biblioteka udostępniona dla aplikacji ALSA ( `libasound.so.2` )

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Jeśli libssl 1.0. x nie jest dostępny, zainstaluj w zamian libssl 1.1.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Jeśli libssl 1.0. x nie jest dostępny, zainstaluj w zamian libssl 1.1.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 i CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - W systemie RHEL/CentOS 7 postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]

---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399947"
---
:::row:::
    :::column span="3":::
        Zestaw SDK mowy obsługuje tylko **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**i **CentOS 7/8** na następujących architekturach docelowych, gdy jest używany z Linuksem:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Podczas kierowania na linuksa ARM64 i przy użyciu języka C# — wymagany jest pakiet .NET Core 3.x (dotnet-sdk-3.x. Jeśli kierujesz reklamy na ARM32 lub ARM64, Python nie jest obsługiwany.

> [!NOTE]
> Architektury x86 Ubuntu 16.04, Ubuntu 18.04 i Debian 9 obsługują tylko rozwój języka C++ za pomocą SDK mowy.

### <a name="system-requirements"></a>Wymagania systemowe

W przypadku aplikacji natywnej sdk mowy opiera się na `libMicrosoft.CognitiveServices.Speech.core.so`. Upewnij się, że architektura docelowa (x86, x64) jest zgodna z aplikacją. W zależności od wersji systemu Linux mogą być wymagane dodatkowe zależności.

- Biblioteki współdzielone biblioteki GNU C (w tym biblioteka programowania wątków POSIX), `libpthreads`)
- Biblioteka OpenSSL`libssl.so.1.0.0` `libssl.so.1.0.2`( lub )
- Biblioteka udostępniona dla aplikacji`libasound.so.2`ALSA ( )

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 i CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania RHEL/CentOS 7 dla speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]

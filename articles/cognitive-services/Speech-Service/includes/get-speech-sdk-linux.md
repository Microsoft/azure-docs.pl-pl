---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399947"
---
:::row:::
    :::column span="3":::
        Zestaw Speech SDK obsługuje tylko **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**i **CentOS 7/8** dla następujących architektur docelowych w przypadku używania z systemem Linux:
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
> W przypadku określania wartości docelowej ARM64 systemu Linux i używania języka C# jest wymagany program .NET Core 3. x (pakiet dotnet-SDK-3. x). Jeśli jesteś celem ARM32 lub ARM64, Język Python nie jest obsługiwany.

> [!NOTE]
> Architektury x86 Ubuntu 16,04, Ubuntu 18,04 i Debian 9 obsługują tylko programowanie w języku C++ przy użyciu zestawu Speech SDK.

### <a name="system-requirements"></a>Wymagania systemowe

W przypadku aplikacji natywnych zestaw Speech SDK opiera się na `libMicrosoft.CognitiveServices.Speech.core.so`. Upewnij się, że architektura docelowa (x86, x64) jest zgodna z aplikacją. W zależności od wersji systemu Linux mogą być wymagane dodatkowe zależności.

- Biblioteki udostępnione biblioteki GNU C (w tym biblioteki programowania wątków POSIX `libpthreads`)
- Biblioteka OpenSSL (`libssl.so.1.0.0` lub `libssl.so.1.0.2`)
- Biblioteka udostępniona dla aplikacji ALSA (`libasound.so.2`)

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
> Postępuj zgodnie z instrukcjami dotyczącymi [sposobu KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]

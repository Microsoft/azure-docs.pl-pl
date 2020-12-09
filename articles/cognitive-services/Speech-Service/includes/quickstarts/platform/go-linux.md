---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: f674edd15b86f49d60450a53f5df5852b32f95a4
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906440"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla systemu Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Wymagania systemowe

System Zapoznaj się z listą [obsługiwanych dystrybucji systemu Linux i architektur docelowych](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz wykonać następujące czynności:

* zatoce
* [Plik binarny języka go (1,13 lub nowszy)](https://golang.org/dl/)

* Na obsługiwanych platformach systemu Linux wymagane są pewne biblioteki ( `libssl` na potrzeby obsługi protokołu Secure Sockets Layer i `libasound2` obsługi dźwięku). Zapoznaj się z dystrybucją poniżej, aby zapoznać się z poleceniami wymaganymi do zainstalowania odpowiednich wersji tych bibliotek.

   * Na Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Jeśli libssl 1.0.0 jest niedostępny, zainstaluj libssl 1.0. x (gdzie x jest większe niż 0) lub zamiast tego libssl 1.1.

   * Na RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - W systemie RHEL/CentOS 7 postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Skonfiguruj środowisko języka go

Wykonaj następujące kroki, aby skonfigurować środowisko go w celu znalezienia zestawu Speech SDK. W obu krokach Zastąp `<architecture>` wartość architekturą procesora procesora. Może to być `x86` , `x64` , `arm32` , lub `arm64` .

1. Ze względu na to, że powiązania są zależne `cgo` , należy ustawić zmienne środowiskowe, aby można było znaleźć zestaw SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Aby uruchamiać aplikacje, w tym zestaw SDK, należy poinformować system operacyjny, gdzie znaleźć libs:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list-go.md)]

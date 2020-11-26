---
title: 'Szybki Start: zestaw Speech SDK dla języka Java (systemy Windows, Linux, macOS) Konfiguracja platformy — Speech Service'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę do korzystania z języka Java (Windows, Linux, macOS) z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 142d4504ab12e7df5cc1e009038554a5b90dff0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188421"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla 64-bitowego języka Java 8 JRE. Jeśli chcesz, aby nazwa pakietu była już uruchomiona, zestaw Java SDK nie jest dostępny w repozytorium centralnym Maven. Bez względu na to, czy korzystasz z Gradle `pom.xml` , czy pliku zależności, musisz dodać repozytorium niestandardowe wskazujące na `https://csspeechstorage.blob.core.windows.net/maven/` (patrz poniżej, aby znaleźć nazwę pakietu).

> [!NOTE]
> Aby uzyskać informacje dotyczące zestawu Speech Devices SDK oraz urządzenia Roobo, zobacz [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Pakiet SDK mowy Java jest dostępny dla następujących systemów operacyjnych:
  - Windows: tylko 64-bitowe
  - Mac: macOS X w wersji 10,13 lub nowszej
  - System Zapoznaj się z listą [obsługiwanych dystrybucji systemu Linux i architektur docelowych](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Zaćmienie IDE Java](https://www.eclipse.org/downloads/) (wymaga już zainstalowanego języka Java)
- Na obsługiwanych platformach systemu Linux wymagane są pewne biblioteki ( `libssl` na potrzeby obsługi protokołu Secure Sockets Layer i `libasound2` obsługi dźwięku). Zapoznaj się z dystrybucją poniżej, aby zapoznać się z poleceniami wymaganymi do zainstalowania odpowiednich wersji tych bibliotek.

  - W systemie Ubuntu/Debian Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Jeśli libssl 1.0.0 jest niedostępny, zainstaluj libssl 1.0. x (gdzie x jest większe niż 0) lub zamiast tego libssl 1.1.

  - W systemie RHEL/CentOS Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - W systemie RHEL/CentOS 7 postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy. Należy pamiętać, że zainstalowanie tego programu po raz pierwszy może wymagać ponownego uruchomienia systemu Windows przed kontynuowaniem pracy z tym przewodnikiem.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Tworzenie projektu w przezaćmieniu i Instalowanie zestawu Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]

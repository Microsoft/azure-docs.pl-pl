---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400016"
---
:::row:::
    :::column span="3":::
        Zestaw Java SDK dla systemu Android jest dostarczany jako <a href="https://developer.android.com/studio/projects/android-library" target="_blank">biblioteka AAR <span class="docon docon-navigate-external x-hidden-focus"> </span>(Android Library), </a>która zawiera niezbędne biblioteki i wymagane uprawnienia systemu Android. Jest hostowany w repozytorium Maven `https://csspeechstorage.blob.core.windows.net/maven/` w `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`pakiecie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Aby korzystać z pakietu z projektu Android Studio, należy wprowadzić następujące zmiany:

1. W pliku *build.gradle* na poziomie projektu dodaj `repository` do sekcji następujące elementy:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. W pliku *build.gradle* na poziomie modułu `dependencies` dodaj do sekcji następujące elementy:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK jest również częścią [SDK urządzeń mowy](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Kod źródłowy szybkiego startu w systemie Java dla systemu Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Kod źródłowy szybkiego startu środowiska wykonawczego Java (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>
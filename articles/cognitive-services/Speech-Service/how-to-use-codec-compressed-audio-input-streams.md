---
title: Przesyłanie strumieniowe skompresowanego dźwięku kodeka za pomocą zestawu SPEECH SDK — usługa rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowany dźwięk do usługi Mowa za pomocą zestawu SPEECH SDK. Dostępne dla języków C++, C# i Java dla systemu Linux, języka Java w systemie Android i języka Objective-C w systemie iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: db902019b4fb1237c8403c719862d8fca4ba4f28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772527"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Używanie skompresowanych danych wejściowych audio koder-in z zestawem Speech SDK

Zestaw SDK usługi Mowa może akceptować skompresowane formaty audio. Dekompresuje dźwięk przed wysłaniem go za pośrednictwem sieci do usługi rozpoznawania mowy jako nieprzetworzoną usługę PCM.

Platforma | Języki | Obsługiwana wersja GStreamer
| :--- | ---: | :---:
Windows (z wyłączeniem platformy UWP)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [obsługiwane dystrybucje systemu Linux i architektury docelowe](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Wersja zestawu SPEECH SDK wymagana dla skompresowanych danych wejściowych audio
* Zestaw SPEECH SDK w wersji 1.10.0 lub nowszej jest wymagany w systemach RHEL 8 i CentOS 8
* Zestaw SPEECH SDK w wersji 1.11.0 lub nowszej jest wymagany dla systemu Windows.
* Zestaw SPEECH SDK w wersji 1.16.0 lub nowszej dla najnowszego programu gstreamer w systemach Windows i Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer wymagany do obsługi skompresowanego dźwięku

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod korzystający ze skompresowanych danych wejściowych audio koderów-koderów

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak rozpoznawać mowę](./get-started-speech-to-text.md)

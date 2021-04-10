---
title: Koder-dekoder audio skompresowany dźwięk przy użyciu zestawu mowy SDK-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi mowy przy użyciu zestawu Speech SDK. Dostępne dla języków C++, C# i Java dla systemu Linux i języka Java w systemie Android i w systemie iOS.
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
ms.openlocfilehash: 1e08203076de2073e39c5b5f5eb40b66c88490d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417710"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Korzystanie z kodera-dekoder skompresowanego audio przy użyciu zestawu Speech SDK

Interfejs API **strumienia danych wejściowych audio** usługi Speech Service SDK umożliwia przesyłanie strumieniowe skompresowanego dźwięku do usługi mowy przy użyciu `PullStream` lub `PushStream` .

Platforma | Języki | Obsługiwana wersja GStreamer
| :--- | ---: | :---:
Windows (z wyłączeniem platformy UWP)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [obsługiwane dystrybucje systemu Linux i architektury docelowe](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Wersja zestawu Speech SDK wymagana przez dane wejściowe skompresowanego dźwięku
* Zestaw Speech SDK w wersji 1.10.0 lub nowszej jest wymagany dla RHEL 8 i CentOS 8
* Dla systemu Windows wymagany jest pakiet Speech SDK w wersji 1.11.0 lub nowszej.
* Zestaw Speech SDK w wersji 1.16.0 lub nowszej dla najnowszej GStreamer w systemach Windows i Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer wymagane do obsłużenia skompresowanego dźwięku

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

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

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
> [Dowiedz się, jak rozpoznać mowę](./get-started-speech-to-text.md)
---
title: Podstawy syntezy mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać zestawu Speech SDK do konwersji zamiany tekstu na mowę. Ten artykuł zawiera informacje na temat konstruowania obiektów, obsługiwanych formatów danych wyjściowych audio oraz niestandardowych opcji konfiguracji dla syntezy mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: c5ceb76b8dcdad5d487e196cf1780703b7e34f17
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874524"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Poznaj podstawy syntezy mowy

W tym artykule przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

> [!TIP]
> Jeśli nie masz możliwości wykonania jednego z naszych przewodników Szybki Start, zachęcamy do rozpoczęcia opon i wypróbowania zamiany tekstu na mowę.
> * [Syntetyzowanie mowy przy użyciu głośnika](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
* [Ulepszanie syntezy za pomocą języka SSML](speech-synthesis-markup.md)

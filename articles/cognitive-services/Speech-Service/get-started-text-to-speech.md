---
title: Przewodnik Szybki Start dotyczący syntezy mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać zestawu Speech SDK do konwersji zamiany tekstu na mowę. W tym przewodniku szybki start znajdziesz informacje na temat konstrukcji obiektów i wzorców projektowych, obsługiwanych formatów danych wyjściowych audio, interfejsu wiersza polecenia mowy oraz niestandardowych opcji konfiguracji dla syntezy mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: 12ee7fbbd7699d09deae85971ffe411e115609ec
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934700"
---
# <a name="get-started-with-speech-synthesis"></a>Wprowadzenie do syntezy mowy

W tym przewodniku szybki start przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

> [!TIP]
> Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady szybkiego startu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) w witrynie GitHub.

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

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
* [Ulepszanie syntezy za pomocą języka SSML](speech-synthesis-markup.md)
* Dowiedz się, jak używać [długiego interfejsu API audio](long-audio-api.md) w przypadku dużych przykładów tekstu, takich jak książki i artykuły z wiadomościami
* Zobacz [przykłady szybkiego startu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) w witrynie GitHub

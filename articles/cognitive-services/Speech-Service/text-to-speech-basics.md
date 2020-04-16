---
title: Podstawy syntezy mowy - Obsługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą SDK mowy do konwertowania tekstu na mowę. W tym artykule dowiesz się o konstrukcji obiektów, obsługiwanych formatach wyjściowych audio i niestandardowych opcjach konfiguracji syntezy mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-sixteen
ms.openlocfilehash: 74f762dd0004bf72b703d0f611a16762b1ff028d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399544"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Poznaj podstawy syntezy mowy

W tym artykule nauczysz się typowych wzorców projektowych do wykonywania syntezy tekstu na mowę przy użyciu SDK mowy. Zacznij od podstawowej konfiguracji i syntezy i przejdź do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, w tym:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie częstotliwości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język znaczników syntezy mowy)
* Korzystanie z głosów neuronowych

> [!TIP]
> Jeśli nie miałeś okazji ukończyć jednego z naszych szybkich startów, zachęcamy do kopania opon i wypróbowania rozpoznawania mowy dla siebie.
> * [Rozpoznawanie mowy przy użyciu mikrofonu](quickstarts/text-to-speech.md)

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
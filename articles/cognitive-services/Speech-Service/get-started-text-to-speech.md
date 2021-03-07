---
title: Przewodnik Szybki Start dotyczący zamiany tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać zestawu Speech SDK do konwersji zamiany tekstu na mowę. W tym przewodniku szybki start znajdziesz informacje na temat konstrukcji obiektów i wzorców projektowych, obsługiwanych formatów danych wyjściowych audio, interfejsu wiersza polecenia mowy oraz niestandardowych opcji konfiguracji dla syntezy mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: Zamiana tekstu na mowę
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428194"
---
# <a name="get-started-with-text-to-speech"></a>Wprowadzenie do zamiany tekstu na mowę

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

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Pobierz informacje o pozycji

Twój projekt może potrzebować wiedzieć, kiedy słowo jest wymawiane przez zamianę mowy na tekst, tak aby mogło wykonać określone czynności na podstawie tego chronometrażu. Jeśli na przykład chcesz wyróżnić słowa jako wymawiane, musisz wiedzieć, co należy zaznaczyć, kiedy ją podświetlić, i na jak długo ją podświetlić.

Można to zrobić za pomocą `WordBoundary` dostępnego w programie zdarzenia `SpeechSynthesizer` . To zdarzenie jest zgłaszane na początku każdego nowego wypowiadanego wyrazu i będzie przesunięte w czasie w strumieniu mówionym, a także przesunięcie tekstu w monicie wejściowym.

* `AudioOffset` raportuje wyjściowy dźwięk, który upłynął czas między rozpoczęciem syntezy a rozpoczęciem następnego wyrazu. Ta wartość jest mierzona w jednostkach setki (SNS) z 10 000 SNS równych 1 milisekund.
* `WordOffset` raportuje pozycję znaku w ciągu wejściowym (oryginalny tekst lub [SSML](speech-synthesis-markup.md)) bezpośrednio przed wyrazem, który ma zostać wypowiadany.

> [!NOTE]
> `WordBoundary` zdarzenia są wywoływane, gdy dane wyjściowe audio staną się dostępne, co będzie szybsze niż odtwarzanie na urządzeniu wyjściowym. Odpowiednio synchronizując chronometraż strumienia do "czasu rzeczywistego" musi zostać osiągnięty przez obiekt wywołujący.

Przykłady użycia można znaleźć w przykładach `WordBoundary` [zamiany tekstu na mowę](https://aka.ms/csspeech/samples) w witrynie GitHub.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
* [Ulepszanie syntezy za pomocą języka SSML](speech-synthesis-markup.md)
* Dowiedz się, jak używać [długiego interfejsu API audio](long-audio-api.md) w przypadku dużych przykładów tekstu, takich jak książki i artykuły z wiadomościami
* Zobacz [przykłady szybkiego startu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) w witrynie GitHub

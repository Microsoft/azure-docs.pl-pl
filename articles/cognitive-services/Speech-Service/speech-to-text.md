---
title: Zamiana mowy na tekst — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany mowy na tekst umożliwia transkrypcję strumieni audio w czasie rzeczywistym do tekstu. Aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania na podstawie tego tekstu. Ta usługa bezproblemowo współpracuje z funkcjami zamiany tekstu na mowę (synteza mowy) i tłumaczenia mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: e0aea7e55381e9571b156701699f5f45315bb384
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399699"
---
# <a name="what-is-speech-to-text"></a>Co to jest zamiana mowy na tekst?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Zamiana mowy na tekst z usługi Mowa, znana również jako rozpoznawanie mowy, umożliwia transkrypcję strumieni audio w czasie rzeczywistym do tekstu. Aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania w tym tekście jako polecenie wprowadzania. Ta usługa jest obsługiwana przez tę samą technologię rozpoznawania, której firma Microsoft używa dla cortany i produktów pakietu Office. Bezproblemowo współpracuje z ofertą usług <a href="./speech-translation.md" target="_blank">tłumaczenia <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i <a href="./text-to-speech.md" target="_blank">zamiany tekstu na mowę. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Aby uzyskać pełną listę dostępnych języków mowy do tekstu, zobacz [obsługiwane języki](language-support.md#speech-to-text).

Usługa zamiany mowy na tekst domyślnie używa modelu języka uniwersalnego. Ten model został przeszkolony przy użyciu danych należących do firmy Microsoft i jest wdrażany w chmurze. Jest to optymalne dla konwersacji i scenariuszy dyktowania. Korzystając z zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, można tworzyć i szkolić niestandardowe modele akustyczne, językowe i wymowy. Personalizacja jest przydatna w rozwiązywaniu problemu hałasu otoczenia lub słownictwa specyficznego dla branży.

> [!NOTE]
> Bing Speech został wycofany ze służby 15 października 2019. Jeśli aplikacje, narzędzia lub produkty korzystają z interfejsów API mowy Bing, utworzyliśmy przewodniki ułatwiające migrację do usługi Mowy.
> - [Migracja z mowy bing do usługi mowy](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Wprowadzenie do zamiany mowy na tekst

Usługa zamiany mowy na tekst jest dostępna za pośrednictwem [sdk mowy.](speech-sdk.md) Istnieje kilka typowych scenariuszy dostępnych jako przewodniki Szybki start w różnych językach i platformach:

 - [Szybki start: rozpoznawanie mowy za pomocą wejścia mikrofonowego](quickstarts/speech-to-text-from-microphone.md)
 - [Szybki start: rozpoznawanie mowy z pliku](quickstarts/speech-to-text-from-file.md)
 - [Szybki start: rozpoznawanie mowy przechowywanej w magazynie obiektów blob](quickstarts/from-blob.md)

Jeśli wolisz korzystać z usługi REST zamiany mowy na tekst, zobacz [INTERFEJSY API REST](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Samouczki i przykładowy kod

Po miał okazję korzystać z usługi mowy, wypróbuj nasz samouczek, który uczy, jak rozpoznać intencje z mowy przy użyciu zestawu SDK mowy i usługi LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy za pomocą zestawu SDK mowy i usługi LUIS przy użyciu języka C #](how-to-recognize-intents-from-speech-csharp.md)

Przykładowy kod dla zestawu SDK mowy jest dostępny w usłudze GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczyt dźwięku z pliku lub strumienia, ciągłe i pojedyncze ujęcie rozpoznawania i pracy z modelami niestandardowymi.

- [Próbki zamiany mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Próbki transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Dostosowywanie

Oprócz standardowego modelu usługi mowy można tworzyć modele niestandardowe. Dostosowywanie pomaga przezwyciężyć bariery rozpoznawania mowy, takie jak styl mówienia, słownictwo i szum w tle, zobacz [Mowa niestandardowa](how-to-custom-speech.md). Opcje dostosowywania różnią się w zależności od języka/ustawień [regionalnych,](supported-languages.md) zobacz obsługiwane języki, aby zweryfikować obsługę.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
- [Pobierz sdk mowy](speech-sdk.md)

---
title: Zamiana tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany tekstu na mowę w usłudze Mowa umożliwia aplikacjom, narzędziom lub urządzeniom konwertowanie tekstu na naturalną syntezowaną mowę podobną do człowieka. Wybierz zaprogramowane głosy lub utwórz własny niestandardowy głos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399502"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiana tekstu na mowę?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Zamiana tekstu na mowę z usługi Mowa umożliwia aplikacjom, narzędziom lub urządzeniom konwertowanie tekstu na syntetyzowaną mowę podobną do człowieka. Wybierz spośród standardowych i nerwowych głosów lub utwórz niestandardowy głos unikalny dla Twojego produktu lub marki. Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i lokalizacjach, a 5 głosów neuronowych jest dostępnych w wybranej liczbie języków i ustawień regionalnych. Aby uzyskać pełną listę obsługiwanych głosów, języków i ustawień regionalnych, zobacz [obsługiwane języki](language-support.md#text-to-speech).

> [!NOTE]
> Bing Speech został wycofany ze służby 15 października 2019. Jeśli aplikacje, narzędzia lub produkty korzystają z interfejsów API mowy Bing lub mowy niestandardowej, utworzyliśmy przewodniki ułatwiające migrację do usługi mowy.
> - [Migracja z mowy bing do usługi mowy](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Podstawowe funkcje

* Synteza mowy — użyj [interfejsu API Speech SDK](quickstarts/text-to-speech-audio-file.md) lub [REST](rest-text-to-speech.md) do konwersji tekstu na mowę przy użyciu standardowych, neuronowych lub niestandardowych głosów.

* Asynchronicznego syntezy długiego dźwięku - Użyj [Long Audio API](long-audio-api.md) asynchronicznie syntetyzować pliki tekstu na mowę dłużej niż 10 minut (na przykład książek audio lub wykładów). W przeciwieństwie do syntezy wykonywane przy użyciu speech SDK lub mowy do tekstu REST INTERFEJSU API, odpowiedzi nie są zwracane w czasie rzeczywistym. Oczekuje się, że żądania są wysyłane asynchronicznie, odpowiedzi są sondowane dla i że syntetyzowany dźwięk jest pobierany po udostępnieniu z usługi. Obsługiwane są tylko niestandardowe głosy neuronowe.

* Głosy standardowe - Utworzone przy użyciu technik statystycznej syntezy parametrycznej i/lub syntezy łączenia. Głosy te są bardzo zrozumiałe i brzmią naturalnie. Możesz łatwo włączyć aplikacje do mówienia w ponad 45 językach, dzięki szerokiej gamie opcji głosowych. Głosy te zapewniają wysoką dokładność wymowy, w tym obsługę skrótów, rozszerzeń akronimu, interpretacji daty /godziny, polifonów i innych. Aby uzyskać pełną listę standardowych głosów, zobacz [obsługiwane języki](language-support.md#text-to-speech).

* Głosy neuronowe - Głębokie sieci neuronowe są używane do pokonywania granic tradycyjnej syntezy mowy w odniesieniu do stresu i intonacji w języku mówionym. Przewidywanie prozody i synteza głosu są wykonywane jednocześnie, co skutkuje bardziej płynnymi i naturalnie brzmiącymi wyjściami. Głosy neuronowe mogą być używane do tworzenia interakcji z chatbotami i asystentami głosowymi bardziej naturalnymi i wciągającymi, przekształcania tekstów cyfrowych, takich jak e-booki, w audiobooki, oraz ulepszania systemów nawigacji samochodowej. Dzięki naturalnej prozodyi i wyraźnej artykulacji słów, głosy nerwowe znacznie zmniejszają zmęczenie słuchowe podczas interakcji z systemami Sztucznej Inteligencji. Aby uzyskać pełną listę głosów neuronowych, zobacz [obsługiwane języki](language-support.md#text-to-speech).

* Język znaczników syntezy mowy (SSML) — język znaczników oparty na języku XML używany do dostosowywania wyjść zamiany mowy na tekst. Za pomocą protokołu SSML można regulować wysokość, dodawać pauzy, poprawiać wymowę, przyspieszać lub spowalniać mówienia, zwiększać lub zmniejszać głośność oraz przypisywać wiele głosów do jednego dokumentu. Zobacz [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Rozpoczęcie pracy

Usługa zamiany tekstu na mowę jest dostępna za pośrednictwem [sdk mowy.](speech-sdk.md) Istnieje kilka typowych scenariuszy dostępnych jako przewodniki Szybki start w różnych językach i platformach:

* [Syntetyzowanie mowy do pliku dźwiękowego](quickstarts/text-to-speech-audio-file.md)
* [Syntetyzowanie mowy przy użyciu głośnika](quickstarts/text-to-speech.md)
* [Asynchronicznie syntetyzuje dźwięk o długiej formie](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Jeśli wolisz, usługa zamiany tekstu na mowę jest dostępna za pośrednictwem [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod tekstu na mowę jest dostępny w usłudze GitHub. Te przykłady obejmują konwersję zamiany tekstu na mowę w najpopularniejszych językach programowania.

- [Próbki zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Próbki zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Dostosowywanie

Oprócz standardowych i neuronowych głosów, można tworzyć i dostrajać niestandardowe głosy unikalne dla twojego produktu lub marki. Wszystko, czego potrzeba, aby rozpocząć, to kilka plików audio i związane z nimi transkrypcje. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do niestandardowego głosu](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota cenowa

Podczas korzystania z usługi zamiany tekstu na mowę, są rozliczane dla każdego znaku, który jest konwertowany na mowę, w tym znaki interpunkcyjne. Chociaż sam dokument SSML nie jest rozliczany, opcjonalne elementy, które są używane do dostosowywania sposobu konwertowania tekstu na mowę, takie jak fonemy i wysokość, są liczone jako znaki rozliczane. Oto lista tego, co można rozliczać:

- Tekst przekazany do usługi zamiany tekstu na mowę w treści SSML żądania
- Wszystkie znaczniki w polu tekstowym treści żądania w formacie `<speak>` SSML, z wyjątkiem i `<voice>` tagów
- Litery, znaki interpunkcyjne, spacje, karty, znaczniki i wszystkie znaki odstępu
- Każdy punkt kodu zdefiniowany w Unicode

Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Każdy znak w języku chińskim, japońskim i koreańskim jest liczony jako dwa znaki do rozliczeń.

## <a name="reference-docs"></a>Dokumenty referencyjne

- [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
- [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj bezpłatną subskrypcję usługi Mowy](get-started.md)
- [Pobierz sdk mowy](speech-sdk.md)

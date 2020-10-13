---
title: Omówienie zamiany tekstu na mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany tekstu na mowę w usłudze mowy umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na rozpoznawanie mowy przez człowieka. Ten artykuł zawiera omówienie zalet i możliwości usługi zamiany tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: Zamiana tekstu na mowę
ms.openlocfilehash: 5d60279a2e3cb6aa7226f518783d53a1a38ddaa8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357458"
---
# <a name="what-is-text-to-speech"></a>Co to jest zamiana tekstu na mowę?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ten przegląd zawiera informacje na temat zalet i możliwości usługi zamiany tekstu na mowę, która umożliwia aplikacjom, narzędziom i urządzeniom Konwertowanie tekstu na mowę, którą daje człowiek. Wybieraj spośród standardowych i neuronowych głosów lub Utwórz niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w wybranej liczbie języków i ustawieniach regionalnych. Aby zapoznać się z pełną listą obsługiwanych głosów, języków i ustawień regionalnych, zobacz [obsługiwane języki](language-support.md#text-to-speech).

> [!NOTE]
> Rozpoznawanie mowy Bing został zlikwidowany 15 października 2019. Jeśli Twoje aplikacje, narzędzia lub produkty używają rozpoznawanie mowy Bing interfejsów API lub Custom Speech, zostały utworzone przewodniki ułatwiające Migrowanie do usługi mowy.
> - [Migrowanie z rozpoznawanie mowy Bing do usługi mowy](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Podstawowe funkcje

* Synteza mowy — używanie [zestawu Speech SDK](quickstarts/text-to-speech-audio-file.md) lub [interfejsu API REST](rest-text-to-speech.md) do konwertowania zamiany tekstu na mowę przy użyciu standardowych, neuronowych lub niestandardowych głosów.

* Asynchroniczna synteza długiego dźwięku — korzystaj z [długiego interfejsu API audio](long-audio-api.md) , aby asynchronicznie wyszukiwać pliki zamiany tekstu na mowę dłużej niż 10 minut (na przykład książki audio lub wykłady). W przeciwieństwie do syntezy przy użyciu zestawu Speech SDK lub interfejsu API REST zamiany mowy na tekst, odpowiedzi nie są zwracane w czasie rzeczywistym. Oczekuje się, że żądania są wysyłane asynchronicznie, odpowiedzi są sondowane i w przypadku, gdy jest ona pobierana z usługi. Obsługiwane są tylko niestandardowe głosy neuronowych.

* Standardowe głosy — tworzone przy użyciu statystycznych metod syntezowania i/lub łączenia technik syntezy. Głosy te są bardzo zrozumiałe i naturalne. Aplikacje można łatwo włączyć w więcej niż 45 językach z szeroką gamą opcji głosowych. Głosy te zapewniają wysoką dokładność odmowy, w tym obsługę skrótów, rozwinięcia akronimów, interpretacje daty/godziny, telefony i inne. Aby uzyskać pełną listę głosów standardowych, zobacz [obsługiwane języki](language-support.md#text-to-speech).

* Głosy neuronowych — głębokie sieci neuronowych są używane do pokonania granic tradycyjnej syntezy mowy, w odniesieniu do obciążenia i intonation w języku mówionym. Funkcja przewidywania Prosody i syntezy głosu są wykonywane jednocześnie, co daje w wyniku bardziej płynne i naturalne wyprowadzanie danych wyjściowych. Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, konwertowanie cyfrowych tekstów, takich jak książki elektroniczne, na Audiobooks i ulepszanie systemów nawigacji między samochodami. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania podczas pracy z systemami AI. Aby uzyskać pełną listę głosów neuronowych, zobacz [obsługiwane języki](language-support.md#text-to-speech).

* Język znaczników syntezy mowy (SSML) — język znaczników oparty na języku XML używany do dostosowywania danych wyjściowych zamiany mowy na tekst. Za pomocą SSML można dostosować gęstość, dodać pauzy, poprawić wymowę, przyspieszyć lub spowalniać liczbę mówienia, zwiększyć lub zmniejszyć ilość, a także dodać wiele głosów do jednego dokumentu. Zobacz [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Rozpoczęcie pracy

Zapoznaj się z [przewodnikiem Szybki Start](get-started-text-to-speech.md) , aby rozpocząć pracę z funkcją zamiany tekstu na mowę. Usługa zamiany tekstu na mowę jest dostępna za pośrednictwem [zestawu Speech SDK](speech-sdk.md), [interfejsu API REST](rest-text-to-speech.md)i [wiersza polecenia mowy](spx-overview.md)

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zamiany tekstu na mowę jest dostępny w witrynie GitHub. Te przykłady obejmują konwersję zamiany tekstu na mowę w najpopularniejszych językach programowania.

- [Przykłady zamiany tekstu na mowę (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Dostosowywanie

Oprócz odgłosów standardowych i neuronowych, można tworzyć i dostrajać niestandardowe głosy unikatowe dla danego produktu lub marki. Wszystkie potrzebne do rozpoczęcia pracy to kilku plików audio i skojarzonych transkrypcji. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z niestandardowym głosem](how-to-custom-voice.md)

## <a name="pricing-note"></a>Uwaga dotycząca cen

W przypadku korzystania z usługi zamiany tekstu na mowę naliczane są opłaty za każdy znak konwertowany na mowę, w tym znaki interpunkcyjne. Chociaż sam dokument SSML nie jest rozliczany, opcjonalne elementy, które są używane do dostosowywania sposobu konwersji tekstu na mowę, takie jak fonemów i gęstość, są zliczane jako znaki rozliczeniowe. Poniżej znajduje się lista rzeczy, które są rozliczane:

- Tekst przesłany do usługi zamiany tekstu na mowę w treści SSML żądania
- Wszystkie znaczniki w polu tekstowym treści żądania w formacie SSML, z wyjątkiem `<speak>` tagów for i `<voice>`
- Litery, interpunkcja, spacje, tabulatory, znaczniki i wszystkie znaki odstępu
- Każdy punkt kodowy zdefiniowany w kodzie Unicode

Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Każdy znak w języku chińskim, japońskim i koreańskim jest traktowany jako dwa znaki do rozliczania.

## <a name="reference-docs"></a>Dokumentacja dokumentacji

- [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

- [Pobierz bezpłatną subskrypcję usługi mowy](overview.md#try-the-speech-service-for-free)
- [Pobieranie zestawu Speech SDK](speech-sdk.md)

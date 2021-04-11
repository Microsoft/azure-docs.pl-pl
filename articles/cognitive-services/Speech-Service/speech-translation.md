---
title: Omówienie tłumaczenia mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja tłumaczenia mowy umożliwia dodawanie do aplikacji, narzędzi i urządzeń kompleksowego tłumaczenia mowy w czasie rzeczywistym na wiele języków. Ten sam interfejs API może służyć do tłumaczeń polegających na zamianie mowy na mowę i mowy na tekst. Ten artykuł zawiera omówienie zalet usługi tłumaczenia mowy i jej możliwości.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: Tłumaczenie mowy
ms.openlocfilehash: 99541d7fe9eaa867860af93bc1423d476ce8bf4a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449940"
---
# <a name="what-is-speech-translation"></a>Co to jest tłumaczenie mowy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ten przegląd zawiera informacje o zaletach i możliwościach usługi tłumaczenia mowy, które umożliwiają korzystanie z funkcji [zamiany mowy na wiele języków](language-support.md#speech-translation) w czasie rzeczywistym oraz Tłumaczenie mowy na mowę i zamianę tekstu na tekst strumieni audio. Za pomocą zestawu Speech SDK aplikacje, narzędzia i urządzenia mają dostęp do transkrypcji źródłowych i danych wyjściowych tłumaczenia dla podanego dźwięku. Tymczasowe transkrypcja i wyniki tłumaczenia są zwracane, gdy wykryto mowę, a końcowe wyniki można przekonwertować na mowę.

Ta dokumentacja zawiera następujące typy artykułów:

* Przewodniki **Szybki Start** to instrukcje umożliwiające wykonywanie żądań do usługi.
* **Przewodniki z** instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.
* **Koncepcje** zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.
* **Samouczki** są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.

## <a name="core-features"></a>Podstawowe funkcje

* Tłumaczenie zamiany mowy na tekst z wynikami rozpoznawania.
* Tłumaczenie mowy na mowę.
* Obsługa tłumaczenia na wiele języków docelowych.
* Tymczasowe rozpoznawanie i wyniki tłumaczenia.

## <a name="get-started"></a>Rozpoczęcie pracy 

Zapoznaj się z [przewodnikiem Szybki Start](get-started-speech-translation.md) , aby rozpocząć tłumaczenie mowy. Usługa tłumaczenia mowy jest dostępna za pośrednictwem [zestawu Speech SDK](speech-sdk.md) i [interfejsu wiersza polecenia mowy](spx-overview.md).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz tłumaczenie i praca z modelami niestandardowymi.

* [Przykłady zamiany mowy na tekst i translacji (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Przewodniki po migracji

Jeśli Twoje aplikacje, narzędzia lub produkty używają [interfejs API tłumaczenia mowy w usłudze translator](./how-to-migrate-from-translator-speech-api.md), zostały utworzone przewodniki ułatwiające Migrowanie do usługi mowy.

* [Migrowanie z interfejs API tłumaczenia mowy w usłudze Translator do usługi mowy](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Zestaw SDK rozpoznawania mowy](./speech-sdk.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
* [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Następne kroki

* Ukończ Tłumaczenie mowy — [Szybki Start](get-started-speech-translation.md)
* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
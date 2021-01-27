---
title: Limity żądań — translator
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę limitów żądań dla translatora. Opłaty są naliczane w oparciu o liczbę znaków, a nie częstotliwość żądań z limitem 5 000 znaków na żądanie. Limity znaków są oparte na subskrypcjach, z F0 ograniczone do 2 000 000 znaków na godzinę.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2bc2c1361c7d2f73ff8a67e906a6db725f669d52
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895411"
---
# <a name="request-limits-for-translator"></a>Limity żądań dla translatora

Ten artykuł zawiera limity ograniczania dla translatora. Usługi obejmują tłumaczenie, przekształcenie, wykrywanie długości zdania, wykrywanie języka i alternatywne tłumaczenia.

## <a name="character-and-array-limits-per-request"></a>Limity liczby znaków i tablic na żądanie

Każde żądanie tłumaczenia jest ograniczone do 10 000 znaków, we wszystkich językach docelowych, do których są tłumaczone. Na przykład wysłanie żądania tłumaczenia o 3 000 znaków do przetłumaczenia na 3 różne języki powoduje, że rozmiar żądania 3000x3 = 9 000 znaków, który spełnia limit żądań. Opłata jest naliczana za znak, a nie przez liczbę żądań. Zaleca się wysyłanie krótszych żądań.

W poniższej tabeli wymieniono limity elementów tablicy i znaków dla każdej operacji translatora.

| Operacja | Maksymalny rozmiar elementu tablicy |    Maksymalna liczba elementów tablicy |    Maksymalny rozmiar żądania (znaki) |
|:----|:----|:----|:----|
| Tłumaczenie | 10 000    | 100   | 10 000 |
| Transliteracja | 5000 | 10    | 5000 |
| Wykrywanie | 50 000 | 100 |   50 000 |
| BreakSentence | 50 000    | 100 | 50 000 |
| Wyszukiwanie w słowniku| 100 |  10  | 1000 |
| Przykłady słowników | 100 dla tekstu i 100 dla tłumaczenia (łącznie 200)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Limity znaków na godzinę

Limit liczby znaków na godzinę jest oparty na warstwie subskrypcji usługi Translator. 

Przydział godzinowy powinien być wykorzystany nawet w ciągu godziny. Na przykład w przypadku F0 limitu warstwy o 2 000 000 znaków na godzinę, znaki powinny być używane nie szybciej niż w przybliżeniu 33 300 znaki na minutę (2 000 000 znaki podzielone przez 60 minut).

W przypadku osiągnięcia lub przekroczenia tych limitów lub wysłania zbyt dużej części limitu przydziału w krótkim czasie prawdopodobnie otrzymasz odpowiedź na brak limitu przydziału. Nie ma ograniczeń dla współbieżnych żądań.

| Warstwa | Limit znaków |
|------|-----------------|
| F0 | 2 000 000 znaków na godzinę |
| S1 | 40 000 000 znaków na godzinę |
| S2/C2 | 40 000 000 znaków na godzinę |
| S3/C3 | 120 000 000 znaków na godzinę |
| S4/C4 | 200 000 000 znaków na godzinę |

Limity dla [subskrypcji dla kilku usług](./reference/v3-0-reference.md#authentication) są takie same jak w przypadku warstwy S1.

Limity te są ograniczone do standardowych modeli tłumaczenia firmy Microsoft. Niestandardowe modele tłumaczenia korzystające z translatora niestandardowego są ograniczone do 1 800 znaków na sekundę.

## <a name="latency"></a>Opóźnienie

Translator ma maksymalne opóźnienie wynoszące 15 sekund przy użyciu modeli standardowych i 120 sekund podczas korzystania z modeli niestandardowych. Zazwyczaj odpowiedzi *na tekst w ciągu 100 znaków* są zwracane w 150 ms do 300 milisekund. Modele translatorów niestandardowych mają podobne charakterystyki opóźnienia dotyczące długotrwałego żądania i mogą mieć wyższy czas opóźnienia, gdy częstotliwość żądań jest nieprzerwana. Czasy odpowiedzi będą się różnić w zależności od rozmiaru pary żądań i języków. Jeśli nie otrzymasz tłumaczenia lub odpowiedź na [błąd](./reference/v3-0-reference.md#errors) w tym okresie, sprawdź kod, połączenie sieciowe i ponów próbę. 

## <a name="sentence-length-limits"></a>Limity długości zdania

W przypadku korzystania z funkcji [BreakSentence](./reference/v3-0-break-sentence.md) długość zdania jest ograniczona do 275 znaków. Istnieją wyjątki dla następujących języków:

| Język | Kod | Limit znaków |
|----------|------|-----------------|
| Chiński | zh | 166 |
| Niemiecki | de | 800 |
| Włoski | it | 800 |
| japoński | ja | 166 |
| Portugalski | pkt. | 800 |
| Hiszpański | es | 800 |
| Tajlandzki | th | 180 |

> [!NOTE]
> Ten limit nie dotyczy tłumaczeń.

## <a name="next-steps"></a>Następne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja usługi Translator w wersji 3](./reference/v3-0-reference.md)
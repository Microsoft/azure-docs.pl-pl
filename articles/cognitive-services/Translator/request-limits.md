---
title: Limity żądań — Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wymieniono limity żądań dla usługi Translator. Opłaty są naliczane na podstawie liczby znaków, a nie częstotliwości żądań z limitem 5000 znaków na żądanie. Limity znaków są oparte na subskrypcji, a liczba znaków F0 jest ograniczona do 2 milionów znaków na godzinę.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727946"
---
# <a name="request-limits-for-translator"></a>Limity żądań dla usługi Translator

Ten artykuł zawiera limity ograniczania przepustowości tłumaczenia, transliteracji, wykrywania długości zdań, wykrywania języka i alternatywnych tłumaczeń.

## <a name="character-and-array-limits-per-request"></a>Limity znaków i tablic na żądanie

Każde żądanie tłumaczenia jest ograniczone do 10 000 znaków we wszystkich językach docelowych, na które jest tłumaczone tłumaczenie. Na przykład wysłanie żądania tłumaczenia 3000 znaków w celu tłumaczenia na trzy różne języki powoduje, że żądanie o rozmiarze 3000 x 3 = 9000 znaków, które spełniają limit żądań. Opłaty są naliczane za znak, a nie za liczbę żądań. Zaleca się wysyłanie krótszych żądań.

W poniższej tabeli wymieniono limity elementów tablicy i znaków dla każdej operacji translatora.

| Operacja | Maksymalny rozmiar elementu tablicy |    Maksymalna liczba elementów tablicy |    Maksymalny rozmiar żądania (znaki) |
|:----|:----|:----|:----|
| Tłumaczenie | 10 000| 100| 10 000 |
| Transliteracja | 5000| 10| 5000 |
| Wykrywanie | 50 000 |100 |50 000 |
| BreakSentence | 50 000| 100 |50 000 |
| Wyszukiwanie w słowniku| 100 |10| 1000 |
| Przykłady słowników | 100 dla tekstu i 100 do tłumaczenia (łącznie 200)| 10|2000 |

## <a name="character-limits-per-hour"></a>Limity znaków na godzinę

Limit znaków na godzinę jest oparty na warstwie subskrypcji usługi Translator.

Przydział godzinowy powinien być zużyty równomiernie w ciągu godziny. Na przykład przy limitie warstwy F0, który wynosi 2 miliony znaków na godzinę, znaki powinny być używane nie szybciej niż około 33 300 znaków na minutę (2 miliony znaków podzielone przez 60 minut).

W przypadku osiągnięcia lub przekroczenia tych limitów albo wysłania zbyt dużej części limitu przydziału w krótkim czasie prawdopodobnie otrzymasz odpowiedź z przekroczeniem limitu przydziału. Nie ma żadnych limitów żądań współbieżnych.

| Warstwa | Limit znaków |
|------|-----------------|
| F0 | 2 miliony znaków na godzinę |
| S1 | 40 milionów znaków na godzinę |
| S2 /C2 | 40 milionów znaków na godzinę |
| S3 / C3 | 120 milionów znaków na godzinę |
| S4/C4 | 200 milionów znaków na godzinę |

Limity [dla subskrypcji z wieloma usługami](./reference/v3-0-reference.md#authentication) są takie same jak w warstwie S1.

Te limity są ograniczone do standardowych modeli tłumaczenia firmy Microsoft. Niestandardowe modele tłumaczenia, które używają Custom Translator są ograniczone do 1800 znaków na sekundę na model.

## <a name="latency"></a>Opóźnienie

Maksymalny czas oczekiwania usługi Translator wynosi 15 sekund przy użyciu modeli standardowych i 120 sekund w przypadku korzystania z modeli niestandardowych. Zazwyczaj odpowiedzi na *tekst w ciągu 100 znaków* są zwracane w ciągu 150 milisekund do 300 milisekund. Niestandardowe modele tłumaczeń mają podobne charakterystyki opóźnień w zakresie trwałego szybkości żądań i mogą mieć większe opóźnienie, gdy żądanie jest sporadyczne. Czasy odpowiedzi różnią się w zależności od rozmiaru pary żądań i języka. Jeśli w tym czasie nie [](./reference/v3-0-reference.md#errors) otrzymasz tłumaczenia ani odpowiedzi o błędzie, sprawdź kod, połączenie sieciowe i spróbuj ponownie.

## <a name="sentence-length-limits"></a>Limity długości zdań

W przypadku korzystania [z funkcji BreakSentence](./reference/v3-0-break-sentence.md) długość zdania jest ograniczona do 275 znaków. Istnieją wyjątki dla tych języków:

| Język | Kod | Limit znaków |
|----------|------|-----------------|
| Chiński | Zh | 166 |
| Niemiecki | de | 800 |
| Włoski | it | 800 |
| japoński | ja | 166 |
| Portugalski | pkt. | 800 |
| Hiszpański | es | 800 |
| Tajlandzki | Th | 180 |

> [!NOTE]
> Ten limit nie ma zastosowania do tłumaczeń.

## <a name="next-steps"></a>Następne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność w regionach](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja usługi Translator w wersji 3](./reference/v3-0-reference.md)
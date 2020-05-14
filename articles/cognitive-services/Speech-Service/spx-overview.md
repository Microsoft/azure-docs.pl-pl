---
title: Usługa SPX-Speech
titleSuffix: Azure Cognitive Services
description: SPX to narzędzie wiersza polecenia do korzystania z usługi mowy bez pisania kodu. Protokół SPX wymaga minimalnej konfiguracji i od razu można zacząć eksperymentować z kluczowymi funkcjami usługi mowy, aby sprawdzić, czy można spełnić Twoje przypadki użycia.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202283"
---
# <a name="what-is-spx"></a>Co to jest SPX?

SPX to narzędzie wiersza polecenia do korzystania z usługi mowy bez pisania kodu. Protokół SPX wymaga minimalnej konfiguracji i od razu można zacząć eksperymentować z kluczowymi funkcjami usługi mowy, aby sprawdzić, czy można spełnić przypadki użycia. W ciągu kilku minut można uruchamiać proste testowe przepływy pracy, takie jak przetwarzanie wsadowe mowy — rozpoznawanie z katalogu plików, lub zamiana tekstu na mowę w kolekcji ciągów z pliku. Oprócz prostych przepływów pracy protokół SPX jest gotowy do produkcji i można go skalować w celu uruchomienia większych procesów przy użyciu skryptów zautomatyzowanych `.bat` lub powłoki.

Większość podstawowych funkcji zestawu Speech SDK jest dostępnych w SPX, ale niektóre zaawansowane funkcje i dostosowania zostały uproszczone w SPX. Należy wziąć pod uwagę następujące wskazówki, aby określić, kiedy należy używać SPX lub zestawu SDK.

Użyj SPX, gdy:
* Chcesz eksperymentować z funkcjami usługi Speech Service przy minimalnej konfiguracji i bez kodu
* Istnieją stosunkowo proste wymagania dotyczące aplikacji produkcyjnej przy użyciu usługi mowy

Użyj zestawu SDK, gdy:
* Chcesz zintegrować funkcje usługi mowy z określonym językiem lub platformą (np. C#, Python, C++)
* Istnieją złożone wymagania, które mogą wymagać zaawansowania żądań obsługi lub opracowywania zachowania niestandardowego, w tym przesyłania strumieniowego odpowiedzi

## <a name="core-features"></a>Podstawowe funkcje

* Rozpoznawanie mowy — Konwertuj mowę na tekst z plików audio lub bezpośrednio z mikrofonu lub transkrypcja zarejestrowane konwersacje.

* Synteza mowy — konwertowanie zamiany tekstu na mowę przy użyciu danych wejściowych z plików tekstowych lub danych wejściowych bezpośrednio z wiersza polecenia. Dostosuj charakterystykę danych wyjściowych mowy przy użyciu [konfiguracji SSML](speech-synthesis-markup.md)oraz [głosów standardowych lub neuronowych](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tłumaczenie mowy — tłumaczenie dźwięku w języku źródłowym na tekst w języku docelowym.

* Uruchom w usłudze Azure COMPUTE Resources — Wyślij polecenia SPX do uruchomienia na zdalnym zasobie obliczeniowym platformy Azure przy użyciu polecenia `spx webjob` .

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć pracę z SPX, zapoznaj się z [artykułem podstawowe informacje](spx-basics.md). W tym artykule pokazano, jak uruchomić niektóre podstawowe polecenia w SPX, a także wyświetlić nieco bardziej zaawansowane polecenia do uruchamiania operacji wsadowych na potrzeby zamiany mowy na tekst i zamiany tekstu na mowę. Po przeczytaniu artykułu podstawowe powinno być wystarczające zrozumienie składni SPX, aby rozpocząć pisanie niektórych poleceń niestandardowych lub zautomatyzować proste operacje mowy.

## <a name="next-steps"></a>Następne kroki

- [SPX — podstawy](spx-basics.md)
- Jeśli Twój przypadek użycia jest bardziej skomplikowany, [Pobierz zestaw Speech SDK](speech-sdk.md)

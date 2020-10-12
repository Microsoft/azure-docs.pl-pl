---
title: Interfejs wiersza polecenia usługi Azure Speech
titleSuffix: Azure Cognitive Services
description: Interfejs wiersza polecenia rozpoznawania mowy to narzędzie do obsługi mowy, które umożliwia korzystanie z usługi Speech bez pisania kodu. Interfejs wiersza polecenia mowy wymaga minimalnej konfiguracji i od razu można zacząć eksperymentować z kluczowymi funkcjami usługi mowy, aby sprawdzić, czy można spełnić przypadki użycia.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067430"
---
# <a name="what-is-the-speech-cli"></a>Co to jest interfejs wiersza polecenia usługi Mowa?

Interfejs wiersza polecenia rozpoznawania mowy to narzędzie do obsługi mowy, które umożliwia korzystanie z usługi Speech bez pisania kodu. Interfejs wiersza polecenia mowy wymaga minimalnej konfiguracji i od razu można zacząć eksperymentować z kluczowymi funkcjami usługi mowy, aby sprawdzić, czy można spełnić przypadki użycia. W ciągu kilku minut można uruchamiać proste testowe przepływy pracy, takie jak przetwarzanie wsadowe mowy — rozpoznawanie z katalogu plików, lub zamiana tekstu na mowę w kolekcji ciągów z pliku. Oprócz prostych przepływów pracy interfejs wiersza polecenia mowy jest gotowy do produkcji i można go skalować w celu uruchomienia większych procesów przy użyciu skryptów zautomatyzowanych `.bat` lub powłoki.

Większość podstawowych funkcji zestawu Speech SDK jest dostępnych w interfejsie wiersza polecenia mowy, a niektóre zaawansowane funkcje i dostosowania są uproszczone w interfejsie wiersza polecenia. Należy wziąć pod uwagę następujące wskazówki, aby określić, kiedy należy używać interfejsu wiersza polecenia mowy lub zestawu Speech SDK.

Użyj interfejsu wiersza polecenia mowy, gdy:
* Chcesz eksperymentować z funkcjami usługi Speech Service przy minimalnej konfiguracji i bez kodu
* Istnieją stosunkowo proste wymagania dotyczące aplikacji produkcyjnej przy użyciu usługi mowy

Użyj zestawu Speech SDK, gdy:
* Chcesz zintegrować funkcje usługi mowy z określonym językiem lub platformą (np. C#, Python, C++)
* Istnieją złożone wymagania, które mogą wymagać zaawansowania żądań obsługi lub opracowywania zachowania niestandardowego, w tym przesyłania strumieniowego odpowiedzi

## <a name="core-features"></a>Podstawowe funkcje

* Rozpoznawanie mowy — Konwertuj mowę na tekst z plików audio lub bezpośrednio z mikrofonu lub transkrypcja zarejestrowane konwersacje.

* Synteza mowy — konwertowanie zamiany tekstu na mowę przy użyciu danych wejściowych z plików tekstowych lub danych wejściowych bezpośrednio z wiersza polecenia. Dostosuj charakterystykę danych wyjściowych mowy przy użyciu [konfiguracji SSML](speech-synthesis-markup.md)oraz [głosów standardowych lub neuronowych](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tłumaczenie mowy — tłumaczenie dźwięku w języku źródłowym na tekst lub dźwięk w języku docelowym.

* Uruchamianie w ramach zasobów obliczeniowych platformy Azure — Wysyłanie poleceń interfejsu wiersza polecenia funkcji rozpoznawania mowy do uruchamiania na zdalnym zasobie obliczeniowym platformy Azure przy użyciu `spx webjob` .

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć pracę z interfejsem wiersza polecenia mowy, zapoznaj się z [artykułem podstawowe informacje](spx-basics.md). W tym artykule przedstawiono sposób uruchamiania niektórych podstawowych poleceń, a także przedstawiono nieco bardziej zaawansowane polecenia do uruchamiania operacji wsadowych na potrzeby zamiany mowy na tekst i zamiany tekstu na mowę. Po przeczytaniu artykułu podstawowe należy mieć wystarczającą wiedzę na temat składni, aby rozpocząć pisanie niektórych poleceń niestandardowych lub zautomatyzować proste operacje usługi Speech.

## <a name="next-steps"></a>Następne kroki

- [Podstawowe informacje o interfejsie wiersza polecenia mowy](spx-basics.md)
- Jeśli Twój przypadek użycia jest bardziej skomplikowany, [Pobierz zestaw Speech SDK](speech-sdk.md)

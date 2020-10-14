---
title: Rozpocznij Eksplorowanie pokazu doradcy metryk
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o interfejsie sieci Web klasyfikatora metryk przy użyciu podania pokazu
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 39748bacf784af0b3f162f960cd7d87cbd03a2eb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047594"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Szybki Start: Eksplorowanie pokazu doradcy metryk z przykładowymi danymi

> [!Note]
> Pokaz doradcy metryk jest tylko do odczytu i nie będzie można dołączyć danych. Aby korzystać z usługi na danych, najpierw [Utwórz zasób klasyfikatora metryk](web-portal.md).

Skorzystaj z tego artykułu, aby szybko rozpocząć Eksplorowanie najważniejszych funkcji usługi Metric Advisor. Udostępniamy witrynę demonstracyjną z przykładowymi danymi i wstępnie ustawionymi konfiguracjami, dzięki czemu możesz zapoznać się z w pełni polecanym portalem internetowym.

Kliknij [ten link](https://aka.ms/MetricsAdvisor/Demo) , aby przejść do witryny demonstracyjnej.

## <a name="view-the-available-sample-data"></a>Wyświetlanie dostępnych przykładowych danych

Po zalogowaniu się do witryny demonstracyjnej zostanie wyświetlona strona **strumieniowe źródło danych** . strumieniowe źródło danych jest logiczną grupą danych szeregów czasowych, które są wysyłane zapytania ze źródła danych. Aby uzyskać więcej informacji na temat warunków i koncepcji używanych w usłudze Advisor Metrics, zobacz [słownik](../glossary.md). 

Istnieje kilka strumieni danych, które są pozyskiwane z różnych typów źródeł danych, takich jak Azure SQL Database lub Azure Table. Każdy z nich używa nieco innych ustawień konfiguracji do nawiązania połączenia ze skojarzonymi magazynami danych.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Lista przykładowych danych" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Eksplorowanie konfiguracji strumieniowego źródła danych

Kliknij źródło *przykładowego kosztu/przychodu — dane dotyczące miejscowości/kategorii* . Zobaczysz kilka sekcji szczegółów dla źródła danych:

* Nazwa strumieniowego źródła danych i stan pozyskiwania.
* Lista metryk, z której pochodzą zapytania ze źródła danych. Na przykład *koszt* i *przychód*. 
* Historia alertu dotycząca momentu, gdy strumieniowe źródło danych staną się niedostępne. 
* Dzienniki, gdy strumieniowe źródło danych zostało zaktualizowane.   
* Informacje i ustawienia strumieniowego źródła danych.

:::image type="content" source="../media/data-feed-view.png" alt-text="Lista przykładowych danych" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Wyświetlanie wizualizacji i konfiguracji szeregów czasowych

Kliknij pozycję Metryka *kosztu* w strumieniu *przykładowego kosztu/przychodu — dane dotyczące miejscowości/kategorii* . Zostanie wyświetlona skojarzona seria czasowa pozielona według wymiarów z wizualizacjami zgodnie z historycznymi danymi metryki. Niebieski pasek wokół danych metryki reprezentuje zakres wartości oczekiwanej z modeli uczenia maszynowego klasyfikatora metryk. Punkty, które znajdują się poza tym pasmem, będą oznaczone czerwonymi kropkami, które wykryją anomalie. 

:::image type="content" source="../media/series-visualization.png" alt-text="Lista przykładowych danych" lightbox="../media/series-visualization.png":::

Wykrywanie anomalii można skonfigurować przez dostrajanie **konfiguracji wykrywania** po lewej stronie szczegółów metryki. Dostępne są wiele metod wykrywania anomalii i można je połączyć. Możesz również wypróbować różne sensitivities, wykrywać wskazówki i inne konfiguracje. Link **Konfiguracja zaawansowana** u dołu **wykrywania konfiguracji** umożliwia tworzenie bardziej złożonych i dostosowanych ustawień wykrywania, które mogą być używane dla grup lub poszczególnych serii. 

Możesz również dostrajać wykrywanie anomalii, przekazując Opinie do algorytmu wykrywania. Kliknij na anomalię i użyj panelu **Dodaj opinię** , aby skonfigurować stan anomalii, sezonowości i stan punktu zmiany. Ta opinia zostanie zarejestrowana w celu wykrycia w przyszłości.  

W dolnej części panelu **Dodaj opinię** znajduje się łącze **do centrum zdarzeń**, które przekieruje Cię do strony analiza zdarzeń i przeanalizuje główną przyczynę zdarzenia.  

:::image type="content" source="../media/incident-link.png" alt-text="Lista przykładowych danych" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Eksplorowanie wyników wykrywania anomalii i wykonywanie analizy głównych przyczyn

Po kliknięciu linku **do centrum zdarzeń** z anomalii zostanie wyświetlona strona analiza zdarzeń zawierająca szczegółowe informacje o zdarzeniu, takie jak ważność, liczba związanych anomalii oraz czas rozpoczęcia/zakończenia. Sekcja **głównej przyczyny** zawiera zautomatyzowane porady, analizując drzewo zdarzeń, biorąc pod uwagę: odchylenia, dystrybucję i udział w przypadku nadrzędnych anomalii, które mogą być główną przyczyną zdarzenia.

Sekcja **Diagnostyka** przedstawia drzewo zdarzenia oraz kilka kart do diagnozowania incydentu.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Lista przykładowych danych" lightbox="../media/incident-diagnostic.png":::

Przez lokalizowanie głównej przyczyny zdarzenia można podjąć działania i rozwiązać problem, zanim sytuacja będzie gorsza. Więcej szczegółowych informacji można także poznać, klikając inne dostępne funkcje diagnostyczne. 

## <a name="next-steps"></a>Następne kroki

- [Używanie portalu internetowego](web-portal.md)
- [Korzystanie z interfejsu API REST](rest-api.md)
- [Dołączanie źródeł danych](../how-tos/onboard-your-data.md)
    - [Zarządzanie strumieniowymi źródłami danych](../how-tos/manage-data-feeds.md)
    - [Konfiguracje dla różnych źródeł danych](../data-feeds-from-different-sources.md)

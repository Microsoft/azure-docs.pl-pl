---
title: Co to jest usługa Advisor metryk?
titleSuffix: Azure Cognitive Services
description: Co to jest Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 901d86b5569be61f89178dac460b8750bce9ea73
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605539"
---
# <a name="what-is-metrics-advisor-preview"></a>Co to jest klasyfikator metryk (wersja zapoznawcza)? 

Doradca metryk jest częścią Cognitive Services platformy Azure, która używa systemu AI do monitorowania danych i wykrywania anomalii w danych szeregów czasowych. Usługa automatyzuje proces stosowania modeli do danych i udostępnia zestaw interfejsów API oraz internetowy obszar roboczy do pozyskiwania danych, wykrywania anomalii i diagnostyki — bez potrzeby uczenia maszynowego. Deweloperzy mogą tworzyć aplikacje AIOps, predicative Maintenance i Business Monitor na podstawie usługi. Użyj usługi Metric Advisor, aby:

* Analizowanie wielowymiarowych danych z wielu źródeł danych
* Identyfikowanie i skorelowanie anomalii
* Konfigurowanie i Dostosowywanie modelu wykrywania anomalii używanego na danych
* Diagnozuj anomalie i pomoc przy analizie przyczyny głównej

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Omówienie klasyfikatora metryk":::

## <a name="connect-to-a-variety-of-data-sources"></a>Łączenie się z różnymi źródłami danych

Usługa Metric Advisor może łączyć się z usługą i dowiązywać dane [metryk wielowymiarowych](how-tos/onboard-your-data.md) z wielu magazynów danych, w tym: SQL Server, Azure Blob Storage, MongoDB i inne.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Łatwe w użyciu i dostosowywalne wykrywanie anomalii

* Klasyfikator metryk automatycznie wybiera najlepszy model danych, bez znajomości uczenia maszynowego.
* Automatycznie Monitoruj każdą serię czasową w ramach [metryk wielowymiarowych](glossary.md#multi-dimensional-metric).
* Używaj [dostrajania parametrów](how-tos/configure-metrics.md) i [interaktywnych informacji zwrotnych](how-tos/anomaly-feedback.md) , aby dostosować model stosowany do danych i w przyszłości wyniki wykrywania anomalii.

## <a name="real-time-alerts-through-multiple-channels"></a>Alerty w czasie rzeczywistym przez wiele kanałów

W przypadku wykrycia anomalii usługa Advisor może [wysyłać alerty w czasie rzeczywistym](how-tos/alerts.md) za pośrednictwem wielu kanałów przy użyciu punktów zaczepienia, takich jak punkty zaczepienia wiadomości e-mail, elementy webhook i punkty zaczepienia usługi Azure DevOps. Elastyczne reguły alertów umożliwiają dostosowywanie alertów, które są wysyłane i ich miejsca docelowego.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Inteligentny wgląd diagnostyczny przez analizowanie anomalii

Analizuj anomalie wykryte w metrykach wielowymiarowych i Generuj [inteligentne informacje diagnostyczne](how-tos/diagnose-incident.md) , w tym najbardziej prawdopodobną przyczynę główną, drzewa diagnostyczne, przechodzenie metryczne i inne. Konfigurując [Wykres metryk](how-tos/metrics-graph.md), można włączyć analizę metryk krzyżowych ułatwiającą wizualizację zdarzeń.


## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: po dodaniu danych możesz dostosować wykrywanie anomalii i utworzyć konfiguracje zgodne z Twoim scenariuszem.

1. [Utwórz zasób platformy Azure dla usługi](https://go.microsoft.com/fwlink/?linkid=2142156) Metric Advisor. 
2. Kompiluj swój pierwszy monitor przy użyciu portalu sieci Web.
    1. Dołączanie danych
    2. Dostrajanie wykrywania anomalii
    3. Subskrybowanie alertów
    4. Wyświetlanie szczegółowych informacji diagnostycznych
3. Aby dostosować wystąpienie, użyj interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z przewodnikiem Szybki Start: [Monitoruj swoją pierwszą metrykę w sieci Web](quickstarts/web-portal.md).
* Zapoznaj się z przewodnikiem Szybki Start: [Użyj interfejsów API REST, aby dostosować rozwiązanie](./quickstarts/rest-api-and-client-library.md).

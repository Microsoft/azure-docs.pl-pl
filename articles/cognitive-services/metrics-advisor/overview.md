---
title: Co to jest usługa Advisor metryk?
titleSuffix: Azure Cognitive Services
description: Co to jest Metrics Advisor?
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 408bdd948977218d9b39a39bf97391a4141e545c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947445"
---
# <a name="what-is-metrics-advisor-preview"></a>Co to jest klasyfikator metryk (wersja zapoznawcza)? 

Doradca metryk jest częścią Cognitive Services platformy Azure, która używa systemu AI do monitorowania danych i wykrywania anomalii w danych szeregów czasowych. Usługa automatyzuje proces stosowania modeli do danych i udostępnia zestaw interfejsów API opartych na sieci Web na potrzeby pozyskiwania danych, wykrywania anomalii i diagnostyki — bez potrzeby uczenia maszynowego. Użyj usługi Metric Advisor, aby:

* Analizowanie wielowymiarowych danych z wielu źródeł danych 
* Identyfikowanie i skorelowanie anomalii
* Konfigurowanie i Dostosowywanie modelu wykrywania anomalii używanego na danych
* Diagnozuj anomalie i Pomóż z analizą głównych przyczyn. 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Omówienie klasyfikatora metryk":::

## <a name="connect-to-a-variety-of-data-sources"></a>Łączenie się z różnymi źródłami danych

Usługa Metric Advisor może łączyć się z usługą i dowiązywać dane [metryk wielowymiarowych](how-tos/onboard-your-data.md) z wielu magazynów danych, w tym: SQL Server, Azure Blob Storage, MongoDB i inne. 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Łatwe w użyciu i dostosowywalne wykrywanie anomalii

* Klasyfikator metryk automatycznie wybiera najlepszy model danych, bez znajomości uczenia maszynowego. 
* Automatycznie Monitoruj każdą serię czasową w ramach [metryk wielowymiarowych](glossary.md#multi-dimensional-metric).
* Używaj [dostrajania parametrów](how-tos/configure-metrics.md) i [interaktywnych informacji zwrotnych](how-tos/anomaly-feedback.md) , aby dostosować model stosowany do danych i w przyszłości wyniki wykrywania anomalii.


## <a name="real-time-alerts-through-multiple-channels"></a>Alerty w czasie rzeczywistym przez wiele kanałów

W przypadku wykrycia anomalii usługa Advisor może [wysyłać alerty w czasie rzeczywistym](how-tos/alerts.md) za pośrednictwem wielu kanałów przy użyciu punktów zaczepienia, takich jak punkty zaczepienia wiadomości e-mail, elementy webhook i punkty zaczepienia usługi Azure DevOps. Elastyczne reguły alertów umożliwiają dostosowywanie alertów, które są wysyłane i gdzie.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Inteligentny wgląd diagnostyczny przez analizowanie anomalii

Analizuj anomalie wykryte w metrykach wielowymiarowych i Generuj [inteligentne informacje diagnostyczne](how-tos/diagnose-incident.md) , w tym najbardziej prawdopodobną przyczynę główną, drzewa diagnostyczne, przechodzenie metryczne i inne. Dzięki skonfigurowaniu [wykresu metryk](how-tos/metrics-graph.md)można analizować metryki krzyżowe, aby ułatwić wizualizację zdarzeń.


## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: po dodaniu danych możesz dostosować wykrywanie anomalii i utworzyć konfiguracje zgodne z Twoim scenariuszem.

1. [Utwórz zasób platformy Azure dla usługi](../cognitive-services-apis-create-account.md) Metric Advisor. 
2. Wypróbuj witrynę demonstracyjną, aby zobaczyć przykładowe wystąpienie usługi Advisor metryk z wstępnie skonfigurowanymi przykładowymi danymi. 
3. Kompiluj swój pierwszy monitor przy użyciu portalu sieci Web.
    1. Dołączanie danych
    2. Dostrajanie wykrywania anomalii
    3. Subskrybowanie alertów
    4. Wyświetlanie szczegółowych informacji diagnostycznych
1. Aby dostosować wystąpienie, użyj interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

* Wypróbuj [witrynę demonstracyjną](quickstarts/explore-demo.md).
* Zapoznaj się z przewodnikiem Szybki Start: [Monitoruj swoją pierwszą metrykę w sieci Web](quickstarts/web-portal.md).
* Zapoznaj się z przewodnikiem Szybki Start: [Użyj interfejsów API REST, aby dostosować rozwiązanie](quickstarts/rest-api.md).

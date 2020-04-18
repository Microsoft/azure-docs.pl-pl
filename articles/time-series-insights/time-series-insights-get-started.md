---
title: Tworzenie środowiska — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z witryny Azure Portal w celu utworzenia nowego środowiska usługi Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602370"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Tworzenie nowego środowiska usługi Time Series Insights w witrynie Azure Portal

W tym artykule opisano sposób tworzenia nowego środowiska usługi Time Series Insights przy użyciu witryny Azure portal.

Usługa Time Series Insights umożliwia rozpoczęcie wizualizacji i wykonywania zapytań o dane przesyłane do centrów IoT platformy Azure i centrów zdarzeń w ciągu kilku minut, umożliwiając wykonywanie zapytań o duże ilości danych szeregów czasowych w ciągu kilku sekund.  Został on zaprojektowany dla internetu rzeczy (IoT) skali i może obsługiwać terabajty danych.

## <a name="steps-to-create-the-environment"></a>Procedura tworzenia środowiska

Wykonaj następujące kroki, aby utworzyć środowisko:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz przycisk **+ Utwórz zasób.**

1. Wybierz kategorię **Internet rzeczy** i wybierz pozycję Wgląd **w szeregi czasowe**.

   [![Tworzenie środowiska usługi Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na stronie **Statystyki szeregów czasowych** wybierz pozycję **Utwórz**.

1. Wypełnij wymagane parametry. W poniższej tabeli wyjaśniono każdy parametr:

   [![Tworzenie grupy zasobów usługi Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Nazwa środowiska | Unikatowa nazwa | Ta nazwa reprezentuje środowisko w [eksploratorze szeregów czasowych](https://insights.timeseries.azure.com)
   Subskrypcja | Twoja subskrypcja | Jeśli masz wiele subskrypcji, wybierz subskrypcję, która zawiera źródło zdarzeń najlepiej. Usługa Time Series Insights może automatycznie wykrywać zasoby usługi Azure IoT Hub i usługi Event Hub istniejące w tej samej subskrypcji.
   Grupa zasobów | Tworzenie nowego lub używanie istniejących | Grupa zasobów jest kolekcją używanych razem zasobów platformy Azure. Można wybrać istniejącą grupę zasobów, na przykład tę, która zawiera Centrum zdarzeń lub Centrum IoT Hub. Możesz też wprowadzić nowy, jeśli ten zasób nie jest powiązany z innymi zasobami.
   Lokalizacja | Najbliższe źródło zdarzeń | Najlepiej wybrać tę samą lokalizację centrum danych, która zawiera dane źródłowe zdarzeń, aby uniknąć dodatkowych kosztów przepustowości między regionami i między strefami oraz dodatkowych opóźnień podczas przenoszenia danych poza region.
   Warstwa cenowa | S1 | Wybierz potrzebną przepustowość. Aby uzyskać najniższe koszty i pojemność rozrusznika, wybierz S1.
   Pojemność | 1 | Pojemność to mnożnik ma zastosowanie do szybkości transferu danych przychodzących, pojemności magazynu i kosztów skojarzonych z wybraną jednostką SKU.  Wydajność środowiska można zmienić po jego utworzeniu. W przypadku najniższych kosztów wybierz pojemność 1.
  
1. Wybierz **pozycję Utwórz,** aby rozpocząć proces inicjowania obsługi administracyjnej. Może to potrwać kilka minut.

1. Aby monitorować proces wdrażania, wybierz symbol **Powiadomień** (ikonę dzwonka).

   [![Obejrzyj powiadomienia](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. W **przeglądzie**zasobu sprawdź ustawienia konfiguracji wdrożenia.

   [![Przypinanie usługi Time Series Insights do pulpitu nawigacyjnego](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcjonalnie)** Wybierz **ikonę pinezki** w prawym górnym rogu, aby łatwo uzyskać dostęp do środowiska usługi Time Series Insights w przyszłości.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych,](time-series-insights-data-access.md) aby zabezpieczyć środowisko.

* [Dodaj źródło zdarzeń centrum zdarzeń usługi Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska usługi Azure Time Series Insights.

* [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzenia.

* Wyświetlanie środowiska w [Eksploratorze usługi Time Series Insights](https://insights.timeseries.azure.com).

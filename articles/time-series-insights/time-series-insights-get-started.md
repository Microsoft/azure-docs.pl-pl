---
title: Tworzenie środowiska Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak utworzyć nowe środowisko Time Series Insights przy użyciu Azure Portal.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602370"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Tworzenie nowego środowiska usługi Time Series Insights w witrynie Azure Portal

W tym artykule opisano sposób tworzenia nowego środowiska Time Series Insights przy użyciu Azure Portal.

Time Series Insights umożliwia rozpoczęcie wizualizacji i wykonywania zapytań dotyczących danych przepływających do centrów Azure IoT Hub i Event Hubs w ciągu kilku minut, dzięki czemu można wysyłać zapytania o duże ilości danych szeregów czasowych w kilka sekund.  Została zaprojektowana na potrzeby skalowania Internetu rzeczy (IoT) i może obsługiwać terabajty danych.

## <a name="steps-to-create-the-environment"></a>Procedura tworzenia środowiska

Wykonaj następujące kroki, aby utworzyć środowisko:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz przycisk **+ Utwórz zasób** .

1. Wybierz kategorię **Internet rzeczy** i wybierz pozycję **Time Series Insights**.

   [![Tworzenie środowiska usługi Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na stronie **Time Series Insights** wybierz pozycję **Utwórz**.

1. Wypełnij wymagane parametry. W poniższej tabeli opisano każdy parametr:

   [![Tworzenie grupy zasobów usługi Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Nazwa środowiska | Unikatowa nazwa | Ta nazwa reprezentuje środowisko w [Eksploratorze szeregów czasowych](https://insights.timeseries.azure.com)
   Subskrypcja | Twoja subskrypcja | Jeśli masz wiele subskrypcji, wybierz subskrypcję zawierającą źródło zdarzeń najlepiej. Time Series Insights może automatycznie wykryć zasoby usługi Azure IoT Hub i centrum zdarzeń istniejące w tej samej subskrypcji.
   Grupa zasobów | Utwórz nową lub Użyj istniejącej | Grupa zasobów jest kolekcją używanych razem zasobów platformy Azure. Możesz wybrać istniejącą grupę zasobów, na przykład tę, która zawiera centrum zdarzeń lub IoT Hub. Można też utworzyć nowy, jeśli ten zasób nie jest powiązany z innymi zasobami.
   Lokalizacja | Bliżej źródła zdarzenia | Najlepiej wybrać tę samą lokalizację centrum danych, w której znajdują się dane źródła zdarzeń, w celu uniknięcia dodatkowego kosztu i limitu przepustowości między strefami oraz dodanych opóźnień podczas przenoszenia danych z regionu.
   Warstwa cenowa | S1 | Wybierz potrzebną przepustowość. W przypadku najniższych kosztów i pojemności Starter wybierz pozycję S1.
   Pojemność | 1 | Wydajność to mnożnik dotyczący szybkości transferu danych przychodzących, pojemności magazynu i kosztu związanego z wybraną jednostką SKU.  Wydajność środowiska można zmienić po jego utworzeniu. W przypadku najniższych kosztów wybierz pojemność 1.
  
1. Wybierz pozycję **Utwórz** , aby rozpocząć proces aprowizacji. Może to potrwać kilka minut.

1. Aby monitorować proces wdrażania, wybierz symbol **powiadomienia** (ikona dzwonka).

   [![Obejrzyj powiadomienia](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. W **przeglądzie**zasobów Sprawdź ustawienia konfiguracji wdrożenia.

   [![Przypinanie usługi Time Series Insights do pulpitu nawigacyjnego](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcjonalnie)** Wybierz **ikonę pinezki** w prawym górnym rogu, aby łatwo uzyskać dostęp do środowiska Time Series Insights w przyszłości.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) w celu zabezpieczenia środowiska.

* [Dodaj źródło zdarzeń centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska Azure Time Series Insightsowego.

* [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzeń.

* Wyświetl swoje środowisko w [eksploratorze Time Series Insights](https://insights.timeseries.azure.com).

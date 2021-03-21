---
title: Skonfiguruj środowisko Gen2 przy użyciu Azure Portal-Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko w Azure Time Series Insights Gen2 przy użyciu Azure Portal.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 56f7ac6f4403867e5e4e53b55b241912cdc57454
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464452"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Tworzenie środowiska Azure Time Series Insights Gen2 przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia środowiska Azure Time Series Insights Gen2 przy użyciu [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Omówienie

Po udostępnieniu środowiska Azure Time Series Insights Gen2 należy utworzyć następujące zasoby platformy Azure:

* Azure Time Series Insights środowisko Gen2, które następuje po modelu cen z płatność zgodnie z rzeczywistym użyciem
* Konto usługi Azure Storage
* Opcjonalny magazyn ciepły dla szybszych i nieograniczonych zapytań

> [!TIP]
>
> * Dowiedz się [, jak zaplanować środowisko](./how-to-plan-your-environment.md).
> * Przeczytaj, jak [dodać Źródło centrum zdarzeń](./how-to-ingest-data-event-hub.md) lub jak [dodać Źródło Centrum IoT](./how-to-ingest-data-iot-hub.md).

Poznasz następujące czynności:

1. Skojarz każde środowisko Azure Time Series Insights generacji 2 ze źródłem zdarzenia. Podajesz również właściwość identyfikatora znacznika czasu i unikatową grupę odbiorców, aby zapewnić, że środowisko będzie miało dostęp do odpowiednich zdarzeń.

1. Po zakończeniu aprowizacji można modyfikować zasady dostępu i inne atrybuty środowiska, aby odpowiadały potrzebom biznesowym.

   > [!NOTE]
   > Pierwszy krok jest opcjonalny w przypadku aprowizacji środowiska. W przypadku pominięcia tego kroku należy dołączyć Źródło zdarzenia do środowiska później, aby dane mogły zacząć przepływać do środowiska i można uzyskać do niego dostęp za pomocą zapytania.

## <a name="create-the-environment"></a>Tworzenie środowiska

Aby utworzyć Azure Time Series Insights środowisko generacji 2:

1. Utwórz zasób Azure Time Series Insights w obszarze *Internet rzeczy* na [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Gen2 (L1)** jako **warstwę**. Podaj nazwę środowiska i wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację do hostowania środowiska.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Utwórz wystąpienie Azure Time Series Insights." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Wprowadź identyfikator szeregów czasowych.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Utwórz konfigurację środowiska Azure Time Series Insights, kontynuując." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * Identyfikator szeregów czasowych *uwzględnia wielkość* liter i jest *niezmienny*. (Nie można jej zmienić po ustawieniu).
   > * Identyfikatory szeregów czasowych mogą składać się z maksymalnie *trzech* kluczy. Należy traktować ją jako klucz podstawowy w bazie danych, która jednoznacznie reprezentuje każdy czujnik urządzenia, który wyśle dane do środowiska. Może to być jedna właściwość lub kombinacja do trzech właściwości.
   > * Przeczytaj więcej na temat [wybierania identyfikatora szeregów czasowych](./how-to-select-tsid.md)

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu, rodzaj konta i wyznaczyć wybór [replikacji](../storage/common/redundancy-migration.md?tabs=portal) . Spowoduje to automatyczne utworzenie konta usługi Azure Storage. Domyślnie zostanie utworzone konto [ogólnego przeznaczenia w wersji 2](../storage/common/storage-account-overview.md) . Konto jest tworzone w tym samym regionie co Azure Time Series Insights środowisko Gen2, które zostało wcześniej wybrane.
Alternatywnie można również utworzyć własny magazyn (BYOS) za pomocą [szablonu Azure Resource Manager](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) podczas tworzenia nowego środowiska Gen2 usługi Azure Time Series.

1. **(Opcjonalnie)** Włącz sklep ciepły dla Twojego środowiska, jeśli chcesz szybciej i nieograniczoną liczbę zapytań dotyczących najnowszych danych w środowisku. Możesz również utworzyć lub usunąć sklep ciepły za pomocą opcji **konfiguracji magazynu** w lewym okienku nawigacji po utworzeniu środowiska Azure Time Series Insights Gen2.

1. **(Opcjonalnie)** Możesz teraz dodać Źródło zdarzenia. Możesz również zaczekać, aż po zainicjowaniu wystąpienia.

   * Azure Time Series Insights obsługuje [platformę azure IoT Hub](./how-to-ingest-data-iot-hub.md) i [Azure Event Hubs](./how-to-ingest-data-event-hub.md) jako opcje źródła zdarzeń. Chociaż można dodać tylko jedno źródło zdarzenia podczas tworzenia środowiska, można później dodać kolejne Źródło zdarzenia.

     Po dodaniu źródła zdarzenia możesz wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców. Upewnij się, że źródło zdarzeń korzysta z unikatowej grupy odbiorców dla danego środowiska, aby można było odczytać z niej dane.

   * Wybierz odpowiednią właściwość sygnatury czasowej. Domyślnie Azure Time Series Insights używa czasu w kolejce dla każdego źródła zdarzenia.

     > [!TIP]
     > Czas w kolejce może nie być najlepszym skonfigurowanym ustawieniem do użycia w scenariuszach zdarzeń wsadowych lub w historycznych scenariuszach przekazywania danych. W takich przypadkach należy zweryfikować decyzję o użyciu lub nie używać właściwości timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Karta Konfiguracja źródła zdarzeń" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Wybierz pozycję **Przegląd + Utwórz** , aby upewnić się, że Twoje środowisko zostało zainicjowane i skonfigurowano w odpowiedni sposób.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Przejrzyj i Utwórz kartę" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Time Series Insights ogólnie dostępnych środowisk i środowisk Gen2 poprzez odczytywanie [planu środowiska](./how-to-plan-your-environment.md).
* Dowiedz się więcej na temat [źródeł zdarzeń pozyskiwania strumieniowego](./concepts-streaming-ingestion-event-sources.md) dla środowiska Azure Time Series Insights Gen2.
* Dowiedz się więcej [na temat zarządzania środowiskiem](./how-to-provision-manage.md).

---
title: Inicjowanie obsługi i zarządzanie środowiskiem generacji 2 — Seria czasowa platformy Azure | Microsoft Docs
description: Dowiedz się, jak inicjować obsługę środowiska Azure Time Series Insights Gen 2 i zarządzać nim.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: e54e8e9de1df4c8a1c870285d36e4580daaa698a
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667830"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Inicjowanie obsługi administracyjnej Azure Time Series Insights Gen2 i zarządzanie nimi

W tym artykule opisano sposób tworzenia środowiska Azure Time Series Insights Gen2 i zarządzania nim przy użyciu [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Omówienie

Po udostępnieniu środowiska Azure Time Series Insights Gen2 należy utworzyć następujące zasoby platformy Azure:

* Azure Time Series Insights środowisko Gen2, które następuje po modelu cen z płatność zgodnie z rzeczywistym użyciem
* Konto usługi Azure Storage
* Opcjonalny magazyn ciepły dla szybszych i nieograniczonych zapytań

> [!TIP]
>
> * Dowiedz się [, jak zaplanować środowisko](./time-series-insights-update-plan.md).
> * Przeczytaj, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) lub jak [dodać Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Poznasz następujące czynności:

1. Skojarz każde środowisko Azure Time Series Insights generacji 2 ze źródłem zdarzenia. Podajesz również właściwość identyfikatora znacznika czasu i unikatową grupę odbiorców, aby zapewnić, że środowisko będzie miało dostęp do odpowiednich zdarzeń.

1. Po zakończeniu aprowizacji można modyfikować zasady dostępu i inne atrybuty środowiska, aby odpowiadały potrzebom biznesowym.

   > [!NOTE]
   > Pierwszy krok jest opcjonalny w przypadku aprowizacji środowiska. W przypadku pominięcia tego kroku należy dołączyć Źródło zdarzenia do środowiska później, aby dane mogły zacząć przepływać do środowiska i można uzyskać do niego dostęp za pomocą zapytania.

## <a name="create-the-environment"></a>Tworzenie środowiska

Aby utworzyć Azure Time Series Insights środowisko generacji 2:

1. Utwórz zasób Azure Time Series Insights w obszarze *Internet rzeczy* na [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Gen2 (L1)** jako **warstwę**. Podaj nazwę środowiska i wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację do hostowania środowiska.

   [![Utwórz wystąpienie Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Wprowadź identyfikator szeregów czasowych.

    > [!NOTE]
    >
    > * Identyfikator szeregów czasowych *uwzględnia wielkość* liter i jest *niezmienny*. (Nie można jej zmienić po ustawieniu).
    > * Identyfikatory szeregów czasowych mogą składać się z maksymalnie *trzech* kluczy. Należy traktować ją jako klucz podstawowy w bazie danych, która jednoznacznie reprezentuje każdy czujnik urządzenia, który wyśle dane do środowiska. Może to być jedna właściwość lub kombinacja do trzech właściwości.
    > * Przeczytaj więcej na temat [wybierania identyfikatora szeregów czasowych](time-series-insights-update-how-to-id.md)

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu, rodzaj konta i wyznaczyć wybór [replikacji](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal) . Spowoduje to automatyczne utworzenie konta usługi Azure Storage. Domyślnie zostanie utworzone konto [ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) . Konto jest tworzone w tym samym regionie co Azure Time Series Insights środowisko Gen2, które zostało wcześniej wybrane.
Alternatywnie można również utworzyć własny magazyn (BYOS) za pomocą [szablonu ARM](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) podczas tworzenia nowego środowiska usługi Azure Time Series Gen2.

1. **(Opcjonalnie)** Włącz sklep ciepły dla Twojego środowiska, jeśli chcesz szybciej i nieograniczoną liczbę zapytań dotyczących najnowszych danych w środowisku. Możesz również utworzyć lub usunąć sklep ciepły za pomocą opcji **konfiguracji magazynu** w lewym okienku nawigacji po utworzeniu środowiska Azure Time Series Insights Gen2.

1. **(Opcjonalnie)** Możesz teraz dodać Źródło zdarzenia. Możesz również zaczekać, aż po zainicjowaniu wystąpienia.

   * Azure Time Series Insights obsługuje [platformę azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje źródła zdarzeń. Chociaż można dodać tylko jedno źródło zdarzenia podczas tworzenia środowiska, można później dodać kolejne Źródło zdarzenia.

     Po dodaniu źródła zdarzenia możesz wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców. Należy pamiętać, że źródło zdarzenia wymaga unikatowej grupy odbiorców dla danego środowiska, aby można było do niego odczytywać dane.

   * Wybierz odpowiednią właściwość sygnatury czasowej. Domyślnie Azure Time Series Insights używa czasu w kolejce dla każdego źródła zdarzenia.

     > [!TIP]
     > Czas w kolejce może nie być najlepszym skonfigurowanym ustawieniem do użycia w scenariuszach zdarzeń wsadowych lub w historycznych scenariuszach przekazywania danych. W takich przypadkach należy zweryfikować decyzję o użyciu lub nie używać właściwości timestamp.

     [![Karta Konfiguracja źródła zdarzeń](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Upewnij się, że Twoje środowisko zostało zainicjowane i skonfigurowano w odpowiedni sposób.

    [![Przejrzyj i Utwórz kartę](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Azure Time Series Insights środowisku Gen2 można zarządzać przy użyciu Azure Portal. Istnieje kilka najważniejszych różnic między środowiskiem Gen2 i Gen1 S1 lub Gen1 S2, które należy wziąć pod uwagę podczas zarządzania środowiskiem za pomocą Azure Portal:

* Blok **przegląd**  Azure Portal Gen2 ma następujące zmiany:

  * Pojemność jest usuwana, ponieważ nie dotyczy środowisk Gen2.
  * Zostanie dodana Właściwość **identyfikatora szeregów czasowych** . Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [eksploratora Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Nazwa konta usługi Azure Storage znajduje się na liście.

* Blok **konfigurowania** Azure Portal jest usuwany, ponieważ jednostki skalowania nie mają zastosowania do Azure Time Series Insights środowisk Gen2. Można jednak użyć **konfiguracji magazynu** w celu skonfigurowania nowo wprowadzonego magazynu w postaci ciepłej.

* Blok **danych referencyjnych** Azure portal został usunięty w Azure Time Series Insights Gen2, ponieważ koncepcja danych referencyjnych została zastąpiona przez [model szeregów czasowych (TSM)](/azure/time-series-insights/concepts-model-overview).

[![Azure Time Series Insights środowisko Gen2 w Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Time Series Insights ogólnie dostępnych środowisk i środowisk Gen2 poprzez odczytywanie [planu środowiska](./time-series-insights-update-plan.md).

* Dowiedz się, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

* Skonfiguruj [Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

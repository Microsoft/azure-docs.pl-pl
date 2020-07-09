---
title: Udostępnianie środowiska w wersji zapoznawczej i zarządzanie nim — Azure Time Series | Microsoft Docs
description: Dowiedz się, jak inicjować obsługę środowiska Azure Time Series Insights w wersji zapoznawczej i zarządzać nim.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087220"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Udostępnianie wersji zapoznawczej Azure Time Series Insights i zarządzanie nią

W tym artykule opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i zarządzania nim przy użyciu [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Omówienie

Środowiska Azure Time Series Insights w wersji zapoznawczej to środowiska *z opcją płatność zgodnie z rzeczywistym* użyciem.

Po udostępnieniu środowiska Azure Time Series Insights w wersji zapoznawczej można utworzyć następujące zasoby platformy Azure:

* Środowisko wersji zapoznawczej Azure Time Series Insights  
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1
* Opcjonalny magazyn ciepły dla szybszych i nieograniczonych zapytań

> [!TIP]
> * Dowiedz się [, jak zaplanować środowisko](./time-series-insights-update-plan.md).
> * Przeczytaj, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) lub jak [dodać Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Poznasz następujące czynności:

1. **(Opcjonalnie)** Skojarz każde środowisko Azure Time Series Insights w wersji zapoznawczej ze źródłem zdarzenia. Podajesz również właściwość identyfikatora znacznika czasu i unikatową grupę odbiorców, aby zapewnić, że środowisko będzie miało dostęp do odpowiednich zdarzeń.

   > [!NOTE]
   > Poprzedni krok jest opcjonalny w przypadku aprowizacji środowiska. W przypadku pominięcia tego kroku należy dołączyć Źródło zdarzenia do środowiska później, aby można było uzyskać dostęp do danych w środowisku.

1. Po zakończeniu aprowizacji można zmodyfikować zasady dostępu i inne atrybuty środowiska zgodnie z wymaganiami firmy.

## <a name="create-the-environment"></a>Tworzenie środowiska

Aby utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej:

1. Wybierz pozycję **PAYG** jako **warstwę**. Podaj nazwę środowiska i wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację do hostowania środowiska.

   [![Utwórz wystąpienie Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Wprowadź identyfikator szeregów czasowych.

    > [!NOTE]
    > * Identyfikator szeregów czasowych *uwzględnia wielkość* liter i jest *niezmienny*. (Nie można jej zmienić po ustawieniu).
    > * Identyfikatory szeregów czasowych mogą składać się z maksymalnie *trzech* kluczy.
    > * Przeczytaj więcej na temat [wybierania identyfikatora szeregów czasowych](time-series-insights-update-how-to-id.md)

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu i wyznaczając wybór replikacji. Spowoduje to automatyczne utworzenie konta ogólnego przeznaczenia w usłudze Azure Storage. Konto jest tworzone w tym samym regionie co wcześniej wybrane środowisko Azure Time Series Insights w wersji zapoznawczej.

    [![Konfiguracja magazynu zimnego](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcjonalnie)** Włącz sklep ciepły dla Twojego środowiska, jeśli chcesz szybciej i nieograniczoną liczbę zapytań dotyczących najnowszych danych w środowisku. Możesz również utworzyć lub usunąć sklep ciepły za pomocą opcji **konfiguracji magazynu** w okienku nawigacji po lewej stronie po utworzeniu środowiska Time Series Insights w wersji zapoznawczej.

    [![Konfiguracja magazynu ciepłego](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcjonalnie)** Możesz teraz dodać Źródło zdarzenia. Możesz również zaczekać, aż po zainicjowaniu wystąpienia.

   * Time Series Insights obsługuje [platformę azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje źródła zdarzeń. Chociaż można dodać tylko jedno źródło zdarzenia podczas tworzenia środowiska, można później dodać kolejne Źródło zdarzenia. 
   
     Po dodaniu źródła zdarzenia możesz wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców. Najlepiej utworzyć unikatową grupę odbiorców, aby upewnić się, że wszystkie zdarzenia są widoczne dla środowiska Azure Time Series Insights w wersji zapoznawczej.

   * Wybierz odpowiednią właściwość sygnatury czasowej. Domyślnie Azure Time Series Insights używa czasu w kolejce dla każdego źródła zdarzenia.

     > [!TIP]
     > Czas w kolejce może nie być najlepszym skonfigurowanym ustawieniem do użycia w scenariuszach zdarzeń wsadowych lub w historycznych scenariuszach przekazywania danych. W takich przypadkach należy zweryfikować decyzję o użyciu lub nie używać właściwości timestamp.

     [![Karta Konfiguracja źródła zdarzeń](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Upewnij się, że Twoje środowisko zostało zainicjowane i skonfigurowano w odpowiedni sposób.

    [![Przejrzyj i Utwórz kartę](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Możesz zarządzać środowiskiem Azure Time Series Insights w wersji zapoznawczej przy użyciu Azure Portal. Istnieje kilka najważniejszych różnic między środowiskiem Azure Time Series Insights w wersji zapoznawczej a ogólnie dostępnymi środowiskami S1 lub S2, które należy wziąć pod uwagę podczas zarządzania środowiskiem za pomocą Azure Portal:

* W bloku **przegląd** Azure Portal w wersji zapoznawczej wprowadzono następujące zmiany:

  * Pojemność jest usuwana, ponieważ nie dotyczy środowisk PAYG.
  * Zostanie dodana Właściwość **identyfikatora szeregów czasowych** . Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [eksploratora Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).
  * Nazwa konta usługi Azure Storage znajduje się na liście.

* Blok **konfigurowania** Azure Portal zostanie usunięty w wersji zapoznawczej programu Azure Time Series Insights, ponieważ nie można konfigurować środowisk PAYG. Można jednak użyć **konfiguracji magazynu** w celu skonfigurowania nowo wprowadzonego magazynu w postaci ciepłej.

* Blok **danych referencyjnych** Azure portal został usunięty w podglądzie Azure Time Series Insights, ponieważ dane referencyjne nie są częścią środowisk PAYG.

[![Środowisko Time Series Insights w wersji zapoznawczej w Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Time Series Insights ogólnie dostępnych środowisk i wersji zapoznawczych, odczytując [Plan środowiska](./time-series-insights-update-plan.md).

- Dowiedz się, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Skonfiguruj [Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

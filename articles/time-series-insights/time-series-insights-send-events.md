---
title: Wysyłanie zdarzeń do środowiska — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum zdarzeń, uruchamiać przykładową aplikację i wysyłać zdarzenia do środowiska Azure Time Series Insightsowego.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 8ee2cc5473d83eafc2443f3aea7cab6a2e77727c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463565"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Wysyłanie zdarzeń do środowiska Azure Time Series Insights Gen1 przy użyciu centrum zdarzeń

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano sposób tworzenia i konfigurowania centrum zdarzeń w usłudze Azure Event Hubs. Opisano w nim również sposób uruchamiania przykładowej aplikacji w celu wypchnięcia zdarzeń do Azure Time Series Insights z Event Hubs. Jeśli masz istniejące centrum zdarzeń ze zdarzeniami w formacie JSON, Pomiń ten samouczek i Sprawdź środowisko w [Azure Time Series Insights](./tutorial-set-up-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń

1. Aby dowiedzieć się, jak utworzyć centrum zdarzeń, Przeczytaj [dokumentację dotyczącą Event Hubs](../event-hubs/index.yml).
1. W polu wyszukiwania Wyszukaj **Event Hubs**. Na liście zwracanej wybierz pozycję **Event Hubs**.
1. Wybierz centrum zdarzeń.
1. Podczas tworzenia centrum zdarzeń tworzona jest przestrzeń nazw centrum zdarzeń. Jeśli nie utworzono jeszcze centrum zdarzeń w przestrzeni nazw, w menu w obszarze **jednostki** Utwórz centrum zdarzeń.

    [![Lista centrów zdarzeń](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Po utworzeniu centrum zdarzeń wybierz je na liście centrów zdarzeń.
1. W menu w obszarze **jednostki** wybierz pozycję **Event Hubs**.
1. Wybierz nazwę centrum zdarzeń, aby je skonfigurować.
1. W obszarze **Przegląd** wybierz pozycję **grupy użytkowników**, a następnie wybierz pozycję **Grupa odbiorców**.

    [![Tworzenie grupy odbiorców](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Upewnij się, że utworzono grupę odbiorców, która jest używana wyłącznie przez źródło zdarzeń Azure Time Series Insights.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa odbiorców nie jest używana przez żadną inną usługę, na przykład zadanie Azure Stream Analytics lub inne środowisko Azure Time Series Insights. Jeśli grupa odbiorców jest używana przez inne usługi, operacje odczytu mają negatywny wpływ na to środowisko i dla innych usług. Jeśli używasz **$default** jako grupy konsumentów, inni czytelnicy mogą potencjalnie ponownie wykorzystać grupę odbiorców.

1. W menu w obszarze **Ustawienia** wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz pozycję **Dodaj**.

    [![Wybierz pozycję Zasady dostępu współdzielonego, a następnie wybierz przycisk Dodaj.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. W okienku **Dodaj nowe zasady dostępu współdzielonego** Utwórz współużytkowany dostęp o nazwie **MySendPolicy**. Te zasady dostępu współdzielonego służą do wysyłania zdarzeń w przykładach w języku C# w dalszej części tego artykułu.

    [![W polu Nazwa zasad wpisz MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. W **obszarze** Zażądaj zaznacz pole wyboru **Wyślij** .

## <a name="add-an-azure-time-series-insights-instance"></a>Dodaj wystąpienie Azure Time Series Insights

W Azure Time Series Insights Gen2 można dodać dane kontekstowe do przychodzącej telemetrii przy użyciu modelu szeregów czasowych (TSM). W TSM Tagi lub sygnały są określane jako *wystąpienia* i można przechowywać dane kontekstowe w *polach wystąpień.* Dane są przyłączone w czasie zapytania przy użyciu **identyfikatora szeregów czasowych**. **Identyfikator szeregów czasowych** dla przykładowego projektu Windmills, który jest używany w dalszej części tego artykułu `id` . Aby dowiedzieć się więcej o przechowywaniu danych w polach wystąpienia, zobacz Omówienie [modelu szeregów czasowych](./concepts-model-overview.md) .

### <a name="create-an-azure-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń Azure Time Series Insights

1. Jeśli nie utworzono źródła zdarzeń, wykonaj kroki, aby [utworzyć źródło zdarzenia](./how-to-ingest-data-event-hub.md).

1. Ustaw wartość dla parametru `timeSeriesId` . Aby dowiedzieć się więcej na temat **identyfikatora szeregów czasowych**, Przeczytaj [modele szeregów czasowych](./concepts-model-overview.md).

### <a name="push-events-to-windmills-sample"></a>Wypchnij zdarzenia do przykładu Windmills

1. Na pasku wyszukiwania Wyszukaj **Event Hubs**. Na liście zwracanej wybierz pozycję **Event Hubs**.

1. Wybierz wystąpienie centrum zdarzeń.

1. Przejdź do **zasad dostępu współdzielonego**  >  **MySendPolicy**. Skopiuj wartość **parametrów połączenia — klucz podstawowy**.

    [![Skopiuj wartość parametrów połączenia klucza podstawowego](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Przejdź do witryny <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. Adres URL tworzy i uruchamia symulowane urządzenia Windmill.
1. W polu **Parametry połączenia centrum zdarzeń** na stronie sieci Web wklej parametry połączenia, które zostały skopiowane do [pola wejściowego Windmill](#push-events-to-windmills-sample).

    [![Wklej parametry połączenia klucza podstawowego w polu parametrów połączenia centrum zdarzeń](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Wybierz **pozycję kliknij, aby rozpocząć**.

    > [!TIP]
    > Symulator Windmill tworzy również kod JSON, którego można użyć jako ładunku z [interfejsami API zapytań Azure Time Series INSIGHTS ga](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > Symulator będzie kontynuował wysyłanie danych, dopóki karta przeglądarki nie zostanie zamknięta.

1. Wróć do centrum zdarzeń w Azure Portal. Na stronie **Przegląd** zostaną wyświetlone nowe zdarzenia odebrane przez centrum zdarzeń.

    [![Strona omówienia centrum zdarzeń, która przedstawia metryki centrum zdarzeń](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Obsługiwane kształty JSON

### <a name="example-one"></a>Przykład jeden

* **Dane wejściowe**: prosty obiekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Dane wyjściowe**: jedno zdarzenie.

    |identyfikator|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Przykład dwa

* **Dane wejściowe**: tablica JSON z dwoma obiektami JSON. Każdy obiekt JSON jest konwertowany na zdarzenie.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Dane wyjściowe**: dwa zdarzenia.

    |identyfikator|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Przykład trzy

* **Wejście**: obiekt JSON z zagnieżdżoną tablicą JSON, która zawiera dwa obiekty JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Dane wyjściowe**: dwa zdarzenia. **Lokalizacja** właściwości jest kopiowana do każdego zdarzenia.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Przykład cztery

* **Wejście**: obiekt JSON z zagnieżdżoną tablicą JSON, która zawiera dwa obiekty JSON. To dane wejściowe pokazują, że właściwości globalne mogą być reprezentowane przez złożony obiekt JSON.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Dane wyjściowe**: dwa zdarzenia.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|ciśnienie|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Następne kroki

* [Wyświetl swoje środowisko](https://insights.timeseries.azure.com) w Eksploratorze Azure Time Series Insights.

* Przeczytaj więcej na temat [IoT Hub komunikatów urządzenia](../iot-hub/iot-hub-devguide-messages-construct.md)
---
title: Jak dodać źródło zdarzeń centrum IoT — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać źródło zdarzeń centrum IoT do środowiska usługi Time Series Insights.
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
ms.openlocfilehash: a0a2f703d9224b8b9dd77c80b2b6a7faee70f5bb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538107"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Dodawanie źródła zdarzeń centrum IoT do środowiska usługi Time Series Insights

W tym artykule opisano sposób korzystania z witryny Azure Portal, aby dodać źródło zdarzeń, które odczytuje dane z usługi Azure IoT Hub do środowiska usługi Azure Time Series Insights.

> [!NOTE]
> Instrukcje w tym artykule dotyczą zarówno usługi Azure Time Series Insights GA, jak i środowisk usługi Time Series Insights Preview.

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [środowiska usługi Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Utwórz [centrum IoT za pomocą portalu Azure](../iot-hub/iot-hub-create-through-portal.md).
* Centrum IoT musi mieć aktywne zdarzenia wiadomości wysyłane w.
* Utwórz dedykowaną grupę odbiorców w centrum IoT hub dla środowiska usługi Time Series Insights, z której można korzystać. Każde źródło zdarzeń usługi Time Series Insights musi mieć własną dedykowaną grupę odbiorców, która nie jest udostępniana żadnemu innemu konsumentowi. Jeśli wielu czytelników zużywają zdarzenia z tej samej grupy odbiorców, wszyscy czytelnicy mogą wykazywać błędy. Aby uzyskać szczegółowe informacje, przeczytaj [przewodnik dla deweloperów usługi Azure IoT Hub.](../iot-hub/iot-hub-devguide.md)

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT

Aplikacje używają grup odbiorców do ściągania danych z usługi Azure IoT Hub. Aby niezawodnie odczytywać dane z centrum IoT hub, należy udostępnić dedykowaną grupę odbiorców, która jest używana tylko przez to środowisko usługi Time Series Insights.

Aby dodać nową grupę odbiorców do centrum IoT hub:

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz centrum IoT Hub.

1. W obszarze **Ustawienia**wybierz pozycję **Wbudowane punkty końcowe**, a następnie wybierz punkt końcowy **zdarzenia.**

   [![Na stronie Wbudowane punkty końcowe wybierz przycisk Zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. W obszarze **Grupy odbiorców**wprowadź unikatową nazwę grupy odbiorców. Tej samej nazwy należy używać w środowisku usługi Time Series Insights podczas tworzenia nowego źródła zdarzeń.

1. Wybierz pozycję **Zapisz**.

## <a name="add-a-new-event-source"></a>Dodawanie nowego źródła zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Wybierz środowisko usługi Time Series Insights.

1. W obszarze **Ustawienia**wybierz pozycję **Źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![Wybierz pozycję Źródła zdarzeń, a następnie wybierz przycisk Dodaj](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. W okienku **Źródło nowego zdarzenia** dla nazwy źródła **zdarzenia**wprowadź nazwę unikatową dla tego środowiska usługi Time Series Insights. Na przykład wprowadź **strumień zdarzeń**.

1. W obszarze **Źródło**wybierz pozycję **IoT Hub**.

1. Wybierz wartość dla **opcji Importuj:**

   * Jeśli masz już centrum IoT hub w jednej z subskrypcji, wybierz **pozycję Użyj Usługi IoT Hub z dostępnych subskrypcji.** Ta opcja jest najprostszym podejściem.
   
     [![Wybieranie opcji w okienku Źródło nowego zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * W poniższej tabeli opisano właściwości, które są wymagane dla **opcji Użyj Usługi IoT Hub z dostępnych subskrypcji:**

       [![Nowe okienko źródła zdarzeń — właściwości ustawione w opcji Użyj Usługi IoT Hub z dostępnych subskrypcji](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja, do której należy żądana usługa Iot Hub. |
       | Nazwa centrum IoT | Nazwa wybranego centrum iot. |
       | Nazwa zasad centrum IoT | Wybierz zasady dostępu współdzielonego. Zasady dostępu współdzielonego można znaleźć na karcie Ustawienia centrum IoT. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia **do łączenia usługi.** |
       | Klucz zasad centrum IoT | Klucz jest wstępnie wypełniony. |

    * Jeśli centrum IoT hub jest zewnętrzne dla subskrypcji lub chcesz wybrać opcje zaawansowane, wybierz **opcję Podaj ustawienia usługi IoT Hub ręcznie**.

      W poniższej tabeli opisano wymagane właściwości **ustawień centrum IoT ręcznie:**

       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja, do której należy żądana usługa Iot Hub. |
       | Grupa zasobów | Nazwa grupy zasobów, w której utworzono centrum IoT Hub. |
       | Nazwa centrum IoT | Nazwa centrum IoT Hub. Podczas tworzenia centrum IoT hub, wprowadzono nazwę centrum IoT hub. |
       | Nazwa zasad centrum IoT | Zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie Ustawienia centrum IoT. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia **do łączenia usługi.** |
       | Klucz zasad centrum IoT | Klucz dostępu współdzielonego, który służy do uwierzytelniania dostępu do obszaru nazw usługi Azure Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

    * Obie opcje mają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa odbiorców centrum IoT | Grupa odbiorców, która odczytuje zdarzenia z centrum IoT Hub. Zdecydowanie zaleca się używanie dedykowanej grupy odbiorców dla źródła zdarzeń. |
       | Format serializacji zdarzeń | Obecnie JSON jest jedynym dostępnym formatem serializacji. Komunikaty o zdarzeniach muszą być w tym formacie lub nie można odczytać żadnych danych. |
       | Nazwa właściwości znacznika czasu | Aby określić tę wartość, należy zrozumieć format wiadomości danych wiadomości, które są wysyłane do centrum IoT hub. Ta wartość jest **nazwą** właściwości określonego zdarzenia w danych wiadomości, które mają być używane jako sygnatura czasowa zdarzenia. W wartości jest rozróżniana wielkość liter. Jeśli pozostanie puste, **czas wyłowić zdarzenie** w źródle zdarzenia jest używany jako sygnatura czasowa zdarzenia. |


1. Dodaj dedykowaną nazwę grupy odbiorców usługi Time Series Insights, która została dodana do centrum IoT Hub.

1. Wybierz pozycję **Utwórz**.

1. Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) w celu zabezpieczenia danych.

* [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzenia.

* Uzyskaj dostęp do środowiska w [Eksploratorze Usługi Time Series Insights](https://insights.timeseries.azure.com).

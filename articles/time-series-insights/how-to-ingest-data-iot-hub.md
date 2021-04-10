---
title: Jak dodać źródło zdarzeń centrum IoT Hub — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak dodać źródło zdarzeń centrum IoT Hub do środowiska usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 85773ec66dbe567afcfd1afca1d14aa0119fb8f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464024"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Dodawanie źródła zdarzeń centrum IoT Hub do środowiska usługi Azure Time Series Insights

W tym artykule opisano, jak za pomocą Azure Portal dodać Źródło zdarzenia, które odczytuje dane z usługi Azure IoT Hub do środowiska Azure Time Series Insights.

> [!NOTE]
> Instrukcje zawarte w tym artykule mają zastosowanie do Azure Time Series Insights generacji 1 i w środowiskach usługi Azure Time Series Insights w generacji 2.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [środowisko Azure Time Series Insights](./tutorial-set-up-environment.md).
* Utwórz [Centrum IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Usługa IoT Hub musi mieć aktywne zdarzenia komunikatów wysyłane w usłudze.
* Utwórz dedykowaną grupę odbiorców w centrum IoT Hub, aby można było używać środowiska usługi Azure Time Series Insights. Każde źródło zdarzeń usługi Azure Time Series Insights musi mieć własną dedykowaną grupę odbiorców, która nie jest współużytkowana z żadnym innym klientem. Jeśli wielu czytników zużywa zdarzenia z tej samej grupy odbiorców, wszystkie czytelnicy mogą wykazywać błędy. Aby uzyskać szczegółowe informacje, Przeczytaj [Przewodnik dla deweloperów platformy Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT Hub

Aplikacje używają grup konsumenckich do ściągania danych z usługi Azure IoT Hub. Aby niezawodnie odczytywać dane z usługi IoT Hub, podaj dedykowaną grupę użytkowników, która jest używana tylko przez to środowisko usługi Azure Time Series Insights.

Aby dodać nową grupę odbiorców do centrum IoT:

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz swoje centrum IoT Hub.

1. W obszarze **Ustawienia** wybierz pozycję **wbudowane punkty końcowe**, a następnie wybierz punkt końcowy **zdarzeń** .

   [![Na stronie punkty końcowe kompilacji wybierz przycisk zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. W obszarze **grupy odbiorców** wprowadź unikatową nazwę grupy odbiorców. Użyj tej samej nazwy w środowisku usługi Azure Time Series Insights podczas tworzenia nowego źródła zdarzeń.

1. Wybierz pozycję **Zapisz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Wybierz środowisko usługi Azure Time Series Insights.

1. W obszarze **Ustawienia** wybierz pozycję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![Wybierz pozycję źródła zdarzeń, a następnie wybierz przycisk Dodaj.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. W okienku **Nowy Źródło zdarzenia** w polu **Nazwa źródła zdarzenia** wprowadź nazwę unikatową dla tego środowiska usługi Azure Time Series Insights. Na przykład wprowadź **strumień zdarzeń**.

1. W obszarze **Źródło** wybierz pozycję **IoT Hub**.

1. Wybierz wartość **opcji importowania**:

   * Jeśli masz już Centrum IoT Hub w jednej z subskrypcji, wybierz opcję **użyj IoT Hub z dostępnych subskrypcji**. Ta opcja jest najprostszym podejściem.

     [![Wybierz opcje w okienku Nowy Źródło zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * W poniższej tabeli opisano właściwości, które są wymagane dla opcji **użyj IoT Hub z dostępnych subskrypcji** :

       [![Nowe okienko źródła zdarzeń — właściwości do ustawienia w opcji Użyj IoT Hub z dostępnych subskrypcji](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja, do której należy wymagane Centrum IoT. |
       | Nazwa Centrum IoT Hub | Nazwa wybranego Centrum IoT Hub. |
       | Nazwa zasad Centrum IoT | Wybierz zasady dostępu współdzielonego. Zasady dostępu współdzielonego można znaleźć na karcie Ustawienia Centrum IoT. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **połączenia z usługą** . |
       | Klucz zasad Centrum IoT | Klucz jest wstępnie wypełniony. |

   * Jeśli Centrum IoT znajduje się poza subskrypcjami lub chcesz wybrać opcje zaawansowane, wybierz opcję **podaj IoT Hub ustawienia ręcznie**.

      W poniższej tabeli opisano wymagane właściwości **Ustawienia podaj IoT Hub ręcznie**:

       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja, do której należy wymagane Centrum IoT. |
       | Grupa zasobów | Nazwa grupy zasobów, w której utworzono Centrum IoT Hub. |
       | Nazwa Centrum IoT Hub | Nazwa Centrum IoT. Po utworzeniu usługi IoT Hub wprowadzono nazwę Centrum IoT. |
       | Nazwa zasad Centrum IoT | Zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie Ustawienia Centrum IoT. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **połączenia z usługą** . |
       | Klucz zasad Centrum IoT | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Azure Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

   * Obie opcje udostępniają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa konsumentów Centrum IoT Hub | Grupa konsumentów, która odczytuje zdarzenia z Centrum IoT Hub. Zdecydowanie zalecamy użycie dedykowanej grupy odbiorców dla źródła zdarzeń. |
       | Format serializacji zdarzeń | Obecnie kod JSON jest jedynym dostępnym formatem serializacji. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
       | Nazwa właściwości znacznika czasu | Aby określić tę wartość, należy zrozumieć format wiadomości przesyłanych do centrum IoT Hub. Ta wartość jest **nazwą** konkretnej właściwości zdarzenia w danych komunikatu, która ma być używana jako sygnatura czasowa zdarzenia. W tej wartości jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas kolejki zdarzeń** w źródle zdarzenia jest używany jako sygnatura czasowa zdarzenia. |

1. Dodaj dedykowaną nazwę grupy odbiorców usługi Azure Time Series Insights dodaną do centrum IoT Hub.

1. Wybierz przycisk **Utwórz**.

1. Po utworzeniu źródła zdarzeń usługa Azure Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](./concepts-access-policies.md) w celu zabezpieczenia danych.

* [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzeń.

* Uzyskaj dostęp do środowiska w [Eksploratorze usługi Azure Time Series Insights](https://insights.timeseries.azure.com).

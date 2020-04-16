---
title: Dodawanie źródła zdarzeń centrum zdarzeń — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać źródło zdarzeń usługi Azure Event Hubs do środowiska usługi Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 021ac5fccf4d694895ab9941bd46dd2388f49af9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407454"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Dodawanie źródła zdarzeń centrum zdarzeń do środowiska usługi Time Series Insights

W tym artykule opisano sposób korzystania z witryny Azure Portal, aby dodać źródło zdarzeń, które odczytuje dane z usługi Azure Event Hubs do środowiska usługi Azure Time Series Insights.

> [!NOTE]
> Kroki opisane w tym artykule dotyczą zarówno środowisk ga usługi Time Series Insights, jak i time series insights preview.

## <a name="prerequisites"></a>Wymagania wstępne

- Tworzenie środowiska usługi Time Series Insights zgodnie z [opisem](./time-series-insights-update-create-environment.md)w obszarze Tworzenie środowiska usługi Azure Time Series Insights .
- Utwórz centrum zdarzeń. Przeczytaj [artykuł Tworzenie obszaru nazw centrów zdarzeń i centrum zdarzeń przy użyciu portalu Azure](../event-hubs/event-hubs-create.md).
- Centrum zdarzeń musi mieć aktywne zdarzenia wiadomości wysłane do niego. Dowiedz się, jak [wysyłać zdarzenia do centrum zdarzeń platformy Azure przy użyciu programu .NET Framework.](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)
- Utwórz dedykowaną grupę odbiorców w centrum zdarzeń, z której może korzystać środowisko usługi Time Series Insights. Każde źródło zdarzeń usługi Time Series Insights musi mieć własną dedykowaną grupę odbiorców, która nie jest udostępniana żadnemu innemu konsumentowi. Jeśli wielu czytelników zużywają zdarzenia z tej samej grupy odbiorców, wszyscy czytelnicy mogą wykazywać błędy. Istnieje limit 20 grup odbiorców na centrum zdarzeń. Aby uzyskać szczegółowe informacje, przeczytaj [przewodnik po programowaniu centrum zdarzeń](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodawanie grupy odbiorców do centrum zdarzeń

Aplikacje używają grup odbiorców do ściągania danych z usługi Azure Event Hubs. Aby niezawodnie odczytywać dane z centrum zdarzeń, należy udostępnić dedykowaną grupę odbiorców, która jest używana tylko przez to środowisko usługi Time Series Insights.

Aby dodać nową grupę odbiorców w centrum zdarzeń:

1. W [witrynie Azure Portal](https://portal.azure.com)znajdź i otwórz wystąpienie centrum zdarzeń w okienku **Przegląd** obszaru nazw centrum zdarzeń. Wybierz **pozycję Encje > Centrum zdarzeń** lub znajdź wystąpienie w obszarze **Nazwa**.

    [![Otwieranie obszaru nazw centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. W wystąpieniu centrum zdarzeń wybierz pozycję **Jednostki > grupy odbiorców**. Następnie wybierz **+ Grupa Konsument,** aby dodać nową grupę odbiorców. 

   [![Centrum zdarzeń — dodawanie grupy odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   W przeciwnym razie wybierz istniejącą grupę odbiorców i przejdź do następnej sekcji.

1. Na stronie **Grupy odbiorców** wprowadź nową unikatową wartość **name**.  Tej samej nazwy należy używać podczas tworzenia nowego źródła zdarzeń w środowisku usługi Time Series Insights.

1. Wybierz pozycję **Utwórz**.

## <a name="add-a-new-event-source"></a>Dodawanie nowego źródła zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko usługi Time Series Insights. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**, a następnie wybierz środowisko usługi Time Series Insights.

1. Wybierz **pozycję Źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![W obszarze Źródła zdarzeń wybierz przycisk Dodaj](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Wprowadź wartość **nazwy źródła zdarzeń,** która jest unikatowa dla `Contoso-TSI-GA-Event-Hub-ES`tego środowiska usługi Time Series Insights, na przykład .

1. W obszarze **Źródło**wybierz pozycję **Centrum zdarzeń**.

1. Wybierz odpowiednie wartości dla **opcji Importuj:**

   * Jeśli masz istniejące centrum zdarzeń w jednej z subskrypcji, wybierz **pozycję Użyj Centrum zdarzeń z dostępnych subskrypcji.** Ta opcja jest najprostszym podejściem.

     [![Wybieranie opcji importowania źródła zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  W poniższej tabeli opisano wymagane właściwości opcji **Użyj Usługi eventów z dostępnych subskrypcji:**

       [![Szczegóły subskrypcji i centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja żądane wystąpienie centrum zdarzeń i obszar nazw należy do. |
       | Przestrzeń nazw centrum zdarzeń | Obszar nazw centrum zdarzeń, do którego należy żądane wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad Centrum zdarzeń | Wybierz żądaną zasadę dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguruj** centrum zdarzeń. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia **do odczytu.** |
       | Klucz zasad Centrum zdarzeń | Wstępnie wypełnione z wybranej wartości zasad Centrum zdarzeń. |

    * Jeśli centrum zdarzeń jest zewnętrzne w ramach subskrypcji lub chcesz wybrać opcje zaawansowane, wybierz **opcję Podaj ustawienia Centrum zdarzeń ręcznie**.

       W poniższej tabeli opisano wymagane właściwości opcji **Podaj centrum zdarzeń ręcznie:**
 
       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja żądane wystąpienie centrum zdarzeń i obszar nazw należy do. |
       | Grupa zasobów | Grupa zasobów, do której należy żądane wystąpienie centrum zdarzeń i obszar nazw. |
       | Przestrzeń nazw centrum zdarzeń | Obszar nazw centrum zdarzeń, do którego należy żądane wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad Centrum zdarzeń | Wybierz żądaną zasadę dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguruj** centrum zdarzeń. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia **do odczytu.** |
       | Klucz zasad Centrum zdarzeń | Klucz dostępu współdzielonego, który służy do uwierzytelniania dostępu do obszaru nazw usługi Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

    * Obie opcje mają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa konsumentów centrum zdarzeń | Grupa odbiorców, która odczytuje zdarzenia z centrum zdarzeń. Zdecydowanie zaleca się używanie dedykowanej grupy odbiorców dla źródła zdarzeń. |
       | Format serializacji zdarzeń | Obecnie JSON jest jedynym dostępnym formatem serializacji. Komunikaty o zdarzeniach muszą być w tym formacie lub nie można odczytać danych. |
       | Nazwa właściwości znacznika czasu | Aby określić tę wartość, należy zrozumieć format wiadomości danych wiadomości, które są wysyłane do centrum zdarzeń. Ta wartość jest **nazwą** właściwości określonego zdarzenia w danych wiadomości, które mają być używane jako sygnatura czasowa zdarzenia. W wartości jest rozróżniana wielkość liter. Jeśli pozostanie puste, **czas wyłowić zdarzenie** w źródle zdarzenia jest używany jako sygnatura czasowa zdarzenia. |

1. Dodaj dedykowaną nazwę grupy odbiorców usługi Time Series Insights, która została dodana do centrum zdarzeń.

1. Wybierz pozycję **Utwórz**.

   Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) w celu zabezpieczenia danych.

* [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzenia.

* Uzyskaj dostęp do środowiska w [Eksploratorze Usługi Time Series Insights](https://insights.timeseries.azure.com).

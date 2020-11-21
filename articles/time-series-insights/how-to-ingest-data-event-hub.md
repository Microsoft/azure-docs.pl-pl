---
title: Dodawanie Event Hubs źródła zdarzeń — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak dodać źródło zdarzeń Event Hubs platformy Azure do środowiska Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 7d672da30a4d81ead9115fc6b829c35aeab3ee6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016823"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Dodawanie źródła zdarzeń centrum zdarzeń do środowiska Azure Time Series Insights

W tym artykule opisano, jak za pomocą Azure Portal dodać Źródło zdarzenia, które odczytuje dane z usługi Azure Event Hubs do środowiska Azure Time Series Insights.

> [!NOTE]
> Kroki opisane w tym artykule dotyczą zarówno środowiska Azure Time Series Insights Gen 1, jak i Azure Time Series Insights generacji 2.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz środowisko Azure Time Series Insights zgodnie z opisem w artykule [Tworzenie środowiska Azure Time Series Insights](./tutorials-set-up-tsi-environment.md).
- Utwórz centrum zdarzeń. Przeczytaj temat [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum zdarzeń musi mieć wysłane aktywne zdarzenia komunikatów. Dowiedz się [, jak wysyłać zdarzenia do usługi Azure Event Hubs przy użyciu .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Utwórz dedykowaną grupę odbiorców w centrum zdarzeń, z której może korzystać środowisko Azure Time Series Insights. Każde źródło zdarzeń Azure Time Series Insights musi mieć własną dedykowaną grupę odbiorców, która nie jest współużytkowana z żadnym innym klientem. Jeśli wielu czytników zużywa zdarzenia z tej samej grupy odbiorców, wszystkie czytelnicy mogą wykazywać błędy. Istnieje limit 20 grup odbiorców dla centrum zdarzeń. Aby uzyskać szczegółowe informacje, Przeczytaj [Przewodnik programowania w Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodawanie grupy odbiorców do centrum zdarzeń

Aplikacje używają grup konsumenckich do ściągania danych z usługi Azure Event Hubs. Aby niezawodnie odczytywać dane z centrum zdarzeń, należy udostępnić dedykowaną grupę użytkowników, która jest używana tylko przez to Azure Time Series Insights środowisku.

Aby dodać nową grupę odbiorców w centrum zdarzeń:

1. W [Azure Portal](https://portal.azure.com)zlokalizuj i Otwórz wystąpienie centrum zdarzeń w okienku **Przegląd** obszaru nazw centrum zdarzeń. Wybierz **jednostki > Event Hubs** lub Znajdź wystąpienie w polu **Nazwa**.

    [![Otwórz przestrzeń nazw centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. W wystąpieniu centrum zdarzeń wybierz pozycję **jednostki > grupy konsumentów**. Następnie wybierz pozycję **+ Grupa konsumentów** , aby dodać nową grupę odbiorców.

   [![Centrum zdarzeń — Dodaj grupę odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   W przeciwnym razie wybierz istniejącą grupę odbiorców i przejdź do następnej sekcji.

1. Na stronie **grupy odbiorców** wprowadź nową unikatową wartość dla **nazwy**.  Użyj tej samej nazwy podczas tworzenia nowego źródła zdarzeń w środowisku Azure Time Series Insights.

1. Wybierz pozycję **Utwórz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko Azure Time Series Insights. W menu po lewej stronie wybierz pozycję **wszystkie zasoby**, a następnie wybierz środowisko Azure Time Series Insights.

1. Wybierz pozycję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![W obszarze źródła zdarzeń wybierz przycisk Dodaj.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Wprowadź wartość dla **nazwy źródła zdarzenia** , która jest unikatowa dla tego środowiska Azure Time Series Insights, na przykład `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. W obszarze **Źródło** wybierz pozycję **centrum zdarzeń**.

1. Wybierz odpowiednie wartości **opcji importowania**:

   - Jeśli masz istniejące centrum zdarzeń w jednej z subskrypcji, wybierz pozycję **Użyj centrum zdarzeń z dostępnych subskrypcji**. Ta opcja jest najprostszym podejściem.

     [![Wybierz opcję importowania źródła zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - W poniższej tabeli opisano wymagane właściwości opcji **Użyj centrum zdarzeń z dostępnych subskrypcji** :

       [![Szczegóły subskrypcji i centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń, do którego należy wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad centrum zdarzeń | Wybierz odpowiednie zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **odczytu** . |
       | Klucz zasad centrum zdarzeń | Wstępnie wypełnione z wybranej wartości zasad centrum zdarzeń. |

   - Jeśli centrum zdarzeń jest zewnętrzne dla subskrypcji lub jeśli chcesz wybrać opcje zaawansowane, wybierz opcję **Podaj ustawienia centrum zdarzeń ręcznie**.

       W poniższej tabeli opisano wymagane właściwości opcji **Podaj ręcznie ustawienia centrum zdarzeń** :

       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Grupa zasobów | Grupa zasobów, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń, do którego należy wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad centrum zdarzeń | Wybierz odpowiednie zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **odczytu** . |
       | Klucz zasad centrum zdarzeń | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

   - Obie opcje udostępniają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa konsumentów centrum zdarzeń | Grupa konsumentów, która odczytuje zdarzenia z centrum zdarzeń. Zdecydowanie zalecamy użycie dedykowanej grupy odbiorców dla źródła zdarzeń. |
       | Format serializacji zdarzeń | Obecnie kod JSON jest jedynym dostępnym formatem serializacji. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
       | Nazwa właściwości znacznika czasu | Aby określić tę wartość, należy zrozumieć format wiadomości przesyłanych do centrum zdarzeń. Ta wartość jest **nazwą** konkretnej właściwości zdarzenia w danych komunikatu, która ma być używana jako sygnatura czasowa zdarzenia. W tej wartości jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas kolejki zdarzeń** w źródle zdarzenia jest używany jako sygnatura czasowa zdarzenia. |

1. Dodaj dedykowaną nazwę grupy konsumentów Azure Time Series Insights, która została dodana do centrum zdarzeń.

1. Wybierz pozycję **Utwórz**.

   Po utworzeniu źródła zdarzeń Azure Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

- [Zdefiniuj zasady dostępu do danych](./concepts-access-policies.md) w celu zabezpieczenia danych.

- [Wyślij zdarzenia](time-series-insights-send-events.md) do źródła zdarzeń.

- Dostęp do środowiska w [eksploratorze Azure Time Series Insights](https://insights.timeseries.azure.com).
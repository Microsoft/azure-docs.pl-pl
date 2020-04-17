---
title: Filtry tematów usługi Azure Service Bus | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak subskrybenci mogą definiować wiadomości, które mają otrzymywać z tematu, określając filtry.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482062"
---
# <a name="topic-filters-and-actions"></a>Filtry tematów i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Komunikaty te są określone w formie co najmniej jednej nazwanej reguły subskrypcji. Każda reguła składa się z warunku, który wybiera określone wiadomości i akcji, która adnotuje wybrany komunikat. Dla każdego pasującego warunku reguły subskrypcja tworzy kopię komunikatu, która może być inaczej adnotowana dla każdej pasującej reguły.

Każda nowo utworzona subskrypcja tematu ma początkową domyślną regułę subskrypcji. Jeśli nie zostanie jawnie określony warunek filtru dla reguły, zastosowany filtr jest **prawdziwym** filtrem, który umożliwia wybranie wszystkich wiadomości do subskrypcji. Reguła domyślna nie ma skojarzonej akcji adnotacji.

Usługa Service Bus obsługuje trzy warunki filtrowania:

-   *Filtry logiczne* — **TrueFilter** i **FalseFilter** powodują, że wszystkie przychodzące wiadomości **(true)** lub żadna z przyjmujących wiadomości **(false)** ma być wybrana dla subskrypcji.

-   *Filtry SQL* — **SqlFilter** przechowuje wyrażenie warunkowe podobne do SQL, które jest oceniane w brokera względem właściwości zdefiniowanych przez użytkownika wiadomości przybywających i właściwości systemu. Wszystkie właściwości systemu muszą być `sys.` poprzedzone w wyrażeniu warunkowym. [Podzbiór języka SQL dla testów warunków filtru](service-bus-messaging-sql-filter.md) dla istnienia właściwości (`EXISTS`), wartości null (`IS NULL`), logiczne NOT/I/OR, operatory `LIKE`relacyjne, prosta arytmetyka numeryczna i proste dopasowanie wzorca tekstu z .

-   *Filtry korelacji* — **Filtr korelacji** zawiera zestaw warunków, które są dopasowane do jednego lub więcej właściwości użytkownika i systemu przychodzącego komunikatu. Typowym zastosowaniem jest dopasowanie do **CorrelationId** właściwości, ale aplikacja może również wybrać dopasowywać się do następujących właściwości:

    - **Contenttype**
     - **Label**
     - **Messageid**
     - **Replyto**
     - **ReplyToSessionId**
     - **Sessionid** 
     - **Do**
     - właściwości zdefiniowane przez użytkownika. 
     
     Dopasowanie istnieje, gdy wartość przychodzącego komunikatu dla właściwości jest równa wartości określonej w filtrze korelacji. W przypadku wyrażeń ciągów porównanie jest rozróżniane. Podczas określania wielu właściwości dopasowania, filtr łączy je jako warunek logiczny i, co oznacza, że filtr do dopasowania, wszystkie warunki muszą być zgodne.

Wszystkie filtry oceniają właściwości wiadomości. Filtry nie mogą ocenić treści wiadomości.

Złożone reguły filtrowania wymagają zdolności przetwarzania. W szczególności użycie reguł filtru SQL powoduje niższą ogólną przepływność komunikatów na poziomie subskrypcji, tematu i obszaru nazw. Jeśli to możliwe, aplikacje powinny wybrać filtry korelacji za pośrednictwem filtrów podobnych do SQL, ponieważ są one znacznie bardziej wydajne w przetwarzaniu i mają mniejszy wpływ na przepływność.

## <a name="actions"></a>Akcje

W warunkach filtru SQL można zdefiniować akcję, która może dodawać adnotacje do wiadomości, dodając, usuwając lub zastępując właściwości i ich wartości. Akcja [używa wyrażenia podobnego do SQL,](service-bus-messaging-sql-filter.md) które luźno opiera się na składni instrukcji SQL UPDATE. Akcja jest wykonywana w wiadomości po jej dopasowaniu i przed wybraniem wiadomości do subskrypcji. Zmiany we właściwościach wiadomości są prywatne dla wiadomości skopiowanych do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszym scenariuszem użycia dla tematu jest to, że każda subskrypcja pobiera kopię każdej wiadomości wysyłanej do tematu, co umożliwia wzorzec emisji.

Filtry i akcje umożliwiają dwie kolejne grupy wzorców: partycjonowanie i routing.

Partycjonowanie używa filtrów do dystrybucji wiadomości w kilku istniejących subskrypcji tematu w sposób przewidywalny i wzajemnie się wykluczają. Wzorzec partycjonowania jest używany, gdy system jest skalowany w poziomie do obsługi wielu różnych kontekstów w funkcjonalnie identycznych przedziałach, które każdy posiada podzbiór danych ogólnych; na przykład informacje o profilu klienta. Z partycjonowania wydawca przesyła wiadomość do tematu bez konieczności znajomości modelu partycjonowania. Komunikat następnie jest przenoszony do poprawnej subskrypcji, z której można następnie pobrać przez program obsługi wiadomości partycji.

Routing używa filtrów do dystrybucji wiadomości między subskrypcjami tematów w przewidywalny sposób, ale niekoniecznie wyłączne. W połączeniu z funkcją [automatycznego przekazywania,](service-bus-auto-forwarding.md) filtry tematów mogą służyć do tworzenia złożonych wykresów routingu w obszarze nazw usługi Service Bus do dystrybucji wiadomości w regionie platformy Azure. Dzięki usłudze Azure Functions lub Azure Logic Apps działającej jako pomost między obszarami nazw usługi Azure Service Bus można tworzyć złożone topologie globalne z bezpośrednią integracją z aplikacjami biznesowymi.


> [!NOTE]
> Obecnie witryna Azure portal nie pozwala określić reguły filtrowania dla subskrypcji. Do definiowania reguł subskrypcji można użyć dowolnego z obsługiwanych zestawów SDK lub szablonów usługi Azure Resource Manager. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przykłady: 

- [.NET - Podstawowe wysyłanie i odbieranie samouczek z filtrami](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtry tematów](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Wpisz skrypt](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Szablon usługi Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)



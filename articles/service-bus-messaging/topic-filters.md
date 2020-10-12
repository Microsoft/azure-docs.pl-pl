---
title: Filtry tematu Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak subskrybenci mogą definiować komunikaty, które chcą otrzymywać z tematu przez określenie filtrów.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5df343ff63c01a7cf10315b758e3d6fba8ac5674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066750"
---
# <a name="topic-filters-and-actions"></a>Filtry tematów i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Komunikaty te są określone w formie co najmniej jednej nazwanej reguły subskrypcji. Każda reguła składa się z warunku, który wybiera określone komunikaty i akcję, która oznacza wybrany komunikat. Dla każdego pasującego warunku reguły subskrypcja tworzy kopię komunikatu, która może być inaczej adnotowana dla każdej pasującej reguły.

Każda nowo utworzona subskrypcja tematu ma wstępną regułę domyślnej subskrypcji. Jeśli nie określisz jawnie warunku filtru dla reguły, stosowany filtr jest filtrem **true** , który umożliwia wybranie wszystkich komunikatów do subskrypcji. Reguła domyślna nie ma skojarzonej akcji adnotacji.

Service Bus obsługuje trzy warunki filtrowania:

-   *Filtry logiczne* — **TrueFilter** i **FalseFilter** powodują, że wszystkie przychodzące komunikaty (**true**) lub żaden z przychodzących komunikatów (**false**) do wybrania dla subskrypcji.

-   *Filtry SQL* — element **sqlfilter** zawiera wyrażenie warunkowe podobne do programu SQL, które jest oceniane w brokerze względem przychodzących komunikatów i właściwości systemu, które zostały zdefiniowane przez użytkownika. Wszystkie właściwości systemu muszą być poprzedzone prefiksem `sys.` w wyrażeniu warunkowym. [Podzbiór języka SQL na potrzeby testów warunków filtru](service-bus-messaging-sql-filter.md) dla istnienia właściwości ( `EXISTS` ), wartości null ( `IS NULL` ), logicznego not/i/lub operatorów relacyjnych, prostej arytmetycznej liczbowej i prostego dopasowania do wzorca tekstu `LIKE` .

-   *Filtry korelacji* — **CorrelationFilter** przechowuje zestaw warunków, które są dopasowane do co najmniej jednej właściwości użytkownika i systemu przychodzącego komunikatu. Typowym zastosowaniem jest dopasowanie do właściwości **Identyfikator korelacji** , ale aplikacja może również dopasować się do następujących właściwości:

    - **ContentType**
     - **Etykieta**
     - **Identyfikatora**
     - **From**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Działanie**
     - wszystkie właściwości zdefiniowane przez użytkownika. 
     
     Dopasowanie istnieje, gdy wartość przychodzącego komunikatu dla właściwości jest równa wartości określonej w filtrze korelacji. W przypadku wyrażeń ciągów w porównaniu jest rozróżniana wielkość liter. Podczas określania wielu właściwości dopasowania filtr łączy je jako logiczne i warunkowe, co oznacza, że filtr jest zgodny, wszystkie warunki muszą być zgodne.

Wszystkie filtry obliczają właściwości wiadomości. Filtry nie mogą oszacować treści komunikatu.

Złożone reguły filtru wymagają pojemności przetwarzania. W szczególności użycie reguł filtru SQL powoduje obniżenie ogólnej przepływności komunikatów na poziomie subskrypcji, tematu i przestrzeni nazw. Zawsze, gdy to możliwe, aplikacje powinny wybierać filtry korelacji dla filtrów przypominających SQL, ponieważ są one znacznie wydajniejsze w przetwarzaniu i mają mniejszy wpływ na przepływność.

## <a name="actions"></a>Akcje

Warunki filtru SQL umożliwiają zdefiniowanie akcji, która może dodawać adnotacje do wiadomości przez dodawanie, usuwanie lub zastępowanie właściwości i ich wartości. Akcja [używa wyrażenia podobnej do języka SQL](service-bus-messaging-sql-filter.md) , które luźno pochylenie w SKŁADNI instrukcji SQL Update. Akcja jest wykonywana po dopasowaniu i przed wybraniem komunikatu do subskrypcji. Zmiany właściwości wiadomości są prywatne dla wiadomości skopiowanej do subskrypcji.

## <a name="usage-patterns"></a>Wzorce użycia

Najprostszy scenariusz użycia tematu polega na tym, że każda subskrypcja pobiera kopię każdej wiadomości wysyłanej do tematu, co umożliwia użycie wzorca emisji.

Filtry i akcje umożliwiają włączenie dwóch grup wzorców: partycjonowanie i Routing.

Partycjonowanie używa filtrów do dystrybuowania komunikatów w kilku istniejących subskrypcjach tematu w sposób przewidywalny i wzajemnie wykluczający. Wzorzec partycjonowania jest używany, gdy system jest skalowany w celu obsługi wielu różnych kontekstów w funkcjonalnie identyczne przedziały, które każdy z nich utrzymuje podzestaw ogólnych danych; na przykład informacje o profilu klienta. W przypadku partycjonowania Wydawca przesyła komunikat do tematu bez konieczności znajomości modelu partycjonowania. Wiadomość zostanie przeniesiona do odpowiedniej subskrypcji, z której będzie można ją pobrać przy użyciu obsługi komunikatów partycji.

Funkcja routingu używa filtrów do dystrybuowania komunikatów między subskrypcjami tematów w przewidywalny sposób, ale niekoniecznie na wyłączność. W połączeniu z funkcją [autoprzekazywania](service-bus-auto-forwarding.md) filtry tematu mogą służyć do tworzenia złożonych wykresów routingu w ramach przestrzeni nazw Service Bus na potrzeby dystrybucji komunikatów w regionie świadczenia usługi Azure. W przypadku Azure Functions lub Azure Logic Apps działającego jako Most między przestrzeniami nazw Azure Service Bus można tworzyć złożone topologie globalne z bezpośrednią integracją z aplikacjami biznesowymi.


> [!NOTE]
> Ponieważ Azure Portal teraz obsługuje funkcje Eksploratora Service Bus, można utworzyć lub edytować filtry subskrypcji z portalu. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przykłady: 

- [.NET — samouczek dotyczący wysyłania i odbierania podstawowych z użyciem filtrów](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET — filtry tematów](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Szablon usługi Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)
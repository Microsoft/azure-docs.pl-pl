---
title: Omówienie integracji usług Azure Service Bus i Event Grid | Microsoft Docs
description: Ten artykuł zawiera opis sposobu, w jaki program Azure Service Bus Messaging integruje się z Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369666"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Omówienie integracji usług Azure Service Bus i Event Grid
Usługa Service Bus może teraz emitować zdarzenia do usługi Event Grid, jeśli kolejka lub subskrypcja zawiera komunikaty, a nie ma żadnych odbiorników. Można tworzyć subskrypcje usługi Event Grid powiązane z przestrzeniami nazw usługi Service Bus, nasłuchiwać tych zdarzeń, a następnie reagować na nie przez uruchomienie odbiornika. W przypadku tej funkcji usługa Service Bus może być używana w reaktywnych modelach programowania. Kluczowy scenariusz tej funkcji polega na tym, że subskrypcje lub kolejki usługi Service Bus z małą liczbą komunikatów nie muszą mieć odbiornika przeprowadzającego sondowanie w poszukiwaniu komunikatów. 

Do włączenia tej funkcji potrzebne są następujące elementy:

* Przestrzeń nazw usługi Service Bus w warstwie Premium z co najmniej jedną kolejką lub tematem usługi Service Bus i co najmniej jedną subskrypcją.
* Dostęp współautora do przestrzeni nazw usługi Service Bus. Przejdź do przestrzeni nazw Service Bus w Azure Portal, a następnie wybierz pozycję **Kontrola dostępu (IAM)** i wybierz pozycję Karta **przypisania ról** . Sprawdź, czy masz dostęp współautora do przestrzeni nazw. 
* Ponadto potrzebujesz subskrypcji usługi Event Grid dla przestrzeni nazw usługi Service Bus. Ta subskrypcja otrzymuje z usługi Event Grid powiadomienia o komunikatach do pobrania. Typowymi subskrybentami mogą być funkcja Logic Apps usługi Azure App Service, funkcje Azure Functions lub element webhook kontaktujący się z aplikacją internetową. Następnie subskrybent przetwarza komunikaty. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid subskrypcje Service Bus przestrzeni nazw
Istnieją trzy różne sposoby tworzenia subskrypcji usługi Event Grid dla przestrzeni nazw usługi Service Bus:

- Azure Portal. Zapoznaj się z poniższymi samouczkami, aby dowiedzieć się, jak za pomocą programu Azure Portal utworzyć subskrypcje Event Grid dla zdarzeń Service Bus z Azure Logic Apps i Azure Functions jako programy obsługi. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Interfejs wiersza polecenia platformy Azure. Poniższy przykład interfejsu wiersza polecenia przedstawia sposób tworzenia subskrypcji Azure Functions dla [tematu systemowego](../event-grid/system-topics.md) utworzonego przez Service Bus przestrzeni nazw.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- Narzędzia. Oto przykład:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Ile zdarzeń jest emitowanych i jak często?

Jeśli masz wiele kolejek i tematów/subskrypcji w przestrzeni nazw, odbierasz co najmniej jedno zdarzenie na kolejkę i jedno na subskrypcję. Zdarzenia są emitowane natychmiast, jeśli w jednostce usługi Service Bus nie ma komunikatów i nadejdzie nowy komunikat. W przeciwnym razie zdarzenia są emitowane co dwie minuty, chyba że usługa Service Bus wykryje aktywny odbiornik. Przeglądanie wiadomości nie przerywa zdarzeń.

Domyślnie usługa Service Bus emituje zdarzenia dla wszystkich jednostek w przestrzeni nazw. Jeśli chcesz odbierać zdarzenia tylko dla konkretnych jednostek, zapoznaj się z kolejną sekcją.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Odbieranie zdarzeń tylko z wybranych jednostek przy użyciu filtrów

Jeśli na przykład chcesz odbierać zdarzenia tylko z jednej kolejki lub jednej subskrypcji w przestrzeni nazw, możesz użyć filtrów *Zaczyna się od* i *Kończy się na* udostępnianych przez usługę Event Grid. W niektórych interfejsach filtry mają nazwy *Prefiks* i *Sufiks*. Jeśli chcesz odbierać zdarzenia z wielu, ale nie wszystkich, kolejek i subskrypcji, możesz utworzyć wiele subskrypcji usługi Event Grid i określić filtr dla każdej z nich.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki: 
- [Azure Logic Apps obsługiwać Service Bus komunikatów odebranych za pośrednictwem Event Grid](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions obsługiwać Service Bus komunikatów odebranych za pośrednictwem Event Grid](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png

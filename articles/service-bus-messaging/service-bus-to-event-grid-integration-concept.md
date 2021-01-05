---
title: Omówienie integracji usług Azure Service Bus i Event Grid | Microsoft Docs
description: Ten artykuł zawiera opis sposobu, w jaki program Azure Service Bus Messaging integruje się z Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5cc112767b2204d019cb1b7bd23b1603cefdf416
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882521"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Omówienie integracji usług Azure Service Bus i Event Grid

W usłudze Azure Service Bus uruchomiono nową opcję integracji z usługą Azure Event Grid. Kluczowy scenariusz tej funkcji polega na tym, że subskrypcje lub kolejki usługi Service Bus z małą liczbą komunikatów nie muszą mieć odbiornika przeprowadzającego sondowanie w poszukiwaniu komunikatów. 

Usługa Service Bus może teraz emitować zdarzenia do usługi Event Grid, jeśli kolejka lub subskrypcja zawiera komunikaty, a nie ma żadnych odbiorników. Można tworzyć subskrypcje usługi Event Grid powiązane z przestrzeniami nazw usługi Service Bus, nasłuchiwać tych zdarzeń, a następnie reagować na nie przez uruchomienie odbiornika. W przypadku tej funkcji usługa Service Bus może być używana w reaktywnych modelach programowania.

Do włączenia tej funkcji potrzebne są następujące elementy:

* Przestrzeń nazw usługi Service Bus w warstwie Premium z co najmniej jedną kolejką lub tematem usługi Service Bus i co najmniej jedną subskrypcją.
* Dostęp współautora do przestrzeni nazw usługi Service Bus.
* Ponadto potrzebujesz subskrypcji usługi Event Grid dla przestrzeni nazw usługi Service Bus. Ta subskrypcja otrzymuje z usługi Event Grid powiadomienia o komunikatach do pobrania. Typowymi subskrybentami mogą być funkcja Logic Apps usługi Azure App Service, funkcje Azure Functions lub element webhook kontaktujący się z aplikacją internetową. Następnie subskrybent przetwarza komunikaty. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Weryfikowanie dostępu współautora
Przejdź do przestrzeni nazw Service Bus, a następnie wybierz pozycję **Kontrola dostępu (IAM)** i wybierz pozycję Karta **przypisania ról** . Sprawdź, czy masz dostęp współautora do przestrzeni nazw. 

### <a name="events-and-event-schemas"></a>Zdarzenia i schematy zdarzeń

Obecnie usługa Service Bus wysyła zdarzenia w przypadku dwóch scenariuszy:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#deadletter-messages-available-event)
* [ActiveMessagesAvailablePeriodicNotifications](#active-messages-available-periodic-notifications)
* [DeadletterMessagesAvailablePeriodicNotifications](#deadletter-messages-available-periodic-notifications)

Ponadto usługa Service Bus używa standardowych zabezpieczeń usługi Event Grid i [mechanizmów uwierzytelniania](../event-grid/security-authentication.md).

Aby uzyskać więcej informacji, zobacz [Azure Event Grid event schemas (Schematy zdarzeń usługi Azure Event Grid)](../event-grid/event-schema.md).

#### <a name="active-messages-available-event"></a>Zdarzenie Dostępne aktywne komunikaty

To zdarzenie jest generowane, jeśli masz aktywne komunikaty w kolejce lub subskrypcji i nie ma nasłuchujących odbiorników.

Schemat tego zdarzenia jest następujący:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="deadletter-messages-available-event"></a>Zdarzenie dostępnego komunikatu o utraconych wiadomościach

Odbierasz co najmniej jedno zdarzenie na kolejkę utraconych komunikatów, która zawiera komunikaty, ale nie ma aktywnych odbiorników.

Schemat tego zdarzenia jest następujący:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Aktywne komunikaty dostępne okresowo powiadomienia

To zdarzenie jest generowane okresowo, jeśli masz aktywne wiadomości w określonej kolejce lub subskrypcji, nawet jeśli istnieją aktywne odbiorniki w danej kolejce lub subskrypcji.

Schemat dla zdarzenia jest następujący.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Komunikaty o utraconych powiadomieniach dostępne okresowo

To zdarzenie jest generowane okresowo w przypadku komunikatów utraconych w określonej kolejce lub subskrypcji, nawet jeśli istnieją aktywne odbiorniki w jednostce utraconej tej kolejki lub subskrypcji.

Schemat dla zdarzenia jest następujący.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Ile zdarzeń jest emitowanych i jak często?

Jeśli masz wiele kolejek i tematów/subskrypcji w przestrzeni nazw, odbierasz co najmniej jedno zdarzenie na kolejkę i jedno na subskrypcję. Zdarzenia są emitowane natychmiast, jeśli w jednostce usługi Service Bus nie ma komunikatów i nadejdzie nowy komunikat. W przeciwnym razie zdarzenia są emitowane co dwie minuty, chyba że usługa Service Bus wykryje aktywny odbiornik. Przeglądanie wiadomości nie przerywa zdarzeń.

Domyślnie usługa Service Bus emituje zdarzenia dla wszystkich jednostek w przestrzeni nazw. Jeśli chcesz odbierać zdarzenia tylko dla konkretnych jednostek, zapoznaj się z kolejną sekcją.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Odbieranie zdarzeń tylko z wybranych jednostek przy użyciu filtrów

Jeśli na przykład chcesz odbierać zdarzenia tylko z jednej kolejki lub jednej subskrypcji w przestrzeni nazw, możesz użyć filtrów *Zaczyna się od* i *Kończy się na* udostępnianych przez usługę Event Grid. W niektórych interfejsach filtry mają nazwy *Prefiks* i *Sufiks*. Jeśli chcesz odbierać zdarzenia z wielu, ale nie wszystkich, kolejek i subskrypcji, możesz utworzyć wiele subskrypcji usługi Event Grid i określić filtr dla każdej z nich.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Tworzenie subskrypcji usługi Event Grid dla przestrzeni nazw usługi Service Bus

Istnieją trzy różne sposoby tworzenia subskrypcji usługi Event Grid dla przestrzeni nazw usługi Service Bus:

* W witrynie Azure Portal
* W [interfejsie wiersza polecenia platformy Azure](#azure-cli-instructions)
* W programie [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instrukcje dotyczące witryny Azure Portal

Aby utworzyć nową subskrypcję usługi Event Grid, wykonaj następujące czynności:
1. W witrynie Azure Portal przejdź do przestrzeni nazw.
2. W okienku po lewej stronie wybierz pozycję **Event Grid**. 
3. Wybierz pozycję **Subskrypcja zdarzeń**.  

   Poniższa ilustracja przedstawia przestrzeń nazw z subskrypcją usługi Event Grid:

   ![Subskrypcje usługi Event Grid](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   Na poniższej ilustracji przedstawiono sposób subskrybowania funkcji lub elementu webhook bez żadnego konkretnego filtru:

   ![21][]

## <a name="azure-cli-instructions"></a>Instrukcje dotyczące interfejsu wiersza polecenia platformy Azure

Najpierw upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. [Pobierz instalatora](/cli/azure/install-azure-cli?view=azure-cli-latest). Wybierz pozycję **Windows + X**, a następnie otwórz nową konsolę programu PowerShell z uprawnieniami administratora. Możesz również użyć powłoki poleceń w witrynie Azure Portal.

Wykonaj następujący kod:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Jeśli używasz BASH 

## <a name="powershell-instructions"></a>Instrukcje dotyczące programu PowerShell

Upewnij się, że masz zainstalowany program Azure PowerShell. [Pobierz instalatora](/powershell/azure/install-Az-ps). Naciśnij klawisze **Windows + X** i otwórz nową konsolę programu PowerShell z uprawnieniami administratora. Możesz również użyć powłoki poleceń w witrynie Azure Portal.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

W tym miejscu możesz eksplorować inne opcje instalacji lub przetestować przepływające zdarzenia.

## <a name="next-steps"></a>Następne kroki

* Pobierz [przykłady](service-bus-to-event-grid-integration-example.md) dla usług Service Bus i Event Grid.
* Dowiedz się więcej o usłudze [Event Grid](../event-grid/index.yml).
* Dowiedz się więcej na temat usługi [Azure Functions](../azure-functions/index.yml).
* Dowiedz się więcej na temat usługi [Logic Apps](../logic-apps/index.yml).
* Dowiedz się więcej na temat usługi [Service Bus](/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[43]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png

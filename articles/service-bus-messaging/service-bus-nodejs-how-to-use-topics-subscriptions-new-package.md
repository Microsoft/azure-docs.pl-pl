---
title: Używanie tematów i subskrypcji platformy Azure/usługi Service Bus z usługą Node.js
description: 'Szybki Start: informacje na temat używania Service Bus tematów i subskrypcji na platformie Azure z poziomu aplikacji Node.js.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 4a1bb3346d50825dcab799477794cb138ac2de91
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326290"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Szybki Start: jak używać tematów Service Bus i subskrypcji z Node.js i pakietem Azure/Service-Bus
W ramach tego samouczka nauczysz się napisać program Node.js do wysyłania komunikatów do Service Bus tematu i odbierania komunikatów z subskrypcji Service Bus przy użyciu nowego [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakietu. Ten pakiet używa szybszego [protokołu AMQP 1,0](service-bus-amqp-overview.md) , podczas gdy starszy pakiet [platformy Azure-SB](https://www.npmjs.com/package/azure-sb) używany [Service Bus interfejsów API czasu wykonywania REST](/rest/api/servicebus/service-bus-runtime-rest). Przykłady są zapisywane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz tematu i subskrypcji do współpracy z programem, wykonaj czynności opisane w [Azure Portal Użyj, aby utworzyć tematy Service Bus i subskrypcje](service-bus-quickstart-topics-subscriptions-portal.md) , aby je utworzyć. Zanotuj parametry połączenia dla wystąpienia Service Bus i nazwy utworzonego tematu i subskrypcji. Będziemy używać tych wartości w przykładach.

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można kopiować i uruchamiać przy użyciu [NodeJS](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/quickstart-nodejs.md)lub [Node.js usługi w chmurze przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakiet nie obsługuje jeszcze tworzenia topcis i subskrypcji. Użyj pakietu, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) Jeśli chcesz programowo je utworzyć.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla Service Bus, Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Korzystanie z tematu Service Bus rozpoczyna się od tworzenia wystąpienia klasy [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) i korzystania z niej w celu utworzenia wystąpienia klasy [TopicClient](/javascript/api/@azure/service-bus/topicclient) . Gdy masz klienta tematu, możesz utworzyć nadawcę i użyć metody [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) do wysyłania wiadomości.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Ten kod wyśle 10 komunikatów do tematu.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
        try {
            for (let i = 0; i < 10; i++) {
              const message= {
                body: `Hello world! ${i}`,
                label: `test`,
                userProperties: {
                    myCustomPropertyName: `my custom property value ${i}`
                }
              };
              console.log(`Sending message: ${message.body}`);
              await sender.send(message);
            }

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę tematu w powyższym kodzie.
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia, aby wykonać ten plik. 

Gratulacje! Właśnie wysłano komunikaty do kolejki Service Bus.

Komunikaty mają pewne właściwości standardowe, takie jak `label` i `messageId` , które można ustawić podczas wysyłania. Jeśli chcesz ustawić właściwości niestandardowe, użyj `userProperties` obiektu, który jest obiektem JSON, który może przechowywać pary klucz-wartość danych niestandardowych.

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma żadnego limitu liczby komunikatów przechowywanych w temacie, ale istnieje limit całkowitego rozmiaru komunikatów przechowywanych w temacie. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Korzystanie z subskrypcji Service Bus rozpoczyna się od utworzenia wystąpienia klasy [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) i użycia jej do utworzenia wystąpienia klasy [SubscriptionClient](/javascript/api/@azure/service-bus/subscriptionclient) . Gdy masz klienta subskrypcji, możesz utworzyć odbiorcę i użyć metody [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) lub [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) do odbierania komunikatów.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `receive.js` i wklej do niego Poniższy kod. Ten kod będzie próbował otrzymywać 10 komunikatów z subskrypcji. Rzeczywista liczba jest zależna od liczby komunikatów w ramach subskrypcji i opóźnienia sieci.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwy tematu i subskrypcji w powyższym kodzie.
4. Następnie uruchom polecenie `node receive.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie odebrano komunikaty z subskrypcji Service Busej.

Metoda [getreceive](/javascript/api/@azure/service-bus/subscriptionclient#createreceiver-receivemode-) przyjmuje w wyniku, `ReceiveMode` który jest wyliczeniem z wartościami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Należy pamiętać o [rozliczeniu komunikatów](message-transfers-locks-settlement.md#settling-receive-operations) , jeśli używasz `PeekLock` trybu przy użyciu dowolnego z `complete()` , `abandon()` , `defer()` lub `deadletter()` metod w komunikacie.

## <a name="subscription-filters-and-actions"></a>Filtry i akcje subskrypcji
Service Bus obsługuje [filtry i akcje dotyczące subskrypcji](topic-filters.md), co umożliwia filtrowanie komunikatów przychodzących do subskrypcji i edytowanie ich właściwości.

Po wystąpieniu programu można `SubscriptionClient` użyć poniższych metod w celu uzyskania, dodania i usunięcia reguł subskrypcji w celu sterowania filtrami i akcjami.

- GetRules
- AddRule
- removeRule

Każda subskrypcja ma regułę domyślną, która używa filtru true, aby zezwalać na wszystkie komunikaty przychodzące. Po dodaniu nowej reguły Pamiętaj, aby usunąć domyślny filtr, aby filtr w nowej regule działał. Jeśli subskrypcja nie ma żadnych reguł, nie otrzyma żadnych komunikatów.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.

- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Wyewidencjonuj inne [przykłady NodeJS dla Service Bus w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)

---
title: Jak używać kolejek platformy Azure/Service-Bus w Node.js
description: Dowiedz się, jak napisać program NodeJS w celu wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus przy użyciu nowego @azure/service-bus pakietu.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: d1d0d2b57ec1ed23082101c9207c4700424dd7cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289756"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Szybki Start: jak używać kolejek Service Bus z Node.js i pakietem Azure/Service-Bus
W ramach tego samouczka nauczysz się pisać program NodeJS w celu wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus przy użyciu nowego [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakietu. Ten pakiet używa szybszego [protokołu AMQP 1,0](service-bus-amqp-overview.md) , podczas gdy starszy pakiet [platformy Azure-SB](https://www.npmjs.com/package/azure-sb) używany [Service Bus interfejsów API czasu wykonywania REST](/rest/api/servicebus/service-bus-runtime-rest). Przykłady są zapisywane w języku JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki. Zanotuj parametry połączenia dla wystąpienia Service Bus i nazwę utworzonej kolejki. Będziemy używać tych wartości w przykładach.

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można kopiować i uruchamiać przy użyciu [NodeJS](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/quickstart-nodejs.md)lub [Node.js usługi w chmurze przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Nowy [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakiet nie obsługuje jeszcze tworzenia kolejek. Użyj pakietu, [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) Jeśli chcesz programowo je utworzyć.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla Service Bus, Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Korzystanie z kolejki Service Bus rozpoczyna się od utworzenia wystąpienia klasy [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) i użycia jej do utworzenia wystąpienia klasy [QueueClient](/javascript/api/@azure/service-bus/queueclient) . Gdy masz klienta kolejki, możesz utworzyć nadawcę i użyć metody [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) lub [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) do wysyłania wiadomości.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Ten kod wyśle 10 komunikatów do kolejki.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
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
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę kolejki w powyższym kodzie.
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie wysłano komunikaty do kolejki Service Bus.

Komunikaty mają pewne właściwości standardowe, takie jak `label` i `messageId` , które można ustawić podczas wysyłania. Jeśli chcesz ustawić właściwości niestandardowe, użyj `userProperties` obiektu, który jest obiektem JSON, który może przechowywać pary klucz-wartość danych niestandardowych.

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nie ma żadnego limitu liczby komunikatów przechowywanych w kolejce, ale istnieje limit całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Korzystanie z kolejki Service Bus rozpoczyna się od utworzenia wystąpienia klasy [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) i użycia jej do utworzenia wystąpienia klasy [QueueClient](/javascript/api/@azure/service-bus/queueclient) . Gdy masz klienta kolejki, możesz utworzyć odbiorcę i użyć metody [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) lub [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) do odbierania komunikatów.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `recieve.js` i wklej do niego Poniższy kod. Ten kod będzie próbował otrzymywać 10 komunikatów z kolejki. Rzeczywista liczba, którą otrzymujesz, zależy od liczby komunikatów w kolejce i opóźnieniu sieci.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę kolejki w powyższym kodzie.
4. Następnie uruchom polecenie `node receiveMessages.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Właśnie odebrano komunikaty z kolejki Service Bus.

Metoda [getreceive](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-) przyjmuje w wyniku, `ReceiveMode` który jest wyliczeniem z wartościami [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) i [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Należy pamiętać o [rozliczeniu komunikatów](message-transfers-locks-settlement.md#settling-receive-operations) , jeśli używasz `PeekLock` trybu przy użyciu dowolnego z `complete()` , `abandon()` , `defer()` lub `deadletter()` metod w komunikacie.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz następujące zasoby.
- [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)
- Wyewidencjonuj inne [przykłady NodeJS dla Service Bus w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](https://azure.microsoft.com/develop/nodejs/)
---
title: Jak używać kolejek usługi Azure/Service-Bus w języku JavaScript
description: Dowiedz się, jak napisać program JavaScript, który używa najnowszej wersji @azure/service-bus pakietu do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 3c499dcb5233cbf5cd4048c641d1b38e289cc35f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739716"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Wysyłanie komunikatów do i odbieranie komunikatów z kolejek Azure Service Bus (JavaScript)
W ramach tego samouczka nauczysz się używać [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakietu w programie JavaScript do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Jeśli nie masz kolejki do współpracy z programem, postępuj zgodnie z instrukcjami w temacie [Use Azure Portal, aby utworzyć](service-bus-quickstart-portal.md) kolejkę Service Bus w celu utworzenia kolejki. Zanotuj **Parametry połączenia** dla przestrzeni nazw Service Bus i nazwę utworzonej **kolejki** .

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można kopiować i uruchamiać przy użyciu [NodeJS](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/quickstart-nodejs.md)lub [Node.js usługi w chmurze przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla Service Bus, Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Poniższy przykładowy kod pokazuje, jak wysłać komunikat do kolejki.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Ten kod wyśle komunikat do kolejki. Komunikat ma etykietę (Naukowc) i treść (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Zamień na `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` Parametry połączenia z przestrzenią nazw Service Bus.
1. Zamień `<QUEUE NAME>` na nazwę kolejki. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node send.js 
    ```
1. Powinny zostać wyświetlone następujące dane wyjściowe.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `receive.js` i wklej do niego następujący kod.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Zamień na `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` Parametry połączenia z przestrzenią nazw Service Bus.
1. Zamień `<QUEUE NAME>` na nazwę kolejki. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node receive.js
    ```
1. Powinny zostać wyświetlone następujące dane wyjściowe.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Na stronie **Omówienie** przestrzeni nazw Service Bus w Azure Portal można zobaczyć liczbę wiadomości **przychodzących** i **wychodzących** . Może być konieczne poczekanie na minutę lub, a następnie odświeżenie strony, aby zobaczyć najnowsze wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba wiadomości przychodzących i wychodzących":::

Wybierz kolejkę na tej stronie **przeglądu** , aby przejść do strony **kolejki Service Bus** . Na tej stronie jest również wyświetlana liczba komunikatów **przychodzących** i **wychodzących** . Widoczne są również inne informacje, takie jak **bieżący rozmiar** kolejki, **Maksymalny rozmiar**, **Liczba aktywnych komunikatów** itd. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Szczegóły kolejki":::
## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami: 

- [Azure Service Bus Biblioteka kliencka dla języka JavaScript](https://www.npmjs.com/package/@azure/service-bus)
- [Przykłady skryptów w języku JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Przykłady języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Dokumentacja referencyjna interfejsu API](/javascript/api/overview/azure/service-bus)
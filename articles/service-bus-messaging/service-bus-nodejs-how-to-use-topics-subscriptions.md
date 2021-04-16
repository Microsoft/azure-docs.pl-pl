---
title: Używanie wersji zapoznawczej języka JavaScript azure/service-bus z tematami i subskrypcjami
description: Dowiedz się, jak napisać program w języku JavaScript, który używa najnowszej wersji zapoznawczej pakietu do wysyłania komunikatów do tematu Service Bus i odbierania komunikatów z subskrypcji @azure/service-bus do tematu.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 59f1bb29099f2b921e687ca9de46365bc34f1b91
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537249"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Szybki start: Service Bus tematach i subskrypcjach przy użyciu usługi Node.js i pakietu azure/service-bus w wersji zapoznawczej
Z tego samouczka dowiesz się, jak używać pakietu w programie JavaScript do wysyłania komunikatów do tematu Service Bus i odbierania komunikatów z subskrypcji Service Bus tego [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) tematu.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Wykonaj kroki opisane w [przewodniku Szybki start: tworzenie](service-bus-quickstart-topics-subscriptions-portal.md)Azure Portal tematu i subskrypcji Service Bus tematu . Zanotuj ciąg połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku Szybki start będziesz używać tylko jednej subskrypcji. 

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można skopiować i uruchomić przy użyciu [oprogramowania Nodejs.](https://nodejs.org/) Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz Create [and deploy a Node.js application to an Azure Website](../app-service/quickstart-nodejs.md), orNode.js Cloud Service using Windows PowerShell (Tworzenie i wdrażanie aplikacji usługi Node.js w witrynie internetowej platformy Azure lub usługaNode.js Cloud Service przy użyciu usługi [Windows PowerShell).](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla programu Service Bus, otwórz wiersz polecenia, który ma w swojej ścieżce, zmień katalog na folder, w którym chcesz mieć przykłady, a następnie `npm` uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Poniższy przykładowy kod przedstawia sposób wysyłania partii komunikatów do Service Bus tematu. Aby uzyskać szczegółowe informacje, zobacz komentarze do kodu. 

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `sendtotopic.js` i wklej do niego poniższy kod. Ten kod spowoduje wysłanie komunikatu do tematu.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Zastąp `<SERVICE BUS NAMESPACE CONNECTION STRING>` ciąg ciągami połączenia z twoją Service Bus nazw.
1. Zastąp `<TOPIC NAME>` nazwą tematu. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node sendtotopic.js 
    ```
1. Powinny zostać wyświetlone następujące dane wyjściowe.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie **receivefromsubscription.js** i wklej do niego następujący kod. Aby uzyskać szczegółowe informacje, zobacz komentarze do kodu. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Zastąp `<SERVICE BUS NAMESPACE CONNECTION STRING>` ciąg ciągami połączenia z przestrzenią nazw . 
1. Zastąp `<TOPIC NAME>` nazwą tematu. 
1. Zastąp `<SUBSCRIPTION NAME>` nazwą subskrypcji tematu. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node receivefromsubscription.js
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

W okienku Azure Portal przejdź do przestrzeni nazw usługi Service Bus, a następnie wybierz temat w dolnym okienku, aby wyświetlić stronę Service Bus **tematu** dla tematu. Na tej stronie powinny być wyświetlane trzy komunikaty przychodzące i trzy wychodzące na **wykresie Komunikaty.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące":::

Jeśli następnym razem uruchamiasz tylko aplikację wysyłania, na stronie **tematu** Service Bus zostanie wyświetlonych sześć komunikatów przychodzących (3 nowe), ale trzy komunikaty wychodzące. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Zaktualizowana strona tematu":::

Na tej stronie, jeśli wybierzesz subskrypcję, zostanie Service Bus **Subskrypcja.** Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę utraconych komunikatów i inne. W tym przykładzie istnieją trzy aktywne komunikaty, które nie zostały jeszcze odebrane przez odbiornik. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą dokumentacją i przykładami: 

- [Azure Service Bus klienta dla języka Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). Folder **javascript zawiera** przykłady języka JavaScript, a **kod typescript** zawiera przykłady języka TypeScript. 
- [Dokumentacja referencyjna usługi azure-servicebus](/javascript/api/overview/azure/service-bus)

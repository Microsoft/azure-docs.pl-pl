---
title: Korzystanie z wersji zapoznawczej JavaScript Azure/Service-Bus z tematami i subskrypcjami
description: Dowiedz się, jak napisać program JavaScript, który używa najnowszej wersji zapoznawczej @azure/service-bus pakietu do wysyłania komunikatów do Service Bus tematu i odbierania komunikatów z subskrypcji do tematu.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: aac3b6339c318c76e9b0c9abd0bc3778f2563a6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498697"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Szybki Start: Tematy Service Bus i subskrypcje z Node.js i pakietem wersji zapoznawczej platformy Azure/usługi Service-Bus
W ramach tego samouczka nauczysz się używać [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakietu w programie JavaScript do wysyłania komunikatów do Service Bus tematu i odbierania komunikatów z subskrypcji Service Bus do tego tematu.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md). Zanotuj parametry połączenia, nazwę tematu i nazwę subskrypcji. W tym przewodniku szybki start będziesz używać tylko jednej subskrypcji. 

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można kopiować i uruchamiać przy użyciu [NodeJS](https://nodejs.org/). Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji Node.js, zobacz [Tworzenie i wdrażanie aplikacji Node.js w witrynie sieci Web platformy Azure](../app-service/quickstart-nodejs.md)lub [Node.js usługi w chmurze przy użyciu programu Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla Service Bus, Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Poniższy przykładowy kod pokazuje, jak wysłać partię komunikatów do tematu Service Bus. Szczegóły można znaleźć w komentarzach do kodu. 

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie `sendtotopic.js` i wklej do niego Poniższy kod. Ten kod wyśle wiadomość do tematu.

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
                    batch = await sender.createBatch();
     
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
3. Zamień na `<SERVICE BUS NAMESPACE CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus.
1. Zamień na `<TOPIC NAME>` nazwę tematu. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node sendtotopic.js 
    ```
1. Powinny zostać wyświetlone następujące dane wyjściowe.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz plik o nazwie **receivefromsubscription.js** i wklej do niego następujący kod. Szczegóły można znaleźć w komentarzach do kodu. 

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
3. Zamień na `<SERVICE BUS NAMESPACE CONNECTION STRING>` Parametry połączenia z przestrzenią nazw. 
1. Zamień na `<TOPIC NAME>` nazwę tematu. 
1. Zamień na `<SUBSCRIPTION NAME>` nazwę subskrypcji tematu. 
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

W Azure Portal przejdź do przestrzeni nazw Service Bus i wybierz temat w dolnym okienku, aby wyświetlić stronę **tematu Service Bus** tematu. Na tej stronie powinny być widoczne trzy przychodzące i trzy komunikaty wychodzące na wykresie **komunikatów** . 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Komunikaty przychodzące i wychodzące":::

Jeśli uruchamiasz jedyną aplikację Wyślij ponownie, na stronie **tematu Service Bus** zobaczysz sześć komunikatów przychodzących (3 nowe), ale trzy komunikaty wychodzące. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Zaktualizowana Strona tematu":::

Na tej stronie, jeśli wybierzesz subskrypcję, uzyskasz dostęp do strony **subskrypcji Service Bus** . Na tej stronie można zobaczyć liczbę aktywnych komunikatów, liczbę wiadomości utraconych i więcej. W tym przykładzie istnieją trzy aktywne komunikaty, które nie zostały jeszcze odebrane przez odbiornik. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Liczba aktywnych komunikatów":::

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższą dokumentacją i przykładami: 

- [Azure Service Bus Biblioteka kliencka dla języka Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). Folder **JavaScript** zawiera przykłady JavaScript, a język **TypeScript** zawiera próbki TypeScript. 
- [Dokumentacja referencyjna platformy Azure-ServiceBus](/javascript/api/overview/azure/service-bus)
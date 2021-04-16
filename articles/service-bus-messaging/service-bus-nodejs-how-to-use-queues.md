---
title: Jak używać kolejek azure/service-bus w języku JavaScript
description: Dowiedz się, jak napisać program w języku JavaScript, który używa najnowszej wersji pakietu do wysyłania komunikatów do kolejki Service Bus @azure/service-bus odbierania komunikatów.
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537240"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Wysyłanie i odbieranie komunikatów z Azure Service Bus (JavaScript)
Z tego samouczka dowiesz się, jak używać pakietu w programie JavaScript do wysyłania komunikatów do kolejki Service Bus [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) komunikatów.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści [dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Jeśli nie masz kolejki do pracy, wykonaj kroki opisane w artykule Use [Azure Portal to create a Service Bus queue to](service-bus-quickstart-portal.md) create a queue (Tworzenie kolejki przy użyciu usługi Azure Portal do tworzenia kolejki). Zanotuj **ciąg** połączenia dla Service Bus nazw i nazwę **utworzonej** kolejki.

> [!NOTE]
> - Ten samouczek współpracuje z przykładami, które można skopiować i uruchomić przy użyciu [oprogramowania Nodejs.](https://nodejs.org/) Aby uzyskać instrukcje dotyczące tworzenia aplikacji Node.js, zobacz Create [and deploy a Node.js application to an Azure Website](../app-service/quickstart-nodejs.md), orNode.js cloud service using Windows PowerShell (Tworzenie i wdrażanie aplikacji usługi Node.js w witrynie sieci Web platformy Azure lub usługaNode.js w chmurze przy użyciu usługi [ Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)
Aby zainstalować pakiet npm dla programu Service Bus, otwórz wiersz polecenia, który ma w swojej ścieżce, zmień katalog na folder, w którym chcesz mieć przykłady, a następnie `npm` uruchom to polecenie.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Poniższy przykładowy kod pokazuje, jak wysłać komunikat do kolejki.

1. Otwórz ulubiony edytor, taki [jak Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz plik o nazwie `send.js` i wklej do niego poniższy kod. Ten kod spowoduje wysłanie komunikatu do kolejki. Komunikat ma etykietę (Analityk) i treść (Przemysł).

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
3. Zastąp `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` ciąg ciągami połączenia z twoją Service Bus nazw.
1. Zastąp `<QUEUE NAME>` nazwą kolejki. 
1. Następnie uruchom polecenie w wierszu polecenia, aby wykonać ten plik.

    ```console
    node send.js 
    ```
1. Powinny zostać wyświetlone następujące dane wyjściowe.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com/)
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
3. Zastąp `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` ciąg ciągami połączenia z Service Bus nazw.
1. Zastąp `<QUEUE NAME>` nazwą kolejki. 
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

Na stronie **Przegląd** dla Service Bus nazw w Azure Portal można zobaczyć **liczbę** komunikatów przychodzących **i** wychodzących. Może być konieczne odczekenie około minuty, a następnie odświeżenie strony w celu zobaczenia najnowszych wartości. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Liczba komunikatów przychodzących i wychodzących":::

Wybierz kolejkę na tej **stronie Przegląd,** aby przejść do **Service Bus kolejki.** Na tej stronie **jest** **również** wyświetlany licznik komunikatów przychodzących i wychodzących. Zobaczysz również inne informacje, takie jak bieżący **rozmiar** **kolejki,** maksymalny rozmiar, liczba aktywnych **komunikatów** i tak dalej. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Szczegóły kolejki":::
## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującą dokumentacją i przykładami: 

- [Azure Service Bus klienta dla języka JavaScript](https://www.npmjs.com/package/@azure/service-bus)
- [Przykłady skryptów w języku JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Przykłady języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Dokumentacja referencyjna interfejsu API](/javascript/api/overview/azure/service-bus)

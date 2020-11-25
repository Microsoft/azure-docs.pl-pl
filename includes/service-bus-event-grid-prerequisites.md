---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 163df5e639f4babb0d4302819f9f5b9538a6708c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999085"
---
## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz [subskrypcji platformy Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus
Postępuj zgodnie z instrukcjami w tym samouczku: [Szybki Start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) , aby wykonać następujące zadania:

- Utwórz przestrzeń nazw Service Bus w **warstwie Premium** . 
- Pobierz parametry połączenia. 
- Utwórz temat Service Bus.
- Utwórz subskrypcję w temacie. W tym samouczku potrzebna jest tylko jedna subskrypcja, więc nie trzeba tworzyć subskrypcji S2 i S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Wysyłanie komunikatów do tematu Service Bus
W tym kroku użyjesz przykładowej aplikacji do wysyłania komunikatów do Service Bus tematu utworzonego w poprzednim kroku. 

1. Sklonuj [repozytorium GitHub Azure-Service-Bus](https://github.com/Azure/azure-service-bus/).
2. W programie Visual Studio przejdź do folderu *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* , a następnie otwórz plik *SBEventGridIntegration. sln* .
3. W oknie Eksplorator rozwiązań rozwiń projekt **MessageSender** i wybierz pozycję **program.cs**.
4. Zamień na `<SERVICE BUS NAMESPACE - CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus i `<TOPIC NAME>` nazwą tematu. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Skompiluj i uruchom program, aby wysłać 5 wiadomości testowych ( `const int numberOfMessages = 5;` ) do tematu Service Bus. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Dane wyjściowe aplikacji konsoli":::
    
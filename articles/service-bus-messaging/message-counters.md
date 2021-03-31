---
title: Azure Service Bus — liczba komunikatów
description: Pobierz liczbę wiadomości przechowywanych w kolejkach i subskrypcjach za pomocą Azure Resource Manager oraz interfejsów API Azure Service Bus NamespaceManager.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341266"
---
# <a name="message-counters"></a>Liczniki komunikatów

Liczbę wiadomości przechowywanych w kolejkach i subskrypcjach można pobrać przy użyciu Azure Resource Manager oraz interfejsów API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) w .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W programie PowerShell można uzyskać licznik w następujący sposób:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Szczegóły liczby komunikatów

Znajomość liczby aktywnych komunikatów jest przydatna w ustaleniu, czy kolejka kompiluje zaległości, które wymagają więcej zasobów do przetworzenia niż aktualnie wdrożone. Następujące szczegóły licznika są dostępne w klasie [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) :

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): komunikaty w kolejce lub subskrypcji, które znajdują się w stanie aktywnym i gotowe do dostarczenia.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): komunikaty w kolejce utraconych wiadomości.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): komunikaty w stanie zaplanowanym.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): komunikaty, które nie zostały przeniesione do innej kolejki lub tematu i zostały przeniesione do kolejki utraconych wiadomości.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): komunikaty oczekujące na przeniesienie do innej kolejki lub tematu.

Jeśli aplikacja chce skalować zasoby na podstawie długości kolejki, należy to zrobić z mierzonym tempem. Pozyskiwanie liczników komunikatów jest kosztowną operacją wewnątrz brokera komunikatów i wykonuje ją często bezpośrednio i niekorzystnie wpływa na wydajność jednostki.

> [!NOTE]
> Komunikaty wysyłane do tematu Service Bus są przekazywane do subskrypcji dla tego tematu. W związku z tym liczba aktywnych komunikatów w samym temacie to 0, ponieważ te komunikaty zostały pomyślnie przekazane do subskrypcji. Pobierz liczbę komunikatów w subskrypcji i sprawdź, czy jest ona większa od 0. Mimo że wyświetlane są komunikaty w ramach subskrypcji, są one przechowywane w magazynie należącym do tematu. 

Jeśli przeszukiwane są subskrypcje, mogą one mieć różną od zera liczbę wiadomości (co powoduje dodanie do 323MB miejsca dla całej jednostki).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652613"
---
W poniższej tabeli wymieniono funkcje usługi wiadomości Java (JMS), które obecnie są obsługiwane przez Azure Service Bus. Przedstawiono w nim również funkcje, które nie są obsługiwane.


| Cecha | Interfejs API |Stan |
|---|---|---|
| Kolejki   | <ul> <li> JMSContext. isqueue (String QueueName) </li> </ul>| **Obsługiwane** |
| Tematy   | <ul> <li> JMSContext... </li> </ul>| **Obsługiwane** |
| Kolejki tymczasowe |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Obsługiwane** |
| Tematy tymczasowe |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Obsługiwane** |
| Producent komunikatu/<br/> JMSProducer |<ul> <li> JMSContext. () </li> </ul>| **Obsługiwane** |
| Przeglądarki kolejek |<ul> <li> JMSContext. IsBrowser (Kolejka kolejki) </li> <li> JMSContext. IsBrowser (Kolejka kolejki, ciąg messageSelector) </li> </ul> | **Obsługiwane** |
| Odbiorca komunikatu/ <br/> JMSConsumer | <ul> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa) </li> <li> JMSContext. isconsumer (miejsce docelowe, ciąg messageSelector) </li> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa, ciąg messageSelector, wartość logiczna nolocal)</li> </ul>  <br/> nolocal nie jest obecnie obsługiwane | **Obsługiwane** |
| Udostępnione trwałe subskrypcje | <ul> <li> JMSContext. createSharedDurableConsumer (temat tematu, nazwa ciągu) </li> <li> JMSContext. createSharedDurableConsumer (temat tematu, nazwa ciągu, ciąg messageSelector) </li> </ul>| **Obsługiwane**|
| Nieudostępniane trwałe subskrypcje | <ul> <li> JMSContext. createDurableConsumer (temat tematu, nazwa ciągu) </li> <li> createDurableConsumer (temat tematu, nazwa ciągu, ciąg messageSelector, wartość logiczna nolocal) </li> </ul> <br/> parametr nolocal nie jest obecnie obsługiwany i powinien być ustawiony na wartość false. | **Obsługiwane** |
| Udostępnione nietrwałe subskrypcje |<ul> <li> JMSContext. createSharedConsumer (temat tematu, ciąg sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (temat tematu, ciąg sharedSubscriptionName, ciąg messageSelector) </li> </ul> | **Obsługiwane** |
| Nieudostępniane subskrypcje nietrwałe |<ul> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa) </li> <li> JMSContext. isconsumer (miejsce docelowe, ciąg messageSelector) </li> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa, ciąg messageSelector, wartość logiczna nolocal) </li> </ul> <br/> parametr nolocal nie jest obecnie obsługiwany i powinien być ustawiony na wartość false. | **Obsługiwane** |
| Selektory komunikatów | zależy od utworzonego odbiorcy | **Obsługiwane** |
| Opóźnienie dostarczania (zaplanowane wiadomości) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Obsługiwane**|
| Utworzono komunikat |<ul> <li> JMSContext. ismessage () </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (obiekt możliwy do serializacji) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (tekst ciągu) </li> </ul>| **Obsługiwane** |
| Transakcje między jednostkami |<ul> <li> Connection. issession (true, Session.SESSION_TRANSACTED) </li> </ul> | **Obsługiwane** |
| Transakcje rozproszone || Nieobsługiwane |

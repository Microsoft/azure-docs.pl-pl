---
title: plik dołączany
description: plik dołączany
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798141"
---
W poniższej tabeli wymieniono funkcje usługi wiadomości Java (JMS), które obecnie są obsługiwane przez Azure Service Bus. Przedstawiono w nim również funkcje, które nie są obsługiwane.


| Cecha | Interfejs API |Stan |
|---|---|---|
| Kolejki   | <ul> <li> JMSContext. isqueue (String QueueName) </li> </ul>| **Obsługiwał** |
| Tematy   | <ul> <li> JMSContext... </li> </ul>| **Obsługiwał** |
| Kolejki tymczasowe |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Obsługiwał** |
| Tematy tymczasowe |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Obsługiwał** |
| Producent komunikatu/<br/> JMSProducer |<ul> <li> JMSContext. () </li> </ul>| **Obsługiwał** |
| Przeglądarki kolejek |<ul> <li> JMSContext. IsBrowser (Kolejka kolejki) </li> <li> JMSContext. IsBrowser (Kolejka kolejki, ciąg messageSelector) </li> </ul> | **Obsługiwał** |
| Odbiorca komunikatu/ <br/> JMSConsumer | <ul> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa) </li> <li> JMSContext. isconsumer (miejsce docelowe, ciąg messageSelector) </li> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa, ciąg messageSelector, wartość logiczna nolocal)</li> </ul>  <br/> nolocal nie jest obecnie obsługiwane | **Obsługiwał** |
| Udostępnione trwałe subskrypcje | <ul> <li> JMSContext. createSharedDurableConsumer (temat tematu, nazwa ciągu) </li> <li> JMSContext. createSharedDurableConsumer (temat tematu, nazwa ciągu, ciąg messageSelector) </li> </ul>| **Obsługiwał**|
| Nieudostępniane trwałe subskrypcje | <ul> <li> JMSContext. createDurableConsumer (temat tematu, nazwa ciągu) </li> <li> createDurableConsumer (temat tematu, nazwa ciągu, ciąg messageSelector, wartość logiczna nolocal) </li> </ul> <br/> parametr nolocal nie jest obecnie obsługiwany i powinien być ustawiony na wartość false. | **Obsługiwał** |
| Udostępnione nietrwałe subskrypcje |<ul> <li> JMSContext. createSharedConsumer (temat tematu, ciąg sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (temat tematu, ciąg sharedSubscriptionName, ciąg messageSelector) </li> </ul> | **Obsługiwał** |
| Nieudostępniane subskrypcje nietrwałe |<ul> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa) </li> <li> JMSContext. isconsumer (miejsce docelowe, ciąg messageSelector) </li> <li> JMSContext. isconsumer (docelowa lokalizacja docelowa, ciąg messageSelector, wartość logiczna nolocal) </li> </ul> <br/> parametr nolocal nie jest obecnie obsługiwany i powinien być ustawiony na wartość false. | **Obsługiwał** |
| Selektory komunikatów | zależy od utworzonego odbiorcy | **Obsługiwał** |
| Opóźnienie dostarczania (zaplanowane wiadomości) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Obsługiwał**|
| Utworzono komunikat |<ul> <li> JMSContext. ismessage () </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (obiekt możliwy do serializacji) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (tekst ciągu) </li> </ul>| **Obsługiwał** |
| Transakcje rozproszone || Nieobsługiwane |

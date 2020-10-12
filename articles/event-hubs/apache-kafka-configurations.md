---
title: Zalecane konfiguracje dla klientów Apache Kafka — Azure Event Hubs
description: Ten artykuł zawiera zalecane konfiguracje Apache Kafka dla klientów korzystających z usługi Azure Event Hubs na potrzeby Apache Kafka.
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097670"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Zalecane konfiguracje dla klientów Apache Kafka
Poniżej przedstawiono zalecane konfiguracje korzystania z platformy Azure Event Hubs z aplikacji klienckich Apache Kafka. 

## <a name="java-client-configuration-properties"></a>Właściwości konfiguracji klienta Java

### <a name="producer-and-consumer-configurations"></a>Konfiguracje producentów i klientów

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (w przybliżeniu) | < 240000 | Można je obniżyć, aby szybciej odbierać zmiany metadanych.
`connections.max.idle.ms`   | 180000 | < 240000 | Platforma Azure zamyka przychodzące dane protokołu TCP bezczynne > 240 000 MS, co może spowodować wysyłanie martwych połączeń (pokazanych jako wygasłe partie z powodu przekroczenia limitu czasu wysyłania).

### <a name="producer-configurations-only"></a>Tylko konfiguracje producentów
Konfiguracje producentów można znaleźć [tutaj](https://kafka.apache.org/documentation/#producerconfigs).

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Usługa będzie zamykać połączenia, jeśli są wysyłane żądania większe niż 1 046 528 bajtów.  *Ta wartość **musi** być zmieniona i spowoduje problemy ze scenariuszami tworzenia dużej przepływności.*
`retries` | > 0 | | Może wymagać zwiększenia wartości delivery.timeout.ms, zobacz dokumentację.
`request.timeout.ms` | 30000.. 60000 | > 20000| Wartość EH będzie domyślnie równa co najmniej 20 000 MS.  *Chociaż żądania o niższych wartościach limitu czasu są akceptowane, zachowanie klienta nie jest gwarantowane.*
`metadata.max.idle.ms` | 180000 | > 5000 | Określa, jak długo producent buforuje metadane dla tematu, który jest bezczynny. Jeśli czas, który upłynął od ostatniego wygenerowania tematu przekracza czas bezczynności metadanych, metadane tematu są zapomniane i następnym dostępem do niego będzie wymuszenie żądania pobrania metadanych.
`linger.ms` | > 0 | | W przypadku scenariuszy o wysokiej przepływności wartość parametru pokutujący powinna być równa najwyższej wartości dopuszczalnej, aby można było korzystać z usługi Batch.
`delivery.timeout.ms` | | | Ustaw zgodnie z formułą ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`enable.idempotence` | fałsz | | Idempotentności nie jest obecnie obsługiwana.
`compression.type` | `none` | | Kompresja nie jest obecnie obsługiwana...

### <a name="consumer-configurations-only"></a>Tylko konfiguracje klientów
Konfiguracje odbiorców można znaleźć [tutaj](https://kafka.apache.org/documentation/#consumerconfigs).

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 jest wartością domyślną i nie powinna być zmieniana.
`session.timeout.ms` | 30000 |6000.. 300000| Zacznij od 30000, zwiększ, jeśli widzisz częste ponowne równoważenie z powodu brakujących pulsów.


## <a name="librdkafka-configuration-properties"></a>Właściwości konfiguracji librdkafka
Główny `librdkafka` plik konfiguracji ([link](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) zawiera rozszerzone opisy właściwości poniżej.

### <a name="producer-and-consumer-configurations"></a>Konfiguracje producentów i klientów

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|-----:|---
`socket.keepalive.enable` | true | | Konieczne, jeśli oczekiwano, że połączenie jest aktywne.  Platforma Azure zamknie bezczynne przychodzące TCP > 240 000 MS.
`metadata.max.age.ms` | ~ 180000| < 240000 | Można je obniżyć, aby szybciej odbierać zmiany metadanych.

### <a name="producer-configurations-only"></a>Tylko konfiguracje producentów

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|-----:|---
`retries` | > 0 | | Wartość domyślna to 2. Zalecamy zachowanie tej wartości. 
`request.timeout.ms` | 30000.. 60000 | > 20000| Wartość EH będzie domyślnie równa co najmniej 20 000 MS.  `librdkafka` wartość domyślna to 5000, co może być problematyczne. *Chociaż żądania o niższych wartościach limitu czasu są akceptowane, zachowanie klienta nie jest gwarantowane.*
`partitioner` | `consistent_random` | Zobacz dokumentację librdkafka | `consistent_random` jest ustawieniem domyślnym i najlepszym.  Klucze puste i puste są obsługiwane w większości przypadków.
`enable.idempotence` | fałsz | | Idempotentności nie jest obecnie obsługiwana.
`compression.codec` | `none` || Kompresja nie jest obecnie obsługiwana.

### <a name="consumer-configurations-only"></a>Tylko konfiguracje klientów

Właściwość | Zalecane wartości | Dozwolony zakres | Uwagi
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 jest wartością domyślną i nie powinna być zmieniana.
`session.timeout.ms` | 30000 |6000.. 300000| Zacznij od 30000, zwiększ, jeśli widzisz częste ponowne równoważenie z powodu brakujących pulsów.


## <a name="further-notes"></a>Dalsze notatki

Zapoznaj się z poniższą tabelą typowych scenariuszy błędów związanych z konfiguracją.

Objawy | Problem | Rozwiązanie
----|---|-----
Przesunięte błędy zatwierdzania z powodu ponownego zrównoważenia | Odbiorca czeka zbyt długo między wywołaniami sondowania (), a Usługa uruchamia odbiorcę z grupy. | Istnieje kilka rozwiązań: <ul><li>Zwiększ limit czasu sesji</li><li>zmniejszenie rozmiaru partii komunikatów w celu przyspieszenia przetwarzania</li><li>Popraw przetwarzanie równoległe przetwarzania, aby uniknąć zablokowania konsumenta. sonda ()</li></ul> Zastosowanie jakiejś kombinacji trzech jest najprawdopodobniej.
Wyjątki sieciowe o wysokiej przepływności tworzenia | Czy używasz klienta Java + domyślny maksymalny rozmiar żądania.  Twoje żądania mogą być zbyt duże. | Zobacz konfiguracje języka Java powyżej.

## <a name="next-steps"></a>Następne kroki
Zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](..//azure-resource-manager/management/azure-subscription-service-limits.md) dotyczące limitów przydziału i limitów dla wszystkich usług platformy Azure. 

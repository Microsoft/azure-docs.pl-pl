---
title: Rozwiązywanie problemów z usługą Azure Event Hubs for Apache Kafka
description: W tym artykule pokazano, jak rozwiązywać problemy z usługą Azure Event Hubs for Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606732"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Apache Kafka przewodnik rozwiązywania problemów dla Centrum zdarzeń
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które mogą wystąpić podczas korzystania z Centrum zdarzeń dla apache platformy Kafka. 

## <a name="server-busy-exception"></a>Wyjątek zajęty serwerem
Możesz otrzymać wyjątek zajęty serwerem z powodu ograniczania kafki. W przypadku klientów USŁUGI AMQP centra zdarzeń natychmiast zwracają wyjątek **zajęty serwerem** po ograniczeniu przepustowości usługi. Jest to równoważne z komunikatem "spróbuj ponownie później". W kafce wiadomości są opóźnione przed zakończeniem. Długość opóźnienia jest zwracana w milisekundach, jak `throttle_time_ms` w odpowiedzi produce/fetch. W większości przypadków te opóźnione żądania nie są rejestrowane jako wyjątki ServerBusy na pulpitach nawigacyjnych centrum zdarzeń. Zamiast tego `throttle_time_ms` wartość odpowiedzi powinna być używana jako wskaźnik, że przepływność przekroczyła aprowizowanym przydziałem.

Jeśli ruch jest nadmierny, usługa ma następujące zachowanie:

- Jeśli opóźnienie żądania produkcji przekracza limit czasu żądania, Usługi Event Hubs zwraca kod błędu **naruszenia zasad.**
- Jeśli opóźnienie żądania pobrania przekracza limit czasu żądania, Usługi Event Hubs rejestruje żądanie jako ograniczone i odpowiada pustym zestawem rekordów i bez kodu błędu.

[Dedykowane klastry](event-hubs-dedicated-overview.md) nie mają mechanizmów ograniczania przepustowości. Możesz swobodnie korzystać ze wszystkich zasobów klastra.

## <a name="no-records-received"></a>Nie otrzymano żadnych rekordów
Możesz zobaczyć konsumentów nie uzyskanie żadnych rekordów i stale równoważenia. W tym scenariuszu konsumenci nie otrzymują żadnych rekordów i stale równoważą się. Nie ma wyjątku lub błędu, gdy to się stanie, ale dzienniki platformy Kafka pokaże, że konsumenci są zablokowane próbuje ponownie dołączyć do grupy i przypisać partycje. Istnieje kilka możliwych przyczyn:

- Upewnij się, `request.timeout.ms` że jest to co najmniej zalecana wartość `session.timeout.ms` 60000, a twoja jest co najmniej zalecana wartość 30000. Zbyt niskie ustawienia mogą powodować limity czasu dla konsumentów, które następnie powodują ponowne zrównoważenie (które następnie powodują więcej limitów czasu, co powoduje większe równoważenie i tak dalej) 
- Jeśli konfiguracja jest zgodna z zalecanymi wartościami i nadal widzisz ciągłe równoważenie, możesz otworzyć problem (upewnij się, że uwzględnisz całą konfigurację w problemie, abyśmy mogli pomóc w debugowaniu)!

## <a name="compressionmessage-format-version-issue"></a>Problem z wersją formatu kompresji/wiadomości
Platforma Kafka obsługuje kompresję, a centra zdarzeń dla platformy Kafka obecnie tego nie robią. Błędy, które wspominają o wersji `The message format version on the broker does not support the request.`formatu wiadomości (na przykład) są spowodowane, gdy klient próbuje wysłać skompresowane wiadomości platformy Kafka do naszych brokerów.

Jeśli skompresowane dane są konieczne, kompresja danych przed wysłaniem ich do brokerów i dekompresja po otrzymaniu jest prawidłowym obejściem. Treść wiadomości jest tylko tablicą bajtów do usługi, więc kompresja/dekompresja po stronie klienta nie spowoduje żadnych problemów.

## <a name="unknownserverexception"></a>Nieznanaexception
Możesz otrzymać UnknownServerException z bibliotek klienta platformy Kafka, podobnie jak w poniższym przykładzie: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Otwórz bilet z pomocą techniczną firmy Microsoft.  Rejestrowanie na poziomie debugowania i sygnatury czasowe wyjątków w czasie UTC są pomocne w debugowaniu problemu. 

## <a name="other-issues"></a>Inne problemy
Sprawdź następujące elementy, jeśli widzisz problemy podczas korzystania z platformy Kafka w centrach zdarzeń.

- **Zapora blokująca ruch** — upewnij się, że port **9093** nie jest zablokowany przez zaporę.
- **TopicAuthorizationException** — najczęstszymi przyczynami tego wyjątku są:
    - Literówka w ciągu połączenia w pliku konfiguracyjnym lub
    - Próba użycia centrów zdarzeń dla platformy Kafka w obszarze nazw warstwy Podstawowa. Centra zdarzeń dla platformy Kafka [są obsługiwane tylko dla obszarów nazw warstwy Standardowa i Dedykowana](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Niezgodność wersji platformy Kafka** — centra zdarzeń dla ekosystemów platformy Kafka obsługuje wersje platformy Kafka 1.0 i nowsze. Niektóre aplikacje korzystające z platformy Kafka w wersji 0.10 i nowszej mogą czasami działać ze względu na wsteczną zgodność protokołu Kafka, ale zdecydowanie zaleca się używanie starych wersji interfejsu API. Protokół Kafka w wersji 0.9 lub wcześniejszej nie obsługuje wymaganych protokołów SASL i nie może połączyć się z centrum zdarzeń.
- **Dziwne kodowania w nagłówkach AMQP podczas korzystania z platformy Kafka** — podczas wysyłania zdarzeń do centrum zdarzeń za pomocą usługi AMQP wszystkie nagłówki ładunku AMQP są serializowane w kodowaniu AMQP. Konsumenci platformy Kafka nie deserializacji nagłówków z usługi AMQP. Aby odczytać wartości nagłówka, ręcznie zdekodować nagłówki AMQP. Alternatywnie można uniknąć używania nagłówków AMQP, jeśli wiesz, że będziesz używać za pośrednictwem protokołu Kafka. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Uwierzytelnianie SASL** — uzyskanie struktury do współpracy z protokołem uwierzytelniania SASL wymaganym przez usługi Event Hubs może być trudniejsze niż na pierwszy rzut oka. Sprawdź, czy można rozwiązać problem konfiguracji przy użyciu zasobów struktury na uwierzytelnianie SASL. 

## <a name="limits"></a>Limity
Apache Kafka vs. Event Hubs Kafka. W przeważającej części Centra zdarzeń dla ekosystemów platformy Kafka ma takie same wartości domyślne, właściwości, kody błędów i ogólne zachowanie, które wykonuje apache kafka. Wystąpienia, w których te dwa jawnie różnią się (lub gdy Centra zdarzeń nakłada limit, którego nie ma kafka) są wymienione poniżej:

- Maksymalna długość `group.id` obiektu wynosi 256 znaków
- Maksymalny rozmiar `offset.metadata.max.bytes` to 1024 bajtów
- Zatwierdzenia offsetowe są ograniczane przy 4 połączeniach/sekundę na partycję z maksymalnym rozmiarem dziennika wewnętrznego 1 MB


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o centrach zdarzeń i centrach zdarzeń dla platformy Kafka, zobacz następujące artykuły:  

- [Apache Kafka — przewodnik dla centrów zdarzeń](apache-kafka-developer-guide.md)
- [Apache Kafka przewodnik migracji dla Centrów zdarzeń](apache-kafka-migration-guide.md)
- [Często zadawane pytania - Event Hubs for Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Zalecane konfiguracje](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

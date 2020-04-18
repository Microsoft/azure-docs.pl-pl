---
title: Często zadawane pytania — Usługi Azure Event Hubs dla platformy Apache Kafka
description: W tym artykule pokazano, jak konsumenci i producenci korzystający z różnych protokołów (AMQP, Apache Kafka i HTTPS) mogą wymieniać zdarzenia podczas korzystania z usługi Azure Event Hubs.
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606745"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Często zadawane pytania - Event Hubs for Apache Kafka 
Ten artykuł zawiera odpowiedzi na niektóre z często zadawanych pytań dotyczących migracji do centrów zdarzeń dla platformy Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Czy prowadzisz Apache Kafka?

Nie.  Wykonujemy operacje interfejsu API platformy Kafka względem infrastruktury centrum zdarzeń.  Ponieważ istnieje ścisła korelacja między Apache Kafka i Event Hubs funkcji AMQP (czyli produkcji, odbioru, zarządzania, tak dalej), jesteśmy w stanie przenieść znaną niezawodność Centrum zdarzeń do przestrzeni Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupa odbiorców Event Hubs a grupa odbiorców platformy Kafka
Jaka jest różnica między grupą odbiorców Centrum zdarzeń a grupą odbiorców platformy Kafka w centrach zdarzeń? Grupy konsumentów platformy Kafka w centrach zdarzeń są w pełni różne od standardowych grup odbiorców Centrum zdarzeń.

**Grupy odbiorców usługi Event Hubs**

- Są one zarządzane za pomocą operacji tworzenia, pobierania, aktualizacji i usuwania (CRUD) za pośrednictwem szablonów portalu, SDK lub Usługi Azure Resource Manager. Grup konsumentów centrum zdarzeń nie można automatycznie utworzyć.
- Są to jednostki podrzędne centrum zdarzeń. Oznacza to, że ta sama nazwa grupy odbiorców może być ponownie używana między centrami zdarzeń w tym samym obszarze nazw, ponieważ są one oddzielne jednostki.
- Nie są one używane do przechowywania przesunięć. Zaaranżowane zużycie amqp odbywa się przy użyciu zewnętrznego magazynu offsetowego, na przykład usługi Azure Storage.

**Grupy konsumentów platformy Kafka**

- Są one automatycznietworzone.  Grupy platformy Kafka można zarządzać za pośrednictwem interfejsów API grupy odbiorców platformy Kafka.
- Mogą przechowywać przesunięcia w usłudze Usługi Event Hubs.
- Są one używane jako klucze w tym, co jest skutecznie magazynu klucza przesunięcia wartości. Dla unikatowej `group.id` pary `topic-partition`i , przechowujemy przesunięcie w usłudze Azure Storage (replikacja 3x). Użytkownicy centrów zdarzeń nie ponoszą dodatkowych kosztów magazynowania z powodu przechowywania przesunięć platformy Kafka. Przesunięcia są manipulable za pośrednictwem interfejsów API grupy konsumentów platformy Kafka, ale *konta* magazynu przeciwstawnego nie są bezpośrednio widoczne lub manipulable dla użytkowników Centrum zdarzeń.  
- Obejmują one obszar nazw. Przy użyciu tej samej nazwy grupy platformy Kafka dla wielu aplikacji na wiele tematów oznacza, że wszystkie aplikacje i ich klientów platformy Kafka zostaną zrównoważone, gdy tylko jedna aplikacja wymaga równoważenia.  Mądrze wybieraj nazwy grup.
- W pełni różnią się od grup konsumentów usługi Event Hubs. **Nie** musisz używać "$Default", ani nie musisz się martwić o klientów platformy Kafka zakłócających obciążenia AMQP.
- Nie są one widoczne w witrynie Azure portal. Informacje o grupie konsumentów są dostępne za pośrednictwem interfejsów API platformy Kafka.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o centrach zdarzeń i centrach zdarzeń dla platformy Kafka, zobacz następujące artykuły:  

- [Apache Kafka — przewodnik dla centrów zdarzeń](apache-kafka-developer-guide.md)
- [Apache Kafka przewodnik migracji dla Centrów zdarzeń](apache-kafka-migration-guide.md)
- [Apache Kafka przewodnik rozwiązywania problemów dla Centrum zdarzeń](apache-kafka-troubleshooting-guide.md)
- [Zalecane konfiguracje](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)


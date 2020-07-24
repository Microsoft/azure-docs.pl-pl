---
title: Często zadawane pytania — Event Hubs platformy Azure dla Apache Kafka
description: W tym artykule pokazano, jak konsumenci i producenci korzystający z różnych protokołów (AMQP, Apache Kafka i HTTPS) mogą wymieniać zdarzenia podczas korzystania z usługi Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8bdd86d9f299a69d5f2d05bb8ec526ed94780608
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031687"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Często zadawane pytania — Event Hubs dla Apache Kafka 
Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące migracji do Event Hubs Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Czy uruchamiasz Apache Kafka?

Nie.  Wykonujemy operacje interfejsu API Kafka w odniesieniu do infrastruktury Event Hubs.  Ponieważ istnieje ścisła korelacja między Apache Kafka i Event Hubs funkcjonalnością AMQP (to jest, produkuje, odbierają, zarządzamy itd.), możemy zapewnić znaną niezawodność Event Hubs do Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs Grupa konsumentów a grupa odbiorców Kafka
Jaka jest różnica między grupą użytkowników centrum zdarzeń i grupą konsumentów Kafka na Event Hubs? Grupy konsumentów Kafka na Event Hubs są w pełni odmienne od standardowych grup konsumenckich Event Hubs.

**Event Hubs grupy konsumentów**

- Są one zarządzane przy użyciu operacji tworzenia, pobierania, aktualizowania i usuwania (CRUD) za pomocą szablonów portalu, zestawu SDK lub Azure Resource Manager. Nie można utworzyć AutoCreate Event Hubs grupy konsumentów.
- Są one jednostkami podrzędnymi centrum zdarzeń. Oznacza to, że ta sama nazwa grupy odbiorców może być ponownie używana między centrami zdarzeń w tej samej przestrzeni nazw, ponieważ są one osobnymi jednostkami.
- Nie są one używane do przechowywania przesunięć. Zorganizowane użycie AMQP odbywa się przy użyciu zewnętrznego magazynu przesunięcia, na przykład usługi Azure Storage.

**Grupy konsumentów Kafka**

- Są one tworzone przez Autotworzenie.  Grupami Kafka można zarządzać za pośrednictwem interfejsów API grup konsumenckich Kafka.
- Mogą przechowywać przesunięcia w usłudze Event Hubs.
- Są one używane jako klucze, co jest efektywnie przesuniętym magazynem wartości. W przypadku unikatowej pary `group.id` i `topic-partition` przechowujemy przesunięcie w usłudze Azure Storage (replikacja 3). Event Hubs użytkownicy nie będą ponosić dodatkowych kosztów związanych z przechowywaniem przesunięć Kafka. Przesunięcia są manipulable za pośrednictwem interfejsów API grup konsumenckich Kafka, ale przesunięte *konta* magazynu nie są bezpośrednio widoczne lub manipulable dla użytkowników centrum zdarzeń.  
- Obejmują one przestrzeń nazw. Użycie tej samej nazwy grupy Kafka dla wielu aplikacji w wielu tematach oznacza, że wszystkie aplikacje i ich klienci Kafka będą ponownie zrównoważyć, gdy tylko jedna aplikacja będzie wymagała ponownego zrównoważenia.  Wybierz swoje nazwy grup.
- Są one w pełni odmienne od Event Hubs grup odbiorców. **Nie** musisz używać "$default" ani nie musisz martwić się o Kafka klientów zakłócających obciążenia AMQP.
- Nie są one widoczne w Azure Portal. Informacje o grupie odbiorców są dostępne za pośrednictwem interfejsów API Kafka.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs i Event Hubs dla Kafka, zobacz następujące artykuły:  

- [Apache Kafka Przewodnik dla deweloperów Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka Przewodnik migracji Event Hubs](apache-kafka-migration-guide.md)
- [Apache Kafka Przewodnik rozwiązywania problemów dla Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Zalecane konfiguracje](apache-kafka-configurations.md)


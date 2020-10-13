---
title: Często zadawane pytania — Event Hubs platformy Azure dla Apache Kafka
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące obsługi usługi Azure Event Hubs "dla klientów Apache Kafka nieuwzględnionych w innych miejscach.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dc6a12b2098a1fdf33adda92b4347f91ab4e5489
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828112"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Często zadawane pytania — Event Hubs dla Apache Kafka 
Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące migracji do Event Hubs Apache Kafka.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Czy usługa Azure Event Hubs działa na Apache Kafka?

Nie. Azure Event Hubs to natywny dla chmury wielowarstwowy Broker z obsługą wielu protokołów, które są opracowywane i obsługiwane przez firmę Microsoft i nie korzystają z żadnego Apache Kafka kodu. Jednym z obsługiwanych protokołów jest protokół RPC Kafka dla klientów i interfejsów API klienta Kafka. Event Hubs współpracuje z wieloma istniejącymi aplikacjami Kafka. Aby uzyskać więcej informacji, zobacz [Event Hubs Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). Ponieważ koncepcje Apache Kafka i Event Hubs platformy Azure są bardzo podobne (ale nie są identyczne), możemy zaoferować niezgodną niezawodność usługi Azure Event Hubs klientom z istniejącymi inwestycjami w Apache Kafka. 

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


---
title: Dystrybuuj dane globalnie za pomocą Azure Cosmos DB
description: Dowiedz się więcej o globalnej replikacji geograficznej, zapisie wieloregionowym, przejściu do trybu failover i odzyskiwaniu danych przy użyciu globalnych baz danych z Azure Cosmos DB, globalnie dystrybuowanej, wielomodelowej usługi bazy danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ddb6bc297a32be856172707a220fe1dfaefb9f6d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487453"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Globalna dystrybucja danych przy użyciu usługi Azure Cosmos DB

Współczesne aplikacje muszą zapewniać szybki czas reakcji i zawsze działać w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Te aplikacje są zwykle wdrażane w wielu centrach danych i są nazywane globalnie dystrybuowanymi. Globalnie dystrybuowane aplikacje muszą mieć globalnie dystrybuowaną bazę danych, która może w sposób przezroczysty replikować dane w dowolnym miejscu na świecie, aby umożliwić aplikacjom działanie na kopii danych znajdującej się blisko użytkowników. 

Azure Cosmos DB to globalnie dystrybuowana usługa bazy danych, która została zaprojektowana w celu zapewnienia małych opóźnień, elastycznej skalowalności, dobrze zdefiniowanej semantyki pod kątem spójności danych i wysokiej dostępności. W krótkim czasie, jeśli Twoja aplikacja potrzebuje szybkiego czasu odpowiedzi w dowolnym miejscu na świecie, jeśli wymagane jest zawsze w trybie online i wymaga nieograniczonej i elastycznej skalowalności przepływności i magazynu, należy skompilować aplikację na Azure Cosmos DB.

Bazy danych można skonfigurować tak, aby były one dystrybuowane globalnie i dostępne w dowolnym regionie świadczenia usługi Azure. Aby zmniejszyć opóźnienie, umieść dane blisko miejsca, w którym znajdują się użytkownicy. Wybór wymaganych regionów zależy od globalnego zasięgu aplikacji i miejsca, w którym znajdują się użytkownicy. Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów skojarzonych z Twoim kontem Cosmos. Udostępnia on pojedynczy obraz systemu dla globalnie rozproszonej bazy danych usługi Azure Cosmos i kontenerów, które aplikacja może odczytać i zapisać lokalnie. 

Za pomocą Azure Cosmos DB możesz w dowolnym momencie dodać lub usunąć regiony skojarzone z Twoim kontem. Aby można było dodać lub usunąć region, aplikacja nie musi być wstrzymana ani ponownie wdrożona. Jest ono ciągle dostępne przez cały czas ze względu na funkcje wielomultihostingune zapewniane przez usługę.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Topologia wdrożenia o wysokiej dostępności" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Najważniejsze zalety dystrybucji globalnej

**Twórz globalne aplikacje aktywne-aktywne.** W przypadku korzystania z nowej wieloregionowej protokołu replikacji zapisów każdy region obsługuje zarówno zapis, jak i odczyt. Funkcja zapisów w wielu regionach umożliwia również:

- Nieograniczone elastyczne zapisywanie i skalowalność.
- 99,999% dostępności odczytu i zapisu na całym świecie.
- Gwarantowane odczyty i zapisy obsługiwane w czasie krótszym niż 10 milisekund w 99 percentylu.

Korzystając z Azure Cosmos DB interfejsów API multihostingu, aplikacja rozpoznaje najbliższy region i może wysyłać żądania do tego regionu. Najbliższy region jest identyfikowany bez żadnych zmian konfiguracji. Po dodaniu i usunięciu regionów do i z konta usługi Azure Cosmos, aplikacja nie musi być ponownie wdrażana ani wstrzymywana, przez cały czas będzie nadal mieć wysoką dostępność.

**Twórz wysoce reagujące aplikacje.** Aplikacja może wykonywać operacje odczytu i zapisu niemal w czasie rzeczywistym względem wszystkich regionów wybranych dla bazy danych. Azure Cosmos DB wewnętrznie obsługuje replikację danych między regionami z gwarancją poziomu spójności wybranego poziomu.

**Twórz aplikacje o wysokiej dostępności.** Uruchamianie bazy danych w wielu regionach na całym świecie zwiększa dostępność bazy danych. Jeśli jeden region jest niedostępny, inne regiony automatycznie obsługują żądania aplikacji. Azure Cosmos DB oferuje dostępność do odczytu i zapisu na 99,999% dla wieloregionowych baz danych.

**Zachowaj ciągłość biznesową podczas awarii regionalnej.** Azure Cosmos DB obsługuje [Automatyczne przełączanie w tryb failover](how-to-manage-database-account.md#automatic-failover) podczas regionalnej awarii. W trakcie regionalnej awarii Azure Cosmos DB nadal utrzymuje czas oczekiwania, dostępność, spójność i przepływność umowy SLA. Aby zapewnić wysoką dostępność całej aplikacji, Cosmos DB oferuje ręczny interfejs API trybu failover w celu symulowania awarii regionalnej. Korzystając z tego interfejsu API, można wykonywać szczegółowe informacje o ciągłości biznesowej.

**Globalne skalowanie przepływności odczytu i zapisu.** Każdy region można włączyć do zapisu i elastycznie skalować operacje odczytu i zapisu na całym świecie. Przepływność, którą aplikacja konfiguruje w bazie danych Azure Cosmos lub jest obsługiwana przez aplikację dla wszystkich regionów skojarzonych z kontem usługi Azure Cosmos. Tymczasowa przepływność jest gwarantowana na podstawie [finansów umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Wybieraj spośród kilku dobrze zdefiniowanych modeli spójności.** Protokół replikacji Azure Cosmos DB oferuje pięć dobrze zdefiniowanych, praktycznych i intuicyjnych modeli spójności. Każdy model ma kompromis między spójnością i wydajnością. Te modele spójności umożliwiają łatwe tworzenie globalnie rozproszonych aplikacji.

## <a name="next-steps"></a><a id="Next Steps"></a>Następne kroki

Więcej informacji na temat dystrybucji globalnej można znaleźć w następujących artykułach:

* [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
* [Jak skonfigurować wieloregionowe operacje zapisu w aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów dla usługi wieloadresowości](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Dodawanie lub usuwanie regionów z konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Utwórz niestandardowe zasady rozwiązywania konfliktów dla kont interfejsu API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programowalne modele spójności w Cosmos DB](consistency-levels.md)
* [Wybierz odpowiedni poziom spójności dla aplikacji](./consistency-levels.md)
* [Poziomy spójności w interfejsach API Azure Cosmos DB](./consistency-levels.md)
* [Wady dostępności i wydajności dla różnych poziomów spójności](./consistency-levels.md)
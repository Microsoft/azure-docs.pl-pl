---
title: Jednostki żądań i przepływność w Azure Cosmos DB
description: Dowiedz się, jak określić i oszacować wymagania jednostki żądań w Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: f1f203d17de9fb0fc9fe8bb0f6de80fe2b93ba8b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327807"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądania w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB płacisz za aprowizowaną przepływność i ilość zużytego miejsca do magazynowania rozliczanych co godzinę. Przepływność należy aprowizować, aby zapewnić stałą dostępność wystarczających zasobów systemowych dla bazy danych usługi Azure Cosmos. Potrzebujesz wystarczającej ilości zasobów, aby spełnić lub przekroczyć [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest znormalizowany przez Azure Cosmos DB i jest wyrażony przez *jednostki żądania* (lub jednostek ru, dla krótkich). Liczbę jednostek RU na sekundę można traktować jak walutę przepływności. Liczba jednostek RU na sekundę to waluta oparta na stawce. Tworzy ona abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonywania operacji bazy danych obsługiwanych przez usługę Azure Cosmos DB. Co najmniej 10 RU/s jest wymagany do przechowywania każdego 1 GB danych.

Koszt przeczytania punktu dla elementu o 1 KB wynosi 1 jednostkę żądania (lub 1 RU). Wszystkim innym operacjom bazy danych koszt jest przypisywany w podobny sposób za pomocą jednostek RU. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru.

Na poniższej ilustracji przedstawiono ogólną koncepcję jednostek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operacje bazy danych zużywają jednostki żądań" border="false":::

Aby można było zarządzać pojemnością i ją planować, usługa Azure Cosmos DB zapewnia, że liczba jednostek RU dla danej operacji bazy danych w danym zestawie danych jest deterministyczna. Możesz sprawdzić nagłówek odpowiedzi, aby śledzić liczbę jednostek RU zużywanych przez każdą operację bazy danych. Gdy zrozumiesz [czynniki wpływające na opłaty za usługę ru](request-units.md#request-unit-considerations) i wymagania dotyczące przepływności aplikacji, możesz efektywnie uruchamiać swoją aplikację.

Dla swojej aplikacji aprowizujesz liczbę jednostek RU na sekundę w przyrostach wynoszących 100 jednostek RU na sekundę. Aby skalować aprowizowaną przepływność dla aplikacji, możesz w dowolnym momencie zwiększyć lub zmniejszyć liczbę jednostek RU. Możesz skalować w przedziałach co 100 jednostek RU. Możesz wprowadzać zmiany programowo lub za pomocą witryny Azure Portal. Opłaty są naliczane godzinowo.

Przepustowość można zainicjować na dwa różne szczegóły:

* **Kontenery**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* **Bazy danych**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Kwestie dotyczące jednostek żądań

W przypadku szacowania liczby jednostek RU na sekundę do aprowizowania należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: liczba jednostek RU do odczytu lub zapisu elementu zwiększa się wraz ze wzrostem rozmiaru elementu.

* **Indeksowanie elementów**: domyślnie każdy element jest automatycznie indeksowany. Jeśli wyłączysz indeksowanie dla niektórych elementów w kontenerze, zostanie użytych mniej jednostek RU.

* **Liczba właściwości elementów**: przy założeniu, że indeksowanie domyślne jest włączone dla wszystkich właściwości, liczba jednostek RU do zapisu elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowane**: zasady indeksowania w każdym kontenerze określają właściwości, które są indeksowane domyślnie. Aby zmniejszyć zużycie jednostek RU operacji zapisu, ogranicz liczbę indeksowanych właściwości.

* **Spójność danych**: mocne i ograniczone nieodświeżone poziomy spójności zużywają około dwa razy więcej jednostek ru podczas wykonywania operacji odczytu w porównaniu z innymi obniżonymi poziomami spójności.

* **Typ odczytów**: punkt odczytuje koszt znacznie mniej niż w przypadku zapytań.

* **Wzorce zapytań**: złożoność zapytania ma wpływ na liczbę jednostek RU używanych w ramach operacji. Czynniki mające wpływ na koszt operacji zapytań obejmują następujące elementu: 
    
    - Liczba wyników zapytań
    - Liczba predykatów
    - Rodzaj predykatów
    - Liczba funkcji zdefiniowanych przez użytkownika
    - Rozmiar danych źródłowych
    - Rozmiar zestawu wyników
    - Projekcje

  Usługa Azure Cosmos DB pozwala zagwarantować, że to samo zapytanie dotyczące tych samych danych zawsze kosztuje tyle samo jednostek RU w przypadku wielokrotnego wykonania.

* **Użycie skryptu**: podobnie jak w przypadku kwerend, procedury składowane i wyzwalacze wykorzystują jednostek RU w oparciu o złożoność wykonywanych operacji. Podczas opracowywania aplikacji sprawdź [nagłówek opłaty za żądanie](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query), aby lepiej zrozumieć, ile jednostek RU używa każda operacja.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, jak zainicjować przepływność w kontenerach i bazach danych usługi Azure Cosmos](set-throughput.md).
* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się więcej o tym, jak [globalnie skalować zainicjowaną przepływność](scaling-throughput.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się [, jak znaleźć opłatę jednostkową żądania dla operacji](find-request-unit-charge.md).
* Informacje na temat [optymalizowania alokowanego kosztu przepływności w Azure Cosmos DB](optimize-cost-throughput.md).
* Dowiedz się [, jak zoptymalizować operacje odczytu i zapisu w Azure Cosmos DB](optimize-cost-reads-writes.md).
* Dowiedz się, jak [zoptymalizować koszt zapytań w Azure Cosmos DB](optimize-cost-queries.md).
* Dowiedz się [, jak używać metryk do monitorowania przepływności](use-metrics.md).

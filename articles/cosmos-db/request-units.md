---
title: Zażądaj jednostek jako przepływność i wydajność w Azure Cosmos DB
description: Dowiedz się, jak określić i oszacować wymagania jednostki żądań w Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88604837"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądania w usłudze Azure Cosmos DB

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest znormalizowany przez Azure Cosmos DB i jest wyrażony przez *jednostki żądania* (lub jednostek ru, dla krótkich). Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB.

Koszt do przeczytania punktu (tj. Pobieranie pojedynczego elementu według jego identyfikatora i wartości klucza partycji) dla elementu 1 KB to 1 jednostka żądania (lub 1 RU). Wszystkim innym operacjom bazy danych koszt jest przypisywany w podobny sposób za pomocą jednostek RU. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru.

Na poniższej ilustracji przedstawiono ogólną koncepcję jednostek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operacje bazy danych zużywają jednostki żądań" border="false":::

Aby można było zarządzać pojemnością i ją planować, usługa Azure Cosmos DB zapewnia, że liczba jednostek RU dla danej operacji bazy danych w danym zestawie danych jest deterministyczna. Możesz sprawdzić nagłówek odpowiedzi, aby śledzić liczbę jednostek RU zużywanych przez każdą operację bazy danych. Gdy zrozumiesz [czynniki wpływające na opłaty za usługę ru](request-units.md#request-unit-considerations) i wymagania dotyczące przepływności aplikacji, możesz efektywnie uruchamiać swoją aplikację.

Typ używanego konta usługi Azure Cosmos określa sposób, w jaki wykorzystano jednostek ru.

- W trybie przewidzianych [przepływności](set-throughput.md) można zarezerwować liczbę jednostek ru dla aplikacji na sekundę w przyrostach wynoszących 100 jednostek ru na sekundę. Aby skalować zainicjowaną przepływność dla aplikacji, można zwiększyć lub zmniejszyć liczbę jednostek RU w dowolnym momencie w przyrostach lub zmniejszeniu liczby 100 jednostek ru. Możesz wprowadzać zmiany programowo lub za pomocą witryny Azure Portal. Opłata jest naliczana godzinowo na podstawie liczby jednostek ru na sekundę, która została zainicjowana. Przepustowość można zainicjować na dwa różne szczegóły:
  - **Kontenery**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
  - **Bazy danych**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
- W trybie [bezserwerowym](serverless.md) nie trzeba inicjować żadnej przepływności podczas tworzenia zasobów na koncie usługi Azure Cosmos. Na koniec okresu rozliczeniowego opłaty są naliczane za liczbę jednostek żądania, które zostały zużyte przez operacje bazy danych.

## <a name="request-unit-considerations"></a>Kwestie dotyczące jednostek żądań

Podczas szacowania liczby jednostek ru zużywanych przez obciążenie należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: liczba jednostek RU do odczytu lub zapisu elementu zwiększa się wraz ze wzrostem rozmiaru elementu.

* **Indeksowanie elementów**: domyślnie każdy element jest automatycznie indeksowany. Jeśli wyłączysz indeksowanie dla niektórych elementów w kontenerze, zostanie użytych mniej jednostek RU.

* **Liczba właściwości elementów**: przy założeniu, że indeksowanie domyślne jest włączone dla wszystkich właściwości, liczba jednostek RU do zapisu elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowane**: zasady indeksowania w każdym kontenerze określają właściwości, które są indeksowane domyślnie. Aby zmniejszyć zużycie jednostek RU operacji zapisu, ogranicz liczbę indeksowanych właściwości.

* **Spójność danych**: mocne i ograniczone nieodświeżone poziomy spójności zużywają około dwa razy więcej jednostek ru podczas wykonywania operacji odczytu w porównaniu z innymi obniżonymi poziomami spójności.

* **Typ odczytu**: punkt odczytuje koszt znacznie mniej jednostek ru niż zapytania.

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
* Dowiedz się więcej o [Azure Cosmos DB](serverless.md).
* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się więcej o tym, jak [globalnie skalować zainicjowaną przepływność](scaling-throughput.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się [, jak znaleźć opłatę jednostkową żądania dla operacji](find-request-unit-charge.md).
* Informacje na temat [optymalizowania alokowanego kosztu przepływności w Azure Cosmos DB](optimize-cost-throughput.md).
* Dowiedz się [, jak zoptymalizować operacje odczytu i zapisu w Azure Cosmos DB](optimize-cost-reads-writes.md).
* Dowiedz się, jak [zoptymalizować koszt zapytań w Azure Cosmos DB](optimize-cost-queries.md).
* Dowiedz się [, jak używać metryk do monitorowania przepływności](use-metrics.md).

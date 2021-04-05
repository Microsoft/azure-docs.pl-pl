---
title: Zażądaj jednostek jako przepływność i wydajność w Azure Cosmos DB
description: Dowiedz się, jak określić i oszacować wymagania jednostki żądań w Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: 23401885580a3883dc49eccc97c17bbedd9080ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187327"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądania w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Jednostka żądania to waluta wydajności, która umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB.

Koszt do przeczytania punktu (tj. Pobieranie pojedynczego elementu według jego identyfikatora i wartości klucza partycji) dla elementu 1 KB to 1 jednostka żądania (lub 1 RU). Wszystkim innym operacjom bazy danych koszt jest przypisywany w podobny sposób za pomocą jednostek RU. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru.

Na poniższej ilustracji przedstawiono ogólną koncepcję jednostek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Operacje bazy danych zużywają jednostki żądań" border="false":::

Aby można było zarządzać pojemnością i ją planować, usługa Azure Cosmos DB zapewnia, że liczba jednostek RU dla danej operacji bazy danych w danym zestawie danych jest deterministyczna. Możesz sprawdzić nagłówek odpowiedzi, aby śledzić liczbę jednostek RU zużywanych przez każdą operację bazy danych. Gdy zrozumiesz [czynniki wpływające na opłaty za usługę ru](request-units.md#request-unit-considerations) i wymagania dotyczące przepływności aplikacji, możesz efektywnie uruchamiać swoją aplikację.

Typ używanego konta usługi Azure Cosmos określa sposób, w jaki wykorzystano jednostek ru. Istnieją trzy tryby, w których można utworzyć konto:

1. **Tryb przepływności aprowizacji**: w tym trybie można zarezerwować liczbę jednostek ru dla aplikacji na sekundę w przyrostach wynoszących 100 jednostek ru na sekundę. Aby skalować zainicjowaną przepływność dla aplikacji, można zwiększyć lub zmniejszyć liczbę jednostek RU w dowolnym momencie w przyrostach lub zmniejszeniu liczby 100 jednostek ru. Możesz wprowadzać zmiany programowo lub za pomocą witryny Azure Portal. Opłata jest naliczana godzinowo na podstawie liczby jednostek ru na sekundę, która została zainicjowana. Aby dowiedzieć się więcej, zapoznaj się z artykułem [zainicjowanej przepływności](set-throughput.md) .

   Przepustowość można zainicjować na dwa różne szczegóły:

   * **Kontenery**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
   * **Bazy danych**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

2. **Tryb bez serwera**: w tym trybie nie trzeba inicjować żadnej przepływności podczas tworzenia zasobów na koncie usługi Azure Cosmos. Na koniec okresu rozliczeniowego opłaty są naliczane za liczbę jednostek żądania, które zostały zużyte przez operacje bazy danych. Aby dowiedzieć się więcej, zobacz artykuł [przepływność Bezserwerowa](serverless.md) . 

3. **Tryb automatycznego skalowania**: w tym trybie można automatycznie i natychmiast skalować przepływność (ru/s) bazy danych lub kontenera na podstawie jego użycia, bez wpływu na dostępność, opóźnienia, przepływność lub wydajność obciążenia. Ten tryb jest dobrze dostosowany do obciążeń o kluczowym znaczeniu, które mają zmienne lub nieprzewidywalne wzorce ruchu, i wymagają umowy SLA na wysoką wydajność i skalowalność. Aby dowiedzieć się więcej, zobacz artykuł [przepływność skalowania automatycznego](provision-throughput-autoscale.md) . 

## <a name="request-unit-considerations"></a>Kwestie dotyczące jednostek żądań

Podczas szacowania liczby jednostek ru zużywanych przez obciążenie należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: liczba jednostek RU do odczytu lub zapisu elementu zwiększa się wraz ze wzrostem rozmiaru elementu.

* **Indeksowanie elementów**: domyślnie każdy element jest automatycznie indeksowany. Jeśli wyłączysz indeksowanie dla niektórych elementów w kontenerze, zostanie użytych mniej jednostek RU.

* **Liczba właściwości elementów**: przy założeniu, że indeksowanie domyślne jest włączone dla wszystkich właściwości, liczba jednostek RU do zapisu elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowane**: zasady indeksowania w każdym kontenerze określają właściwości, które są indeksowane domyślnie. Aby zmniejszyć zużycie jednostek RU operacji zapisu, ogranicz liczbę indeksowanych właściwości.

* **Spójność danych**: mocne i ograniczone nieodświeżone poziomy spójności zużywają około dwa razy więcej jednostek ru podczas wykonywania operacji odczytu w porównaniu z innymi obniżonymi poziomami spójności.

* **Typ odczytu**: punkt odczytuje koszt znacznie mniej jednostek ru niż zapytania.

* **Wzorce zapytań**: złożoność zapytania ma wpływ na liczbę jednostek RU używanych w ramach operacji. Czynniki mające wpływ na koszt operacji zapytań obejmują następujące elementu: 
 
  * Liczba wyników zapytań
  * Liczba predykatów
  * Rodzaj predykatów
  * Liczba funkcji zdefiniowanych przez użytkownika
  * Rozmiar danych źródłowych
  * Rozmiar zestawu wyników
  * Projekcje

  Te same zapytania dotyczące tych samych danych będą zawsze kosztować tę samą liczbę jednostek ru przy powtarzanych wykonaniach.

* **Użycie skryptu**: podobnie jak w przypadku kwerend, procedury składowane i wyzwalacze wykorzystują jednostek RU w oparciu o złożoność wykonywanych operacji. Podczas opracowywania aplikacji sprawdź [nagłówek opłaty za żądanie](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request), aby lepiej zrozumieć, ile jednostek RU używa każda operacja.

## <a name="request-units-and-multiple-regions"></a>Jednostki żądań i wiele regionów

Jeśli zainicjujesz jednostek ru *"r"* w kontenerze Cosmos (lub bazie danych), Cosmos DB zapewnia dostępność *"r"* jednostek RU w *każdym* regionie skojarzonym z Twoim kontem Cosmos. Nie można wybiórczo przypisywać jednostek ru do określonego regionu. Obsługa jednostek RU w kontenerze Cosmos (lub bazie danych) jest obsługiwana we wszystkich regionach skojarzonych z Twoim kontem Cosmos.

Przy założeniu, że kontener Cosmos jest skonfigurowany przy użyciu *"R"* jednostek ru, a istnieją regiony *"N"* skojarzone z kontem Cosmos, Łączna liczba jednostek ru dostępnych globalnie w kontenerze = *R* x *N*.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływność. Możesz uzyskać około 2x przepływność odczytu dla bardziej swobodnych poziomów spójności (np. *sesji*, *spójnego prefiksu* i spójności *ostatecznej* ) w porównaniu z silniejszymi poziomami spójności (np. *z ograniczeniami nieodświeżonymi* lub *silną* spójnością).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [tym, jak zainicjować przepływność w kontenerach i bazach danych usługi Azure Cosmos](set-throughput.md).
- Dowiedz się więcej o [Azure Cosmos DB](serverless.md).
- Dowiedz się więcej na temat [partycji logicznych](./partitioning-overview.md).
- Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
- Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
- Dowiedz się [, jak znaleźć opłatę jednostkową żądania dla operacji](find-request-unit-charge.md).
- Informacje na temat [optymalizowania alokowanego kosztu przepływności w Azure Cosmos DB](optimize-cost-throughput.md).
- Dowiedz się [, jak zoptymalizować operacje odczytu i zapisu w Azure Cosmos DB](optimize-cost-reads-writes.md).
- Dowiedz się, jak [zoptymalizować koszt zapytań w Azure Cosmos DB](./optimize-cost-reads-writes.md).
- Dowiedz się [, jak używać metryk do monitorowania przepływności](use-metrics.md).

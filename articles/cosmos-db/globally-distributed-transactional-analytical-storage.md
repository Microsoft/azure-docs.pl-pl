---
title: Globalnie dystrybuowane transakcyjne i analityczne (w prywatnej wersji zapoznawczej) magazyn dla kontenerów usługi Azure Cosmos
description: Dowiedz się więcej o magazynie transakcyjnym i analitycznym oraz ich opcjach konfiguracji dla kontenerów usługi Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623393"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalnie dystrybuowany magazyn transakcyjny i analityczny dla kontenerów usługi Azure Cosmos

Kontener usługi Azure Cosmos jest wewnętrznie obsługiwany przez dwa aparaty magazynu — transakcyjny aparat magazynu i aktualizowalny aparat magazynu analitycznego (w ramach prywatnej wersji zapoznawczej). Zarówno aparaty magazynu mają strukturę dzienników, jak i są zoptymalizowane pod kątem zapisu w celu przyspieszenia aktualizacji. Jednak każdy z nich jest zakodowany inaczej:

* **Transakcyjny aparat magazynu** — jest zakodowany w formacie zorientowanym na wiersze dla szybkich operacji odczytu i zapytań transakcyjnych.

* **Aparat magazynu analitycznego** — jest on zakodowany w formacie kolumnowym w celu uzyskania szybkich zapytań analitycznych i skanowania.

![Aparaty magazynu i mapowanie interfejsów API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transakcyjny aparat magazynu jest obsługiwany przez lokalną dysków SSD, podczas gdy magazyn analityczny jest przechowywany w niedrogim magazynie SSD w klastrze. W poniższej tabeli przedstawiono istotne różnice między transakcyjną i analityczną magazynem.


|Funkcja  |Magazyn transakcyjny  |Magazyn analityczny |
|---------|---------|---------|
|Maksymalny rozmiar magazynu na kontener usługi Azure Cosmos |   Nieograniczona liczba      |    Nieograniczona liczba     |
|Maksymalny rozmiar magazynu na klucz partycji logicznej   |   20 GB      |   Nieograniczona liczba      |
|Kodowanie magazynu  |   Zorientowane na wiersze przy użyciu formatu wewnętrznego.   |   Zorientowane na kolumny przy użyciu formatu Apache Parquet. |
|Miejscowość magazynu |   Zreplikowane magazyny obsługiwane przez lokalne/wewnętrzne dysków SSD klastra. |  Replikowany magazyn został poddany niedrogim dysków SSD zdalnego/wyłączenia klastra.       |
|Trwałość  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Interfejsy API, które uzyskują dostęp do danych  |   SQL, MongoDB, Cassandra, Gremlin, tabele i etcd.       | Apache Spark         |
|Przechowywanie (czas wygaśnięcia lub TTL)   |  Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos przy `DefaultTimeToLive` użyciu właściwości.       |   Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos przy `ColumnStoreTimeToLive` użyciu właściwości.      |
|Cena za GB    |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Cena dla transakcji magazynu    |  Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Zalety magazynu transakcyjnego i analitycznego

### <a name="no-etl-operations"></a>Brak operacji ETL

Tradycyjne potoki analityczne są złożone z wieloma etapami, które wymagają operacji extract-transform-load (ETL) do i z warstw obliczeniowych i magazynu. Powoduje to złożone architektury przetwarzania danych. Oznacza to wysokie koszty dotyczące wielu etapów magazynowania i obliczeń oraz duże opóźnienia wynikające z dużych ilości danych przesyłanych między różnymi etapami magazynu i obliczeń.  

Nie ma żadnych nakładów na wykonywanie operacji ETL przy użyciu Azure Cosmos DB. Każdy kontener usługi Azure Cosmos jest obsługiwany przez zarówno sieciowe, jak i analityczne aparaty magazynowe, a transfer danych między sieciowym i analitycznym aparatem magazynu odbywa się w aparacie bazy danych i bez żadnych przeskoków sieci. Wynikowe opóźnienie i koszt są znacznie niższe w porównaniu z tradycyjnymi rozwiązaniami analitycznymi. W przypadku obciążeń transakcyjnych i analitycznych uzyskuje się pojedynczy globalnie rozproszony system przechowywania.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Przechowywanie wielu wersji, aktualizowanie i wykonywanie zapytań do magazynu analitycznego

Magazyn analityczny jest w pełni aktualizowalny i zawiera pełną historię wersji wszystkich aktualizacji transakcyjnych, które wystąpiły w kontenerze usługi Azure Cosmos.

Wszystkie aktualizacje w magazynie transakcyjnym są gwarantowane, aby były widoczne dla magazynu analitycznego w ciągu 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globalnie dystrybuowany magazyn analityczny z wieloma wzorcami

Jeśli Twoje konto usługi Azure Cosmos ma zakres do jednego regionu, dane przechowywane (w magazynie transakcyjnym i analitycznym) w kontenerach są również objęte zakresem jednego regionu. Z drugiej strony, jeśli konto usługi Azure Cosmos jest dystrybuowane globalnie, dane przechowywane w kontenerach również są dystrybuowane globalnie.

W przypadku kont usługi Azure Cosmos skonfigurowanych z wieloma regionami zapisu operacje zapisu w kontenerze (zarówno w przypadku magazynu transakcyjnego, jak i w magazynie analitycznym) są zawsze wykonywane w regionie lokalnym, a tym samym szybko.

W przypadku kont usługi Azure Cosmos w jednym lub wielu regionach, niezależnie od tego, czy pojedynczy region zapisu (pojedynczy wzorzec) lub wiele regionów zapisu (znany również jako wiele wzorców), operacje transakcyjne i analityczne są wykonywane lokalnie w danym regionie.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolacja wydajności między obciążeniami transakcyjnymi i analitycznymi

W danym regionie obciążenia transakcyjne działają względem magazynu transakcyjnych/wierszy kontenera. Z drugiej strony obciążenia analityczne działają na magazyn analityczny/kolumnowy kontenera. Dwa silniki magazynu działają niezależnie i zapewniają ścisłą izolację wydajności między obciążeniami.

Obciążenia transakcyjne zużywają zainicjowaną przepływność (jednostek ru). W przeciwieństwie do obciążeń transakcyjnych przepływność obciążeń analitycznych opiera się na rzeczywistym zużyciu. Obciążenia analityczne zużywają zasoby na żądanie.

## <a name="next-steps"></a>Następne kroki

* [Czas wygaśnięcia w Azure Cosmos DB](time-to-live.md)

---
title: Optymalizacja kosztów magazynu w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami magazynu dla danych przechowywanych w Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080854"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów magazynu w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB oferuje nieograniczony magazyn i przepływność. W przeciwieństwie do przepływności, które trzeba zainicjować i skonfigurować w odniesieniu do kontenerów lub baz danych usługi Azure Cosmos, magazyn jest rozliczany na podstawie użycia. Opłaty są naliczane tylko za używane magazyny logiczne i nie trzeba rezerwować żadnego magazynu z góry. Magazyn automatycznie skaluje się w górę i w dół w oparciu o dane dodawane lub usuwane do kontenera usługi Azure Cosmos.

## <a name="storage-cost"></a>Koszty magazynu

Magazyn jest rozliczany przy użyciu jednostki GB. Lokalny dysk SSD — magazyn kopii zapasowych jest używany przez dane i indeksowanie. Łączny używany magazyn jest równy magazynowi wymaganemu przez dane i indeksy używane we wszystkich regionach, w których są używane Azure Cosmos DB. Jeśli globalnie replikujesz konto usługi Azure Cosmos w trzech regionach, będziesz płacić za łączny koszt magazynu w każdym z tych trzech regionów. Aby oszacować wymaganie magazynu, zobacz Narzędzie do [planowania pojemności](https://www.documentdb.com/capacityplanner) . Koszt magazynu w Azure Cosmos DB wynosi $0,25 GB/miesiąc, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) , aby uzyskać najnowsze aktualizacje. Można skonfigurować alerty w celu określenia magazynu używanego przez kontener usługi Azure Cosmos, aby monitorować magazyn, zobacz artykuł [monitorowanie Azure Cosmos DB](./monitor-cosmos-db.md)).

## <a name="optimize-cost-with-item-size"></a>Optymalizuj koszt z rozmiarem elementu

Azure Cosmos DB oczekuje, że rozmiar elementu wynosi 2 MB lub mniej, aby zapewnić optymalną wydajność i korzyści z kosztów. Jeśli potrzebujesz dowolnego elementu do przechowywania większej ilości danych niż 2 MB, rozważ przeprojektowanie schematu elementu. W rzadkim zdarzeniu, którego nie można ponownie zaprojektować schematu, można podzielić element na elementy podrzędne i połączyć je logicznie ze wspólnym identyfikatorem (ID). Wszystkie funkcje Azure Cosmos DB działają spójnie przez zakotwiczenie tego identyfikatora logicznego.

## <a name="optimize-cost-with-indexing"></a>Optymalizowanie kosztów za pomocą indeksowania

Domyślnie dane są indeksowane automatycznie, co może zwiększyć łączny zużyty magazyn. Można jednak zastosować niestandardowe zasady indeksu w celu zmniejszenia tego obciążenia. Automatyczne indeksowanie, które nie zostało dostrojone za pomocą zasad, to około 10-20% rozmiaru elementu. Przez usunięcie lub dostosowanie zasad indeksu nie są napłacone dodatkowe koszty związane z zapisem i nie jest wymagane dodatkowe możliwości przepływności. Aby skonfigurować niestandardowe zasady indeksowania, zobacz [indeksowanie w Azure Cosmos DB](index-policy.md) . Jeśli wcześniej pracujesz z relacyjnymi bazami danych, możesz zastanowić się, że "indeks wszystkiego" oznacza Podwajanie przestrzeni dyskowej lub wyższą. Jednak w Azure Cosmos DB, w przypadku gdy jest to znacznie mniejsze. W Azure Cosmos DB obciążenie magazynu dla indeksu jest zwykle niskie (10-20%) nawet w przypadku automatycznego indeksowania, ponieważ jest ono przeznaczone do niskiego rozmiaru magazynu. Zarządzając zasadami indeksowania, można kontrolować kompromisy między wpływami indeksowania i wydajnością zapytań w bardziej szczegółowy sposób.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optymalizowanie kosztów za pomocą czasu wygaśnięcia i kanału informacyjnego zmiany

Gdy nie potrzebujesz już danych, możesz bezpiecznie usunąć je z konta usługi Azure Cosmos, używając [czasu wygaśnięcia](time-to-live.md), [kanału informacyjnego zmiany](change-feed.md) lub migracji starych danych do innego magazynu danych, takiego jak Azure Blob Storage lub Azure Data Warehouse. W przypadku czasu wygaśnięcia lub czasu wygaśnięcia usługa Azure Cosmos DB zapewnia możliwość automatycznego usuwania elementów z kontenera po upływie określonego czasu. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąpić wartość dla każdego elementu. Po ustawieniu czasu wygaśnięcia w kontenerze lub na poziomie elementu Azure Cosmos DB automatycznie usunie te elementy po upływie czasu od momentu ostatniej modyfikacji. Używając kanału informacyjnego zmiany, można migrować dane do innego kontenera w Azure Cosmos DB lub do zewnętrznego magazynu danych. Migracja zajmie zero czasu i po zakończeniu migracji można usunąć lub skonfigurować czas wygaśnięcia, aby usunąć źródłowy kontener platformy Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optymalizowanie kosztów dzięki rozbudowanym typom danych multimediów 

Jeśli chcesz przechowywać rozbudowane typy multimediów, na przykład wideo, obrazy itp., masz kilka opcji dostępnych w Azure Cosmos DB. Jedną z opcji jest przechowywanie tych bogatych typów multimediów jako elementów usługi Azure Cosmos. Dla każdego elementu występuje limit 2 MB, a można uniknąć tego limitu poprzez łańcuchowanie elementu danych w wielu elementach SubItems. Lub można je przechowywać w usłudze Azure Blob Storage i używać ich do odwoływania się do nich z poziomu elementów usługi Azure Cosmos. Ta metoda ma wiele zalet i wad. Pierwsze podejście pozwala uzyskać najlepszą wydajność w zakresie opóźnień, umowy SLA przepływności i gotowe globalną dystrybucję dla bogatych typów danych multimedialnych oraz zwykłych elementów usługi Azure Cosmos. Jednak pomoc techniczna jest dostępna z wyższą ceną. Przechowywanie multimediów w usłudze Azure Blob Storage pozwala obniżyć koszty ogólne. Jeśli opóźnienie ma krytyczne znaczenie, można użyć usługi Premium Storage dla plików multimediów rozbudowanych, do których odwołuje się element Cosmos platformy Azure. Integruje się to natywnie z siecią CDN w celu obsługi obrazów z serwera brzegowego przy niższych kosztach w celu obejścia ograniczenia geograficznego. W tym scenariuszu należy zaradzić sobie z dwoma usługami — Azure Cosmos DB i magazynem obiektów blob platformy Azure, co może zwiększyć koszty operacyjne. 

## <a name="check-storage-consumed"></a>Sprawdź zużyte miejsce w magazynie

Aby sprawdzić użycie magazynu przez kontener usługi Azure Cosmos, możesz uruchomić żądanie HEAD lub GET na kontenerze i sprawdzić `x-ms-request-quota` `x-ms-request-usage` nagłówki i. Alternatywnie, podczas pracy z zestawem SDK .NET, można użyć właściwości [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))i [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) , aby uzyskać zużyty magazyn.

## <a name="using-sdk"></a>Używanie zestawu SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](./optimize-cost-reads-writes.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)

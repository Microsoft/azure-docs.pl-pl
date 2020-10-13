---
title: Wycofane Azure Cosmos DB poziomów wydajności
description: Zapoznaj się z poziomami wydajności S1, S2 i S3 wcześniej dostępnymi w Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: ce81ce9afa45c93010c457bc292bba037607f96f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020885"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Wycofywanie poziomów wydajności S1, S2 i S3

> [!IMPORTANT] 
> Poziomy wydajności S1, S2 i S3 omówione w tym artykule są wycofywane i nie są już dostępne dla nowych kont Azure Cosmos DB.

Ten artykuł zawiera omówienie poziomów wydajności S1, S2 i S3 oraz omawia, w jaki sposób kolekcje korzystające z tych poziomów wydajności można migrować do kolekcji z jedną partycją. Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania:

- [Dlaczego są wycofywane poziomy wydajności S1, S2 i S3?](#why-retired)
- [Jak kolekcje pojedynczej partycji i kolekcje partycjonowane są porównywane z poziomami wydajności S1, S2, S3?](#compare)
- [Co należy zrobić, aby zapewnić nieprzerwany dostęp do danych?](#uninterrupted-access)
- [Jak Moja kolekcja zmieni się po migracji?](#collection-change)
- [Jak zmienia się rozliczenia po przeprowadzeniu migracji do kolekcji z jedną partycją?](#billing-change)
- [Co zrobić, jeśli potrzebuję więcej niż 20 GB miejsca?](#more-storage-needed)
- [Czy mogę zmienić poziomy wydajności S1, S2 i S3 przed planowaną migracją?](#change-before)
- [Jak mogę przeprowadzić migrację z poziomów wydajności S1, S2, S3 do kolekcji z pojedynczą partycją?](#migrate-diy)
- [Jak ma wpływ klient z umową EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Dlaczego są wycofywane poziomy wydajności S1, S2 i S3?

Poziomy wydajności S1, S2 i S3 nie oferują elastyczności oferowanej przez standardową Azure Cosmos DB. W przypadku poziomów wydajności S1, S2 i S3 zarówno przepustowość, jak i pojemność magazynu są wstępnie ustawione i nie oferują elastyczności. Azure Cosmos DB teraz oferuje możliwość dostosowywania przepływności i magazynu, co zapewnia większą elastyczność skalowania w miarę potrzeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekcje pojedynczej partycji i kolekcje partycjonowane są porównywane z poziomami wydajności S1, S2, S3?

Poniższa tabela zawiera porównanie opcji przepływności i magazynu dostępnych w kolekcjach z jedną partycją, kolekcjami partycjonowanymi i poziomami wydajności w warstwie S1, S2 i S3. Oto przykład dla regionu Wschodnie stany USA 2:

| Nazwa przydziału  |Partycjonowana kolekcja|Kolekcja pojedynczej partycji|S1|S2|S3|
|---|---|---|---|---|---|
|Maksymalna przepływność|Nieograniczona liczba|10 000 jednostek RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimalna przepływność|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maksymalna ilość miejsca w magazynie|Nieograniczona liczba|20 GB|20 GB|20 GB|20 GB|
|Cena (co miesiąc)|Przepływność: $6/100 RU/s<br><br>Magazyn: $0,25/GB|Przepływność: $6/100 RU/s<br><br>Magazyn: $0,25/GB|$25 USD|$50 USD|$100 USD|

Czy jesteś klientem z umową EA? Jeśli tak, zobacz, [jak to miało wpływ klient z umową EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co należy zrobić, aby zapewnić nieprzerwany dostęp do danych?

Jeśli masz kolekcję S1, S2 lub S3, należy migrować kolekcję do kolekcji z jedną partycją programowo [przy użyciu zestawu .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak Moja kolekcja zmieni się po migracji?

Jeśli masz kolekcję S1, możesz ją migrować do kolekcji z jedną partycją z przepustowością 400 RU/s. 400 RU/s to najniższa przepływność dostępna z kolekcjami z jedną partycją. Jednak koszt dla 400 RU/s w kolekcji o pojedynczej partycji jest w przybliżeniu taki sam, jak w przypadku zbierania S1 i 250 RU/s — nie płacisz za dodatkowe 150 RU/s.

Jeśli masz kolekcję S2, możesz ją migrować do kolekcji z jedną partycją z 1 K RU/s. Nie zostanie wprowadzona zmiana poziomu przepływności.

Jeśli masz kolekcję S3, możesz ją migrować do kolekcji z jedną partycją z 2,5 K RU/s. Nie zostanie wprowadzona zmiana poziomu przepływności.

W każdym z tych przypadków po przeprowadzeniu migracji kolekcji będzie można dostosować poziom przepływności lub skalować ją w górę i w dół, aby zapewnić użytkownikom dostęp do małych opóźnień. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak zmienia się rozliczenia po migracji do kolekcji z jedną partycją?

Przy założeniu, że masz 10 zbiorów S1, 1 GB miejsca w magazynie dla każdego, w regionie Wschodnie stany USA, i przeniesiesz te 10 zebranych kolekcji S1 do 10 kolekcji z jedną partycją na 400 RU/s (poziom minimalny). Rachunek będzie wyglądać następująco w przypadku przechowywania 10 pojedynczych kolekcji partycji przez cały miesiąc:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Jak ceny S1 dla 10 kolekcji są porównywane z 10 kolekcjami przy użyciu cen dla kolekcji z jedną partycją" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Co zrobić, jeśli potrzebuję więcej niż 20 GB miejsca?

Bez względu na to, czy masz kolekcję z poziomem wydajności S1, S2 lub S3, czy masz kolekcję o pojedynczej partycji, z których wszystkie mają 20 GB dostępnego miejsca w magazynie, możesz użyć narzędzia do migracji danych Azure Cosmos DB, aby zmigrować dane do kolekcji partycjonowanej z niemal nieograniczonym magazynem. Aby uzyskać informacje o korzyściach z kolekcji partycjonowanej, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Czy mogę zmienić poziomy wydajności S1, S2 i S3 przed planowaną migracją?

Tylko istniejące konta z wydajnością S1, S2 i S3 można zmieniać i zmieniać warstwy wydajności programowo [przy użyciu zestawu .NET SDK](#migrate-diy). W przypadku zmiany z S1, S3 lub S3 do kolekcji z jedną partycją nie można wrócić do poziomów wydajności S1, S2 i S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak mogę przeprowadzić migrację z poziomów wydajności S1, S2, S3 do kolekcji z pojedynczą partycją?

Można przeprowadzić migrację z poziomów wydajności S1, S2 i S3 do kolekcji z jedną partycją programowo przy [użyciu zestawu .NET SDK](#migrate-diy). Możesz to zrobić samodzielnie przed planowaną migracją, aby korzystać z elastycznych opcji przepływności dostępnych dla kolekcji z jedną partycją.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrowanie do kolekcji z jedną partycją przy użyciu zestawu .NET SDK

Ta sekcja dotyczy tylko zmiany poziomu wydajności kolekcji przy użyciu [interfejsu API programu SQL .NET](sql-api-sdk-dotnet.md), ale ten proces jest podobny dla naszych innych zestawów SDK.

Oto fragment kodu dotyczący zmiany przepływności kolekcji na 5 000 jednostek żądań na sekundę:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Odwiedź witrynę [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) , aby wyświetlić dodatkowe przykłady i dowiedzieć się więcej na temat naszych metod oferty:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak ma wpływ klient z umową EA?

Klienci z umowami EA będą zabezpieczeni z ceną do końca bieżącego kontraktu.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat cen i zarządzania danymi za pomocą Azure Cosmos DB, zapoznaj się z następującymi zasobami:

1.  [Partycjonowanie danych w Cosmos DB](sql-api-partition-data.md). Zapoznaj się z różnicą między kontenerem z jedną partycją i kontenerami partycjonowanymi, a także wskazówki dotyczące wdrażania strategii partycjonowania w sposób płynny.
2.  [Cennik Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Dowiedz się więcej o kosztach aprowizacji i zużywaniu magazynu.
3.  [Jednostki żądania](request-units.md). Zapoznaj się z użyciem przepływności dla różnych typów operacji, na przykład odczyt, zapis, zapytanie.

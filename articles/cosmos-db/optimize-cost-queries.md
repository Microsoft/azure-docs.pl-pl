---
title: Optymalizuj koszt i RU/s, aby uruchamiać zapytania w Azure Cosmos DB
description: Dowiedz się, jak oszacować opłaty jednostkowe żądań dla zapytania i zoptymalizować zapytanie pod względem wydajności i kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6ff67e3c1588f6671173d6fa1eec3dc15883291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020919"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym zapytania relacyjne i hierarchiczne, które działają na elementach w kontenerze. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Nie myśl o zasobach sprzętowych i zarządzaniu nimi — zamiast tego pomyśl o jednostce żądania (RU) jako pojedynczej mierze zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługiwania żądań. W tym artykule opisano, jak oszacować opłaty za jednostki żądania dla zapytania i jak zoptymalizować zapytanie pod względem wydajności i kosztów.

Odczyty w Azure Cosmos DB są zwykle uporządkowane od najszybszego/najbardziej wydajnego do wolniejszego/mniej wydajnego w zakresie przepływności w następujący sposób:  

* Odczyty punktów (Wyszukiwanie klucza/wartości dla identyfikatora pojedynczego elementu i klucza partycji).

* Zapytanie z klauzulą filtru w ramach jednego klucza partycji.

* Kwerenda bez klauzuli filtru równości lub zakresu dla każdej właściwości.

* Zapytanie bez filtrów.

Ponieważ wyszukiwania klucz/wartość w IDENTYFIKATORze elementu są najbardziej wydajnym typem odczytu, należy się upewnić, że identyfikator elementu ma zrozumiałą wartość.

Zapytania odczytujące dane z co najmniej jednej partycji powodują wyższe opóźnienia i zużywają większą liczbę jednostek żądania. Ponieważ każda partycja ma Automatyczne indeksowanie wszystkich właściwości, zapytanie może być obsługiwane efektywnie z indeksu. Można tworzyć zapytania, które używają wielu partycji szybciej, przy użyciu opcji równoległości. Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [partycjonowanie w Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Oceń opłatę jednostkową żądania dla zapytania

Po zapisaniu danych w kontenerach usługi Azure Cosmos można użyć Eksplorator danych w Azure Portal do konstruowania i uruchamiania zapytań. Koszt zapytań można również uzyskać za pomocą Eksploratora danych. Ta metoda zapewnia zrozumienie rzeczywistych opłat związanych z typowymi zapytaniami i operacjami obsługiwanymi przez system.

Koszt zapytań można również uzyskać programowo przy użyciu zestawów SDK. Aby zmierzyć obciążenie związane z dowolną operacją, taką jak tworzenie, aktualizowanie lub usuwanie, należy przeprowadzić inspekcję `x-ms-request-charge` nagłówka podczas korzystania z interfejsu API REST. Jeśli używasz platformy .NET lub zestawu Java SDK, `RequestCharge` Właściwość jest równoważną właściwością do uzyskania opłaty za żądanie, a ta właściwość jest obecna w ResourceResponse lub FeedResponse.

```csharp
// Measure the performance (request units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Czynniki wpływające na opłatę jednostkową żądania dla zapytania

Jednostki żądań dla zapytań są zależne od wielu czynników. Na przykład liczba załadowanych/zwróconych elementów usługi Azure Cosmos, liczba wyszukiwań względem indeksu, czas kompilacji zapytania itd. szczegóły. Azure Cosmos DB gwarantuje, że to samo zapytanie wykonywane na tych samych danych zawsze będzie używać tej samej liczby jednostek żądania nawet z powtarzanymi wykonaniami. Profil zapytania korzystający z metryk wykonywania zapytania daje dobry pomysł na to, jak jednostki żądań są zużywane.  

W niektórych przypadkach może zostać wyświetlona sekwencja odpowiedzi 200 i 429 oraz zmienne jednostki żądań w wykonaniu stronicowanej kwerendy, czyli ponieważ zapytania będą uruchamiane tak szybko, jak to możliwe, na podstawie dostępnych jednostek ru. Może zostać wyświetlony podział zapytania na wiele stron/rundy między serwerem a klientem. Na przykład 10 000 elementy mogą być zwracane jako wiele stron, z których każda jest naliczana na podstawie obliczeń wykonanych dla tej strony. Po rozpoczęciu sumowania na tych stronach należy uzyskać taką samą liczbę jednostek ru jak w przypadku całego zapytania.  

## <a name="metrics-for-troubleshooting"></a>Metryki dotyczące rozwiązywania problemów

Wydajność i przepływność zużywane przez zapytania, funkcje zdefiniowane przez użytkownika (UDF) głównie zależą od treści funkcji. Najprostszym sposobem, aby dowiedzieć się, ile czasu wykonanie zapytania w systemie UDF i ile jednostek ru zużyto, jest przez włączenie metryk zapytania. Jeśli używasz zestawu SDK platformy .NET, poniżej przedstawiono przykładowe metryki zapytań zwrócone przez zestaw SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Najlepsze rozwiązania dotyczące kosztu optymalizacji zapytań 

Podczas optymalizowania zapytań dotyczących kosztów należy wziąć pod uwagę następujące najlepsze rozwiązania:

* **Lokalizowanie wielu typów jednostek**

   Spróbuj zlokalizować wiele typów jednostek w jednej lub mniejszej liczbie kontenerów. Ta metoda daje korzyści nie tylko z perspektywy cenowej, ale również dla wykonywania zapytań i transakcji. Zapytania są objęte zakresem jednego kontenera; i niepodzielne transakcje dla wielu rekordów za pośrednictwem procedur składowanych/wyzwalaczy są ograniczone do klucza partycji w ramach jednego kontenera. Współlokalizowanie jednostek w tym samym kontenerze może zmniejszyć liczbę podróży w sieci, aby rozwiązać relacje między rekordami. Zwiększa to wydajność, co umożliwia wykonywanie transakcji niepodzielnych za pośrednictwem wielu rekordów dla większego zestawu danych, a w efekcie obniża koszty. Jeśli współlokalizowanie wielu typów jednostek w jednej lub mniejszej liczbie kontenerów jest trudne dla danego scenariusza, zazwyczaj ze względu na to, że przeprowadzasz migrację istniejącej aplikacji i nie chcesz wprowadzać żadnych zmian w kodzie — należy wziąć pod uwagę przepływność aprowizacji na poziomie bazy danych.  

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

   Złożoność zapytania wpływa na liczbę jednostek żądań (jednostek ru) używanych do operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych. Wszystkie te czynniki wpływają na koszt operacji związanych z wykonywaniem zapytań. 

   Opłata za żądanie zwrócona w nagłówku żądania wskazuje koszt danego zapytania. Na przykład, jeśli zapytanie zwraca elementy 1000 1-KB, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz artykuł [jednostki żądań](request-units.md) i Kalkulator jednostek żądania. 

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej o tym, [jak działa Cennik platformy Azure Cosmos](how-pricing-works.md)
* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
* Dowiedz się więcej o [zarezerwowanej pojemności Azure Cosmos DB](cosmos-db-reserved-capacity.md)


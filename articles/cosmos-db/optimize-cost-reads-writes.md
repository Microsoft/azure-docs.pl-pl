---
title: Optymalizacja kosztów żądań w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zoptymalizować koszty podczas wystawiania żądań na Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 58b57bd592ec0b302724f9339c0e0d48fed42d15
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281185"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Optymalizuj koszt żądania w Azure Cosmos DB

W tym artykule opisano, jak żądania odczytu i zapisu są tłumaczone na [jednostki żądań](request-units.md) oraz jak zoptymalizować koszt tych żądań. Operacje odczytu obejmują odczyty punktów i zapytania. Operacje zapisu obejmują Wstawianie, zastępowanie, usuwanie i upsert elementów.

Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, które działają na elementach w kontenerze. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych w celu obsługi żądania.

## <a name="measuring-the-ru-charge-of-a-request"></a>Mierzenie opłaty dla jednostki RU dla żądania

Ważne jest, aby mierzyć obciążenie RU dla żądań, aby zrozumieć ich rzeczywisty koszt, a także oszacować efektywność optymalizacji. Ten koszt można pobrać przy użyciu Azure Portal lub inspekcji odpowiedzi wysyłanej z Azure Cosmos DB za pomocą jednego z zestawów SDK. Aby uzyskać szczegółowe instrukcje na ten temat, zobacz [Znajdowanie opłaty jednostkowej żądania w Azure Cosmos DB](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Odczytywanie danych: odczyty punktów i zapytania

Operacje odczytu w Azure Cosmos DB są zwykle uporządkowane od najszybszego/najbardziej wydajnego do wolniejszego/mniej wydajnego w zakresie użycia RU w następujący sposób:  

* Odczyty punktów (Wyszukiwanie klucza/wartości dla identyfikatora pojedynczego elementu i klucza partycji).
* Zapytanie z klauzulą filtru w ramach jednego klucza partycji.
* Kwerenda bez klauzuli filtru równości lub zakresu dla każdej właściwości.
* Zapytanie bez filtrów.

### <a name="role-of-the-consistency-level"></a>Rola poziomu spójności

Przy użyciu **silnych** lub **ograniczonych** [poziomów spójności](consistency-levels.md), koszt ru każdej operacji odczytu (punkt odczytu lub zapytania) jest podwójny.

### <a name="point-reads"></a>Odczyty punktów

Jedynym czynnikiem wpływającym na opłatę za obiekt typu odczyt punktu (poza używanym poziomem spójności) jest rozmiar pobranego elementu. W poniższej tabeli przedstawiono koszty związane z odczytami w punkcie RU dla elementów o rozmiarze 1 KB i 100 KB.

| **Rozmiar elementu** | **Koszt odczytu z jednego punktu** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 jednostek RU |

Ponieważ odczyty punktów (wyszukiwania typu klucz/wartość w IDENTYFIKATORze elementu) są najbardziej wydajnym rodzajem odczytu, należy się upewnić, że identyfikator elementu ma zrozumiałą wartość, aby można było pobrać elementy z punktem odczytu (zamiast zapytania), gdy jest to możliwe.

### <a name="queries"></a>Zapytania

Jednostki żądań dla zapytań są zależne od wielu czynników. Na przykład liczba załadowanych/zwróconych elementów usługi Azure Cosmos, liczba wyszukiwań względem indeksu, czas kompilacji zapytania itd. szczegóły. Azure Cosmos DB gwarantuje, że to samo zapytanie wykonywane na tych samych danych zawsze będzie używać tej samej liczby jednostek żądania nawet z powtarzanymi wykonaniami. Profil zapytania korzystający z metryk wykonywania zapytania daje dobry pomysł na to, jak jednostki żądań są zużywane.  

W niektórych przypadkach może zostać wyświetlona sekwencja odpowiedzi 200 i 429 oraz zmienne jednostki żądań w wykonaniu stronicowanej kwerendy, czyli ponieważ zapytania będą uruchamiane tak szybko, jak to możliwe, na podstawie dostępnych jednostek ru. Może zostać wyświetlony podział zapytania na wiele stron/rundy między serwerem a klientem. Na przykład 10 000 elementy mogą być zwracane jako wiele stron, z których każda jest naliczana na podstawie obliczeń wykonanych dla tej strony. Po rozpoczęciu sumowania na tych stronach należy uzyskać taką samą liczbę jednostek ru jak w przypadku całego zapytania.

#### <a name="metrics-for-troubleshooting-queries"></a>Metryki zapytań dotyczących rozwiązywania problemów

Wydajność i przepływność zużywane przez zapytania (w tym funkcje zdefiniowane przez użytkownika) głównie zależą od treści funkcji. Najprostszym sposobem, aby dowiedzieć się, ile czasu wykonanie zapytania w systemie UDF i ile jednostek ru zużyto, jest przez włączenie metryk zapytania. Jeśli używasz zestawu SDK platformy .NET, poniżej przedstawiono przykładowe metryki zapytań zwrócone przez zestaw SDK:

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

#### <a name="best-practices-to-cost-optimize-queries"></a>Najlepsze rozwiązania dotyczące kosztu optymalizacji zapytań 

Podczas optymalizowania zapytań dotyczących kosztów należy wziąć pod uwagę następujące najlepsze rozwiązania:

* **Lokalizowanie wielu typów jednostek**

   Spróbuj zlokalizować wiele typów jednostek w jednej lub mniejszej liczbie kontenerów. Ta metoda daje korzyści nie tylko z perspektywy cenowej, ale również dla wykonywania zapytań i transakcji. Zapytania są objęte zakresem jednego kontenera; i niepodzielne transakcje dla wielu rekordów za pośrednictwem procedur składowanych/wyzwalaczy są ograniczone do klucza partycji w ramach jednego kontenera. Współlokalizowanie jednostek w tym samym kontenerze może zmniejszyć liczbę podróży w sieci, aby rozwiązać relacje między rekordami. Zwiększa to wydajność, co umożliwia wykonywanie transakcji niepodzielnych za pośrednictwem wielu rekordów dla większego zestawu danych, a w efekcie obniża koszty. Jeśli współlokalizowanie wielu typów jednostek w jednej lub mniejszej liczbie kontenerów jest trudne dla danego scenariusza, zazwyczaj ze względu na to, że przeprowadzasz migrację istniejącej aplikacji i nie chcesz wprowadzać żadnych zmian w kodzie — należy wziąć pod uwagę przepływność aprowizacji na poziomie bazy danych.  

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

   Złożoność zapytania wpływa na liczbę jednostek żądań (jednostek ru) używanych do operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych. Wszystkie te czynniki wpływają na koszt operacji związanych z wykonywaniem zapytań. 

Azure Cosmos DB zapewnia przewidywalną wydajność w zakresie przepływności i opóźnień przy użyciu modelu przepływności aprowizacji. Obsługiwana przepływność jest reprezentowana w zakresie [jednostek żądań](request-units.md) na sekundę lub RU/s. Jednostka żądania (RU) to logiczne Abstrakcja zasobów obliczeniowych, takich jak procesor CPU, pamięć, we/wy itp., które są wymagane do wykonania żądania. Zainicjowana przepływność (jednostek ru) jest oddzielona i przeznaczona dla kontenera lub bazy danych, aby zapewnić przewidywalną przepływność i opóźnienia. Elastyczna przepływność umożliwia Azure Cosmos DB w celu zapewnienia przewidywalnej i spójnej wydajności, gwarantowanych małych opóźnień i wysokiej dostępności w dowolnej skali. Jednostki żądań reprezentują znormalizowaną walutę, która upraszcza powód, ile zasobów potrzebuje aplikacja.

Opłata za żądanie zwrócona w nagłówku żądania wskazuje koszt danego zapytania. Na przykład, jeśli zapytanie zwraca elementy 1000 1-KB, koszt operacji wynosi 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz artykuł [jednostki żądań](request-units.md) i Kalkulator jednostek żądania.

## <a name="writing-data"></a>Zapisywanie danych

Koszt RU pisania elementu zależy od:

- Rozmiar elementu.
- Liczba właściwości objętych [zasadami indeksowania](index-policy.md) i wymaganych do indeksowania.

Wstawianie 1 KB elementu o mniej niż 5 właściwościach w celu indeksowania kosztów na około 5 jednostek ru. Zastępowanie kosztu elementu dwa razy więcej niż opłata wymagana do wstawienia tego samego elementu.

### <a name="optimizing-writes"></a>Optymalizowanie zapisów

Najlepszym sposobem optymalizacji kosztu operacji zapisu w programie RU jest rightsize elementów i liczby właściwości, które są indeksowane.

- Przechowywanie bardzo dużych elementów w Azure Cosmos DB powoduje wysokie opłaty za RU i można je traktować jako antywzorców. W szczególności nie należy przechowywać zawartości binarnej ani dużych fragmentów tekstu, które nie są potrzebne do wykonywania zapytań. Najlepszym rozwiązaniem jest umieszczenie tego rodzaju danych na [platformie Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) i zapisanie odwołania (lub linku) do obiektu BLOB w ramach elementu, który można zapisać do Azure Cosmos DB.
- Optymalizacja zasad indeksowania do indeksowania tylko właściwości, dla których filtr zapytań może spowodować ogromne różnice w jednostek ru zużywanym przez operacje zapisu. Podczas tworzenia nowego kontenera domyślne zasady indeksowania indeksuje każdą właściwość i każdą z nich znajdującą się w elementach. Chociaż jest to dobre domyślne dla działań programistycznych, zdecydowanie zaleca się ponowne ocenienie i [dostosowanie zasad indeksowania](how-to-manage-indexing-policy.md) podczas przechodzenia do środowiska produkcyjnego lub gdy obciążenie zacznie otrzymywać znaczny ruch.

Podczas masowego pozyskiwania danych zaleca się również korzystanie z [Azure Cosmos DB biblioteki wykonawców zbiorczych](bulk-executor-overview.md) , ponieważ została ona zaprojektowana w celu zoptymalizowania zużycia ru takich operacji. Opcjonalnie można również użyć [Azure Data Factory](../data-factory/introduction.md) , która jest oparta na tej samej bibliotece.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
* Dowiedz się więcej o [zarezerwowanej pojemności Azure Cosmos DB](cosmos-db-reserved-capacity.md)

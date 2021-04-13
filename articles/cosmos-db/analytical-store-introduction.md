---
title: Co to jest magazyn analityczny usługi Azure Cosmos DB?
description: Dowiedz się Azure Cosmos DB magazynach transakcyjnych (opartych na wierszach) i analitycznych (opartych na kolumnach). Zalety magazynu analitycznego, wpływ na wydajność obciążeń na dużą skalę i automatyczna synchronizacja danych z magazynu transakcyjnego do magazynu analitycznego
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364612"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Co to jest Azure Cosmos DB analityczny?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB analityczny jest w pełni izolowanym magazynem kolumn umożliwiającym analizę na dużą skalę na potrzeby danych operacyjnych w Azure Cosmos DB bez wpływu na obciążenia transakcyjne. 

Azure Cosmos DB magazyn transakcyjny jest niezależny od schematu i umożliwia iteracyjne tworzenie aplikacji transakcyjnych bez konieczności zarządzania schematami lub indeksami. W przeciwieństwie do tego Azure Cosmos DB analityczny jest schematowany w celu optymalizacji pod kątem wydajności zapytań analitycznych. W tym artykule opisano szczegółowo magazyn analityczny.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Wyzwania związane z analizą danych operacyjnych na dużą skalę

Wielo modelowe dane operacyjne w kontenerze Azure Cosmos DB są wewnętrznie przechowywane w indeksowym magazynie transakcyjnym opartym na wierszach. Format magazynu wierszy został zaprojektowany w celu umożliwienia szybkiego odczytu i zapisu transakcyjnego w czasie odpowiedzi rzędu milisekund oraz zapytań operacyjnych. Jeśli zestaw danych powiększy się, złożone zapytania analityczne mogą być kosztowne pod względem aprowizowanej przepływności danych przechowywanych w tym formacie. Wysokie zużycie aprowizowanej przepływności z kolei ma wpływ na wydajność obciążeń transakcyjnych używanych przez aplikacje i usługi w czasie rzeczywistym.

Tradycyjnie, aby analizować duże ilości danych, dane operacyjne są Azure Cosmos DB transakcyjnego magazynu danych i przechowywane w oddzielnej warstwie danych. Na przykład dane są przechowywane w magazynie danych lub data lake w odpowiednim formacie. Te dane są później używane do analizy na dużą skalę i analizowane przy użyciu aparatu obliczeniowego, takiego Apache Spark klastrów. To oddzielenie magazynu analitycznego i warstw obliczeniowych od danych operacyjnych powoduje dodatkowe opóźnienie, ponieważ potoki ETL (wyodrębnianie, przekształcanie, ładowanie) są uruchamiane rzadziej, aby zminimalizować potencjalny wpływ na obciążenia transakcyjne.

Potoki ETL stają się również złożone podczas obsługi aktualizacji danych operacyjnych w porównaniu z obsługą tylko nowo pozyowanych danych operacyjnych. 

## <a name="column-oriented-analytical-store"></a>Magazyn analityczny zorientowany na kolumny

Azure Cosmos DB analitycznych rozwiązuje problemy ze złożonością i opóźnieniami, które występują w tradycyjnych potokach ETL. Azure Cosmos DB analityczny może automatycznie synchronizować dane operacyjne w oddzielnym magazynie kolumn. Format magazynu kolumn jest odpowiedni dla zapytań analitycznych na dużą skalę, które mają być wykonywane w zoptymalizowany sposób, co poprawia opóźnienie takich zapytań.

Za Azure Synapse Link można teraz tworzyć rozwiązania HTAP typu no-ETL, łącząc się bezpośrednio Azure Cosmos DB magazynu analitycznego z Azure Synapse Analytics. Umożliwia uruchamianie analiz na dużą skalę niemal w czasie rzeczywistym na podstawie danych operacyjnych.

## <a name="features-of-analytical-store"></a>Funkcje magazynu analitycznego 

Po włączeniu magazynu analitycznego w kontenerze Azure Cosmos DB nowy magazyn kolumn jest tworzony wewnętrznie na podstawie danych operacyjnych w kontenerze. Ten magazyn kolumn jest utrwalany niezależnie od transakcyjnego magazynu zorientowanego na wiersz dla tego kontenera. Wstawianie, aktualizacje i usuwanie danych operacyjnych są automatycznie synchronizowane z magazynem analitycznym. Do synchronizowania danych nie jest potrzebny kanał zmian ani ETL.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Magazyn kolumn dla obciążeń analitycznych na danych operacyjnych

Obciążenia analityczne zwykle obejmują agregacje i sekwencyjne skanowania wybranych pól. Przechowując dane w kolejności głównych kolumn, magazyn analityczny umożliwia serializowanie grupy wartości dla każdego pola. Ten format zmniejsza liczbę operacji we/wy wymaganych do skanowania lub obliczania statystyk dla określonych pól. Znacznie poprawia to czas odpowiedzi na zapytania w przypadku skanowania dużych zestawów danych. 

Jeśli na przykład tabele operacyjne mają następujący format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Przykładowa tabela operacyjna" border="false":::

Magazyn wierszy przechowuje powyższe dane w zsecylizizowanym formacie na wiersz na dysku. Ten format umożliwia szybsze odczyty transakcyjne, zapis i zapytania operacyjne, takie jak "Zwracanie informacji o produkcie Product1". Jednak w miarę powiększania się zestawu danych i uruchamiania złożonych zapytań analitycznych na danych może to być kosztowne. Jeśli na przykład chcesz uzyskać "trendy sprzedaży dla produktu w kategorii o nazwie "Sprzęt" w różnych jednostkach biznesowych i miesiącach", musisz uruchomić złożone zapytanie. Duże skanowania tego zestawu danych mogą być kosztowne pod względem aprowizowanej przepływności, a także wpływać na wydajność obciążeń transakcyjnych, które zasilają aplikacje i usługi w czasie rzeczywistym.

Magazyn analityczny, który jest magazynem kolumn, jest lepiej przystosowany do takich zapytań, ponieważ serializuje podobne pola danych razem i zmniejsza liczbę IOPS dysku.

Na poniższej ilustracji przedstawiono transakcyjny magazyn wierszy w porównaniu z magazynem kolumn analitycznych w Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transakcyjny magazyn wierszy a magazyn kolumn analitycznych w Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Oddzielenie wydajności dla obciążeń analitycznych

Nie ma to wpływu na wydajność obciążeń transakcyjnych ze względu na zapytania analityczne, ponieważ magazyn analityczny jest oddzielony od magazynu transakcyjnego.  Magazyn analityczny nie wymaga przydzielania oddzielnych jednostek żądań (JEDNOSTEK ŻĄDAŃ).

### <a name="auto-sync"></a>Automatyczna synchronizacja

Automatyczna synchronizacja odnosi się do w pełni zarządzanej funkcji usługi Azure Cosmos DB w której wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego niemal w czasie rzeczywistym. Opóźnienie automatycznej synchronizacji zwykle wynosi w ciągu 2 minut. W przypadku bazy danych z udostępnioną przepływnością z dużą liczbą kontenerów opóźnienie automatycznej synchronizacji poszczególnych kontenerów może być wyższe i może potrwać do 5 minut. Chcemy dowiedzieć się więcej o tym, jak to opóźnienie pasuje do Twoich scenariuszy. W tym celu skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Możliwość automatycznej synchronizacji wraz z magazynem analitycznym zapewnia następujące kluczowe korzyści:

### <a name="scalability--elasticity"></a>Elastyczność & skalowalności

Dzięki partycjonowania poziomemu Azure Cosmos DB magazynu transakcyjnego można elastycznie skalować magazyn i przepływność bez żadnych przestojów. Partycjonowanie poziome w magazynie transakcyjnym zapewnia skalowalność & elastyczność w automatycznej synchronizacji, aby zapewnić synchronizację danych z magazynem analitycznym niemal w czasie rzeczywistym. Synchronizacja danych odbywa się niezależnie od przepływności ruchu transakcyjnego, niezależnie od tego, czy wynosi ona 1000 operacji na sekundę, czy 1 milion operacji na sekundę, i nie ma wpływu na aprowizowana przepływność w magazynie transakcyjnym. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatyczna obsługa aktualizacji schematu

Azure Cosmos DB magazyn transakcyjny jest niezależny od schematu i umożliwia iteracyjne tworzenie aplikacji transakcyjnych bez konieczności zarządzania schematami lub indeksami. W przeciwieństwie do tego Azure Cosmos DB analityczny jest schematowany w celu optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji automatycznej synchronizacji Azure Cosmos DB wnioskowanie schematu za pośrednictwem najnowszych aktualizacji z magazynu transakcyjnego.  Zarządza również reprezentacją schematu w magazynie analitycznym, co obejmuje obsługę zagnieżdżonych typów danych.

Gdy schemat ewoluuje i wraz z czasem dodawane są nowe właściwości, magazyn analityczny automatycznie przedstawia schemat unionizowany we wszystkich schematach historycznych w magazynie transakcyjnym.

#### <a name="schema-constraints"></a>Ograniczenia schematu

Następujące ograniczenia mają zastosowanie do danych operacyjnych w programie Azure Cosmos DB po włączeniu magazynu analitycznego, aby automatycznie wywnioskować i poprawnie reprezentować schemat:

* Można mieć maksymalnie 1000 właściwości na dowolnym poziomie zagnieżdżania w schemacie i maksymalną głębokość zagnieżdżania 127.
  * Tylko pierwsze 1000 właściwości jest reprezentowanych w magazynie analitycznym.
  * Tylko pierwsze 127 zagnieżdżonych poziomów jest reprezentowanych w magazynie analitycznym.

* Chociaż w dokumentach JSON (i Cosmos DB/kontenerach) jest wielkość liter z perspektywy unikatowości, magazyn analityczny nie jest.

  * **W tym samym dokumencie:** Nazwy właściwości na tym samym poziomie powinny być unikatowe w porównaniu bez uwzględniania liter. Na przykład następujący dokument JSON ma na tym samym poziomie nazwy "Name" i "name". Chociaż jest to prawidłowy dokument JSON, nie spełnia on ograniczenia unikatowości i dlatego nie będzie w pełni reprezentowany w magazynie analitycznym. W tym przykładzie "Name" i "name" są takie same w porównaniu w sposób bez uwzględniania liter. Tylko `"Name": "fred"` będą reprezentowane w magazynie analitycznym, ponieważ jest to pierwsze wystąpienie. I `"name": "john"` nie będzie w ogóle reprezentowany.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **W różnych dokumentach:** Właściwości na tym samym poziomie i o tej samej nazwie, ale w różnych przypadkach, będą reprezentowane w tej samej kolumnie przy użyciu formatu nazwy pierwszego wystąpienia. Na przykład następujące dokumenty JSON mają elementy `"Name"` i na tym samym `"name"` poziomie. Ponieważ pierwszy format dokumentu to , będzie on używany do reprezentowania nazwy `"Name"` właściwości w magazynie analitycznym. Innymi słowy nazwa kolumny w magazynie analitycznym to `"Name"` . W kolumnie zostaną reprezentowane zarówno wartości `"fred"` i , jak i `"john"` `"Name"` .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Pierwszy dokument kolekcji definiuje początkowy schemat magazynu analitycznego.
  * Właściwości na pierwszym poziomie dokumentu będą reprezentowane jako kolumny.
  * Dokumenty o większej właściwości niż początkowy schemat będą generować nowe kolumny w magazynie analitycznym.
  * Nie można usunąć kolumn.
  * Usunięcie wszystkich dokumentów w kolekcji nie powoduje zresetowania schematu magazynu analitycznego.
  * Nie ma wersji schematu. Ostatnia wersja wywnioskowana z magazynu transakcyjnego jest tym, co będzie widać w magazynie analitycznym.

* Obecnie nie obsługujemy odczytywania nazw kolumn Azure Synapse Spark, które zawierają puste wartości (białe spacje).

* Należy oczekiwać innego zachowania w odniesieniu do wartości `null` jawnych:
  * Pule platformy Spark Azure Synapse odczytają te wartości jako `0` (zero).
  * Pule bez serwera SQL Azure Synapse odczytają te wartości tak, jakby pierwszy dokument kolekcji miał dla tej samej właściwości wartość o `NULL` `non-numeric` typie danych.
  * Pule bez serwera SQL w programie Azure Synapse odczytają te wartości jako (zero), jeśli pierwszy dokument kolekcji ma dla tej samej właściwości wartość z `0` `numeric` typem danych.

* W przypadku brakujących kolumn należy spodziewać się różnych zachowań:
  * Pule platformy Spark w Azure Synapse będą reprezentować te kolumny jako `undefined` .
  * Pule bez serwera SQL w Azure Synapse będą reprezentować te kolumny jako `NULL` .

#### <a name="schema-representation"></a>Reprezentacja schematu

W magazynie analitycznym można korzystać z dwóch trybów reprezentacji schematu. W tych trybach zastosowano kompromisy między prostotą reprezentacji kolumnowej, obsługą schematów polimorficznych i prostotą środowiska zapytań:

* Dobrze zdefiniowana reprezentacja schematu
* Reprezentacja schematu pełnej wierności

> [!NOTE]
> W przypadku kont interfejsu API SQL (podstawowych) po włączeniu magazynu analitycznego domyślna reprezentacja schematu w magazynie analitycznym jest dobrze zdefiniowana. Natomiast w przypadku Azure Cosmos DB API dla kont bazy danych MongoDB domyślna reprezentacja schematu w magazynie analitycznym jest reprezentacją schematu pełnej wierności. Jeśli masz scenariusze wymagające innej reprezentacji schematu niż domyślna oferta dla każdego z tych interfejsów API, sproś się do zespołu [Azure Cosmos DB,](mailto:cosmosdbsynapselink@microsoft.com) aby go włączyć.

**Dobrze zdefiniowana reprezentacja schematu**

Dobrze zdefiniowana reprezentacja schematu tworzy prostą reprezentację tabelaryczne danych niezależny od schematu w magazynie transakcyjnym. Dobrze zdefiniowana reprezentacja schematu ma następujące zagadnienia:

* Właściwość zawsze ma ten sam typ dla wielu elementów.
* Zezwalamy tylko na 1 zmianę typu z wartości null na dowolny inny typ danych. Pierwsze wystąpienie o wartości innych niż null definiuje typ danych kolumny.

  * Na przykład `{"a":123} {"a": "str"}` program nie ma dobrze zdefiniowanego schematu, ponieważ czasami jest ciągiem, a czasami `"a"` liczbą. W takim przypadku magazyn analityczny rejestruje typ danych elementu jako typ danych w pierwszym występującym elementie w `"a"` `“a”` okresie istnienia kontenera. Dokument nadal będzie zawarty w magazynie analitycznym, ale elementy, w których typ danych `"a"` różni się, nie będą.
  
    Ten warunek nie ma zastosowania do właściwości o wartości null. Na przykład, `{"a":123} {"a":null}` jest nadal dobrze zdefiniowany.

* Typy tablic muszą zawierać jeden powtarzany typ.

  * Na przykład `{"a": ["str",12]}` nie jest dobrze zdefiniowanym schematem, ponieważ tablica zawiera kombinację liczb całkowitych i typów ciągów.

> [!NOTE]
> Jeśli magazyn Azure Cosmos DB analityczny jest zgodny z dobrze zdefiniowaną reprezentacją schematu, a określona powyżej specyfikacja jest naruszona przez niektóre elementy, te elementy nie zostaną uwzględnione w magazynie analitycznym.

* Należy oczekiwać różnych zachowań w odniesieniu do różnych typów w dobrze zdefiniowanym schemacie:
  * Pule platformy Spark Azure Synapse będą reprezentować te wartości jako `undefined` .
  * Bez serwerowe pule SQL w Azure Synapse będą reprezentować te wartości jako `NULL` .


**Reprezentacja schematu pełnej wierności**

Reprezentacja schematu pełnej wierności jest przeznaczona do obsługi pełnego zestawu schematów polimorficznych w danych operacyjnych, które nie są niezależne od schematów. W tej reprezentacji schematu żadne elementy nie są porzucane z magazynu analitycznego, nawet jeśli naruszono dobrze zdefiniowane ograniczenia schematu (które nie są polami o mieszanym typie danych ani mieszanymi tablicami typów danych).

Jest to osiągane przez przetłumaczenie właściwości liści danych operacyjnych do magazynu analitycznego z oddzielnymi kolumnami na podstawie typu danych wartości we właściwości . Nazwy właściwości liści są rozszerzane przy użyciu typów danych jako sufiksu w schemacie magazynu analitycznego, aby można było wysyłać zapytania bez niejednoznaczności.

Na przykład skorzystajmy z następującego przykładowego dokumentu w magazynie transakcyjnym:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

Właściwość `streetNo` liścia w zagnieżdżonych obiektach `address` będzie reprezentowana w schemacie magazynu analitycznego jako kolumna `address.object.streetNo.int32` . Typ danych jest dodawany jako sufiks do kolumny. W ten sposób, jeśli kolejny dokument zostanie dodany do magazynu transakcyjnego, gdzie wartość właściwości liścia to "123" (należy pamiętać, że jest to ciąg), schemat magazynu analitycznego automatycznie ewoluuje bez zmiany typu wcześniej napisanej `streetNo` kolumny. Nowa kolumna dodana do magazynu analitycznego, w której `address.object.streetNo.string` jest przechowywana ta wartość "123".

**Mapowanie typu danych na sufiks**

Oto mapa wszystkich typów danych właściwości i reprezentacji ich sufiksów w magazynie analitycznym:

|Oryginalny typ danych  |Sufiks  |Przykład  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Tablica | ".array" |    ["a", "b"]|
|Binarne | ".binary" |0|
|Wartość logiczna    | ".bool"   |Prawda|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Zero   | ".null"   | null|
|Ciąg|    ".string" | "ABC"|
|Znacznik czasu |    ".timestamp" |  Sygnatura czasowa (0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Dokument   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Ekonomiczne archiwizowanie danych historycznych

Warstwy danych odnoszą się do rozdzielania danych między infrastrukturami magazynu zoptymalizowanych pod kątem różnych scenariuszy. W ten sposób poprawisz ogólną wydajność i opłacalność stosu danych typu end-to-end. Dzięki magazynowi analitycznemu Azure Cosmos DB automatyczną obsługę warstw danych z magazynu transakcyjnego do magazynu analitycznego z różnymi układami danych. Magazyn analityczny zoptymalizowany pod kątem kosztów magazynowania w porównaniu z magazynem transakcyjnym umożliwia zachowanie znacznie dłuższych horyzontów danych operacyjnych na podstawie analizy historycznej.

Po włączeniu magazynu analitycznego, w zależności od potrzeb przechowywania danych obciążeń transakcyjnych, można skonfigurować właściwość "Transakcyjny czas wygaśnięcia magazynu (transakcyjny czas wygaśnięcia)", aby rekordy były automatycznie usuwane z magazynu transakcyjnego po upływie określonego czasu. Podobnie "Analityczny czas wygaśnięcia (analityczny czas wygaśnięcia)" umożliwia zarządzanie cyklem życia danych zachowywanych w magazynie analitycznym niezależnie od magazynu transakcyjnego. Włączając magazyn analityczny i konfigurując właściwości czasu wygaśnięcia, można bezproblemowo utworzyć warstwę i zdefiniować okres przechowywania danych dla dwóch magazynów.

### <a name="global-distribution"></a>Dystrybucja globalna

Jeśli masz globalnie dystrybuowane konto Azure Cosmos DB, po włączeniu magazynu analitycznego dla kontenera będzie on dostępny we wszystkich regionach tego konta.  Wszelkie zmiany danych operacyjnych są globalnie replikowane we wszystkich regionach. Zapytania analityczne można skutecznie uruchamiać względem najbliższej regionalnej kopii danych w Azure Cosmos DB.

### <a name="security"></a>Zabezpieczenia

Uwierzytelnianie w magazynie analitycznym jest takie samo jak magazyn transakcyjny dla danej bazy danych. Do uwierzytelniania można użyć kluczy podstawowych lub tylko do odczytu. Możesz użyć połączonej usługi w usłudze Synapse Studio, aby zapobiec wklejania kluczy Azure Cosmos DB w notesach platformy Spark. Dostęp do tej połączonej usługi jest dostępny dla wszystkich osób, które mają dostęp do obszaru roboczego.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Obsługa wielu Azure Synapse Analytics uruchomieniowych

Magazyn analityczny jest zoptymalizowany pod kątem zapewnienia skalowalności, elastyczności i wydajności dla obciążeń analitycznych bez żadnych zależności od czasów wykonywania obliczeń. Technologia magazynu jest samodzielnie zarządzana w celu optymalizacji obciążeń analitycznych bez konieczności ręcznego działania.

Oddzielenie systemu magazynu analitycznego od analitycznego systemu obliczeniowego umożliwia jednoczesne zapytania o dane w magazynie analitycznym usługi Azure Cosmos DB z różnych środowisk uruchomieniowych analizy obsługiwanych przez Azure Synapse Analytics. Obecnie usługa Azure Synapse Analytics obsługuje Apache Spark i bez serwera SQL z Azure Cosmos DB magazynem analitycznym.

> [!NOTE]
> Odczyt z magazynu analitycznego można odczytywać tylko przy Azure Synapse Analytics uruchomieniowym. Dane można zapisać z powrotem w magazynie transakcyjnym jako warstwę obsługującą.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Ceny

Magazyn analityczny jest zgodny z modelem cenowym opartym na zużyciu, w którym są naliczane opłaty za:

* Magazyn: ilość danych zachowywanych w magazynie analitycznym co miesiąc, w tym dane historyczne zdefiniowane przez analityczny czas wygaśnięcia.

* Analityczne operacje zapisu: w pełni zarządzana synchronizacja aktualizacji danych operacyjnych z magazynu analitycznego z magazynu transakcyjnego (automatyczna synchronizacja)

* Analityczne operacje odczytu: operacje odczytu wykonywane względem magazynu analitycznego z Azure Synapse Analytics Spark i bez serwera puli SQL.

Cennik magazynu analitycznego jest oddzielony od modelu cen magazynu transakcji. W magazynie analitycznym nie ma koncepcji aprowowanych procesorów. Zobacz [Azure Cosmos DB cennika](https://azure.microsoft.com/pricing/details/cosmos-db/), aby uzyskać szczegółowe informacje na temat modelu cen dla magazynu analitycznego.

Aby uzyskać wysokie oszacowanie kosztów w celu włączenia magazynu analitycznego w kontenerze usługi Azure Cosmos DB, możesz użyć planisty wydajności usługi [Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) i uzyskać oszacowanie kosztów magazynu analitycznego i operacji zapisu. Koszty operacji odczytu analitycznego zależą od charakterystyki obciążenia analitycznego, ale w ramach oszacowania wysokiego poziomu skanowanie 1 TB danych w magazynie analitycznym zwykle powoduje 130 000 analitycznych operacji odczytu i powoduje koszt 0,065 USD.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analityczny czas wygaśnięcia (TTL)

Analityczny czas wygaśnięcia wskazuje, jak długo dane mają być przechowywane w magazynie analitycznym dla kontenera. 

Jeśli magazyn analityczny jest włączony, wstawianie, aktualizacje i usuwanie danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego, niezależnie od konfiguracji transakcyjnego wygaśnięcia. Przechowywanie tych danych operacyjnych w magazynie analitycznym może być kontrolowane przez wartość analitycznego czasu wygaśnięcia na poziomie kontenera, jak określono poniżej:

Analityczny czas wygaśnięcia w kontenerze jest ustawiany przy użyciu `AnalyticalStoreTimeToLiveInSeconds` właściwości :

* Jeśli wartość jest ustawiona na "0", brakująca (lub ustawiona na wartość null): magazyn analityczny jest wyłączony i żadne dane nie są replikowane z magazynu transakcyjnego do magazynu analitycznego

* Jeśli wartość jest obecna, a wartość jest ustawiona na "-1": magazyn analityczny zachowuje wszystkie dane historyczne, niezależnie od przechowywania danych w magazynie transakcyjnym. To ustawienie wskazuje, że magazyn analityczny ma nieskończone przechowywanie danych operacyjnych

* Jeśli ta wartość jest obecna, a wartość jest ustawiona na liczbę dodatnią "n": elementy wygasną z magazynu analitycznego "n" sekund po ich ostatniej modyfikacji w magazynie transakcyjnym. Tego ustawienia można użyć, jeśli chcesz przechowywać dane operacyjne przez ograniczony czas w magazynie analitycznym, niezależnie od przechowywania danych w magazynie transakcyjnym

Oto niektóre ważne kwestie:

*   Po włączeniu magazynu analitycznego z wartością analitycznego TTL można go później zaktualizować do innej prawidłowej wartości. 
*   Transakcyjny czas wygaśnięcia można ustawić na poziomie kontenera lub elementu, jednak analityczny czas wygaśnięcia można obecnie ustawić tylko na poziomie kontenera.
*   Dłuższe przechowywanie danych operacyjnych w magazynie analitycznym można osiągnąć, ustawiając wartość analitycznego czasu wygaśnięcia >= transakcyjny czas wygaśnięcia na poziomie kontenera.
*   Magazyn analityczny można utworzyć tak, aby dublował magazyn transakcyjny, ustawiając analityczny czas wygaśnięcia = transakcyjny czas wygaśnięcia.

Po włączeniu magazynu analitycznego w kontenerze:

* W Azure Portal wartość domyślna opcji analitycznego TTL to -1. Możesz zmienić tę wartość na "n" sekund, przechodząc do ustawień kontenera w obszarze Eksplorator danych. 
 
* Z zestawu Azure SDK, programu PowerShell lub interfejsu wiersza polecenia opcję analitycznego wygaśnięcia można włączyć, ustawiając dla tej opcji opcję na -1 lub "n". 

Aby dowiedzieć się więcej, zobacz [jak skonfigurować analityczny czas wygaśnięcia w kontenerze.](configure-synapse-link.md#create-analytical-ttl)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Usługa Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link.md)

* [Rozpoczynanie pracy z usługą Azure Synapse Link dla usługi Azure Cosmos DB](configure-synapse-link.md)

* [Często zadawane pytania dotyczące usługi Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Przypadki użycia usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-use-cases.md)

---
title: Co to jest Azure Cosmos DB magazyn analityczny?
description: Dowiedz się więcej na temat Azure Cosmos DB transakcyjnych (opartych na wierszach) i analitycznych (opartych na kolumnach). Zalety magazynu analitycznego, wpływ na wydajność obciążeń o dużej skali oraz automatyczne synchronizowanie danych z magazynu transakcyjnego do magazynu analitycznego
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: bca4eb7f5f266a639916c0f8e520f025d259c39b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577363"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Co to jest Azure Cosmos DB magazyn analityczny?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Magazyn analityczny Azure Cosmos DB to w pełni izolowany magazyn kolumn służący do włączania analiz na dużą skalę na potrzeby danych operacyjnych w Azure Cosmos DB, bez wpływu na obciążenia transakcyjne. 

Magazyn transakcyjny Azure Cosmos DB to Schema-niezależny od i umożliwia iteracyjne wykonywanie iteracji w aplikacjach transakcyjnych bez konieczności rozwiązywania problemów ze schematem lub zarządzaniem indeksem. W przeciwieństwie do tego Azure Cosmos DB magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. W tym artykule opisano szczegółowo informacje na temat magazynu analitycznego.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Wyzwania związane z analizą danych operacyjnych na dużą skalę

Dane operacyjne dla wielomodelowego kontenera Azure Cosmos DB są przechowywane wewnętrznie w indeksowanym wierszu "magazyn transakcyjny". Format magazynu wierszy został zaprojektowany tak, aby umożliwiał szybkie odczyty transakcyjne i zapisy w kolejności czasu odpowiedzi w milisekundach i zapytaniach operacyjnych. Jeśli zestaw danych powiększa duże, złożone zapytania analityczne mogą być kosztowne pod względem alokowanej przepływności danych przechowywanych w tym formacie. Duże użycie zainicjowanej przepływności ma wpływ na wydajność obciążeń transakcyjnych używanych przez aplikacje i usługi w czasie rzeczywistym.

Tradycyjnie, aby analizować duże ilości danych, dane operacyjne są wyodrębniane z magazynu transakcyjnego Azure Cosmos DB i przechowywane w oddzielnej warstwie danych. Na przykład dane są przechowywane w magazynie danych lub w usłudze Data Lake w odpowiednim formacie. Te dane są później używane do analizy na dużą skalę i analizowane przy użyciu aparatu obliczeniowego, takiego jak klastry Apache Spark. Takie rozdzielenie warstwowych magazynów analitycznych i obliczeniowych z danych operacyjnych skutkuje dodatkowym opóźnieniem, ponieważ potoki ETL (wyodrębnianie, przekształcanie, ładowanie) są uruchamiane rzadziej, aby zminimalizować potencjalny wpływ obciążeń transakcyjnych.

Potoki ETL są również złożone w przypadku obsługi aktualizacji danych operacyjnych w porównaniu z obsługą tylko nowo wprowadzonych danych operacyjnych. 

## <a name="column-oriented-analytical-store"></a>Magazyn analityczny zorientowany na kolumny

Azure Cosmos DB magazyn analityczny rozwiązuje problemy z złożonością i opóźnieniem występujące w tradycyjnych potokach ETL. Magazyn analityczny Azure Cosmos DB może automatycznie synchronizować dane operacyjne w oddzielnym magazynie kolumn. Format magazynu kolumn jest odpowiedni dla zapytań analitycznych na dużą skalę, które mają być wykonywane w sposób zoptymalizowany, co poprawia opóźnienia takich zapytań.

Korzystając z linku Synapse platformy Azure, możesz teraz tworzyć rozwiązania No-ETL HTAP, bezpośrednio łącząc się z magazynem analitycznym Azure Cosmos DB z usługi Azure Synapse Analytics. Umożliwia ona uruchamianie w czasie niemal rzeczywistym analiz na dużą skalę na danych operacyjnych.

## <a name="features-of-analytical-store"></a>Funkcje magazynu analitycznego 

Po włączeniu magazynu analitycznego w kontenerze Azure Cosmos DB nowy magazyn kolumn jest tworzony wewnętrznie na podstawie danych operacyjnych w kontenerze. Ten magazyn kolumn jest utrwalany niezależnie od magazynu transakcyjnego zorientowanego na wiersze dla tego kontenera. Operacje wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynem analitycznym. Nie potrzebujesz kanału informacyjnego zmiany ani ETL do synchronizowania danych.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Magazyn kolumn dla obciążeń analitycznych dotyczących danych operacyjnych

Obciążenia analityczne zwykle obejmują agregacje i sekwencyjne skanowanie wybranych pól. Przechowywanie danych w kolejności najważniejszych kolumn — magazyn analityczny pozwala na Serializowanie grupy wartości dla każdego pola. Ten format zmniejsza liczbę operacji we/wy wymaganą do skanowania lub obliczania statystyk dla określonych pól. Znacznie skraca czas odpowiedzi na zapytania w przypadku skanowania w dużych zestawach danych. 

Na przykład jeśli tabele operacyjne mają następujący format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Przykładowa tabela operacyjna" border="false":::

Magazyn wierszy zachowuje powyższe dane w serializowanym formacie, na jeden wiersz na dysku. Ten format umożliwia szybsze operacje odczytu, zapisu i działania, takie jak "Zwróć informacje o Product1". Jednak w miarę wzrostu zestawu danych i, jeśli chcesz uruchomić złożone zapytania analityczne dotyczące danych, może to być kosztowne. Jeśli na przykład chcesz uzyskać "trendy sprzedaży dla produktu w kategorii o nazwie" sprzęt "w różnych jednostkach i miesiącach", musisz uruchomić złożone zapytanie. Duże skany w tym zestawie danych mogą być kosztowne w sensie zainicjowanej przepływności i mogą również wpływać na wydajność obciążeń transakcyjnych, które umożliwiają działanie aplikacji i usług w czasie rzeczywistym.

Magazyn analityczny, który jest magazynem kolumn, jest lepiej dostosowany do takich zapytań, ponieważ deserializacji podobne pola danych i zmniejsza liczbę operacji we/wy dysku.

Na poniższej ilustracji przedstawiono magazyn wierszy transakcyjnych i analitycznego magazynu kolumn w Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Magazyn wierszy transakcyjnych i magazyn kolumn analitycznych w Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Oddzielona wydajność obciążeń analitycznych

Nie ma to wpływu na wydajność obciążeń transakcyjnych ze względu na zapytania analityczne, ponieważ magazyn analityczny jest oddzielony od magazynu transakcyjnego.  Magazyn analityczny nie potrzebuje oddzielnych jednostek żądania (jednostek ru) do przydzielenia.

### <a name="auto-sync"></a>Autosynchronizacja

Automatyczna synchronizacja dotyczy w pełni zarządzanej funkcji Azure Cosmos DB, w której operacje wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego w czasie niemal rzeczywistym. Opóźnienie autosynchronizacji jest zwykle w ciągu 2 minut. W przypadku udostępnionej bazy danych przepływności o dużej liczbie kontenerów czas oczekiwania na synchronizację poszczególnych kontenerów może być większy i potrwać do 5 minut. Chcielibyśmy dowiedzieć się więcej o tym, jak to opóźnienie pasuje do Twoich scenariuszy. W tym celu skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Funkcja autosynchronizacji wraz z magazynem analitycznym zapewnia następujące korzyści:

### <a name="scalability--elasticity"></a>Elastyczność & skalowalności

Korzystając z partycjonowania poziomego, Azure Cosmos DB magazyn transakcyjny może elastycznie skalować magazyn i przepływność bez przestojów. Partycjonowanie poziome w magazynie transakcyjnym zapewnia skalowalność & elastyczność w ramach autosynchronizacji, aby upewnić się, że dane są synchronizowane z magazynem analitycznym niemal w czasie rzeczywistym. Synchronizacja danych odbywa się niezależnie od transakcyjnej przepływności ruchu, niezależnie od tego, czy jest to 1000 operacji/s czy 1 000 000 operacji/s, i nie ma wpływu na przepływność administracyjną w magazynie transakcyjnym. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatycznie Obsługuj aktualizacje schematu

Magazyn transakcyjny Azure Cosmos DB to Schema-niezależny od i umożliwia iteracyjne wykonywanie iteracji w aplikacjach transakcyjnych bez konieczności rozwiązywania problemów ze schematem lub zarządzaniem indeksem. W przeciwieństwie do tego Azure Cosmos DB magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji autosynchronizacji Program Azure Cosmos DB zarządza wnioskami o schemacie w porównaniu z najnowszymi aktualizacjami ze sklepu transakcyjnego.  Zarządza również reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych.

Gdy schemat zostanie rozwijający się, a nowe właściwości są dodawane z upływem czasu, magazyn analityczny automatycznie przedstawia schemat składający się ze zbiorów we wszystkich schematach historycznych w magazynie transakcyjnym.

#### <a name="schema-constraints"></a>Ograniczenia schematu

Poniższe ograniczenia dotyczą danych operacyjnych w Azure Cosmos DB po włączeniu magazynu analitycznego w celu poprawnego automatycznego wywnioskowania i reprezentowania schematu:

* Można mieć maksymalnie 1000 właściwości na dowolnym poziomie zagnieżdżenia schematu i maksymalnej głębokości zagnieżdżenia 127.
  * Tylko pierwsze właściwości 1000 są reprezentowane w magazynie analitycznym.
  * Tylko pierwsze 127 poziomów zagnieżdżonych są reprezentowane w magazynie analitycznym.

* W czasie, gdy dokumenty JSON (i Cosmos DB kolekcje/kontenery) są rozróżniane wielkością liter, magazyn analityczny nie jest.

  * **W tym samym dokumencie:** Nazwy właściwości na tym samym poziomie powinny być unikatowe w porównaniu z wielkością liter. Na przykład następujący dokument JSON ma "name" i "name" na tym samym poziomie. Chociaż jest to prawidłowy dokument JSON, nie spełnia on ograniczenia unikatowości, dlatego nie będzie w pełni reprezentowany w magazynie analitycznym. W tym przykładzie nazwy "name" i "name" są takie same w porównaniu z wielkością liter. `"Name": "fred"`Będzie reprezentowana tylko w magazynie analitycznym, ponieważ jest to pierwsze wystąpienie. I `"name": "john"` nie będą reprezentowane w ogóle.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **W różnych dokumentach:** Właściwości na tym samym poziomie i o tej samej nazwie, ale w różnych przypadkach, będą reprezentowane w tej samej kolumnie przy użyciu formatu nazwy pierwszego wystąpienia. Na przykład następujące dokumenty JSON mają `"Name"` i `"name"` na tym samym poziomie. Ponieważ pierwszy format dokumentu to `"Name"` , to oznacza, że będzie on używany do reprezentowania nazwy właściwości w magazynie analitycznym. Innymi słowy, nazwa kolumny w magazynie analitycznym będzie `"Name"` . Obie `"fred"` i `"john"` będą reprezentowane w `"Name"` kolumnie.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Pierwszy dokument kolekcji definiuje początkowy schemat magazynu analitycznego.
  * Właściwości na pierwszym poziomie dokumentu będą reprezentowane jako kolumny.
  * Dokumenty o większej liczbie właściwości niż początkowy schemat spowodują wygenerowanie nowych kolumn w magazynie analitycznym.
  * Nie można usunąć kolumn.
  * Usunięcie wszystkich dokumentów w kolekcji nie powoduje zresetowania schematu magazynu analitycznego.
  * Nie istnieje wersja schematu. Ostatnia wersja wywnioskowana z magazynu transakcyjnego jest wyświetlana w magazynie analitycznym.

* Obecnie firma Microsoft nie obsługuje nazw kolumn odczytanych przez usługę Azure Synapse Spark, które zawierają wartości puste (białe znaki).

* Oczekiwane jest inne zachowanie w odniesieniu do `NULL` wartości:
  * Pule Spark w usłudze Azure Synapse będą odczytywać te wartości jako 0 (zero).
  * Pule bezserwerowe programu SQL Server w usłudze Azure Synapse będą odczytywać te wartości jako `NULL` .

* Zaczekaj inne zachowanie w odniesieniu do brakujących kolumn:
  * Pule platformy Spark w usłudze Azure Synapse będą przedstawiać te kolumny jako `undefined` .
  * Pule bezserwerowe programu SQL Server w usłudze Azure Synapse będą przedstawiać te kolumny jako `NULL` .

#### <a name="schema-representation"></a>Reprezentacja schematu

W magazynie analitycznym można korzystać z dwóch trybów reprezentacji schematu. W tych trybach zastosowano kompromisy między prostotą reprezentacji kolumnowej, obsługą schematów polimorficznych i prostotą środowiska zapytań:

* Dobrze zdefiniowana reprezentacja schematu
* Reprezentacja schematu pełnej wierności

> [!NOTE]
> W przypadku kont interfejsu API SQL (Core), gdy jest włączony magazyn analityczny, domyślną reprezentację schematu w magazynie analitycznym jest dobrze zdefiniowana. W przypadku Azure Cosmos DB interfejsu API dla kont MongoDB domyślną reprezentację schematu w magazynie analitycznym jest przedstawiona w pełni wierność schematem. Jeśli istnieją scenariusze wymagające innej reprezentacji schematu niż domyślna oferta dla każdego z tych interfejsów API, skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) , aby go włączyć.

**Dobrze zdefiniowana reprezentacja schematu**

Dobrze zdefiniowana reprezentacja schematu tworzy prostą tabelaryczną reprezentację danych Schema-niezależny od w magazynie transakcyjnym. Dobrze zdefiniowana reprezentacja schematu ma następujące zagadnienia:

* Właściwość zawsze ma ten sam typ w wielu elementach.

  * Na przykład nie `{"a":123} {"a": "str"}` ma dobrze zdefiniowanego schematu, ponieważ `"a"` jest czasami ciągiem i czasami liczbą. W takim przypadku magazyn analityczny rejestruje typ danych `"a"` jako typ danych `“a”` w pierwszym elemencie w okresie istnienia kontenera. Dokument będzie nadal znajdować się w magazynie analitycznym, ale elementy, w których typ danych `"a"` różni się od siebie.
  
    Ten warunek nie ma zastosowania do właściwości o wartości null. Na przykład, `{"a":123} {"a":null}` jest nadal zdefiniowane.

* Typy tablic muszą zawierać pojedynczy powtórzony typ.

  * Na przykład `{"a": ["str",12]}` nie jest dobrze zdefiniowanym schematem, ponieważ tablica zawiera kombinację typów całkowitych i ciągów.

> [!NOTE]
> Jeśli Azure Cosmos DB magazyn analityczny jest zgodny z dobrze zdefiniowaną reprezentacją schematu, a powyższa specyfikacja została naruszona przez pewne elementy, te elementy nie zostaną uwzględnione w magazynie analitycznym.

**Reprezentacja schematu pełnej wierności**

Reprezentacja schematu pełnej wierności jest przeznaczona do obsługi pełnej szerokości schematów polimorficznych w danych operacyjnych schematu-niezależny od. W tej reprezentacji schematu żadne elementy nie są usuwane z magazynu analitycznego, nawet jeśli nie zostały naruszone prawidłowo zdefiniowane ograniczenia schematu (które nie są polami mieszanymi typu danych ani tablicami typu danych mieszanych).

Jest to osiągane poprzez przetłumaczenie właściwości liścia danych operacyjnych do magazynu analitycznego z różnymi kolumnami na podstawie typu danych wartości we właściwości. Nazwy właściwości liścia są rozszerzane o typy danych jako sufiks w schemacie magazynu analitycznego, aby można było wysyłać zapytania bez niejednoznaczności.

Załóżmy na przykład, że następujące przykładowe dokumenty są przechowywane w magazynie transakcyjnym:

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

Właściwość liścia `streetNo` w obiekcie zagnieżdżonym `address` będzie reprezentowana w schemacie magazynu analitycznego jako kolumna `address.object.streetNo.int32` . Typ danych jest dodawany jako sufiks do kolumny. W ten sposób, jeśli inny dokument zostanie dodany do magazynu transakcyjnego, w którym Właściwość liścia `streetNo` ma wartość "123" (należy zauważyć, że jest to ciąg), schemat magazynu analitycznego jest automatycznie rozwijany bez zmiany typu wcześniej zapisanej kolumny. Nowa kolumna dodana do magazynu analitycznego, w `address.object.streetNo.string` której jest przechowywana ta wartość "123".

**Typ danych na mapę sufiksów**

Oto mapa wszystkich typów danych właściwości i ich reprezentacje sufiksów w magazynie analitycznym:

|Typ danych oryginalnych  |Przedrostk  |Przykład  |
|---------|---------|---------|
| Double |  ". Float64" |    24,99|
| Tablica | ". Array" |    ["a", "b"]|
|Binarne | ". Binary" |0|
|Wartość logiczna    | ". bool"   |Prawda|
|Int32  | ". Int32"  |123|
|Int64  | ". Int64"  |255486129307|
|Zero   | ". null"   | null|
|Ciąg|    ". ciąg" | "ABC"|
|Znacznik czasu |    ". timestamp" |  Sygnatura czasowa (0, 0)|
|DateTime   |". Date"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". objectId"    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Dokument   |". Object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Ekonomiczne archiwizowanie danych historycznych

Obsługa warstw danych dotyczy rozdzielania danych między infrastrukturami magazynu zoptymalizowanymi pod kątem różnych scenariuszy. W efekcie zwiększenie ogólnej wydajności i efektywności kosztu kompleksowego stosu danych. W przypadku magazynu analitycznego Azure Cosmos DB obsługuje automatyczne warstwy danych ze sklepu transakcyjnego do magazynu analitycznego z różnymi układami danych. Dzięki magazynowi analitycznemu zoptymalizowanemu pod względem kosztów magazynu w porównaniu z magazynem transakcyjnym można zachować znacznie dłuższy Horizons danych operacyjnych na potrzeby analizy historycznej.

Po włączeniu magazynu analitycznego, w zależności od potrzeb przechowywania danych w ramach obciążeń transakcyjnych, można skonfigurować właściwość "czas magazynu transakcyjnego na żywo" (transakcyjna wartość czasu wygaśnięcia), aby rekordy były automatycznie usuwane ze sklepu transakcyjnego po upływie określonego czasu. Analogicznie "czas trwania magazynu analitycznego (analityczne TTL)" pozwala zarządzać cyklem życia danych przechowywanych w magazynie analitycznym niezależnym od magazynu transakcyjnego. Włączając magazyn analityczny i konfigurując właściwości czasu wygaśnięcia, można bezproblemowo warstwować i definiować okres przechowywania danych dla dwóch magazynów.

### <a name="global-distribution"></a>Dystrybucja globalna

Jeśli masz konto Azure Cosmos DB dystrybuowane globalnie, po włączeniu magazynu analitycznego dla kontenera będzie ono dostępne we wszystkich regionach tego konta.  Wszelkie zmiany danych operacyjnych są replikowane globalnie we wszystkich regionach. Możesz efektywnie uruchamiać zapytania analityczne w stosunku do najbliższej kopii regionalnej danych w Azure Cosmos DB.

### <a name="security"></a>Zabezpieczenia

Uwierzytelnianie za pomocą magazynu analitycznego jest takie samo jak w przypadku magazynu transakcyjnego dla danej bazy danych. Do uwierzytelniania można użyć kluczy podstawowych lub tylko do odczytu. Możesz użyć połączonej usługi w programie Synapse Studio, aby zapobiec wklejaniu kluczy Azure Cosmos DB w notesach platformy Spark. Dostęp do tej połączonej usługi jest dostępny dla każdego, kto ma dostęp do obszaru roboczego.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Obsługa wielu środowisk uruchomieniowych usługi Azure Synapse Analytics

Magazyn analityczny jest zoptymalizowany pod kątem zapewnienia skalowalności, elastyczności i wydajności obciążeń analitycznych bez żadnej zależności od czasu wykonywania obliczeń. Technologia magazynowania jest samozarządzana w celu optymalizacji obciążeń związanych z analizą bez ręcznych działań.

Poprzez oddzielenie systemu magazynu analitycznego od systemu obliczeń analitycznych dane w Azure Cosmos DB magazyn analityczny mogą być wysyłane jednocześnie z różnych środowisk uruchomieniowych analizy obsługiwanych przez usługę Azure Synapse Analytics. Obecnie usługa Azure Synapse Analytics obsługuje pulę SQL Apache Spark i bezserwerową z magazynem analitycznym Azure Cosmos DB.

> [!NOTE]
> Można odczytywać tylko z magazynu analitycznego za pomocą usługi Azure Synapse Analytics. Dane można zapisać z powrotem do magazynu transakcyjnego jako obsługujący warstwę.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Wpisaną

Magazyn analityczny jest zgodny z modelem cen opartym na zużyciu, w którym opłata jest naliczana za:

* Magazyn: ilość danych przechowywanych w magazynie analitycznym co miesiąc, łącznie z danymi historycznymi zdefiniowanymi przez analityczny czas wygaśnięcia.

* Operacje zapisu analitycznego: w pełni zarządzana synchronizacja aktualizacji danych operacyjnych z magazynem analitycznym ze sklepu transakcyjnego (Autosynchronizacja)

* Operacje odczytu analitycznego: operacje odczytu wykonywane w odniesieniu do magazynu analitycznego z puli Spark usługi Azure Synapse Analytics i bezserwerowej puli SQL.

Cennik sklepu analitycznego jest oddzielony od modelu cen magazynu transakcji. W magazynie analitycznym nie ma koncepcji jednostek rud. Aby uzyskać szczegółowe informacje o modelu cen dla magazynu analitycznego, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Aby uzyskać szacunkowy koszt wysokiego poziomu, aby umożliwić magazyn analityczny w kontenerze Azure Cosmos DB, można użyć [terminarza wydajności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) i uzyskać szacunkowy koszt magazynu analitycznego i operacji zapisu. Koszty operacji odczytu analitycznego są zależne od cech obciążenia analizy, ale w przypadku oszacowania wysokiego poziomu skanowanie 1 TB danych w magazynie analitycznym zwykle skutkuje wynikiem operacji odczytu analitycznego 130 000, a wynikiem jest koszt $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Czas trwania analizy (TTL)

Analityczny czas wygaśnięcia wskazuje, jak długo dane mają być przechowywane w magazynie analitycznym dla kontenera. 

Jeśli magazyn analityczny jest włączony, operacje wstawiania, aktualizacji i usuwania danych operacyjnych są automatycznie synchronizowane z magazynu transakcyjnego do magazynu analitycznego, niezależnie od transakcyjnej konfiguracji czasu wygaśnięcia (TTL). Przechowywanie danych operacyjnych w magazynie analitycznym może być kontrolowane przez analityczną wartość czasu wygaśnięcia na poziomie kontenera, jak określono poniżej:

Analityczny czas wygaśnięcia dla kontenera jest ustawiany przy użyciu `AnalyticalStoreTimeToLiveInSeconds` Właściwości:

* Jeśli wartość jest równa "0", brak (lub ma wartość null): Magazyn analityczny jest wyłączony i żadne dane nie są replikowane z magazynu transakcyjnego do magazynu analitycznego

* Jeśli jest obecny i wartość jest równa "-1": Magazyn analityczny zachowuje wszystkie dane historyczne, niezależnie od przechowywania danych w magazynie transakcyjnym. To ustawienie wskazuje, że magazyn analityczny ma nieskończone przechowywanie danych operacyjnych

* Jeśli jest obecny, a wartość jest równa liczbie dodatniej "n": elementy wygaśnie z magazynu analitycznego "n" s po ostatniej modyfikacji w magazynie transakcyjnym. Tego ustawienia można użyć, jeśli chcesz przechowywać dane operacyjne przez ograniczony czas w magazynie analitycznym, niezależnie od przechowywania danych w magazynie transakcyjnym.

Oto niektóre ważne kwestie:

*   Gdy magazyn analityczny jest włączony z wartością analityczną TTL, można go później zaktualizować do innej prawidłowej wartości. 
*   Gdy transakcyjny czas wygaśnięcia można ustawić na poziomie kontenera lub elementu, analityczny czas TTL można ustawić tylko na poziomie kontenera.
*   Możesz uzyskać więcej przechowywania danych operacyjnych w magazynie analitycznym, ustawiając wartości w polu analityczny czas wygaśnięcia >= transakcyjna wartość czasu wygaśnięcia na poziomie kontenera.
*   Magazyn analityczny może być tworzony w celu dublowania magazynu transakcyjnego przez ustawienie wartości czas wygaśnięcia (TTL).

Po włączeniu magazynu analitycznego w kontenerze:

* W Azure Portal opcja analityczny czas wygaśnięcia jest ustawiona na wartość domyślną-1. Można zmienić tę wartość na "n" s, przechodząc do pozycji Ustawienia kontenera w obszarze Eksplorator danych. 
 
* Korzystając z zestawu Azure SDK lub programu PowerShell lub interfejsu wiersza polecenia, można włączyć funkcję analitycznego czasu wygaśnięcia, ustawiając ją na wartość-1 lub "n". 

Aby dowiedzieć się więcej, zobacz [jak skonfigurować analityczny czas wygaśnięcia dla kontenera](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Usługa Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link.md)

* [Rozpoczynanie pracy z usługą Azure Synapse Link dla usługi Azure Cosmos DB](configure-synapse-link.md)

* [Często zadawane pytania dotyczące usługi Synapse Link dla usługi Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Przypadki użycia usługi Azure Synapse Link dla usługi Azure Cosmos DB](synapse-link-use-cases.md)

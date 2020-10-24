---
title: Typowe przypadki użycia i scenariusze dla Azure Cosmos DB
description: 'Dowiedz się więcej na temat pięciu najważniejszych przypadków użycia dla Azure Cosmos DB: zawartości wygenerowanej przez użytkownika, rejestrowania zdarzeń, danych katalogu, danych preferencji użytkownika i Internet rzeczy (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: b8d74f9da62b0f5a596c811efa9f93b55c99f1d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476352"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Typowe przypadki użycia usługi Azure Cosmos DB

Ten artykuł zawiera omówienie kilku typowych przypadków użycia Azure Cosmos DB.  Zalecenia przedstawione w tym artykule stanowią punkt wyjścia podczas opracowywania aplikacji przy użyciu Cosmos DB.

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania: 

* Jakie są typowe przypadki użycia dla Azure Cosmos DB?
* Jakie korzyści wynikają z używania Azure Cosmos DB w przypadku aplikacji sieci sprzedaży?
* Jakie korzyści wynikają z używania Azure Cosmos DB jako magazynu danych dla systemów Internet rzeczy (IoT)?
* Jakie korzyści wynikają z używania Azure Cosmos DB aplikacji internetowych i mobilnych?

## <a name="introduction"></a>Wprowadzenie

[Azure Cosmos DB](../cosmos-db/introduction.md) to Microsoft Fast NoSQL Database z otwartymi interfejsami API dla dowolnej skali. Usługa została zaprojektowana tak, aby klienci mogli elastycznie (i niezależnie) skalować przepływność i magazyn w dowolnej liczbie regionów geograficznych. Azure Cosmos DB to pierwsza globalnie dystrybuowana usługa bazy danych na rynku, aby zaoferować kompleksowe [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/cosmos-db/) obejmujące przepływność, opóźnienia, dostępność i spójność.

Azure Cosmos DB to globalnie dystrybuowana, wielomodelowa baza danych, która jest używana w wielu różnych aplikacjach i przypadkach użycia. Jest to dobry wybór dla wszystkich aplikacji [bezserwerowych](https://azure.com/serverless) , które potrzebują czasu odpowiedzi o niskiej kolejności (w milisekundach) i muszą być skalowane szybko i globalnie. Obsługuje ona wiele modeli danych (klucz-wartość, dokumenty, wykresy i kolumnowy) i wiele interfejsów API na potrzeby dostępu do danych, w [Azure Cosmos DB tym interfejs API usługi MongoDB](mongodb-introduction.md), [interfejs API SQL](./introduction.md), [interfejs API Gremlin](graph-introduction.md), interfejs [API tabel](table-introduction.md) natywnie i w sposób rozszerzalny. 

Poniżej przedstawiono niektóre atrybuty Azure Cosmos DB, które są odpowiednie dla aplikacji o wysokiej wydajności z globalnym ambitnego.

* Azure Cosmos DB natywnie Partycjonowanie danych pod kątem wysokiej dostępności i skalowalności. Azure Cosmos DB oferuje 99,99% gwarancji dostępności, przepływności, małych opóźnień i spójności na wszystkich kontach w jednym regionie, a wszystkie konta wieloregionowe z niską spójnością i 99,999% dostępności odczytu na wszystkich wieloregionowych kontach baz danych.
* Azure Cosmos DB ma magazyn z dyskiem SSD z niską opóźnieniami czasu odpowiedzi.
* Azure Cosmos DB obsługa poziomów spójności, takich jak ostateczna, spójny prefiks, sesja i ograniczone — nieaktualność, pozwala na zapewnienie pełnej elastyczności i niskiego współczynnika wydajności. Żadna usługa bazy danych nie oferuje większej elastyczności, jak Azure Cosmos DB w poziomie spójności. 
* Azure Cosmos DB ma elastyczny model cen przyjazny dla danych, który polega niezależnie na magazynowaniu i przepływności.
* Zastrzeżony model przepływności Azure Cosmos DB umożliwia określenie liczby operacji odczytu/zapisu zamiast użycia procesora CPU/pamięci/liczby IOPs dla bazowego sprzętu.
* Projekt Azure Cosmos DB umożliwia skalowanie do dużych ilości żądań w kolejności bln żądań dziennie.

Te atrybuty są korzystne w aplikacjach sieci Web, mobilnych, gier i IoT, które wymagają niskich czasów odpowiedzi i muszą obsługiwać ogromne ilości operacji odczytu i zapisu.

## <a name="iot-and-telematics"></a>IoT i telematyka

Przypadki użycia IoT często udostępniają pewne wzorce w sposobie pozyskiwania, przetwarzania i przechowywania danych.  Najpierw te systemy muszą pozyskiwać dane z czujników urządzeń różnych ustawień regionalnych. Następnie te systemy przetwarzają i analizują dane przesyłane strumieniowo w celu uzyskania szczegółowych informacji w czasie rzeczywistym. Dane są następnie archiwizowane w chłodnym magazynie na potrzeby analizy wsadowej. Microsoft Azure oferuje bogate usługi, które mogą być stosowane do przypadków użycia IoT, w tym Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, centrum powiadomień platformy Azure, Azure Machine Learning, Azure HDInsight i Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

Duże ilości danych można pozyskać w usłudze Azure Event Hubs, ponieważ oferuje ona wysoką przepływność pozyskiwania danych z małymi opóźnieniami. Dane pozyskiwane, które muszą zostać przetworzone w celu wglądu w dane w czasie rzeczywistym, można doAzure Stream Analytics do analizy w czasie rzeczywistym. Dane można ładować do Azure Cosmos DB na potrzeby zapytań ad hoc. Gdy dane zostaną załadowane do Azure Cosmos DB, dane są gotowe do zapytania. Ponadto nowe dane i zmiany w istniejących danych mogą być odczytywane ze źródła zmian. Kanał informacyjny zmiany jest trwały, Dołącz tylko dziennik, który przechowuje zmiany w kontenerach Cosmos w kolejności sekwencyjnej. Wszystkie dane i tylko zmiany danych w Azure Cosmos DB mogą być używane jako dane referencyjne w ramach analizy w czasie rzeczywistym. Ponadto dane można dodatkowo modyfikować i przetwarzać, łącząc Azure Cosmos DB dane z usługą HDInsight na potrzeby zadań związanych z świniami, Hive lub map/zmniejszania.  Ulepszone dane są następnie ładowane z powrotem do Azure Cosmos DB na potrzeby raportowania.   

Przykładowe rozwiązanie IoT wykorzystujące Azure Cosmos DB, EventHubs i burzę można znaleźć w [repozytorium HDInsight-burz-Samples w witrynie GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Aby uzyskać więcej informacji na temat ofert platformy Azure dla IoT, zobacz [Tworzenie Internetu rzeczy](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Sprzedaż detaliczna i marketing
Azure Cosmos DB jest szeroko stosowana we własnych platformach handlu elektronicznego firmy Microsoft, w których jest uruchomiony Sklep Windows i usługa XBox Live. Jest on również używany w handlu detalicznym do przechowywania danych wykazu i określania źródła zdarzeń w potokach przetwarzania zamówień.

Scenariusze użycia danych wykazu obejmują przechowywanie i wykonywanie zapytań dotyczących zestawu atrybutów dla jednostek, takich jak osoby, miejsca i produkty. Przykładami danych wykazu są konta użytkowników, katalogi produktów, rejestry urządzeń IoT i systemy BOM. Atrybuty tych danych mogą się różnić i mogą ulec zmianie w czasie, aby dopasować wymagania aplikacji.

Rozważmy przykład katalogu produktów dla dostawcy części motoryzacyjnych. Każda część może mieć własne atrybuty oprócz wspólnych atrybutów, które są udostępniane przez wszystkie części. Ponadto atrybuty dla określonej części mogą zmieniać następujący rok po wydaniu nowego modelu. Azure Cosmos DB obsługuje elastyczne schematy i dane hierarchiczne, więc jest to dobrze dopasowane do przechowywania danych katalogu produktów.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

Azure Cosmos DB jest często używana w przypadku określania źródła zdarzeń do architektur opartych na zdarzeniach, przy użyciu funkcji [źródła zmian](change-feed.md) . Kanał informacyjny zmiany zapewnia mikrousługom podrzędnym możliwość niezawodnego i przyrostowego odczytu operacji wstawiania i aktualizacji (na przykład wydarzeń związanych z kolejnością) do Azure Cosmos DB. Ta funkcja może być używana w celu zapewnienia trwałego magazynu zdarzeń jako brokera komunikatów dla zdarzeń zmieniania stanu i przepływu pracy przetwarzania kolejności dysków między wieloma mikrousługami (które można zaimplementować jako [bezserwerowe Azure Functions](https://azure.com/serverless)).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

Ponadto dane przechowywane w Azure Cosmos DB mogą być zintegrowane z usługą HDInsight na potrzeby analizy danych Big Data za pośrednictwem Apache Spark zadań. Aby uzyskać szczegółowe informacje na temat łącznika Spark dla Azure Cosmos DB, zobacz [Uruchamianie zadania Spark za pomocą Cosmos DB i usługi HDInsight](spark-connector.md).

## <a name="gaming"></a>Gry
Warstwa bazy danych jest kluczowym elementem składowym aplikacji gier. Współczesne gry wykonują przetwarzanie graficzne na klientach mobilnych/konsolowych, ale to technologia chmury dostarcza odpowiednio dostosowaną i spersonalizowaną zawartość, taką jak statystyki uzyskane podczas gry, zestawienia najlepszych wyników czy integracja z mediami społecznościowymi. Gry często wymagają opóźnień pojedynczej milisekund dla operacji odczytu i zapisu, aby zapewnić zaangażowanie w grę. Baza danych w przypadku gier musi być szybka i musi być w stanie obsłużyć ogromne ilości żądań podczas uruchomień nowych gier i aktualizacji funkcji.

Azure Cosmos DB jest używany przez gry, takie jak pochodzenie przez [nie martwe się: Brak gruntów człowieku](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) w [następnych grach](https://www.nextgames.com/)i [Halo 5: opiekunów](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB oferuje deweloperom gier następujące korzyści:

* Azure Cosmos DB pozwala na skalowanie w górę lub w dół wydajności elastycznie. Dzięki temu gry mogą obsługiwać aktualizowanie profilu i statystyk z dziesiątek do milionów jednoczesnych graczy, wykonując jedno wywołanie interfejsu API.
* Azure Cosmos DB obsługuje milisekundy odczytów i zapisów, które pomagają uniknąć jakichkolwiek spowolnienia podczas gry.
* Automatyczne indeksowanie Azure Cosmos DB umożliwia filtrowanie wielu różnych właściwości w czasie rzeczywistym, na przykład lokalizowanie graczy według wewnętrznych identyfikatorów odtwarzaczy lub ich GameCenter, Facebook, Google ID lub zapytań opartych na członkostwie w odtwarzaczu w Guild. Jest to możliwe bez tworzenia złożonej infrastruktury indeksowania lub fragmentowania.
* Funkcje społecznościowe, w tym wiadomości czatu z rozmowy w grach, członkostwo w programie Player Guild, zakończyły się wyzwania, rankingi o wysokiej jakości i wykresy społecznościowe, są łatwiejsze do wdrożenia przy użyciu elastycznego schematu.
* Azure Cosmos DB jako zarządzana platforma jako usługa (PaaS) wymagała minimalnej instalacji i zarządzania, aby umożliwić szybką iterację i skrócenie czasu wprowadzenia na rynek.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

## <a name="web-and-mobile-applications"></a>Aplikacje internetowe i mobilne
Azure Cosmos DB jest często używana w aplikacjach internetowych i mobilnych. Dobrze nadaje się do modelowania interakcji społecznościowych, integracji z usługami innych firm oraz do tworzenia bogatych, spersonalizowanych środowisk. Zestawy SDK Cosmos DB mogą być używane do tworzenia rozbudowanych aplikacji dla systemów iOS i Android przy użyciu popularnej [platformy Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplikacje społecznościowe
Typowym zastosowaniem Azure Cosmos DB jest przechowywanie i wykonywanie zapytań dotyczących zawartości wygenerowanej przez użytkownika (UGC) dla aplikacji sieci Web, urządzeń przenośnych i mediów społecznościowych. Przykładami UGC są sesje rozmowy, Tweety, wpisy w blogu, oceny i komentarze. Często UGC w aplikacjach mediów społecznościowych to mieszanka tekstu, właściwości, tagów i relacji, które nie są ograniczone przez sztywną strukturę. Zawartość, taka jak rozmowy, komentarze i wpisy, może być przechowywana w Cosmos DB bez konieczności przekształceń ani złożonego obiektu do warstw mapowania relacyjnego.  Właściwości danych mogą być łatwo dodawane lub modyfikowane w celu dopasowania do wymagań, gdy deweloperzy przechodzą przez kod aplikacji, a tym samym promowanie szybkiego rozwoju.  

Aplikacje, które integrują się z sieciami społecznościowymi innych firm, muszą reagować na zmiany schematów z tych sieci. Ponieważ dane są automatycznie indeksowane domyślnie w Cosmos DB, dane są gotowe do zapytania w dowolnym momencie. W związku z tym te aplikacje mają elastyczność do pobierania projekcji zgodnie z ich potrzebami.

Wiele aplikacji społecznościowych działa na skalę globalną i może wykazywać nieprzewidywalne wzorce użycia. Elastyczność skalowania magazynu danych jest niezbędna, ponieważ warstwa aplikacji jest skalowana w celu dopasowania do zapotrzebowania na użycie.  Możesz skalować w poziomie przez dodanie dodatkowych partycji danych w ramach konta Cosmos DB.  Ponadto można również utworzyć dodatkowe konta Cosmos DB w wielu regionach. Aby uzyskać dostęp do Cosmos DB regionu usługi, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

### <a name="personalization"></a>Personalizacja
Obecnie, nowoczesne aplikacje są dostarczane z złożonymi widokami i środowiskami. Są one zazwyczaj dynamiczne, zbiorowe względem preferencji użytkownika lub nastrój i potrzeby znakowania. W związku z tym aplikacje muszą mieć możliwość skutecznego pobierania spersonalizowanych ustawień w celu szybkiego renderowania elementów interfejsu użytkownika i środowiska. 

Format JSON, obsługiwany przez Cosmos DB, to efektywny format do reprezentowania danych układu interfejsu użytkownika, ponieważ nie jest on tylko lekki, ale również może być łatwo interpretowany przez JavaScript. Cosmos DB oferuje poziomy spójności możliwość dostosowania, które umożliwiają szybkie odczyty z zapisami o małym opóźnieniu. W związku z tym przechowywanie danych układu interfejsu użytkownika z uwzględnieniem spersonalizowanych ustawień jako dokumentów JSON w Cosmos DB jest skutecznym sposobem na uzyskanie tych danych w sieci.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Architektura referencyjna Azure Cosmos DB IoT" border="false":::

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z usługą Azure Cosmos DB, Skorzystaj z naszego [przewodnika Szybki Start](create-sql-api-dotnet.md), który przeprowadzi Cię przez proces tworzenia konta i wprowadzenie do Cosmos DB.

* Jeśli chcesz dowiedzieć się więcej o klientach przy użyciu Azure Cosmos DB, zobacz stronę [analizy przypadków klientów](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
---
title: 'Wzorzec projektowy Azure Cosmos DB: aplikacje multimedialne społeczności'
description: Dowiedz się więcej na temat wzorca projektowego dla sieci społecznościowych, wykorzystując elastyczność magazynu Azure Cosmos DB i innych usług platformy Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 329c4b40f11b36de80581d4a1396813bc8de5c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010320"
---
# <a name="going-social-with-azure-cosmos-db"></a>Przechodzenie do społeczności Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Żyjące w wysoce niepołączonym świecie polega na tym, że w pewnym momencie Life staje się częścią **sieci społecznościowej**. Sieci społecznościowe umożliwiają kontaktowanie się ze znajomymi, współpracownikami i rodziną, a czasami udostępnianie pasją użytkownikom z typowymi zainteresowaniami.

Jak inżynierowie lub deweloperzy, prawdopodobnie zastanawiasz się, jak te sieci przechowują i współdziałają z danymi. Możesz również mieć jeszcze zadanie tworzenia lub projektowania nowej sieci społecznościowej dla określonego rynku. To jest ważne pytanie: jak są przechowywane wszystkie te dane?

Załóżmy, że tworzysz nową i Shiny sieć społecznościową, w której użytkownicy mogą publikować artykuły z pokrewnymi nośnikami, takimi jak obrazy, filmy wideo, a nawet muzykę. Użytkownicy mogą komentować wpisy i dawać punkty klasyfikacji. Na stronie docelowej głównej witryny sieci Web będą widoczne źródła danych, które użytkownicy zobaczą i z którymi będą korzystać. Ta metoda nie jest w pierwszej kolejności skomplikowana, ale na potrzeby uproszczenia Zatrzymajmy. (Można oddzielać się do niestandardowych źródeł użytkowników, których dotyczą relacje, ale wykraczających poza cel tego artykułu).

W jaki sposób można przechowywać te dane i gdzie?

Być może masz doświadczenie w bazach danych SQL lub masz koncepcję [modelowania danych relacyjnych](https://en.wikipedia.org/wiki/Relational_model). Możesz rozpocząć rysowanie w następujący sposób:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="Diagram ilustrujący względny model relacyjny" border="false":::

Doskonale znormalizowana i łatwa struktura danych... to nie skalowanie.

Nie otrzymuję niewłaściwych informacji, pracowałem nad bazami danych SQL. Są one wspaniałe, ale podobnie jak w przypadku wszystkich wzorców, rozwiązań i platformy oprogramowania, nie są idealnym rozwiązaniem dla każdego scenariusza.

Dlaczego w tym scenariuszu nie jest najlepszy wybór w języku SQL? Przyjrzyjmy się strukturze pojedynczego wpisu. Jeśli chcę pokazać wpis w witrynie sieci Web lub aplikacji, muszę wykonać zapytanie z... przez przyłączenie ośmiu tabel (!) tylko do wyświetlania jednego pojedynczego wpisu. Teraz Zanotuj strumień ogłoszeń, które są dynamicznie ładowane i wyświetlane na ekranie, i możesz zobaczyć, gdzie się znajdują.

Możesz użyć nieolbrzymiego wystąpienia SQL z wystarczającą ilością mocy, aby rozwiązać tysiące zapytań z wieloma sprzężeniami w celu obsłużenia zawartości. Ale dlaczego warto, gdy istnieje prostsze rozwiązanie?

## <a name="the-nosql-road"></a>NoSQL Road

W tym artykule opisano sposób modelowania danych platformy społecznościowej przy użyciu usługi Azure NoSQL [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) Database. Informuje również, jak używać innych funkcji Azure Cosmos DB, takich jak [interfejs API Gremlin](../cosmos-db/graph-introduction.md). Przy użyciu podejścia [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , zapisywania danych w formacie JSON i stosowania [denormalizacji](https://en.wikipedia.org/wiki/Denormalization)wcześniej skomplikowany wpis może być przekształcony w jeden [dokument](https://en.wikipedia.org/wiki/Document-oriented_database):

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

Może to być jedna kwerenda i bez sprzężeń. To zapytanie jest bardzo proste i proste, a i nie jest dostępne, ale wymaga mniejszej ilości zasobów, aby osiągnąć lepszy wynik.

Azure Cosmos DB upewnij się, że wszystkie właściwości są indeksowane przy użyciu automatycznego indeksowania. Automatyczne indeksowanie można nawet [dostosować](index-policy.md). Podejście bez schematu pozwala nam przechowywać dokumenty z różnymi strukturami dynamicznymi. Czy na pewno chcesz, aby wpisy miały listę kategorii lub hasztagów skojarzonych z nimi? Cosmos DB będzie obsługiwać nowe dokumenty z dodanymi atrybutami bez dodatkowej pracy wymaganej przez nas.

Komentarze na wpisie mogą być traktowane jako inne wpisy z właściwością nadrzędną. (Ta metoda upraszcza mapowanie obiektu).

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

Wszystkie interakcje społecznościowe mogą być przechowywane na osobnym obiekcie jako liczniki:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

Tworzenie źródeł danych jest kwestią tworzenia dokumentów, które mogą zawierać listę identyfikatorów wpisów z określoną kolejnością istotności:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

Można utworzyć "najnowszy" strumień z wpisami uporządkowanymi według daty utworzenia. Możesz też mieć strumień "okienko" z tymi wpisami o większej liczbie polubień w ciągu ostatnich 24 godzin. Można nawet zaimplementować niestandardowy strumień dla każdego użytkownika na podstawie logiki, takiej jak obserwatorzy i zainteresowania. Nadal będzie to lista wpisów. Jest to kwestia, w jaki sposób można kompilować te listy, ale wydajność odczytu pozostaje niezakłócona. Po uzyskaniu jednej z tych list możesz wydać pojedyncze zapytanie, aby Cosmos DB za pomocą [słowa kluczowego in w](sql-query-keywords.md#in) celu pobrania stron wpisów w danym momencie.

Strumienie kanałów informacyjnych można skompilować przy użyciu procesów w tle [platformy Azure App Services](https://azure.microsoft.com/services/app-service/) : [Zadania WebJob](../app-service/webjobs-create.md). Po utworzeniu wpisu przetwarzanie w tle może zostać wyzwolone za pomocą [kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md) [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i zadań WebJob WYZWALANYCH przy użyciu [zestawu SDK usługi Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki), implementując propagację w obrębie strumieni na podstawie własnej logiki niestandardowej.

Punkty i polubienia na wpisie mogą być przetwarzane w sposób odroczony za pomocą tej samej techniki, aby utworzyć ostatecznie spójne środowisko.

Obserwatorzy są trickier. Cosmos DB ma limit rozmiaru dokumentu, a odczyt/zapis dużych dokumentów może mieć wpływ na skalowalność aplikacji. Możesz zastanowić się nad przechowywaniem obserwatorów jako dokumentu w tej strukturze:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

Ta struktura może być poprawna dla użytkownika, który ma kilka tysięcy obserwatorów. Jeśli niektóre osobistości przyłączają się do rang, jednak takie podejście będzie prowadzić do dużego rozmiaru dokumentu i ostatecznie osiągnie limit rozmiaru dokumentu.

Aby rozwiązać ten problem, można użyć podejścia mieszanego. W ramach dokumentu statystyki użytkownika można przechowywać liczbę obserwatorów:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

Możesz przechowywać rzeczywisty Graf obserwatorów przy użyciu Azure Cosmos DB [interfejsu API Gremlin](../cosmos-db/graph-introduction.md) do tworzenia [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) dla każdego użytkownika i [krawędzi](http://mathworld.wolfram.com/GraphEdge.html) , które utrzymują relacje "A-po-B". Za pomocą interfejsu API Gremlin można uzyskać obserwatorów określonego użytkownika i utworzyć bardziej złożone zapytania, aby zasugerować wspólne osoby. Jeśli dodasz do grafu Kategorie zawartości, które lubię lub cieszą się, możesz zacząć korzystać z funkcji odtwarzania, które obejmują funkcję inteligentnego odnajdywania zawartości, sugerując zawartość, do której Ci się odnoszą, lub znalezienie osób, które mogą być często bardzo popularne.

Dokument statystyk użytkownika może być nadal używany do tworzenia kart w interfejsie użytkownika lub szybkich podglądach profilów.

## <a name="the-ladder-pattern-and-data-duplication"></a>Wzorzec "drabina" i duplikacja danych

Jak zauważono w dokumencie JSON, który odwołuje się do wpisu, istnieje wiele wystąpień użytkownika. A ty masz prawo, te duplikaty oznaczają, że informacje opisujące użytkownika, które zostały określone przez tę denormalizację, mogą znajdować się w więcej niż jednym miejscu.

Aby umożliwić szybsze wykonywanie zapytań, powiąże się z duplikowaniem danych. Problem z tym efektem ubocznym polega na tym, że w przypadku niektórych działań dane użytkownika są zmieniane, należy znaleźć wszystkie działania, które kiedykolwiek zadziałały, i zaktualizować je wszystkie. Nie jest to przydatne w praktyce?

Należy rozwiązać ten problem, identyfikując kluczowe atrybuty użytkownika, który jest wyświetlany w aplikacji dla każdego działania. Jeśli zobaczysz wizualnie wpis w aplikacji i pokażesz tylko nazwę i obraz twórcy, dlaczego wszystkie dane użytkownika mają być przechowywane w atrybucie "createdBy"? Jeśli dla każdego komentarza po prostu wyświetlasz obraz użytkownika, nie potrzebujesz pozostałej części informacji o użytkowniku. Jest to miejsce, w którym nastąpiło wywołanie "wzorca Drabinu".

Przechodźmy na przykład informacje o użytkowniku:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

Przeglądając te informacje, można szybko wykryć, które są krytycznymi informacjami, a tym samym nie, tworząc "drabinę":

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="Diagram wzorca drabiny" border="false":::

Najmniejszy krok jest nazywany UserChunk, minimalną ilością informacji identyfikujących użytkownika i używaną do duplikacji danych. Zmniejszając rozmiar zduplikowanych danych tylko do informacji, które będą wyświetlane, można zmniejszyć możliwość aktualizowania.

Środkowy krok jest nazywany użytkownikiem. Jest to pełne dane, które będą używane na większości zapytań zależnych od wydajności na Cosmos DB, najbardziej dostępnym i krytycznym. Zawiera informacje reprezentowane przez UserChunk.

Największym jest rozszerzonym użytkownikiem. Zawiera ona najważniejsze informacje o użytkownikach i inne dane, które nie muszą być szybko odczytywane lub które nie są dostępne do użycia, takie jak proces logowania. Te dane mogą być przechowywane poza Cosmos DB, w Azure SQL Database lub w tabelach usługi Azure Storage.

Dlaczego należy podzielić użytkownika, a nawet przechowywać te informacje w różnych miejscach? Ze względu na wydajność punktu widzenia większe dokumenty, costlier zapytania. Zachowaj dokumenty w Slim i z właściwymi informacjami, aby wykonać wszystkie zapytania zależne od wydajności dla sieci społecznościowej. Przechowuj inne dodatkowe informacje na potrzeby scenariuszy, takich jak pełne modyfikacje profilów, logowania i wyszukiwanie danych na potrzeby analizy użycia i inicjatyw dotyczących danych Big Data. Naprawdę nie należy uważać, że zbieranie danych na potrzeby wyszukiwania danych jest wolniejsze, ponieważ jest ono uruchomione na Azure SQL Database. Użytkownik ma problemy, chociaż użytkownicy mają szybkie i Slim środowisko. Użytkownik zapisany na Cosmos DB będzie wyglądać podobnie do tego kodu:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

A wpis będzie wyglądać następująco:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

Gdy Edycja nastąpi w przypadku, gdy ma to na przykład atrybut fragmentu, można łatwo znaleźć odpowiednie dokumenty. Po prostu Użyj zapytań wskazujących atrybuty indeksowane, takie jak `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` , a następnie zaktualizuj fragmenty.

## <a name="the-search-box"></a>Pole wyszukiwania

Użytkownicy będą generować, na szczęście, dużo zawartości. Ponadto powinno być możliwe przeszukanie i znalezienie zawartości, która może nie znajdować się bezpośrednio w swoich strumieniach zawartości, może to być spowodowane faktem, że nie są one zgodne z twórców lub że właśnie próbujesz znaleźć stary wpis, który był sześć miesięcy temu.

Ponieważ używasz Azure Cosmos DB, możesz łatwo zaimplementować aparat wyszukiwania za pomocą [usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/services/search/) w ciągu kilku minut bez wpisywania kodu, innego niż proces wyszukiwania i interfejs użytkownika.

Dlaczego ten proces jest łatwy?

Usługa Azure Wyszukiwanie poznawcze implementuje działania [indeksatorów](/rest/api/searchservice/Indexer-operations), procesy w tle, które znajdują się w repozytoriach danych i umożliwiają autododawanie, aktualizowanie lub usuwanie obiektów w indeksach. Obsługują Indeksatory [Azure SQL Database](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search), indeksatorów [obiektów blob platformy Azure](../search/search-howto-indexing-azure-blob-storage.md) i Thankfully, [Azure Cosmos DB indeksatorów](../search/search-howto-index-cosmosdb.md). Przejście informacji z Cosmos DB do usługi Azure Wyszukiwanie poznawcze jest proste. Obie technologie przechowują informacje w formacie JSON, więc wystarczy [utworzyć indeks](../search/search-what-is-an-index.md) i zmapować atrybuty z dokumentów, które mają być indeksowane. Gotowe. W zależności od rozmiaru danych cała zawartość będzie dostępna do przeszukania w ciągu kilku minut przez najlepsze rozwiązanie typu "wyszukiwanie jako usługa" w infrastrukturze chmurowej.

Więcej informacji o usłudze Azure Wyszukiwanie poznawcze można znaleźć w [przewodniku hitchhiker](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search).

## <a name="the-underlying-knowledge"></a>Podstawowa baza wiedzy

Po zapisaniu całej zawartości, która powiększa się i powiększa codziennie, może się okazać, że zastanawiasz się: co mogę zrobić przy użyciu tego strumienia informacji od moich użytkowników?

Odpowiedź jest prosta: należy ją umieścić i poznać.

Ale co można się uczyć? Oto kilka łatwych przykładów: [Analiza tonacji](https://en.wikipedia.org/wiki/Sentiment_analysis), zalecenia dotyczące zawartości oparte na preferencjach użytkownika, a nawet zautomatyzowany moderator zawartości, który gwarantuje, że zawartość publikowana w sieci społecznościowej jest bezpieczna dla rodziny.

Teraz, gdy nastąpiło przełączenie, prawdopodobnie będziesz potrzebować pewnych doktora w nauce matematycznej, aby wyodrębnić te wzorce oraz informacje z prostych baz danych i plików, ale nie są one prawidłowe.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), część [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), to w pełni zarządzana usługa w chmurze, która umożliwia tworzenie przepływów pracy przy użyciu algorytmów w prostym interfejsie przeciągania i upuszczania, koduje własne algorytmy w języku [R](https://en.wikipedia.org/wiki/R_\(programming_language\))lub użycie niektórych już wbudowanych i gotowych do użycia interfejsów api, takich jak [Analiza tekstu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), Content moderator lub [rekomendacje](https://gallery.azure.ai/Solution/Recommendations-Solution).

Aby osiągnąć dowolne z tych Machine Learning scenariuszy, można użyć [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) do pozyskiwania informacji z różnych źródeł. Możesz również użyć [języka U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) do przetwarzania informacji i generowania danych wyjściowych, które mogą być przetwarzane przez Azure Machine Learning.

Kolejną dostępną opcją jest użycie [usługi Azure Cognitive Services](https://www.microsoft.com/cognitive-services) do analizowania zawartości użytkowników. można nie tylko zrozumieć ich lepsze (poprzez analizowanie ich zapisu w [interfejs API analizy tekstu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale można również wykryć niechcianą lub dojrzałą zawartość oraz odpowiednio do [interfejs API przetwarzania obrazów](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services obejmuje wiele wbudowanych rozwiązań, które nie wymagają żadnego rodzaju wiedzy Machine Learning do użycia.

## <a name="a-planet-scale-social-experience"></a>Środowisko społecznościowe w skali globalnej

Istnieje ostatni, ale nie najmniejsza, istotny artykuł, który należy rozwiązać: **skalowalność**. Podczas projektowania architektury każdy składnik powinien być skalowany samodzielnie. Ostatecznie trzeba będzie przetworzyć więcej danych lub zwiększyć pokrycie geograficzne. Thankfully, osiągnięcie obu zadań to **środowisko gotowe** z Cosmos DB.

Cosmos DB obsługuje dynamiczne partycjonowanie. Automatycznie tworzy partycje na podstawie danego **klucza partycji**, który jest zdefiniowany jako atrybut w dokumentach. Definiowanie poprawnego klucza partycji musi odbywać się w czasie projektowania. Aby uzyskać więcej informacji, zobacz [partycjonowanie w Azure Cosmos DB](partitioning-overview.md).

W przypadku korzystania z społecznościowych należy wyrównać strategię partycjonowania w sposób wykonywania zapytań i pisania. (Na przykład odczyty w obrębie tej samej partycji są pożądane i należy unikać operacji "gorąca") poprzez rozłożenie zapisu na wielu partycjach. Niektóre opcje są następujące: partycje oparte na kluczu czasowym (dzień/miesiąc/tydzień) według kategorii zawartości według regionu geograficznego lub przez użytkownika. Wszystko to jest zależne od tego, w jaki sposób będziesz wykonywać zapytania dotyczące danych i wyświetlać dane w środowisku społecznościowym.

Cosmos DB będą uruchamiać zapytania (łącznie z [agregacjami](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) we wszystkich partycjach w sposób przezroczysty, więc nie trzeba dodawać żadnych logiki w miarę rozwoju danych.

Od momentu ostatecznie zwiększy się ruch, a użycie zasobów (mierzone w [jednostek ru](request-units.md), lub jednostki żądań) zwiększy się. Często przeczytasz i zapisujesz w miarę zwiększania się bazy danych. Baza użytkownika zacznie tworzyć i czytać więcej zawartości. Dzięki temu możliwość **skalowania przepływności** jest istotna. Zwiększenie jednostek ru jest łatwe. Można to zrobić za pomocą kilku kliknięć na Azure Portal lub przez [wystawienie poleceń za pomocą interfejsu API](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Skalowanie w górę i Definiowanie klucza partycji":::

Co się stanie, jeśli problemy są lepsze? Załóżmy, że użytkownicy z innego regionu, kraju lub kontynentu zauważają platformę i zaczynają z niej korzystać. To świetny niespodziewany!

To nie wszystko! Wkrótce zapoznaj się z platformą, która nie jest optymalna. Są one daleko od regionu operacyjnego, który jest jaszczurów opóźnienia. Oczywiście nie chcesz, aby zostały zamknięte. Jeśli było to prosty sposób **rozszerzania Twojego dostępu globalnego**? Istnieje!

Cosmos DB umożliwia [globalną replikację danych](../cosmos-db/tutorial-global-distribution-sql-api.md) i przezroczystość za pomocą kilku kliknięć i automatyczne wybór spośród dostępnych regionów z [kodu klienta](../cosmos-db/tutorial-global-distribution-sql-api.md). Ten proces oznacza również, że można mieć [wiele regionów trybu failover](high-availability.md).

W przypadku globalnej replikacji danych należy upewnić się, że klienci mogą korzystać z niej. W przypadku korzystania z frontonu sieci Web lub uzyskiwania dostępu do interfejsów API z klientów mobilnych można wdrożyć [usługę Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) i sklonować Azure App Service we wszystkich żądanych regionach przy użyciu konfiguracji wydajności do obsługi rozszerzonego globalnego pokrycia. Gdy klienci uzyskują dostęp do frontonu lub interfejsów API, będą kierowani do najbliższej App Service, która z kolei nawiąże połączenie z lokalną repliką Cosmos DB.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Dodawanie globalnego pokrycia do platformy społecznościowej" border="false":::

## <a name="conclusion"></a>Podsumowanie

W tym artykule przedstawiono część niedrogiego tworzenia sieci społecznościowych na platformie Azure. zapewnia ona wyniki poprzez zachęcanie do korzystania z wielowarstwowego rozwiązania magazynu i dystrybucji danych o nazwie "drabina".

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Diagram interakcji między usługami platformy Azure dla sieci społecznościowych" border="false":::

W rzeczywistości jest to, że dla tego rodzaju scenariuszy nie ma punktora Silver. Jest to synergia utworzona przez połączenie doskonałych usług, które pozwalają nam budować wspaniałe środowiska: szybkość i swoboda Azure Cosmos DB zapewniania doskonałej aplikacji społecznościowej, analizy za pierwszą klasą rozwiązania do wyszukiwania, takiego jak Azure Wyszukiwanie poznawcze, elastyczności App Services platformy Azure do hostowania nienawet aplikacji języka niezależny od, ale zaawansowane procesy w tle i rozszerzalny magazyny platformy Azure oraz Azure SQL Database do przechowywania dużych ilości danych i możliwości analitycznych w usłudze Azure Machine Zapoznaj się z artykułem dotyczącym tworzenia wiedzy i analizy, dzięki której możesz dostarczać opinie dotyczące procesów i pomóc nam w dostarczaniu odpowiedniej zawartości do odpowiednich użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat przypadków użycia dla Cosmos DB, zobacz [typowe przypadki użycia Cosmos DB](use-cases.md).
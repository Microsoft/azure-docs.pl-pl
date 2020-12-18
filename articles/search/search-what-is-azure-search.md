---
title: Wprowadzenie do usługi Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania w chmurze firmy Microsoft. Zapoznaj się z przypadkami użycia, przepływem pracy programistycznej, porównaniami z innymi produktami wyszukiwania firmy Microsoft i sposobami ich rozpoczęcia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 12/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 1814555f738f37523c5b23ae729bf20bff62e1f9
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679530"
---
# <a name="what-is-azure-cognitive-search"></a>Co to jest usługa Azure Cognitive Search?

Wyszukiwanie poznawcze platformy Azure ([dawniej znana jako "Azure Search"](whats-new.md#new-service-name)) to usługa wyszukiwania w chmurze, która zapewnia deweloperom interfejsy API i narzędzia do tworzenia bogatego środowiska wyszukiwania za pośrednictwem prywatnej, niejednorodnej zawartości w aplikacjach sieci Web, mobilnych i firmowych. 

Po utworzeniu usługi Wyszukiwanie poznawcze otrzymujesz następujące polecenie:

+ Aparat wyszukiwania, który wykonuje indeksowanie i wykonywanie zapytań
+ Trwały magazyn indeksów wyszukiwania, które tworzysz i którymi zarządzasz
+ Język zapytań umożliwiający tworzenie prostych i złożonych zapytań
+ [Wzbogacanie oparte na AI](cognitive-search-concept-intro.md), tworzenie zawartości z możliwością wyszukiwania poza obrazy, nieprzetworzony tekst, pliki aplikacji
+ Integracja z innymi usługami platformy Azure na potrzeby danych, uczenia maszynowego/AI i zabezpieczeń

W sposób architektoniczny usługa wyszukiwania znajduje się między zewnętrznymi magazynami danych, które zawierają dane nieindeksowane, i aplikacji klienckiej, która wysyła żądania zapytań do indeksu wyszukiwania i obsługuje odpowiedź.

![Architektura usługi Azure Wyszukiwanie poznawcze](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura usługi Azure Wyszukiwanie poznawcze")

Poza tym usługa wyszukiwania integruje się z innymi usługami platformy Azure w postaci *indeksatorów* , które automatyzują pozyskiwanie/pobieranie danych ze źródeł danych platformy Azure, a także *umiejętności* , które obejmują możliwe do użycia AI z Cognitive Services, takie jak analiza obrazów i tekstu, lub niestandardowy AI, utworzony w Azure Machine Learning lub zawijania w Azure Functions.

W samej usłudze wyszukiwania dwa podstawowe obciążenia są *indeksami* i *wykonywaniem zapytań*. 

+ Indeksowanie powoduje przeniesienie tekstu do usługi wyszukiwania i przeszukiwanie. Wewnętrznie tekst przychodzący jest przetwarzany do tokenów i przechowywany w odwróconych indeksach w celu szybkiego skanowania. 

  W ramach indeksowania można dodać *wzbogacanie AI* za pomocą [umiejętności poznawczych](cognitive-search-working-with-skillsets.md), wstępnie zdefiniowanych przez firmę Microsoft lub niestandardowych umiejętności, które tworzysz. Kolejna analiza i przekształcenia mogą spowodować powstanie nowych informacji i struktur, które jeszcze nie istniały, co zapewnia duże narzędzia do wykonywania wielu wyszukiwania i scenariuszy wyszukiwania w bazie wiedzy.

+ Gdy indeks zostanie wypełniony danymi z możliwością wyszukiwania, aplikacja kliencka wysyła żądania zapytań do usługi Search i obsługuje odpowiedzi. Wszystkie wykonywanie zapytań znajduje się nad indeksem wyszukiwania tworzonym, własnym i przechowywanym w usłudze. W aplikacji klienckiej środowisko wyszukiwania jest definiowane przy użyciu interfejsów API z platformy Azure Wyszukiwanie poznawcze i może obejmować dostrajanie istotności, Autouzupełnianie, dopasowywanie synonimów, dopasowywanie rozmyte, dopasowywanie do wzorców, filtrowanie i sortowanie.

Funkcje są uwidaczniane za pośrednictwem prostego [interfejsu API REST](/rest/api/searchservice/) lub [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md), które pozwalają zamaskować złożoność związaną z używaniem pobierania informacji. Za pomocą narzędzi do tworzenia prototypów i wykonywania zapytań dotyczących indeksów i umiejętności można także używać Azure Portal do administrowania usługą i zarządzania zawartością. Ponieważ usługa ta działa w chmurze, zarządzaniem infrastrukturą i dostępnością zajmuje się firma Microsoft.

## <a name="why-use-cognitive-search"></a>Dlaczego warto używać Wyszukiwanie poznawcze

Wyszukiwanie poznawcze platformy Azure są odpowiednie dla następujących scenariuszy aplikacji:

+ Konsolidowanie niejednorodnej zawartości do prywatnego, zdefiniowanego przez użytkownika indeksu wyszukiwania. Indeks wyszukiwania można wypełniać strumieniami dokumentów JSON z dowolnego źródła. W przypadku obsługiwanych źródeł na platformie Azure za pomocą *indeksatora* można zautomatyzować indeksowanie. Kontrola nad schematem indeksu i harmonogramem odświeżania jest kluczowym powodem korzystania z Wyszukiwanie poznawcze.

+ Łatwa implementacja funkcji związanych z wyszukiwaniem. Interfejsy API wyszukiwania upraszczają konstruowanie zapytań, nawigację aspektową, filtry (w tym wyszukiwanie geoprzestrzenne), mapowanie synonimów, Autouzupełnianie i dostrajanie istotności. Korzystając z wbudowanych funkcji, można zaspokoić oczekiwania użytkowników końcowych w przypadku wyszukiwania podobnego do komercyjnych aparatów wyszukiwania w sieci Web.

+ Nieprzetworzona zawartość to duże, nierozróżniane pliki tekstowe lub graficzne lub pliki aplikacji przechowywane w usłudze Azure Blob Storage lub Cosmos DB. Podczas indeksowania można stosować [umiejętności poznawcze](cognitive-search-concept-intro.md) w celu identyfikowania i wyodrębniania tekstu, tworzenia struktury lub tworzenia nowych informacji, takich jak przetłumaczony tekst lub jednostki.

+ Zawartość wymaga analizy językowej lub niestandardowej. Jeśli masz zawartość w wersji innej niż angielska, platforma Azure Wyszukiwanie poznawcze obsługuje zarówno analizatory Lucene, jak i Microsoft Natural Language. Można również skonfigurować analizatory do osiągnięcia wyspecjalizowanego przetwarzania nieprzetworzonej zawartości, takiej jak filtrowanie znaków diakrytycznych lub rozpoznawanie i zachowywanie wzorców w ciągach.

Aby uzyskać więcej informacji o konkretnych funkcjach, zobacz [funkcje platformy Azure wyszukiwanie poznawcze](search-features-list.md)

## <a name="how-to-get-started"></a>Jak zacząć

Kompleksowa Eksploracja podstawowych funkcji wyszukiwania można osiągnąć w czterech krokach:

1. [**Utwórz usługę wyszukiwania**](search-create-service-portal.md) w warstwie Bezpłatna współdzielona z innymi subskrybentami lub [warstwę płatną](https://azure.microsoft.com/pricing/details/search/) dla dedykowanych zasobów używanych tylko przez usługę. Wszystkie samouczki i przewodniki Szybki Start można ukończyć, korzystając z bezpłatnej usługi.

1. [**Utwórz indeks wyszukiwania**](search-what-is-an-index.md) przy użyciu portalu, [interfejsu API REST](/rest/api/searchservice/create-index). [Zestaw SDK platformy .NET](search-howto-dotnet-sdk.md)lub inny zestaw SDK. Schemat indeksu definiuje strukturę zawartości z możliwością wyszukiwania.

1. [**Przekaż zawartość**](search-what-is-data-import.md) do indeksu. Użyj [modelu "push"](tutorial-optimize-indexing-push-api.md) , aby wypchnąć dokumenty JSON z dowolnego źródła lub użyć [modelu "pull" (indeksatory)](search-indexer-overview.md) , jeśli dane źródłowe są na platformie Azure.

1. Tworzenie [**zapytań względem indeksu**](search-query-overview.md) przy użyciu [Eksploratora wyszukiwania](search-explorer.md) w portalu, [interfejsie API REST](search-get-started-rest.md), [zestawie .NET SDK](/dotnet/api/azure.search.documents.searchclient.search)lub innym zestawie SDK.

> [!TIP]
> Zminimalizuj kroki, rozpoczynając od [**Kreatora importowania danych**](search-get-started-portal.md) i źródła danych platformy Azure, aby utworzyć, załadować i zbadać indeks w ciągu kilku minut.

## <a name="how-it-compares"></a>Porównanie z innymi rozwiązaniami

Klienci często pytają, jak platforma Azure Wyszukiwanie poznawcze porównuje z innymi rozwiązaniami związanymi z wyszukiwaniem. Poniższa tabela zawiera podsumowanie podstawowych różnic.

| W porównaniu do | Podstawowe różnice |
|-------------|-----------------|
| Microsoft Search | [Wyszukiwanie firmy Microsoft](/microsoftsearch/overview-microsoft-search) jest przeznaczone dla Microsoft 365 uwierzytelnionych użytkowników, którzy muszą wykonywać zapytania dotyczące zawartości w programie SharePoint. Jest oferowany jako gotowe do użycia środowisko wyszukiwania, włączone i skonfigurowane przez administratorów, z możliwością akceptowania zawartości zewnętrznej za pośrednictwem łączników z firmy Microsoft i innych źródeł. W przypadku opisywania scenariusza firma Microsoft Search z Microsoft 365 jest atrakcyjną opcją eksplorowania.<br/><br/>W przeciwieństwie do usługi Azure Wyszukiwanie poznawcze wykonuje zapytania w zdefiniowanym indeksie, wypełniane danymi i dokumentami, często z różnych źródeł. Usługa Azure Wyszukiwanie poznawcze ma możliwości przeszukiwarki dla niektórych źródeł danych platformy Azure za pomocą [indeksatorów](search-indexer-overview.md), ale można wypchnąć dowolny dokument JSON, który jest zgodny ze schematem indeksu, w pojedynczy, skonsolidowany zasób do przeszukiwania. Możesz również dostosować potok indeksowania, aby uwzględnić Uczenie maszynowe i analizatory leksykalne. Ponieważ Wyszukiwanie poznawcze jest wbudowanym składnikiem wtyczki w większych rozwiązaniach, możesz zintegrować wyszukiwanie w prawie każdej aplikacji na dowolnej platformie.|
|Bing | [Interfejs API wyszukiwania w sieci Web Bing](../cognitive-services/bing-web-search/index.yml) wyszukuje przesłane pasujące terminy w indeksach witryny Bing.com. Indeksy są tworzone na podstawie plików HTML i XML oraz innej zawartości internetowej pochodzącej z publicznych witryn. Na takiej samej zasadzie [Wyszukiwanie niestandardowe Bing](/azure/cognitive-services/bing-custom-search/) udostępnia tę samą przeszukiwarkę dla typów zawartości internetowej, ograniczoną do poszczególnych witryn.<br/><br/>W Wyszukiwanie poznawcze można zdefiniować i wypełnić indeks. [Indeksatorów](search-indexer-overview.md) można użyć do przeszukiwania danych w źródłach danych platformy Azure lub wypchnięcia dowolnego dokumentu JSON z indeksem do usługi wyszukiwania. |
|Wyszukiwanie w bazie danych | Wiele platform baz danych udostępnia wbudowane środowisko wyszukiwania. Platforma SQL Server oferuje [wyszukiwanie pełnotekstowe](/sql/relational-databases/search/full-text-search). Usługa Cosmos DB i podobne technologie mają indeksy odpytywalne. W przypadku oceny produktów, które łączą funkcje wyszukiwania i magazynu, może być trudno wybrać najlepsze rozwiązanie. Wiele rozwiązań używa zarówno systemu DBMS dla magazynu, jak i usługi Azure Wyszukiwanie poznawcze na potrzeby wyspecjalizowanych funkcji wyszukiwania.<br/><br/>W porównaniu z wyszukiwaniem w systemie DBMS usługa Azure Wyszukiwanie poznawcze przechowuje zawartość z niejednorodnych źródeł i oferuje wyspecjalizowane funkcje przetwarzania tekstu, takie jak przetwarzanie tekstu z uwzględnieniem języka (Lematyzacja, formularze programu Word) w [językach 56](/rest/api/searchservice/language-support). Obsługuje ona również autokorektę błędnie napisanych wyrazów, [synonimy](/rest/api/searchservice/synonym-map-operations), [sugestie](/rest/api/searchservice/suggestions), [formanty oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [aspekty](./search-filters-facets.md)i [tokenizację niestandardową](/rest/api/searchservice/custom-analyzers-in-azure-search). [Aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) w usłudze Azure wyszukiwanie poznawcze jest oparty na usłudze Apache Lucene, w standardzie do pobrania informacji. Mimo że platforma Azure Wyszukiwanie poznawcze utrzymuje dane w postaci odwróconego indeksu, nie jest to zamiennik dla rzeczywistego magazynu danych i nie zalecamy używania go w tej pojemności. Więcej informacji znajduje się we [wpisie na forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Użycie zasobów to kolejny etap w tej kategorii. Indeksowanie i niektóre operacje zapytań są często intensywne z obliczeniowego punktu widzenia. Przeniesienie wyszukiwania z DBMS do dedykowanego rozwiązania w chmurze pozwala zachować zasoby systemowe dla przetwarzania transakcyjnego. Dzięki temu można łatwo dostosować skalę do liczby zapytań.|
|Dedykowane rozwiązanie wyszukiwania | Jeśli przyjmiemy, że decydujesz się na dedykowane wyszukiwanie z pełnego zakresu funkcjonalności, ostateczne porównanie w oparciu o kategorie odbywa się miedzy rozwiązaniami lokalnymi a usługą w chmurze. Wiele technologii wyszukiwania umożliwia sterowanie indeksowaniem i potokami zapytań oraz dostęp do zaawansowanej składni zapytań i filtrów. Pozwalają one sterować rangą i trafnością, a także udostępniają funkcje samodzielnego i inteligentnego wyszukiwania. <br/><br/>Usługę w chmurze warto wybrać, jeśli potrzebne jest gotowe rozwiązanie z regulowaną skalą, które prawie nie wymaga konserwacji i nakładu pracy. <br/><br/>W ramach modelu chmury kilku dostawców oferuje porównywalne funkcje bazowe, obejmujące wyszukiwanie pełnotekstowe, wyszukiwanie geograficzne oraz obsługę określonego poziomu niejednoznaczności w danych wejściowych wyszukiwania. Zwykle korzysta się z [wyspecjalizowanej funkcji](search-features-list.md) lub wybiera się łatwość użytkowania i ogólną prostotę interfejsów API, narzędzi i funkcji zarządzania. |

Dzięki dostawcom chmury usługa Azure Wyszukiwanie poznawcze jest najmocna w przypadku obciążeń wyszukiwania pełnotekstowego w magazynach zawartości i bazach danych na platformie Azure, w przypadku aplikacji, które korzystają głównie z wyszukiwania zarówno do pobierania informacji, jak i nawigowania po zawartości. 

Oto najważniejsze zalety:

+ Integracja danych platformy Azure (przeszukiwarki) w warstwie indeksowania
+ Integracja z usługą Azure Private link w celu zapewnienia obsługi wymagań dotyczących zabezpieczeń z Internetu
+ Integracja z przetwarzaniem AI w celu przeszukiwania typów zawartości, które można wyszukiwać.
+ Analiza językowa i niestandardowa z analizatorami umożliwiającymi spójne wyszukiwanie pełnotekstowe w 56 językach
+ [Funkcje krytyczne](search-features-list.md): bogaty język zapytań, dostrajanie przydatności, tworzenie aspektów, Autouzupełnianie, synonimy, wyszukiwanie geograficzne i kompozycja wyników.
+ Skalowalność i niezawodność platformy Azure oraz dostępność światowej klasy

Wśród naszych klientów mogą korzystać z szerokiego zakresu funkcji platformy Azure Wyszukiwanie poznawcze obejmują wykazy online, programy biznesowe i aplikacje do odnajdywania dokumentów.

## <a name="watch-this-video"></a>Obejrzyj ten film wideo

W tym 15-minutowym filmie wideo Menedżer programów Luis Cabrera wprowadza platformę Azure Wyszukiwanie poznawcze.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]
---
title: Wprowadzenie do usługi Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania w chmurze firmy Microsoft. Zapoznaj się z przypadkami użycia, przepływem pracy programistycznej, porównaniami z innymi produktami wyszukiwania firmy Microsoft i sposobami ich rozpoczęcia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549893"
---
# <a name="what-is-azure-cognitive-search"></a>Co to jest usługa Azure Cognitive Search?

Wyszukiwanie poznawcze platformy Azure ([dawniej znana jako "Azure Search"](whats-new.md#new-service-name)) to usługa wyszukiwania w chmurze, która zapewnia deweloperom interfejsy API i narzędzia do tworzenia bogatego środowiska wyszukiwania za pośrednictwem prywatnej, niejednorodnej zawartości w aplikacjach sieci Web, mobilnych i firmowych. 

Wyszukiwanie jest podstawą dla każdej aplikacji, która wyświetla zawartość użytkownikom, z typowymi scenariuszami, takimi jak wykaz lub wyszukiwanie dokumentów, wyszukiwanie w witrynie handlu elektronicznego lub wydobycie wiedzy na potrzeby analizy danych. Interfejsy API i architektura Wyszukiwanie poznawcze upraszczają zadanie dodawania zaawansowanych danych do dowolnych rozwiązań.

Usługa wyszukiwania ma następujące składniki:

+ Wyszukaj w aparacie wyszukiwania pełnotekstowego
+ Trwały magazyn zawartości indeksowanej użytkownika
+ Interfejsy API do indeksowania i wykonywania zapytań dotyczących zawartości
+ Opcjonalne [wzbogacania oparte na formacie AI](cognitive-search-concept-intro.md), tworzenie zawartości z możliwością wyszukiwania poza obrazy, nieprzetworzony tekst bez struktury, pliki aplikacji
+ Opcjonalna integracja z innymi usługami platformy Azure na potrzeby danych, uczenia maszynowego/AI, monitorowania i zabezpieczeń
+ Opcjonalna implementacja [wyszukiwania semantycznego (wersja zapoznawcza)](semantic-search-overview.md) w celu zwiększenia istotności

W sposób architektoniczny usługa wyszukiwania znajduje się między zewnętrznymi magazynami danych, które zawierają dane nieindeksowane, i aplikacji klienckiej, która wysyła żądania zapytań do indeksu wyszukiwania i obsługuje odpowiedź.

![Architektura usługi Azure Wyszukiwanie poznawcze](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura usługi Azure Wyszukiwanie poznawcze")

Wyszukiwanie można zintegrować z innymi usługami platformy Azure w postaci *indeksatorów* , które automatyzują pozyskiwanie/pobieranie danych ze źródeł danych platformy Azure, a także *umiejętności* , które obejmują możliwość konsumowania zawartości AI z Cognitive Services, takich jak analiza obrazów i tekstu, lub niestandardowa AI, którą tworzysz w Azure Machine Learning lub zawijania w Azure Functions.

## <a name="inside-a-search-service"></a>Wewnątrz usługi wyszukiwania

W samej usłudze wyszukiwania dwa podstawowe obciążenia są *indeksami* i *wykonywaniem zapytań*. 

+ [Indeksowanie](search-what-is-an-index.md) to proces pobierania, który ładuje zawartość do usługi wyszukiwania i umożliwia wyszukiwanie. Wewnętrznie tekst przychodzący jest przetwarzany do tokenów i przechowywany w odwróconych indeksach w celu szybkiego skanowania. Można przekazać dowolny tekst w postaci dokumentów JSON.

  Ponadto, jeśli zawartość zawiera pliki mieszane, możesz dodać *wzbogacanie AI* za pomocą [umiejętności poznawczych](cognitive-search-working-with-skillsets.md). Wzbogacanie AI może wyodrębnić tekst osadzony w plikach aplikacji, a także wywnioskować tekst i strukturę z plików innych niż tekstowe przez analizowanie zawartości. 

  Umiejętności zapewniające analizę są wstępnie zdefiniowane przez firmę Microsoft lub niestandardowe umiejętności, które tworzysz. Kolejna analiza i przekształcenia mogą spowodować powstanie nowych informacji i struktur, które jeszcze nie istniały, co zapewnia duże narzędzia do wykonywania wielu wyszukiwania i scenariuszy wyszukiwania w bazie wiedzy.

+ [Wykonywanie zapytań](search-query-overview.md) może wystąpić, gdy indeks zostanie wypełniony tekstem z możliwością wyszukiwania, gdy aplikacja kliencka wysyła żądania zapytań do usługi Search i obsługuje odpowiedzi. Wszystkie wykonywanie zapytań znajduje się nad indeksem wyszukiwania tworzonym, własnym i przechowywanym w usłudze. W aplikacji klienckiej środowisko wyszukiwania jest definiowane przy użyciu interfejsów API z platformy Azure Wyszukiwanie poznawcze i może obejmować dostrajanie istotności, Autouzupełnianie, dopasowywanie synonimów, dopasowywanie rozmyte, dopasowywanie do wzorców, filtrowanie i sortowanie.

Funkcje są uwidaczniane za pośrednictwem prostego [interfejsu API REST](/rest/api/searchservice/) lub [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md), które pozwalają zamaskować złożoność związaną z używaniem pobierania informacji. Za pomocą narzędzi do tworzenia prototypów i wykonywania zapytań dotyczących indeksów i umiejętności można także używać Azure Portal do administrowania usługą i zarządzania zawartością. Ponieważ usługa ta działa w chmurze, zarządzaniem infrastrukturą i dostępnością zajmuje się firma Microsoft.

## <a name="why-use-cognitive-search"></a>Dlaczego warto używać Wyszukiwanie poznawcze

Wyszukiwanie poznawcze platformy Azure są odpowiednie dla następujących scenariuszy aplikacji:

+ Konsolidowanie niejednorodnej zawartości do prywatnego, zdefiniowanego przez użytkownika indeksu wyszukiwania.

+ Łatwe wdrażanie funkcji związanych z wyszukiwaniem: dostrojenie przydatności, Nawigacja aspektowa, filtry (w tym wyszukiwanie geograficzne), mapowanie synonimów i Autouzupełnianie.

+ Przekształć duże, nierozróżniane pliki tekstowe lub graficzne, lub pliki aplikacji przechowywane w usłudze Azure Blob Storage lub Cosmos DB, w dokumenty JSON do przeszukiwania. Jest to osiągane podczas indeksowania przez [umiejętności poznawcze](cognitive-search-concept-intro.md) , które dodają zewnętrzne przetwarzanie.

+ Dodawanie analizy językowej lub niestandardowej. Jeśli masz zawartość w wersji innej niż angielska, platforma Azure Wyszukiwanie poznawcze obsługuje zarówno analizatory Lucene, jak i Microsoft Natural Language. Można również skonfigurować analizatory do osiągnięcia wyspecjalizowanego przetwarzania nieprzetworzonej zawartości, takiej jak filtrowanie znaków diakrytycznych lub rozpoznawanie i zachowywanie wzorców w ciągach.

Aby uzyskać więcej informacji o konkretnych funkcjach, zobacz [funkcje platformy Azure wyszukiwanie poznawcze](search-features-list.md)

## <a name="how-to-get-started"></a>Jak zacząć

Kompleksowa Eksploracja podstawowych funkcji wyszukiwania można osiągnąć w czterech krokach:

1. [**Utwórz usługę wyszukiwania**](search-create-service-portal.md) w udostępnionej warstwie Bezpłatna lub w [warstwie](https://azure.microsoft.com/pricing/details/search/) płatnej dla zasobów dedykowanych używanych tylko przez usługę. Wszystkie Przewodniki Szybki Start i samouczki można wykonać w usłudze udostępnionej.

1. [**Utwórz indeks wyszukiwania**](search-what-is-an-index.md) przy użyciu portalu, [interfejsu API REST](/rest/api/searchservice/create-index), [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md)lub innego zestawu SDK. Schemat indeksu definiuje strukturę zawartości z możliwością wyszukiwania.

1. [**Przekaż zawartość**](search-what-is-data-import.md) przy użyciu [modelu "push"](tutorial-optimize-indexing-push-api.md) , aby wypchnąć dokumenty JSON z dowolnego źródła lub użyć [modelu "pull" (indeksatory)](search-indexer-overview.md) , jeśli dane źródłowe są na platformie Azure.

1. Tworzenie [**zapytań względem indeksu**](search-query-overview.md) przy użyciu [Eksploratora wyszukiwania](search-explorer.md) w portalu, [interfejsie API REST](search-get-started-rest.md), [zestawie .NET SDK](/dotnet/api/azure.search.documents.searchclient.search)lub innym zestawie SDK.

W przypadku początkowej eksploracji Rozpocznij pracę z [**kreatorem importowania danych**](search-get-started-portal.md) i wbudowanym źródłem danych platformy Azure, aby utworzyć, załadować i zbadać indeks w ciągu kilku minut.

Aby uzyskać pomoc dotyczącą złożonych lub niestandardowych rozwiązań, [**skontaktuj się z partnerem**](resource-partners-knowledge-mining.md) z dogłębną wiedzą w zakresie technologii wyszukiwanie poznawcze.

## <a name="compare-search-options"></a>Porównanie opcji wyszukiwania

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
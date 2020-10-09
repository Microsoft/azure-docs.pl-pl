---
title: Pojęcia dotyczące wzbogacania AI
titleSuffix: Azure Cognitive Search
description: Wyodrębnianie zawartości, przetwarzanie języka naturalnego (NLP) i przetwarzanie obrazów są używane do tworzenia zawartości z możliwością wyszukiwania w indeksach Wyszukiwanie poznawcze platformy Azure ze wstępnie zdefiniowanymi umiejętnościami poznawczymi i niestandardowymi algorytmami AI.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 09e7a39a2d97626dd01a00fdaef9bc4d711d557b
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828088"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Wzbogacanie AI na platformie Azure Wyszukiwanie poznawcze

Wzbogacanie AI to rozszerzenie [indeksatorów](search-indexer-overview.md) , które mogą służyć do wyodrębniania tekstu z obrazów, obiektów blob i innych źródeł danych bez struktury. Wzbogacanie i wyodrębnianie sprawia, że zawartość może być bardziej przeszukiwana w obiektach wyjściowych indeksatora, w [indeksie wyszukiwania](search-what-is-an-index.md) lub w [sklepie z bazami wiedzy](knowledge-store-concept-intro.md). 

Wyodrębnianie i wzbogacanie są implementowane przy użyciu *umiejętności poznawczych* dołączonych do potoku opartego na indeksatorze. Możesz korzystać z wbudowanych umiejętności firmy Microsoft lub osadzać zewnętrzne przetwarzanie w [*niestandardowych umiejętnościach*](cognitive-search-create-custom-skill-example.md) , które tworzysz. Przykłady niestandardowej umiejętności mogą być niestandardowym modułem jednostki lub klasyfikatorem dokumentu przeznaczonym dla konkretnej domeny, takiej jak finanse, publikacje naukowe lub medycyna.

Wbudowane umiejętności należą do następujących kategorii: 

+ Umiejętności **przetwarzania języka naturalnego** obejmują [rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md), [wykrywanie języka](cognitive-search-skill-language-detection.md), [wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md), manipulowanie tekstem, [wykrywanie tonacji](cognitive-search-skill-sentiment.md)i [wykrywanie](cognitive-search-skill-pii-detection.md)wielu osób. Dzięki tym umiejętnościom tekst bez struktury jest mapowany jako pola z możliwością wyszukiwania i filtrowania w indeksie.

+ Umiejętności **przetwarzania obrazów** obejmują [optyczne rozpoznawanie znaków (OCR)](cognitive-search-skill-ocr.md) i identyfikację [funkcji wizualizacji](cognitive-search-skill-image-analysis.md), takich jak wykrywanie twarzy, interpretacja obrazu, rozpoznawanie obrazu (sławę osoby i punkty orientacyjne) lub atrybuty, takie jak Orientacja obrazu. Te umiejętności tworzą tekstową reprezentację zawartości obrazu, dzięki czemu można ją przeszukiwać przy użyciu możliwości zapytań w usłudze Azure Wyszukiwanie poznawcze.

![Diagram potoku wzbogacania](./media/cognitive-search-intro/cogsearch-architecture.png "Przegląd potoku wzbogacania")

Wbudowane umiejętności na platformie Azure Wyszukiwanie poznawcze opierają się na wstępnie szkolonych modelach uczenia maszynowego w interfejsy API usług Cognitive Services: [Przetwarzanie obrazów](../cognitive-services/computer-vision/index.yml) i [Analiza tekstu](../cognitive-services/text-analytics/overview.md). Możesz dołączyć zasób Cognitive Services, jeśli chcesz korzystać z tych zasobów podczas przetwarzania zawartości.

Przetwarzanie języka naturalnego i obrazu jest stosowane w fazie pozyskiwania danych, a wyniki stają się częścią kompozycji dokumentu w indeksie wyszukiwania na platformie Azure Wyszukiwanie poznawcze. Dane są źródłem danych jako zestaw danych platformy Azure, a następnie wypychane za pośrednictwem potoku indeksowania przy użyciu zależnych [umiejętności](cognitive-search-predefined-skills.md) , które są potrzebne.  

## <a name="when-to-use-ai-enrichment"></a>Kiedy używać wzbogacenia AI

Należy rozważyć użycie wbudowanych umiejętności poznawczych, jeśli nieprzetworzona zawartość jest tekstem bez struktury, zawartością obrazu lub treścią wymagającą wykrywania i tłumaczenia języka. Zastosowanie AI przy użyciu wbudowanych umiejętności poznawczych pozwala odblokować tę zawartość, zwiększając jej wartość i narzędzie w aplikacjach do wyszukiwania i analizy danych. 

Ponadto możesz rozważyć dodanie niestandardowej umiejętności, jeśli masz kod typu open source, innej firmy lub kodu pierwszej firmy, który chcesz zintegrować z potokiem. Modele klasyfikacji, które identyfikują charakterystyki najważniejsze różnych typów dokumentów, należą do tej kategorii, ale można użyć dowolnego pakietu, który dodaje wartość do zawartości.

### <a name="more-about-built-in-skills"></a>Więcej informacji na temat umiejętności wbudowanych

[Zestawu umiejętności](cognitive-search-defining-skillset.md) , który jest montowany przy użyciu wbudowanych umiejętności, jest odpowiedni dla następujących scenariuszy aplikacji:

+ Zeskanowane dokumenty (JPEG), które mają być używane do wyszukiwania pełnotekstowego. Możesz dołączyć umiejętność optycznego rozpoznawania znaków (OCR) do identyfikowania, wyodrębniania i pozyskiwania tekstu z plików JPEG.

+ Pliki PDF z połączonym obrazem i tekstem. Tekst w plikach PDF można wyodrębnić podczas indeksowania bez użycia kroków wzbogacania, ale dodanie obrazu i przetwarzania języka naturalnego może często generować lepszy wynik niż w przypadku standardowego indeksowania.

+ Zawartość obejmująca wiele języków, do której chcesz zastosować wykrywanie języka i tłumaczenie tekstu.

+ Dokumenty bez struktury lub częściowo strukturalne zawierające zawartość, która ma nieodłączne znaczenie lub kontekst, który jest ukryty w większym dokumencie. 

  Obiekty blob w szczególności często zawierają dużą treść zawartości zapakowanej do jednego "pola". Dołączając umiejętność przetwarzania obrazów i języka naturalnego do indeksatora, można utworzyć nowe informacje, które są extant w nieprzetworzonej zawartości, ale nie są w inny sposób naliczone jako odrębne pola. Niektóre gotowe do użycia umiejętności poznawcze, które mogą pomóc: Wyodrębnianie kluczowych fraz, analiza tonacji oraz rozpoznawanie jednostek (osoby, organizacje i lokalizacje).

  Ponadto wbudowane umiejętności mogą również służyć do zmiany struktury zawartości poprzez operacje dzielenia tekstu, scalania i kształtowania.

### <a name="more-about-custom-skills"></a>Więcej informacji na temat umiejętności niestandardowych

Umiejętności niestandardowe mogą obsługiwać bardziej złożone scenariusze, takie jak rozpoznawanie formularzy lub wykrywanie jednostek niestandardowych przy użyciu udostępnianego modelu i zawijania w [niestandardowym interfejsie sieci Web](cognitive-search-custom-skill-interface.md). Kilka przykładów umiejętności niestandardowych obejmuje [aparat rozpoznawania formularzy](../cognitive-services/form-recognizer/overview.md), integrację [interfejs API wyszukiwania jednostek Bing](./cognitive-search-create-custom-skill-example.md)i [niestandardowego rozpoznawania jednostek](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Kroki potoku wzbogacania <a name="enrichment-steps"></a>

Potok wzbogacania jest oparty na [*indeksatorach*](search-indexer-overview.md). Indeksatory wypełniają indeks na podstawie mapowań pola do pola między indeksem a źródłem danych na potrzeby łamania dokumentów. Umiejętności, które są teraz dołączone do indeksatorów, przechwytuje i wzbogacają dokumenty zależnie od zdefiniowanych zestawu umiejętności. Po indeksowaniu możesz uzyskać dostęp do zawartości za pośrednictwem żądań wyszukiwania przez wszystkie [typy zapytań obsługiwane przez usługę Azure wyszukiwanie poznawcze](search-query-overview.md).  Jeśli jesteś nowym indeksatorem, ta sekcja przeprowadzi Cię przez kroki.

### <a name="step-1-connection-and-document-cracking-phase"></a>Krok 1. faza nawiązywania połączenia i dokumentu

Na początku potoku masz tekst bez struktury lub zawartość nietekstową (na przykład obrazy, zeskanowane dokumenty lub pliki JPEG). Dane muszą istnieć w usłudze Azure Data Storage, do której można uzyskać dostęp za pomocą indeksatora. Indeksatory mogą "pęknięcia" dokumenty źródłowe w celu wyodrębnienia tekstu z danych źródłowych. Łamanie dokumentu to proces wyodrębniania lub tworzenia zawartości tekstowej ze źródeł nietekstowych podczas indeksowania.

![Faza łamania dokumentu](./media/cognitive-search-intro/document-cracking-phase-blowup.png "łamanie dokumentów")

 Obsługiwane źródła obejmują usługę Azure Blob Storage, usługę Azure Table Storage, Azure SQL Database i Azure Cosmos DB. Zawartość oparta na tekście można wyodrębnić z następujących typów plików: PDF, Word, PowerPoint i CSV. Aby zapoznać się z pełną listą, zobacz [obsługiwane formaty](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Indeksowanie odbywa się w czasie, w którym rozpoczyna się od małego, reprezentatywnego zestawu danych, a następnie kompiluje się przyrostowo wraz z oczekiwaniami.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Krok 2. umiejętności poznawcze i faza wzbogacania

Wzbogacanie jest wykonywane z *umiejętnościami poznawczymi* wykonującymi operacje niepodzielne. Na przykład po rozwiązaniu pliku PDF można zastosować rozpoznawanie jednostek, wykrywanie języka lub wyodrębnianie kluczowych fraz w celu utworzenia nowych pól w indeksie, które nie są dostępne natywnie w źródle. Całkowicie kolekcja umiejętności używanych w potoku jest nazywana *zestawu umiejętności*.  

![Faza wzbogacania](./media/cognitive-search-intro/enrichment-phase-blowup.png "Faza wzbogacania")

Zestawu umiejętności jest oparta na [wbudowanych umiejętnościach poznawczych](cognitive-search-predefined-skills.md) lub [niestandardowych umiejętnościach](cognitive-search-create-custom-skill-example.md) , które zapewniasz i łączą się z zestawu umiejętności. Zestawu umiejętności może być minimalny lub wysoce skomplikowany i określa nie tylko typ przetwarzania, ale również kolejność operacji. Zestawu umiejętności oraz mapowania pól zdefiniowane jako część indeksatora w pełni określają potok wzbogacania. Aby uzyskać więcej informacji na temat ściągania wszystkich z tych fragmentów, zobacz [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md).

Wewnętrznie potok generuje kolekcję ulepszonych dokumentów. Można zdecydować, które części wzbogaconych dokumentów mają być mapowane do pól indeksowanych w indeksie wyszukiwania. Na przykład jeśli zastosowano wyodrębnianie kluczowych fraz i umiejętności rozpoznawania jednostek, te nowe pola staną się częścią wzbogaconego dokumentu i mogą być mapowane do pól w indeksie. Zobacz [Adnotacje](cognitive-search-concept-annotations-syntax.md) , aby dowiedzieć się więcej na temat formatów wejścia/wyjścia.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Dodaj element knowledgeStore, aby zapisać wzbogacenia

[Interfejs API REST usługi Search — wersja = 2020-06-30](/rest/api/searchservice/) rozszerza umiejętności z `knowledgeStore` definicją, która zapewnia połączenie z usługą Azure Storage i projekcje opisujące sposób przechowywania wzbogacania. Jest to uzupełnienie indeksu. W standardowym potoku AI, wzbogacone dokumenty są nietrwałe, używane tylko podczas indeksowania, a następnie odrzucane. W sklepie merytorycznym chronione dokumenty są zachowywane. Aby uzyskać więcej informacji, zobacz artykuł [sklep z bazami](knowledge-store-concept-intro.md)danych.

### <a name="step-3-search-index-and-query-based-access"></a>Krok 3. Wyszukiwanie indeksu i dostępu opartego na zapytaniach

Po zakończeniu przetwarzania masz indeks wyszukiwania składający się z wzbogaconych dokumentów, w pełni tekstu — wyszukiwanie na platformie Azure Wyszukiwanie poznawcze. [Zapytanie o indeks](search-query-overview.md) polega na tym, jak deweloperzy i użytkownicy uzyskują dostęp do wzbogaconej zawartości wygenerowanej przez potok. 

![Indeks przy użyciu ikony wyszukiwania](./media/cognitive-search-intro/search-phase-blowup.png "Indeks przy użyciu ikony wyszukiwania")

Indeks jest podobny do dowolnego innego, co można utworzyć dla Wyszukiwanie poznawcze platformy Azure: możesz uzupełnić za pomocą analizatorów niestandardowych, wywołać zapytania wyszukiwania rozmytego, dodać przefiltrowane wyszukiwanie lub eksperymentować z profilami oceniania, aby zmienić kształt wyników wyszukiwania.

Indeksy są generowane na podstawie schematu indeksu, który definiuje pola, atrybuty i inne konstrukcje dołączone do określonego indeksu, takie jak profile oceniania i mapy synonimów. Gdy indeks jest zdefiniowany i wypełniany, można indeksować przyrostowo, aby pobrać nowe i zaktualizowane dokumenty źródłowe. Niektóre modyfikacje wymagają pełnej kompilacji. Aby projekt schematu był stabilny, należy użyć małego zestawu danych. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks](search-howto-reindex.md).

**Lista kontrolna: Typowy przepływ pracy**

1. Podzbiór danych źródłowych platformy Azure do reprezentatywnej próbki. Indeksowanie odbywa się w czasie, w którym rozpoczyna się od małego, reprezentatywnego zestawu danych, a następnie kompiluje się przyrostowo wraz z oczekiwaniami.

1. Utwórz [obiekt źródła danych](/rest/api/searchservice/create-data-source) w usłudze Azure wyszukiwanie poznawcze, aby podać parametry połączenia na potrzeby pobierania danych.

1. Utwórz [zestawu umiejętności](/rest/api/searchservice/create-skillset) z procedurami wzbogacania.

1. Zdefiniuj [schemat indeksu](/rest/api/searchservice/create-index). Kolekcja *Fields* zawiera pola z danych źródłowych. Należy również utworzyć zastępcze dodatkowe pola, aby przechowywać wygenerowane wartości dla zawartości utworzonej podczas wzbogacania.

1. Zdefiniuj [indeksator](/rest/api/searchservice/create-indexer) odwołujący się do źródła danych, zestawu umiejętności i indeksu.

1. W obszarze indeksatora Dodaj *outputFieldMappings*. Ta sekcja mapuje dane wyjściowe z zestawu umiejętności (w kroku 3) do pól danych wejściowych w schemacie indeksu (w kroku 4).

1. Wyślij właśnie utworzone żądanie *utworzenia indeksatora* (żądanie post z definicją indeksatora w treści żądania), aby wyrazić indeksator na platformie Azure wyszukiwanie poznawcze. Ten krok polega na tym, jak uruchomić indeksator, wywołując potok.

1. Uruchom zapytania, aby obliczyć wyniki i zmodyfikować kod w celu zaktualizowania konfiguracji umiejętności, schematu lub indeksatora.

1. [Zresetuj indeksator](search-howto-reindex.md) przed ponownym kompilacją potoku.

## <a name="next-steps"></a>Następne kroki

+ [Linki do dokumentacji dotyczącej wzbogacania AI](cognitive-search-resources-documentation.md)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI (C#)](cognitive-search-create-custom-skill-example.md)
+ [Szybki Start: Wypróbuj wzbogacanie AI w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: informacje na temat interfejsów API wzbogacania AI](cognitive-search-tutorial-blob.md)
+ [Magazyn wiedzy](knowledge-store-concept-intro.md)
+ [Tworzenie sklepu merytorycznego w usłudze REST](knowledge-store-create-rest.md)
+ [Porady dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

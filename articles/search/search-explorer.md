---
title: Narzędzie zapytania Eksploratora wyszukiwania w Azure Portal
titleSuffix: Azure Cognitive Search
description: W tym Azure Portal szybkiego startu Użyj Eksploratora wyszukiwania, aby poznać składnię zapytań, testować wyrażenia zapytań lub sprawdzić dokument wyszukiwania. Eksplorator wyszukiwania wyszukuje indeksy w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179779"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Szybki Start: korzystanie z Eksploratora wyszukiwania do uruchamiania zapytań w portalu

**Eksplorator wyszukiwania** to wbudowane narzędzie do tworzenia zapytań służące do uruchamiania zapytań w odniesieniu do indeksu wyszukiwania w usłudze Azure wyszukiwanie poznawcze. To narzędzie ułatwia naukę składni zapytań, testowanie zapytania lub wyrażenia filtru lub potwierdzenie odświeżania danych przez sprawdzenie, czy nowa zawartość istnieje w indeksie.

Ten przewodnik Szybki Start używa istniejącego indeksu do zademonstrowania Eksploratora wyszukiwania. Żądania są formułowane przy użyciu [interfejsu API REST wyszukiwania](/rest/api/searchservice/search-documents)z odpowiedziami zwracanymi jako pełne dokumenty JSON.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć na miejsce następujące wymagania wstępne:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ Na potrzeby tego przewodnika Szybki Start jest używany *indeks realestate-US-Sample* . Skorzystaj z [przewodnika Szybki Start: Utwórz indeks](search-import-data-portal.md) , aby utworzyć indeks przy użyciu wartości domyślnych. Wbudowane przykładowe źródło danych obsługiwane przez firmę Microsoft (**realestate-US-Sample**) zapewnia dane.

## <a name="start-search-explorer"></a>Uruchom Eksploratora wyszukiwania

1. W [Azure Portal](https://portal.azure.com)Otwórz stronę przegląd wyszukiwania na pulpicie nawigacyjnym lub [Znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Otwórz Eksploratora wyszukiwania na pasku poleceń:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Polecenie Search Explorer w portalu" border="true":::

    Lub użyj osadzonej karty **Eksploratora wyszukiwania** w otwartym indeksie:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Karta Eksplorator wyszukiwania" border="true":::

## <a name="unspecified-query"></a>Nieokreślone zapytanie

Aby najpierw obejrzeć zawartość, wykonaj puste wyszukiwanie, klikając przycisk **Wyszukaj** bez podanych warunków. Puste wyszukiwanie jest przydatne jako pierwsze zapytanie, ponieważ zwraca całe dokumenty, aby można było sprawdzić kompozycję dokumentu. W przypadku pustego wyszukiwania nie ma rangi wyszukiwania i dokumenty są zwracane w dowolnej kolejności ( `"@search.score": 1` dla wszystkich dokumentów). Domyślnie w żądaniu wyszukiwania są zwracane dokumenty 50.

Odpowiednikiem składni pustego wyszukiwania jest `*` lub `search=*` .
   
   ```http
   search=*
   ```

   **Wyniki**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Przykład niekwalifikowanego lub pustego zapytania" border="true":::

## <a name="free-text-search"></a>Wyszukiwanie swobodne tekstu

Zapytania o dowolnej postaci, z operatorami lub bez, są przydatne do symulowania zapytań zdefiniowanych przez użytkownika, które są wysyłane z niestandardowej aplikacji do Wyszukiwanie poznawcze platformy Azure. Tylko pola, które są przypisywane do **wyszukiwania** w definicji indeksu, są skanowane pod kątem dopasowań. 

Zwróć uwagę, że w przypadku podania kryteriów wyszukiwania, takich jak terminy zapytania lub wyrażenia, ranga wyszukiwania jest dostępna. Poniższy przykład ilustruje wyszukiwanie swobodne tekstu.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Wyniki**

   Możesz użyć kombinacji Ctrl-F, aby przeszukać wyniki dla określonych warunków zainteresowania.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Przykład zapytania Free text" border="true":::

## <a name="count-of-matching-documents"></a>Liczba pasujących dokumentów 

Dodaj **$Count = true** , aby uzyskać liczbę dopasowań znalezionych w indeksie. W przypadku pustego wyszukiwania liczba to łączna liczba dokumentów w indeksie. W przypadku kwalifikowanego wyszukiwania jest to liczba dokumentów pasujących do danych wejściowych zapytania. Odwołaj, że usługa domyślnie zwraca 50 najlepszych dopasowań, więc w indeksie mogą znajdować się więcej dopasowań niż zawarte w wynikach.

   ```http
   $count=true
   ```

   **Wyniki**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Liczba pasujących dokumentów w indeksie" border="true":::

## <a name="limit-fields-in-search-results"></a>Ograniczanie pól w wynikach wyszukiwania

Dodaj [**$SELECT**](search-query-odata-select.md) , aby ograniczyć wyniki do jawnie nazwanych pól, aby uzyskać bardziej czytelny wynik w **Eksploratorze wyszukiwania**. Aby zachować ciąg wyszukiwania i **$Count = true**, prefiks argumentów z **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Wyniki**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Ograniczanie pól w wynikach wyszukiwania" border="true":::

## <a name="return-next-batch-of-results"></a>Zwróć następną partię wyników

Usługa Azure Wyszukiwanie poznawcze zwraca górne dopasowania 50 w oparciu o rangę wyszukiwania. Aby uzyskać następny zestaw pasujących dokumentów, Dołącz **$Top = 100, &$Skip = 50** , aby zwiększyć zestaw wyników do 100 dokumentów (wartość domyślna to 50, maksimum to 1000), pomijając pierwsze dokumenty 50. Możesz sprawdzić dokument przy użyciu klucza dokumentu (listingID). 

Odwołaj, że musisz podać kryteria wyszukiwania, takie jak termin lub wyrażenie zapytania, aby uzyskać rankingowe wyniki. Zwróć uwagę, że wyniki wyszukiwania zmniejszają głębię osiągniętą w wynikach wyszukiwania.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Wyniki**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Zwróć następną partię wyników wyszukiwania" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Wyrażenia filtru (większe niż, mniejsze niż, równe)

Użyj parametru [**$Filter**](search-query-odata-filter.md) , aby określić dokładne kryteria zamiast wyszukiwania tekstu wolnego. Pole musi być przypisane do **filtru** w indeksie. Ten przykład wyszukuje sypialniami większe niż 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Wyniki**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtruj według kryteriów" border="true":::

## <a name="order-by-expressions"></a>Wyrażenia order-by

Dodaj [**$OrderBy**](search-query-odata-orderby.md) , aby sortować wyniki według innego pola niż wynik wyszukiwania. Pole musi być przypisane do **sortowania** w indeksie. Przykładowe wyrażenie, którego można użyć do przetestowania:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Wyniki**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Zmiana kolejności sortowania" border="true":::

Wyrażenia **$Filter** i **$OrderBy** są konstrukcjami OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Wnioski

W tym przewodniku szybki start użyto **Eksploratora wyszukiwania** do wykonywania zapytań względem indeksu przy użyciu interfejsu API REST.

+ Wyniki są zwracane jako pełne dokumenty JSON, dzięki czemu można w całości przeglądać Konstruowanie i zawartość dokumentu. Parametr **$SELECT** w wyrażeniu zapytania może ograniczyć liczbę zwracanych pól.

+ Dokumenty składają się ze wszystkich pól oznaczonych jako możliwy do **pobierania** w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij pozycję *realestate-US — przykład* na liście **indeksy** na stronie Przegląd wyszukiwania.

+ Zapytania o dowolnej postaci, podobne do tego, co można wprowadzać w komercyjnej przeglądarce internetowej, są przydatne do testowania środowiska użytkownika końcowego. Na przykład przy założeniu wbudowanego przykładowego indeksu realestate można wprowadzić wartość "Seattle apartamentach jeziora Waszyngton", a następnie użyć kombinacji klawiszy Ctrl-F, aby znaleźć warunki w wynikach wyszukiwania. 

+ Wyrażenia zapytań i filtrów są łączone w składni zaimplementowanej przez Wyszukiwanie poznawcze platformy Azure. Wartość domyślna to [prosta składnia](/rest/api/searchservice/simple-query-syntax-in-azure-search), ale opcjonalnie można użyć [pełnych Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) dla bardziej zaawansowanych zapytań. [Wyrażenia filtru](/rest/api/searchservice/odata-expression-syntax-for-azure-search) są składnią OData.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach i składni zapytań, użyj programu Poster lub równoważnego narzędzia do tworzenia wyrażeń zapytania, które wykorzystują więcej części interfejsu API. [Interfejs API REST usługi Search](/rest/api/searchservice/search-documents) jest szczególnie przydatny do uczenia się i eksploracji.

> [!div class="nextstepaction"]
> [Tworzenie podstawowego zapytania w programie Poster](search-get-started-rest.md)
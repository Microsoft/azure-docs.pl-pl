---
title: 'Szybki Start: Tworzenie zestawu umiejętności w Azure Portal'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Kreatora importu danych dodać umiejętności poznawcze do potoku indeksowania w usłudze Azure Wyszukiwanie poznawcze. Umiejętności obejmują optyczne rozpoznawanie znaków (OCR) i przetwarzanie języka naturalnego.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472421"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Szybki Start: Tworzenie usługi Azure Wyszukiwanie poznawcze poznawcze zestawu umiejętności w Azure Portal

Zestawu umiejętności to funkcja AI, która wyodrębnia informacje i strukturę z dużych nierozróżnianych plików tekstowych lub obrazów, i umożliwia indeksowanie i wyszukiwanie kwerend wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze. 

W tym przewodniku szybki start utworzysz usługi i dane w chmurze platformy Azure, aby utworzyć zestawu umiejętności. Gdy wszystko będzie na miejscu, uruchom kreatora **importowania danych** w portalu, aby ściągnąć wszystkie te elementy. Wynik końcowy to indeks z możliwością wyszukiwania, wypełniony danymi utworzonymi przez przetwarzanie AI, które można badać w portalu ([Eksplorator wyszukiwania](search-explorer.md)).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki Start używa platformy Azure Wyszukiwanie poznawcze, [usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/)i [usługi Azure COGNITIVE Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. 

Ponieważ obciążenie jest tak małe, Cognitive Services jest wybierane w tle, aby zapewnić bezpłatne przetwarzanie dla maksymalnie 20 transakcji. W przypadku takiego małego zestawu danych możesz pominąć tworzenie i dołączanie zasobów Cognitive Services.

1. [Pobierz przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) składające się z małego zestawu plików różnych typów. Rozpakuj pliki.

1. [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Wybierz ten sam region co usługa Azure Wyszukiwanie poznawcze, aby uniknąć naliczania opłat za przepustowość. 
   
   Wybierz typ konta StorageV2 (ogólnego przeznaczenia w wersji 2), jeśli chcesz wypróbować funkcję magazynu wiedzy później, w innym instruktażu. W przeciwnym razie wybierz dowolny typ.

1. Otwórz strony usługi BLOB Services i Utwórz kontener. Można użyć domyślnego poziomu dostępu publicznego. 

1. W kontenerze kliknij pozycję **Przekaż** , aby przekazać pliki przykładowe pobrane w pierwszym kroku. Należy zauważyć, że masz szeroką gamę typów zawartości, w tym obrazy i pliki aplikacji, które nie umożliwiają wyszukiwania pełnotekstowego w ich formatach natywnych.

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start.

Teraz można przystąpić do przenoszenia Kreatora importu danych.

## <a name="run-the-import-data-wizard"></a>Uruchom Kreatora importowania danych

Na stronie Przegląd usługi wyszukiwania kliknij pozycję **Importuj dane** na pasku poleceń, aby skonfigurować wzbogacanie poznawcze w czterech krokach.

  ![Polecenie importu danych](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Krok 1. Tworzenie źródła danych

1. W obszarze **Połącz z danymi**wybierz pozycję **Azure Blob Storage**, wybierz utworzone konto magazynu i kontener. Podaj nazwę źródła danych i użyj wartości domyślnych dla pozostałych ustawień. 

   ![Konfiguracja obiektu blob platformy Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Przejdź do następnej strony.

### <a name="step-2---add-cognitive-skills"></a>Krok 2. Dodawanie umiejętności poznawczych

Następnie skonfiguruj wzbogacanie AI, aby wywoływać OCR, analizę obrazów i przetwarzanie języka naturalnego. 

1. W tym przewodniku szybki start korzystamy z **bezpłatnego** zasobu Cognitive Services. Przykładowe dane składają się z 14 plików, więc w tym przewodniku szybki start wystarcza bezpłatny przydział 20 transakcji na Cognitive Services. 

   ![Dołączanie usług Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Rozwiń pozycję **Dodaj wzbogacania** i dokonaj czterech zaznaczeń. 

   Włącz OCR, aby dodać umiejętność analizy obrazu do strony kreatora.

   Ustaw poziom szczegółowości na strony, aby podzielić tekst na mniejsze fragmenty. Kilka umiejętności związanych z tekstem jest ograniczonych do 5 KB danych wejściowych.

   Wybierz pozycję Rozpoznawanie jednostek (ludzie, organizacje, lokalizacje) i analiza obrazu.

   ![Dołączanie usług Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Przejdź do następnej strony.

### <a name="step-3---configure-the-index"></a>Krok 3 — Konfigurowanie indeksu

Indeks zawiera zawartość z możliwością wyszukiwania, a Kreator **importu danych** zazwyczaj może utworzyć schemat przez próbkowanie źródła danych. W tym kroku zapoznaj się z wygenerowanym schematem i ewentualnie Popraw wszystkie ustawienia. Poniżej znajduje się domyślny schemat utworzony dla demonstracyjnego zestawu danych obiektów BLOB.

W przypadku tego przewodnika Szybki start kreator wykonuje dobrą pracę, ustawiając rozsądne wartości domyślne:  

+ Pola domyślne są oparte na właściwościach istniejących obiektów blob, a nowe pola zawierają dane wyjściowe wzbogacania (na `people`przykład `organizations`, `locations`,). Typy danych są wywnioskowane na podstawie metadanych i próbkowania danych.

+ Domyślny klucz dokumentu jest *metadata_storage_path* (wybrany, ponieważ pole zawiera unikatowe wartości).

+ Domyślne atrybuty są do **pobierania** i **wyszukiwania**. **Wyszukiwanie** umożliwia wyszukiwanie pełnotekstowe w polu. Pobieranie **oznacza,** że wartości pól mogą być zwracane w wynikach. W kreatorze przyjęto założenie, że chcesz, aby te pola oferowały możliwości pobierania i wyszukiwania, ponieważ zostały utworzone za pośrednictwem zestawu umiejętności.

  ![Pola indeksu](media/cognitive-search-quickstart-blob/index-fields.png)

Zwróć uwagę na przekreślenie i znak zapytania w atrybucie **Możliwość pobierania** obok pola `content`. W przypadku dokumentów obiektów blob z dużą ilością tekstu pole `content` zawiera większą część pliku, która może potencjalnie składać się z tysięcy wierszy. Takie pole jest nieporęczny w wynikach wyszukiwania i należy je wykluczyć dla tej wersji demonstracyjnej. 

Jeśli jednak chcesz przekazać zawartość pliku do kodu klienta, upewnij się, że wybrano opcję **pobierania** . W przeciwnym razie Rozważ wyczyszczenie tego `content` atrybutu w przypadku, gdy wyodrębnione `people`elementy `organizations`( `locations`takie jak,, i tak dalej) są wystarczające.

Oznaczenie pola jako **Możliwość pobierania** nie oznacza, że pole *musi* znajdować się w wynikach wyszukiwania. Można precyzyjnie kontrolować wyniki wyszukiwania za pomocą parametru zapytania **$select** w celu wybrania pól do uwzględnienia. W przypadku pól zawierających dużo tekstu, takich jak `content`, parametr **$select** to rozwiązanie, które oferuje użytkownikom aplikacji łatwe w zarządzaniu wyniki wyszukiwania, gwarantując jednocześnie, że kod klienta ma dostęp do wszystkich wymaganych informacji za pośrednictwem atrybutu **Możliwość pobierania**.
  
Przejdź do następnej strony.

### <a name="step-4---configure-the-indexer"></a>Krok 4 — Konfigurowanie indeksatora

Indeksator jest procesem wysokiego poziomu sterującym procesem indeksowania. Określa on nazwę źródła danych, docelowy indeks oraz częstotliwość wykonywania. Kreator **importu danych** tworzy kilka obiektów, a z nich jest zawsze indeksator, który można wielokrotnie uruchamiać.

1. Na stronie **indeksatora** można zaakceptować nazwę domyślną i kliknąć opcję harmonogramu **jednokrotnego** , aby natychmiast uruchomić ją. 

   ![Definicja indeksatora](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kliknij przycisk **Prześlij**, aby utworzyć i od razu uruchomić indeksator.

## <a name="monitor-status"></a>Monitorowanie stanu

Indeksowanie umiejętności poznawcze trwa dłużej niż typowe indeksowanie tekstowe, w szczególności OCR i analiza obrazu. Aby monitorować postęp, przejdź do strony przegląd i kliknij pozycję **indeksatory** na środku strony.

  ![Powiadomienie dotyczące usługi Azure Wyszukiwanie poznawcze](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Ostrzeżenia są zwykle nadawane szerokiemu zakresowi typów zawartości. Niektóre typy zawartości są nieprawidłowe dla pewnych umiejętności i niższych warstw, które często napotykają [limity indeksatora](search-limits-quotas-capacity.md#indexer-limits). Na przykład powiadomienia o obcięciu o 32 000 znaków są limitem indeksatora w warstwie Bezpłatna. Jeśli ten pokaz został uruchomiony w wyższej warstwie, wiele ostrzeżeń obcięcia wyjdzie.

Aby sprawdzić ostrzeżenia lub błędy, kliknij stan ostrzeżenia na liście indeksatorów, aby otworzyć stronę Historia wykonywania.

Na tej stronie kliknij ponownie pozycję stan ostrzeżenia, aby wyświetlić listę ostrzeżeń podobną do przedstawionego poniżej. 

  ![Lista ostrzeżeń indeksatora](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Szczegóły są wyświetlane po kliknięciu określonego wiersza stanu. To ostrzeżenie oznacza, że scalanie zostało zatrzymane po osiągnięciu maksymalnego progu (ten konkretny plik PDF jest duży).

  ![Szczegóły ostrzeżenia](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Zapytanie w Eksploratorze wyszukiwania

Po utworzeniu indeksu można uruchamiać zapytania, aby zwracać wyniki. W portalu Użyj **Eksploratora wyszukiwania** dla tego zadania. 

1. Na stronie pulpitu nawigacyjnego usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać utworzony indeks.

1. Wprowadź ciąg wyszukiwania, aby wykonać zapytanie względem indeksu, np. `search=Microsoft&$select=people,organizations,locations,imageTags`.

Wyniki są zwracane w formacie JSON, który może być pełny i trudny do odczytania, szczególnie w dużych dokumentach pochodzących z obiektów blob platformy Azure. Niektóre porady dotyczące wyszukiwania w tym narzędziu obejmują następujące techniki:

+ Dołącz `$select` , aby określić, które pola mają być uwzględniane w wynikach. 
+ Użyj kombinacji klawiszy CTRL-F, aby przeszukać w formacie JSON dla określonych właściwości lub warunków.

Ciągi zapytania są rozróżniane wielkości liter, więc jeśli zostanie wyświetlony komunikat "nieznany pole", sprawdź **pola** lub **definicję indeksu (JSON)** , aby zweryfikować nazwę i wielkość liter. 

  ![Przykład Eksploratora wyszukiwania](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Wnioski

Po utworzeniu pierwszej zestawu umiejętności i zapoznaniu się z ważnymi pojęciami przydatnymi przy tworzeniu prototypów rozwiązania do wyszukiwania przy użyciu własnych danych.

Niektóre kluczowe założenia, które, mamy nadzieję, zostały wychwycone, obejmują zależności od źródeł danych platformy Azure. Zestawu umiejętności jest powiązany z indeksatorem, a Indeksatory są oparte na platformie Azure i dla źródła. Mimo że w tym przewodniku Szybki start jest używana usługa Azure Blob Storage, możliwe są inne źródła danych platformy Azure. Aby uzyskać więcej informacji, zobacz [indeksatory w usłudze Azure wyszukiwanie poznawcze](search-indexer-overview.md). 

Innym ważnym pojęciem jest to, że umiejętności działają nad typami zawartości, a podczas pracy z zawartością heterogeniczną niektóre dane wejściowe zostaną pominięte. Ponadto duże pliki lub pola mogą przekroczyć limity indeksatora warstwy usług. Jest to normalne, aby wyświetlić ostrzeżenia w przypadku wystąpienia tych zdarzeń. 

Dane wyjściowe są kierowane do indeksu wyszukiwania i istnieje mapowanie między parami nazw-wartości utworzonymi podczas indeksowania i poszczególnych pól w indeksie. Wewnętrznie portal konfiguruje [adnotacje](cognitive-search-concept-annotations-syntax.md) i definiuje [zestaw umiejętności](cognitive-search-defining-skillset.md), ustanawiając kolejność operacji i ogólny przepływ. Te kroki są ukryte w portalu, ale po rozpoczęciu pisania kodu te koncepcje stają się istotne.

Na koniec nauczysz się, że można zweryfikować zawartość, badając indeks. Na końcu usługa Azure Wyszukiwanie poznawcze zapewnia indeks z możliwością wyszukiwania, który można badać przy użyciu [prostej](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) lub w [pełni rozszerzonej składni zapytania](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Indeks zawierający wzbogacone pola jest taki sam, jak każdy inny. Jeśli chcesz uwzględnić standardowe lub [Niestandardowe analizatory](search-analyzers.md), [Profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonimy](search-synonyms.md), [filtry aspektowe](search-filters-facets.md), wyszukiwanie geograficzne lub dowolną inną funkcję wyszukiwanie poznawcze platformy Azure, możesz to zrobić.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Umiejętności można utworzyć przy użyciu portalu, zestawu SDK platformy .NET lub interfejsu API REST. Aby dowiedzieć się więcej, wypróbuj interfejs API REST przy użyciu programu Poster i więcej przykładowych danych.

> [!div class="nextstepaction"]
> [Samouczek: Wyodrębnianie tekstu i struktury z obiektów BLOB JSON przy użyciu interfejsów API REST](cognitive-search-tutorial-blob.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub wypróbuj inny Przewodnik wzbogacania, Usuń indeksator w portalu. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji do zera na potrzeby przetwarzania Cognitive Services.
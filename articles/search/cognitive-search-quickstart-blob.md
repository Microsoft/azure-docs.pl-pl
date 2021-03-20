---
title: Utwórz zestawu umiejętności w Azure Portal
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Kreatora importu danych dodać umiejętności poznawcze do potoku indeksowania w usłudze Azure Wyszukiwanie poznawcze. Umiejętności obejmują optyczne rozpoznawanie znaków (OCR) i przetwarzanie języka naturalnego.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: d07b52d8abeab34d565ebde4bac58eec66780dce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179269"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Szybki Start: Tworzenie usługi Azure Wyszukiwanie poznawcze poznawcze zestawu umiejętności w Azure Portal

Zestawu umiejętności to funkcja oparta na formacie AI, która korzysta z modeli uczenia głębokiego do wyodrębniania informacji i struktury z dużych plików tekstowych lub obrazów, dzięki czemu zawartość można indeksować i przeszukiwać na platformie Azure Wyszukiwanie poznawcze. 

W tym przewodniku szybki start utworzysz usługi i dane w chmurze platformy Azure, aby utworzyć zestawu umiejętności. Gdy wszystko będzie na miejscu, uruchom kreatora **importowania danych** w Azure Portal, aby ściągnąć wszystkie jednocześnie. Wynik końcowy to indeks z możliwością wyszukiwania, wypełniony danymi utworzonymi przez przetwarzanie AI, które można badać w portalu ([Eksplorator wyszukiwania](search-explorer.md)).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć na miejsce następujące wymagania wstępne:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ Konto usługi Azure Storage z [magazynem obiektów BLOB](../storage/blobs/index.yml).

> [!NOTE]
> Ten przewodnik Szybki Start używa również [usługi Azure Cognitive Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. Ze względu na to, że obciążenie jest małe, Cognitive Services jest wybierana w tle, aby można było bezpłatnie przetwarzać do 20 transakcji. Oznacza to, że można wykonać to ćwiczenie bez konieczności tworzenia dodatkowego zasobu Cognitive Services.

## <a name="set-up-your-data"></a>Skonfiguruj dane

W poniższych krokach skonfiguruj kontener obiektów BLOB w usłudze Azure Storage do przechowywania plików zawartości heterogenicznej.

1. [Pobierz przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) składające się z małego zestawu plików różnych typów. Rozpakuj pliki.

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Wybierz ten sam region co usługa Azure Wyszukiwanie poznawcze, aby uniknąć naliczania opłat za przepustowość. 

   + Wybierz typ konta StorageV2 (ogólnego przeznaczenia w wersji 2), jeśli chcesz wypróbować funkcję magazynu wiedzy później, w innym instruktażu. W przeciwnym razie wybierz dowolny typ.

1. Otwórz strony usługi BLOB Services i Utwórz kontener. Można użyć domyślnego poziomu dostępu publicznego. 

1. W kontenerze kliknij pozycję **Przekaż** , aby przekazać pliki przykładowe pobrane w pierwszym kroku. Należy zauważyć, że masz szeroką gamę typów zawartości, w tym obrazy i pliki aplikacji, które nie umożliwiają wyszukiwania pełnotekstowego w ich formatach natywnych.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Pliki źródłowe w usłudze Azure Blob Storage" border="false":::

Teraz można przystąpić do przenoszenia Kreatora importu danych.

## <a name="run-the-import-data-wizard"></a>Uruchom Kreatora importowania danych

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

1. [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) i na stronie Przegląd kliknij pozycję **Importuj dane** na pasku poleceń, aby skonfigurować wzbogacanie poznawcze w czterech krokach.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Polecenie importu danych" border="false":::

### <a name="step-1---create-a-data-source"></a>Krok 1. Tworzenie źródła danych

1. W obszarze **Połącz z danymi** wybierz pozycję **Azure Blob Storage**, wybierz utworzone konto magazynu i kontener. Podaj nazwę źródła danych i użyj wartości domyślnych dla pozostałych ustawień. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Konfiguracja obiektu blob platformy Azure" border="false":::

    Przejdź do następnej strony.

### <a name="step-2---add-cognitive-skills"></a>Krok 2. Dodawanie umiejętności poznawczych

Następnie skonfiguruj wzbogacanie AI, aby wywoływać OCR, analizę obrazów i przetwarzanie języka naturalnego. 

1. W tym przewodniku szybki start korzystamy z **bezpłatnego** zasobu Cognitive Services. Przykładowe dane składają się z 14 plików, więc w tym przewodniku szybki start wystarcza bezpłatny przydział 20 transakcji na Cognitive Services. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Dołącz Cognitive Services Dołącz usługę podstawową" border="false":::

1. Rozwiń pozycję **Dodaj wzbogacania** i dokonaj czterech zaznaczeń. 

   Włącz OCR, aby dodać umiejętność analizy obrazu do strony kreatora.

   Ustaw poziom szczegółowości na strony, aby podzielić tekst na mniejsze fragmenty. Kilka umiejętności związanych z tekstem jest ograniczonych do 5 KB danych wejściowych.

   Wybierz pozycję Rozpoznawanie jednostek (ludzie, organizacje, lokalizacje) i analiza obrazu.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Dołącz Cognitive Services wybierz usługi dla zestawu umiejętności" border="false":::

   Przejdź do następnej strony.

### <a name="step-3---configure-the-index"></a>Krok 3 — Konfigurowanie indeksu

Indeks zawiera zawartość z możliwością wyszukiwania, a Kreator **importu danych** zazwyczaj może utworzyć schemat przez próbkowanie źródła danych. W tym kroku zapoznaj się z wygenerowanym schematem i ewentualnie Popraw wszystkie ustawienia. Poniżej znajduje się domyślny schemat utworzony dla demonstracyjnego zestawu danych obiektów BLOB.

W przypadku tego przewodnika Szybki start kreator wykonuje dobrą pracę, ustawiając rozsądne wartości domyślne:  

+ Pola domyślne są oparte na właściwościach istniejących obiektów blob, a nowe pola zawierają dane wyjściowe wzbogacania (na przykład,, `people` `organizations` `locations` ). Typy danych są wywnioskowane na podstawie metadanych i próbkowania danych.

+ Domyślny klucz dokumentu jest *metadata_storage_path* (wybrany, ponieważ pole zawiera unikatowe wartości).

+ Domyślne atrybuty są do **pobierania** i **wyszukiwania**. **Wyszukiwanie** umożliwia wyszukiwanie pełnotekstowe w polu. Pobieranie **oznacza,** że wartości pól mogą być zwracane w wynikach. W kreatorze przyjęto założenie, że chcesz, aby te pola oferowały możliwości pobierania i wyszukiwania, ponieważ zostały utworzone za pośrednictwem zestawu umiejętności.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Pola indeksu" border="false":::

Zwróć uwagę na przekreślenie i znak zapytania w atrybucie możliwym do **pobierania** według `content` pola. W przypadku dokumentów obiektów blob z dużą ilością tekstu pole `content` zawiera większą część pliku, która może potencjalnie składać się z tysięcy wierszy. Takie pole jest nieporęczny w wynikach wyszukiwania i należy je wykluczyć dla tej wersji demonstracyjnej. 

Jeśli jednak chcesz przekazać zawartość pliku do kodu klienta, upewnij się, że wybrano opcję **pobierania** . W przeciwnym razie Rozważ wyczyszczenie tego atrybutu w `content` przypadku, gdy wyodrębnione elementy (takie jak `people` ,, `organizations` `locations` i tak dalej) są wystarczające.

Oznaczenie pola jako **Możliwość pobierania** nie oznacza, że pole *musi* znajdować się w wynikach wyszukiwania. Można precyzyjnie kontrolować wyniki wyszukiwania za pomocą parametru zapytania **$select** w celu wybrania pól do uwzględnienia. W przypadku pól zawierających dużo tekstu, takich jak `content`, parametr **$select** to rozwiązanie, które oferuje użytkownikom aplikacji łatwe w zarządzaniu wyniki wyszukiwania, gwarantując jednocześnie, że kod klienta ma dostęp do wszystkich wymaganych informacji za pośrednictwem atrybutu **Możliwość pobierania**.
  
Przejdź do następnej strony.

### <a name="step-4---configure-the-indexer"></a>Krok 4 — Konfigurowanie indeksatora

Indeksator jest procesem wysokiego poziomu sterującym procesem indeksowania. Określa on nazwę źródła danych, docelowy indeks oraz częstotliwość wykonywania. Kreator **importu danych** tworzy kilka obiektów, a z nich jest zawsze indeksator, który można wielokrotnie uruchamiać.

1. Na stronie **indeksatora** można zaakceptować nazwę domyślną i kliknąć opcję harmonogramu **jednokrotnego** , aby natychmiast uruchomić ją. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Definicja indeksatora" border="false":::

1. Kliknij przycisk **Prześlij**, aby utworzyć i od razu uruchomić indeksator.

## <a name="monitor-status"></a>Monitorowanie stanu

Indeksowanie umiejętności poznawcze trwa dłużej niż typowe indeksowanie tekstowe, w szczególności OCR i analiza obrazu. Aby monitorować postęp, przejdź do strony przegląd i kliknij pozycję **indeksatory** na środku strony.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Powiadomienie dotyczące usługi Azure Wyszukiwanie poznawcze" border="false":::

Ostrzeżenia są zwykle nadawane szerokiemu zakresowi typów zawartości. Niektóre typy zawartości są nieprawidłowe dla pewnych umiejętności i niższych warstw, które często napotykają [limity indeksatora](search-limits-quotas-capacity.md#indexer-limits). Na przykład powiadomienia o obcięciu o 32 000 znaków są limitem indeksatora w warstwie Bezpłatna. Jeśli ten pokaz został uruchomiony w wyższej warstwie, wiele ostrzeżeń obcięcia wyjdzie.

Aby sprawdzić ostrzeżenia lub błędy, kliknij stan ostrzeżenia na liście indeksatorów, aby otworzyć stronę Historia wykonywania.

Na tej stronie kliknij ponownie pozycję stan ostrzeżenia, aby wyświetlić listę ostrzeżeń podobną do przedstawionego poniżej. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Lista ostrzeżeń indeksatora" border="false":::

Szczegóły są wyświetlane po kliknięciu określonego wiersza stanu. To ostrzeżenie oznacza, że scalanie zostało zatrzymane po osiągnięciu maksymalnego progu (ten konkretny plik PDF jest duży).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Szczegóły ostrzeżenia" border="false":::

## <a name="query-in-search-explorer"></a>Zapytanie w Eksploratorze wyszukiwania

Po utworzeniu indeksu można uruchamiać zapytania, aby zwracać wyniki. W portalu Użyj **Eksploratora wyszukiwania** dla tego zadania. 

1. Na stronie pulpitu nawigacyjnego usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać utworzony indeks.

1. Wprowadź ciąg wyszukiwania, aby wykonać zapytanie względem indeksu, np. `search=Microsoft&$select=people,organizations,locations,imageTags`.

Wyniki są zwracane w formacie JSON, który może być pełny i trudny do odczytania, szczególnie w dużych dokumentach pochodzących z obiektów blob platformy Azure. Niektóre porady dotyczące wyszukiwania w tym narzędziu obejmują następujące techniki:

+ Dołącz `$select` , aby określić, które pola mają być uwzględniane w wynikach. 
+ Użyj kombinacji klawiszy CTRL-F, aby przeszukać w formacie JSON dla określonych właściwości lub warunków.

Ciągi zapytania są rozróżniane wielkości liter, więc jeśli zostanie wyświetlony komunikat "nieznany pole", sprawdź **pola** lub **definicję indeksu (JSON)** , aby zweryfikować nazwę i wielkość liter. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Przykład Eksploratora wyszukiwania" border="false":::

## <a name="takeaways"></a>Wnioski

Po utworzeniu pierwszej zestawu umiejętności i zapoznaniu się z ważnymi pojęciami przydatnymi przy tworzeniu prototypów rozwiązania do wyszukiwania przy użyciu własnych danych.

Niektóre kluczowe założenia, które, mamy nadzieję, zostały wychwycone, obejmują zależności od źródeł danych platformy Azure. Zestawu umiejętności jest powiązany z indeksatorem, a Indeksatory są oparte na platformie Azure i dla źródła. Mimo że w tym przewodniku Szybki start jest używana usługa Azure Blob Storage, możliwe są inne źródła danych platformy Azure. Aby uzyskać więcej informacji, zobacz [indeksatory w usłudze Azure wyszukiwanie poznawcze](search-indexer-overview.md). 

Innym ważnym pojęciem jest to, że umiejętności działają nad typami zawartości, a podczas pracy z zawartością heterogeniczną niektóre dane wejściowe zostaną pominięte. Ponadto duże pliki lub pola mogą przekroczyć limity indeksatora warstwy usług. Jest to normalne, aby wyświetlić ostrzeżenia w przypadku wystąpienia tych zdarzeń. 

Dane wyjściowe są kierowane do indeksu wyszukiwania i istnieje mapowanie między parami nazw-wartości utworzonymi podczas indeksowania i poszczególnych pól w indeksie. Wewnętrznie portal konfiguruje [adnotacje](cognitive-search-concept-annotations-syntax.md) i definiuje [zestaw umiejętności](cognitive-search-defining-skillset.md), ustanawiając kolejność operacji i ogólny przepływ. Te kroki są ukryte w portalu, ale po rozpoczęciu pisania kodu te koncepcje stają się istotne.

Na koniec nauczysz się, że można zweryfikować zawartość, badając indeks. Na końcu usługa Azure Wyszukiwanie poznawcze zapewnia indeks z możliwością wyszukiwania, który można badać przy użyciu [prostej](/rest/api/searchservice/simple-query-syntax-in-azure-search) lub w [pełni rozszerzonej składni zapytania](/rest/api/searchservice/lucene-query-syntax-in-azure-search). Indeks zawierający wzbogacone pola jest taki sam, jak każdy inny. Jeśli chcesz uwzględnić standardowe lub [Niestandardowe analizatory](search-analyzers.md), [Profile oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonimy](search-synonyms.md), [filtry aspektowe](search-filters-facets.md), wyszukiwanie geograficzne lub dowolną inną funkcję wyszukiwanie poznawcze platformy Azure, możesz to zrobić.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Umiejętności można utworzyć przy użyciu portalu, zestawu SDK platformy .NET lub interfejsu API REST. Aby dowiedzieć się więcej, wypróbuj interfejs API REST przy użyciu programu Poster i więcej przykładowych danych.

> [!div class="nextstepaction"]
> [Samouczek: Wyodrębnianie tekstu i struktury z obiektów BLOB JSON przy użyciu interfejsów API REST ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub wypróbuj inny Przewodnik wzbogacania, Usuń indeksator w portalu. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji do zera na potrzeby przetwarzania Cognitive Services.
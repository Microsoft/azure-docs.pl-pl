---
title: 'Instrukcje: wyszukiwanie Data Catalog'
description: Ten artykuł zawiera omówienie sposobu przeszukiwania wykazu danych.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554026"
---
# <a name="search-the-azure-purview-data-catalog"></a>Przeszukaj Data Catalog Azure kontrolą

W tym artykule opisano sposób korzystania z różnych funkcji wyszukiwania w usłudze Azure kontrolą Data Catalog.

## <a name="search-the-catalog-for-assets"></a>Wyszukaj w katalogu zasoby

Kroki prowadzące do przeprowadzenia wyszukiwania zasobów to:

1. [Otwórz okno dialogowe wyszukiwania zasobów](#open-the-asset-search-dialog) , wybierając pozycję **katalog wyszukiwania**.
1. [Wprowadź terminy wyszukiwania](#enter-search-terms) , aby znaleźć zasoby o cechach zgodnych z warunkami.
1. [Ustaw szybkie filtry](#set-quick-filters) , aby zawęzić wyszukiwanie.
1. [Rozpocznij wyszukiwanie](#start-the-search) i przejdź do wyników wyszukiwania.

Nie ma znaczenia, czy należy ustawić szybkie filtry przed lub po wprowadzeniu wyszukiwanych terminów.

W przypadku braku wyszukiwanych terminów i braku filtrów wyniki wyszukiwania obejmują wszystkie elementy zawartości.

### <a name="open-the-asset-search-dialog"></a>Otwórz okno dialogowe wyszukiwania zasobów

Otwórz okno dialogowe wyszukiwania zasobów, wybierając pozycję **katalog wyszukiwania**.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Poniżej elementu &quot;Search Catalog&quot; istnieje lewe okienko z filtrami wyszukiwania oraz okienko po prawej stronie z niedawnymi wyszukiwaniami." border="true":::

W oknie dialogowym wyszukiwania są wyświetlane szybkie filtry, historia wyszukiwania oraz lista ostatnio używanych zasobów.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="Lista wyszukiwania znajduje się w prawym okienku poniżej katalogu wyszukiwania." border="true":::

### <a name="enter-search-terms"></a>Wprowadź terminy wyszukiwania

Wprowadź co najmniej jeden termin wyszukiwania w **katalogu wyszukiwania**. Podczas wpisywania pasujące terminy wyszukiwania od ostatnich wyszukiwań są wyświetlane w **ostatnich wyszukiwaniach**, sugerowane terminy wyszukiwania są wyświetlane w obszarze **sugestie wyszukiwania** i pasujące zasoby danych są wymienione w **sugestii zasobów**.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Zrzut ekranu przedstawiający wyniki wyszukiwania wprowadzonego w polu katalog wyszukiwania":::

Wyniki wyszukiwania obejmują tylko zasoby z co najmniej jedną charakterystyką zgodną z wyszukiwanymi terminami. Te cechy obejmują nazwę zasobu, typ zasobu, klasyfikacje i kontakty.

#### <a name="types-of-search-criteria"></a>Typy kryteriów wyszukiwania

Usługa Azure kontrolą obsługuje następujące typy kryteriów wyszukiwania.

> [!Note]
> Zawsze określaj operatory logiczne (**i**, **lub**, **nie**) we wszystkich wersalikach. W przeciwnym razie przypadek nie ma znaczenia lub nie rób dodatkowych spacji.

- **gałąź**: Znajdź dokumenty zawierające **gałąź Hive**.
- **baza danych programu Hive**: Znajdź dokumenty zawierające dokładnie **bazę danych Hive**.
- **Hive lub baza danych**: Znajdź dokumenty, które zawierają **gałęzie** lub **bazy danych**.
- Baza danych Hive **i baza danych** programu **Hive && Database**: Znajdź dokumenty, które zawierają **bazy danych** **Hive** i Database.
- **Hive i (baza danych lub magazyn)**: Znajdź dokumenty, które zawierają **gałąź Hive** , **bazę danych** lub **Magazyn** albo oba te elementy.
- **GAŁĄŹ nie bazy danych**: Znajdź dokumenty zawierające **gałąź Hive**, ale nie **bazę danych**.
- **HIV**: Znajdź dokumenty zawierające wyraz rozpoczynający się od **HIV**. Na przykład **HIV**, **Hive**, **hivbar** (* jest symbolem wieloznacznym, który pasuje do dowolnej liczby znaków).

### <a name="set-quick-filters"></a>Ustaw szybkie filtry

Lista wyników wyszukiwania jest określana na podstawie wyszukiwanych terminów wprowadzonych w **katalogu wyszukiwania** oraz wartości wybranych dla szybkich filtrów.

Szybki filtr ogranicza listę wyników wyszukiwania do zasobów, które mają wybraną wartość właściwości. Filtr ma listę rozwijaną i pole tekstowe. Lista rozwijana zawiera wartości właściwości, które znajdują się w *bieżących* wynikach wyszukiwania. Obok każdej wartości na liście jest liczba zasobów w bieżącym wynikach wyszukiwania, które mają tę wartość. Jeśli wybierzesz wartość z listy, wyniki wyszukiwania zostaną ograniczone do zasobów o tej wartości. Można wybrać tylko jedną wartość.

Bieżące wyniki wyszukiwania używane podczas tworzenia listy rozwijanej są określane przez:

- Terminy wyszukiwania wprowadzone w **katalogu wyszukiwania**. 
- Wartości wybrane w szybkich filtrach.

Oto przykład szybkiego filtru "typ zasobu".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Przykład szybkiego filtru typu elementu zawartości." border="true":::

Możesz wpisać tekst w polu tekstowym, aby ograniczyć wartości z listy rozwijanej do wartości, które pasują lub częściowo pasują do tekstu. Aby zapoznać się z przykładami dotyczącymi używania pola tekstowego, zobacz [Szybki filtr wyszukiwania: filtrowanie według typu zasobu](#search-quick-filter-filter-by-asset-type)i [Wyszukiwanie szybkie filtrowanie: Filtruj według klasyfikacji](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Szybki filtr wyszukiwania: Filtruj według typu zasobu

Aby filtrować według typu zasobu, Użyj szybkiego filtru **typu elementu zawartości** . Na liście rozwijanej są wyświetlane typy zasobów Znalezione w bieżących wynikach wyszukiwania zgodnie z warunkami wyszukiwania i szybkimi filtrami. Dla każdego typu wyświetlana jest liczba zasobów tego typu.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Wyróżnione są szybkie filtry typu elementu zawartości. Pokazuje on typy zasobów i liczbę dla każdej z nich." border="true":::

Wybierz typ zasobu, aby ograniczyć wyniki wyszukiwania do zasobów tego typu. Można wybrać tylko jeden typ.

Aby wyświetlić tylko typy zasobów, których nazwy pasują do ciągu, wprowadź ciąg w polu tekstowym. Aby na przykład wyświetlić tylko typy zasobów z **SQL** , wpisz **SQL**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Okienko szybkie filtry zawiera dane SQL dla elementu &quot;typ zasobu&quot;. Lista zasobów zawierających SQL zawiera trzy wpisy." border="true":::

Wybierz typ zasobu, aby ograniczyć wyniki wyszukiwania do zasobów tego typu. Można wybrać tylko jeden typ.

#### <a name="search-quick-filter-filter-by-classification"></a>Szybki filtr wyszukiwania: Filtruj według klasyfikacji

Aby filtrować według klasyfikacji zasobów, Użyj szybkiego filtru **klasyfikacji** . Lista rozwijana zawiera klasyfikacje, które zostały przypisane do co najmniej jednego zasobu w bieżącym wynikach wyszukiwania, zgodnie z warunkami wyszukiwania i szybkimi filtrami. Dla każdej klasyfikacji jest wyświetlana liczba zasobów przypisanych do klasyfikacji.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Szybkie filtry klasyfikacji są wyróżnione." border="true":::

Wybierz klasyfikację, aby ograniczyć wyniki wyszukiwania do zasobów przypisanych do tej klasyfikacji. Można wybrać tylko jedną klasyfikację.

Aby wyświetlić tylko klasyfikacje, których nazwy pasują do ciągu, wprowadź ciąg w polu tekstowym. Na przykład, aby wyświetlić tylko klasyfikacje z **liczbą** w swoich nazwach, wprowadź **numer**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="W okienku Szybkie filtry klasyfikacja jest równa &quot;Bank&quot;, a wymienione klasyfikacje zawierają tę wartość." border="true":::

Wybierz klasyfikację, aby ograniczyć wyniki wyszukiwania do zasobów, do których przypisano tę klasyfikację. Nie można wybrać więcej niż jednej klasyfikacji.

#### <a name="search-quick-filter-filter-by-contacts"></a>Szybki filtr wyszukiwania: Filtruj według kontaktów

*Kontakt* to osoba, która jest przypisana do elementu zawartości jako właściciel lub ekspert. Po wyświetleniu szczegółów zasobu kontakty są wyświetlane na karcie **kontakty** .

Istnieją dwa sposoby wyszukiwania zasobów, do których przypisano określony kontakt.

- Wprowadź całość lub część nazwy kontaktu w **katalogu wyszukiwania** i Przeszukaj. Wyniki wyszukiwania będą zawierać zasoby zawierające kontakty, których nazwy są zgodne z wyszukiwanymi terminami.
- Wybierz kontakt z zainteresowaniami w szybkim filtrze **kontaktu** i wykonaj wyszukiwanie.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="Wartość osoby w okienku Szybkie filtry to &quot;Darren&quot;. W okienku sugestie znajdują się trzy sugestie." border="true":::

## <a name="search-example"></a>Przykład wyszukiwania

Rozważmy hipotetyczny przykład, aby sprawdzić, jak wyszukiwane terminy i szybkie Filtry umożliwiają określanie wyników wyszukiwania. W szczególności będziemy monitorować liczbę typów zasobów **BLOB Storage platformy Azure**.

- Pierwsze wyszukiwanie nie ma wprowadzonych terminów wyszukiwania i nie wybrano żadnych wartości z szybkich filtrów. Wyszukiwanie spowoduje znalezienie wszystkich zasobów w katalogu. Lista wyników wyszukiwania i **Typ zasobu** szybkie odfiltrowanie:

    - Lista wyników wyszukiwania ma 164 230 zasobów, czyli wszystkich zasobów w katalogu.
    - Lista rozwijana **Typ elementu zawartości** zawiera 43 wpisów. Są to wszystkie typy zasobów w katalogu. Ponieważ każdy element zawartości ma jeden i tylko jeden typ, suma liczby typów zasobów 43 wynosi 164 230.
    - Typ elementu zawartości **platformy Azure Blob Storage** to pierwszy wpis na liście rozwijanej szybkiego filtru **typu elementu zawartości** . Wartości są uporządkowane według liczby, największej, więc **platforma Azure Blob Storage** jest najbardziej popularnym typem elementu zawartości. Liczba to 118 174.

- Teraz wprowadzimy **Parquet** w **katalogu wyszukiwania** i wykorzystasz inne wyszukiwanie. Wyniki wyszukiwania obejmują tylko zasoby o cechach zgodnych z **Parquet**. Zmniejsza to liczbę wszystkich liczników w następujący sposób:

    - Lista wyników wyszukiwania zawiera 493 zasobów. Tylko 493 zasobów 164 230 w katalogu ma właściwości, które pasują do "Parquet".
    - Lista rozwijana **Typ elementu zawartości** zawiera 15 wpisów. Co najmniej jeden z 493 zasobów składa się z jednego z tych 15 typów, a suma wartości 15 typów wynosi 493.
    - Istnieją 456 zasobów typu **Azure Blob Storage**. Pozostałe zasoby 37 (493 minus 456) są jednym z pozostałych 14 typów.

- Teraz spójrzmy na listę rozwijaną innego szybkiego filtru, **klasyfikacji**:

    - Na liście wyników wyszukiwania znajdują się 12 klasyfikacji dla zasobów 493. Liczby zasobów 493 nie są sumowane do 493, ponieważ dowolna liczba klasyfikacji może być przypisana do elementu zawartości.
    - Klasyfikacja **nazwy osoby** jest przypisana do 36 zasobów, więcej niż wszystkie inne klasyfikacje.

- Wybieramy klasyfikację **nazwy osoby** . Lista wyników wyszukiwania spadnie do 36 zasobów zgodnie z oczekiwaniami, ponieważ liczba dla **nazwy osoby** to 36. Żaden z tych wyników nie jest typu **Azure Blob Storage**.

Firma Microsoft może dokończyć, że nie ma elementu zawartości, którego typem jest **BLOB Storage platformy Azure** , który jest zgodny z **Parquet** i który ma klasyfikację **nazwiska osoby**.

## <a name="start-the-search"></a>Rozpocznij wyszukiwanie

Podczas wyszukiwania terminy wyszukiwania wprowadzane w **katalogu wyszukiwania** są dopasowywane do właściwości zasobów. Te cechy obejmują nazwę, typ, klasyfikację i kontakty. Elementy zawartości o pasujących właściwościach pojawiają się na liście wyników wyszukiwania, chyba że zostaną wykluczone przez szybki filtr.

Po wprowadzeniu wyszukiwanych terminów i ustawieniu szybkich filtrów Rozpocznij wyszukiwanie w jeden z następujących sposobów:

- Aby wyszukać na podstawie wprowadzonych warunków, wybierz ikonę wyszukiwania ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), naciśnij klawisz **Enter** lub wybierz pozycję **Wyświetl wyniki wyszukiwania**.
- Aby wyszukać warunki z poprzedniego wyszukiwania, wybierz je z **ostatnich wyszukiwań**.
- Aby wyszukać przy użyciu sugerowanych warunków, wybierz je z **sugestii wyszukiwania**.

Wybierz element zawartości z **sugestii dotyczących zasobów** , aby przejść bezpośrednio do strony szczegółów elementu zawartości. Żadne wyszukiwanie nie jest wykonywane.

Lista wyników dla sugestii i wyszukiwań użytkowników może się nieco różnić. Wyniki na liście sugestii są oparte na dopasowaniach rozmytych, a wyniki wyszukiwania inicjowane przez użytkownika są oparte na dokładnych dopasowaniach.

Podczas wyszukiwania zostanie wyświetlona strona **wyniki wyszukiwania** zawierająca listę zasobów znalezionych przez wyszukiwanie.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Zrzut ekranu przedstawiający wyniki wyszukiwania dla wartości wyszukiwania contoso.":::

Aby wyświetlić szczegóły zasobu, wybierz nazwę elementu zawartości.

Użyj kontrolek na dolnej stronie wyników wyszukiwania, aby przejść do innych stron wyników wyszukiwania.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania po stronach wyników wyszukiwania.":::

### <a name="sort-search-results"></a>Sortuj wyniki wyszukiwania

Użyj **sortowania Sortuj według** , aby posortować wyniki wyszukiwania według **istotności** lub **nazwy**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Zrzut ekranu przedstawiający sposób sortowania wyników wyszukiwania. Na potrzeby tego przykładu listy rozwijanej Sortuj według są ustawione na wartość istotność.":::

### <a name="search-results-dynamic-filters"></a>Filtry dynamiczne wyników wyszukiwania

Okienko **filtru** na stronie **wyników wyszukiwania** zawiera filtry, które zapewniają dynamiczne filtrowanie zasobów na liście wyników wyszukiwania. Filtrowanie jest dynamiczne w tym, że dodatkowe filtry mogą być wyświetlane na podstawie wybranych opcji filtrowania.

Filtry dynamiczne mają pole wyboru dla każdej wartości na liście rozwijanej. Użyj tych pól wyboru, aby odfiltrować dowolną liczbę wartości.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Filtr dynamiczny wyników wyszukiwania: Filtruj według typu zasobu

W przypadku wybrania typu zasobów na liście rozwijanej **Typ elementu zawartości** zostaną wyświetlone filtry dynamiczne, które zapewniają dodatkowe sposoby zawężania wyników wyszukiwania. Filtry różnią się w zależności od wybranego typu elementu zawartości. Na przykład w przypadku wybrania **Azure SQL Database** są wyświetlane filtry dynamiczne dla serwera, bazy danych i schematu. Wartości w tych filtrach pochodzą z zasobów w wynikach wyszukiwania wybranego typu.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Element filtru Azure SQL Database jest jedynym wybranym elementem typu elementu zawartości. Zostanie również wyróżniony wynik wyszukiwania tego typu elementu zawartości." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Filtr dynamiczny wyników wyszukiwania: Filtruj według klasyfikacji

Każda Klasyfikacja na liście **klasyfikacji** ma zastosowanie do co najmniej jednego elementu na liście wyników wyszukiwania. Wybierz co najmniej jedną klasyfikację, aby zawęzić wyniki wyszukiwania do zasobów wybranych klasyfikacji.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Filtr klasyfikacji elementu &quot;wyniki wyszukiwania&quot; jest wyróżniony." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Edytuj i Usuń filtry wyników wyszukiwania

Aby usunąć filtr, usuń zaznaczenie pola wyboru obok nazwy filtru.

### <a name="recently-accessed-assets"></a>Ostatnio używane zasoby

W sekcji **ostatnio używane** w rozwiniętym polu wyszukiwania są wyświetlane ostatnio używane zasoby, jeśli istnieją.

- Wybierz pozycję **Wyświetl wszystko** w **ostatnio używanej** sekcji, aby wyświetlić pełną listę ostatnio używanych zasobów.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Zrzut ekranu przedstawiający sekcję ostatnio używane w rozwiniętym polu wyszukiwania.":::

   Zostanie wyświetlona lista ostatnio używanych zasobów.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Zrzut ekranu przedstawiający listę ostatnio używanych zasobów.":::

- Aby filtrować według nazwy, wprowadź ciąg wyszukiwania w polu **Filtruj według nazwy**.

- Aby usunąć elementy z listy, zaznacz je za pomocą pól wyboru, a następnie wybierz pozycję **Usuń**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Zrzut ekranu przedstawiający sposób usuwania elementów z listy ostatnio używanych zasobów.":::

- Aby wyczyścić całą listę, wybierz pozycję **Wyczyść**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Zrzut ekranu przedstawiający sposób czyszczenia listy ostatnio używanych zasobów":::

### <a name="search-assets"></a>Wyszukaj zasoby

Wiele stron innych niż **Strona główna** ma pole **zasobów wyszukiwania** u góry. Na przykład poniżej znajduje się Strona szczegóły zasobów z wyróżnionymi **zasobami wyszukiwania** .

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Zrzut ekranu przedstawiający stronę szczegółów zasobu z wyróżnionymi zasobami wyszukiwania":::

Wybierz pozycję **Wyszukaj zasoby** , aby uruchomić pole wyszukiwania, takie jak ta, która jest uzyskiwana z **katalogu wyszukiwania** w **domu**, przy użyciu tych samych funkcji.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Zrzut ekranu przedstawiający pole rozszerzone elementy zawartości wyszukiwania.":::

## <a name="next-steps"></a>Następne kroki

- [Tworzenie, importowanie i eksportowanie terminów słownika](how-to-create-import-export-glossary.md)
- [Zarządzanie szablonami terminów dla słownika biznesowego](how-to-manage-term-templates.md)

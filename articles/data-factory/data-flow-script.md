---
title: Mapowanie skryptu przepływu danych
description: Przegląd kodu skryptu przepływu danych Data Factory w języku związanym z kodem
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534825"
---
# <a name="data-flow-script-dfs"></a>Skrypt przepływu danych (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Skrypt przepływu danych (DFS) to metadane bazowe, podobne do języka kodowania, które są używane do wykonywania transformacji zawartych w przepływie danych mapowania. Każda transformacja jest reprezentowana przez serię właściwości, które zawierają informacje niezbędne do poprawnego uruchomienia zadania. Skrypt jest widoczny i można go edytować w ramach funkcji ADF, klikając przycisk "skrypt" na górnej Wstążce interfejsu użytkownika przeglądarki.

![Przycisk skryptu](media/data-flow/scriptbutton.png "Przycisk skryptu")

Na przykład, `allowSchemaDrift: true,` w transformacji źródłowej instruuje usługę, aby dołączać wszystkie kolumny ze źródłowego zestawu danych w przepływie danych, nawet jeśli nie znajdują się one w projekcji schematu.

## <a name="use-cases"></a>Przypadki zastosowań
System plików DFS jest automatycznie tworzony przez interfejs użytkownika. Kliknięcie przycisku skryptu umożliwia wyświetlenie i dostosowanie skryptu. Możesz również generować skrypty poza interfejsem użytkownika funkcji ADF, a następnie przekazać je do polecenia cmdlet programu PowerShell. Podczas debugowania złożonych przepływów danych może okazać się łatwiejsze skanowanie kodu skryptu, a nie skanowanie wykresu interfejsu użytkownika w celu przedstawienia przepływów.

Oto kilka przykładowych przypadków użycia:
- Programowo wytwarzający wiele przepływów danych, które są dość podobne, czyli przepływy danych "stemplowanie".
- Złożone wyrażenia, które trudno zarządzać w interfejsie użytkownika lub są wynikiem problemów ze sprawdzaniem poprawności.
- Debugowanie i lepsze zrozumienie różnych błędów zwróconych podczas wykonywania.

Podczas kompilowania skryptu przepływu danych, który ma być używany z programem PowerShell lub interfejsem API, należy zwinąć sformatowany tekst w jeden wiersz. Tabulatory i znaki nowego wiersza można przechowywać jako znak ucieczki. Ale tekst musi być sformatowany do dopasowania wewnątrz właściwości JSON. Istnieje przycisk w interfejsie użytkownika edytora skryptów u dołu, który sformatuje skrypt jako jeden wiersz.

![Kopiuj](media/data-flow/copybutton.png "Kopiuj")

## <a name="how-to-add-transforms"></a>Jak dodać przekształcenia
Dodawanie transformacji wymaga trzech podstawowych kroków: dodanie podstawowych danych transformacji, przekierowanie strumienia wejściowego, a następnie ponowne przekierowanie strumienia wyjściowego. Może to być widoczne najłatwiej na przykład.
Załóżmy, że zaczynamy od prostego źródła do przepływu danych ujścia, tak jak przedstawiono poniżej:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Jeśli zdecydujesz się dodać transformację pochodną, najpierw musimy utworzyć podstawowy tekst transformacji, który ma proste wyrażenie umożliwiające dodanie nowej jednopisanej kolumny o nazwie `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Następnie zajmiemy istniejący system plików DFS i dodamy transformację:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

A teraz ponownie kierujemy strumień przychodzący, identyfikując, który przekształcenie ma mieć nowe przekształcenie po (w tym przypadku `source1` ) i kopiując nazwę strumienia do nowej transformacji:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Wreszcie zidentyfikujemy transformację, którą chcemy po tej nowej transformacji, i Zastąp swój strumień wejściowy (w tym przypadku `sink1` ) nazwą strumienia wyjściowego dla nowej transformacji:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Podstawy systemu plików DFS
System plików DFS składa się z szeregu połączonych transformacji, w tym źródeł, ujść i różnych osób, które mogą dodawać nowe kolumny, filtrować dane, dołączać dane i wiele innych. Zwykle skrypt rozpoczynający się od co najmniej jednego źródła, po którym następuje wiele przekształceń i kończący się na jednym lub większej liczbie zlewów.

Wszystkie źródła mają tę samą konstrukcję podstawową:
```
source(
  source properties
) ~> source_name
```

Na przykład proste źródło z trzema kolumnami (movieId, title, gatunek) byłoby następujące:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Wszystkie przekształcenia inne niż źródła mają tę samą konstrukcję podstawową:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Na przykład prostą transformację pochodną, która przyjmuje kolumnę (tytuł) i zastępuje ją wielką wersją, będzie następująca:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

I ujścia bez schematu zostałby po prostu:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Fragmenty kodu skryptu

Fragmenty kodu skryptu są współdzielnym kodem skryptu przepływu danych, którego można użyć do udostępniania wielu przepływów danych. Ten film wideo poniżej przedstawia, jak używać fragmentów skryptów i skryptu przepływu danych do kopiowania i wklejania fragmentów skryptów za wykresami przepływu danych:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Zagregowane statystyki podsumowania
Dodaj zagregowaną transformację do przepływu danych o nazwie "SummaryStats", a następnie wklej poniższy kod do funkcji agregującej w skrypcie, zastępując istniejący SummaryStats. Spowoduje to udostępnienie ogólnego wzorca dla statystyk podsumowania profilu danych.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Możesz również użyć poniższego przykładu, aby policzyć liczbę unikatowych i liczbę odrębnych wierszy danych. Poniższy przykład można wkleić do przepływu danych z transformację agregacji o nazwie ValueDistAgg. W tym przykładzie użyta zostanie kolumna o nazwie "title". Pamiętaj, aby zastąpić "title" kolumną ciągu w danych, których chcesz użyć, aby uzyskać liczbę wartości.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Uwzględnij wszystkie kolumny w agregacji
Jest to ogólny wzorzec agregujący, który pokazuje, jak można zachować pozostałe kolumny w metadanych wyjściowych podczas kompilowania agregacji. W tym przypadku używamy ```first()``` funkcji do wybrania pierwszej wartości w każdej kolumnie, której nazwa nie jest "film". Aby użyć tej metody, należy utworzyć transformację agregacji o nazwie DistinctRows, a następnie wkleić ją w skrypcie nad istniejącym skryptem agregującym DistinctRows.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Utwórz odcisk palca skrótu wiersza 
Użyj tego kodu w skrypcie przepływu danych, aby utworzyć nową kolumnę pochodną o nazwie ```DWhash``` , która generuje ```sha1``` skrót trzech kolumn.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Możesz też użyć poniższego skryptu, aby wygenerować skrót wiersza przy użyciu wszystkich kolumn znajdujących się w strumieniu, bez konieczności podania nazwy każdej kolumny:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg równoważne
Ten kod będzie pełnić funkcję T-SQL ```string_agg()``` i agreguje wartości ciągów do tablicy. Następnie można rzutować tę tablicę na ciąg, który ma być używany z miejscami docelowymi SQL.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Liczba aktualizacji, upserts, wstawienia, usunięcia
W przypadku korzystania z przekształcenia ALTER Row można obliczyć liczbę aktualizacji, upserts, wstawienia, usunąć wynik z zasad zmiany wiersza. Dodaj transformację zagregowaną po zmianie wiersza i wklej ten skrypt przepływu danych do definicji agregacji dla tych liczników.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Unikatowy wiersz ze wszystkimi kolumnami
Ten fragment kodu doda nowe Przekształcenie agregacji do przepływu danych, który będzie przyjmować wszystkie przychodzące kolumny, wygenerowanie skrótu, który jest używany do grupowania, aby wyeliminować duplikaty, a następnie podać pierwsze wystąpienie każdego duplikatu jako dane wyjściowe. Nie musisz jawnie określać nazw kolumn, zostaną one automatycznie wygenerowane na podstawie przychodzącego strumienia danych.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Sprawdź pod kątem wartości NULL we wszystkich kolumnach
Jest to fragment, który można wkleić do przepływu danych w celu ogólnego sprawdzenia wszystkich kolumn pod kątem wartości NULL. Ta technika wykorzystuje dryf schematu, aby przeszukać wszystkie kolumny we wszystkich wierszach i używa podziału warunkowego w celu oddzielenia wierszy z wartościami NULL z wierszy bez wartości NULL. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>AutoMap dryf schematu z zaznaczeniem
Gdy zachodzi potrzeba załadowania istniejącego schematu bazy danych z nieznanego lub dynamicznego zestawu kolumn przychodzących, należy zmapować kolumny po prawej stronie w transformację ujścia. Jest to konieczne tylko w przypadku ładowania istniejącej tabeli. Dodaj ten fragment kodu przed utworzeniem obiektu sink, który umożliwia wybranie automapowania kolumn. Pozostaw mapowanie ujścia na Automap.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Utrwalanie typów danych kolumny
Dodaj ten skrypt wewnątrz definicji kolumny pochodnej, aby przechowywać nazwy kolumn i typy danych z przepływu danych w magazynie trwałym przy użyciu ujścia.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Wypełnij w dół
Poniżej przedstawiono sposób implementacji typowego problemu "wypełniania" z zestawami danych, gdy chcesz zastąpić wartości NULL wartością z poprzedniej wartości innej niż NULL w sekwencji. Należy zauważyć, że ta operacja może mieć negatywny wpływ na wydajność, ponieważ należy utworzyć okno syntetyczne w całym zestawie danych za pomocą wartości kategorii "fikcyjne". Ponadto należy posortować według wartości, aby utworzyć poprawną sekwencję danych w celu znalezienia poprzedniej wartości innej niż NULL. Poniższy fragment kodu tworzy kategorię syntetyczną jako "fikcyjną" i sortuje według klucza zastępczego. Możesz usunąć klucz zastępczy i użyć własnego klucza sortowania określonego dla danych. W tym fragmencie kodu założono, że dodano już transformację źródłową o nazwie ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Średnia przenoszona
Średnia przenoszona można łatwo zaimplementować w przepływach danych przy użyciu transformacji systemu Windows. W poniższym przykładzie utworzono 15-dniową średnią cenę giełdową dla firmy Microsoft.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Następne kroki

Eksploruj przepływy danych, zaczynając od [artykułu Omówienie przepływów danych](concepts-data-flow-overview.md)

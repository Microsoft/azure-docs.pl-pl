---
title: Mapowanie skryptu przepływu danych
description: Omówienie języka skryptu przepływ danych usługi Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: e0042960c25d58b72bc0ab884de5a2db62e566d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413442"
---
# <a name="data-flow-script-dfs"></a>Skrypt przepływu danych (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Skrypt przepływu danych (DFS) jest podstawowym metadanym, podobnie jak język kodowania, który jest używany do wykonywania przekształceń, które są zawarte w przepływie danych mapowania. Każda transformacja jest reprezentowana przez szereg właściwości, które dostarczają informacji niezbędnych do prawidłowego uruchomienia zadania. Skrypt jest widoczny i edytowalny z podajnika ADF, klikając przycisk "skrypt" na górnej wstążce interfejsu użytkownika przeglądarki.

![Przycisk Skrypt](media/data-flow/scriptbutton.png "Przycisk Skrypt")

Na przykład `allowSchemaDrift: true,` w transformacji źródła informuje usługi, aby uwzględnić wszystkie kolumny z źródłowego zestawu danych w przepływie danych, nawet jeśli nie są one uwzględnione w projekcji schematu.

## <a name="use-cases"></a>Przypadki zastosowań
Dfs jest automatycznie generowany przez interfejs użytkownika. Możesz kliknąć przycisk Skrypt, aby wyświetlić i dostosować skrypt. Można również wygenerować skrypty poza interfejsem użytkownika usługi ADF, a następnie przekazać je do polecenia cmdlet programu PowerShell. Podczas debugowania złożonych przepływów danych może okazać się łatwiejsze skanowanie skryptu związane z kodem zamiast skanowania reprezentacji wykresu interfejsu użytkownika przepływów.

Oto kilka przykładowych przypadków użycia:
- Programowo wytwarzając wiele przepływów danych, które są dość podobne, czyli przepływy danych "wybijania".
- Złożone wyrażenia, które są trudne do zarządzania w interfejsie użytkownika lub powodują problemy z sprawdzaniem poprawności.
- Debugowanie i lepsze zrozumienie różnych błędów zwracanych podczas wykonywania.

Podczas tworzenia skryptu przepływu danych do użycia z programem PowerShell lub interfejsu API, należy zwinąć sformatowany tekst w jednym wierszu. Możesz zachować karty i nowe linie jako znaki ucieczki. Ale tekst musi być sformatowany, aby zmieścić się wewnątrz właściwości JSON. U dołu znajduje się przycisk interfejsu użytkownika edytora skryptów, który sformatować skrypt jako pojedynczy wiersz.

![Kopiuj](media/data-flow/copybutton.png "Kopiuj")

## <a name="how-to-add-transforms"></a>Jak dodać transformacje
Dodawanie przekształceń wymaga trzech podstawowych kroków: dodanie podstawowych danych transformacji, przekierowanie strumienia wejściowego, a następnie przekierowanie strumienia wyjściowego. Można to zobaczyć najłatwiej w przykładzie.
Załóżmy, że zaczynamy od prostego źródła do pochłaniania przepływu danych, jak poniżej:

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

Jeśli zdecydujemy się dodać transformację pochodną, najpierw musimy utworzyć podstawowy tekst transformacji, który ma proste `upperCaseTitle`wyrażenie, aby dodać nową wielką kolumnę o nazwie:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Następnie bierzemy istniejący DFS i dodajemy transformację:
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

A teraz możemy przekierować przychodzącego strumienia, identyfikując, które transformacja chcemy, aby nowa transformacja przyjść po (w tym przypadku) `source1`i kopiowanie nazwy strumienia do nowej transformacji:
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

Na koniec identyfikujemy transformację, którą chcemy nadejść po tej nowej `sink1`transformacji i zastępujemy jej strumień wejściowy (w tym przypadku) nazwą strumienia wyjściowego naszej nowej transformacji:
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
System plików DFS składa się z serii połączonych przekształceń, w tym źródeł, pochłaniacze i różnych innych, które mogą dodawać nowe kolumny, filtrować dane, dołączać dane i wiele więcej. Zazwyczaj skrypt zaczyna się od jednego lub więcej źródeł, po których następuje wiele przekształceń, a kończąc na jednym lub więcej pochłaniacze.

Wszystkie źródła mają tę samą podstawową konstrukcję:
```
source(
  source properties
) ~> source_name
```

Na przykład prostym źródłem z trzema kolumnami (movieId, title, genres) będzie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Wszystkie przekształcenia inne niż źródła mają tę samą podstawową konstrukcję:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Na przykład proste transformacja pochodna, która przyjmuje kolumnę (tytuł) i zastępuje ją wersją wielkich liter, będzie następująca:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

A zlew bez schematu byłby po prostu:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Fragmenty kodu skryptu

Fragmenty kodu skryptu to kod współużytkowany skryptu przepływu danych, którego można używać do udostępniania w przepływach danych. W tym klipie wideo poniżej opisano, jak używać fragmentów skryptów i używać skryptu przepływu danych do kopiowania i wklejania fragmentów skryptu za wykresami przepływu danych:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Statystyki podsumowania zagregowane
Dodaj transformację agregacji do przepływu danych o nazwie "SummaryStats", a następnie wklej w tym kodzie poniżej dla funkcji agregacji w skrypcie, zastępując istniejące SummaryStats. Zapewni to ogólny wzorzec dla statystyk podsumowania profilu danych.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Możesz również użyć poniższego przykładu, aby policzyć liczbę unikatowych i liczbę różnych wierszy w danych. Poniższy przykład można wkleić do przepływu danych z transformacją agregacji o nazwie ValueDistAgg. W tym przykładzie użyto kolumny o nazwie "title". Pamiętaj, aby zastąpić "title" kolumną ciągu w danych, których chcesz użyć, aby uzyskać liczbę wartości.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Uwzględnij wszystkie kolumny w agregacji
Jest to ogólny wzorzec agregacji, który pokazuje, jak można zachować pozostałe kolumny w metadanych danych wyjściowych podczas tworzenia agregacji. W takim przypadku używamy ```first()``` funkcji, aby wybrać pierwszą wartość w każdej kolumnie, której nazwa nie jest "film". Aby użyć tego, należy utworzyć transformację agregacji o nazwie DistinctRows, a następnie wkleić to w skrypcie na wierzchu istniejącego skryptu agregacji DistinctRows.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Tworzenie odcisku palca skrótu wiersza 
Ten kod w skrypcie przepływu danych służy ```DWhash``` do tworzenia ```sha1``` nowej kolumny pochodnej o nazwie, która tworzy skrót trzech kolumn.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Następne kroki

Eksploruj przepływy danych, zaczynając od [artykułu omówienie przepływów danych](concepts-data-flow-overview.md)

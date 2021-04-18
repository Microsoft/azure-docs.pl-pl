---
title: Funkcje rozmieszczania danych w Azure Data Factory
description: Omówienie dostępnych funkcji przetwarzania danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600944"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Funkcje przekształcania w Power Query do przetwarzania danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Przetwarzanie danych w usłudze Azure Data Factory umożliwia elastyczne przygotowywanie i porządkowanie danych bez kodu w skali chmury przez tłumaczenie skryptów Power Query na skrypty Przepływ danych ```M``` kodu. Funkcja ADF integruje się z [usługą Power Query Online](/powerquery-m/power-query-m-reference) i udostępnia funkcje Power Query do przetwarzania danych za pośrednictwem wykonywania na platformie Spark przy użyciu ```M``` infrastruktury platformy Spark przepływu danych. 

> [!NOTE]
> Power Query adf jest obecnie dostępny w publicznej wersji zapoznawczej

Obecnie nie wszystkie funkcje Power Query M są obsługiwane w przypadku przetwarzania danych, mimo że są dostępne podczas tworzenia. Podczas tworzenia mash-upów zostanie wyświetlony następujący komunikat o błędzie, jeśli funkcja nie jest obsługiwana:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Poniżej znajduje się lista obsługiwanych funkcji Power Query M.

## <a name="column-management"></a>Zarządzanie kolumnami

* Selection: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Usuwanie: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Zmiana nazwy: [Table.RenameColumns,](/powerquery-m/table-renamecolumns) [Table.PrefixColumns,](/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Zmiana kolejności: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrowanie wierszy

Użyj funkcji M [Table.SelectRows,](/powerquery-m/table-selectrows) aby filtrować według następujących warunków:

* Równość i nierówności
* Porównania liczbowe, tekstowe i daty (ale nie dateTime)
* Informacje liczbowe, takie [jak Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Zawieranie tekstu przy użyciu [funkcji Text.Contains,](/powerquery-m/text-contains) [Text.StartsWith](/powerquery-m/text-startswith)lub [Text.EndsWith](/powerquery-m/text-endswith)
* Zakresy dat, w tym wszystkie funkcje daty ["IsIn")](/powerquery-m/date-functions) 
* Kombinacje tych elementów przy użyciu warunków i, lub lub nie

## <a name="adding-and-transforming-columns"></a>Dodawanie i przekształcanie kolumn

Następujące funkcje M dodają lub przekształcają kolumny: [Table.AddColumn,](/powerquery-m/table-addcolumn) [Table.TransformColumns,](/powerquery-m/table-transformcolumns) [Table.ReplaceValue,](/powerquery-m/table-replacevalue) [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Poniżej przedstawiono obsługiwane funkcje przekształcania.

* Arytmetyka liczbowa
* Konkasowanie tekstu
* Arytmetyka daty i czasu (operatory arytmetyczne, [Date.AddDays,](/powerquery-m/date-adddays) [Date.AddMonths,](/powerquery-m/date-addmonths) [Date.AddQuarters,](/powerquery-m/date-addquarters) [Date.AddWeeks,](/powerquery-m/date-addweeks) [Date.AddYears](/powerquery-m/date-addyears))
* Czasy trwania mogą być używane do arytmetyki daty i godziny, ale muszą zostać przekształcone w inny typ przed ich zapisano w ujściu (operatory arytmetyczne, [#duration](/powerquery-m/sharpduration), [Duration.Days,](/powerquery-m/duration-days) [Duration.Hours,](/powerquery-m/duration-hours) [Duration.Minutes,](/powerquery-m/duration-minutes) [Duration.Seconds](/powerquery-m/duration-seconds), [Duration.TotalDays,](/powerquery-m/duration-totaldays) [Duration.TotalHours](/powerquery-m/duration-totalhours), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds))    
* Większość standardowych, naukowych i trygonometrycznych funkcji liczbowych (wszystkie funkcje w obszarze [Operacje,](/powerquery-m/number-functions#operations) [Zaokrąglanie](/powerquery-m/number-functions#rounding)i [Trygonometry](/powerquery-m/number-functions#trigonometry) z wyjątkiem  Number.Factorial, Number.Permutations i Number.Combinations)
* Replacement ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Wyodrębnianie tekstu pozykcyjnego ([Text.PositionOf](/powerquery-m/text-positionof), [Text.Length,](/powerquery-m/text-length) [Text.Start](/powerquery-m/text-start), [Text.End,](/powerquery-m/text-end) [Text.Middle,](/powerquery-m/text-middle) [Text.ReplaceRange,](/powerquery-m/text-replacerange) [Text.RemoveRange](/powerquery-m/text-removerange))
* Podstawowe formatowanie tekstu[(Text.Lower,](/powerquery-m/text-lower) [Text.Upper,](/powerquery-m/text-upper) [Text.Trim](/powerquery-m/text-trim) / [Początek,](/powerquery-m/text-trimstart) / [](/powerquery-m/text-trimend) [Text.PadStart](/powerquery-m/text-padstart) / [End,](/powerquery-m/text-padend) [Text.Reverse](/powerquery-m/text-reverse))
* Funkcje daty/godziny ([Date.Day,](/powerquery-m/date-day) [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour,](/powerquery-m/time-hour) [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* Jeśli wyrażenia (ale gałęzie muszą mieć pasujące typy)
* Filtry wierszy jako kolumna logiczna
* Liczby, tekst, stałe logiczne, daty i daty/godziny

<a name="mergingjoining-tables"></a>Scalanie/łączenie tabel
----------------------
* Power Query wygeneruje zagnieżdżone sprzężenia (Table.NestedJoin; użytkownicy mogą również ręcznie zapisywać [Table.AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Użytkownicy muszą następnie rozwinąć zagnieżdżoną kolumnę sprzężenia do nie zagnieżdżonych sprzężenia (Table.ExpandTableColumn, która nie jest obsługiwana w żadnym innym kontekście).
* Funkcja M   [Table.Join](/powerquery-m/table-join) może zostać napisana bezpośrednio, aby uniknąć konieczności dodatkowego kroku rozszerzania, ale użytkownik musi upewnić się, że nie ma żadnych zduplikowanych nazw kolumn między tabelami sprzężenia
* Obsługiwane rodzaje sprzężenia:   [Wewnętrzny,](/powerquery-m/joinkind-inner)   [LeftOuter,](/powerquery-m/joinkind-leftouter)   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* Zarówno   [Value.Equals,](/powerquery-m/value-equals)   [jak i Value.NullableEquals](/powerquery-m/value-nullableequals) są obsługiwane jako porównujące równość kluczy

## <a name="group-by"></a>Grupuj według

Użyj [funkcji Table.Group,](/powerquery-m/table-group) aby agregować wartości.
* Musi być używana z funkcją agregacji
* Obsługiwane funkcje agregacji:   [List.Sum,](/powerquery-m/list-sum)   [List.Count,](/powerquery-m/list-count)   [List.Average,](/powerquery-m/list-average)   [List.Min,](/powerquery-m/list-min)   [List.Max,](/powerquery-m/list-max)   [List.StandardDeviation,](/powerquery-m/list-standarddeviation)   [List.First,](/powerquery-m/list-first)   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sortowanie

Użyj [funkcji Table.Sort,](/powerquery-m/table-sort) aby posortować wartości.

## <a name="reducing-rows"></a>Zmniejszanie liczby wierszy

Zachowaj i usuń górę, Zachowaj zakres (odpowiadające funkcje M, tylko liczniki obsługi, nie warunki: [Table.FirstN,](/powerquery-m/table-firstn) [Table.Skip,](/powerquery-m/table-skip) [Table.RemoveFirstN,](/powerquery-m/table-removefirstn) [Table.Range,](/powerquery-m/table-range) [Table.MinN,](/powerquery-m/table-minn) [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Znane nieobsługiwane funkcje

| Funkcja | Stan |
| -- | -- |
| Table.PromoteHeaders | Nieobsługiwane. Ten sam wynik można osiągnąć, ustawiając w zestawie danych ustawienie "Pierwszy wiersz jako nagłówek". |
| Table.CombineColumns | Jest to powszechny scenariusz, który nie jest obsługiwany bezpośrednio, ale można to osiągnąć, dodając nową kolumnę, która łączy dwie podane kolumny.  Na przykład Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Jest to obsługiwane w większości przypadków. Następujące scenariusze nie są obsługiwane: przekształcanie ciągu na typ waluty, przekształcanie ciągu na typ czasu, przekształcanie ciągu na typ procentu. |
| Table.NestedJoin | Samo wykonanie sprzężenia spowoduje błąd walidacji. Kolumny muszą być rozwinięte, aby działały. |
| Table.Distinct | Usuwanie zduplikowanych wierszy nie jest obsługiwane. |
| Table.RemoveLastN | Usuwanie dolnych wierszy nie jest obsługiwane. |
| Table.RowCount | Ta wartość nie jest obsługiwana, ale można ją osiągnąć, dodając kolumnę niestandardową zawierającą wartość 1, a następnie agregując ją za pomocą funkcji List.Sum. Table.Group jest obsługiwana. | 
| Obsługa błędów na poziomie wiersza | Obsługa błędów na poziomie wiersza nie jest obecnie obsługiwana. Na przykład aby odfiltrować wartości nieliczbowe z kolumny, jednym z podejść jest przekształcenie kolumny tekstowej w liczbę. Każda komórka, której nie można przekształcić, będzie w stanie błędu i musi być filtrowana. Ten scenariusz nie jest możliwy w przypadku skalowania w poziomie M. |
| Table.Transpose | Nieobsługiwane |
| Table.Pivot | Nieobsługiwane |
| Table.SplitColumn | Częściowo obsługiwane |

## <a name="m-script-workarounds"></a>Obejścia skryptów w języku M

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>Istnieje ```SplitColumn``` alternatywa dla podziału według długości i położenia

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Wstawione pierwsze znaki", "Zakres tekstu", każdy Text.Middle([Email], 4, 9), typ text)

Ta opcja jest dostępna z opcji Wyodrębnij na wstążce

![Power Query Dodaj kolumnę](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>For ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName])


## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak utworzyć układ danych w Power Query UDF.](wrangling-tutorial.md)

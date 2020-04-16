---
title: Funkcje przekształcania przepływu danych w usłudze Azure Data Factory
description: Omówienie dostępnych funkcji przepływu danych w rozsypanie w usłudze Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7235e95e5b33fb931411a51796a8dbec96c46355
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417661"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funkcje przekształcania w przepływie danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wrangling przepływu danych w usłudze Azure Data Factory umożliwia bezkodowe przygotowanie danych agile i wrangling w skali chmury. Wrangling przepływ danych integruje się z [usługą Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) i sprawia, że funkcje dodatku Power Query M dostępne dla danych wrangling poprzez wykonanie iskier. 

Obecnie nie wszystkie funkcje dodatku Power Query M są obsługiwane w przypadku wranglingu danych, mimo że są dostępne podczas tworzenia. Podczas tworzenia przepływów danych wrangling, zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Poniżej znajduje się lista obsługiwanych funkcji dodatku Power Query M.

## <a name="column-management"></a>Zarządzanie kolumnami

* Wybór: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Usuwanie: [Table.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Zmiana nazwy: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Ponowne kolejność: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrowanie wierszy

Użyj funkcji M [Table.SelectRows,](https://docs.microsoft.com/powerquery-m/table-selectrows) aby filtrować następujące warunki:

* Równość i nierówność
* Porównania liczbowe, tekstowe i daty (ale nie DateTime)
* Informacje liczbowe, takie jak [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Hermetyzowanie tekstu za pomocą [text.contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)lub [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Zakresy dat, w tym wszystkie [funkcje daty](https://docs.microsoft.com/powerquery-m/date-functions)"IsIn") 
* Kombinacje tych wykorzystaniem i, lub, lub nie warunki

## <a name="adding-and-transforming-columns"></a>Dodawanie i przekształcanie kolumn

Następujące funkcje M dodają lub przekształcają kolumny: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Poniżej znajdują się obsługiwane funkcje transformacji.

* Arytmetyka numeryczna
* Łączenie tekstu
* Data i Czas Arytmetyka (operatory arytmetyczne, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Czas trwania może być używany dla arytmetyki daty i godziny, ale musi zostać przekształcony w inny typ przed zapisaniem do ujścia (operatory arytmetyczne, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Czas trwania.Dni](https://docs.microsoft.com/powerquery-m/duration-days), [Czas trwania.Godziny,](https://docs.microsoft.com/powerquery-m/duration-hours) [Czas trwania.Minuty,](https://docs.microsoft.com/powerquery-m/duration-minutes) [Czas trwania.Sekundy,](https://docs.microsoft.com/powerquery-m/duration-seconds) [Czas trwania.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Czas trwania.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Czas trwania.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Czas trwania.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Większość standardowych, naukowych i trygonometrycznych funkcji liczbowych (wszystkie funkcje w obszarze [Operacje,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Zaokrąglanie](https://docs.microsoft.com/powerquery-m/number-functions#rounding)i [Trygonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *z wyjątkiem* liczby.factorial, liczba.permutacje i liczba.kombinacje)
* Wymiana[(Replacer.Replacetext](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text.Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Wyodrębnianie tekstu pozycyjnego ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Podstawowe formatowanie tekstu ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End,](https://docs.microsoft.com/powerquery-m/text-trimend) [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End,](https://docs.microsoft.com/powerquery-m/text-padend) [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funkcje daty/godziny ([Data.Dzień](https://docs.microsoft.com/powerquery-m/date-day), [Data.Miesiąc](https://docs.microsoft.com/powerquery-m/date-month), [Data.Rok](https://docs.microsoft.com/powerquery-m/date-year) [Czas.Godzina](https://docs.microsoft.com/powerquery-m/time-hour), [Godzina.Minuta](https://docs.microsoft.com/powerquery-m/time-minute), [Czas.Drugi](https://docs.microsoft.com/powerquery-m/time-second), [Data.DayWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Data.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Data.DniWmie](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Jeśli wyrażenia (ale gałęzie muszą mieć pasujące typy)
* Filtry wierszy jako kolumna logiczna
* Stałe liczby, tekstu, logiczne, daty i daty i daty

<a name="mergingjoining-tables"></a>Scalanie/łączenie tabel
----------------------
* Dodatek Power Query wygeneruje sprzężenie zagnieżdżone (Table.NestedJoin; użytkownicy mogą również ręcznie pisać [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Użytkownicy muszą następnie rozwinąć kolumnę sprzężenia zagnieżdżonego do sprzężenia niezagnieżdżonego (Table.ExpandTableColumn, nieobjętego w żadnym innym kontekście).
* Funkcja M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) może być zapisywana bezpośrednio, aby uniknąć konieczności dodatkowego kroku rozszerzenia, ale użytkownik musi upewnić się, że między tabelami sprzężonymi nie ma zduplikowanych nazw kolumn.
* Obsługiwane rodzaje sprzężenia: [Wewnętrzny,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Zarówno [Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) i [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) są obsługiwane jako moduły porównujące równość kluczy

## <a name="group-by"></a>Grupuj według

Użyj [table.group](https://docs.microsoft.com/powerquery-m/table-group) do agregowania wartości.
* Musi być używany z funkcją agregacji
* Obsługiwane funkcje agregacji: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Sortowanie

Sortowanie wartości za pomocą [funkcji Table.Sort.](https://docs.microsoft.com/powerquery-m/table-sort)

## <a name="reducing-rows"></a>Zmniejszanie wierszy

Zachowaj i usuń górną, Zachowaj zakres (odpowiednie funkcje M, tylko podtrzymujące liczby, a nie warunki: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Znane nieobsługiwały funkcje

| Funkcja | Stan |
| -- | -- |
| Table.PromoteHeaders | Bez pomocy technicznej. Ten sam wynik można osiągnąć, ustawiając "Pierwszy wiersz jako nagłówek" w zestawie danych. |
| Table.CombineColumns | Jest to typowy scenariusz, który nie jest bezpośrednio obsługiwany, ale można osiągnąć, dodając nową kolumnę, która łączy dwie kolumny.  Na przykład Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Jest to obsługiwane w większości przypadków. Następujące scenariusze są nieobsługiwały: przekształcanie ciągu do typu waluty, przekształcanie ciągu do typu czasu, przekształcanie ciągu do typu procentowego. |
| Table.NestedJoin | Samo wykonanie sprzężenia spowoduje błąd sprawdzania poprawności. Kolumny muszą zostać rozwinięte, aby działały. |
| Table.Distinct | Usuwanie zduplikowanych wierszy nie jest obsługiwane. |
| Table.RemoveLastN | Usuń dolne wiersze nie jest obsługiwane. |
| Table.RowCount | Nie jest obsługiwany, ale można go osiągnąć za pomocą kolumny dodawania ze wszystkimi pustymi komórkami (kolumna warunku może być używana), a następnie za pomocą grupy w tej kolumnie. Table.Group jest obsługiwana. | 
| Obsługa błędów poziomu wiersza | Obsługa błędów na poziomie wiersza nie jest obecnie obsługiwana. Na przykład, aby odfiltrować wartości nienumeryczne z kolumny, jednym z podejść byłoby przekształcenie kolumny tekstowej w liczbę. Każda komórka, która nie może przekształcić będzie w stanie błędu i muszą być filtrowane. Ten scenariusz nie jest możliwe w wrangling przepływu danych. |
| Table.Transpose | Nieobsługiwane |
| Table.Pivot | Nieobsługiwane |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych.](wrangling-data-flow-tutorial.md)
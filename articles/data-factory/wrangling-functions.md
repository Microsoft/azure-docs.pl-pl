---
title: Funkcje przetwarzanie danych w Azure Data Factory
description: Omówienie dostępnych funkcji przetwarzanie danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: a88f9fab2b10271aa7856a6d0b5ee114f46cfb49
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634137"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Funkcje transformacji w Power Query danych przetwarzanie

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Data przetwarzanie w Azure Data Factory umożliwia wykonywanie bezobsługowego przetwarzania danych Agile i przetwarzanie w skali chmury przez przetłumaczenie ```M``` skryptów Power Query do skryptu przepływu danych. Moduł ADF integruje się z usługą [Power Query online](/powerquery-m/power-query-m-reference) i udostępnia ```M``` funkcje Power Query dla przetwarzanie danych za pośrednictwem wykonywania platformy Spark przy użyciu infrastruktury Spark przepływu danych. 

> [!NOTE]
> Power Query w usłudze ADF jest obecnie dostępnych w publicznej wersji zapoznawczej

Obecnie nie wszystkie Power Query funkcje M są obsługiwane dla przetwarzanie danych, mimo że są dostępne podczas tworzenia. Podczas kompilowania swoich różny zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

Poniżej znajduje się lista obsługiwanych Power Query funkcji M.

## <a name="column-management"></a>Zarządzanie kolumnami

* Wybór: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Usuwanie: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Zmiana nazwy: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Zmiana kolejności: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrowanie wierszy

Użyj funkcji M [. SelectRows](/powerquery-m/table-selectrows) , aby odfiltrować następujące warunki:

* Równość i nierówność
* Porównania liczbowe, tekstowe i daty (ale nie daty i godziny)
* Informacje liczbowe, takie jak [Number.](/powerquery-m/number-iseven) / [](/powerquery-m/number-iseven) isparzyste
* Zawieranie tekstu przy użyciu [Text. Contains](/powerquery-m/text-contains), [Text. StartsWith](/powerquery-m/text-startswith)lub [Text. EndsWith](/powerquery-m/text-endswith)
* Zakresy dat, w tym wszystkie funkcje "IsIn' [Dates](/powerquery-m/date-functions)" 
* Kombinacje tych elementów za pomocą i, lub, lub nie warunki

## <a name="adding-and-transforming-columns"></a>Dodawanie i przekształcanie kolumn

Następujące funkcje M dodają lub przekształcają kolumny: [Table. addColumn](/powerquery-m/table-addcolumn), [Table. TransformColumns](/powerquery-m/table-transformcolumns), [Table. ReplaceValue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Poniżej przedstawiono obsługiwane funkcje transformacji.

* Arytmetyka liczbowa
* Łączenie tekstu
* Date andTime arytmetyczne (operatory arytmetyczne, [Date. AddDays](/powerquery-m/date-adddays), [Date. addmiesiącach](/powerquery-m/date-addmonths), [Date. addćwiartks](/powerquery-m/date-addquarters), [Date. Addtygs](/powerquery-m/date-addweeks), [Date. AddYears](/powerquery-m/date-addyears))
* Czas trwania może być używany w przypadku operacji arytmetycznych daty i czasu, ale musi być przekształcony na inny typ przed zapisaniem w zlewie (operatory arytmetyczne, [#duration](/powerquery-m/sharpduration), [czas trwania. dni](/powerquery-m/duration-days), [czas trwania. godz](/powerquery-m/duration-hours)., czas trwania: [minuty](/powerquery-m/duration-minutes), czas trwania. [s](/powerquery-m/duration-seconds), czas trwania. [TotalDays](/powerquery-m/duration-totaldays), [czas trwania. TotalHours](/powerquery-m/duration-totalhours), [czas trwania. TotalMinutes](/powerquery-m/duration-totalminutes), [czas trwania. TotalSeconds](/powerquery-m/duration-totalseconds))    
* Większość standardowych, naukowych i trygonometrycznych funkcji liczbowych (wszystkie funkcje w ramach [operacji](/powerquery-m/number-functions#operations), [zaokrąglania](/powerquery-m/number-functions#rounding)i [trygonometryczne](/powerquery-m/number-functions#trigonometry) , *z wyjątkiem* Number. silnia, Number. permutacje i Number).
* Zastąpienie (preplacement[. ReplaceText](/powerquery-m/replacer-replacetext), [replace. ReplaceValue](/powerquery-m/replacer-replacevalue), [Text. Replace](/powerquery-m/text-replace), [Text. Remove](/powerquery-m/text-remove))
* Wyodrębnianie tekstu pozycyjnego ([Text. PositionOf](/powerquery-m/text-positionof), [Text. length](/powerquery-m/text-length), [Text. Start](/powerquery-m/text-start), [Text. end](/powerquery-m/text-end), [Text. Middle](/powerquery-m/text-middle), [Text. ReplaceRange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Podstawowe formatowanie tekstu ([Text. Lower](/powerquery-m/text-lower), [Text. Upper](/powerquery-m/text-upper), [Text. Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [](/powerquery-m/text-trimend), [Text. PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text. Reverse](/powerquery-m/text-reverse))
* Funkcje daty i godziny ([Date. Day](/powerquery-m/date-day), [Date. month](/powerquery-m/date-month), [Date. Year](/powerquery-m/date-year) [Time. Hour](/powerquery-m/time-hour), [Time. minute](/powerquery-m/time-minute), [Time. Second](/powerquery-m/time-second), [Date. DayOfWeek](/powerquery-m/date-dayofweek), [Date. dzieńroku](/powerquery-m/date-dayofyear), [Date. DaysInMonth](/powerquery-m/date-daysinmonth))
* Wyrażenie if (ale gałęzie muszą mieć zgodne typy)
* Filtry wierszy jako kolumna logiczna
* Stałe Number, text, Logical, date i DateTime

<a name="mergingjoining-tables"></a>Scalanie/Łączenie tabel
----------------------
* Power Query generuje zagnieżdżone sprzężenie (Table. NestedJoin; użytkownicy mogą również ręcznie napisać [tabelę. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Użytkownicy muszą następnie rozwinąć zagnieżdżoną kolumnę sprzężenia do niezagnieżdżonego sprzężenia (Table. ExpandTableColumn, nieobsługiwane w żadnym innym kontekście).
* Tabela funkcji M   [. Join](/powerquery-m/table-join) można pisać bezpośrednio, aby uniknąć potrzeby dodatkowego kroku rozszerzania, ale użytkownik musi upewnić się, że nie ma żadnych zduplikowanych nazw kolumn wśród sprzężonych tabel
* Obsługiwane rodzaje sprzężeń:   [wewnętrzne](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* Obie   [wartości. Equals](/powerquery-m/value-equals) i   [Value. NullableEquals](/powerquery-m/value-nullableequals) są obsługiwane jako porównania klucza równości

## <a name="group-by"></a>Grupuj według

Użyj [tabeli Table. Group](/powerquery-m/table-group) do agregowania wartości.
* Musi być używana z funkcją agregacji
* Obsługiwane funkcje agregacji:   [list. sum](/powerquery-m/list-sum),   [list. Count](/powerquery-m/list-count),   [list. Average](/powerquery-m/list-average),   [list. min](/powerquery-m/list-min),   [list. Max](/powerquery-m/list-max),   [list. StandardDeviation](/powerquery-m/list-standarddeviation),   [list. First](/powerquery-m/list-first),   [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sortowanie

Użyj [tabeli. Sort](/powerquery-m/table-sort) , aby sortować wartości.

## <a name="reducing-rows"></a>Zmniejszanie liczby wierszy

Zachowuj i usuwaj najważniejsze, zachowuj zakres (odpowiadające funkcje M, tylko obsługiwane liczby, a nie warunki: [Table. FirstN](/powerquery-m/table-firstn), [Table. Skip](/powerquery-m/table-skip), [Table. RemoveFirstN](/powerquery-m/table-removefirstn), [Table. Range](/powerquery-m/table-range), [Table. MinN](/powerquery-m/table-minn), [Table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Znane Nieobsługiwane funkcje

| Funkcja | Stan |
| -- | -- |
| Table.PromoteHeaders | Nieobsługiwane. Ten sam wynik można osiągnąć przez ustawienie "pierwszy wiersz jako nagłówek" w zestawie danych. |
| Table.CombineColumns | Jest to typowy scenariusz, który nie jest bezpośrednio obsługiwany, ale można go osiągnąć przez dodanie nowej kolumny, która łączy dwie podaną kolumnę.  Na przykład: Table. addColumn (RemoveEmailColumn, "name", each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Jest to obsługiwane w większości przypadków. Następujące scenariusze nie są obsługiwane: transformowanie ciągu do typu waluty, transformowanie ciągu do typu czasu, transformowanie ciągu do typu procentowego. |
| Table.NestedJoin | Wykonanie sprzężenia spowoduje błąd walidacji. Aby można było obejść te kolumny, muszą być rozwinięte. |
| Table.Distinct | Usuwanie zduplikowanych wierszy nie jest obsługiwane. |
| Table.RemoveLastN | Usuwanie dolnych wierszy nie jest obsługiwane. |
| Table.RowCount | Nieobsługiwane, ale można je osiągnąć przez dodanie kolumny niestandardowej zawierającej wartość 1, a następnie agregowanie tej kolumny z listą. sum. Tabela. Grupa jest obsługiwana. | 
| Obsługa błędów na poziomie wiersza | Obsługa błędów na poziomie wiersza nie jest obecnie obsługiwana. Na przykład, aby odfiltrować wartości inne niż liczbowe z kolumny, jednym z metod jest przekształcenie kolumny tekstowej na liczbę. Każda komórka, której nie można przekształcić, będzie w stanie błędu i musi zostać przefiltrowana. Ten scenariusz nie jest możliwy w przepływie danych przetwarzanie. |
| Table.Transpose | Nieobsługiwane |
| Table.Pivot | Nieobsługiwane |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć Power Query danych przetwarzanie w usłudze ADF](wrangling-tutorial.md).

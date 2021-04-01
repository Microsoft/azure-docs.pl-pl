---
title: Przewodnik programowania programu "U-SQL" zdefiniowany przez użytkownika dla Azure Data Lake
description: Dowiedz się więcej na temat przewodnika programowania U-SQL UDO — zmniejszenie zdefiniowane przez użytkownika.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512629"
---
# <a name="use-user-defined-reducer"></a>Korzystanie z reduktora zdefiniowanego przez użytkownika

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: ograniczenie zdefiniowane przez użytkownika

Język U-SQL umożliwia pisanie niestandardowych funkcji zmniejszania zestawów wierszy w języku C# przy użyciu zdefiniowanej przez użytkownika platformy rozszerzalności operatora i implementowanie interfejsu IReducer.

W celu wyeliminowania niepotrzebnych wierszy podczas wyodrębniania danych (import) można użyć ograniczenia zdefiniowanego przez użytkownika lub UDR. Można go również użyć do manipulowania i obliczenia wierszy i kolumn. Na podstawie logiki programowania można także określić, które wiersze muszą zostać wyodrębnione.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Jak zdefiniować i korzystać z ograniczenia zdefiniowanego przez użytkownika
Aby zdefiniować klasę UDR, musimy utworzyć `IReducer` interfejs z opcjonalnym `SqlUserDefinedReducer` atrybutem.

Ten interfejs klasy powinien zawierać definicję `IEnumerable` przesłonięcia zestawu wierszy interfejsu.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Atrybut **SqlUserDefinedReducer** wskazuje, że typ powinien być zarejestrowany jako zmniejszenie zdefiniowane przez użytkownika. Klasa ta nie może być dziedziczona.
**SqlUserDefinedReducer** jest opcjonalnym atrybutem dla definicji ograniczenia zdefiniowanej przez użytkownika. Służy do definiowania właściwości isrekurencyjnej.

* bool isrekurencyjny    
* **true**  = wskazuje, czy ten spadek jest asocjacyjny i komutatywna

Główne obiekty programowalności są **danymi wejściowymi** i **wyjściowymi**. Obiekt wejściowy jest używany do wyliczania wierszy wejściowych. Dane wyjściowe służą do ustawiania wierszy wyjściowych w wyniku zmniejszenia aktywności.

W przypadku wyliczenia wierszy wejściowych używamy `Row.Get` metody.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametr `Row.Get` metody jest kolumną, która jest przenoszona jako część `PRODUCE` klasy `REDUCE` instrukcji skryptu podstawowego U-SQL. Musimy również użyć poprawnego typu danych.

W przypadku danych wyjściowych Użyj `output.Set` metody.

Ważne jest, aby zrozumieć, że w przypadku danych wyjściowych tylko wartości, które są zdefiniowane za pomocą `output.Set` wywołania metody.

```csharp
output.Set<string>("mycolumn", guid);
```

Rzeczywiste dane wyjściowe tego ograniczenia są wyzwalane przez wywołanie `yield return output.AsReadOnly();` .

Poniżej znajduje się przykład zredukowania:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

W tym scenariuszu przypadku użycia, zmniejszenie jest pomijane w wierszach z pustą nazwą użytkownika. Dla każdego wiersza w zestawie wierszy odczytuje wszystkie wymagane kolumny, a następnie oblicza długość nazwy użytkownika. Wyświetla on rzeczywisty wiersz tylko wtedy, gdy wartość nazwy użytkownika jest większa niż 0.

Poniżej przedstawiono podstawowy skrypt U-SQL, który używa niestandardowego ograniczenia:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
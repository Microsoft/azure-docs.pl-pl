---
title: Podręcznik programowania U-SQL zdefiniowany przez użytkownika applier dla Azure Data Lake
description: Dowiedz się więcej na temat przewodnika programowania U-SQL UDO — zdefiniowanego przez użytkownika applier.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512641"
---
# <a name="use-user-defined-applier"></a>Użyj applier zdefiniowanych przez użytkownika 

## <a name="u-sql-udo-user-defined-applier"></a>UDO U-SQL: zdefiniowane przez użytkownika applier
Zdefiniowany przez użytkownika applier języka U-SQL umożliwia wywoływanie niestandardowej funkcji C# dla każdego wiersza, który jest zwracany przez wyrażenie tabeli zewnętrznej zapytania. Prawidłowe dane wejściowe są oceniane dla każdego wiersza z lewej strony wejściowej, a tworzone wiersze są łączone dla końcowych danych wyjściowych. Lista kolumn, które są tworzone przez operator APPLY, jest kombinacją zestawu kolumn po lewej stronie i po prawej stronie.


## <a name="how-to-define-and-use-user-defined-applier"></a>Jak definiować i używać applier zdefiniowanych przez użytkownika
Applier zdefiniowany przez użytkownika jest wywoływany jako część wyrażenia USQL SELECT.

Typowe wywołanie applier zdefiniowanego przez użytkownika wygląda następująco:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Aby uzyskać więcej informacji o korzystaniu z appliers w wyrażeniu SELECT, zobacz [U-SQL Wybierz opcję select from Cross Apply i OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Zdefiniowana przez użytkownika definicja klasy bazowej applier jest następująca:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Aby zdefiniować applier zdefiniowany przez użytkownika, musimy utworzyć `IApplier` interfejs z `SqlUserDefinedApplier` atrybutem [], który jest opcjonalny dla definicji applier zdefiniowanej przez użytkownika.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Zastosuj jest wywoływany dla każdego wiersza tabeli zewnętrznej. Zwraca `IUpdatableRow` wyjściowy zestaw wierszy.
* Klasa konstruktora służy do przekazywania parametrów do applier zdefiniowanych przez użytkownika.

**SqlUserDefinedApplier** wskazuje, że typ powinien być zarejestrowany jako applier zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

**SqlUserDefinedApplier** jest **opcjonalny** dla definicji applier zdefiniowanej przez użytkownika.


Główne obiekty programowalności są następujące:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Wejściowe zestawy wierszy są przesyłane jako `IRow` dane wejściowe. Wiersze wyjściowe są generowane jako `IUpdatableRow` Interfejs wyjściowy.

Nazwy poszczególnych kolumn można określić przez wywołanie `IRow` metody schematu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Aby uzyskać rzeczywiste wartości danych z przychodzącego `IRow` , używamy metody get () `IRow` interfejsu.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Lub użyjemy nazwy kolumny schematu:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Wartości wyjściowe muszą być ustawione przy użyciu `IUpdatableRow` danych wyjściowych:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Ważne jest, aby zrozumieć, że niestandardowe appliers tylko kolumny i wartości, które są zdefiniowane za pomocą `output.Set` wywołania metody.

Rzeczywista wartość wyjściowa jest wyzwalana przez wywołanie `yield return output.AsReadOnly();` .

Parametry applier zdefiniowane przez użytkownika mogą być przesyłane do konstruktora. Applier może zwrócić zmienną liczbę kolumn, które należy zdefiniować podczas wywołania applier w podstawowym skrypcie U-SQL.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Oto przykład applier zdefiniowany przez użytkownika:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Poniżej przedstawiono podstawowy skrypt U-SQL dla tego applier zdefiniowanego przez użytkownika:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

W tym scenariuszu przypadku użycia applier zdefiniowany przez użytkownika pełni rolę analizatora wartości rozdzielanych przecinkami dla właściwości floty samochodowej. Wiersze pliku wejściowego wyglądają następująco:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Jest to typowy plik TSV rozdzielany tabulatorami z kolumną właściwości, która zawiera właściwości samochodu, takie jak marka i model. Te właściwości muszą zostać przeanalizowane do kolumn tabeli. Dostarczony applier umożliwia również generowanie dynamicznej liczby właściwości w zestawie wierszy wynik na podstawie przekazanego parametru. Można wygenerować wszystkie właściwości lub tylko określony zestaw właściwości.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Applier zdefiniowany przez użytkownika może być wywoływana jako nowe wystąpienie obiektu applier:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Lub z wywołaniem metody fabryki otoki:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
---
title: Skrypt U-SQL zdefiniowany przez użytkownika Przewodnik programowania dla Azure Data Lake
description: Dowiedz się więcej na temat przewodnika programowania U-SQL UDO — zdefiniowanego przez użytkownika połączenia.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512698"
---
# <a name="use-user-defined-combiner"></a>Korzystanie z funkcji łączenia zdefiniowanej przez użytkownika

## <a name="u-sql-udo-user-defined-combiner"></a>UDO U-SQL: zdefiniowane przez użytkownika łączenie
Zdefiniowany przez użytkownika element łączenia lub UDC umożliwia łączenie wierszy z wierszy z lewej i po prawej stronie, na podstawie logiki niestandardowej. Program łączący zdefiniowany przez użytkownika jest używany z wyrażeniem łączenia.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Jak zdefiniować i użyć łączenia zdefiniowanego przez użytkownika

Połączenie jest wywoływane z wyrażeniem łączenia, które dostarcza niezbędne informacje na temat wejściowych zestawów wierszy, kolumn grupowania, oczekiwanego schematu wyniku i dodatkowych informacji.

Aby wywołać połączenie w podstawowym skrypcie U-SQL, należy użyć następującej składni:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Aby uzyskać więcej informacji, zobacz [łączenie wyrażenia (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Aby zdefiniować zdefiniowany przez użytkownika połączenie, musimy utworzyć `ICombiner` interfejs z `SqlUserDefinedCombiner` atrybutem [], który jest opcjonalny dla definicji łączenia zdefiniowanej przez użytkownika.

`ICombiner`Definicja klasy bazowej:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Implementacja niestandardowa `ICombiner` interfejsu powinna zawierać definicję `IEnumerable<IRow>` przesłonięcia łączenia.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Atrybut **SqlUserDefinedCombiner** wskazuje, że typ powinien być zarejestrowany jako połączenie zdefiniowane przez użytkownika. Klasa ta nie może być dziedziczona.

**SqlUserDefinedCombiner** jest używany do definiowania właściwości tryb łączenia. Jest to opcjonalny atrybut dla definicji łączenia zdefiniowanej przez użytkownika.

Tryb CombinerMode

Wyliczenie CombinerMode może przyjmować następujące wartości:

* Pełny (0) każdy wiersz danych wyjściowych może być zależny od wszystkich wierszy danych wejściowych od lewej i prawej z tą samą wartością klucza.

* Po lewej (1) każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego od lewej strony (i potencjalnie wszystkie wiersze z prawej strony z tą samą wartością klucza).

* Prawy (2) każdy wiersz danych wyjściowych zależy od jednego wiersza wejściowego z prawej strony (i potencjalnie z lewej strony z tą samą wartością klucza).

* Wewnętrzny (3) każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego od lewej i prawej z tą samą wartością.

Przykład: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


Główne obiekty programowalności są następujące:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Wejściowe zestawy wierszy są przesyłane jako **lewe** i **prawego** `IRowset` typu interfejsu. Oba zestawy wierszy muszą być wyliczane do przetwarzania. Każdy interfejs można wyliczyć tylko raz, dlatego musimy wyliczyć i w razie potrzeby w pamięci podręcznej.

Na potrzeby buforowania można utworzyć \<T\> Typ listy struktury pamięci w wyniku wykonania zapytania LINQ, w zależności od tego,<`IRow`>. Typ danych anonimowych może być również używany podczas wyliczania.

Zobacz [wprowadzenie do zapytań LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) , aby uzyskać więcej informacji na temat zapytań LINQ i [ \<T\> interfejsu IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) , aby uzyskać więcej informacji na temat \<T\> interfejsu IEnumerable.

Aby uzyskać rzeczywiste wartości danych z przychodzącego `IRowset` , używamy metody get () `IRow` interfejsu.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Nazwy poszczególnych kolumn można określić przez wywołanie `IRow` metody schematu.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Lub przy użyciu nazwy kolumny schematu:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Ogólne Wyliczenie za pomocą LINQ wygląda następująco:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Po wyliczyć obu zestawów wierszy przejdziemy do pętli przez wszystkie wiersze. Dla każdego wiersza w lewym zestawie wierszy będziemy znajdować wszystkie wiersze, które spełniają warunek naszego połączenia.

Wartości wyjściowe muszą być ustawione przy użyciu `IUpdatableRow` danych wyjściowych.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Rzeczywista wartość wyjściowa jest wyzwalana przez wywołanie metody `yield return output.AsReadOnly();` .

Poniżej znajduje się przykład łączenia:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

W tym scenariuszu przypadku użycia tworzymy raport analityczny dla sprzedawcy detalicznego. Celem jest znalezienie wszystkich produktów, które są droższe niż $20 000 i które kupują w witrynie sieci Web szybciej niż w przypadku zwykłego handlu detalicznego w określonym przedziale czasu.

Oto podstawowy skrypt U-SQL. Można porównać logikę między regularnym SPRZĘŻENIem a złączem:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Zdefiniowany przez użytkownika element łączący może być wywoływany jako nowe wystąpienie obiektu applier:

```csharp
USING new MyNameSpace.MyCombiner();
```


Lub z wywołaniem metody fabryki otoki:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
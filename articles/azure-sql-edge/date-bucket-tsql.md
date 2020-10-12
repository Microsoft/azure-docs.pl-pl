---
title: Date_Bucket (Transact-SQL) — Azure SQL Edge
description: Dowiedz się więcej o używaniu Date_Bucket w usłudze Azure SQL Edge
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 896caae2dfd79c4678ffb34c531fb56835e9bd66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886835"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Ta funkcja zwraca wartość daty i godziny odpowiadającą początkowi każdego zasobnika DateTime, od sygnatury czasowej zdefiniowanej przez `origin` parametr lub domyślną wartość pierwotną, `1900-01-01 00:00:00.000` Jeśli nie określono parametru Origin. 

Zobacz [typy danych daty i godziny oraz funkcje &#40;Transact-sql&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) , aby zapoznać się z omówieniem wszystkich typów danych i funkcji języka Transact-SQL.

[Konwencje składni języka Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Składnia

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argumenty

*Funkcja*

Część *daty* , która jest używana z parametrem "number". Np. Rok, miesiąc, minuta, sekunda itp.

> [!NOTE]
> `DATE_BUCKET` nie akceptuje dla argumentów *datepPart* zmiennych zdefiniowanych przez użytkownika.
  
|*Funkcja*|Skróty|  
|---|---|
|**dzień**|**DD**, **d**|  
|**ciągu**|**tydz**, **ww**|  
|**wydajność**|**hh**|  
|**minuta**|**mi**, **n**|  
|**sekunda**|**SS**, **s**|  
|**milisekund**|**Arial**|  

*Liczba*

Liczba całkowita, która decyduje o szerokości zasobnika połączonego z argumentem *datePart* . Reprezentuje szerokość zasobników datapart z czasu pochodzenia. **`This argument cannot be a negative integer value`**. 

*dniu*

Wyrażenie, które może zostać rozpoznane jako jedna z następujących wartości:

+ **dniu**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **pierwszym**

W przypadku *daty*program `DATE_BUCKET` przyjmuje wyrażenie kolumny, wyrażenie lub zmienną zdefiniowaną przez użytkownika, jeśli rozwiążą się one z dowolnym z wymienionych powyżej typów danych.

**Źródł** 

Opcjonalne wyrażenie, które może zostać rozpoznane jako jedna z następujących wartości:

+ **dniu**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **pierwszym**

Typ danych dla `Origin` powinien być zgodny z typem danych `Date` parametru. 

`DATE_BUCKET` używa domyślnej wartości daty pochodzenia `1900-01-01 00:00:00.000` tj. 12:00 w poniedziałek, styczeń 1 1900, jeśli dla funkcji nie określono wartości pochodzenia.

## <a name="return-type"></a>Typ zwracany

Typ danych wartości zwracanej dla tej metody jest dynamiczny. Zwracany typ zależy od argumentu dostarczonego dla `date` . Jeśli podano prawidłowy typ danych wejściowych dla `date` , `DATE_BUCKET` zwraca ten sam typ danych. `DATE_BUCKET` zgłasza błąd, jeśli dla parametru określono literał ciągu `date` .

## <a name="return-values"></a>Wartości zwrócone

### <a name="understanding-the-output-from-date_bucket"></a>Informacje wyjściowe z `DATE_BUCKET`

`Date_Bucket` zwraca najnowszą wartość daty lub godziny odpowiadającą parametrowi datePart i liczbie. Na przykład w poniższych wyrażeniach `Date_Bucket` zwróci wartość wyjściową `2020-04-13 00:00:00.0000000` , ponieważ dane wyjściowe są obliczane na podstawie przedziałów tygodni od domyślnego czasu pochodzenia `1900-01-01 00:00:00.000` . Wartość `2020-04-13 00:00:00.0000000` to 6276 tygodnie z wartości początkowej `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Dla wszystkich poniższych wyrażeń zostanie zwrócona ta sama wartość wyjściowa elementu `2020-04-13 00:00:00.0000000` . Wynika to z faktu, że `2020-04-13 00:00:00.0000000` 6276 tygodnie od daty pochodzenia i 6276 jest podzielny przez 2, 3, 4 i 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Dane wyjściowe poniższego wyrażenia to `2020-04-06 00:00:00.0000000` , czyli 6275 tygodni od domyślnego czasu pochodzenia `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

Dane wyjściowe poniższego wyrażenia to `2020-06-09 00:00:00.0000000` , czyli 75 tygodni od określonego czasu pochodzenia `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>Argument datepart

**dzieńroku**, **Day**i **Weekday** zwracają tę samą wartość. Każdy *parametr datepart* i jego skróty zwracają tę samą wartość.
  
## <a name="number-argument"></a>Argument liczb

Argument *liczbowy* nie może przekroczyć zakresu dodatnich wartości **int** . W poniższych instrukcjach argument *liczby* przekracza zakres **int** o 1. Poniższa instrukcja zwraca następujący komunikat o błędzie: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Jeśli do funkcji zostanie przekazana wartość ujemna dla liczby `Date_Bucket` , zostanie zwrócony następujący błąd. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>Argument daty  

`DATE_BUCKET` Zwróć wartość bazową odpowiadającą typowi danych `date` argumentu. W poniższym przykładzie zwracana jest wartość wyjściowa z typem danych datetime2. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>Argument źródła  

Typ danych `origin` `date` argumentów i w musi być taki sam. Jeśli są używane różne typy danych, zostanie wygenerowany błąd.

## <a name="remarks"></a>Uwagi

Użyj `DATE_BUCKET` w następujących klauzulach:

+ GROUP BY
+ HAVING
+ ORDER BY
+ ZAZNACZENIA \<list>
+ WHERE

## <a name="examples"></a>Przykłady

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Obliczanie Date_Bucket z szerokością zasobnika 1 od czasu pochodzenia

Każda z tych instrukcji zwiększa *date_bucket* z szerokością zasobnika 1 od czasu pochodzenia:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Tutaj znajduje się zestaw wyników.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Używanie wyrażeń jako argumentów dla parametrów liczbowych i daty

W tych przykładach różne typy wyrażeń są używane jako argumenty dla parametrów *Number* i *Date* . Te przykłady są kompilowane przy użyciu bazy danych "AdventureWorksDW2017".
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Określanie zmiennych zdefiniowanych przez użytkownika jako liczby i daty  

Ten przykład określa zmienne zdefiniowane przez użytkownika jako argumenty dla *liczby* i *daty*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Tutaj znajduje się zestaw wyników.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Określanie kolumny jako daty

W poniższym przykładzie obliczamy sumę OrderQuantity i sumę wartości CenaJednostkowa pogrupowanych za pośrednictwem tygodniowych przedziałów dat.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Tutaj znajduje się zestaw wyników.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Określanie skalarnej funkcji systemowej jako daty

Ten przykład określa `SYSDATETIME` dla *daty*. Zwracana wartość jest zależna od dnia i godziny wykonywania instrukcji:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Tutaj znajduje się zestaw wyników.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Określanie skalarnych podzapytań i funkcji skalarnych jako liczby i daty

W tym przykładzie są wykorzystywane podzapytania skalarne, `MAX(OrderDate)` jako argumenty dla *liczby* i *daty*. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` służy jako sztuczny argument parametru Number, aby pokazać, jak wybrać argument *liczbowy* z listy wartości.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Określanie wyrażeń liczbowych i skalarnych funkcji systemowych jako liczby i daty

W tym przykładzie jest stosowane wyrażenie liczbowe ((10/2)) oraz skalarne funkcje systemowe (SYSDATETIME) jako argumenty liczby i daty.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Określanie funkcji okna agregacji jako liczby

Ten przykład używa funkcji okna agregacji jako argumentu dla *liczby*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Użycie niedomyślnej wartości pierwotnej

W tym przykładzie w celu wygenerowania przedziałów dat jest stosowana niedomyślna wartość ORGIN. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Zobacz też

[RZUTowanie i konwertowanie &#40;języka Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)

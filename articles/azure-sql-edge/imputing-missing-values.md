---
title: Wypełnianie odstępów czasu i brakujących wartości w programie Azure SQL Edge
description: Dowiedz się więcej na temat wypełniania przerw w czasie i brakujących wartości w usłudze Azure SQL Edge
keywords: SQL Edge, szeregów czasowych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185593"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Wypełnianie odstępów czasu i brak wartości w postaci 

Gdy chodzi o dane szeregów czasowych, często istnieje możliwość, że dane szeregów czasowych nie mają wartości atrybutów. Istnieje również możliwość, że ze względu na charakter danych lub ze względu na przerwy w zbieraniu danych istnieją *przerwy* w danych.

Na przykład podczas zbierania statystyk zużycia energii dla urządzenia inteligentnego, gdy urządzenie nie działa, w statystyce użycia będą przerwy. Podobnie w scenariuszu zbierania danych telemetrii na komputerze jest możliwe, że różne czujniki są skonfigurowane do emitowania danych przy różnych częstotliwościach, co powoduje brak wartości dla czujników. Na przykład jeśli istnieją dwie czujniki, napięcie i ciśnienie skonfigurowane odpowiednio o częstotliwości 100 Hz i 10 Hz, czujnik napięcia emituje dane co jedna część sekundy, a czujnik ciśnienia będzie emitować dane co jeden dziesiąty sekund.

W poniższej tabeli opisano zestaw danych telemetrii komputera, który został zebrany w jednym z przedziału sekund. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Istnieją dwie ważne cechy poprzedzającego zestawu danych. 

- Zestaw danych nie zawiera żadnych punktów danych dotyczących kilku sygnatur czasowych `2020-09-07 06:14:47.000` , `2020-09-07 06:14:48.000` , `2020-09-07 06:14:50.000` , `2020-09-07 06:14:53.000` i `2020-09-07 06:14:55.000` . Te sygnatury czasowe są *przerwy* w zestawie danych.  
- Brak wartości, które są reprezentowane jako w `null` przypadku odczytów napięcia i ciśnienia. 

## <a name="gap-filling"></a>Wypełnienie przerwy 

Wypełnienie przerwy jest techniką, która pomaga w tworzeniu ciągłego, uporządkowanego zestawu sygnatur czasowych, aby ułatwić analizę danych szeregów czasowych. W usłudze Azure SQL Edge Najprostszym sposobem wypełnienia przerw w zestawie danych szeregów czasowych jest zdefiniowanie tabeli tymczasowej z żądanym rozkładem czasu, a następnie wykonanie `LEFT OUTER JOIN` `RIGHT OUTER JOIN` operacji lub w tabeli zestawu danych. 

Przy użyciu `MachineTelemetry` danych przedstawionych powyżej na przykład następujące zapytanie może służyć do generowania ciągłego, uporządkowanego zestawu sygnatur czasowych do analizy. 

> [!NOTE]
> Poniższe zapytanie generuje brakujące wiersze z wartościami sygnatur czasowych i `null` wartościami atrybutów. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
Powyższe zapytanie generuje następujące dane wyjściowe zawierające wszystkie sygnatury czasowe z *jedną sekundą* w określonym zakresie.

Oto zestaw wyników

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Imputacja brakujących wartości

Poprzednie zapytanie wygenerowało brakujące sygnatury czasowe do analizy danych, ale nie zastąpiły żadne brakujące wartości (reprezentowane jako null) dla `voltage` i `pressure` odczytów. W usłudze Azure SQL Edge dodano nową składnię do języka T-SQL `LAST_VALUE()` i `FIRST_VALUE()` funkcji, które zapewniają mechanizmy do dodawania do nich wartości, na podstawie powyższych lub następujących wartości w zestawie danych. 

Nowa składnia dodaje `IGNORE NULLS` `RESPECT NULLS` klauzulę and do `LAST_VALUE()` funkcji and `FIRST_VALUE()` . Następujące zapytanie w `MachineTelemetry` zestawie danych oblicza brakujące wartości przy użyciu funkcji last_value, gdzie brakujące wartości są zamieniane na ostatnią obserwowana wartość w zestawie danych.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Oto zestaw wyników

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Następujące zapytanie dodaje brakujące wartości przy użyciu `LAST_VALUE()` `FIRST_VALUE` funkcji i. W przypadku, kolumna wyjściowa, `ImputedVoltage` brakujące wartości są zastępowane przez ostatnią zaobserwowana wartość, natomiast w przypadku kolumny wyjściowej `ImputedPressure` brakujące wartości są zastępowane przez następną zaobserwowana wartość w zestawie danych. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Oto zestaw wyników

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> Powyższe zapytanie używa funkcji, `FIRST_VALUE()` Aby zastąpić brakujące wartości przy następnej obserwowanej wartości. Ten sam wynik można osiągnąć przy użyciu `LAST_VALUE()` funkcji z `ORDER BY <ordering_column> DESC` klauzulą.

## <a name="next-steps"></a>Następne kroki 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Funkcje agregujące (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)
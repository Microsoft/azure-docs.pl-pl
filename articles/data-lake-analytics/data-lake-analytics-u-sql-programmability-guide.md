---
title: Przewodnik programowania U-SQL dla Azure Data Lake
description: Zapoznaj się z omówieniem U-SQL i programowaniem UDF w Azure Data Lake Analytics, aby umożliwić tworzenie dobrego skryptu USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510866"
---
# <a name="u-sql-programmability-guide-overview"></a>Przewodnik programowania U-SQL — Omówienie

Skrypt U-SQL jest językiem zapytań, który jest przeznaczony do obsługi dużych ilości danych obciążeń. Jedną z unikatowych funkcji języka U-SQL jest kombinacja języków deklaratywnych podobnej do języka SQL z rozszerzalnością i programowaniem udostępnianym przez język C#. W tym przewodniku skoncentrujemy się na rozszerzalności i programowaniu języka U-SQL, który jest włączony w języku C#.

## <a name="requirements"></a>Wymagania

Pobierz i zainstaluj [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Wprowadzenie do języka U-SQL  

Spójrz na następujący skrypt U-SQL:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Ten skrypt definiuje dwa zestawy wierszy: `@a` i `@results` . Zestaw wierszy `@results` jest definiowany z `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Typy i wyrażenia C# w skrypcie U-SQL

Wyrażenie U-SQL to wyrażenie języka C# połączone z operacjami logicznymi U-SQL, takimi jak `AND` , `OR` i `NOT` . Wyrażenia U-SQL mogą być używane z opcją SELECT, EXTRACT, WHERE, HAVING, GROUP BY i DECLARE. Na przykład następujący skrypt analizuje ciąg jako wartość DateTime.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Poniższy fragment kodu analizuje ciąg jako wartość DateTime w instrukcji DECLARE.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Używanie wyrażeń języka C# do konwersji typów danych

W poniższym przykładzie pokazano, jak można wykonać konwersję danych daty/godziny przy użyciu wyrażeń języka C#. W tym konkretnym scenariuszu dane daty i godziny są konwertowane na standardową datę i godzinę o północy 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Użyj wyrażeń języka C# dla dzisiejszej daty

Aby ściągnąć bieżącą datę, można użyć następującego wyrażenia języka C#: `DateTime.Now.ToString("M/d/yyyy")`

Oto przykład sposobu użycia tego wyrażenia w skrypcie:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Korzystanie z zestawów .NET

Model rozszerzalności U-SQL jest w dużym stopniu oparty na możliwości dodawania niestandardowego kodu z zestawów .NET. 

### <a name="register-a-net-assembly"></a>Rejestrowanie zestawu platformy .NET

Użyj `CREATE ASSEMBLY` instrukcji, aby umieścić zestaw .NET w SQL Database. Następnie skrypty U-SQL mogą używać tych zestawów przy użyciu `REFERENCE ASSEMBLY` instrukcji. 

Poniższy kod przedstawia sposób rejestrowania zestawu:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Poniższy kod ilustruje sposób odwoływania się do zestawu:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Zapoznaj się z [instrukcjami dotyczącymi rejestracji zestawów](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) , które szczegółowo omawiają ten temat.


### <a name="use-assembly-versioning"></a>Użyj wersji zestawu
Obecnie język U-SQL używa .NET Framework w wersji 4.7.2. Upewnij się, że własne zestawy są zgodne z tą wersją środowiska uruchomieniowego.

Jak wspomniano wcześniej, skrypt U-SQL uruchamia kod w formacie 64-bitowym (x64). Upewnij się, że kod został skompilowany do uruchamiania na x64. W przeciwnym razie zostanie wyświetlony komunikat o błędzie o niepoprawnym formacie.

Każda przekazana Biblioteka DLL zestawu i plik zasobów, na przykład inne środowisko uruchomieniowe, zestaw natywny lub plik konfiguracji, może mieć co najwyżej 400 MB. Łączny rozmiar wdrożonych zasobów, za pośrednictwem zasobu wdrażania lub odwołań do zestawów i ich dodatkowych plików, nie może przekroczyć 3 GB.

Na koniec należy pamiętać, że każda baza danych U-SQL może zawierać tylko jedną wersję danego zestawu. Jeśli na przykład wymagana jest wersja 7 i 8 biblioteki NewtonSoft Json.NET, należy zarejestrować je w dwóch różnych bazach danych. Ponadto każdy skrypt może odwoływać się tylko do jednej wersji biblioteki DLL zestawu. W tym aspekcie skrypt U-SQL jest zgodny z zasadami zarządzania zestawami w języku C# i semantyką wersji.

## <a name="use-user-defined-functions-udf"></a>Korzystanie z funkcji zdefiniowanych przez użytkownika: UDF
Funkcje języka U-SQL zdefiniowane przez użytkownika lub UDF są procedurami programowania, które akceptują parametry, wykonywania akcji (takich jak złożone obliczenia) i zwracania wyniku tej akcji jako wartości. Wartość zwracana przez funkcję UDF może być tylko jedną wartością skalarną. Funkcja UDF języka u-SQL może być wywoływana w skrypcie podstawowym języka U-SQL, podobnie jak w przypadku każdej innej funkcji skalarnej C#.

Zalecamy zainicjowanie funkcji U-SQL zdefiniowanych przez użytkownika jako **Public** i **static**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Najpierw przyjrzyjmy się prostemu przykładowi tworzenia plików UDF.

W tym scenariuszu przypadku użycia musimy określić okres obrachunkowy, łącznie z kwartałem obrachunkowym oraz miesiącem obrachunkowym pierwszego logowania dla określonego użytkownika. Pierwszy miesiąc obrachunkowy roku w naszym scenariuszu to czerwiec.

Aby obliczyć okres obrachunkowy, wprowadzamy następującą funkcję języka C#:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Po prostu oblicza miesiąc obrachunkowy i kwartał i zwraca wartość ciągu. W czerwcu pierwszy miesiąc pierwszego kwartału obrachunkowego używa "Q1: P1". W lipcu używamy "Q1: P2" i tak dalej.

Jest to zwykła funkcja języka C#, która będzie używana w naszym projekcie U-SQL.

Oto jak wygląda sekcja powiązane z kodem w tym scenariuszu:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Teraz będziemy wywoływać tę funkcję z podstawowego skryptu U-SQL. W tym celu należy podać w pełni kwalifikowaną nazwę funkcji, w tym przestrzeń nazw, która w tym przypadku jest przestrzeń nazw. Class. Function (parametr).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Poniżej przedstawiono rzeczywisty skrypt podstawowy U-SQL:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Poniżej znajduje się plik wyjściowy wykonywania skryptu:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

W tym przykładzie pokazano proste użycie wbudowanego formatu UDF w języku U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Zachowaj stan między wywołaniami UDF
Obiekty programowania U-SQL C# mogą być bardziej zaawansowane, wykorzystując interaktywność za pomocą zmiennych globalnych związanych z kodem. Przyjrzyjmy się w następującym scenariuszu przypadku użycia biznesowego.

W dużych organizacjach użytkownicy mogą przełączać się między odmianami wewnętrznych aplikacji. Mogą one obejmować program Microsoft Dynamics CRM, Power BI i tak dalej. Klienci mogą chcieć zastosować analizę telemetrii, w jaki sposób użytkownicy przełączają się między różnymi aplikacjami, czym są trendy użycia i tak dalej. Celem firmy jest zoptymalizowanie użycia aplikacji. Mogą także chcieć połączyć różne aplikacje lub konkretne procedury logowania.

Aby osiągnąć ten cel, musimy określić identyfikatory sesji i czas zwłoki między ostatnią sesją, która wystąpiła.

Musimy znaleźć poprzednie logowanie, a następnie przypisać to logowanie do wszystkich sesji, które są generowane w tej samej aplikacji. Pierwsze wyzwanie polega na tym, że skrypt podstawowy języka U-SQL nie pozwala na stosowanie obliczeń do już obliczonych kolumn z funkcją LAG. Drugie wyzwanie polega na tym, że musimy utrzymywać określoną sesję dla wszystkich sesji w tym samym czasie.

Aby rozwiązać ten problem, używana jest zmienna globalna wewnątrz sekcji kodu: `static public string globalSession;` .

Ta zmienna globalna jest stosowana do całego zestawu wierszy podczas wykonywania skryptu.

Oto sekcja kodu w naszym programie U-SQL:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Ten przykład pokazuje zmienną globalną `static public string globalSession;` używaną wewnątrz `getStampUserSession` funkcji i zainicjowaną ponownie za każdym razem, gdy parametr sesji zostanie zmieniony.

Skrypt podstawowy U-SQL jest następujący:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Funkcja `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` jest wywoływana w tym miejscu podczas obliczania drugiego zestawu wierszy pamięci. Przekazuje `UserSessionTimestamp` kolumnę i zwraca wartość do momentu `UserSessionTimestamp` zmiany.

Plik wyjściowy jest następujący:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

W tym przykładzie przedstawiono bardziej skomplikowany scenariusz przypadków użycia, w którym używana jest zmienna globalna w sekcji kodu, która jest stosowana do całego zestawu wierszy pamięci.

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Przewodnik programowania U-SQL — UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
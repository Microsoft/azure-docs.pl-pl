---
title: Przewodnik programowania U-SQL dla Azure Data Lake
description: Dowiedz się więcej o zestawie usług w Azure Data Lake Analytics, które umożliwiają tworzenie opartej na chmurze platformy danych Big Data.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 1c22aa9fb91b0a86704b95586afc1779023e85b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288952"
---
# <a name="u-sql-programmability-guide"></a>Przewodnik programowania U-SQL

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

Zapoznaj się z [instrukcjami dotyczącymi rejestracji zestawów](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) , które szczegółowo omawiają ten temat.


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

## <a name="use-user-defined-types-udt"></a>Użyj typów zdefiniowanych przez użytkownika: UDT
Typy zdefiniowane przez użytkownika lub UDT są kolejną funkcją programowalności języka U-SQL. Program U-SQL UDT działa jak zwykły typ zdefiniowany przez użytkownika w języku C#. C# jest jednoznacznie określonym językiem, który umożliwia korzystanie z wbudowanych i niestandardowych typów zdefiniowanych przez użytkownika.

Skrypt U-SQL nie może niejawnie serializować lub deserializować dowolnych UDTs, gdy UDT jest przenoszona między wierzchołkami w zestawach wierszy. Oznacza to, że użytkownik musi dostarczyć jawny program formatujący przy użyciu interfejsu IFormatter. Zapewnia to język U-SQL z metodami serializacji i deserializacji dla UDT.

> [!NOTE]
> Wbudowane wtyczki języka U-SQL i wypełniania nie można obecnie serializować lub deserializować danych UDT do lub z plików, nawet z zestawem IFormatter. Dlatego podczas zapisywania danych UDT do pliku z instrukcją OUTPUT lub odczytywania go za pomocą ekstraktora należy przekazać go jako ciąg lub tablicę bajtów. Następnie należy wywołać kod serializacji i deserializacji (oznacza to, że Metoda ToString () obiektu UDT jest jawnie. Zdefiniowane przez użytkownika programy wyodrębniające i wyskakujące, z drugiej strony, mogą odczytywać i zapisywać UDTs.

Jeśli spróbujemy użyć UDT w EKSTRAKTORze lub wypełnieniu (z poprzedniego wyboru), jak pokazano poniżej:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Zostanie wyświetlony następujący błąd:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Aby można było korzystać z UDT w wypełnieniu, musimy serializować go do ciągu za pomocą metody ToString () lub utworzyć niestandardowy element.

Nie można obecnie używać UDTs w grupie GROUP BY. Jeśli UDT jest używany w grupie przez, zgłaszany jest następujący błąd:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Aby zdefiniować UDT, musimy:

* Dodaj następujące przestrzenie nazw:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Dodaj `Microsoft.Analytics.Interfaces` , który jest wymagany dla interfejsów UDT. Ponadto `System.IO` może być konieczne zdefiniowanie interfejsu IFormatter.

* Zdefiniuj użyty-zdefiniowany typ z atrybutem SqlUserDefinedType.

**SqlUserDefinedType** służy do oznaczania definicji typu w zestawie jako typu zdefiniowanego przez użytkownika (UDT) w języku U-SQL. Właściwości w atrybucie odzwierciedlają fizyczne cechy UDT. Klasa ta nie może być dziedziczona.

SqlUserDefinedType jest wymaganym atrybutem dla definicji UDT.

Konstruktor klasy:  

* SqlUserDefinedTypeAttribute (typ programu formatującego)

* Typ programu formatującego: parametr wymagany do zdefiniowania programu formatującego typu UDT — w `IFormatter` tym celu należy przesłać tutaj typ interfejsu.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typowy typ UDT wymaga również definicji interfejsu IFormatter, jak pokazano w następującym przykładzie:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Interfejs umożliwia serializacji i deserializacji grafu obiektów z typem głównym \<typeparamref name="T"> .

\<typeparam name="T">Typ główny grafu obiektów do serializacji i deserializacji.

* **Deserializacja: deserializacji dane**z dostarczonego strumienia i reprodukcji grafu obiektów.

* **Serializacja**: serializacja obiektu lub grafu obiektów z podanym elementem głównym do podanego strumienia.

`MyType` wystąpienie: wystąpienie typu.  
`IColumnWriter` składnik zapisywania/ `IColumnReader` czytelnik: źródłowy strumień kolumn.  
`ISerializationContext` Context: enum, który definiuje zestaw flag, który określa źródłowy lub docelowy kontekst strumienia podczas serializacji.

* **Pośredni**: określa, że kontekst źródłowy lub docelowy nie jest magazynem utrwalonym.

* **Trwałość**: określa, że kontekst źródłowy lub docelowy to utrwalony magazyn.

Jako zwykły typ C# definicja UDT języka U-SQL może zawierać przesłonięcia dla operatorów, takich jak +/= =/! =. Może również zawierać metody statyczne. Na przykład jeśli będziemy używać tego UDT jako parametru do minimalnej funkcji agregującej U-SQL, musimy zdefiniować < przesłonięcia operatora.

Wcześniej w tym przewodniku przedstawiono przykład identyfikacji okresu obrachunkowego z określonego dnia w formacie `Qn:Pn (Q1:P10)` . Poniższy przykład pokazuje, jak zdefiniować typ niestandardowy dla wartości okresu obrachunkowego.

Poniżej znajduje się przykład sekcji związanej z kodem z niestandardowym interfejsem UDT i IFormatter:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Zdefiniowany typ zawiera dwie liczby: kwartał i miesiąc. Operatory `==/!=/>/<` i metoda statyczna `ToString()` są zdefiniowane w tym miejscu.

Jak wspomniano wcześniej, można użyć UDT w wyrażeniach SELECT, ale nie można używać go w wypełnieniu/EKSTRAKTORze bez serializacji niestandardowej. Musi być serializowany jako ciąg z `ToString()` lub używany z niestandardowym wyciągiem/ekstraktorem.

Teraz omawiamy użycie UDT. W sekcji kodu została zmieniona nasza funkcja GetFiscalPeriod na następujące:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Jak widać, zwraca wartość naszego typu FiscalPeriod.

Tutaj przedstawiono przykład korzystania z niego w skrypcie podstawowym języka U-SQL. W tym przykładzie pokazano różne formy wywołania UDT ze skryptu U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Oto przykład pełnej sekcji dotyczącej kodu:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Użyj agregacji zdefiniowanych przez użytkownika: UDAGG
Agregacje zdefiniowane przez użytkownika to wszelkie funkcje związane z agregacją, które nie są dostarczane z użyciem języka U-SQL. Przykładem może być agregacja, która umożliwia wykonywanie niestandardowych obliczeń matematycznych, łączenie ciągów, manipulowanie za pomocą ciągów i tak dalej.

Zdefiniowana przez użytkownika definicja klasy bazowej agregacji jest następująca:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** wskazuje, że typ powinien być zarejestrowany jako agregacja zdefiniowana przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDefinedType jest **opcjonalny** dla definicji UDAGG.


Klasa bazowa umożliwia przekazywanie trzech parametrów abstrakcyjnych: dwa jako parametry wejściowe i jeden jako wynik. Typy danych są zmienne i powinny być zdefiniowane podczas dziedziczenia klas.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** wywołuje jeden raz dla każdej grupy podczas obliczania. Zapewnia procedurę inicjowania dla każdej grupy agregacji.  
* **Gromadzenie** jest wykonywane raz dla każdej wartości. Zapewnia główną funkcjonalność algorytmu agregacji. Może służyć do agregowania wartości przy użyciu różnych typów danych, które są zdefiniowane podczas dziedziczenia klas. Może akceptować dwa parametry zmiennych typów danych.
* **Zakończenie** jest wykonywane raz dla każdej grupy agregacji na końcu przetwarzania, aby wyprowadzić wynik dla każdej grupy.

Aby zadeklarować poprawne dane wejściowe i wyjściowe, Użyj definicji klasy w następujący sposób:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: pierwszy parametr do akumulacji
* T2: drugi parametr do akumulacji
* TResult: zwracany typ przerwania

Na przykład:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

lub

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Używanie UDAGG w języku U-SQL
Aby użyć UDAGG, najpierw Zdefiniuj ją w kodzie lub odwołuje się do niej z istniejącej biblioteki DLL programowania, jak opisano wcześniej.

Następnie użyj następującej składni:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Oto przykład UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

I podstawowy skrypt U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

W tym scenariuszu przypadku użycia łączymy identyfikatory GUID klas dla określonych użytkowników.

## <a name="use-user-defined-objects-udo"></a>Użyj obiektów zdefiniowanych przez użytkownika: UDO
Język U-SQL umożliwia Definiowanie niestandardowych obiektów programistycznych, które są nazywane obiektami zdefiniowanymi przez użytkownika lub UDO.

Poniżej znajduje się lista UDO w języku U-SQL:

* Ekstraktory zdefiniowane przez użytkownika
    * Wyodrębnij wiersz według wiersza
    * Służy do implementowania wyodrębniania danych z niestandardowych plików strukturalnych

* Wyskakujące definicje zdefiniowane przez użytkownika
    * Wiersz danych wyjściowych według wiersza
    * Używane do wyprowadzania niestandardowych typów danych lub niestandardowych formatów plików

* Procesory zdefiniowane przez użytkownika
    * Weź jeden wiersz i Wygeneruj jeden wiersz
    * Służy do zmniejszania liczby kolumn lub tworzenia nowych kolumn z wartościami, które są wyprowadzane z istniejącego zestawu kolumn

* Appliers zdefiniowany przez użytkownika
    * Weź jeden wiersz i Wygeneruj 0 do n wierszy
    * Stosowane z ZASTOSOWANIEm ZEWNĘTRZNYm/KRZYŻowym

* Połączenia zdefiniowane przez użytkownika
    * Łączy zestawy wierszy--zdefiniowane przez użytkownika sprzężeń

* Ograniczenia zdefiniowane przez użytkownika
    * Zrób n wierszy i Wygeneruj jeden wiersz
    * Służy do zmniejszania liczby wierszy

UDO jest zazwyczaj wywoływana jawnie w skrypcie U-SQL w ramach następujących instrukcji U-SQL:

* EXTRACT
* ROZDZIELCZOŚCI
* PODSTAWOWYCH
* ŻĄDANY
* ZMNIEJSZENIE

> [!NOTE]  
> UDO są ograniczone do zużywania 0,5 GB pamięci.  To ograniczenie pamięci nie ma zastosowania do lokalnych wykonań.

## <a name="use-user-defined-extractors"></a>Korzystanie z wyodrębniania zdefiniowanego przez użytkownika
Język U-SQL umożliwia importowanie danych zewnętrznych przy użyciu instrukcji EXTRACT. Instrukcja EXTRACT może używać wbudowanych UDO Extracts:  

* *Extracts. Text ()*: zapewnia wyodrębnianie z rozdzielanych plików tekstowych różnych kodowań.

* *Extractors.Csv ()*: zapewnia ekstrakcję z plików z wartościami rozdzielanymi przecinkami (CSV) różnych kodowań.

* *Extracts. tsv ()*: zapewnia ekstrakcję z plików z wartościami rozdzielanymi tabulatorami (tsv) różnych kodowań.

Może być przydatne do opracowania niestandardowego ekstraktora. Może to być przydatne podczas importowania danych, jeśli chcemy wykonać dowolne z następujących zadań:

* Zmodyfikuj dane wejściowe, dzieląc kolumny i modyfikując poszczególne wartości. Funkcja procesora jest lepsza do łączenia kolumn.
* Analizuj dane bez struktury, takie jak strony sieci Web i wiadomości e-mail, lub częściowo niestrukturalne dane, takie jak XML/JSON.
* Analizuj dane w nieobsługiwanym kodowaniu.

Aby zdefiniować zdefiniowany przez użytkownika Ekstraktor lub LUCZ, musimy utworzyć `IExtractor` interfejs. Wszystkie parametry wejściowe do ekstraktora, takie jak ograniczniki kolumn/wierszy i kodowanie, muszą być zdefiniowane w konstruktorze klasy. `IExtractor`Interfejs powinien również zawierać definicję `IEnumerable<IRow>` przesłonięcia w następujący sposób:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Atrybut **SqlUserDefinedExtractor** wskazuje, że typ powinien być zarejestrowany jako Ekstraktor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedExtractor jest opcjonalnym atrybutem definicji LUCZ. Służy do definiowania właściwości AtomicFileProcessing dla obiektu LUCZ.

* AtomicFileProcessing bool   

* **true** = wskazuje, że ten Ekstraktor wymaga niepodzielnych plików wejściowych (JSON, XML,...)
* **wartość false** = wskazuje, że ten Ekstraktor może obsłużyć pliki podzielone/rozproszone (CSV, SEQ,...)

Główne obiekty programowalności LUCZ są **danymi wejściowymi** i **wyjściowymi**. Obiekt wejściowy służy do wyliczania danych wejściowych jako `IUnstructuredReader` . Obiekt wyjściowy służy do ustawiania danych wyjściowych w wyniku działania wyodrębniania.

Dane wejściowe są dostępne za pomocą `System.IO.Stream` i `System.IO.StreamReader` .

W przypadku wyliczania kolumn wejściowych należy najpierw podzielić strumień wejściowy przy użyciu ogranicznika wiersza.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Następnie można dodatkowo podzielić wiersz wejściowy na części kolumn.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Aby ustawić dane wyjściowe, używamy `output.Set` metody.

Ważne jest, aby zrozumieć, że niestandardowy Ekstraktor tylko wyprowadza kolumny i wartości, które są zdefiniowane przy użyciu danych wyjściowych. Ustaw wywołanie metody.

```csharp
output.Set<string>(count, part);
```

Rzeczywiste dane wyjściowe wyekstraktoru są wyzwalane przez wywołanie `yield return output.AsReadOnly();` .

Poniżej znajduje się przykład wyekstraktora:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

W tym scenariuszu przypadku użycia Ekstraktor ponownie generuje identyfikator GUID dla kolumny "GUID" i konwertuje wartości kolumny "User" na wielkie litery. Niestandardowe ekstrakty mogą generować bardziej skomplikowane wyniki przez analizowanie danych wejściowych i manipulowanie nimi.

Poniżej przedstawiono podstawowy skrypt U-SQL, który używa wyskakującego ekstraktora:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Użyj zdefiniowanych przez użytkownika wyskakujących okienek
Wypełnianie zdefiniowane przez użytkownika to inny UDO języka U-SQL, który pozwala na rozbudowa wbudowanej funkcji języka U-SQL. Podobnie jak w przypadku wyodrębniania, istnieje kilka wbudowanych wypełnień.

* *. Text ()*: zapisuje dane do rozdzielanych plików tekstowych różnych kodowań.
* *Outputters.Csv ()*: zapisuje dane w plikach z wartościami rozdzielanymi przecinkami (CSV) różnych kodowań.
* *. Tsv ()*: zapisuje dane w plikach z wartościami rozdzielanymi tabulatorami (tsv) różnych kodowań.

Niestandardowy element wypełnień umożliwia zapisanie danych w niestandardowym formacie zdefiniowanym. Może to być przydatne w przypadku następujących zadań:

* Zapisywanie danych w plikach z częściową strukturą lub bez struktury.
* Zapisywanie danych nie jest obsługiwane.
* Modyfikowanie danych wyjściowych lub Dodawanie atrybutów niestandardowych.

Aby zdefiniować wystawcy zdefiniowany przez użytkownika, musimy utworzyć `IOutputter` interfejs.

Poniżej znajduje się `IOutputter` Implementacja klasy bazowej:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Wszystkie parametry wejściowe do elementu wypełnienia, takie jak ograniczniki kolumn/wierszy, kodowanie i tak dalej, muszą być zdefiniowane w konstruktorze klasy. `IOutputter`Interfejs powinien również zawierać definicję `void Output` przesłonięcia. `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`Opcjonalnie można ustawić atrybut na potrzeby przetwarzania plików niepodzielnych. Aby uzyskać więcej informacji, zobacz następujące szczegóły.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` jest wywoływana dla każdego wiersza wejściowego. Zwraca `IUnstructuredWriter output` zestaw wierszy.
* Klasa konstruktora służy do przekazywania parametrów do zdefiniowanego przez użytkownika wystawcy.
* `Close` służy do opcjonalnego przesłonięcia w celu zwolnienia drogi lub określenia czasu zapisania ostatniego wiersza.

Atrybut **SqlUserDefinedOutputter** wskazuje, że typ powinien być zarejestrowany jako element, zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedOutputter jest opcjonalnym atrybutem dla definicji elementu wyszukania zdefiniowanej przez użytkownika. Służy do definiowania właściwości AtomicFileProcessing.

* AtomicFileProcessing bool   

* **true** = wskazuje, że ten wynik nie wymaga niepodzielnych plików wyjściowych (JSON, XML,...)
* **wartość false** = wskazuje, że ten wynik może dotyczyć plików podzielonych/rozproszonych (CSV, SEQ,...)

Główne obiekty programowalności są **wierszami** i **danymi wyjściowymi**. Obiekt **Row** służy do wyliczania danych wyjściowych jako `IRow` interfejsów. Dane **wyjściowe** są używane do ustawiania danych wyjściowych w pliku docelowym.

Dostęp do danych wyjściowych uzyskuje się za pomocą `IRow` interfejsu. Dane wyjściowe są przekazywane w wierszu jednocześnie.

Poszczególne wartości są wyliczane przez wywołanie metody get interfejsu IRow:

```csharp
row.Get<string>("column_name")
```

Nazwy poszczególnych kolumn można określić, wywołując `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Takie podejście umożliwia tworzenie elastycznego elementu wymawianego dla dowolnego schematu metadanych.

Dane wyjściowe są zapisywane do pliku przy użyciu `System.IO.StreamWriter` . Parametr Stream jest ustawiany `output.BaseStream` jako część `IUnstructuredWriter output` .

Należy pamiętać, że należy zwrócić bufor danych do pliku po każdej iteracji wiersza. Ponadto, `StreamWriter` obiekt musi być używany z włączonym atrybutem jednorazowym (domyślnie) i słowem kluczowym **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

W przeciwnym razie wywołanie metody Flush () jawnie po każdej iteracji. Pokazujemy to w poniższym przykładzie.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ustawianie nagłówków i stopek dla elementu predefiniowanego zdefiniowanego przez użytkownika
Aby ustawić nagłówek, Użyj pojedynczego przepływu wykonywania iteracji.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Kod w pierwszym `if (isHeaderRow)` bloku jest wykonywany tylko raz.

Dla stopki Użyj odwołania do wystąpienia `System.IO.Stream` obiektu ( `output.BaseStream` ). Napisz stopkę w metodzie Close () `IOutputter` interfejsu.  (Aby uzyskać więcej informacji, zobacz Poniższy przykład).

Poniżej znajduje się przykład elementu predefiniowanego zdefiniowanego przez użytkownika:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

I skrypt podstawowy U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Jest to niektóry element HTML, który tworzy plik HTML z danymi tabeli.

### <a name="call-outputter-from-u-sql-base-script"></a>Wywołaj instrukcję z podstawowego skryptu języka U-SQL
Aby wywołać niestandardowy element wywołujący z podstawowego skryptu U-SQL, należy utworzyć nowe wystąpienie tego obiektu.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby uniknąć tworzenia wystąpienia obiektu w skrypcie podstawowym, możemy utworzyć otokę funkcji, jak pokazano w poprzednim przykładzie:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

W takim przypadku oryginalne wywołanie wygląda następująco:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Korzystanie z procesorów zdefiniowanych przez użytkownika
Procesor zdefiniowany przez użytkownika lub UDP jest typem UDO języka U-SQL, który umożliwia przetwarzanie wierszy przychodzących przez zastosowanie funkcji programowalności. Protokół UDP umożliwia łączenie kolumn, modyfikowanie wartości i dodawanie nowych kolumn w razie potrzeby. W zasadzie ułatwia przetwarzanie zestawu wierszy w celu utworzenia wymaganych elementów danych.

Aby zdefiniować protokół UDP, musimy utworzyć `IProcessor` interfejs z `SqlUserDefinedProcessor` atrybutem, który jest opcjonalny dla protokołu UDP.

Ten interfejs powinien zawierać definicję `IRow` przesłonięcia zestawu wierszy interfejsu, jak pokazano w następującym przykładzie:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** wskazuje, że typ powinien być zarejestrowany jako procesor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDefinedProcessor jest **opcjonalny** dla definicji UDP.

Główne obiekty programowalności są **danymi wejściowymi** i **wyjściowymi**. Obiekt wejściowy służy do wyliczania kolumn wejściowych i danych wyjściowych oraz do ustawiania danych wyjściowych w wyniku działania procesora.

W przypadku wyliczania kolumn wejściowych używamy `input.Get` metody.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametr `input.Get` metody jest kolumną, która jest przenoszona jako część `PRODUCE` klauzuli `PROCESS` instrukcji skryptu podstawowego U-SQL. W tym miejscu musimy użyć poprawnego typu danych.

W przypadku danych wyjściowych Użyj `output.Set` metody.

Należy pamiętać, że producent niestandardowy tylko wyprowadza kolumny i wartości, które są zdefiniowane za pomocą `output.Set` wywołania metody.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Rzeczywiste dane wyjściowe procesora są wyzwalane przez wywołanie `return output.AsReadOnly();` .

Poniżej znajduje się przykład procesora:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

W tym scenariuszu przypadku użycia procesor generuje nową kolumnę o nazwie "full_description" przez połączenie istniejących kolumn — w tym przypadku "użytkownik" w Wielkiej litery i "des". Powoduje również ponowne wygenerowanie identyfikatora GUID i zwrócenie oryginalnych i nowych wartości identyfikatora GUID.

Jak widać w poprzednim przykładzie, można wywołać metody języka C# podczas `output.Set` wywołania metody.

Poniżej znajduje się przykład podstawowego skryptu U-SQL, który używa niestandardowego procesora:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Użyj appliers zdefiniowanych przez użytkownika
Zdefiniowany przez użytkownika applier języka U-SQL umożliwia wywoływanie niestandardowej funkcji C# dla każdego wiersza, który jest zwracany przez wyrażenie tabeli zewnętrznej zapytania. Prawidłowe dane wejściowe są oceniane dla każdego wiersza z lewej strony wejściowej, a tworzone wiersze są łączone dla końcowych danych wyjściowych. Lista kolumn, które są tworzone przez operator APPLY, jest kombinacją zestawu kolumn po lewej stronie i po prawej stronie.

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

## <a name="use-user-defined-combiners"></a>Używanie złączów zdefiniowanych przez użytkownika
Zdefiniowany przez użytkownika element łączenia lub UDC umożliwia łączenie wierszy z wierszy z lewej i po prawej stronie, na podstawie logiki niestandardowej. Program łączący zdefiniowany przez użytkownika jest używany z wyrażeniem łączenia.

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

## <a name="use-user-defined-reducers"></a>Używanie zmniejszeń zdefiniowanych przez użytkownika

Język U-SQL umożliwia pisanie niestandardowych funkcji zmniejszania zestawów wierszy w języku C# przy użyciu zdefiniowanej przez użytkownika platformy rozszerzalności operatora i implementowanie interfejsu IReducer.

W celu wyeliminowania niepotrzebnych wierszy podczas wyodrębniania danych (import) można użyć ograniczenia zdefiniowanego przez użytkownika lub UDR. Można go również użyć do manipulowania i obliczenia wierszy i kolumn. Na podstawie logiki programowania można także określić, które wiersze muszą zostać wyodrębnione.

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

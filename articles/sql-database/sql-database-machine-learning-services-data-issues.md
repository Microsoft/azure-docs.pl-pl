---
title: Praca z typami i obiektami danych języka R i SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak pracować z typami danych i obiektami danych w języku R za pomocą usługi Azure SQL Database przy użyciu usług uczenia maszynowego (wersja zapoznawcza), w tym typowe problemy, które mogą wystąpić.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453203"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)

W tym artykule omówiono niektóre z typowych problemów, które mogą wystąpić podczas przenoszenia danych między bazą danych R i SQL w [usługach uczenia maszynowego (z R) w bazie danych SQL azure.](sql-database-machine-learning-services-overview.md) Doświadczenie zdobyte w tym ćwiczeniu zapewnia podstawowe tło podczas pracy z danymi we własnym skrypcie.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Typowe problemy, które mogą wystąpić obejmują:

- Typy danych czasami nie są zgodne
- Mogą mieć miejsce konwersje niejawne
- Operacje rzutowania i konwertowania są czasami wymagane
- R i SQL używają różnych obiektów danych

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeniach, musisz najpierw mieć [włączona usługa Azure SQL Database z włączonymi usługami uczenia maszynowego (z funkcją języka R).](sql-database-machine-learning-services-overview.md)

- Upewnij się, że zainstalowano najnowsze [program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przewodniku Szybki start użyjesz programu SSMS.

## <a name="working-with-a-data-frame"></a>Praca z ramką danych

Gdy skrypt zwraca wyniki z języka R do SQL, musi zwrócić dane jako **data.frame**. Każdy inny typ obiektu, który można wygenerować w skrypcie — czy to lista, czynnik, wektor lub dane binarne - musi być przekonwertowany na ramkę danych, jeśli chcesz wyprowadzić go jako część wyników procedury składowanej. Na szczęście istnieje wiele funkcji R do obsługi zmiany innych obiektów do ramki danych. Można nawet serializować model binarny i zwracać go w ramce danych, co zrobisz w dalszej części tego artykułu.

Najpierw poeksperymentujmy z niektórymi podstawowymi obiektami Języka R — wektorami, macierzami i listami — i zobaczmy, jak konwersja do ramki danych zmienia dane wyjściowe przekazane do języka SQL.

Porównaj te dwa skrypty "Hello World" w R. Skrypty wyglądają prawie identycznie, ale pierwsza zwraca pojedynczą kolumnę trzech wartości, podczas gdy druga zwraca trzy kolumny z pojedynczą wartością.

**Przykład 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Przykład 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Dlaczego wyniki są tak różne?

Odpowiedź można zwykle znaleźć za `str()` pomocą polecenia R. Dodaj funkcję `str(object_name)` w dowolnym miejscu skryptu Języka R, aby schemat danych określonego obiektu R został zwrócony jako komunikat informacyjny. Wiadomości można wyświetlać na karcie **Wiadomości** w ssms.

Aby dowiedzieć się, dlaczego przykład 1 i przykład `str(OutputDataSet)` 2 mają `@script` takie różne wyniki, wstaw wiersz na końcu definicji zmiennej w każdej instrukcji, w tym stylu:

**Przykład 1 z dodaną funkcją str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Przykład 2 z dodaną funkcją str**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Teraz przejrzyj tekst w **wiadomościach,** aby zobaczyć, dlaczego dane wyjściowe są różne.

**Wyniki — przykład 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Wyniki - Przykład 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Jak widać, niewielka zmiana składni R miała duży wpływ na schemat wyników. Dla wszystkich szczegółów, różnice w typach danych R są wyjaśnione w szczegółach w sekcji *Struktury danych* w ["Zaawansowane R" przez Hadley Wickham](http://adv-r.had.co.nz).

Na razie należy tylko pamiętać, że należy sprawdzić oczekiwane wyniki podczas wymuszania R obiektów do ramek danych.

> [!TIP]
> Można również użyć funkcji tożsamości `is.matrix`R, takich jak , `is.vector`aby zwrócić informacje o wewnętrznej strukturze danych.

## <a name="implicit-conversion-of-data-objects"></a>Niejawna konwersja obiektów danych

Każdy obiekt danych R ma własne reguły obsługi wartości w połączeniu z innymi obiektami danych, jeśli dwa obiekty danych mają taką samą liczbę wymiarów lub jeśli dowolny obiekt danych zawiera heterogeniczne typy danych.

Załóżmy na przykład, że chcesz wykonać mnożenie macierzy przy użyciu języka R. Chcesz pomnożyć macierz jednokolumnową z trzema wartościami przez tablicę z czterema wartościami i oczekiwać w rezultacie macierzy 4x3.

Najpierw utwórz małą tabelę danych testowych.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Teraz uruchom następujący skrypt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

W obszarze obejmuje kolumna trzech wartości jest konwertowana na macierz jednokolumnową. Ponieważ macierz jest tylko specjalny przypadek tablicy `y` w R, tablica jest niejawnie wymuszane do macierzy jednokolumnowej, aby dwa argumenty są zgodne.

**Wyniki**

|Col1|Col2|Okręg wyborczy Col3|Okręg wyborczy Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Należy jednak zwrócić uwagę na to, `y`co się stanie po zmianie rozmiaru tablicy .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Teraz R zwraca pojedynczą wartość jako wynik.

**Wyniki**
    
|Col1|
|---|
|1542|

Dlaczego? W takim przypadku, ponieważ dwa argumenty mogą być obsługiwane jako wektory o tej samej długości, R zwraca produkt wewnętrzny jako macierz.  Jest to oczekiwane zachowanie zgodnie z zasadami algebry liniowej. Jednak może to spowodować problemy, jeśli aplikacja podrzędna oczekuje, że schemat danych wyjściowych nigdy się nie zmieni!

## <a name="merge-or-multiply-columns-of-different-length"></a>Scalanie lub mnożenie kolumn o różnej długości

R zapewnia dużą elastyczność pracy z wektorami o różnych rozmiarach i łączenia tych struktur podobnych do kolumn w ramki danych. Listy wektorów może wyglądać jak tabela, ale nie są zgodne ze wszystkimi regułami, które regulują tabele bazy danych.

Na przykład poniższy skrypt definiuje tablicę numeryczną o długości 6 `df1`i przechowuje ją w zmiennej R . Tablica numeryczna jest następnie łączona z liczbami całkowitymi tabeli RTestData (utworzonej powyżej), która zawiera `df2`trzy (3) wartości, aby utworzyć nową ramkę danych, .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Aby wypełnić ramkę danych, R powtarza elementy pobrane z RTestData tyle razy, ile `df1`potrzeba, aby dopasować liczbę elementów w tablicy .

**Wyniki**
    
|*Col2*|*Okręg wyborczy Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Należy pamiętać, że ramka danych wygląda tylko jak tabela, ale w rzeczywistości jest listą wektorów.

## <a name="cast-or-convert-sql-data"></a>Rzutowanie lub konwertowanie danych SQL

R i SQL nie używają tych samych typów danych, więc po uruchomieniu kwerendy w języku SQL, aby uzyskać dane, a następnie przekazać je do środowiska wykonawczego języka R, zwykle ma miejsce pewnego rodzaju konwersji niejawnej. Inny zestaw konwersji odbywa się po powrocie danych z języka R do SQL.

- SQL wypycha dane z kwerendy do procesu języka R i konwertuje je na wewnętrzną reprezentację w celu zwiększenia wydajności.
- Środowisko wykonawcze R ładuje dane do zmiennej data.frame i wykonuje własne operacje na danych.
- Aparat bazy danych zwraca dane do sql przy użyciu zabezpieczonego połączenia wewnętrznego i przedstawia dane pod względem typów danych SQL.
- Dane można uzyskać, łącząc się z sql przy użyciu biblioteki klienta lub sieci, które mogą wydawać zapytania SQL i obsługiwać tabelaryczne zestawy danych. Ta aplikacja kliencka może potencjalnie wpłynąć na dane w inny sposób.

Aby zobaczyć, jak to działa, uruchom kwerendę, taką jak ta w magazynie danych [AdventureWorksDW.](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) Ten widok zwraca dane sprzedaży używane do tworzenia prognoz.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Można użyć dowolnej wersji AdventureWorks lub utworzyć inną kwerendę przy użyciu własnej bazy danych. Chodzi o to, aby spróbować obsłużyć niektóre dane, które zawierają tekst, datetime i wartości liczbowe.

Teraz spróbuj użyć tej kwerendy jako dane wejściowe do procedury składowanej.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Jeśli zostanie wyświetlony błąd, prawdopodobnie musisz wprowadzić pewne zmiany w tekście kwerendy. Na przykład predykat ciągu w klauzuli WHERE musi być ujęty w dwa zestawy pojedynczych cudzysłowów.

Po zakończeniu kwerendy przejrzyj `str` wyniki funkcji, aby zobaczyć, jak R traktuje dane wejściowe.

**Wyniki**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Kolumna datetime została przetworzona przy użyciu typu danych R, **POSIXct**.
- Kolumna tekstowa "ProductSeries" została zidentyfikowana jako **czynnik,** co oznacza zmienną kategoryczną. Wartości ciągów są domyślnie traktowane jako czynniki. Jeśli przekażesz ciąg do R, jest konwertowany na liczę całkowitą do użytku wewnętrznego, a następnie mapowane z powrotem do ciągu na dane wyjściowe.

## <a name="summary"></a>Podsumowanie

Nawet z tych krótkich przykładów można zobaczyć potrzebę sprawdzenia skutków konwersji danych podczas przekazywania zapytań SQL jako dane wejściowe. Ponieważ niektóre typy danych SQL nie są obsługiwane przez R, należy wziąć pod uwagę następujące sposoby, aby uniknąć błędów:

- Przetestuj dane z wyprzedzeniem i sprawdź kolumny lub wartości w schemacie, które mogą być problemem po przekazaniu do kodu R.
- Określ kolumny w wejściowym źródle danych `SELECT *`indywidualnie, a nie za pomocą programu i dowiedz się, jak każda kolumna będzie obsługiwana.
- Podczas przygotowywania danych wejściowych należy wykonać jawne rzutowania, aby uniknąć niespodzianek.
- Unikaj przekazywania kolumn danych (takich jak guids lub rowguids), które powodują błędy i nie są przydatne do modelowania.

Aby uzyskać więcej informacji na temat obsługiwanych i nieobsługiconych typów danych języka R, zobacz [Biblioteki języka R i typy danych](/sql/advanced-analytics/r/r-libraries-and-data-types).

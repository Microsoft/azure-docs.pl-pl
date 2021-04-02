---
title: Przykład In-Memory
description: Wypróbuj Azure SQL Database technologie In-Memory z przykładem OLTP i magazynu kolumn.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786585"
---
# <a name="in-memory-sample"></a>Przykład In-Memory
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Technologie In-Memory w Azure SQL Database umożliwiają zwiększenie wydajności aplikacji i może obniżyć koszty związane z bazą danych. Korzystając z technologii In-Memory w Azure SQL Database, można osiągnąć ulepszenia wydajności w różnych obciążeniach.

W tym artykule zobaczysz dwa przykłady ilustrujące zastosowanie In-Memory OLTP, a także indeksów magazynu kolumn w Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:

- [Omówienie OLTP i scenariusze użycia w pamięci (w](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) tym informacje na temat analiz przypadków klientów i informacji na temat rozpoczynania pracy)
- [Dokumentacja usługi In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybrydowe przetwarzanie transakcyjne/analityczne (HTAP), znane także jako [Analiza operacyjna w czasie rzeczywistym](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Zainstaluj próbkę In-Memory OLTP

Przykładową bazę danych AdventureWorksLT można utworzyć za pomocą kilku kliknięć w [Azure Portal](https://portal.azure.com/). Następnie w krokach w tej sekcji wyjaśniono, jak można wzbogacić bazę danych AdventureWorksLT za pomocą obiektów OLTP i zademonstrować korzyści In-Memory z wydajności.

Aby uzyskać więcej uproszczony, ale bardziej atrakcyjny wizualnie pokaz wydajności dla In-Memory OLTP, zobacz:

- Wydanie: [w pamięci-OLTP-Demonstracja-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Kod źródłowy: [w pamięci-OLTP-Demonstracja-kod](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Kroki instalacji

1. W [Azure Portal](https://portal.azure.com/)Utwórz bazę danych Premium lub krytyczne dla działania firmy na serwerze. Ustaw **Źródło** na przykładową bazę danych AdventureWorksLT. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie pierwszej bazy danych w Azure SQL Database](database/single-database-create-quickstart.md).

2. Nawiąż połączenie z bazą danych za pomocą SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms).

3. Skopiuj [skrypt OLTP języka Transact-SQL w pamięci](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) do Schowka. Skrypt T-SQL tworzy niezbędne In-Memory obiektów w przykładowej bazie danych AdventureWorksLT utworzonej w kroku 1.

4. Wklej skrypt T-SQL do programu SSMS, a następnie wykonaj skrypt. `MEMORY_OPTIMIZED = ON`Klauzula CREATE TABLE instrukcji jest istotna. Na przykład:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Błąd 40536

Jeśli zostanie wyświetlony komunikat o błędzie 40536 podczas uruchamiania skryptu T-SQL, uruchom następujący skrypt T-SQL, aby sprawdzić, czy baza danych obsługuje pamięć:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Wynik **0** oznacza, że In-Memory nie jest obsługiwana i **1** oznacza, że jest obsługiwany. Aby zdiagnozować problem, upewnij się, że baza danych znajduje się w warstwie usługi Premium.

### <a name="about-the-created-memory-optimized-items"></a>Informacje o utworzonych elementach zoptymalizowanych pod kątem pamięci

**Tabele**: przykład zawiera następujące tabele zoptymalizowane pod kątem pamięci:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demonstracja. DemoSalesOrderHeaderSeed
- Demonstracja. DemoSalesOrderDetailSeed

Tabele zoptymalizowane pod kątem pamięci można sprawdzić za pomocą **Eksplorator obiektów** w programie SSMS. Kliknij prawym przyciskiem myszy pozycję **tabele**  >    >  **Ustawienia filtru** filtr  >  **jest zoptymalizowane pod kątem pamięci**. Wartość jest równa 1.

Lub można wykonywać zapytania dotyczące widoków wykazu, takich jak:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Procedura składowana skompilowana w sposób macierzysty**: można sprawdzić SalesLT.usp_InsertSalesOrder_inmem za pomocą zapytania widoku wykazu:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Uruchamianie przykładowego obciążenia OLTP

Jedyną różnicą między poniższymi *procedurami składowanymi* jest użycie w pierwszej kolejności wersji zoptymalizowanych pod kątem pamięci, podczas gdy druga procedura używa zwykłych tabel na dysku:

- Tabeli SalesLT **.** usp_InsertSalesOrder **_inmem**
- Tabeli SalesLT **.** usp_InsertSalesOrder **_ondisk**

W tej sekcji dowiesz się, jak za pomocą narzędzia przydatne **ostress.exe** wykonać dwa procedury składowane na poziomach obciążeniowych. Można porównać czas trwania dwóch przebiegów obciążeniowych.

Po uruchomieniu ostress.exe zalecamy przekazanie wartości parametrów przeznaczonych dla obu następujących elementów:

- Uruchom dużą liczbę jednoczesnych połączeń przy użyciu polecenia-N100.
- Każda pętla połączeń ma setki razy za pomocą-R500.

Można jednak zacząć od dużo mniejszych wartości takich jak-N10 i-R50, aby upewnić się, że wszystko działa.

### <a name="script-for-ostressexe"></a>Skrypt dla ostress.exe

W tej sekcji jest wyświetlany skrypt T-SQL osadzony w naszym ostress.exe wierszu polecenia. Skrypt używa elementów, które zostały utworzone przez skrypt T-SQL, który został zainstalowany wcześniej.

Poniższy skrypt wstawia przykładowy porządek sprzedaży z pięcioma wierszami wierszy do następujących *tabel* zoptymalizowanych pod kątem pamięci:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Aby *_ondisk* wersja poprzedniego skryptu T-SQL dla ostress.exe, należy zamienić oba wystąpienia *_inmem* podciągu na *_ondisk*. Te zamienniki mają wpływ na nazwy tabel i procedur składowanych.

#### <a name="install-rml-utilities-and-ostress"></a>Zainstaluj narzędzia RML i `ostress`

Najlepiej zaplanować uruchamianie ostress.exe na maszynie wirtualnej platformy Azure. Należy utworzyć [maszynę wirtualną platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) w tym samym regionie geograficznym platformy Azure, w którym znajduje się baza danych AdventureWorksLT. Można jednak uruchomić ostress.exe na laptopie.

Na maszynie wirtualnej lub na dowolnym z wybranych hostów Zainstaluj narzędzia odtwarzania RML. Narzędzia te obejmują ostress.exe.

Aby uzyskać więcej informacji, zobacz:

- Dyskusja ostress.exe w [przykładowej bazie danych dla In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Przykładowa baza danych dla In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Blog służący do instalowania ostress.exe](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Najpierw uruchom obciążenie obciążeniowe *_inmem*

Możesz użyć okna *wiersza polecenia RML cmd* , aby uruchomić nasz ostress.exe wiersz poleceń. Parametry wiersza polecenia bezpośrednio `ostress` do:

- Uruchom połączenia 100 współbieżnie (-N100).
- Każde połączenie uruchamia skrypt T-SQL 50 razy (-R50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

Aby uruchomić poprzednią ostress.exe wiersz polecenia:

1. Zresetuj zawartość danych bazy danych, uruchamiając następujące polecenie w programie SSMS, aby usunąć wszystkie dane, które zostały wstawione przez wszystkie poprzednie uruchomienia:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Skopiuj tekst poprzedniego ostress.exe wiersza polecenia do Schowka.

3. Zamień `<placeholders>` na parametry-S-u-P-d z prawidłowymi wartościami rzeczywistymi.

4. Uruchom edytowany wiersz polecenia w oknie RML cmd.

#### <a name="result-is-a-duration"></a>Wynik to czas trwania

Po `ostress.exe` zakończeniu zapisuje czas wykonywania jako ostatni wiersz danych wyjściowych w oknie RML cmd. Na przykład krótszy przebieg testu został zaostatni o 1,5 minut:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Zresetuj, Edytuj pod kątem *_ondisk*, a następnie uruchom ponownie

Po uruchomieniu *_inmem* wykonaj następujące czynności w celu uruchomienia *_ondisk* :

1. Zresetuj bazę danych, uruchamiając następujące polecenie w programie SSMS, aby usunąć wszystkie dane, które zostały wstawione przez poprzednie uruchomienie:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edytuj wiersz polecenia ostress.exe, aby zastąpić wszystkie *_inmem* z *_ondisk*.

3. Ponownie uruchom ostress.exe po raz drugi i Przechwyć wynik trwania.

4. Ponownie Zresetuj bazę danych (w celu uzyskania wystarczającej ilości danych testowych).

#### <a name="expected-comparison-results"></a>Oczekiwane wyniki porównania

Nasze testy In-Memory pokazują, że wydajność ulepszona przez **dziewięć razy** w przypadku tego obciążenia uproszczony z `ostress` URUCHOMIONYM na maszynie wirtualnej platformy Azure w tym samym regionie świadczenia usługi Azure, w której znajduje się baza danych.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Zainstaluj przykład In-Memory Analytics

W tej sekcji porównano wyniki operacji we/wy i statystyki w przypadku używania indeksu magazynu kolumn w porównaniu z tradycyjnym indeksem drzewa b.

W przypadku analiz w czasie rzeczywistym w obciążeniu OLTP często najlepszym rozwiązaniem jest użycie nieklastrowanego indeksu magazynu kolumn. Aby uzyskać szczegółowe informacje, zobacz [Opis indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview).

### <a name="prepare-the-columnstore-analytics-test"></a>Przygotuj test analizy magazynu kolumn

1. Użyj Azure Portal, aby utworzyć nową bazę danych AdventureWorksLT z przykładu.
   - Użyj tej dokładnej nazwy.
   - Wybierz dowolną warstwę usługi Premium.

2. Skopiuj [memory_analytics_sample sql_in](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) do Schowka.
   - Skrypt T-SQL tworzy niezbędne In-Memory obiektów w przykładowej bazie danych AdventureWorksLT utworzonej w kroku 1.
   - Skrypt tworzy tabelę wymiarów i dwie tabele faktów. Tabele faktów są wypełniane 3 500 000 wierszami.
   - Wykonanie skryptu może zająć 15 minut.

3. Wklej skrypt T-SQL do programu SSMS, a następnie wykonaj skrypt. Słowo kluczowe **magazynu kolumn** w instrukcji **create index** ma kluczowe znaczenie w:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ustaw AdventureWorksLT na poziom zgodności 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Poziom 130 nie jest bezpośrednio związany z funkcjami In-Memory. Jednak poziom 130 zapewnia szybsze wykonywanie zapytań niż 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Tabele kluczy i indeksy magazynu kolumn

- dbo. FactResellerSalesXL_CCI to tabela mająca klastrowany indeks magazynu kolumn, która ma zaawansowaną kompresję na poziomie *danych* .

- dbo. FactResellerSalesXL_PageCompressed to tabela, która ma odpowiednik regularnego indeksu klastrowanego, który jest kompresowany tylko na poziomie *strony* .

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Kluczowe zapytania do porównywania indeksu magazynu kolumn

Istnieje [kilka typów zapytań T-SQL, które można uruchomić,](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) aby zobaczyć ulepszenia wydajności. W kroku 2 w skrypcie T-SQL należy zwrócić uwagę na tę parę zapytań. Różnią się one tylko jednym wierszem:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Klastrowany indeks magazynu kolumn znajduje się w \_ tabeli FactResellerSalesXL WIK.

Poniższy fragment skryptu T-SQL drukuje statystyki dla operacji we/wy oraz czas dla kwerendy każdej tabeli.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

W bazie danych z warstwą cenową P2 można oczekiwać około dziewięciu razy większej wydajności dla tego zapytania przy użyciu klastrowanego indeksu magazynu kolumn w porównaniu z tradycyjnym indeksem. Za pomocą P15 można oczekiwać około 57 razy większej wydajności przy użyciu indeksu magazynu kolumn.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start 1: In-Memory technologii OLTP w celu uzyskania szybszej wydajności T-SQL](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [Używanie In-Memory OLTP w istniejącej aplikacji Azure SQL](in-memory-oltp-configure.md)

- [Monitoruj In-Memory magazyn OLTP](in-memory-oltp-monitor-space.md) dla In-Memory OLTP

## <a name="additional-resources"></a>Dodatkowe zasoby

### <a name="deeper-information"></a>Dokładniejsze informacje

- [Dowiedz się, jak kworum podwaja obciążenie bazy danych przy jednoczesnym obniżeniu liczby jednostek DTU o 70% dzięki In-Memory OLTP w Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Przetwarzanie OLTP w pamięci w Azure SQL Database wpis w blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Dowiedz się więcej o In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Informacje o indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Informacje o analizie operacyjnej w czasie rzeczywistym](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Zapoznaj się [z typowymi wzorcami obciążeń i zagadnieniami](/previous-versions/dn673538(v=msdn.10)) dotyczącymi migracji (które opisują wzorce obciążeń, w których In-Memory OLTP często zapewnia znaczny wzrost wydajności)

#### <a name="application-design"></a>Projekt aplikacji

- [Przetwarzanie OLTP w pamięci (Optymalizacja w pamięci)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Używanie In-Memory OLTP w istniejącej aplikacji Azure SQL](in-memory-oltp-configure.md)

#### <a name="tools"></a>Narzędzia

- [Witryna Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)
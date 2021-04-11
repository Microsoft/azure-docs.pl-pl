---
title: Konfigurowanie maksymalnego stopnia równoległości (MAXDOP)
titleSuffix: Azure SQL Database
description: Dowiedz się więcej na temat maksymalnego stopnia równoległości (MAXDOP).
ms.date: 03/29/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111814"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Skonfiguruj maksymalny stopień równoległości (MAXDOP) w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  W tym artykule opisano **Maksymalny stopień równoległości (MAXDOP)** w Azure SQL Database oraz sposób jego konfiguracji. 

> [!NOTE]
> **Ta zawartość koncentruje się na Azure SQL Database.** Azure SQL Database jest oparta na najnowszej stabilnej wersji aparatu bazy danych Microsoft SQL Server Database, dlatego większość zawartości jest podobna, chociaż opcje rozwiązywania problemów i konfiguracji różnią się. Aby uzyskać więcej informacji na temat MAXDOP SQL Server, zobacz [Konfigurowanie maksymalnego stopnia konfiguracji serwera równoległego](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Omówienie
  W Azure SQL Database domyślne ustawienie MAXDOP dla każdej nowej pojedynczej bazy danych i bazy danych elastycznej puli to 8. Oznacza to, że aparat bazy danych może wykonywać zapytania przy użyciu wielu wątków. W przeciwieństwie do SQL Server, w którym domyślny serwer MAXDOP ma wartość 0 (nieograniczony), domyślnie nowe bazy danych w Azure SQL Database są ustawione na MAXDOP 8. Ta wartość domyślna uniemożliwia niepotrzebne wykorzystanie zasobów i zapewnia spójne środowisko klienta. Nie jest to zwykle konieczne do dalszej konfiguracji MAXDOP w obciążeniach Azure SQL Database, ale może to stanowić zaawansowane ćwiczenie dostrajania wydajności.

> [!Note]
>   We wrześniu 2020, na podstawie lat telemetrii w Azure SQL Database Service [MAXDOP 8 została wybrana](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) jako wartość domyślna dla nowych baz danych jako optymalna, dla szerokiej gamy obciążeń klientów. Ta wartość domyślna pomaga zapobiec problemom z wydajnością z powodu nadmiernej równoległości. W systemach wcześniejszych niż to ustawienie domyślne dla nowych baz danych zostało MAXDOP 0. Opcja konfiguracji z zakresem bazy danych MAXDOP nie została zmieniona dla istniejących baz danych utworzonych przed 2020 września.

  Ogólnie rzecz biorąc, jeśli aparat bazy danych wybierze wykonywanie zapytania przy użyciu równoległości, czas wykonywania jest szybszy. Jednak nadmierna równoległość może zużywać nadmierne zasoby procesora bez zwiększania wydajności zapytań. Na dużą skalę nadmierna równoległość może mieć negatywny wpływ na wydajność zapytań dla wszystkich zapytań wykonywanych w ramach tego samego wystąpienia aparatu bazy danych, więc ustawienie górnego obramowania dla równoległości jest typowym ćwiczeniem dostrajania wydajności w SQL Server obciążeniach.

  W poniższej tabeli opisano zachowanie aparatu bazy danych podczas wykonywania zapytań z różnymi wartościami MAXDOP:

| MAXDOP | Zachowanie | 
|--|--|
| = 1 | Aparat bazy danych nie wykonuje zapytań przy użyciu wielu współbieżnych wątków. | 
| > 1 | Aparat bazy danych Ustawia górną granicę dla liczby wątków równoległych. Aparat bazy danych wybiera liczbę dodatkowych wątków roboczych, które mają być używane. Całkowita liczba wątków roboczych użytych do wykonania zapytania może być większa niż określona wartość MAXDOP. |
| = 0 | Aparat bazy danych może używać wielu równoległych wątków z górną granicą zależną od łącznej liczby procesorów logicznych. Aparat bazy danych wybiera liczbę wątków równoległych, które mają być używane.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Zagadnienia dotyczące  

-   W Azure SQL Database można zmienić domyślną wartość MAXDOP:
    -   Na poziomie zapytania, korzystając z [wskazówki zapytania](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP** .     
    -   Na poziomie bazy danych przy użyciu [konfiguracji z zakresem bazy danych](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) **MAXDOP** Database.

-   Długoterminowe SQL Server zagadnienia dotyczące MAXDOP i [zalecenia](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) dotyczące Azure SQL Database. 

-   MAXDOP jest wymuszane na [zadanie](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Nie jest wymuszane na [żądanie](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lub na zapytanie. Oznacza to, że podczas równoległego wykonywania zapytań pojedyncze żądanie może zduplikować wiele zadań z górną granicą określoną przez MAXDOP. Aby uzyskać więcej informacji, zobacz sekcję *Planowanie zadań równoległych* w [przewodniku po architekturze wątku i zadania](/sql/relational-databases/thread-and-task-architecture-guide). 
  
-   Operacje indeksowania, które tworzą lub kompilują indeks lub usuwają indeks klastrowany, mogą być czasochłonne. Można zastąpić maksymalny stopień równoległości dla operacji indeksowania przez określenie opcji indeksu MAXDOP w `CREATE INDEX` `ALTER INDEX` instrukcji or. Wartość MAXDOP jest stosowana do instrukcji w czasie wykonywania i nie jest przechowywana w metadanych indeksu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie równoległych operacji indeksów](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Oprócz zapytań i operacji indeksowania opcja konfiguracji z zakresem bazy danych dla MAXDOP kontroluje również równoległość instrukcji DBCC CHECKTABLE, DBCC CHECKDB i DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Mając  

  Zmiana MAXDOP bazy danych może mieć istotny wpływ na wydajność zapytań i wykorzystanie zasobów, zarówno dodatnie, jak i ujemne. Nie istnieje jednak jedna wartość MAXDOP, która jest optymalna dla wszystkich obciążeń. Zalecenia dotyczące ustawiania MAXDOP są złożonych i zależą od wielu czynników. 

  Niektóre obciążenia współbieżne mogą działać lepiej przy użyciu innego MAXDOP niż inne. Prawidłowo skonfigurowany MAXDOP powinien zmniejszyć ryzyko związane z wydajnością i dostępnością, a w niektórych przypadkach obniża koszty, dzięki czemu można uniknąć niepotrzebnych zasobów, a tym samym skalować w dół do niższego celu usługi.

### <a name="excessive-parallelism"></a>Nadmierna równoległość

  Wyższa MAXDOP często skraca czas trwania zapytań intensywnie korzystających z procesora CPU. Jednak nadmierna równoległość może pogorszyć inne współbieżne działanie obciążenia, blokują inne zapytania o zasoby procesora CPU i wątków roboczych. W skrajnych przypadkach nadmierna równoległość może zużywać wszystkie zasoby bazy danych lub puli elastycznej, co powoduje przekroczenie limitu czasu zapytania, błędów i awarii aplikacji. 

  Firma Microsoft zaleca, aby klienci nie mogli uniknąć MAXDOP 0 nawet wtedy, gdy nie wydaje się, że obecnie powodują problemy. Nadmierna równoległość stanie się najbardziej problematyczna, gdy wątki procesora i procesu roboczego otrzymują więcej współbieżnych żądań, niż może być obsługiwane przez cel usługi. Unikaj MAXDOP 0, aby zmniejszyć ryzyko potencjalnych problemów w przyszłości spowodowanych nadmierną równoległością, jeśli baza danych jest skalowana, lub jeśli przyszłe generacje sprzętu w Azure SQL Database zapewniają więcej rdzeni dla tego samego celu usługi bazy danych.

### <a name="modifying-maxdop"></a>Modyfikowanie MAXDOP 

  Jeśli określisz, że inne ustawienie MAXDOP jest optymalne dla obciążenia Azure SQL Database, możesz użyć `ALTER DATABASE SCOPED CONFIGURATION` instrukcji języka T-SQL. Przykłady można znaleźć w przykładach [przy użyciu języka Transact-SQL](#examples) . Dodaj ten krok do procesu wdrażania, aby zmienić MAXDOP po utworzeniu bazy danych.

  Jeśli MAXDOP inne niż domyślne tylko podzbiór zapytań w obciążeniu, można przesłonić MAXDOP na poziomie zapytania przez dodanie wskazówki OPTION (MAXDOP). Przykłady można znaleźć w przykładach [przy użyciu języka Transact-SQL](#examples) . 

  Dokładnie Przetestuj zmiany konfiguracji MAXDOP z testowaniem obciążenia, w tym realistycznie współbieżnych obciążeń zapytań. 

  MAXDOP dla replik podstawowych i pomocniczych można skonfigurować niezależnie, aby korzystać z różnych optymalnych ustawień MAXDOP dla obciążeń do odczytu i zapisu. Dotyczy to Azure SQL Database [odczytywania skalowalnego](read-scale-out.md)w poziomie, [replikacji geograficznej](active-geo-replication-overview.md)i [Azure SQL Database replik pomocniczych w ramach skalowania](service-tier-hyperscale.md). Domyślnie wszystkie repliki pomocnicze dziedziczą konfigurację MAXDOP repliki podstawowej.

## <a name="security"></a><a name="Security"></a> Bezpieczeństw  
  
###  <a name="permissions"></a><a name="Permissions"></a> Uprawnienia  
  `ALTER DATABASE SCOPED CONFIGURATION`Instrukcja musi być wykonywana jako administrator serwera jako członek roli bazy danych `db_owner` lub użytkownik, który udzielił `ALTER ANY DATABASE SCOPED CONFIGURATION` uprawnienia.
 
## <a name="examples"></a>Przykłady   

  Te przykłady używają najnowszej przykładowej bazy danych **AdventureWorksLT** w przypadku `SAMPLE` wybrania opcji dla nowej pojedynczej bazy danych Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Konfiguracja zakresu bazy danych MAXDOP   

  Ten przykład pokazuje, jak używać instrukcji [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) w celu skonfigurowania `max degree of parallelism` opcji `2` . To ustawienie jest stosowane od razu. Polecenie cmdlet programu PowerShell [Invoke-sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) wykonuje zapytania T-SQL, aby ustawić i zwrócić konfigurację zakresu bazy danych MAXDOP Database. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Ten przykład jest przeznaczony do użycia z bazami danych Azure SQL Database z włączonymi replikami [skalowalnymi](read-scale-out.md)w poziomie, [replikacją geograficzną](active-geo-replication-overview.md)i [Azure SQL Databaseami pomocniczymi w ramach skalowania](service-tier-hyperscale.md)w poziomie. Przykładowo replika podstawowa jest ustawiona na inną domyślną MAXDOP jako replikę pomocniczą, co wskazuje na to, że mogą wystąpić różnice między obsługą odczytu i zapisu a obciążeniem tylko do odczytu.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  Aby wykonać zapytania T-SQL dotyczące Azure SQL Database, można użyć [edytora zapytań Azure Portal](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)lub [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) .

1.  Połącz się z Azure SQL Database. Nie można zmienić konfiguracji bazy danych w bazie danych Master.
  
2.  Na pasku standardowym wybierz pozycję **nowe zapytanie**.   
  
3.  Skopiuj i wklej poniższy przykład do okna zapytania i wybierz polecenie **Execute (wykonaj**). 


#### <a name="maxdop-database-scoped-configuration"></a>Konfiguracja zakresu bazy danych MAXDOP

  W tym przykładzie pokazano, jak określić bieżącą konfigurację bazy danych MAXDOP baza danych przy użyciu widoku wykazu systemu [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) .

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Ten przykład pokazuje, jak używać instrukcji [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) w celu skonfigurowania `max degree of parallelism` opcji `8` . To ustawienie jest stosowane od razu.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Ten przykład jest przeznaczony do użycia z bazami danych Azure SQL Database z włączonymi replikami [skalowalnymi](read-scale-out.md)w poziomie, [replikacją geograficzną](active-geo-replication-overview.md)i [Azure SQL Databaseami pomocniczymi w ramach skalowania](service-tier-hyperscale.md)w poziomie. Przykładowo replika podstawowa jest ustawiona na inną domyślną MAXDOP jako replikę pomocniczą, co wskazuje na to, że mogą wystąpić różnice między obsługą odczytu i zapisu a obciążeniem tylko do odczytu. `value_for_secondary`Kolumna `sys.database_scoped_configurations` zawierająca ustawienia dla repliki pomocniczej.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP — Wskazówka zapytania

  Ten przykład pokazuje, jak wykonać zapytanie za pomocą wskazówki zapytania, aby wymusić `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP — opcja indeksu

  Ten przykład pokazuje, jak ponownie skompilować indeks przy użyciu opcji indeks, aby wymusić `max degree of parallelism` `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Zobacz też  
* [Konfiguracja ALTER DATABASE SCOPEd &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurowanie równoległych operacji indeksów](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Wskazówki dotyczące zapytań &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Ustawianie opcji indeksu](/sql/relational-databases/indexes/set-index-options)     
* [Zrozumienie i rozwiązywanie problemów z blokowaniem Azure SQL Database](understand-resolve-blocking.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i dostrajanie wydajności](/sql/relational-databases/performance/monitor-and-tune-for-performance)

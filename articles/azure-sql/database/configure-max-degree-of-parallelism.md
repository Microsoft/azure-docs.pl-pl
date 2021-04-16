---
title: Konfigurowanie maksymalnego stopnia równoległości (MAXDOP)
titleSuffix: Azure SQL Database
description: Dowiedz się więcej o maksymalnym stopniu równoległości (MAXDOP).
ms.date: 04/12/2021
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
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536906"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurowanie maksymalnego stopnia równoległości (MAXDOP) w usłudze Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  W tym artykule opisano ustawienie konfiguracji maksymalnego **stopnia równoległości (MAXDOP)** w Azure SQL Database. 

> [!NOTE]
> **Ta zawartość koncentruje się na Azure SQL Database.** Azure SQL Database jest oparta na najnowszej stabilnej wersji aparatu bazy danych Microsoft SQL Server, więc większość zawartości jest podobna, chociaż rozwiązywanie problemów i opcje konfiguracji różnią się. Aby uzyskać więcej informacji na temat opcji MAXDOP SQL Server, zobacz Configure the max degree of parallelism Server Configuration Option (Konfigurowanie maksymalnego stopnia [równoległości opcji konfiguracji serwera).](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)

## <a name="overview"></a>Omówienie
  Funkcja MAXDOP steruje równoległością wewnątrz zapytań w a aparatze bazy danych. Wyższe wartości MAXDOP zwykle mają więcej równoległych wątków na zapytanie i szybsze wykonywanie zapytań. 

  W Azure SQL Database domyślne ustawienie MAXDOP dla każdej nowej pojedynczej bazy danych i bazy danych puli elastycznej wynosi 8. To ustawienie domyślne zapobiega niepotrzebnemu wykorzystaniu zasobów, jednocześnie umożliwiając aparatowi bazy danych szybsze wykonywanie zapytań przy użyciu równoległych wątków. Zazwyczaj nie jest konieczne dalsze konfigurowanie opcji MAXDOP w obciążeniach Azure SQL Database, chociaż może to zapewnić korzyści w zaawansowanym ćwiczeniu dostrajania wydajności.

> [!Note]
>   We wrześniu 2020 r., na podstawie danych telemetrycznych z lat w usłudze Azure SQL Database MAXDOP 8, została domyślną wartością dla nowych baz danych [,](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)jako optymalną wartość dla najszerszej gamy obciążeń klientów. Ta wartość domyślna pomogła zapobiegać problemom z wydajnością z powodu nadmiernej równoległości. Wcześniej ustawieniem domyślnym dla nowych baz danych była wartość MAXDOP 0. Wartość MAXDOP nie została automatycznie zmieniona dla istniejących baz danych utworzonych przed wrześniem 2020 r.

  Ogólnie rzecz biorąc, jeśli aparat bazy danych zdecyduje się wykonać zapytanie przy użyciu równoległości, czas wykonywania jest krótszy. Jednak nadmiarowa równoległość może zużywać dodatkowe zasoby procesora bez poprawiania wydajności zapytań. Na dużą skalę nadmiarowa równoległość może negatywnie wpłynąć na wydajność zapytań dla wszystkich zapytań wykonywanych w tym samym wystąpieniu aparatu bazy danych. Tradycyjnie ustawianie górnej granicy dla równoległości było częstym ćwiczeniem dostrajania wydajności SQL Server obciążeniami.

  W poniższej tabeli opisano zachowanie aparatu bazy danych podczas wykonywania zapytań z różnymi wartościami MAXDOP:

| Maxdop | Zachowanie | 
|--|--|
| = 1 | Aparat bazy danych używa pojedynczego wątku szeregowego do wykonywania zapytań. Wątki równoległe nie są używane. | 
| > 1 | Aparat bazy danych ustawia [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) liczbę dodatkowych harmonogramów, które mają być używane przez wątki równoległe, na wartość MAXDOP lub łączną liczbę procesorów logicznych, w zależności od tego, która wartość jest mniejsza. |
| = 0 | Aparat bazy danych ustawia [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) liczbę dodatkowych harmonogramów, które mają być używane przez wątki równoległe, na łączną liczbę procesorów logicznych lub 64, w zależności od tego, która wartość jest mniejsza. | 
| | |

> [!Note]
> Każde zapytanie jest wykonywane przy użyciu co najmniej jednego harmonogramu i jednego wątku roboczego w tym harmonogramie.
>
> Zapytanie wykonywane z równoległością używa dodatkowych harmonogramów i dodatkowych wątków równoległych. Ponieważ wiele równoległych wątków może być wykonywanych w tym samym harmonogramie, łączna liczba wątków użytych do wykonania zapytania może być większa niż określona wartość MAXDOP lub łączna liczba procesorów logicznych. Aby uzyskać więcej informacji, zobacz [Planowanie zadań równoległych.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Zagadnienia dotyczące  

-   W Azure SQL Database można zmienić domyślną wartość MAXDOP:
    -   Na poziomie zapytania, używając wskazówki **zapytania MAXDOP** [.](/sql/t-sql/queries/hints-transact-sql-query)     
    -   Na poziomie bazy danych przy użyciu konfiguracji o zakresie bazy danych **MAXDOP** [.](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)

-   Długotrwałe zagadnienia SQL Server maxdop i zalecenia [mają](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) zastosowanie do Azure SQL Database. 

-   Operacje indeksowania, które tworzą lub ponownie tworzą indeks lub porzućą indeks klastrowany, mogą wymagać dużej ilości zasobów. Wartość MAXDOP bazy danych dla operacji indeksu można zastąpić, określając opcję indeksu MAXDOP w instrukcji `CREATE INDEX` `ALTER INDEX` lub . Wartość MAXDOP jest stosowana do instrukcji w czasie wykonywania i nie jest przechowywana w metadanych indeksu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie równoległych operacji indeksu](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Oprócz zapytań i operacji indeksowania opcja konfiguracji o zakresie bazy danych dla opcji MAXDOP kontroluje również równoległość innych instrukcji, które mogą używać wykonywania równoległego, takich jak DBCC CHECKTABLE, DBCC CHECKDB i DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Zalecenia  

  Zmiana wartości MAXDOP dla bazy danych może mieć duży wpływ na wydajność zapytań i wykorzystanie zasobów, zarówno dodatnie, jak i ujemne. Nie ma jednak pojedynczej wartości MAXDOP, która jest optymalna dla wszystkich obciążeń. Zalecenia [dotyczące](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) ustawiania opcji MAXDOP są nuancedne i zależą od wielu czynników. 

  Niektóre szczytowe obciążenia współbieżne mogą działać lepiej z inną wartością MAXDOP niż inne. Prawidłowo skonfigurowana wartość MAXDOP powinna zmniejszyć ryzyko wystąpienia zdarzeń związanych z wydajnością i dostępnością, a w niektórych przypadkach może obniżyć koszty dzięki możliwości uniknięcia niepotrzebnego wykorzystania zasobów, a tym samym skalowania w dół do niższego celu usługi.

### <a name="excessive-parallelism"></a>Nadmierne równoległość

  Większa wartość MAXDOP często skraca czas trwania zapytań intensywnie obciążanych procesorem CPU. Jednak nadmierna równoległość może pogorszyć wydajność innych współbieżnych obciążeń przez blokowania innych zapytań dotyczących zasobów procesora CPU i wątku roboczego. W skrajnych przypadkach nadmierna równoległość może zużywać wszystkie zasoby bazy danych lub elastycznej puli, powodując przekleństwa zapytań, błędy i błędy aplikacji. 

> [!Tip]
> Zalecamy, aby klienci unikali ustawiania wartości MAXDOP na 0, nawet jeśli wydaje się, że obecnie nie powoduje to problemów.

  Nadmierna równoległość staje się najbardziej problematyczna, gdy istnieje więcej równoczesnych żądań niż jest obsługiwanych przez zasoby procesora CPU i wątku roboczego zapewniane przez cel usługi. Unikaj wartości MAXDOP 0, aby zmniejszyć ryzyko potencjalnych przyszłych problemów z powodu nadmiernej równoległości, jeśli baza danych jest skalowana w górę lub jeśli przyszłe generacje sprzętu w programie Azure SQL Database zapewniają więcej rdzeni dla tego samego celu usługi bazy danych.

### <a name="modifying-maxdop"></a>Modyfikowanie maxdop 

  Jeśli ustalisz, że ustawienie MAXDOP inne niż domyślne jest optymalne dla obciążenia Azure SQL Database, możesz użyć instrukcji `ALTER DATABASE SCOPED CONFIGURATION` języka T-SQL. Przykłady można znaleźć w sekcji [Przykłady korzystające z języka Transact-SQL](#examples) poniżej. Aby zmienić ustawienie MAXDOP na wartość niedomyślną dla każdej nowo tworzyć bazy danych, dodaj ten krok do procesu wdrażania bazy danych.

  Jeśli wartość MAXDOP innych niż domyślna przynosi korzyści tylko niewielkiej części zapytań w obciążeniu, można zastąpić ustawienie MAXDOP na poziomie zapytania, dodając wskazówkę OPTION (MAXDOP). Przykłady można znaleźć w sekcji [Przykłady korzystające z języka Transact-SQL](#examples) poniżej. 

  Dokładnie przetestuj zmiany konfiguracji MAXDOP za pomocą testów obciążeniowych obejmujących realistyczne równoczesne obciążenia zapytań. 

  Wartość MAXDOP dla replik podstawowych i pomocniczych można skonfigurować niezależnie, jeśli różne ustawienia MAXDOP są optymalne dla obciążeń tylko do odczytu i zapisu. Dotyczy to Azure SQL Database [skalowania odczytu,](read-scale-out.md) [replikacji](active-geo-replication-overview.md)geograficznej [i](service-tier-hyperscale.md) replik pomocniczych w hiperskali. Domyślnie wszystkie repliki pomocnicze dziedziczą konfigurację MAXDOP repliki podstawowej.

## <a name="security"></a><a name="Security"></a> Zabezpieczeń  
  
###  <a name="permissions"></a><a name="Permissions"></a> Uprawnienia  
  Instrukcja musi być wykonywana jako administrator serwera, jako członek roli bazy danych lub użytkownik, który `ALTER DATABASE SCOPED CONFIGURATION` `db_owner` ma przyznane `ALTER ANY DATABASE SCOPED CONFIGURATION` uprawnienie.
 
## <a name="examples"></a>Przykłady   

  W tych przykładach jest dostępna najnowsza przykładowa baza danych **AdventureWorksLT,** gdy zostanie wybrana opcja dla nowej pojedynczej bazy danych `SAMPLE` Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Konfiguracja o zakresie bazy danych MAXDOP   

  W tym przykładzie pokazano, jak [za pomocą instrukcji ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ustawić `MAXDOP` konfigurację na `2` . To ustawienie jest natychmiastowe w przypadku nowych zapytań. Polecenie cmdlet programu PowerShell [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) wykonuje zapytania T-SQL do ustawienia i zwraca konfigurację o zakresie bazy danych MAXDOP. 

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

Ten przykład jest do użytku z bazami Azure SQL z włączonymi replikami skalowania w celu [odczytu,](read-scale-out.md)replikacją geograficzną i Azure SQL Database [replikami pomocniczym w hiperskali.](service-tier-hyperscale.md) [](active-geo-replication-overview.md) Na przykład replika podstawowa jest ustawiana na inną domyślną wartość MAXDOP jako replikę pomocniczą, przewidując, że mogą wystąpić różnice między obciążeniem odczytu i zapisu a obciążeniem tylko do odczytu.

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
  
  Możesz użyć edytora [Azure Portal](connect-query-portal.md)zapytań , [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)lub [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) do wykonywania zapytań T-SQL względem Azure SQL Database.

1.  Otwórz nowe okno zapytania.

2.  Połącz się z bazą danych, w której chcesz zmienić wartość MAXDOP. W bazie danych master nie można zmieniać konfiguracji o zakresie bazy danych.
  
3.  Skopiuj i wklej poniższy przykład w oknie zapytania, a następnie wybierz pozycję **Wykonaj**. 

#### <a name="maxdop-database-scoped-configuration"></a>Konfiguracja o zakresie bazy danych MAXDOP

  W tym przykładzie pokazano, jak określić bieżącą konfigurację bazy danych MAXDOP o zakresie bazy danych przy [użyciu sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) wykazu systemu.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  W tym przykładzie pokazano, jak [za pomocą instrukcji ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ustawić `MAXDOP` konfigurację na `8` . To ustawienie jest stosowane od razu.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Ten przykład jest do użytku z bazami Azure SQL z włączonymi replikami skalowania w celu [odczytu,](read-scale-out.md)replikacją geograficzną i [replikami](service-tier-hyperscale.md) pomocniczym w hiperskali. [](active-geo-replication-overview.md) Na przykład replika podstawowa jest ustawiona na inną wartość MAXDOP niż replika pomocnicza, przewidując, że mogą wystąpić różnice między obciążeniami tylko do odczytu i zapisu. Wszystkie instrukcje są wykonywane w replice podstawowej. Kolumna `value_for_secondary` zawiera `sys.database_scoped_configurations` ustawienia repliki pomocniczej.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>Wskazówka zapytania MAXDOP

  W tym przykładzie pokazano, jak wykonać zapytanie przy użyciu wskazówki zapytania, aby wymusić `max degree of parallelism` na `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>Opcja indeksu MAXDOP

  W tym przykładzie pokazano, jak ponownie skompilować indeks przy użyciu opcji indeksu, aby wymusić `max degree of parallelism` na . `12`  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Zobacz też  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurowanie równoległych operacji indeksu](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Wskazówki dotyczące zapytań &#40;języka Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Ustawianie opcji indeksu](/sql/relational-databases/indexes/set-index-options)     
* [Zrozumienie i rozwiązywanie Azure SQL Database problemów z blokowaniem](understand-resolve-blocking.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i dostrajanie pod celu wydajności](/sql/relational-databases/performance/monitor-and-tune-for-performance)

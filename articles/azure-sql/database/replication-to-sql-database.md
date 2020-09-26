---
title: Replikacja SQL Server platformy Azure do Azure SQL Database
description: Bazę danych można skonfigurować w Azure SQL Database jako subskrybent wypychany w jednokierunkowej topologii replikacji transakcyjnej lub migawki.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 079d187f66cf77585121198df06cabafc454fea1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362133"
---
# <a name="replication-to-azure-sql-database"></a>Replikacja do Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database jako subskrybenta wypychania można skonfigurować w jednokierunkowej topologii replikacji.

> [!NOTE]
> W tym artykule opisano sposób korzystania z [replikacji transakcyjnej](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) w Azure SQL Database. Nie jest ona powiązana z [aktywną replikacją geograficzną](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication), a funkcja Azure SQL Database, która umożliwia tworzenie kompletnych możliwych do odczytu replik poszczególnych baz danych.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje
  
- Azure SQL Database może być tylko subskrybentem wypychania SQL Server wydawcy i dystrybutora.  
- Wystąpienie SQL Server działające jako Wydawca i/lub dystrybutor może być wystąpieniem [SQL Server działającego lokalnie](https://www.microsoft.com/sql-server/sql-server-downloads), [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/instance-create-quickstart.md)lub wystąpienia [SQL Server uruchomionego na maszynie wirtualnej platformy Azure w chmurze](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- Bazy danych dystrybucji i agentów replikacji nie można umieścić w bazie danych w Azure SQL Database.  
- [Migawki](/sql/relational-databases/replication/snapshot-replication) i [jednokierunkowa replikacja transakcyjna](/sql/relational-databases/replication/transactional/transactional-replication) są obsługiwane. Replikacja transakcyjna równorzędna i replikacja scalająca nie są obsługiwane.

### <a name="versions"></a>Wersje  

Aby pomyślnie przeprowadzić replikację do bazy danych w Azure SQL Database, SQL Server wydawcy i dystrybutorzy muszą korzystać z jednej z następujących wersji:

Publikowanie w dowolnych Azure SQL Database z bazy danych SQL Server jest obsługiwane przez następujące wersje SQL Server:

- SQL Server 2016 i nowsze
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) lub [z dodatkiem SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) lub [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Próba skonfigurowania replikacji za pomocą nieobsługiwanej wersji może spowodować wystąpienie błędu MSSQL_REPL20084 (proces nie mógł nawiązać połączenia z subskrybentem) i MSSQL_REPL40532 (nie można otworzyć serwera \<name> żądanego przez login. Logowanie nie powiodło się.  

Aby korzystać ze wszystkich funkcji Azure SQL Database, musisz używać najnowszych wersji narzędzi [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Typy replikacji

Istnieją różne [typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):

| Replikacja | Azure SQL Database | Wystąpienie zarządzane Azure SQL |
| :----| :------------- | :--------------- |
| [**Standardowa transakcyjna**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybent) | Tak | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybent) | Tak|
| [**Replikacja scalająca**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Równorzędny**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| [**Kierunk**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Tak|
| [**Aktualizowalne subskrypcje**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Uwagi

- Obsługiwane są tylko subskrypcje wypychane do Azure SQL Database.  
- Replikację można skonfigurować za pomocą [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) lub przez wykonanie instrukcji języka Transact-SQL na wydawcy. Nie można skonfigurować replikacji przy użyciu Azure Portal.  
- Replikacja może być SQL Server używana tylko w celu nawiązania połączenia z Azure SQL Database.
- Zreplikowane tabele muszą mieć klucz podstawowy.  
- Musisz mieć istniejącą subskrypcję platformy Azure.  
- Subskrybent Azure SQL Database może znajdować się w dowolnym regionie.  
- Jedna publikacja na SQL Server może obsługiwać zarówno Azure SQL Database, jak i SQL Server (lokalnie i SQL Server w ramach maszyn wirtualnych platformy Azure).  
- Zarządzanie replikacją, monitorowanie i rozwiązywanie problemów należy wykonać z SQL Server, a nie Azure SQL Database.  
- `@subscriber_type = 0`Obsługiwane tylko w **sp_addsubscription** SQL Database.  
- Azure SQL Database nie obsługuje replikacji dwukierunkowej, natychmiastowej, aktualizowalnej lub równorzędnej.

## <a name="replication-architecture"></a>Architektura replikacji  

![Na diagramie przedstawiono architekturę replikacji z Azure SQL Database, która zawiera kilka klastrów subskrybentów w różnych regionach i lokalnych maszyn wirtualnych platformy Azure, które zawierają pliki wykonywalne wydawcy, LogRead i, które łączą się z klastrami zdalnymi.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenariusze  

### <a name="typical-replication-scenario"></a>Typowy scenariusz replikacji  

1. Utwórz publikację z replikacją transakcyjną w bazie danych SQL Server.  
2. Na SQL Server Użyj **Kreatora nowej subskrypcji** lub instrukcji języka Transact-SQL, aby utworzyć wypychanie do subskrypcji Azure SQL Database.  
3. W przypadku baz danych o pojedynczej i puli w Azure SQL Database początkowy zestaw danych jest migawką utworzoną przez agenta migawek i dystrybuowaną i stosowaną przez agenta dystrybucji. Za pomocą wydawcy wystąpienia zarządzanego SQL można także użyć kopii zapasowej bazy danych do wypełniania subskrybenta Azure SQL Database.

### <a name="data-migration-scenario"></a>Scenariusz migracji danych  

1. Replikowanie danych z bazy danych SQL Server do Azure SQL Database za pomocą replikacji transakcyjnej.  
2. Przekieruj klienta lub aplikacje warstwy środkowej, aby zaktualizować kopię bazy danych.  
3. Zatrzymaj aktualizację wersji SQL Server tabeli i Usuń publikację.  

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane w przypadku subskrypcji Azure SQL Database:

- Kopiuj skojarzenie grup plików  
- Kopiuj schematy partycjonowania tabel  
- Kopiuj schematy partycjonowania indeksów  
- Kopiowanie statystyk zdefiniowanych przez użytkownika  
- Kopiuj domyślne powiązania  
- Kopiuj powiązania reguły  
- Kopiuj indeksy pełnotekstowy  
- Kopiuj plik XSD XML  
- Kopiuj indeksy XML  
- Uprawnienia do kopiowania  
- Kopiuj indeksy przestrzenne  
- Kopiuj filtrowane indeksy  
- Kopiuj atrybut kompresji danych  
- Kopiuj atrybut kolumny rozrzedzonej  
- Konwertuj dane FILESTREAM na maksymalne typy danych  
- Konwertuj hierarchyid na maksymalną liczbę typów danych  
- Konwertuj dane przestrzenne na wartości typu MAX  
- Kopiuj właściwości rozszerzone  

### <a name="limitations-to-be-determined"></a>Ograniczenia do określenia

- Kopiuj sortowanie  
- Wykonywanie w serializowanej transakcji programu SP  

## <a name="examples"></a>Przykłady

Utwórz publikację i subskrypcję wypychaną. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) , używając nazwy serwera jako subskrybenta (na przykład **N'azuresqldbdns. Database. Windows. NET**) i nazwy Azure SQL Database jako docelowej bazy danych (na przykład **AdventureWorks**).  

## <a name="see-also"></a>Zobacz też  

- [Replikacja transakcyjna](../managed-instance/replication-transactional-overview.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  

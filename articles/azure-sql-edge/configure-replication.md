---
title: Konfigurowanie replikacji do usługi Azure SQL Edge (wersja zapoznawcza)
description: Informacje o konfigurowaniu replikacji do usługi Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6249d72ba43bf59a2862595f40adf2d1ac5a6346
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235162"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Konfigurowanie replikacji do usługi Azure SQL Edge (wersja zapoznawcza) 

Wystąpienie usługi Azure SQL Edge można skonfigurować jako subskrybenta wypychanego dla jednokierunkowej replikacji transakcyjnej lub replikacji migawek. Wystąpienie usługi Azure SQL Edge nie może działać jako Wydawca lub dystrybutor dla konfiguracji replikacji transakcyjnej. Replikacja scalająca, replikacja P2P i publikowanie w programie Oracle nie są obsługiwane w usłudze Azure SQL Edge.

## <a name="supported-configurations"></a>**Obsługiwane konfiguracje**:
  
- Wystąpienie usługi Azure SQL Edge musi być subskrybentem wypychanym dla wydawcy.
- Wydawca i dystrybutor może być albo
   - Wystąpienie SQL Server uruchomione lokalnie lub w wystąpieniu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [SQL Server na platformie Azure — omówienie Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). Wystąpienia SQL Server muszą używać wersji nowszej niż SQL Server 2016.
   - Wystąpienie wystąpienia zarządzanego usługi Azure SQL. Wystąpienie zarządzane może hostować bazy danych wydawcy, dystrybutora i subskrybentów. Aby uzyskać więcej informacji, zobacz [replikacja z wystąpieniem zarządzanym SQL Database](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Nie można umieścić bazy danych dystrybucji i agentów replikacji w wystąpieniu usługi Azure SQL Edge.  

> [!NOTE]
> Próba skonfigurowania replikacji za pomocą nieobsługiwanej wersji może spowodować wystąpienie błędu MSSQL_REPL20084 (proces nie mógł nawiązać połączenia z subskrybentem) i MSSQL_REPL40532 (nie można otworzyć serwera \<name> żądanego przez login. Logowanie nie powiodło się.  

Aby korzystać ze wszystkich funkcji usługi Azure SQL Edge, musisz używać najnowszych wersji narzędzi [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Uwagi

- Replikację można skonfigurować za pomocą [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub przez wykonanie instrukcji języka Transact-SQL na wydawcy przy użyciu SQL Server Management Studio lub [usługi Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
- Replikacja może być SQL Server używana tylko w celu nawiązania połączenia z wystąpieniem usługi Azure SQL Edge.
- Zreplikowane tabele muszą mieć klucz podstawowy.
- Jedna publikacja na SQL Server może obsługiwać zarówno usługi Azure SQL Edge, jak i SQL Server (lokalne i SQL Server w ramach maszyn wirtualnych platformy Azure).  
- Zarządzanie replikacją, monitorowanie i rozwiązywanie problemów należy wykonać z wystąpienia SQL Server.  
- Obsługiwane są tylko subskrypcje wypychane do usługi Azure SQL Edge.  
- `@subscriber_type = 0`Obsługiwane tylko w **Sp_addsubscription** dla usługi Azure SQL Edge.  
- Usługa Azure SQL Edge nie obsługuje dwukierunkowej, natychmiastowej lub aktualizowalnej replikacji równorzędnej.
- Usługa Azure SQL Edge obsługuje tylko podzestaw funkcji dostępnych w SQL Server lub wystąpieniu zarządzanym SQL, ponieważ próba replikacji bazy danych (lub obiektów w bazie danych), która zawiera co najmniej jedną nieobsługiwaną funkcję, spowoduje wystąpienie błędu. Na przykład próba replikacji bazy danych zawierającej obiekty z danymi przestrzennymi spowoduje wystąpienie błędu. Aby uzyskać więcej informacji o funkcjach obsługiwanych przez usługę Azure SQL Edge, zobacz [obsługiwane funkcje usługi Azure SQL Edge](features.md).

## <a name="scenarios"></a>Scenariusze  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inicjowanie danych referencyjnych w wystąpieniu krawędzi

Typowy scenariusz, w którym replikacja może być przydatna, ma miejsce, gdy istnieje potrzeba zainicjowania wystąpienia brzegowego z danymi referencyjnymi, które zmieniają się w czasie. Na przykład podczas nauczenia się na wystąpieniu SQL Server należy zaktualizować modele ML w wystąpieniu krawędzi.

1. Utwórz publikację z replikacją transakcyjną w bazie danych SQL Server.  
2. Na wystąpieniu SQL Server Użyj **Kreatora nowej subskrypcji** lub instrukcji języka Transact-SQL, aby utworzyć wypychanie do subskrypcji usługi Azure SQL Edge.  
3. Replikowana baza danych w usłudze Azure SQL Edge może zostać zainicjowana za pomocą migawki wygenerowanej przez agenta migawek i dystrybuowanej i dostarczonej przez agenta dystrybucji albo przy użyciu kopii zapasowej bazy danych od wydawcy. Jeśli kopia zapasowa bazy danych zawiera obiekty/funkcje, które nie są obsługiwane przez usługę Azure SQL Edge, operacja przywracania zakończy się niepowodzeniem.

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane w przypadku subskrypcji usługi Azure SQL Edge:

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
- Kopiuj dane typu FILESTREAM, hierarchyid lub przestrzenne.
- Konwertuj hierarchyid na maksymalną liczbę typów danych  
- Konwertuj dane przestrzenne na wartości typu MAX  
- Kopiuj właściwości rozszerzone  
- Uprawnienia do kopiowania  

## <a name="examples"></a>Przykłady

Utwórz publikację i subskrypcję wypychaną. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) , używając nazwy/adresu IP serwera usługi Azure SQL Edge jako subskrybenta (na przykład **myEdgeinstance, 1433**) i nazwy bazy danych w wystąpieniu usługi Azure SQL Edge jako docelowej bazy danych (na przykład **AdventureWorks**).  

## <a name="see-also"></a>Zobacz także  

- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  



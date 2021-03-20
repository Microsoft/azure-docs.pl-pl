---
title: Replikacja transakcyjna
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się więcej o używaniu SQL Server replikacji transakcyjnej przy użyciu wystąpienia zarządzanego Azure SQL (wersja zapoznawcza).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225347"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Replikacja transakcyjna z wystąpieniem zarządzanym usługi Azure SQL (wersja zapoznawcza)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Replikacja transakcyjna to funkcja wystąpienia zarządzanego usługi Azure SQL i SQL Server, które umożliwiają replikację danych z tabeli w wystąpieniu zarządzanym usługi Azure SQL lub wystąpienia SQL Server do tabel umieszczonych w zdalnych bazach danych. Ta funkcja pozwala synchronizować wiele tabel w różnych bazach danych. 

Replikacja transakcyjna jest obecnie dostępna w publicznej wersji zapoznawczej dla wystąpienia zarządzanego SQL. 

## <a name="overview"></a>Omówienie

Za pomocą replikacji transakcyjnej można wypchnąć zmiany wprowadzone w wystąpieniu zarządzanym Azure SQL:

- Baza danych SQL Server lokalna lub na maszynie wirtualnej platformy Azure
- Baza danych w Azure SQL Database
- Baza danych wystąpienia w wystąpieniu zarządzanym Azure SQL

  > [!NOTE]
  > Aby korzystać ze wszystkich funkcji wystąpienia zarządzanego Azure SQL, należy użyć najnowszych wersji programu [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Składniki

Kluczowymi składnikami replikacji transakcyjnej są **Wydawca**, **dystrybutor** i **subskrybent**, jak pokazano na poniższej ilustracji:  

![replikacja przy użyciu SQL Database](./media/replication-transactional-overview/replication-to-sql-database.png)

| Rola | Azure SQL Database | Wystąpienie zarządzane Azure SQL |
| :----| :------------- | :--------------- |
| **Wydawca** | Nie | Tak |
| **Dystrybutor** | Nie | Tak|
| **Subskrybent ściągania** | Nie | Tak|
| **Wypychanie subskrybenta**| Tak | Tak|
| &nbsp; | &nbsp; | &nbsp; |

**Wydawca** publikuje zmiany wprowadzone w niektórych tabelach (artykułach), wysyłając aktualizacje do dystrybutora. Wydawca może być wystąpieniem zarządzanym usługi Azure SQL lub wystąpieniem SQL Server.

**Dystrybutor** zbiera zmiany w artykułach od wydawcy i dystrybuuje je do subskrybentów. Dystrybutor może być wystąpieniem zarządzanym usługi Azure SQL lub wystąpieniem SQL Server (dowolna wersja jest równa lub większa niż wersja wydawcy).

**Subskrybent** otrzymuje zmiany dokonane na wydawcy. Wystąpienie SQL Server i wystąpienie zarządzane Azure SQL mogą być zarówno subskrybentami wypychania, jak i ściągania, chociaż subskrypcja ściągania nie jest obsługiwana, gdy dystrybutor jest wystąpieniem zarządzanym usługi Azure SQL, a subskrybent nie jest. Baza danych w Azure SQL Database może być subskrybentem wypychania.

Wystąpienie zarządzane Azure SQL może obsługiwać subskrybenta z następujących wersji SQL Server:

- SQL Server 2016 i nowsze
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) lub [z dodatkiem SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) lub [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - W przypadku innych wersji SQL Server, które nie obsługują publikowania w obiektach na platformie Azure, można użyć metody [ponownego publikowania danych](/sql/relational-databases/replication/republish-data) w celu przeniesienia danych do nowszych wersji SQL Server.
   > - Próba skonfigurowania replikacji przy użyciu starszej wersji może spowodować wystąpienie błędu MSSQL_REPL20084 (proces nie mógł nawiązać połączenia z subskrybentem) i MSSQ_REPL40532 (nie można otworzyć serwera \<name> żądanego przez nazwę logowania. Logowanie nie powiodło się.

### <a name="types-of-replication"></a>Typy replikacji

Istnieją różne [typy replikacji](/sql/relational-databases/replication/types-of-replication):

| Replikacja | Azure SQL Database | Wystąpienie zarządzane Azure SQL |
| :----| :------------- | :--------------- |
| [**Standardowa transakcyjna**](/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybent) | Tak |
| [**Zdjęcie**](/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybent) | Tak|
| [**Replikacja scalająca**](/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Równorzędny**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| [**Kierunk**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Tak|
| [**Aktualizowalne subskrypcje**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Macierz obsługi

  Macierz obsługi replikacji transakcyjnej dla wystąpienia zarządzanego Azure SQL jest taka sama jak w przypadku SQL Server.
  
| **Wydawca**   | **Dystrybutor** | **Subscriber** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Kiedy stosować

Replikacja transakcyjna jest przydatna w następujących scenariuszach:

- Publikuj zmiany wprowadzone w co najmniej jednej tabeli w bazie danych i Rozpowszechnij je w jednej lub wielu bazach danych w wystąpieniu SQL Server lub Azure SQL Database, które zasubskrybują zmiany.
- Utrzymuj synchronizację kilku rozproszonych baz danych.
- Migrowanie baz danych z jednego wystąpienia SQL Server lub wystąpienia zarządzanego usługi Azure SQL do innej bazy danych przez ciągłe publikowanie zmian.

### <a name="compare-data-sync-with-transactional-replication"></a>Porównywanie synchronizacji danych z replikacją transakcyjną

| Kategoria | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | -Aktywne-aktywne wsparcie<br/>-Dwukierunkowe między środowiskiem lokalnym i Azure SQL Database | -Małe opóźnienia<br/>-Spójności transakcyjnej<br/>-Ponowne użycie istniejącej topologii po migracji |
| Wady | -Brak spójności transakcyjnej<br/>— Wyższy wpływ na wydajność | -Nie można opublikować z Azure SQL Database <br/>— Wysoki koszt konserwacji |

## <a name="common-configurations"></a>Typowe konfiguracje

Ogólnie rzecz biorąc, Wydawca i dystrybutor musi znajdować się w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Wydawca z lokalnym dystrybutorem w wystąpieniu zarządzanym SQL

![Pojedyncze wystąpienie jako Wydawca i dystrybutora](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Wydawca i dystrybutor są konfigurowane w ramach jednego wystąpienia zarządzanego SQL i rozpowszechniać zmiany do innego wystąpienia zarządzanego SQL, SQL Database lub wystąpienia SQL Server.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Wydawca z zdalnym dystrybutorem w wystąpieniu zarządzanym SQL

W tej konfiguracji jedno wystąpienie zarządzane publikuje zmiany w dystrybutorze umieszczonym w innym wystąpieniu zarządzanym SQL, które może obsłużyć wiele wystąpień zarządzanych przez program SQL i dystrybuować zmiany do jednego lub wielu obiektów docelowych w Azure SQL Database, wystąpieniu zarządzanym Azure SQL lub SQL Server.

![Oddziel wystąpienia wydawcy i dystrybutora](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Wydawca i dystrybutor są konfigurowane w dwóch zarządzanych wystąpieniach. Istnieją pewne ograniczenia dotyczące tej konfiguracji:

- Oba wystąpienia zarządzane znajdują się w tej samej sieci wirtualnej.
- Oba wystąpienia zarządzane znajdują się w tej samej lokalizacji.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Lokalny Wydawca/dystrybutor z subskrybentem zdalnym

![Azure SQL Database jako subskrybent](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

W tej konfiguracji baza danych w Azure SQL Database lub wystąpienie zarządzane Azure SQL jest subskrybentem. Ta konfiguracja obsługuje migrację z lokalizacji lokalnej na platformę Azure. Jeśli subskrybent jest bazą danych w Azure SQL Database, musi być w trybie push.  

## <a name="requirements"></a>Wymagania

- Użyj uwierzytelniania SQL na potrzeby łączności między uczestnikami replikacji.
- Użyj udziału konta usługi Azure Storage dla katalogu roboczego używanego przez replikację.
- Otwórz port wychodzący TCP 445 w regułach zabezpieczeń podsieci, aby uzyskać dostęp do udziału plików platformy Azure.
- Otwórz port wychodzący TCP 1433, gdy wystąpienie zarządzane SQL jest wydawcą/dystrybutorem, a subskrybentem nie jest. Może być również konieczne zmodyfikowanie reguły zabezpieczeń wychodzącej sieciowej grupy zabezpieczeń wystąpienia zarządzanego SQL dla programu `allow_linkedserver_outbound` dla **tagu usługi docelowej** portu 1433 z `virtualnetwork` na `internet` .
- Umieść wydawcę i dystrybutora w chmurze lub lokalnie.
- Skonfiguruj komunikację równorzędną sieci VPN między sieciami wirtualnymi uczestników replikacji, jeśli sieci wirtualne są różne.

> [!NOTE]
> W przypadku nawiązywania połączenia z plikiem usługi Azure Storage może wystąpić błąd 53, jeśli port wychodzącej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) 445 jest blokowany, gdy dystrybutor jest bazą danych wystąpienia zarządzanego Azure SQL i jest on lokalny. Aby rozwiązać ten problem [, zaktualizuj sieciowej grupy zabezpieczeń sieci wirtualnej](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) .

## <a name="with-failover-groups"></a>Z grupami trybu failover

[Aktywna replikacja geograficzna](../database/active-geo-replication-overview.md) nie jest obsługiwana w przypadku wystąpienia zarządzanego SQL korzystającego z replikacji transakcyjnej. Zamiast aktywnej replikacji geograficznej, należy użyć [grup z obsługą trybu failover](../database/auto-failover-group-overview.md), ale należy pamiętać, że publikacja należy [ręcznie usunąć](transact-sql-tsql-differences-sql-server.md#replication) z podstawowego wystąpienia zarządzanego i utworzyć je na pomocniczym wystąpieniu zarządzanym SQL po przejściu do trybu failover.

W przypadku włączenia replikacji geograficznej w wystąpieniu zarządzanym przez **wydawcę** lub **dystrybutora** w [grupie trybu failover](../database/auto-failover-group-overview.md)administrator wystąpienia zarządzanego SQL musi oczyścić wszystkie publikacje na starym serwerze podstawowym i skonfigurować je ponownie na nowym serwerze podstawowym po przejściu w tryb failover. W tym scenariuszu są niezbędne następujące działania:

1. Zatrzymaj wszystkie zadania replikacji uruchomione w bazie danych, jeśli istnieją.
1. Porzuć metadane subskrypcji z wydawcą, uruchamiając następujący skrypt w bazie danych wydawcy:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Usuwanie metadanych subskrypcji z subskrybenta. Uruchom następujący skrypt w bazie danych subskrypcji na wystąpieniu zarządzanym SQL subskrybenta:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Wymuszone porzucenie wszystkich obiektów replikacji z wydawcy przez uruchomienie następującego skryptu w opublikowanej bazie danych:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Wymuszone porzucanie starego dystrybutora z oryginalnego podstawowego wystąpienia zarządzanego SQL (w przypadku przełączenia w tryb failover do starego elementu podstawowego, który używał dystrybutora). Uruchom następujący skrypt w bazie danych Master w starym wystąpieniu zarządzanym SQL dystrybutora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

W przypadku włączenia replikacji geograficznej w wystąpieniu **subskrybenta** w grupie trybu failover publikacja powinna być skonfigurowana w taki sposób, aby nawiązać połączenie z punktem końcowym odbiornika grupy trybu failover dla wystąpienia zarządzanego przez subskrybenta. W przypadku przełączenia w tryb failover kolejne akcje podejmowane przez administratora wystąpienia zarządzanego są zależne od typu trybu failover, który wystąpił:

- W przypadku przejścia w tryb failover bez utraty danych replikacja będzie działać po przejściu do trybu failover.
- W przypadku pracy awaryjnej z utratą danych replikacja również będzie działała. Spowoduje to ponowną replikację utraconych zmian.
- W przypadku przejścia w tryb failover z utratą danych, ale utrata danych jest poza okresem przechowywania bazy danych dystrybucji, administrator wystąpienia zarządzanego SQL będzie musiał ponownie zainicjować bazę danych subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:

- [Konfigurowanie replikacji między wydawcą i subskrybentem wystąpienia zarządzanego SQL](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Skonfiguruj replikację między Wydawca wystąpienia zarządzanego SQL, dystrybutorem wystąpienia zarządzanego SQL i subskrybentem SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Utwórz publikację](/sql/relational-databases/replication/publish/create-a-publication).
- [Utwórz subskrypcję wypychaną](/sql/relational-databases/replication/create-a-push-subscription) , używając nazwy serwera jako subskrybenta (na przykład `N'azuresqldbdns.database.windows.net` bazy danych w Azure SQL Database nazwie jako docelowej bazy danych (na przykład **AdventureWorks**. )

## <a name="see-also"></a>Zobacz też  

- [Replikacja z wystąpieniem zarządzanym SQL i grupą trybu failover](transact-sql-tsql-differences-sql-server.md#replication)
- [Replikacja do bazy danych SQL](../database/replication-to-sql-database.md)
- [Replikacja do wystąpienia zarządzanego](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Tworzenie publikacji](/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](/sql/relational-databases/replication/initialize-a-subscription)

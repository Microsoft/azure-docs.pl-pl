---
title: Replikacja transakcyjna
description: Dowiedz się więcej o używaniu SQL Server replikacji transakcyjnej z pojedynczymi bazami danych w puli i wystąpieniami w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607593"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replikacja transakcyjna z pojedynczymi, w puli i wystąpieniami baz danych w Azure SQL Database

Replikacja transakcyjna to funkcja Azure SQL Database i SQL Server, która umożliwia replikowanie danych z tabeli w Azure SQL Database lub SQL Server do tabel umieszczonych w zdalnych bazach danych. Ta funkcja pozwala synchronizować wiele tabel w różnych bazach danych.

## <a name="when-to-use-transactional-replication"></a>Kiedy używać replikacji transakcyjnej

Replikacja transakcyjna jest przydatna w następujących scenariuszach:
- Publikuj zmiany wprowadzone w co najmniej jednej tabeli w bazie danych i Rozpowszechnij je w jednej lub wielu SQL Server lub bazach danych Azure SQL, które zasubskrybują zmiany.
- Utrzymuj synchronizację kilku rozproszonych baz danych.
- Migrowanie baz danych z jednego SQL Server lub wystąpienia zarządzanego do innej bazy danych przez ciągłe publikowanie zmian.

## <a name="overview"></a>Omówienie

Najważniejsze składniki replikacji transakcyjnej przedstawiono na poniższej ilustracji:  

![replikacja przy użyciu SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

**Wydawca** jest wystąpieniem lub serwerem, który publikuje zmiany wprowadzone w niektórych tabelach (artykuły), wysyłając aktualizacje do dystrybutora. Publikowanie w dowolnej bazie danych Azure SQL Database z lokalnego SQL Server jest obsługiwane przez następujące wersje SQL Server:

- SQL Server 2019 (wersja zapoznawcza)
- SQL Server 2016 do SQL 2017
- SQL Server 2014 z dodatkiem SP1 CU3 lub nowszym (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 z dodatkiem SP3 lub nowszym (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- W przypadku innych wersji SQL Server, które nie obsługują publikowania w obiektach na platformie Azure, można użyć metody [ponownego publikowania danych](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) w celu przeniesienia danych do nowszych wersji SQL Server. 

**Dystrybutor** jest wystąpieniem lub serwerem, który gromadzi zmiany w artykułach od wydawcy i dystrybuuje je do subskrybentów. Dystrybutor może być Azure SQL Database wystąpieniem zarządzanym lub SQL Server (dowolna wersja jest równa lub większa niż wersja wydawcy). 

**Subskrybent** jest wystąpieniem lub serwerem, który otrzymuje zmiany dokonane przez wydawcę. Subskrybenci mogą być pojedynczymi, w puli i wystąpieniami baz danych w bazie danych Azure SQL Database lub SQL Server. Subskrybent z pojedynczą lub pulą baz danych musi być skonfigurowany jako subskrybent wypychania. 

| Rola | Pojedyncze i w puli baz danych | Wystąpienia baz danych |
| :----| :------------- | :--------------- |
| **Wydawca** | Nie | Tak | 
| **Dystrybutor** | Nie | Tak|
| **Subskrybent ściągania** | Nie | Tak|
| **Wypychanie subskrybenta**| Tak | Tak|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Subskrypcja ściągania nie jest obsługiwana, gdy dystrybutor jest bazą danych wystąpienia i subskrybentem nie jest. 

Istnieją różne [typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikacja | Pojedyncze i w puli baz danych | Wystąpienia baz danych|
| :----| :------------- | :--------------- |
| [**Standardowa transakcyjna**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybent) | Tak | 
| [**Zdjęcie**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybent) | Tak|
| [**Replikacja scalająca**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| [**Kierunk**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Tak|
| [**Aktualizowalne subskrypcje**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Próba skonfigurowania replikacji przy użyciu starszej wersji może spowodować wystąpienie błędu MSSQL_REPL20084 (proces nie mógł nawiązać połączenia z subskrybentem) i MSSQ_REPL40532 (nie można otworzyć \<nazwy serwera> żądanej przez nazwę logowania. Logowanie nie powiodło się.
  > - Aby korzystać ze wszystkich funkcji Azure SQL Database, musisz używać najnowszych wersji narzędzi [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Macierz obsługi dla baz danych wystąpień i systemów lokalnych
  Macierz obsługi replikacji dla baz danych wystąpień jest taka sama jak dla SQL Server lokalnego. 
  
| **Wydawca**   | **Dystrybutor** | **Subscriber** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Wymagania

- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji. 
- Udział konta usługi Azure Storage dla katalogu roboczego używanego przez replikację. 
- Port 445 (ruch wychodzący TCP) musi być otwarty w regułach zabezpieczeń podsieci wystąpienia zarządzanego w celu uzyskania dostępu do udziału plików platformy Azure. 
- Port 1433 (ruch wychodzący TCP) musi zostać otwarty, jeśli Wydawca/dystrybutor znajduje się w wystąpieniu zarządzanym, a subskrybent nie jest. Może być również konieczne zmiana reguły zabezpieczeń ruchu wychodzącego sieciowej grupy zabezpieczeń wystąpienia zarządzanego `allow_linkedserver_outbound` dla programu dla **tagu usługi docelowej** portu 1433 `virtualnetwork` z `internet`na. 
- Wszystkie typy uczestników replikacji (wydawcy, dystrybutora, subskrybent ściągania i wypychania) mogą być umieszczane w wystąpieniach zarządzanych, ale Wydawca i dystrybutor muszą być zarówno w chmurze, jak i w środowisku lokalnym.
- Jeśli Wydawca, dystrybutor i/lub subskrybent istnieje w różnych sieciach wirtualnych, Komunikacja równorzędna sieci VPN musi być ustanowiona między poszczególnymi jednostkami, na przykład w przypadku komunikacji równorzędnej VPN między wydawcą i dystrybutorem, a/lub komunikacji równorzędnej między dystrybutorem a subskrybentem. 


>[!NOTE]
> - W przypadku nawiązywania połączenia z plikiem usługi Azure Storage może wystąpić błąd 53, jeśli port wychodzącej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) 445 jest blokowany, gdy dystrybutor jest bazą danych wystąpienia, a subskrybent jest w środowisku lokalnym. Aby rozwiązać ten problem [, zaktualizuj sieciowej grupy zabezpieczeń sieci wirtualnej](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) . 


### <a name="compare-data-sync-with-transactional-replication"></a>Porównywanie synchronizacji danych z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | -Aktywne-aktywne wsparcie<br/>-Dwukierunkowe między środowiskiem lokalnym i Azure SQL Database | -Małe opóźnienia<br/>-Spójności transakcyjnej<br/>-Ponowne użycie istniejącej topologii po migracji |
| Wady | -5 min lub więcej opóźnień<br/>-Brak spójności transakcyjnej<br/>— Wyższy wpływ na wydajność | -Nie można publikować z Azure SQL Database pojedynczej bazy danych lub bazy danych w puli<br/>— Wysoki koszt konserwacji |
| | | |

## <a name="common-configurations"></a>Typowe konfiguracje

Ogólnie rzecz biorąc, Wydawca i dystrybutor musi znajdować się w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Wydawca z lokalnym dystrybutorem w wystąpieniu zarządzanym

![Pojedyncze wystąpienie jako Wydawca i dystrybutora](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Wydawca i dystrybutor są konfigurowane w ramach jednego wystąpienia zarządzanego i dystrybuowanie zmian do innych wystąpień zarządzanych, pojedynczej bazy danych, puli baz danych lub SQL Server lokalnych. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Wydawca z dystrybutorem zdalnym w wystąpieniu zarządzanym

W tej konfiguracji jedno wystąpienie zarządzane publikuje zmiany dystrybutora umieszczane w innym zarządzanym wystąpieniu, które może obsłużyć wiele wystąpień zarządzanych przez program, i rozesłać zmiany do jednego lub wielu obiektów docelowych w wystąpieniu zarządzanym, pojedynczej bazie danych, puli baz danych lub SQL Server.

![Oddziel wystąpienia wydawcy i dystrybutora](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Wydawca i dystrybutor są konfigurowane w dwóch zarządzanych wystąpieniach. Istnieją pewne ograniczenia dotyczące tej konfiguracji: 

- Oba wystąpienia zarządzane znajdują się w tej samej sieci wirtualnej.
- Oba wystąpienia zarządzane znajdują się w tej samej lokalizacji.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Wydawca i dystrybutor w środowisku lokalnym z subskrybentem w jednej, puli i wystąpieniu bazy danych 

![Usługa Azure SQL DB jako subskrybent](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
W tej konfiguracji Azure SQL Database (pojedyncza, w puli i baza danych wystąpienia) jest subskrybentem. Ta konfiguracja obsługuje migrację z lokalizacji lokalnej na platformę Azure. Jeśli subskrybent znajduje się w jednej lub puli baz danych, musi być w trybie push.  

## <a name="with-failover-groups"></a>Z grupami trybu failover

W przypadku włączenia replikacji geograficznej w wystąpieniu **wydawcy** lub **dystrybutora** w [grupie trybu failover](sql-database-auto-failover-group.md)administrator wystąpienia zarządzanego musi oczyścić wszystkie publikacje na starym serwerze podstawowym i ponownie skonfigurować je na nowym serwerze podstawowym po przejściu w tryb failover. W tym scenariuszu są niezbędne następujące działania:

1. Zatrzymaj wszystkie zadania replikacji uruchomione w bazie danych, jeśli istnieją.
2. Porzuć metadane subskrypcji z wydawcą, uruchamiając następujący skrypt w bazie danych wydawcy:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Usuwanie metadanych subskrypcji z subskrybenta. Uruchom następujący skrypt na wystąpieniu bazy danych subskrypcji na subskrybencie:

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

1. Wymuszone porzucanie starego dystrybutora z oryginalnego wystąpienia podstawowego (w przypadku powrotu po awarii do starego elementu głównego, który używa dystrybutora). Uruchom następujący skrypt w bazie danych Master w starym wystąpieniu zarządzanym dystrybutora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

W przypadku włączenia replikacji geograficznej w wystąpieniu **subskrybenta** w grupie trybu failover publikacja powinna być skonfigurowana w taki sposób, aby nawiązać połączenie z punktem końcowym odbiornika grupy trybu failover dla wystąpienia zarządzanego przez subskrybenta. W przypadku przełączenia w tryb failover kolejne akcje podejmowane przez administratora wystąpienia zarządzanego są zależne od typu trybu failover, który wystąpił: 

- W przypadku przejścia w tryb failover bez utraty danych replikacja będzie działać po przejściu do trybu failover. 
- W przypadku pracy awaryjnej z utratą danych replikacja również będzie działała. Spowoduje to ponowną replikację utraconych zmian. 
- W przypadku przejścia w tryb failover z utratą danych, ale utrata danych jest poza okresem przechowywania bazy danych dystrybucji, administrator wystąpienia zarządzanego będzie musiał ponownie zainicjować bazę danych subskrypcji. 

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj replikację między wydawcą a subskrybentem](replication-with-sql-database-managed-instance.md)
- [Skonfiguruj replikację między wydawcą MI, dystrybutorem i SQL Server subskrybentem](sql-database-managed-instance-configure-replication-tutorial.md)
- [Utwórz publikację](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) , używając nazwy serwera Azure SQL Database jako subskrybenta (na przykład `N'azuresqldbdns.database.windows.net` i nazwy Azure SQL Database jako docelowej bazy danych (na przykład **AdventureWorks**. )


Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:



## <a name="see-also"></a>Zobacz też  

- [Replikacja za pomocą MI i grupy trybu failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikacja do bazy danych SQL](replication-to-sql-database.md)
- [Replikacja do wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  

---
title: Informacje o wersji
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach usługi Azure SQL Database oraz w dokumentacji Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: sstein
ms.openlocfilehash: 2d89320b4e5237017b51d19495c60c03ce6288f7
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838488"
---
# <a name="sql-database-release-notes"></a>Informacje o wersji SQL Database

W tym artykule wymieniono SQL Database funkcje, które są obecnie dostępne w publicznej wersji zapoznawczej. Aby uzyskać SQL Database aktualizacje i ulepszenia, zobacz [SQL Database Updates](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać aktualizacje i ulepszenia dla innych usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="single-database"></a>[Pojedyncza baza danych](#tab/single-database)

| Funkcja | Szczegóły |
| ---| --- |
| Nowe generacja sprzętu serii Fsv2 i serii M| Aby uzyskać więcej informacji, zobacz [generacja sprzętu](sql-database-service-tiers-vcore.md#hardware-generations).|
| Szybsze odzyskiwanie bazy danych przy użyciu pojedynczych baz danych i pul elastycznych | Aby uzyskać więcej informacji, zobacz [przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md).|
|Przybliżona liczba unikatowych|Aby uzyskać więcej informacji, zobacz [Przybliżona liczba odrębna](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Tryb wsadowy w magazynu wierszy (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [Tryb wsadowy w witrynie magazynu wierszy](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Odnajdowanie i klasyfikacja danych  |Aby uzyskać więcej informacji, zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikacji](sql-database-data-discovery-and-classification.md).|
| Zadania elastycznych baz danych | Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie zadań elastycznych oraz zarządzanie nimi](elastic-jobs-overview.md). |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [Omówienie zapytania elastycznego](sql-database-elastic-query-overview.md). |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze](sql-database-elastic-transactions-overview.md). |
|Opinie dotyczące przydzielenia pamięci (tryb wiersza) (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz informacje o [przydzieleniu pamięci (tryb wiersza)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Edytor zapytań w Azure Portal |Aby uzyskać więcej informacji, zobacz [Używanie edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych](sql-database-connect-query-portal.md).|
| Usługa R/Uczenie maszynowe z pojedynczymi bazami danych i pulami elastycznymi |Aby uzyskać więcej informacji, zobacz [Machine Learning Services w Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analiza SQL|Aby uzyskać więcej informacji, zobacz [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Kompilacja odroczona do zmiennej tabeli (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [kompilacja zmiennej tabeli odroczonej](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

| Funkcja | Szczegóły |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pule wystąpień</a> | Wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury. |
| <a href="https://aka.ms/managed-instance-aadlogins">Nazwy główne serwera usługi Azure AD na poziomie wystąpienia (logowania)</a> | Utwórz nazwy logowania na poziomie serwera przy użyciu instrukcji <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN from External Provider</a> . |
| [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) | Replikowanie zmian z tabel do innych baz danych umieszczonych w wystąpieniach zarządzanych, pojedynczych bazach danych lub wystąpieniach SQL Server lub aktualizowanie tabel w przypadku zmiany niektórych wierszy w innych wystąpieniach zarządzanych lub wystąpieniu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji w Azure SQL Database bazie danych wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md). |
| Wykrywanie zagrożeń |Aby uzyskać więcej informacji, zobacz [Konfigurowanie wykrywania zagrożeń w Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-threat-detection.md).|
| Długoterminowe przechowywanie kopii zapasowych | Aby uzyskać więcej informacji, zobacz [Konfigurowanie długoterminowego przechowywania kopii zapasowych w Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-long-term-backup-retention-configure.md), które jest obecnie w ograniczonej publicznej wersji zapoznawczej. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Wystąpienie zarządzane — nowe funkcje i znane problemy

### <a name="managed-instance-h2-2019-updates"></a>Zarządzane wystąpienie H2 2019 aktualizacje

- [Konfiguracja podsieci z obsługą usług](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Bezpieczny i wygodny sposób zarządzania konfiguracją podsieci służącą do sterowania ruchem danych, gdy wystąpienie zarządzane gwarantuje nieprzerwany przepływ ruchu zarządzania
- [Transparent Data Encryption (TDE) with Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umożliwia przeprowadzenie scenariusza z kluczem własnym (BYOK) w celu zapewnienia ochrony danych w spoczynku i umożliwia organizacjom oddzielenie obowiązków związanych z zarządzaniem kluczami i danymi.
- [Grupy autotrybu failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umożliwiają replikację wszystkich baz danych z wystąpienia podstawowego do wystąpienia dodatkowego w innym regionie.
- Skonfiguruj zachowanie wystąpienia zarządzanego przy użyciu [globalnych flag śledzenia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Wystąpienia zarządzane — aktualizacje 2019

W modelu wdrażania wystąpienia zarządzanego w H1 2019 są włączone następujące funkcje:
  - Obsługa subskrypcji za pomocą <a href="https://aka.ms/sql-mi-visual-studio-subscribers">miesięcznych środków na korzystanie z platformy Azure dla subskrybentów programu Visual Studio</a> i zwiększonych [granic regionalnych](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Obsługa <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">programów SharePoint 2016 i SharePoint 2019</a> oraz <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">usługi Dynamics 365 Business Central</a>
  - Utwórz wystąpienia z wybranym <a href="https://aka.ms/managed-instance-collation">sortowaniem na poziomie serwera</a> i <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">strefą czasową</a> .
  - Zarządzane wystąpienia są teraz chronione za pomocą <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">wbudowanej zapory</a>.
  - Skonfiguruj wystąpienia do korzystania z [publicznych punktów końcowych](sql-database-managed-instance-public-endpoint-configure.md), połączenia [zastąpień serwera proxy](sql-database-connectivity-architecture.md#connection-policy) w celu uzyskania lepszej wydajności sieci, <a href="https://aka.ms/four-cores-sql-mi-update">4 rdzeni wirtualnych na generowanie sprzętu 5 rdzeń</a> lub <a href="https://aka.ms/managed-instance-configurable-backup-retention">Skonfiguruj przechowywanie kopii zapasowych do 35 dni</a> w przypadku przywracania do punktu w czasie. [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md#managed-instance-support) (do 10 lat) jest obecnie w ograniczonej publicznej wersji zapoznawczej.  
  - Nowe funkcje umożliwiają <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">przeprzywracanie geograficznej bazy danych do innego centrum danych przy użyciu programu PowerShell</a>, [zmiana nazwy bazy danych](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [usunięcie klastra wirtualnego](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nowa [rola współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) wbudowanego wystąpienia umożliwia rozdzielenie cła (SOD) z zasadami zabezpieczeń i zgodności z normami przedsiębiorstwa.
  - Wystąpienie zarządzane jest dostępne w następujących Azure Government regionach do GA (US Gov Teksas, US Gov Arizona), a także w Chiny Północne 2 i Chiny Wschodnie 2. Jest ona również dostępna w następujących regionach publicznych: Australia Środkowa, Australia Środkowa 2, Brazylia Południowa, Francja Południowa, Europa Środkowa, Płn. Zjednoczone Emiraty Arabskie, Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki.

### <a name="known-issues"></a>Znane problemy

|Problem  |Data wykrycia  |Stan  |Data rozwiązania  |
|---------|---------|---------|---------|
|[Agent przestaje odpowiadać przy modyfikowaniu, wyłączaniu lub włączaniu istniejących zadań](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 maja|Automatycznie skorygowane| |
|[Uprawnienia do grupy zasobów nie zostały zastosowane do wystąpienia zarządzanego](#permissions-on-resource-group-not-applied-to-managed-instance)|2020 lutego|Ma obejście| |
|[Ograniczenie ręcznego trybu failover za pośrednictwem portalu dla grup trybu failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Sty 2020|Ma obejście| |
|[Role agenta SQL wymagają jawnych uprawnień do wykonywania dla logowań innych niż sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Ma obejście| |
|[Zadania agenta SQL można przerwać przez ponowne uruchomienie procesu agenta](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Resolved|Mar 2020|
|[Nazwy logowania i użytkownicy usługi AAD nie są obsługiwane w programie SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Lis 2019|Brak obejścia| |
|[Limity pamięci OLTP w pamięci nie są stosowane](#in-memory-oltp-memory-limits-are-not-applied)|2019 października|Ma obejście| |
|[Podczas próby usunięcia pliku, który nie jest pusty, został zwrócony nieprawidłowy błąd](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 października|Ma obejście| |
|[Zmiana warstwy usługi i tworzenie wystąpienia są blokowane przez trwającą operację przywracania bazy danych](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Wrz 2019|Ma obejście| |
|[Po przejściu w tryb failover może zajść konieczność ponownego skonfigurowania przyrządu zasobów na Krytyczne dla działania firmyej warstwie usług](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Wrz 2019|Ma obejście| |
|[Okna dialogowe Service Broker między bazami danych muszą zostać zainicjowane po uaktualnieniu warstwy usług](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 sie|Ma obejście| |
|[Impersonification typów logowania usługi Azure AD nie jest obsługiwana](#impersonification-of-azure-ad-login-types-is-not-supported)|Lip 2019|Brak obejścia| |
|[@queryparametr nie jest obsługiwany w sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Kwi 2019|Brak obejścia| |
|[Należy ponownie skonfigurować replikację transakcyjną po geograficznym przejściu do trybu failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Brak obejścia| |
|[Tymczasowa baza danych jest używana podczas operacji przywracania](#temporary-database-is-used-during-restore-operation)||Ma obejście| |
|[Struktura i zawartość bazy danych TEMPDB są odtwarzane](#tempdb-structure-and-content-is-re-created)| |Brak obejścia| |
|[Przekraczanie miejsca do magazynowania z małymi plikami bazy danych](#exceeding-storage-space-with-small-database-files)| |Ma obejście| |
|[Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych](#guid-values-shown-instead-of-database-names) ||Ma obejście| |
|[Dzienniki błędów nie są utrwalone](#error-logs-arent-persisted)||Brak obejścia| |
|[Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |Ma obejście| |
|[Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |Resolved|Mar 2020|
|Nie sprawdzono spójności bazy danych przy użyciu polecenia DBCC CHECKDB po przywróceniu bazy danych z platformy Azure Blob Storage.| |Resolved|Lis 2019|
|Przywracanie bazy danych do punktu w czasie z warstwy Krytyczne dla działania firmy do warstwy Ogólnego przeznaczenia nie powiedzie się, jeśli źródłowa baza danych zawiera obiekty OLTP w pamięci.| |Resolved|2019 października|
|Funkcja Poczta bazy danych z zewnętrznymi serwerami poczty (poza platformą Azure) przy użyciu bezpiecznego połączenia| |Resolved|2019 października|
|Zawarte bazy danych nie są obsługiwane w wystąpieniu zarządzanym| |Resolved|2019 sie|

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent przestaje odpowiadać przy modyfikowaniu, wyłączaniu lub włączaniu istniejących zadań

W pewnych okolicznościach modyfikacja istniejącego zadania, wyłączenie go lub włączenie go może spowodować, że Agent przestanie odpowiadać. Problem jest automatycznie zmniejszany w przypadku wykrywania powodującego ponowne uruchomienie procesu agenta.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Uprawnienia do grupy zasobów nie zostały zastosowane do wystąpienia zarządzanego

Rola RBAC współautor wystąpienia zarządzanego w przypadku zastosowania do grupy zasobów (RG) nie jest zastosowana do wystąpienia zarządzanego i nie ma żadnego wpływu.

**Obejście**: Skonfiguruj rolę współautor wystąpienia zarządzanego dla użytkowników na poziomie subskrypcji.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ograniczenie ręcznego trybu failover za pośrednictwem portalu dla grup trybu failover

Jeśli grupa trybu failover obejmuje wiele wystąpień w różnych subskrypcjach lub grupach zasobów platformy Azure, nie można zainicjować ręcznego przełączania do trybu failover z wystąpienia podstawowego w grupie trybu failover.

**Obejście**: zainicjuj pracę w trybie failover za pośrednictwem portalu z wystąpienia geograficznego.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL wymagają jawnych uprawnień do wykonywania dla logowań innych niż sysadmin

Jeśli nazwy logowania inne niż sysadmin są dodawane do dowolnych [ról bazy danych programu SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), istnieje problem, w którym jawne uprawnienia do wykonania muszą zostać przyznane głównym procedurom składowanym dla tych logowań. Jeśli wystąpi ten problem, zostanie wyświetlony komunikat o błędzie "uprawnienie EXECUTE zostało odrzucone w obiekcie <object_name> (Microsoft SQL Server, błąd: 229)".

**Obejście**: po dodaniu logowań do jednej z ról stałych baz danych programu SQL Agent: SQLAgentUserRole, SQLAgentReaderRole lub SQLAgentOperatorRole, dla każdego z nazw logowania dodanych do tych ról, wykonaj Poniższy skrypt T-SQL, aby jawnie udzielić uprawnień do wykonywania w wymienionych procedurach składowanych.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Zadania agenta SQL można przerwać przez ponowne uruchomienie procesu agenta

**(Rozwiązane w marcu 2020)** Program SQL Agent tworzy nową sesję przy każdym uruchomieniu zadania, stopniowo zwiększając użycie pamięci. Aby uniknąć przekroczenia limitu pamięci wewnętrznej, który blokuje wykonywanie zaplanowanych zadań, proces agenta zostanie uruchomiony ponownie, gdy jego użycie pamięci osiągnie wartość progową. Może to spowodować przerwanie wykonywania zadań uruchomionych w momencie ponownego uruchomienia.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity pamięci OLTP w pamięci nie są stosowane

Krytyczne dla działania firmy warstwa usług nie zastosuje prawidłowo [maksymalnych limitów pamięci dla obiektów zoptymalizowanych pod kątem pamięci](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) w niektórych przypadkach. Wystąpienie zarządzane może umożliwiać użycie większej ilości pamięci dla operacji OLTP w pamięci, co może mieć wpływ na dostępność i stabilność wystąpienia. Zapytania OLTP w pamięci, które zbliżają się do limitów, mogą kończyć się niepowodzeniem. Ten problem zostanie rozwiązany wkrótce. Zapytania, które używają więcej pamięci OLTP w pamięci, będą kończyć się niepowodzeniem, jeśli osiągnieją [limity](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Obejście:** [Monitoruj użycie magazynu OLTP w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) za pomocą [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) , aby upewnić się, że obciążenie nie korzysta z więcej niż dostępnej pamięci. Zwiększ limity pamięci, które są zależne od liczby rdzeni wirtualnych, lub Zoptymalizuj obciążenie, aby użyć mniejszej ilości pamięci.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Podczas próby usunięcia pliku, który nie jest pusty, został zwrócony nieprawidłowy błąd

Wystąpienie SQL Server/zarządzane [nie zezwala użytkownikowi na usuwanie niepustego pliku](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Próba usunięcia niepustego pliku danych za pomocą `ALTER DATABASE REMOVE FILE` instrukcji spowoduje, że błąd `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` nie zostanie natychmiast zwrócony. Wystąpienie zarządzane będzie nadal próbowało porzucić plik i operacja zakończy się niepowodzeniem po `Internal server error`fragmentach z.

**Obejście**: Usuń zawartość pliku przy użyciu `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` polecenia. Jeśli jest to jedyny plik w grupie plików, należy usunąć dane z tabeli lub partycji skojarzonej z tą grupą plików przed zmniejszeniem pliku i opcjonalnie załadować te dane do innej tabeli lub partycji.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Zmiana warstwy usługi i tworzenie wystąpienia są blokowane przez trwającą operację przywracania bazy danych

Ciągła `RESTORE` instrukcja, proces migracji usługi migracji danych oraz wbudowane przywracanie do punktu w czasie zablokują aktualizację warstwy usług lub zmiany rozmiaru istniejącego wystąpienia, a następnie tworzy nowe wystąpienia do momentu zakończenia procesu przywracania. Proces przywracania spowoduje zablokowanie tych operacji na zarządzanych wystąpieniach i pulach wystąpień w tej samej podsieci, w której jest uruchomiony proces przywracania. Nie ma to żadnego oddziaływania na wystąpienia w pulach wystąpień. Operacje tworzenia lub zmiany warstwy usług nie będą kończyć się niepowodzeniem lub przekroczenia limitu czasu — będą one działać po ukończeniu lub anulowaniu procesu przywracania.

**Obejście**: Poczekaj na zakończenie procesu przywracania lub Anuluj proces przywracania, jeśli operacja tworzenia lub aktualizacji warstwy usługi ma wyższy priorytet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po przejściu w tryb failover może zajść konieczność ponownego skonfigurowania przyrządu zasobów na Krytyczne dla działania firmyej warstwie usług

Funkcja [zarządcy zasobów](/sql/relational-databases/resource-governor/resource-governor) , która umożliwia ograniczenie zasobów przypisanych do obciążenia użytkownikami, może nieprawidłowo sklasyfikować pewne obciążenie użytkownika po przejściu w tryb failover lub na zainicjowanej przez użytkownika zmianie warstwy usług (na przykład zmiana maksymalnego rozmiaru magazynu rdzeń wirtualny lub maksymalnego wystąpienia).

**Obejście**: `ALTER RESOURCE GOVERNOR RECONFIGURE` uruchamiaj okresowo lub jako część zadania programu SQL Agent, które wykonuje zadanie SQL, gdy wystąpienie zostanie uruchomione, gdy korzystasz z [gubernatora zasobów](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Okna dialogowe Service Broker między bazami danych muszą zostać zainicjowane po uaktualnieniu warstwy usług

Okna dialogowe Service Broker między bazami danych przestaną przekazanie komunikatów do usług w innych bazach danych po operacji zmiany warstwy usług. Komunikaty nie są **tracone** i znajdują się w kolejce nadawcy. Każda zmiana rozmiaru magazynu rdzeni wirtualnych lub wystąpienia w wystąpieniu zarządzanym spowoduje zmianę `service_broke_guid` wartości w widoku [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) dla wszystkich baz danych. Wszystkie `DIALOG` utworzone przy użyciu instrukcji [BEGIN dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , które odwołują się do brokerów usług w innej bazie danych, zatrzymają dostarczanie komunikatów do usługi docelowej.

**Obejście problemu:** Przed aktualizacją warstwy usług Zatrzymaj wszystkie działania, które używają konwersacji między bazami danych Service Broker. Jeśli pozostałe komunikaty są niedostarczone po zmianie warstwy usług, należy odczytać komunikaty z kolejki źródłowej i ponownie wysłać je do kolejki docelowej.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonification typów logowania usługi Azure AD nie jest obsługiwana

Personifikacja `EXECUTE AS USER` przy `EXECUTE AS LOGIN` użyciu lub następujących głównych podmiotów usługi AAD nie jest obsługiwana:
-    Aliasy użytkowników usługi AAD. W tym przypadku `15517`zwracany jest następujący błąd.
- Nazwy logowania i użytkownicy usługi AAD w oparciu o aplikacje lub nazwy główne usług w usłudze AAD. W takim przypadku `15517` zwracane są następujące błędy i `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametr nie jest obsługiwany w sp_send_db_mail

`@query` Parametr w procedurze [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nie działa.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Należy ponownie skonfigurować replikację transakcyjną po geograficznym przejściu do trybu failover

Jeśli replikacja transakcyjna jest włączona w bazie danych w grupie autotrybu failover, administrator wystąpienia zarządzanego musi oczyścić wszystkie publikacje na starym serwerze podstawowym i skonfigurować je ponownie na nowym serwerze podstawowym po przejściu w tryb failover do innego regionu. Aby uzyskać więcej informacji, zobacz [replikacja](sql-database-managed-instance-transact-sql-information.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Nazwy logowania i użytkownicy usługi AAD nie są obsługiwane w programie SSDT

Narzędzia SQL Server Data Tools nie obsługują w pełni logowania i użytkowników usługi Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tymczasowa baza danych jest używana podczas operacji przywracania

Gdy baza danych jest przywracana w wystąpieniu zarządzanym, usługa Restore najpierw utworzy pustą bazę danych o podanej nazwie w celu przydzielenia nazwy do wystąpienia. Po pewnym czasie ta baza danych zostanie porzucona, a przywrócenie rzeczywistej bazy danych zostanie rozpoczęte. Baza danych, która jest w stanie *przywracania* , będzie miała tymczasową wartość identyfikatora GUID, a nie nazwę. Nazwa tymczasowa zostanie zmieniona na żądaną nazwę określoną w `RESTORE` instrukcji po zakończeniu procesu przywracania. W fazie początkowej użytkownik może uzyskać dostęp do pustej bazy danych, a nawet utworzyć tabele lub załadować dane w tej bazie danych. Ta Tymczasowa baza danych zostanie porzucona, gdy usługa przywracania rozpocznie działanie drugiej fazy.

**Obejście**: nie należy uzyskiwać dostępu do przywracanej bazy danych, dopóki nie zobaczysz, że Przywracanie zostało zakończone.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura i zawartość bazy danych TEMPDB są odtwarzane

`tempdb` Baza danych jest zawsze podzielona na 12 plików danych i nie można zmienić struktury pliku. Nie można zmienić maksymalnego rozmiaru pliku i nie można dodać do `tempdb`niego nowych plików. `Tempdb`zawsze jest tworzona jako pusta baza danych, gdy wystąpienie zostanie uruchomione lub działa w trybie failover, a wszelkie zmiany wprowadzone `tempdb` w programie nie zostaną zachowane.

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekraczanie miejsca do magazynowania z małymi plikami bazy danych

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, i `RESTORE DATABASE` instrukcje mogą się nie powieść, ponieważ wystąpienie może osiągnąć limit magazynu platformy Azure.

Każde Ogólnego przeznaczenia wystąpienia zarządzanego ma do 35 TB pamięci zarezerwowanej dla miejsca na dysku w warstwie Premium. Każdy plik bazy danych jest umieszczany na osobnym dysku fizycznym. Rozmiary dysków mogą być 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku jest nieobciążone, ale całkowita suma rozmiarów dysków w warstwie Premium platformy Azure nie może przekroczyć 35 TB. W niektórych przypadkach wystąpienie zarządzane, które nie wymaga 8 TB w sumie, może przekroczyć limit 35 TB platformy Azure dla rozmiaru magazynu z powodu wewnętrznej fragmentacji.

Na przykład wystąpienie zarządzane Ogólnego przeznaczenia może mieć jeden duży plik o rozmiarze 1,2 TB na dysku z 4 TBm. Może również mieć 248 plików o rozmiarze 1 GB, które są umieszczane na oddzielnych dyskach 128 GB. W tym przykładzie:

- Łączny rozmiar magazynu dyskowego to 1 x 4 TB + 248 x 128 GB = 35 TB.
- Całkowite zarezerwowane miejsce dla baz danych w wystąpieniu to 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

W tym przykładzie pokazano, że w pewnych okolicznościach, ze względu na określoną dystrybucję plików, wystąpienie zarządzane może osiągnąć limit 35-TB zarezerwowane dla dołączonego dysku Azure Premium, gdy użytkownik może nie oczekiwać.

W tym przykładzie istniejące bazy danych nadal pracują i mogą wzrosnąć bez żadnego problemu, o ile nie zostaną dodane nowe pliki. Nie można utworzyć ani przywrócić nowych baz danych, ponieważ nie ma wystarczającej ilości miejsca na nowe dyski, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiągnie limitu rozmiaru wystąpienia. Błąd zwracany w tym przypadku nie jest wyczyszczony.

[Liczbę pozostałych plików można określić](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) za pomocą widoków systemu. Jeśli osiągnięto ten limit, spróbuj [opróżnić i usunąć niektóre z mniejszych plików przy użyciu instrukcji DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełączyć się do [warstwy krytyczne dla działania firmy, która nie ma tego ograniczenia](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych

W kilku widokach systemu, licznikach wydajności, komunikatach o błędach, XEvents i dzienniku błędów są wyświetlane identyfikatory baz danych GUID zamiast rzeczywistej nazwy bazy danych. Nie należy polegać na tych identyfikatorach GUID, ponieważ są one zastępowane rzeczywistymi nazwami baz danych w przyszłości.

**Obejście**: Użyj widoku sys. databases, aby rozpoznać rzeczywistą nazwę bazy danych z nazwy fizycznej bazy danych określonej w postaci identyfikatorów baz danych GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są utrwalone

Dzienniki błędów dostępne w wystąpieniu zarządzanym nie są utrwalane, a ich rozmiar nie jest uwzględniony w maksymalnym limicie magazynu. Dzienniki błędów mogą być automatycznie wymazywane w przypadku przełączenia w tryb failover. W historii dzienników błędów mogą występować luki, ponieważ wystąpienie zarządzane zostało przeniesione kilka razy na kilka maszyn wirtualnych.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany

**(Rozwiązane w marcu 2020)** Klasa `TransactionScope` w programie .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w tym samym wystąpieniu w ramach tego samego zakresu transakcji:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Obejście (niewymagane od marca 2020):** Użyj elementu [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) , aby użyć innej bazy danych w kontekście połączenia zamiast korzystać z dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP

Moduły CLR umieszczane w wystąpieniu zarządzanym oraz połączone serwery lub zapytania rozproszone, które odwołują się do bieżącego wystąpienia czasami nie mogą rozpoznać adresu IP wystąpienia lokalnego. Ten błąd jest przejściowym problemem.

**Obejście problemu:** Jeśli to możliwe, Użyj połączeń kontekstu w module CLR.

## <a name="updates"></a>Aktualizacje

Listę aktualizacji i ulepszeń SQL Database można znaleźć w temacie [SQL Database Updates Service](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać aktualizacje i ulepszenia dla wszystkich usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby współtworzyć dokumentację Azure SQL Database, zobacz [Przewodnik współautora dokumentów](https://docs.microsoft.com/contribute/).

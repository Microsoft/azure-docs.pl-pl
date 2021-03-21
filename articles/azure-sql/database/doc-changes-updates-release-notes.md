---
title: Co nowego
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się więcej na temat nowych funkcji i ulepszeń dokumentacji Azure SQL Database & wystąpienia zarządzane SQL.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: sstein
ms.openlocfilehash: 0ddd2c96be3513d253537cefd5b9eb83da2b3c12
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634842"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Co nowego w Azure SQL Database & wystąpieniu zarządzanym SQL?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule wymieniono Azure SQL Database i funkcje wystąpienia zarządzanego usługi Azure SQL, które są obecnie dostępne w publicznej wersji zapoznawczej. Aby uzyskać SQL Database i ulepszenia i usprawnienia wystąpienia zarządzanego przez usługę SQL, zobacz [SQL Database & aktualizacje usługi wystąpienia zarządzanego SQL](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać aktualizacje i ulepszenia dla innych usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Co nowego

Dokumentacja Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL zostały podzielone na oddzielne sekcje. Zaktualizowaliśmy również sposób odwoływania się do wystąpienia zarządzanego z *Azure SQL Database wystąpienia zarządzanego* do *wystąpienia zarządzanego Azure SQL*.

Zostało to zrobione, ponieważ niektóre funkcje i funkcje różnią się w zależności od pojedynczej bazy danych i wystąpienia zarządzanego. coraz trudniejsze jest wyjaśnienie złożonej wszystkie szczegóły między Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL w poszczególnych udostępnionych artykułach.

To wyjaśnienie między różnymi produktami SQL platformy Azure powinna uprościć i usprawnić proces pracy z aparatem bazy danych SQL Server na platformie Azure, niezależnie od tego, czy jest to pojedyncza zarządzana baza danych w Azure SQL Database, w pełni dopracowane wystąpienie zarządzane obsługujące wiele baz danych w wystąpieniu zarządzanym Azure SQL lub znany lokalnie SQL Server produkt hostowany na maszynie wirtualnej na platformie Azure.

Należy wziąć pod uwagę, że jest to trwające prace, a nie każdy artykuł został jeszcze zaktualizowany. Na przykład dokumentacja dotycząca instrukcji języka Transact-SQL (T-SQL), procedur składowanych i wielu funkcji udostępnionych między Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL nie została jeszcze ukończona, więc Dziękujemy za cierpliwość w zakresie wyjaśniania zawartości. 

Ta tabela zawiera krótkie porównanie zmian w terminologii: 


|**Nowy termin**  | **Poprzedni termin**  |**Wyjaśnienie** |
|---------|---------|---------|
|**Wystąpienie zarządzane Azure SQL** | Azure SQL Database *wystąpienie zarządzane*| Wystąpienie zarządzane Azure SQL to własny produkt należący do rodziny Azure SQL, a nie tylko opcja wdrażania w ramach Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database *pojedynczą bazę danych*| O ile nie określono jawnie inaczej, nazwa produktu Azure SQL Database obejmuje pojedyncze bazy danych i bazy danych wdrożone w puli elastycznej. |
|**Azure SQL Database**|Azure SQL Database *elastyczna Pula*| O ile nie określono jawnie inaczej, nazwa produktu Azure SQL Database obejmuje pojedyncze bazy danych i bazy danych wdrożone w puli elastycznej.  |
|**Azure SQL Database** |Azure SQL Database | Mimo że termin pozostaje taki sam, ma zastosowanie tylko do wdrożeń pojedynczej bazy danych i pul elastycznych i nie obejmuje wystąpienia zarządzanego. |
| **Azure SQL**| Nie dotyczy | Odnosi się to do rodziny SQL Server produktów aparatu bazy danych, które są dostępne na platformie Azure: Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i SQL Server na maszynach wirtualnych platformy Azure. | 

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Cecha | Szczegóły |
| ---| --- |
| Zadania Elastic Database (wersja zapoznawcza) | Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie zadań elastycznych oraz zarządzanie nimi](elastic-jobs-overview.md). |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [Omówienie zapytania elastycznego](elastic-query-overview.md). |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze](elastic-transactions-overview.md). |
| Edytor zapytań w Azure Portal |Aby uzyskać więcej informacji, zobacz [Używanie edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych](connect-query-portal.md).|
|Analiza SQL|Aby uzyskać więcej informacji, zobacz [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

| Cecha | Szczegóły |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">Transakcje rozproszone</a> | Transakcje rozproszone między wystąpieniami zarządzanymi. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pule wystąpień</a> | Wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury. |
| <a href="/en-gb/sql/t-sql/statements/create-login-transact-sql">Nazwy główne serwera usługi Azure AD na poziomie wystąpienia (logowania)</a> | Utwórz identyfikatory logowania na poziomie wystąpienia przy użyciu instrukcji <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true">CREATE LOGIN from External Provider</a> . |
| [Replikacja transakcyjna](../managed-instance/replication-transactional-overview.md) | Replikuj zmiany z tabel do innych baz danych w wystąpieniu zarządzanym SQL, SQL Database lub SQL Server. Lub zaktualizuj tabele, gdy niektóre wiersze są zmieniane w innych wystąpieniach wystąpienia zarządzanego SQL lub SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji w wystąpieniu zarządzanym usługi Azure SQL](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Wykrywanie zagrożeń |Aby uzyskać więcej informacji, zobacz [Konfigurowanie wykrywania zagrożeń w wystąpieniu zarządzanym usługi Azure SQL](../managed-instance/threat-detection-configure.md).|
| Długoterminowe przechowywanie kopii zapasowych | Aby uzyskać więcej informacji, zobacz [Konfigurowanie długoterminowego przechowywania kopii zapasowych w wystąpieniu zarządzanym usługi Azure SQL](../managed-instance/long-term-backup-retention-configure.md), które jest obecnie w ograniczonej publicznej wersji zapoznawczej. | 

---

## <a name="new-features"></a>Nowe funkcje

### <a name="sql-managed-instance-h2-2019-updates"></a>Wystąpienia zarządzane SQL H2 2019 aktualizacje

- [Konfiguracja podsieci wspomagana przez usługę](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) jest bezpiecznym i wygodnym sposobem zarządzania konfiguracją podsieci w celu kontrolowania ruchu danych, gdy wystąpienie zarządzane SQL gwarantuje nieprzerwany przepływ ruchu zarządzania.
- [Transparent Data Encryption (TDE) with Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umożliwia korzystanie ze scenariusza "dołączenie do własnego klucza" (BYOK) w celu zapewnienia ochrony danych w czasie spoczynku i umożliwia organizacjom oddzielenie obowiązków związanych z zarządzaniem kluczami i danymi.
- [Grupy autotrybu failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umożliwiają replikację wszystkich baz danych z wystąpienia podstawowego do wystąpienia dodatkowego w innym regionie.
- [Globalne flagi śledzenia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) umożliwiają skonfigurowanie zachowania wystąpienia zarządzanego SQL.

### <a name="sql-managed-instance-h1-2019-updates"></a>Wystąpienia zarządzane SQL H1 2019 aktualizacje

W modelu wdrażania wystąpienia zarządzanego SQL w H1 2019 są włączone następujące funkcje:
  - Obsługa subskrypcji za pomocą <a href="/azure/azure-sql/managed-instance/resource-limits"> miesięcznych środków na korzystanie z platformy Azure dla subskrybentów programu Visual Studio </a> i zwiększonych [granic regionalnych](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Obsługa programów <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 i sharepoint 2019 </a> oraz <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Utwórz wystąpienie zarządzane z <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">sortowaniem na poziomie wystąpienia</a> i wybraną <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">strefę czasową</a> .
  - Zarządzane wystąpienia są teraz chronione za pomocą [wbudowanej zapory](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Skonfiguruj wystąpienie zarządzane SQL, tak aby korzystało z [publicznych punktów końcowych](../managed-instance/public-endpoint-configure.md), połączenia [zastąpień serwera proxy](connectivity-architecture.md#connection-policy) w celu uzyskania lepszej wydajności sieci, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 rdzeni wirtualnych na generowanie sprzętu 5 rdzeń</a> lub <a href="/azure/azure-sql/database/automated-backups-overview">Skonfiguruj przechowywanie kopii zapasowych do 35 dni</a> w przypadku przywracania do punktu w czasie. [Długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md) (do 10 lat) jest obecnie dostępne w publicznej wersji zapoznawczej.  
  - Nowe funkcje umożliwiają <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">przeprzywracanie geograficznej bazy danych do innego centrum danych przy użyciu programu PowerShell</a>, [zmiana nazwy bazy danych](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [usunięcie klastra wirtualnego](../managed-instance/virtual-cluster-delete.md).
  - Nowa [rola współautor](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) wbudowanego wystąpienia umożliwia rozdzielenie cła (SOD) z zasadami zabezpieczeń i zgodności z normami przedsiębiorstwa.
  - Wystąpienie zarządzane SQL jest dostępne w następujących regionach Azure Government, do GA (US Gov Teksas, US Gov Arizona) i w Chiny Północne 2 i Chiny Wschodnie 2. Jest ona również dostępna w następujących regionach publicznych: Australia Środkowa, Australia Środkowa 2, Brazylia Południowa, Francja Południowa, Europa Środkowa, Płn. Zjednoczone Emiraty Arabskie, Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki.

## <a name="known-issues"></a>Znane problemy

|Problem  |Data wykrycia  |Stan  |Data rozwiązania  |
|---------|---------|---------|---------|
|[Procedura sp_send_dbmail może przejściowo kończyć się niepowodzeniem, gdy @query parametr jest używany](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Sty 2021|Ma obejście||
|[Transakcje rozproszone można wykonać po usunięciu wystąpienia zarządzanego z grupy zaufania serwera](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020 października|Ma obejście||
|[Nie można wykonać transakcji rozproszonych po operacji skalowania wystąpienia zarządzanego](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020 października|Ma obejście||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / Funkcja [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) w usłudze Azure SQL i `BACKUP` / `RESTORE` instrukcja w wystąpieniu zarządzanym nie mogą używać tożsamości zarządzania usługą Azure AD do uwierzytelniania w usłudze Azure Storage|Wrz 2020|Ma obejście||
|[Nazwa główna usługi nie może uzyskać dostępu do usługi Azure AD i AKV](#service-principal-cannot-access-azure-ad-and-akv)|2020 sie|Ma obejście||
|[Przywrócenie ręcznej kopii zapasowej bez sumy KONTROLnej może zakończyć się niepowodzeniem](#restoring-manual-backup-without-checksum-might-fail)|Maj 2020 r.|Resolved|Czerwiec 2020 r.|
|[Agent przestaje odpowiadać po zmodyfikowaniu, wyłączeniu lub włączeniu istniejących zadań](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maj 2020 r.|Resolved|Czerwiec 2020 r.|
|[Uprawnienia do grupy zasobów nie są stosowane do wystąpienia zarządzanego SQL](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020 lutego|Resolved|Lis 2020|
|[Ograniczenie ręcznego trybu failover za pośrednictwem portalu dla grup trybu failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Sty 2020|Ma obejście||
|[Role agenta SQL wymagają jawnych uprawnień do wykonywania w przypadku identyfikatorów logowania innych niż sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Ma obejście||
|[Zadania agenta SQL można przerwać przez ponowne uruchomienie procesu agenta](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Resolved|Mar 2020|
|[Nazwy logowania i użytkownicy usługi Azure AD nie są obsługiwane w programie SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Lis 2019|Brak obejścia||
|[Limity pamięci OLTP w pamięci nie są stosowane](#in-memory-oltp-memory-limits-are-not-applied)|2019 października|Ma obejście||
|[Podczas próby usunięcia pliku, który nie jest pusty, został zwrócony nieprawidłowy błąd](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 października|Ma obejście||
|[Zmiana warstwy usługi i tworzenie wystąpienia są blokowane przez trwającą operację przywracania bazy danych](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Wrz 2019|Ma obejście||
|[Po przejściu w tryb failover może zajść konieczność ponownego skonfigurowania przyrządu zasobów na Krytyczne dla działania firmyej warstwie usług](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Wrz 2019|Ma obejście||
|[Okna dialogowe Service Broker między bazami danych muszą zostać zainicjowane ponownie po uaktualnieniu warstwy usług](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019 sie|Ma obejście||
|[Personifikacja typów logowania usługi Azure AD nie jest obsługiwana](#impersonation-of-azure-ad-login-types-is-not-supported)|Lip 2019|Brak obejścia||
|[@query parametr nie jest obsługiwany w sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Kwi 2019|Resolved|Sty 2021|
|[Należy ponownie skonfigurować replikację transakcyjną po geograficznym przejściu do trybu failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Brak obejścia||
|[Tymczasowa baza danych jest używana podczas operacji przywracania](#temporary-database-is-used-during-restore-operation)||Ma obejście||
|[Struktura i zawartość bazy danych TEMPDB są odtwarzane](#tempdb-structure-and-content-is-re-created)||Brak obejścia||
|[Przekraczanie miejsca do magazynowania z małymi plikami bazy danych](#exceeding-storage-space-with-small-database-files)||Ma obejście||
|[Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych](#guid-values-shown-instead-of-database-names)||Ma obejście||
|[Dzienniki błędów nie są utrwalone](#error-logs-arent-persisted)||Brak obejścia||
|[Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ma obejście|Mar 2020|
|[Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ma obejście||
|Nie sprawdzono spójności bazy danych przy użyciu polecenia DBCC CHECKDB po przywróceniu bazy danych z platformy Azure Blob Storage.||Resolved|Lis 2019|
|Przywracanie bazy danych do punktu w czasie z warstwy Krytyczne dla działania firmy do warstwy Ogólnego przeznaczenia nie powiedzie się, jeśli źródłowa baza danych zawiera obiekty OLTP w pamięci.||Resolved|2019 października|
|Funkcja poczty bazy danych z zewnętrznymi serwerami poczty (poza platformą Azure) przy użyciu bezpiecznego połączenia||Resolved|2019 października|
|Zawarte bazy danych nie są obsługiwane w wystąpieniu zarządzanym SQL||Resolved|2019 sie|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Procedura sp_send_dbmail może przejściowo kończyć się niepowodzeniem, gdy @query parametr jest używany

Procedura sp_send_dbmail może przejściowo kończyć się niepowodzeniem, gdy `@query` parametr jest używany. W przypadku wystąpienia tego problemu Wystąpił błąd i komunikat o błędzie co druga procedura sp_send_dbmail `Msg 22050, Level 16, State 1` `Failed to initialize sqlcmd library with error number -2147467259` . Aby można było prawidłowo zobaczyć ten błąd, procedura powinna zostać wywołana z wartością domyślną 0 dla parametru `@exclude_query_output` , w przeciwnym razie błąd nie zostanie propagowany.
Przyczyną tego problemu jest znana usterka dotycząca sposobu używania personifikacji i puli połączeń przez sp_send_dbmail.
Aby obejść ten problem, Zapakuj kod w celu wysłania wiadomości e-mail do logiki ponawiania, która opiera się na parametrze danych wyjściowych `@mailitem_id` . Jeśli wykonanie nie powiedzie się, wartość parametru będzie RÓWNa NULL, co oznacza, że sp_send_dbmail powinna być wywoływana jeszcze raz, aby pomyślnie wysłać wiadomość e-mail. Oto przykład tej logiki ponawiania.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Transakcje rozproszone można wykonać po usunięciu wystąpienia zarządzanego z grupy zaufania serwera

[Grupy zaufania serwera](../managed-instance/server-trust-group-overview.md) są używane do ustanawiania relacji zaufania między wystąpieniami zarządzanymi, które są warunkiem wstępnym wykonywania [transakcji rozproszonych](./elastic-transactions-overview.md). Po usunięciu wystąpienia zarządzanego z grupy zaufania serwera lub usunięciu grupy nadal może być możliwe wykonywanie transakcji rozproszonych. Istnieje obejście, które można zastosować, aby upewnić się, że transakcje rozproszone są wyłączone i że [inicjowane przez użytkownika ręczne](../managed-instance/user-initiated-failover.md) przełączenie w tryb failover w wystąpieniu zarządzanym.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Nie można wykonać transakcji rozproszonych po operacji skalowania wystąpienia zarządzanego

Operacje skalowania wystąpienia zarządzanego, które obejmują zmianę warstwy usług lub liczby rdzeni wirtualnych, spowodują zresetowanie ustawień grupy zaufania serwera w zapleczu i wyłączenie uruchamiania [transakcji rozproszonych](./elastic-transactions-overview.md). Aby obejść ten sposób, Usuń i Utwórz nową [grupę zaufania serwera](../managed-instance/server-trust-group-overview.md) na Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>Instrukcje BULK INSERT i tworzenia kopii zapasowej/przywracania nie mogą używać tożsamości zarządzanej do uzyskiwania dostępu do usługi Azure Storage

Instrukcje wstawiania zbiorczego, tworzenia kopii zapasowych i przywracania oraz funkcji OPENROWSET nie mogą być używane `DATABASE SCOPED CREDENTIAL` z tożsamością zarządzaną do uwierzytelniania w usłudze Azure Storage. Aby obejść ten sposób, przełącz się na uwierzytelnianie SYGNATURy dostępu współdzielonego. Poniższy przykład nie będzie działał w przypadku usługi Azure SQL (bazy danych i wystąpienia zarządzanego):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Obejście**: Użyj [sygnatury dostępu współdzielonego, aby uwierzytelnić w magazynie](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Nazwa główna usługi nie może uzyskać dostępu do usługi Azure AD i AKV

W pewnych okolicznościach może wystąpić problem z jednostką usługi służącą do uzyskiwania dostępu do usług Azure AD i Azure Key Vault (AKV). W związku z tym ten problem ma wpływ na użycie uwierzytelniania usługi Azure AD i przezroczyste szyfrowanie bazy danych (TDE) z wystąpieniem zarządzanym SQL. Może się to zdarzyć w przypadku sporadycznego problemu z łącznością lub nie można uruchomić takich instrukcji, jak tworzenie logowania/użytkownika z zewnętrznego dostawcy lub wykonywanie jako nazwa logowania/użytkownika. Skonfigurowanie TDE z kluczem zarządzanym przez klienta w nowym wystąpieniu zarządzanym Azure SQL może również nie funkcjonować w pewnych okolicznościach.

**Obejście**: aby zapobiec występowaniu tego problemu w wystąpieniu zarządzanym SQL przed wykonaniem jakichkolwiek poleceń aktualizacji lub w przypadku, gdy ten problem wystąpił już po poleceniach aktualizacji, przejdź do Azure Portal, dostęp do wystąpienia zarządzanego SQL [Active Directory bloku administratora](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal). Sprawdź, czy widzisz komunikat o błędzie "wystąpienie zarządzane potrzebuje nazwy głównej usługi, aby uzyskać dostęp do Azure Active Directory. Kliknij tutaj, aby utworzyć nazwę główną usługi ". Jeśli ten komunikat o błędzie został napotkany, kliknij go i postępuj zgodnie z instrukcjami krok po kroku, które są dostępne do momentu rozwiązania tego błędu.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Przywrócenie ręcznej kopii zapasowej bez sumy KONTROLnej może zakończyć się niepowodzeniem

W pewnych okolicznościach ręczne tworzenie kopii zapasowych baz danych, które zostały wykonane w wystąpieniu zarządzanym bez sumy KONTROLnej, nie może zostać przywrócone. W takich przypadkach ponów próbę przywrócenia kopii zapasowej do momentu pomyślnego zakończenia.

**Obejście**: Utwórz ręcznie kopie zapasowe baz danych w wystąpieniach zarządzanych z WŁĄCZONĄ sumą kontrolną.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent przestaje odpowiadać po zmodyfikowaniu, wyłączeniu lub włączeniu istniejących zadań

W pewnych okolicznościach modyfikowanie, wyłączanie lub Włączanie istniejącego zadania może spowodować, że Agent przestanie odpowiadać. Problem jest automatycznie zmniejszany podczas wykrywania, co spowoduje ponowne uruchomienie procesu agenta.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Uprawnienia do grupy zasobów nie są stosowane do wystąpienia zarządzanego SQL

Gdy rola platformy Azure współautora wystąpienia zarządzanego SQL jest stosowana do grupy zasobów (RG), nie jest ona stosowana do wystąpienia zarządzanego SQL i nie ma żadnego wpływu.

**Obejście**: Skonfiguruj rolę współautora wystąpienia zarządzanego SQL dla użytkowników na poziomie subskrypcji.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ograniczenie ręcznego trybu failover za pośrednictwem portalu dla grup trybu failover

Jeśli grupa trybu failover rozciąga się między wystąpieniami w różnych subskrypcjach lub grupach zasobów platformy Azure, nie można zainicjować ręcznego przełączania do trybu failover z wystąpienia podstawowego w grupie trybu failover.

**Obejście**: Zainicjuj tryb failover za pośrednictwem portalu z wystąpienia geograficznego.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL wymagają jawnych uprawnień do wykonywania w przypadku identyfikatorów logowania innych niż sysadmin

Jeśli nazwy logowania inne niż sysadmin są dodawane do dowolnych [ról bazy danych programu SQL Agent](/sql/ssms/agent/sql-server-agent-fixed-database-roles), istnieje problem, w którym jawne uprawnienia do wykonania muszą zostać przyznane głównym procedurom składowanym dla tych logowań do działania. Jeśli wystąpi ten problem, zostanie wyświetlony komunikat o błędzie "uprawnienie EXECUTE zostało odrzucone w obiekcie <object_name> (Microsoft SQL Server, błąd: 229)".

**Obejście**: po dodaniu logowań do stałej roli bazy danych programu SQL Agent (SQLAgentUserRole, SQLAgentReaderRole lub SQLAgentOperatorRole) dla każdego z nazw logowania dodanych do tych ról, wykonaj Poniższy skrypt T-SQL, aby jawnie udzielić uprawnień do wykonywania w wymienionych procedurach składowanych.

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

**(Rozwiązane w marcu 2020)** Program SQL Agent tworzy nową sesję przy każdym uruchomieniu zadania, stopniowo zwiększając użycie pamięci. Aby uniknąć przekroczenia limitu pamięci wewnętrznej, co spowoduje zablokowanie wykonywania zaplanowanych zadań, proces agenta zostanie uruchomiony ponownie, gdy użycie pamięci osiągnie wartość progową. Może to spowodować przerwanie wykonywania zadań uruchomionych w momencie ponownego uruchomienia.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity pamięci OLTP w pamięci nie są stosowane

Krytyczne dla działania firmy warstwa usługi nie będzie prawidłowo stosować [maksymalnego limitu pamięci dla obiektów zoptymalizowanych pod kątem pamięci](../managed-instance/resource-limits.md#in-memory-oltp-available-space) w niektórych przypadkach. Wystąpienie zarządzane SQL może umożliwić obciążeniu użycie większej ilości pamięci dla operacji OLTP w pamięci, co może mieć wpływ na dostępność i stabilność wystąpienia. Zapytania OLTP w pamięci, które zbliżają się do limitów, mogą kończyć się niepowodzeniem. Ten problem zostanie rozwiązany wkrótce. Zapytania, które używają więcej pamięci OLTP w pamięci, będą kończyć się niepowodzeniem, jeśli osiągnieją [limity](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Obejście**: [Monitoruj użycie magazynu OLTP w pamięci](../in-memory-oltp-monitor-space.md) za pomocą [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) , aby upewnić się, że obciążenie nie korzysta z więcej niż dostępnej pamięci. Zwiększ limity pamięci, które są zależne od liczby rdzeni wirtualnych, lub Zoptymalizuj obciążenie, aby użyć mniejszej ilości pamięci.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Podczas próby usunięcia pliku, który nie jest pusty, został zwrócony nieprawidłowy błąd

SQL Server i wystąpienie zarządzane SQL [nie zezwala użytkownikowi na porzucanie pliku, który nie jest pusty](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Próba usunięcia niepustego pliku danych przy użyciu `ALTER DATABASE REMOVE FILE` instrukcji spowoduje, że błąd `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` nie zostanie natychmiast zwrócony. Wystąpienie zarządzane SQL będzie nadal próbowało porzucić plik i operacja zakończy się niepowodzeniem po 30 minutach z `Internal server error` .

**Obejście**: Usuń zawartość pliku przy użyciu `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` polecenia. Jeśli jest to jedyny plik w grupie plików, należy usunąć dane z tabeli lub partycji skojarzonej z tą grupą plików przed zmniejszeniem pliku i opcjonalnie załadować te dane do innej tabeli lub partycji.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Zmiana warstwy usługi i tworzenie wystąpienia są blokowane przez trwającą operację przywracania bazy danych

Ciągła `RESTORE` instrukcja, proces migracji usługi migracji danych oraz wbudowane przywracanie do punktu w czasie zablokują aktualizację warstwy usług lub zmianę rozmiaru istniejącego wystąpienia, a następnie tworzy nowe wystąpienia do momentu zakończenia procesu przywracania. 

Proces przywracania spowoduje zablokowanie tych operacji na zarządzanych wystąpieniach i pulach wystąpień w tej samej podsieci, w której jest uruchomiony proces przywracania. Nie ma to żadnego oddziaływania na wystąpienia w pulach wystąpień. Operacje tworzenia lub zmiany warstwy usług będą kończyć się niepowodzeniem lub przekraczają limit czasu. Będą one działać po zakończeniu procesu przywracania lub anulowaniu.

**Obejście**: Poczekaj na zakończenie procesu przywracania lub Anuluj proces przywracania, jeśli operacja tworzenia lub aktualizowania warstwy usługi ma wyższy priorytet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po przejściu w tryb failover może zajść konieczność ponownego skonfigurowania przyrządu zasobów na Krytyczne dla działania firmyej warstwie usług

Funkcja [gubernatora zasobów](/sql/relational-databases/resource-governor/resource-governor) , która umożliwia ograniczenie zasobów przypisanych do obciążenia użytkownikami, może nieprawidłowo sklasyfikować pewne obciążenie użytkownika po przejściu w tryb failover lub zainicjowanej przez użytkownika zmianie warstwy usług (na przykład zmiana maksymalnego rozmiaru magazynu rdzeń wirtualny lub maksymalnego wystąpienia).

**Obejście**: uruchamiaj `ALTER RESOURCE GOVERNOR RECONFIGURE` okresowo lub jako część zadania programu SQL Agent, które wykonuje zadanie SQL, gdy wystąpienie zostanie uruchomione, jeśli używasz [prezesa zasobów](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Okna dialogowe Service Broker między bazami danych muszą zostać zainicjowane ponownie po uaktualnieniu warstwy usług

Okna dialogowe Service Broker między bazami danych przestaną przekazanie komunikatów do usług w innych bazach danych po operacji zmiany warstwy usług. Komunikaty nie są *tracone* i można je znaleźć w kolejce nadawcy. Wszelkie zmiany rozmiaru magazynu rdzeni wirtualnych lub wystąpienia w wystąpieniu zarządzanym SQL spowodują zmianę `service_broke_guid` wartości w widoku [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) dla wszystkich baz danych. Wszystkie `DIALOG` utworzone przy użyciu instrukcji [BEGIN dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , które odwołują się do brokerów usług w innej bazie danych, zatrzymają dostarczanie komunikatów do usługi docelowej.

**Obejście**: Zatrzymaj wszystkie działania, które używają konwersacji okna dialogowego Service Broker między bazami danych przed aktualizacją warstwy usług i ponownie zainicjuj je później. Jeśli pozostałe komunikaty są niedostarczone po zmianie warstwy usług, należy odczytać komunikaty z kolejki źródłowej i ponownie wysłać je do kolejki docelowej.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Personifikacja typów logowania usługi Azure AD nie jest obsługiwana

Personifikacja przy użyciu `EXECUTE AS USER` lub `EXECUTE AS LOGIN` z następujących podmiotów Azure Active Directory (Azure AD) nie jest obsługiwana:
-   Aliasy użytkowników usługi Azure AD. W tym przypadku zwracany jest następujący błąd: `15517` .
- Nazwy logowania i użytkownicy usługi Azure AD na podstawie aplikacji usługi Azure AD lub ich nazw głównych. W takim przypadku zwracane są następujące błędy: `15517` i `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parametr nie jest obsługiwany w sp_send_db_mail

`@query`Parametr w procedurze [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nie działa.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Należy ponownie skonfigurować replikację transakcyjną po geograficznym przejściu do trybu failover

Jeśli replikacja transakcyjna jest włączona w bazie danych w grupie autotrybu failover, administrator wystąpienia zarządzanego SQL musi oczyścić wszystkie publikacje na starym serwerze podstawowym i skonfigurować je ponownie na nowym serwerze podstawowym po przejściu w tryb failover do innego regionu. Aby uzyskać więcej informacji, zobacz [replikacja](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Nazwy logowania i użytkownicy usługi Azure AD nie są obsługiwane w programie SSDT

Narzędzia SQL Server Data Tools nie obsługują w pełni nazw logowania i użytkowników usługi Azure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tymczasowa baza danych jest używana podczas operacji przywracania

Gdy baza danych jest przywracana w wystąpieniu zarządzanym SQL, usługa Restore najpierw utworzy pustą bazę danych o podanej nazwie w celu przydzielenia nazwy do wystąpienia. Po pewnym czasie ta baza danych zostanie usunięta i zostanie uruchomiona przywracanie rzeczywistej bazy danych. 

Baza danych, która jest w stanie *przywracania* , będzie tymczasowo mieć losową wartość identyfikatora GUID, a nie nazwę. Nazwa tymczasowa zostanie zmieniona na żądaną nazwę określoną w `RESTORE` instrukcji po zakończeniu procesu przywracania. 

W fazie początkowej użytkownik może uzyskać dostęp do pustej bazy danych, a nawet utworzyć tabele lub załadować dane w tej bazie danych. Ta Tymczasowa baza danych zostanie porzucona, gdy usługa przywracania rozpocznie działanie drugiej fazy.

**Obejście**: nie należy uzyskiwać dostępu do przywracanej bazy danych, dopóki nie zobaczysz, że Przywracanie zostało zakończone.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura i zawartość bazy danych TEMPDB są odtwarzane

`tempdb`Baza danych jest zawsze podzielona na 12 plików danych i nie można zmienić jej struktury. Nie można zmienić maksymalnego rozmiaru pliku i nie można dodać do niego nowych plików `tempdb` . `Tempdb` zawsze jest tworzona jako pusta baza danych, gdy wystąpienie zostanie uruchomione lub działa w trybie failover, a wszelkie zmiany wprowadzone w programie `tempdb` nie zostaną zachowane.

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekraczanie miejsca do magazynowania z małymi plikami bazy danych

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` , i `RESTORE DATABASE` instrukcje mogą się nie powieść, ponieważ wystąpienie może osiągnąć limit magazynu platformy Azure.

Każde wystąpienie Ogólnego przeznaczenia wystąpienia zarządzanego SQL ma do 35 TB pamięci zarezerwowanej dla miejsca na dysku w warstwie Premium. Każdy plik bazy danych jest umieszczany na osobnym dysku fizycznym. Rozmiary dysków mogą być 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Nieużywane miejsce na dysku jest nieobciążone, ale całkowita suma rozmiarów dysków w warstwie Premium platformy Azure nie może przekroczyć 35 TB. W niektórych przypadkach wystąpienie zarządzane, które nie wymaga 8 TB w sumie, może przekroczyć limit 35 TB platformy Azure dla rozmiaru magazynu z powodu wewnętrznej fragmentacji.

Na przykład wystąpienie Ogólnego przeznaczenia wystąpienia zarządzanego SQL może mieć jeden duży plik o rozmiarze 1,2 TB umieszczonym na dysku o pojemności 4 TB. Może również mieć 248 plików o pojemności 1 GB, które są umieszczane na oddzielnych dyskach 128 GB. W tym przykładzie:

- Łączny rozmiar magazynu dyskowego to 1 x 4 TB + 248 x 128 GB = 35 TB.
- Całkowite zarezerwowane miejsce dla baz danych w wystąpieniu to 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

W tym przykładzie pokazano, że w pewnych okolicznościach, ze względu na określoną dystrybucję plików, wystąpienie wystąpienia zarządzanego SQL może osiągnąć limit 35-TB zarezerwowane dla dołączonego dysku Azure Premium, gdy użytkownik może nie oczekiwać.

W tym przykładzie istniejące bazy danych nadal pracują i mogą wzrosnąć bez żadnego problemu, o ile nie zostaną dodane nowe pliki. Nie można utworzyć ani przywrócić nowych baz danych, ponieważ nie ma wystarczającej ilości miejsca na nowe dyski, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiągnie limitu rozmiaru wystąpienia. Błąd zwracany w tym przypadku nie jest wyczyszczony.

[Liczbę pozostałych plików można określić](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) za pomocą widoków systemu. Jeśli osiągnięto ten limit, spróbuj [opróżnić i usunąć niektóre z mniejszych plików przy użyciu instrukcji DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełączyć się do [warstwy krytyczne dla działania firmy, która nie ma tego ograniczenia](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych

W kilku widokach systemu, licznikach wydajności, komunikatach o błędach, XEvents i dzienniku błędów są wyświetlane identyfikatory baz danych GUID zamiast rzeczywistej nazwy bazy danych. Nie należy polegać na tych identyfikatorach GUID, ponieważ są one zastępowane rzeczywistymi nazwami baz danych w przyszłości.

**Obejście**: Użyj widoku sys. databases, aby rozpoznać rzeczywistą nazwę bazy danych z nazwy fizycznej bazy danych określonej w postaci identyfikatorów baz danych GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są utrwalone

Dzienniki błędów, które są dostępne w wystąpieniu zarządzanym SQL, nie są utrwalane, a ich rozmiar nie jest uwzględniony w maksymalnym limicie magazynu. Dzienniki błędów mogą być automatycznie wymazywane w przypadku przełączenia w tryb failover. W historii dzienników błędów mogą występować luki, ponieważ wystąpienie zarządzane SQL zostało przenoszone kilka razy na kilku maszynach wirtualnych.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany

**(Rozwiązane w marcu 2020)** `TransactionScope` Klasa w programie .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w tym samym wystąpieniu w ramach tego samego zakresu transakcji:

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

**Obejście (niewymagane od marca 2020)**: Użyj [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) , aby użyć innej bazy danych w kontekście połączenia zamiast korzystać z dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP

Moduły CLR w wystąpieniu zarządzanym SQL i połączone serwery lub zapytania rozproszone, które odwołują się do bieżącego wystąpienia czasami nie mogą rozpoznać adresu IP wystąpienia lokalnego. Ten błąd jest przejściowym problemem.

**Obejście**: Jeśli to możliwe, Użyj połączeń kontekstu w module CLR.

## <a name="updates"></a>Aktualizacje

Listę aktualizacji i ulepszeń SQL Database można znaleźć w temacie [SQL Database Updates Service](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać aktualizacje i ulepszenia dla wszystkich usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby współtworzyć dokumentację usługi Azure SQL, zobacz [Przewodnik współautora](/contribute/)w witrynie docs.

---
title: Co nowego
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach dokumentacji Azure SQL Database & SQL Managed Instance.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: d0522fe2c8b6d6b623903a720e6c8e760bd6aa92
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602086"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Co nowego w programie Azure SQL Database & SQL Managed Instance?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule wymieniono Azure SQL Database i Azure SQL Managed Instance, które są obecnie dostępne w publicznej wersji zapoznawczej. Aby SQL Database i SQL Managed Instance i ulepszenia, zobacz [SQL Database & SQL Managed Instance aktualizacji usługi](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać informacje o aktualizacjach i ulepszeniach innych usług platformy Azure, zobacz [Aktualizacje usług](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Co nowego

Dokumentacja dotycząca Azure SQL Database i Azure SQL Managed Instance została podzielona na oddzielne sekcje. Zaktualizowaliśmy również sposób, w jaki odwołaliśmy się do wystąpienia zarządzanego z Azure SQL Database *do* *Azure SQL Managed Instance*.

Zrobiliśmy to, ponieważ niektóre funkcje i funkcje różnią się znacznie między pojedynczą bazą danych i wystąpieniem zarządzanym, a w poszczególnych udostępnionych artykułach coraz trudniej jest wyjaśnić złożone różnice między Azure SQL Database i Azure SQL Managed Instance.

To wyjaśnienie między różnymi produktami Azure SQL powinno uprościć i usprawnić proces pracy z aparatem bazy danych SQL Server na platformie Azure, niezależnie od tego, czy jest to pojedyncza zarządzana baza danych na platformie Azure SQL Database, w pełni zarządzane wystąpienie hostowane w wielu bazach danych na platformie Azure SQL Managed Instance, czy znany produkt lokalnie SQL Server hostowany na maszynie wirtualnej na platformie Azure.

Należy wziąć pod uwagę, że jest to praca w toku i nie każdy artykuł został jeszcze zaktualizowany. Na przykład dokumentacja instrukcji Języka Transact-SQL (T-SQL), procedur składowanych i wielu funkcji udostępnianych między językami Azure SQL Database i Azure SQL Managed Instance nie jest jeszcze ukończona, dlatego dziękujemy za cierpliwość w dalszej pracy nad wyjaśnieniem zawartości. 

Ta tabela zawiera krótkie porównanie zmian w terminologii: 


|**Nowy termin**  | **Poprzedni termin**  |**Wyjaśnienie** |
|---------|---------|---------|
|**Wystąpienie zarządzane Azure SQL** | Azure SQL Database wystąpienia *zarządzanego*| Azure SQL Managed Instance to własny produkt z rodziny Azure SQL, a nie tylko opcja wdrażania w Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database pojedynczej *bazy danych*| O ile nie określono inaczej, nazwa produktu Azure SQL Database obejmuje zarówno pojedyncze bazy danych, jak i bazy danych wdrożone w elastycznej puli. |
|**Azure SQL Database**|Azure SQL Database *elastyczna pula*| O ile nie określono inaczej, nazwa produktu Azure SQL Database obejmuje zarówno pojedyncze bazy danych, jak i bazy danych wdrożone w elastycznej puli.  |
|**Azure SQL Database** |Azure SQL Database | Mimo że termin pozostaje taki sam, obecnie ma zastosowanie tylko do wdrożeń pojedynczej bazy danych i elastycznej puli i nie obejmuje wystąpienia zarządzanego. |
| **Azure SQL**| Nie dotyczy | Dotyczy to rodziny produktów aparatu bazy SQL Server, które są dostępne na platformie Azure: Azure SQL Database, Azure SQL Managed Instance i SQL Server na SQL Server azure. | 

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Cecha | Szczegóły |
| ---| --- |
| Zadania elastycznej bazy danych (wersja zapoznawcza) | Aby uzyskać więcej informacji, [zobacz Tworzenie i konfigurowanie zadań elastycznych oraz zarządzanie nimi.](elastic-jobs-overview.md) |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [Omówienie zapytań elastycznych.](elastic-query-overview.md) |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze.](elastic-transactions-overview.md) |
| Edytor zapytań w Azure Portal |Aby uzyskać więcej informacji, [zobacz Use the Azure Portal's SQL query editor to connect and query data](connect-query-portal.md)(Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań za pomocą edytora zapytań SQL).|
|Analiza SQL|Aby uzyskać więcej informacji, [zobacz Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

| Cecha | Szczegóły |
| ---| --- |
| [Transakcje rozproszone](/azure/azure-sql/database/elastic-transactions-overview) | Transakcje rozproszone w wystąpieniach zarządzanych. |
| [Pule wystąpień](/azure/sql-database/sql-database-instance-pools) | Wygodny i opłacany sposób migrowania mniejszych wystąpień SQL do chmury. |
| [Jednostki usługi (identyfikatory logowania) serwera Azure AD na poziomie wystąpienia](/sql/t-sql/statements/create-login-transact-sql) | Utwórz identyfikatory logowania na poziomie wystąpienia przy użyciu [instrukcji CREATE LOGIN FROM EXTERNAL PROVIDER.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |
| [Replikacja transakcyjna](../managed-instance/replication-transactional-overview.md) | Replikuj zmiany z tabel do innych baz danych w SQL Managed Instance, SQL Database lub SQL Server. Można też zaktualizować tabele, gdy niektóre wiersze zostaną zmienione w innych wystąpieniach SQL Managed Instance lub SQL Server. Aby uzyskać więcej informacji, [zobacz Konfigurowanie replikacji w Azure SQL Managed Instance](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Wykrywanie zagrożeń |Aby uzyskać więcej informacji, [zobacz Konfigurowanie wykrywania zagrożeń w Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).|
| Długoterminowe przechowywanie kopii zapasowych | Aby uzyskać więcej informacji, [zobacz Konfigurowanie długoterminowego](../managed-instance/long-term-backup-retention-configure.md)przechowywania kopii zapasowej w Azure SQL Managed Instance , która jest obecnie dostępna w ograniczonej publicznej wersji zapoznawczej. |

---

## <a name="new-features"></a>Nowe funkcje

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance aktualizacji Z2. 2019 r.

- [Konfiguracja podsieci wspomaganej](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) przez usługę to bezpieczny i wygodny sposób zarządzania konfiguracją podsieci, w której kontrolujesz ruch danych, SQL Managed Instance zapewnia nieprzerwany przepływ ruchu zarządzania.
- [Transparent Data Encryption (TDE)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) z usługą Bring Your Own Key (BYOK) umożliwia korzystanie ze scenariusza byok (bring your own-key) w celu ochrony danych magazynowany i umożliwia organizacjom oddzielnie obowiązków zarządzania kluczami i danymi.
- [Grupy automatycznego trybu failover umożliwiają](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) replikowanie wszystkich baz danych z wystąpienia podstawowego do wystąpienia pomocniczego w innym regionie.
- [Globalne flagi śledzenia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) umożliwiają konfigurowanie SQL Managed Instance zachowania.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance aktualizacji Z1. 2019 r.

Następujące funkcje są włączone w SQL Managed Instance wdrażania w H1 2019:
  - Obsługa subskrypcji z comiesięcznych środków na korzystanie z platformy <a href="/azure/azure-sql/managed-instance/resource-limits">Azure Visual Studio subskrybentów</a> i zwiększone [limity regionalne.](../managed-instance/resource-limits.md#regional-resource-limitations)
  - Obsługa programu <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 i programu SharePoint 2019 oraz </a> <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> usług Dynamics 365 Business Central. </a>
  - Utwórz wystąpienie zarządzane z <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">sortowaniem na poziomie</a> wystąpienia i <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">wybranej</a> strefy czasowej.
  - Wystąpienia zarządzane są teraz chronione za pomocą [wbudowanej zapory](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Skonfiguruj SQL Managed Instance, aby używać publicznych punktów [końcowych,](../managed-instance/public-endpoint-configure.md)przesłoń połączenie serwera [proxy](connectivity-architecture.md#connection-policy) w celu uzyskania lepszej wydajności sieci, 4 rdzenie wirtualne generacji sprzętu <a href="https://aka.ms/four-cores-sql-mi-update"> generacji 5.</a> generacji lub skonfiguruj przechowywanie kopii zapasowych do <a href="/azure/azure-sql/database/automated-backups-overview">35</a> dni w przypadku przywracania do punktu w czasie. [Długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md) (do 10 lat) jest obecnie dostępne w publicznej wersji zapoznawczej.  
  - Nowe funkcje umożliwiają przywracanie geograficzne bazy danych do innego centrum danych przy użyciu programu <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell,</a>zmienianie [nazwy](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)bazy danych, [usuwanie klastra wirtualnego.](../managed-instance/virtual-cluster-delete.md)
  - Nowa wbudowana rola [współautora wystąpień](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) umożliwia rozdzielenie obowiązków z zasadami zabezpieczeń i zgodnością ze standardami przedsiębiorstwa.
  - SQL Managed Instance są dostępne w następujących regionach Azure Government (US Gov Teksas, US Gov Arizona) oraz w Chinach Północnej 2 i Chiny Wschodnie 2. Jest ona również dostępna w następujących regionach publicznych: Australia Środkowa, Australia Środkowa 2, Brazylia Południowa, Francja Południowa, Środkowe Zjednoczone Emiraty Zjednoczone, Północne Zjednoczone Emiraty Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki.

## <a name="known-issues"></a>Znane problemy

|Problem  |Data odnalezionia  |Stan  |Data rozwiązania  |
|---------|---------|---------|---------|
|[Zmiana typu połączenia nie ma wpływu na połączenia za pośrednictwem punktu końcowego grupy trybu failover](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Sty 2021 r.|Ma obejście||
|[Procedura sp_send_dbmail przejściowym niepowodzeniem, gdy @query używany jest parametr](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Sty 2021 r.|Ma obejście||
|[Transakcje rozproszone można wykonać po usunięciu wystąpienia zarządzanego z grupy zaufania serwera](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Październik 2020 r.|Ma obejście||
|[Transakcje rozproszone nie mogą być wykonywane po operacji skalowania wystąpienia zarządzanego](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Październik 2020 r.|Ma obejście||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [Zestaw OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) w Azure SQL instrukcji w wystąpieniu zarządzanym nie może używać tożsamości zarządzanej usługi `BACKUP` / `RESTORE` Azure AD do uwierzytelniania w usłudze Azure Storage|Wrz 2020 r.|Ma obejście||
|[Usługa Service Principal nie może uzyskać dostępu do usług Azure AD i AKV](#service-principal-cannot-access-azure-ad-and-akv)|Sierpień 2020 r.|Ma obejście||
|[Przywracanie ręcznej kopii zapasowej bez sumy kontrolnej może się nie powieść](#restoring-manual-backup-without-checksum-might-fail)|Maj 2020 r.|Resolved|Czerwiec 2020 r.|
|[Agent przestaje odpowiadać podczas modyfikowania, wyłączania lub włączania istniejących zadań](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maj 2020 r.|Resolved|Czerwiec 2020 r.|
|[Uprawnienia do grupy zasobów nie są stosowane do SQL Managed Instance](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Luty 2020 r.|Resolved|Listopad 2020 r.|
|[Ograniczenie ręcznego trybu failover w portalu dla grup trybu failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Sty 2020 r.|Ma obejście||
|[Role agenta SQL wymagają jawnych uprawnień do wykonywania w przypadku identyfikatorów logowania innych niż sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Grudzień 2019 r.|Ma obejście||
|[Zadania agenta SQL mogą zostać przerwane przez ponowne uruchomienie procesu agenta](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Grudzień 2019 r.|Resolved|Mar 2020 r.|
|[Identyfikatory logowania i użytkownicy usługi Azure AD nie są obsługiwane w programie SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Listopad 2019 r.|Brak obejścia||
|[Limity pamięci OLTP w pamięci nie są stosowane](#in-memory-oltp-memory-limits-are-not-applied)|Październik 2019 r.|Ma obejście||
|[Podczas próby usunięcia pliku, który nie jest pusty, jest zwracany nieprawidłowy błąd](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Październik 2019 r.|Ma obejście||
|[Operacje zmiany warstwy usługi i tworzenia wystąpień są blokowane przez trwające przywracanie bazy danych](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Wrz 2019 r.|Ma obejście||
|[Zarządca zasobów w Krytyczne dla działania firmy usługi może wymagać ponownej konfiguracji po zakończeniu pracy w awarii](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Wrz 2019 r.|Ma obejście||
|[Okna dialogowe zarządzania Service Broker między bazami danych muszą zostać ponownie zaim iniekcjowane po uaktualnieniu warstwy usługi](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Sierpień 2019 r.|Ma obejście||
|[Personifikacja typów logowania usługi Azure AD nie jest obsługiwana](#impersonation-of-azure-ad-login-types-is-not-supported)|Lipiec 2019 r.|Brak obejścia||
|[@query parametr nie jest obsługiwany w sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Kwi 2019 r.|Resolved|Sty 2021 r.|
|[Po zakończeniu geograficznego trybu failover należy ponownie skonfigurować replikację transakcyjną](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019 r.|Brak obejścia||
|[Tymczasowa baza danych jest używana podczas operacji RESTORE](#temporary-database-is-used-during-restore-operation)||Ma obejście||
|[Struktura i zawartość bazy danych TEMPDB są tworzone ponownie](#tempdb-structure-and-content-is-re-created)||Brak obejścia||
|[Przekroczenie miejsca do magazynowania przy użyciu małych plików bazy danych](#exceeding-storage-space-with-small-database-files)||Ma obejście||
|[Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych](#guid-values-shown-instead-of-database-names)||Ma obejście||
|[Dzienniki błędów nie są utrwalane](#error-logs-arent-persisted)||Brak obejścia||
|[Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ma obejście|Mar 2020 r.|
|[Moduły CLR i połączone serwery czasami nie mogą odwoływać się do lokalnego adresu IP](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ma obejście||
|Spójność bazy danych nie została zweryfikowana przy użyciu bazy danych DBCC CHECKDB po przywróceniu bazy danych z Azure Blob Storage.||Resolved|Listopad 2019 r.|
|Przywracanie bazy danych do punktu w czasie z warstwy Krytyczne dla działania firmy do warstwy Ogólnego przeznaczenia nie powiedzie się, jeśli źródłową bazę danych zawierają obiekty OLTP w pamięci.||Resolved|Październik 2019 r.|
|Funkcja poczty bazy danych z zewnętrznymi serwerami poczty (spoza platformy Azure) korzystającymi z bezpiecznego połączenia||Resolved|Październik 2019 r.|
|Zawarte bazy danych nie są obsługiwane w SQL Managed Instance||Resolved|Sierpień 2019 r.|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>Zmiana typu połączenia nie ma wpływu na połączenia za pośrednictwem punktu końcowego grupy trybu failover

Jeśli wystąpienie uczestniczy w grupie automatycznego trybu failover, [](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) zmiana typu połączenia wystąpienia nie ma wpływu na połączenia nawiązane za pośrednictwem punktu końcowego odbiornika grupy trybu [failover.](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)

**Obejście:** Porzuć i utwórz ponownie grupę automatycznego trybu failover, zmieniając typ połączenia.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Procedura sp_send_dbmail przejściowym niepowodzeniem, gdy @query używany jest parametr

Procedura sp_send_dbmail może przejściowo nie powieść się, `@query` gdy jest używany parametr. W przypadku wystąpienia tego problemu co drugie wykonanie procedury sp_send_dbmail kończy się niepowodzeniem z `Msg 22050, Level 16, State 1` błędem i komunikatem `Failed to initialize sqlcmd library with error number -2147467259` . Aby można było prawidłowo zobaczyć ten błąd, procedura powinna być wywoływana z domyślną wartością 0 dla parametru . W przeciwnym razie błąd nie `@exclude_query_output` zostanie rozpropagowany.
Ten problem jest spowodowany przez znaną usterkę związaną z tym, sp_send_dbmail korzysta z personifikacji i puli połączeń.
Aby ominąć ten problem, opakuj kod wysyłania wiadomości e-mail do logiki ponawiania, która opiera się na parametrze wyjściowym `@mailitem_id` . Jeśli wykonanie zakończy się niepowodzeniem, wartość parametru będzie mieć wartość NULL, co sp_send_dbmail powinno zostać jeszcze raz wywołane w celu pomyślnego wysłania wiadomości e-mail. Oto przykład tej logiki ponawiania prób.
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

[Grupy zaufania serwera są](../managed-instance/server-trust-group-overview.md) używane do ustanawiania relacji zaufania między wystąpieniami zarządzanymi, które są wymagane do wykonywania [transakcji rozproszonych.](./elastic-transactions-overview.md) Po usunięciu wystąpienia zarządzanego z grupy zaufania serwera lub usunięciu grupy nadal można wykonywać transakcje rozproszone. Istnieje obejście, które można zastosować, aby upewnić się, że transakcje rozproszone są wyłączone i jest to zainicjowane przez użytkownika ręczne tryb [failover](../managed-instance/user-initiated-failover.md) w wystąpieniu zarządzanym.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Po operacji skalowania wystąpienia zarządzanego nie można wykonać transakcji rozproszonych

Operacje skalowania wystąpienia zarządzanego, które obejmują zmianę warstwy usługi lub liczbę rdzeni wirtualnych, zresetuje ustawienia grupy zaufania serwera na za zaplecza i wyłączy uruchomione [transakcje rozproszone.](./elastic-transactions-overview.md) Aby obejść ten problem, usuń i utwórz nową [grupę zaufania serwera](../managed-instance/server-trust-group-overview.md) na Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT i instrukcje BACKUP/RESTORE nie mogą używać tożsamości zarządzanej do uzyskiwania dostępu do usługi Azure Storage

Instrukcje wstawiania zbiorczego, tworzenia kopii zapasowej i przywracania oraz funkcja OPENROWSET nie mogą używać funkcji z tożsamością zarządzaną `DATABASE SCOPED CREDENTIAL` do uwierzytelniania w usłudze Azure Storage. Aby obejść ten problem, przełącz się na uwierzytelnianie ZA POMOCĄ SYGNATURY DOSTĘPU WSPÓŁDZIELOWEGO. Poniższy przykład nie będzie działać na Azure SQL (zarówno w przypadku bazy danych, jak i wystąpienia zarządzanego):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Obejście:** Użyj [sygnatury dostępu współdzielonego do uwierzytelniania w magazynie](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Usługa Service Principal nie może uzyskać dostępu do usług Azure AD i AKV

W niektórych sytuacjach może wystąpić problem z jednostką usługi używaną do uzyskiwania dostępu do usługi Azure AD i Azure Key Vault (AKV). W związku z tym ten problem ma wpływ na użycie uwierzytelniania usługi Azure AD i funkcji Transparent Database Encryption (TDE) z SQL Managed Instance. Ten problem może wystąpić jako sporadyczny problem z łącznością lub nie można uruchomić instrukcji, takich jak CREATE LOGIN/USER FROM EXTERNAL PROVIDER lub EXECUTE AS LOGIN/USER. Skonfigurowanie szyfrowania TDE przy użyciu klucza zarządzanego przez klienta w nowej Azure SQL Managed Instance może również nie działać w pewnych okolicznościach.

**Obejście:** Aby zapobiec występowaniu tego problemu w usłudze SQL Managed Instance przed wykonaniem jakichkolwiek poleceń aktualizacji lub jeśli ten problem wystąpił już po wykonaniu poleceń aktualizacji, przejdź do Azure Portal, uzyskaj dostęp do SQL Managed Instance administratora usługi [Active Directory.](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal) Sprawdź, czy widzisz komunikat o błędzie "Wystąpienie zarządzane wymaga jednostki usługi w celu uzyskania dostępu do Azure Active Directory. Kliknij tutaj, aby utworzyć jednostkę usługi". W przypadku napotkania tego komunikatu o błędzie kliknij go i postępuj zgodnie z instrukcjami krok po kroku, aż ten błąd zostanie rozwiązany.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Przywracanie ręcznej kopii zapasowej bez sumy kontrolnej może się nie powieść

W pewnych okolicznościach ręczne tworzenie kopii zapasowych baz danych, które zostały wykonane w wystąpieniu zarządzanym bez sumy kontrolnej, może nie zostać przywrócone. W takich przypadkach spróbuj ponownie przywrócić kopię zapasową do momentu pomyślnego wykonania.

**Obejście:** Ręczne tworzenie kopii zapasowych baz danych w wystąpieniach zarządzanych z włączoną łączona funkcja CHECKSUM.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent przestaje odpowiadać podczas modyfikowania, wyłączania lub włączania istniejących zadań

W pewnych okolicznościach modyfikowanie, wyłączanie lub włączanie istniejącego zadania może spowodować brak reakcji agenta. Problem jest automatycznie ograniczany po wykryciu, co powoduje ponowne uruchomienie procesu agenta.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Uprawnienia do grupy zasobów, które nie zostały zastosowane do SQL Managed Instance

Gdy rola SQL Managed Instance platformy Azure jest stosowana do grupy zasobów, nie jest stosowana do grupy zasobów SQL Managed Instance nie ma żadnego efektu.

**Obejście:** Skonfiguruj rolę SQL Managed Instance współautora dla użytkowników na poziomie subskrypcji.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ograniczenie ręcznego trybu failover w portalu dla grup trybu failover

Jeśli grupa trybu failover obejmuje wystąpienia w różnych subskrypcjach platformy Azure lub grupach zasobów, ręcznego trybu failover nie można zainicjować z wystąpienia podstawowego w grupie trybu failover.

**Obejście:** Zainicjuj trybu failover za pośrednictwem portalu z wystąpienia pomocniczego geograficznego.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL wymagają jawnych uprawnień do wykonywania w przypadku identyfikatorów logowania innych niż sysadmin

Jeśli identyfikatory logowania inne niż sysadmin są dodawane do dowolnych stałych ról bazy danych programu [SQL Agent,](/sql/ssms/agent/sql-server-agent-fixed-database-roles)istnieje problem, w którym należy przyznać jawne uprawnienia EXECUTE do głównych procedur składowanych, aby te identyfikatory logowania działały. Jeśli ten problem zostanie napotkany, zostanie wyświetlony komunikat o błędzie "Odmowa uprawnień EXECUTE dla obiektu <object_name> (Microsoft SQL Server, Błąd: 229)".

**Obejście:** Po dodaniu identyfikatorów logowania do stałej roli bazy danych programu SQL Agent (SQLAgentUserRole, SQLAgentReaderRole lub SQLAgentOperatorRole) dla każdego identyfikatora logowania dodanego do tych ról wykonaj poniższy skrypt T-SQL, aby jawnie przyznać uprawnienia EXECUTE do wymienionych procedur składowanych.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Zadania agenta SQL mogą zostać przerwane przez ponowne uruchomienie procesu agenta

**(Rozwiązane w marcu 2020 r.)** Program SQL Agent tworzy nową sesję przy każdym rozpoczynaniu zadania, stopniowo zwiększając zużycie pamięci. Aby uniknąć osiągnięcia wewnętrznego limitu pamięci, co blokowałoby wykonywanie zaplanowanych zadań, proces agenta zostanie uruchomiony ponownie, gdy jego użycie pamięci osiągnie wartość progową. Może to spowodować przerwanie wykonywania zadań uruchomionych w momencie ponownego uruchomienia.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity pamięci OLTP w pamięci nie są stosowane

Warstwa Krytyczne dla działania firmy usługi w niektórych przypadkach [](../managed-instance/resource-limits.md#in-memory-oltp-available-space) nie zastosuje poprawnie maksymalnych limitów pamięci dla obiektów zoptymalizowanych pod kątem pamięci. SQL Managed Instance obciążenie może umożliwić użycie większej ilości pamięci na potrzeby operacji OLTP w pamięci, co może mieć wpływ na dostępność i stabilność wystąpienia. Zapytania OLTP w pamięci, które osiągają limity, mogą natychmiast nie powieść się. Ten problem zostanie wkrótce rozwiązany. Zapytania, które korzystają z większej ilości pamięci OLTP w pamięci, nie powiodą się wcześniej, jeśli osiągną [limity](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Obejście:** [Monitoruj użycie](../in-memory-oltp-monitor-space.md) magazynu [](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) OLTP w pamięci przy użyciu SQL Server Management Studio, aby upewnić się, że obciążenie nie używa więcej niż dostępna pamięć. Zwiększ limity pamięci, które zależą od liczby rdzeni wirtualnych, lub zoptymalizuj obciążenie, aby użyć mniejszej ilości pamięci.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Podczas próby usunięcia pliku, który nie jest pusty, jest zwracany nieprawidłowy błąd

SQL Server i SQL Managed Instance nie [zezwalają](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)użytkownikowi na upuszczenie pliku, który nie jest pusty. Jeśli spróbujesz usunąć plik danych o żadnym rozmiarze przy użyciu instrukcji , błąd nie `ALTER DATABASE REMOVE FILE` `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` zostanie natychmiast zwrócony. SQL Managed Instance będzie próbować usunąć plik, a operacja nie powiedzie się po 30 minutach z . `Internal server error`

**Obejście:** Usuń zawartość pliku za pomocą `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` polecenia . Jeśli jest to jedyny plik w grupie plików, należy usunąć dane z tabeli lub partycji skojarzonej z tą grupą plików przed zmniejszeniem pliku i opcjonalnie załadować te dane do innej tabeli/partycji.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Operacje zmiany warstwy usługi i tworzenia wystąpień są blokowane przez trwające przywracanie bazy danych

Bieżące instrukcje, proces migracji usługi Data Migration Service i wbudowane przywracanie do punktu w czasie blokują aktualizowanie warstwy usługi lub zmienianie rozmiaru istniejącego wystąpienia oraz tworzenie nowych wystąpień do momentu zakończenia procesu `RESTORE` przywracania. 

Proces przywracania zablokuje te operacje w wystąpieniach zarządzanych i pulach wystąpień w tej samej podsieci, w której jest uruchomiony proces przywracania. Nie ma to wpływu na wystąpienia w pulach wystąpień. Tworzenie lub zmienianie warstwy usługi nie powiedzie się ani nie przejdą w czasie. Będą one kontynuowane po zakończeniu lub anulowaniu procesu przywracania.

**Obejście:** Zaczekaj na zakończenie procesu przywracania lub anuluj proces przywracania, jeśli operacja tworzenia lub aktualizowania warstwy usługi ma wyższy priorytet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Zarządca zasobów w Krytyczne dla działania firmy usługi może wymagać ponownej konfiguracji po zakończeniu pracy w awarii

Funkcja [](/sql/relational-databases/resource-governor/resource-governor) zarządcy zasobów, która umożliwia ograniczenie zasobów przypisanych do obciążenia użytkownika, może niepoprawnie sklasyfikować niektóre obciążenia użytkowników po pracy w trybu failover lub zmianie warstwy usługi zainicjowanej przez użytkownika (na przykład zmiana maksymalnego rozmiaru magazynu rdzeni wirtualnych lub maksymalnego rozmiaru magazynu wystąpienia).

**Obejście:** Uruchom okresowo lub w ramach zadania agenta SQL, które wykonuje zadanie SQL podczas uruchamiania wystąpienia, jeśli `ALTER RESOURCE GOVERNOR RECONFIGURE` używasz [zarządcy zasobów.](/sql/relational-databases/resource-governor/resource-governor)

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Okna dialogowe Service Broker między bazami danych muszą zostać ponownie zaim iniekcjowane po uaktualnieniu warstwy usługi

Okna dialogowe Service Broker między bazami danych przestaną dostarczać komunikaty do usług w innych bazach danych po zmianie warstwy usługi. Komunikaty nie *zostaną utracone i* można je znaleźć w kolejce nadawcy. Każda zmiana rdzeni wirtualnych lub rozmiaru magazynu wystąpienia w SQL Managed Instance spowoduje zmianę wartości w widoku `service_broke_guid` [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) dla wszystkich baz danych. Wszelkie `DIALOG` utworzone przy użyciu instrukcji BEGIN [DIALOG,](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) która odwołuje się do brokerów usług w innej bazie danych, przestaną dostarczać komunikaty do usługi docelowej.

**Obejście:** Zatrzymaj wszystkie działania, które Service Broker między bazami danych, przed zaktualizowaniem warstwy usługi, a następnie ponownie je zaimskrybuj. Jeśli istnieją pozostałe komunikaty, które są niedostarczane po zmianie warstwy usługi, odczytaj komunikaty z kolejki źródłowej i wyślij je ponownie do kolejki docelowej.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Personifikacja typów logowania usługi Azure AD nie jest obsługiwana

Personifikacja `EXECUTE AS USER` przy użyciu lub następujących Azure Active Directory `EXECUTE AS LOGIN` (Azure AD) nie jest obsługiwana:
-   Aliasy użytkowników usługi Azure AD. W tym przypadku jest zwracany następujący błąd: `15517` .
- Identyfikatory logowania i użytkownicy usługi Azure AD oparte na aplikacjach usługi Azure AD lub nazwach głównych usługi. W tym przypadku są zwracane następujące błędy: `15517` i `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parametr nie jest obsługiwany w sp_send_db_mail

Parametr `@query` w [](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) sp_send_db_mail nie działa.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Po zakończeniu geograficznego trybu failover należy ponownie skonfigurować replikację transakcyjną

Jeśli replikacja transakcyjna jest włączona w bazie danych w grupie automatycznego trybu failover, administrator programu SQL Managed Instance musi wyczyścić wszystkie publikacje na starej bazie danych podstawowej i ponownie skonfigurować je w nowej podstawowej bazie danych po zakończeniu pracy w trybie failover w innym regionie. Aby uzyskać więcej informacji, zobacz [Replikacja](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Identyfikatory logowania i użytkownicy usługi Azure AD nie są obsługiwane w programie SSDT

SQL Server Data Tools nie obsługują w pełni identyfikatorów logowania i użytkowników usługi Azure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tymczasowa baza danych jest używana podczas operacji RESTORE

Gdy baza danych jest przywracana w SQL Managed Instance, usługa przywracania najpierw utworzy pustą bazę danych z odpowiednią nazwą, aby przydzielić nazwę w wystąpieniu. Po pewnym czasie ta baza danych zostanie porzucona i rozpocznie się przywracanie rzeczywistej bazy danych. 

Baza danych, która jest w *stanie Przywracanie,* będzie tymczasowo mieć losową wartość identyfikatora GUID zamiast nazwy. Nazwa tymczasowa zostanie zmieniona na żądaną nazwę określoną w `RESTORE` instrukcji po zakończeniu procesu przywracania. 

W początkowej fazie użytkownik może uzyskać dostęp do pustej bazy danych, a nawet tworzyć tabele lub ładować dane w tej bazie danych. Ta tymczasowa baza danych zostanie porzucona, gdy usługa przywracania rozpocznie drugą fazę.

**Obejście:** Nie należy uzyskać dostępu do przywracanych baz danych, dopóki przywracanie nie zostanie ukończone.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura i zawartość bazy danych TEMPDB jest ponownie tworzona

Baza `tempdb` danych jest zawsze dzielona na 12 plików danych i nie można zmienić struktury plików. Nie można zmienić maksymalnego rozmiaru pliku i nie można dodać nowych plików do pliku `tempdb` . `Tempdb` Element jest zawsze tworzony ponownie jako pusta baza danych podczas uruchamiania lub awarii wystąpienia, a wszelkie zmiany wprowadzone w programie `tempdb` nie zostaną zachowane.

### <a name="exceeding-storage-space-with-small-database-files"></a>Przekroczenie miejsca do magazynowania przy użyciu małych plików bazy danych

`CREATE DATABASE`Instrukcje `ALTER DATABASE ADD FILE` , i mogą się nie `RESTORE DATABASE` powieść, ponieważ wystąpienie może osiągnąć limit usługi Azure Storage.

Każde Ogólnego przeznaczenia usługi SQL Managed Instance ma do 35 TB miejsca zarezerwowanego dla usługi Azure Premium Disk. Każdy plik bazy danych jest umieszczany na oddzielnym dysku fizycznym. Rozmiary dysków to 128 GB, 256 GB, 512 GB, 1 TB lub 4 TB. Za nieużywane miejsce na dysku nie są naliczane opłaty, ale łączna suma rozmiarów dysków Azure Premium Disk nie może przekraczać 35 TB. W niektórych przypadkach wystąpienie zarządzane, które nie potrzebuje łącznie 8 TB, może przekroczyć limit rozmiaru magazynu na platformie Azure do 35 TB z powodu fragmentacji wewnętrznej.

Na przykład Ogólnego przeznaczenia serwera SQL Managed Instance może mieć jeden duży plik o rozmiarze 1,2 TB umieszczony na dysku o rozmiarze 4 TB. Może również zawierać 248 plików, z których każdy ma 1 GB i które znajdują się na oddzielnych dyskach o pojemności 128 GB. W tym przykładzie:

- Łączny przydzielony rozmiar magazynu na dysku wynosi 1 x 4 TB + 248 x 128 GB = 35 TB.
- Całkowita ilość zarezerwowanego miejsca dla baz danych w wystąpieniu wynosi 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

W tym przykładzie pokazano, że w pewnych okolicznościach ze względu na określoną dystrybucję plików wystąpienie usługi SQL Managed Instance może osiągnąć limit 35 TB zarezerwowany dla dołączonego dysku Azure Premium Disk, jeśli nie jest to możliwe.

W tym przykładzie istniejące bazy danych nadal działają i mogą rosnąć bez żadnych problemów, o ile nie dodano nowych plików. Nie można utworzyć ani przywrócić nowych baz danych, ponieważ nie ma wystarczającej ilości miejsca dla nowych dysków, nawet jeśli całkowity rozmiar wszystkich baz danych nie osiągnie limitu rozmiaru wystąpienia. Błąd, który jest zwracany w takim przypadku, nie jest oczywisty.

Liczbę [pozostałych plików można zidentyfikować przy użyciu](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) widoków systemowych. Jeśli osiągniesz ten limit, spróbuj opróżnić i usunąć niektóre mniejsze pliki przy użyciu instrukcji [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) lub przełącz się do warstwy Krytyczne dla działania firmy, która nie ma [tego limitu.](../managed-instance/resource-limits.md#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>Wyświetlane wartości identyfikatora GUID zamiast nazw baz danych

Kilka widoków systemowych, liczników wydajności, komunikatów o błędach, zdarzeń XEvent i wpisów dziennika błędów wyświetla identyfikatory bazy danych GUID zamiast rzeczywistych nazw baz danych. Nie należy polegać na tych identyfikatorach GUID, ponieważ w przyszłości zostaną one zastąpione rzeczywistymi nazwami baz danych.

**Obejście:** Użyj widoku sys.databases, aby rozpoznać rzeczywistą nazwę bazy danych na podstawie nazwy fizycznej bazy danych określonej w postaci identyfikatorów bazy danych GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Dzienniki błędów nie są utrwalane

Dzienniki błędów, które są SQL Managed Instance utrwalone, nie są utrwalane, a ich rozmiar nie jest uwzględniony w maksymalnym limitie magazynu. Dzienniki błędów mogą zostać automatycznie wymazane w przypadku wystąpienia trybu failover. W historii dziennika błędów mogą wystąpić luki, ponieważ SQL Managed Instance kilka razy zostały przeniesione na kilka maszyn wirtualnych.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Zakres transakcji w dwóch bazach danych w tym samym wystąpieniu nie jest obsługiwany

**(Rozwiązane w marcu 2020 r.)** Klasa na .NET nie działa, jeśli dwa zapytania są wysyłane do dwóch baz danych w ramach tego samego wystąpienia `TransactionScope` w tym samym zakresie transakcji:

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

Obejście (nie jest wymagane od marca **2020 r.):** Użyj funkcji [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) do używania innej bazy danych w kontekście połączenia zamiast dwóch połączeń.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduły CLR i serwery połączone czasami nie mogą odwoływać się do lokalnego adresu IP

Moduły CLR w SQL Managed Instance i serwerach połączonych lub zapytania rozproszone odwołujące się do bieżącego wystąpienia czasami nie mogą rozpoznać adresu IP wystąpienia lokalnego. Ten błąd jest przejściowym problemem.

**Obejście:** Jeśli to możliwe, używaj połączeń kontekstowych w module CLR.

## <a name="updates"></a>Aktualizacje

Aby uzyskać listę aktualizacji SQL Database i ulepszeń, [zobacz SQL Database aktualizacji usługi](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać informacje o aktualizacjach i ulepszeniach wszystkich usług platformy Azure, [zobacz Aktualizacje usług](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby współtworować dokumentację Azure SQL, zobacz [przewodnik współautora dokumentacji Docs](/contribute/).

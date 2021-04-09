---
title: Model danych dzienników Azure Monitor
description: W tym artykule dowiesz się więcej na temat Azure Monitor Log Analytics szczegóły modelu danych Azure Backup.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 004c5a6c0c2c4dcfcf13134bd5a5143ba647048f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500992"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics model danych dla Azure Backup danych

Użyj Log Analytics model danych do tworzenia niestandardowych alertów z Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> * Ten model danych znajduje się w odniesieniu do trybu Diagnostyka Azure wysyłania zdarzeń diagnostycznych do Log Analytics (LA). Aby poznać model danych dla nowego trybu specyficznego dla zasobu, można zapoznać się z następującym artykułem: [model danych dla Azure Backup zdarzeń diagnostycznych](./backup-azure-reports-data-model.md)
> * W przypadku tworzenia niestandardowych widoków raportów zaleca się używanie [funkcji systemowych na Azure monitor dziennikach](backup-reports-system-functions.md) zamiast pracy z nieprzetworzonymi tabelami wymienionymi poniżej.

## <a name="using-azure-backup-data-model"></a>Korzystanie z Azure Backup model danych

Możesz użyć następujących pól dostarczonych jako część modelu danych do tworzenia wizualizacji, niestandardowych zapytań i pulpitu nawigacyjnego zgodnie z wymaganiami.

### <a name="alert"></a>Alerty

Ta tabela zawiera szczegółowe informacje o polach związanych z alertami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |Unikatowy identyfikator wygenerowanego alertu |
| AlertType_s |Tekst |Typ alertu, na przykład kopia zapasowa |
| AlertStatus_s |Tekst |Stan alertu, na przykład aktywne |
| AlertOccurrenceDateTime_s |Data/godzina |Data i godzina utworzenia alertu |
| AlertSeverity_s |Tekst |Ważność alertu, na przykład krytyczne |
|AlertTimeToResolveInMinutes_s    | Liczba        |Czas potrzebny na rozwiązanie alertu. Puste dla aktywnych alertów.         |
|AlertConsolidationStatus_s   |Tekst         |Określ, czy alert jest skonsolidowanym alertem, czy nie         |
|CountOfAlertsConsolidated_s     |Liczba         |Liczba alertów skonsolidowanych, jeśli jest to skonsolidowany alert          |
|AlertRaisedOn_s     |Tekst         |Typ jednostki, w której jest wywoływany alert         |
|AlertCode_s     |Tekst         |Kod umożliwiający jednoznaczną identyfikację typu alertu         |
|RecommendedAction_s   |Tekst         |Akcja zalecana w celu rozwiązania alertu         |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| SchemaVersion_s |Tekst |Bieżąca wersja schematu, na przykład **v2** |
| State_s |Tekst |Bieżący stan obiektu alertu, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy służący do wykonywania kopii zapasowej, na przykład IaaSVM, FileFolder, do którego należy ten alert |
| OperationName |Tekst |Nazwa bieżącej operacji, na przykład alertu |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor. Zawsze AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedContainerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzony z alertem (został ProtectedServerUniqueId_s w wersji 1)|
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu skojarzonego z alertem |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Unikatowy identyfikator zasobu, na którym są zbierane dane. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="backupitem"></a>BackupItem

Ta tabela zawiera szczegółowe informacje dotyczące pól kopii zapasowej powiązanych z elementem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| BackupItemId_s |Tekst |Identyfikator elementu kopii zapasowej (to pole jest tylko dla schematu v1) |
| BackupItemName_s |Tekst |Nazwa elementu kopii zapasowej |
| BackupItemFriendlyName_s |Tekst |Przyjazna nazwa elementu kopii zapasowej |
| BackupItemType_s |Tekst |Typ elementu kopii zapasowej, na przykład VM, FileFolder |
| BackupItemProtectionState_s |Tekst |Stan ochrony elementu kopii zapasowej |
| BackupItemAppVersion_s |Tekst |Wersja aplikacji elementu kopii zapasowej |
| ProtectionState_s |Tekst |Bieżący stan ochrony elementu kopii zapasowej, na przykład protected, ProtectionStopped |
| ProtectionGroupName_s |Tekst | Nazwa grupy ochrony, w której jest chroniona kopia zapasowa, dla programu SC DPM i serwera usługi MAB, jeśli ma zastosowanie|
| SecondaryBackupProtectionState_s |Tekst |Czy dla elementu kopii zapasowej jest włączona ochrona pomocnicza|
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **v2** |
| State_s |Tekst |Stan obiektu elementu kopii zapasowej, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy służący do wykonywania kopii zapasowej, na przykład IaaSVM, FileFolder, do której należy ten element kopii zapasowej |
| OperationName |Tekst |Nazwa operacji, na przykład BackupItem |
| Kategoria |Tekst |Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor. Zawsze AzureBackupReport |
| Zasób |Tekst |Zasób, dla którego zbierane są dane, na przykład Recovery Services nazwa magazynu |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych, na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services) dla zbieranych danych |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services) dla zbieranych danych |
| ResourceProvider |Tekst |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia. Zawsze jest AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| State_s |Tekst |Bieżący stan obiektu skojarzenia elementu kopii zapasowej, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| BackupItemSourceSize_s |Tekst | Rozmiar frontonu elementu kopii zapasowej |
| BackupManagementServerUniqueId_s |Tekst | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypychanych do Log Analytics. Jest AzureBackupReport |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — BackupItemAssociation |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedContainerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzony z elementem kopii zapasowej (został ProtectedServerUniqueId_s w wersji 1) |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu zawierającego element kopii zapasowej |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ta tabela zawiera szczegółowe informacje o skojarzeniach elementów kopii zapasowej z różnymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
|BackupManagementServerName_s     |Tekst         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Tekst         |Wersja agenta Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Tekst         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s    |Tekst            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Tekst         |Typ serwera zarządzania kopiami zapasowymi, jako serwera usługi MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Pole do unikatowego identyfikowania serwera zarządzania kopiami zapasowymi       |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów zbieranych danych, na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu zbieranych danych, na przykład magazyny |

### <a name="job"></a>Zadanie

Ta tabela zawiera szczegółowe informacje o polach związanych z zadaniami.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |Nazwa zdarzenia. Zawsze AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator elementu kopii zapasowej |
| SchemaVersion_s |Tekst |Wersja schematu, na przykład **v2** |
| State_s |Tekst |Bieżący stan obiektu zadania, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — zadanie |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator chronionego serwera skojarzonego z zadaniem |
| ProtectedContainerUniqueId_s |Tekst | Unikatowy identyfikator identyfikujący chroniony kontener, w którym jest uruchamiane zadanie |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator chronionego magazynu |
| JobOperation_s |Tekst |Operacja, dla której zadanie jest uruchamiane na przykład kopia zapasowa, przywracanie, konfigurowanie kopii zapasowej |
| JobStatus_s |Tekst |Stan ukończonego zadania, na przykład ukończone, zakończone niepowodzeniem |
| JobFailureCode_s |Tekst |Ciąg kodu błędu z powodu niepowodzenia zadania |
| JobStartDateTime_s |Data/godzina |Data i godzina uruchomienia zadania |
| BackupStorageDestination_s |Tekst |Miejsce docelowe magazynu kopii zapasowych, na przykład Chmura, dysk  |
| AdHocOrScheduledJob_s |Tekst | Pole, aby określić, czy zadanie jest w trybie ad hoc, czy zaplanowane |
| JobDurationInSecs_s | Liczba |Łączny czas trwania zadania w sekundach |
| DataTransferredInMB_s | Liczba |Dane transferowane w MB dla tego zadania|
| JobUniqueId_g |Tekst |Unikatowy identyfikator identyfikujący zadanie |
| RecoveryJobDestination_s |Tekst | Miejsce docelowe zadania odzyskiwania, w którym dane są odzyskiwane |
| RecoveryJobRPDateTime_s |DateTime | Data, godzina utworzenia odzyskiwanego punktu odzyskiwania |
| RecoveryJobRPLocation_s |Tekst | Lokalizacja, w której zapisano odzyskiwany punkt odzyskiwania|
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services|
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="policy"></a>Zasady

Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z zasadami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Tekst ||To pole reprezentuje nazwę tego zdarzenia. Zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| State_s |Tekst ||Bieżący stan obiektu zasad, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst ||To pole reprezentuje nazwę bieżącej operacji — zasady |
| Kategoria |Tekst ||To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Jest AzureBackupReport |
| Zasób |Tekst ||Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| PolicyUniqueId_g |Tekst ||Unikatowy identyfikator identyfikujący zasady |
| PolicyName_s |Tekst ||Nazwa zdefiniowanych zasad |
| BackupFrequency_s |Tekst ||Częstotliwość, z jaką są uruchamiane kopie zapasowe, na przykład codziennie, co tydzień |
| BackupTimes_s |Tekst ||Data i godzina zaplanowanego tworzenia kopii zapasowych |
| BackupDaysOfTheWeek_s |Tekst ||Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych |
| RetentionDuration_s |Liczbowy całkowity ||Czas przechowywania skonfigurowanych kopii zapasowych |
| DailyRetentionDuration_s |Liczbowy całkowity ||Łączny czas przechowywania w dniach dla skonfigurowanych kopii zapasowych |
| DailyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania codziennego przechowywania |
| WeeklyRetentionDuration_s |Liczba dziesiętna ||Łączny czas trwania przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania przechowywania tygodniowego |
| WeeklyRetentionDaysOfTheWeek_s |Tekst ||Dni tygodnia wybrane do przechowywania tygodniowego |
| MonthlyRetentionDuration_s |Liczba dziesiętna ||Łączny czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych |
| MonthlyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania miesięcznego przechowywania |
| MonthlyRetentionFormat_s |Tekst ||Typ konfiguracji do przechowywania miesięcznego, na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień |
| MonthlyRetentionDaysOfTheWeek_s |Tekst ||Dni tygodnia wybrane do przechowywania miesięcznie |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst ||Tygodnie miesiąca, w którym jest skonfigurowane przechowywanie miesięczne, na przykład First, Last |
| YearlyRetentionDuration_s |Liczba dziesiętna ||Łączny czas przechowywania w latach dla skonfigurowanych kopii zapasowych |
| YearlyRetentionTimes_s |Tekst ||Data i godzina skonfigurowania okresu przechowywania rocznego |
| YearlyRetentionMonthsOfTheYear_s |Tekst ||Miesiąc roku wybranego do przechowywania rocznego |
| YearlyRetentionFormat_s |Tekst ||Typ konfiguracji do przechowywania rocznego, na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień | |
| YearlyRetentionDaysOfTheMonth_s |Tekst ||Daty w miesiącu wybrane do przechowywania rocznego |
| SynchronisationFrequencyPerDay_s |Liczbowy całkowity |v2|Liczba przypadków synchronizacji kopii zapasowej plików dla programu SC DPM i serwera usługi MAB |
| DiffBackupFormat_s |Tekst |v2|Format różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure |
| DiffBackupTime_s |Godzina |v2|Czas różnicowych kopii zapasowych bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| DiffBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| LogBackupFrequency_s |Liczba dziesiętna |v2|Częstotliwość tworzenia kopii zapasowych dzienników dla bazy danych SQL|
| LogBackupRetentionDuration_s |Liczba dziesiętna |v2|Czas przechowywania kopii zapasowych dziennika dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure|
| DiffBackupDaysofTheWeek_s |Tekst |v2|Dni tygodnia różnicowych kopii zapasowych dla bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure|
| SourceSystem |Tekst ||System źródłowy bieżących danych — Azure |
| ResourceId |Tekst ||Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst ||Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst ||Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst ||Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="policyassociation"></a>PolicyAssociation

Ta tabela zawiera szczegółowe informacje o skojarzeniach zasad z różnymi jednostkami.

| Pole | Typ danych | Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| EventName_s |Tekst ||To pole reprezentuje nazwę tego zdarzenia. Zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| State_s |Tekst ||Bieżący stan obiektu zasad, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst ||Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst ||To pole reprezentuje nazwę bieżącej operacji — PolicyAssociation |
| Kategoria |Tekst ||To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Jest AzureBackupReport |
| Zasób |Tekst ||Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| PolicyUniqueId_g |Tekst ||Unikatowy identyfikator identyfikujący zasady |
| VaultUniqueId_s |Tekst ||Unikatowy identyfikator magazynu, do którego należy ta zasada |
| BackupManagementServerUniqueId_s |Tekst |v2 |Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby        |
| SourceSystem |Tekst ||System źródłowy bieżących danych — Azure |
| ResourceId |Tekst ||Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst ||Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst ||Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst ||Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst ||Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="protected-container"></a>Chroniony kontener

Ta tabela zawiera podstawowe pola dotyczące chronionych kontenerów. (Was ProtectedServer w wersji 1)

| Pole | Typ danych | Opis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Tekst | Pole do unikatowego identyfikowania chronionego kontenera |
| ProtectedContainerOSType_s |Tekst |Typ systemu operacyjnego chronionego kontenera |
| ProtectedContainerOSVersion_s |Tekst |Wersja systemu operacyjnego chronionego kontenera |
| AgentVersion_s |Tekst |Numer wersji kopii zapasowej agenta lub agenta ochrony (w przypadku oprogramowania SC DPM i serwera usługi MAB) |
| BackupManagementType_s |Tekst |Typ dostawcy służący do wykonywania kopii zapasowej. Na przykład IaaSVM, FileFolder |
| EntityState_s |Tekst |Bieżący stan obiektu chronionego serwera. Na przykład aktywne, usunięte |
| ProtectedContainerFriendlyName_s |Tekst |Przyjazna nazwa serwera chronionego |
| ProtectedContainerName_s |Tekst |Nazwa chronionego kontenera |
| ProtectedContainerWorkloadType_s |Tekst |Utworzono kopię zapasową typu chronionego kontenera. Na przykład IaaSVMContainer |
| ProtectedContainerLocation_s |Tekst |Czy chroniony kontener znajduje się lokalnie, czy na platformie Azure |
| ProtectedContainerType_s |Tekst |Czy chroniony kontener jest serwerem lub kontenerem |
| ProtectedContainerProtectionState_s "  |Tekst |Stan ochrony chronionego kontenera |

### <a name="storage"></a>Storage

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| CloudStorageInBytes_s |Liczba dziesiętna |Magazyn kopii zapasowych w chmurze używany przez kopie zapasowe, obliczony na podstawie najnowszej wartości (to pole jest tylko dla schematu v1)|
| ProtectedInstances_s |Liczba dziesiętna |Liczba chronionych wystąpień używanych do obliczenia magazynu frontonu w ramach rozliczeń obliczonych na podstawie najnowszej wartości |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia. Zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład aktywny, usunięty |
| BackupManagementType_s |Tekst |Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącej operacji — Storage |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| ProtectedServerUniqueId_s |Tekst |Unikatowy identyfikator serwera chronionego, dla którego jest naliczany magazyn |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu dla magazynu jest obliczany |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| StorageType_s |Tekst |Typ magazynu, na przykład Chmura, wolumin, dysk |
| StorageName_s |Tekst |Nazwa jednostki magazynu, na przykład E:\ |
| StorageTotalSizeInGBs_s |Tekst |Łączny rozmiar magazynu (w GB) zużyty przez jednostkę magazynu|

### <a name="storageassociation"></a>StorageAssociation

Ta tabela zawiera podstawowe pola powiązane z magazynem łączące magazyn z innymi jednostkami.

| Pole | Typ danych | Opis |
| --- | --- |  --- |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej powiązanego z jednostką magazynu |
| BackupManagementServerUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi powiązanego z jednostką magazynową|
| VaultUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania magazynu związanego z jednostką magazynu|
| StorageConsumedInMBs_s |Liczba|Rozmiar magazynu zużywanego przez odpowiedni element kopii zapasowej w odpowiednim magazynie |
| StorageAllocatedInMBs_s |Liczba |Rozmiar magazynu przydzielonego przez odpowiedni element kopii zapasowej w odpowiednim magazynie typu dysk|

### <a name="vault"></a>Magazyn

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynem.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| EventName_s |Tekst |To pole reprezentuje nazwę tego zdarzenia. Zawsze jest AzureBackupCentralReport |
| SchemaVersion_s |Tekst |To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| State_s |Tekst |Bieżący stan obiektu magazynu, na przykład aktywny, usunięty |
| OperationName |Tekst |To pole reprezentuje nazwę bieżącego magazynu operacji |
| Kategoria |Tekst |To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Jest AzureBackupReport |
| Zasób |Tekst |Jest to zasób, dla którego zbierane są dane, zawiera Recovery Services nazwa magazynu |
| VaultUniqueId_s |Tekst |Unikatowy identyfikator magazynu |
| VaultName_s |Tekst |Nazwa magazynu |
| AzureDataCenter_s |Tekst |Centrum danych, w którym znajduje się magazyn |
| StorageReplicationType_s |Tekst |Typ replikacji magazynu dla magazynu, na przykład geonadmiarowości |
| SourceSystem |Tekst |System źródłowy bieżących danych — Azure |
| ResourceId |Tekst |Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| SubscriptionId |Tekst |Identyfikator subskrypcji zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceGroup |Tekst |Grupa zasobów zasobu (np. Magazyn Recovery Services), dla którego zbierane są dane |
| ResourceProvider |Tekst |Dostawca zasobów, dla którego zbierane są dane. Na przykład Microsoft. RecoveryServices |
| ResourceType |Tekst |Typ zasobu, dla którego zbierane są dane. Na przykład magazyny |

### <a name="backup-management-server"></a>Serwer zarządzania kopiami zapasowymi

Ta tabela zawiera podstawowe pola dotyczące serwerów zarządzania kopiami zapasowymi.

|Pole  |Typ danych  | Opis  |
|---------|---------|----------|
|BackupManagementServerName_s     |Tekst         |Nazwa serwera zarządzania kopiami zapasowymi        |
|AzureBackupAgentVersion_s     |Tekst         |Wersja agenta Azure Backup na serwerze zarządzania kopiami zapasowymi          |
|BackupManagementServerVersion_s     |Tekst         |Wersja serwera zarządzania kopiami zapasowymi|
|BackupManagementServerOSVersion_s     |Tekst            |Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi|
|BackupManagementServerType_s     |Tekst         |Typ serwera zarządzania kopiami zapasowymi, jako serwera usługi MAB, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Pole do unikatowego identyfikowania serwera zarządzania kopiami zapasowymi       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

W tej tabeli określono obciążenia, z którymi skojarzony jest wolumin.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| StorageUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania jednostki magazynu |
| BackupItemType_s |Tekst |Obciążenia, dla których ten wolumin jest preferowanym magazynem|

### <a name="protectedinstance"></a>ProtectedInstance

Ta tabela zawiera podstawowe pola powiązane z chronionymi wystąpieniami.

| Pole | Typ danych |Odpowiednie wersje | Opis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Tekst |v2|Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych, których kopia zapasowa jest wykonywana za pomocą programu DPM, serwera usługi MAB|
| ProtectedContainerUniqueId_s |Tekst |v2|Unikatowy identyfikator używany do identyfikowania chronionego kontenera dla wszystkiego z wyjątkiem maszyn wirtualnych, których kopia zapasowa została utworzona przy użyciu programu DPM, serwera usługi MAB|
| ProtectedInstanceCount_s |Tekst |v2|Liczba chronionych wystąpień dla skojarzonego elementu kopii zapasowej lub chronionego kontenera w tej dacie i godzinie|

### <a name="recoverypoint"></a>RecoveryPoint

Ta tabela zawiera podstawowe pola związane z punktem odzyskiwania.

| Pole | Typ danych | Opis |
| --- | --- | --- |
| BackupItemUniqueId_s |Tekst |Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych, których kopia zapasowa jest wykonywana za pomocą programu DPM, serwera usługi MAB|
| OldestRecoveryPointTime_s |Tekst |Data i godzina najstarszego punktu odzyskiwania dla elementu kopii zapasowej|
| OldestRecoveryPointLocation_s |Tekst |Lokalizacja najstarszego punktu odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointTime_s |Tekst |Data i godzina ostatniego punktu odzyskiwania dla elementu kopii zapasowej|
| LatestRecoveryPointLocation_s |Tekst |Lokalizacja najnowszego punktu odzyskiwania dla elementu kopii zapasowej|

## <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Poniżej przedstawiono kilka przykładów ułatwiających pisanie zapytań dotyczących danych Azure Backup, które znajdują się w tabeli Diagnostyka Azure:

- Wszystkie zadania tworzenia kopii zapasowej zakończone powodzeniem

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Wszystkie zadania tworzenia kopii zapasowej zakończone niepowodzeniem

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej dziennika SQL

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Wszystkie pomyślne zadania agenta Azure Backup

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

## <a name="v1-schema-vs-v2-schema"></a>Schemat wersji V1 schematu vs v2

Wcześniej dane diagnostyczne dla agenta Azure Backup i kopii zapasowej maszyny wirtualnej platformy Azure zostały wysłane do tabeli Diagnostyka Azure w schemacie określonym jako ***V1 Schema** _. Następnie dodano nowe kolumny do obsługi innych scenariuszy i obciążeń, a dane diagnostyczne zostały wypchnięte w nowym schemacie, o ile w _schemacie _ * v2 schemat_* *.  

Ze względu na zgodność z poprzednimi wersjami dane diagnostyczne dla agenta Azure Backup i kopii zapasowej maszyny wirtualnej platformy Azure są obecnie wysyłane do Diagnostyka Azure tabeli w schemacie V1 i v2 (z teraz schematem w wersji 1). Istnieje możliwość zidentyfikowania, które rekordy w Log Analytics znajdują się w schemacie V1 przez filtrowanie rekordów dla SchemaVersion_s = = "v1" w zapytaniach dziennika.

Zapoznaj się z trzecią kolumną "Description" w [modelu danych](#using-azure-backup-data-model) opisanym powyżej, aby określić, które kolumny należą do schematu tylko w wersji 1.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Modyfikowanie zapytań do korzystania ze schematu v2

Ponieważ schemat V1 znajduje się w ścieżce przestarzałej, zaleca się używanie tylko schematu v2 we wszystkich niestandardowych zapytaniach dotyczących Azure Backup danych diagnostycznych. Poniżej znajduje się przykład sposobu aktualizowania zapytań w celu usunięcia zależności od schematu V1:

1. Ustal, czy zapytanie używa dowolnego pola, które ma zastosowanie tylko do schematu v1. Załóżmy, że masz zapytanie, aby wyświetlić listę wszystkich elementów kopii zapasowej i skojarzonych z nimi serwerów w następujący sposób:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    Powyższe zapytanie używa pola ProtectedServerUniqueId_s, które ma zastosowanie tylko do schematu v1. W tym polu ProtectedContainerUniqueId_s znajduje się odpowiednik schematu w wersji 2 (patrz tabele powyżej). Pole BackupItemUniqueId_s ma zastosowanie nawet w schemacie 2, a to samo pole może być używane w tym zapytaniu.

2. Zaktualizuj zapytanie, aby użyć nazw pól schematu w wersji 2. Zaleca się użycie filtru, **gdzie SchemaVersion_s = = "v2"** we wszystkich zapytaniach, tak aby tylko rekordy odpowiadające schematowi v2 były analizowane przez zapytanie:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu modelu danych możesz rozpocząć [Tworzenie niestandardowych zapytań](../azure-monitor/visualize/tutorial-logs-dashboards.md) w dziennikach Azure monitor, aby utworzyć własny pulpit nawigacyjny.

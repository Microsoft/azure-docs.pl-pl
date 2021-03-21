---
title: Model danych dla zdarzeń diagnostyki Azure Backup
description: Ten model danych znajduje się w odniesieniu do trybu specyficznego dla zasobu, który wysyła zdarzenia diagnostyczne do Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499598"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Model danych dla zdarzeń diagnostyki Azure Backup

> [!NOTE]
>
> W przypadku tworzenia niestandardowych widoków raportów zaleca się używanie [funkcji systemowych na Azure monitor dziennikach](backup-reports-system-functions.md) zamiast pracy z nieprzetworzonymi tabelami wymienionymi poniżej.

## <a name="coreazurebackup"></a>CoreAzureBackup

Ta tabela zawiera informacje o podstawowych jednostkach kopii zapasowych, takich jak magazyny i elementy kopii zapasowej.

| **Pole**                         | **Typ danych** | **Opis**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Tekst          | Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services. |
| OperationName                     | Tekst          | To pole reprezentuje nazwę bieżącej operacji — BackupItem, BackupItemAssociation lub ProtectedContainer. |
| Kategoria                          | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor. Na przykład CoreAzureBackup. |
| AgentVersion                      | Tekst          | Numer wersji kopii zapasowej agenta lub agenta ochrony (w przypadku oprogramowania SC DPM i serwera usługi MAB) |
| AzureBackupAgentVersion           | Tekst          | Wersja agenta Azure Backup na serwerze zarządzania kopiami zapasowymi |
| AzureDataCenter                   | Tekst          | Centrum danych, w którym znajduje się magazyn                       |
| BackupItemAppVersion              | Tekst          | Wersja aplikacji elementu kopii zapasowej                       |
| BackupItemFriendlyName            | Tekst          | Przyjazna nazwa elementu kopii zapasowej                             |
| BackupItemName                    | Tekst          | Nazwa elementu kopii zapasowej                                      |
| BackupItemProtectionState         | Tekst          | Stan ochrony elementu kopii zapasowej                          |
| BackupItemFrontEndSize            | Tekst          | Rozmiar frontonu elementu kopii zapasowej                            |
| BackupItemType                    | Tekst          | Typ elementu kopii zapasowej. Na przykład: VM, FileFolder             |
| BackupItemUniqueId                | Tekst          | Unikatowy identyfikator elementu kopii zapasowej                         |
| BackupManagementServerType        | Tekst          | Typ serwera zarządzania kopiami zapasowymi, jak w serwera usługi MAB, SC DPM     |
| BackupManagementServerUniqueId    | Tekst          | Pole do unikatowego identyfikowania serwera zarządzania kopiami zapasowymi      |
| BackupManagementType              | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| BackupManagementServerName        | Tekst          | Nazwa serwera zarządzania kopiami zapasowymi                         |
| BackupManagementServerOSVersion   | Tekst          | Wersja systemu operacyjnego serwera zarządzania kopiami zapasowymi                   |
| BackupManagementServerVersion     | Tekst          | Wersja serwera zarządzania kopiami zapasowymi                      |
| LatestRecoveryPointLocation       | Tekst          | Lokalizacja najnowszego punktu odzyskiwania dla elementu kopii zapasowej    |
| LatestRecoveryPointTime           | DateTime      | Data i godzina ostatniego punktu odzyskiwania dla elementu kopii zapasowej   |
| OldestRecoveryPointLocation       | Tekst          | Lokalizacja najstarszego punktu odzyskiwania dla elementu kopii zapasowej    |
| OldestRecoveryPointTime           | DateTime      | Data i godzina ostatniego punktu odzyskiwania dla elementu kopii zapasowej   |
| PolicyUniqueId                    | Tekst          | Unikatowy identyfikator identyfikujący zasady                             |
| ProtectedContainerFriendlyName    | Tekst          | Przyjazna nazwa serwera chronionego                        |
| ProtectedContainerLocation        | Tekst          | Czy chroniony kontener znajduje się lokalnie, czy na platformie Azure |
| ProtectedContainerName            | Tekst          | Nazwa chronionego kontenera                            |
| ProtectedContainerOSType          | Tekst          | Typ systemu operacyjnego chronionego kontenera                          |
| ProtectedContainerOSVersion       | Tekst          | Wersja systemu operacyjnego chronionego kontenera                        |
| ProtectedContainerProtectionState | Tekst          | Stan ochrony chronionego kontenera                  |
| ProtectedContainerType            | Tekst          | Czy chroniony kontener jest serwerem lub kontenerem  |
| ProtectedContainerUniqueId        | Tekst          | Unikatowy identyfikator używany do identyfikowania chronionego kontenera dla wszystkiego z wyjątkiem maszyn wirtualnych, których kopia zapasowa została utworzona przy użyciu programu DPM, serwera usługi MAB |
| ProtectedContainerWorkloadType    | Tekst          | Utworzono kopię zapasową typu chronionego kontenera. Na przykład IaaSVMContainer |
| ProtectionGroupName               | Tekst          | Nazwa grupy ochrony, w której jest chroniona kopia zapasowa, dla programu SC DPM i serwera usługi MAB, jeśli ma zastosowanie |
| ResourceGroupName                 | Tekst          | Grupa zasobów zasobu (na przykład magazyn Recovery Services) dla zbieranych danych |
| SchemaVersion                     | Tekst          | To pole oznacza bieżącą wersję schematu. Jest to **wersja 2** |
| SecondaryBackupProtectionState    | Tekst          | Czy dla elementu kopii zapasowej jest włączona ochrona pomocnicza  |
| Stan                             | Tekst          | Stan obiektu elementu kopii zapasowej. Na przykład aktywne, usunięte |
| StorageReplicationType            | Tekst          | Typ replikacji magazynu dla magazynu. Na przykład nadmiarowy |
| SubscriptionId                    | Tekst          | Identyfikator subskrypcji zasobu (na przykład magazyn Recovery Services), dla którego zbierane są dane |
| VaultName                         | Tekst          | Nazwa magazynu                                            |
| VaultTags                         | Tekst          | Tagi skojarzone z zasobem magazynu                    |
| VaultUniqueId                     | Tekst          | Unikatowy identyfikator magazynu                             |
| SourceSystem                      | Tekst          | System źródłowy bieżących danych — Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Ta tabela zawiera szczegółowe informacje o polach związanych z alertami.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unikatowy identyfikator zasobu, na którym są zbierane dane. Na przykład identyfikator zasobu magazynu Recovery Services |
| OperationName                  | Tekst          | Nazwa bieżącej operacji. Na przykład alert            |
| Kategoria                       | Tekst          | Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor — AddonAzureBackupAlerts |
| AlertCode                      | Tekst          | Kod umożliwiający jednoznaczną identyfikację typu alertu                     |
| AlertConsolidationStatus       | Tekst          | Określ, czy alert jest skonsolidowanym alertem, czy nie         |
| AlertOccurrenceDateTime        | DateTime      | Data i godzina utworzenia alertu                     |
| AlertRaisedOn                  | Tekst          | Typ jednostki, w której jest wywoływany alert                        |
| AlertSeverity                  | Tekst          | Ważność alertu. Na przykład krytyczne                 |
| AlertStatus                    | Tekst          | Stan alertu. Na przykład aktywne                     |
| AlertTimeToResolveInMinutes    | Liczba        | Czas potrzebny na rozwiązanie alertu. Puste dla aktywnych alertów.     |
| AlertType                      | Tekst          | Typ alertu. Na przykład kopia zapasowa                           |
| AlertUniqueId                  | Tekst          | Unikatowy identyfikator wygenerowanego alertu                    |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator elementu kopii zapasowej skojarzonego z alertem |
| BackupManagementServerUniqueId | Tekst          | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| BackupManagementType           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| CountOfAlertsConsolidated      | Liczba        | Liczba alertów skonsolidowanych, jeśli jest to skonsolidowany alert  |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego serwera skojarzony z alertem |
| RecommendedAction              | Tekst          | Akcja zalecana w celu rozwiązania alertu                      |
| SchemaVersion                  | Tekst          | Bieżąca wersja schematu, na przykład **v2**            |
| Stan                          | Tekst          | Bieżący stan obiektu alertu, na przykład aktywny, usunięty |
| StorageUniqueId                | Tekst          | Unikatowy identyfikator używany do identyfikowania jednostki magazynu                |
| VaultUniqueId                  | Tekst          | Unikatowy identyfikator używany do identyfikowania magazynu związanego z alertem    |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Ta tabela zawiera podstawowe pola powiązane z chronionymi wystąpieniami.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Unikatowy identyfikator zasobu, na którym są zbierane dane. Na przykład identyfikator zasobu magazynu Recovery Services |
| OperationName                  | Tekst          | Nazwa operacji, na przykład ProtectedInstance         |
| Kategoria                       | Tekst          | Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor — AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator elementu kopii zapasowej                                 |
| BackupManagementServerUniqueId | Tekst          | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| BackupManagementType           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej, na przykład IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator identyfikujący chroniony kontener, w którym jest uruchamiane zadanie |
| ProtectedInstanceCount         | Tekst          | Liczba chronionych wystąpień dla skojarzonego elementu kopii zapasowej lub chronionego kontenera w tej dacie i godzinie |
| SchemaVersion                  | Tekst          | Bieżąca wersja schematu, na przykład **v2**            |
| Stan                          | Tekst          | Stan obiektu elementu kopii zapasowej, na przykład aktywny, usunięty |
| VaultUniqueId                  | Tekst          | Unikatowy identyfikator chronionego magazynu skojarzonego z chronionym wystąpieniem |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Ta tabela zawiera szczegółowe informacje o polach związanych z zadaniami.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| OperationName                  | Tekst          | To pole reprezentuje nazwę bieżącej operacji — zadanie    |
| Kategoria                       | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor — AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Tekst          | Pole, aby określić, czy zadanie jest w trybie ad hoc, czy zaplanowane           |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej powiązanego z jednostką magazynu |
| BackupManagementServerUniqueId | Tekst          | Unikatowy identyfikator używany do identyfikowania serwera zarządzania kopiami zapasowymi powiązanego z jednostką magazynową |
| BackupManagementType           | Tekst          | Typ dostawcy służący do wykonywania kopii zapasowej, na przykład IaaSVM, FileFolder, do którego należy to zadanie |
| DataTransferredInMB            | Liczba        | Dane transferowane w MB dla tego zadania                          |
| JobDurationInSecs              | Liczba        | Łączny czas trwania zadania w sekundach                                |
| JobFailureCode                 | Tekst          | Ciąg kodu błędu z powodu niepowodzenia zadania    |
| JobOperation                   | Tekst          | Operacja, dla której zadanie jest uruchamiane na przykład kopia zapasowa, przywracanie, konfigurowanie kopii zapasowej |
| JobOperationSubType            | Tekst          | Podtyp operacji zadania. Na przykład "log" w przypadku zadania tworzenia kopii zapasowej dziennika |
| JobStartDateTime               | DateTime      | Data i godzina uruchomienia zadania                       |
| JobStatus                      | Tekst          | Stan ukończonego zadania, na przykład ukończone, zakończone niepowodzeniem   |
| JobUniqueId                    | Tekst          | Unikatowy identyfikator identyfikujący zadanie                                |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego serwera skojarzonego z zadaniem |
| RecoveryJobDestination         | Tekst          | Miejsce docelowe zadania odzyskiwania, w którym dane są odzyskiwane   |
| RecoveryJobRPDateTime          | DateTime      | Data, godzina utworzenia odzyskiwanego punktu odzyskiwania |
| RecoveryJobLocation            | Tekst          | Lokalizacja, w której zapisano odzyskiwany punkt odzyskiwania |
| RecoveryLocationType           | Tekst          | Typ lokalizacji odzyskiwania                                |
| SchemaVersion                  | Tekst          | Bieżąca wersja schematu, na przykład **v2**            |
| Stan                          | Tekst          | Bieżący stan obiektu zadania, na przykład aktywny, usunięty |
| VaultUniqueId                  | Tekst          | Unikatowy identyfikator chronionego magazynu skojarzonego z zadaniem |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Ta tabela zawiera szczegółowe informacje dotyczące pól związanych z zasadami.

| **Pole**                       | **Typ danych**  | **Opis**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Tekst           | Unikatowy identyfikator zasobu, na którym są zbierane dane. Na przykład identyfikator zasobu magazynu Recovery Services |
| OperationName                   | Tekst           | Nazwa operacji, na przykład Policy lub PolicyAssociation |
| Kategoria                        | Tekst           | Kategoria danych diagnostycznych wypychanych do dzienników Azure Monitor — AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Tekst           | Dni tygodnia, w których zaplanowano tworzenie kopii zapasowych            |
| BackupFrequency                 | Tekst           | Częstotliwość uruchamiania kopii zapasowych. Na przykład codziennie, co tydzień |
| BackupManagementType            | Tekst           | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Tekst           | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| Przestoje                     | Tekst           | Data i godzina zaplanowanego tworzenia kopii zapasowych                     |
| DailyRetentionDuration          | Liczbowy całkowity   | Łączny czas przechowywania w dniach dla skonfigurowanych kopii zapasowych      |
| DailyRetentionTimes             | Tekst           | Data i godzina skonfigurowania codziennego przechowywania            |
| DiffBackupDaysOfTheWeek         | Tekst           | Dni tygodnia różnicowych kopii zapasowych dla bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure |
| DiffBackupFormat                | Tekst           | Format różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure   |
| DiffBackupRetentionDuration     | Liczba dziesiętna | Czas przechowywania różnicowych kopii zapasowych dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure |
| DiffBackupTime                  | Godzina           | Czas różnicowych kopii zapasowych bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure     |
| LogBackupFrequency              | Liczba dziesiętna | Częstotliwość tworzenia kopii zapasowych dzienników dla bazy danych SQL                            |
| LogBackupRetentionDuration      | Liczba dziesiętna | Czas przechowywania kopii zapasowych dziennika dla bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure |
| MonthlyRetentionDaysOfTheMonth  | Tekst           | Tygodnie miesiąca w przypadku skonfigurowania miesięcznego przechowywania.  Na przykład, First, Last |
| MonthlyRetentionDaysOfTheWeek   | Tekst           | Dni tygodnia wybrane do przechowywania miesięcznie              |
| MonthlyRetentionDuration        | Tekst           | Łączny czas przechowywania w miesiącach dla skonfigurowanych kopii zapasowych    |
| MonthlyRetentionFormat          | Tekst           | Typ konfiguracji do przechowywania miesięcznie. Na przykład dziennie w oparciu o dzień, co tydzień w oparciu o tydzień |
| MonthlyRetentionTimes           | Tekst           | Data i godzina skonfigurowania miesięcznego przechowywania           |
| MonthlyRetentionWeeksOfTheMonth | Tekst           | Tygodnie miesiąca w przypadku skonfigurowania miesięcznego przechowywania.   Na przykład, First, Last |
| PolicyName                      | Tekst           | Nazwa zdefiniowanych zasad                                   |
| PolicyUniqueId                  | Tekst           | Unikatowy identyfikator identyfikujący zasady                             |
| PolicyTimeZone                  | Tekst           | Strefa czasowa, w której w dziennikach są określone pola czasu zasad |
| RetentionDuration               | Tekst           | Czas przechowywania skonfigurowanych kopii zapasowych                    |
| Czas przechowywania                   | Tekst           | Typ przechowywania                                            |
| SchemaVersion                   | Tekst           | To pole oznacza bieżącą wersję schematu, to **v2** |
| Stan                           | Tekst           | Bieżący stan obiektu zasad. Na przykład aktywne, usunięte |
| SynchronisationFrequencyPerDay  | Liczbowy całkowity   | Liczba przypadków synchronizacji kopii zapasowej plików dla programu SC DPM i serwera usługi MAB |
| VaultUniqueId                   | Tekst           | Unikatowy identyfikator magazynu, do którego należą te zasady          |
| WeeklyRetentionDaysOfTheWeek    | Tekst           | Dni tygodnia wybrane do przechowywania tygodniowego               |
| WeeklyRetentionDuration         | Liczba dziesiętna | Łączny czas trwania przechowywania w tygodniach dla skonfigurowanych kopii zapasowych |
| WeeklyRetentionTimes            | Tekst           | Data i godzina skonfigurowania przechowywania tygodniowego            |
| YearlyRetentionDaysOfTheMonth   | Tekst           | Daty w miesiącu wybrane do przechowywania rocznego             |
| YearlyRetentionDaysOfTheWeek    | Tekst           | Dni tygodnia wybrane do przechowywania rocznego               |
| YearlyRetentionDuration         | Liczba dziesiętna | Łączny czas przechowywania w latach dla skonfigurowanych kopii zapasowych     |
| YearlyRetentionFormat           | Tekst           | Typ konfiguracji do przechowywania rocznego, na przykład dziennie na podstawie dnia, co tydzień w oparciu o tydzień |
| YearlyRetentionMonthsOfTheYear  | Tekst           | Miesiąc roku wybranego do przechowywania rocznego             |
| YearlyRetentionTimes            | Tekst           | Data i godzina skonfigurowania okresu przechowywania rocznego            |
| YearlyRetentionWeeksOfTheMonth  | Tekst           | Tygodnie miesiąca wybrane do utrzymania rocznego             |
| SourceSystem                    | Tekst           | System źródłowy bieżących danych — Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Ta tabela zawiera szczegółowe informacje o polach związanych z magazynem.

| **Pole**                      | **Typ danych** | **Opis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Tekst          | Identyfikator zasobu zbieranych danych. Na przykład identyfikator zasobu magazynu Recovery Services |
| OperationName                  | Tekst          | To pole reprezentuje nazwę bieżącej operacji — Storage lub StorageAssociation |
| Kategoria                       | Tekst          | To pole reprezentuje kategorię danych diagnostycznych wypychanych do dzienników Azure Monitor — AddonAzureBackupStorage |
| BackupItemUniqueId             | Tekst          | Unikatowy identyfikator używany do identyfikowania elementu kopii zapasowej dla maszyn wirtualnych, których kopia zapasowa jest wykonywana za pomocą programu DPM, serwera usługi MAB |
| BackupManagementServerUniqueId | Tekst          | Pole, aby jednoznacznie identyfikować serwer zarządzania kopiami zapasowymi, za pomocą którego jest chroniony element kopii zapasowej, w razie potrzeby |
| BackupManagementType           | Tekst          | Typ dostawcy dla serwera wykonującego zadanie tworzenia kopii zapasowej. Na przykład IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Tekst          | Obciążenie, dla którego ten wolumin jest preferowanym magazynem      |
| ProtectedContainerUniqueId     | Tekst          | Unikatowy identyfikator chronionego kontenera skojarzonego z elementem kopii zapasowej |
| SchemaVersion                  | Tekst          | Wersja schematu. Na przykład **v2**                   |
| Stan                          | Tekst          | Stan obiektu elementu kopii zapasowej. Na przykład aktywne, usunięte |
| StorageAllocatedInMBs          | Liczba        | Rozmiar magazynu przydzielonego przez odpowiedni element kopii zapasowej w odpowiednim magazynie typu dysk |
| StorageConsumedInMBs           | Liczba        | Rozmiar magazynu zużywanego przez odpowiedni element kopii zapasowej w odpowiednim magazynie |
| StorageName                    | Tekst          | Nazwa jednostki magazynowej. Na przykład E:\                      |
| StorageTotalSizeInGBs          | Tekst          | Łączny rozmiar magazynu (w GB) zużyty przez jednostkę magazynu     |
| StorageType                    | Tekst          | Typ magazynu, na przykład Chmura, wolumin, dysk             |
| StorageUniqueId                | Tekst          | Unikatowy identyfikator używany do identyfikowania jednostki magazynu                |
| VaultUniqueId                  | Tekst          | Unikatowy identyfikator używany do identyfikowania magazynu związanego z jednostką magazynu |
| VolumeFriendlyName             | Tekst          | Przyjazna nazwa woluminu magazynu                          |
| SourceSystem                   | Tekst          | System źródłowy bieżących danych — Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Prawidłowe nazwy operacji dla każdej tabeli

Każdy rekord w powyższych tabelach ma skojarzoną **nazwę operacji**. Nazwa operacji opisuje typ rekordu (a także wskazuje, które pola w tabeli są wypełnione dla tego rekordu). Każda tabela (kategoria) obsługuje jedną lub więcej unikatowych nazw operacji. Poniżej znajduje się podsumowanie obsługiwanych nazw operacji dla każdej z powyższych tabel.

| **Nazwa/Kategoria tabeli**                   | **Obsługiwane nazwy operacji** | **Opis**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Reprezentuje rekord zawierający wszystkie szczegóły danego elementu kopii zapasowej, takie jak ID, Name, Type itd. |
| CoreAzureBackup | BackupItemAssociation | Reprezentuje mapowanie między elementem kopii zapasowej i skojarzonym z nim kontenerem chronionym (jeśli dotyczy). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | Reprezentuje mapowanie między elementem kopii zapasowej a jego rozmiarem frontonu. |
| CoreAzureBackup | ProtectedContainer | Reprezentuje rekord zawierający wszystkie szczegóły danego chronionego kontenera, takie jak ID, Name, Type itd. |
| CoreAzureBackup | ProtectedContainerAssociation | Reprezentuje mapowanie między chronionym kontenerem i magazynem używanym do jego tworzenia kopii zapasowych. |
| CoreAzureBackup | Magazyn | Reprezentuje rekord zawierający wszystkie szczegóły danego magazynu, np. Identyfikator, nazwa, Tagi, lokalizacja itd. |
| CoreAzureBackup | RecoveryPoint | Reprezentuje rekord zawierający najstarszy i najnowszy punkt odzyskiwania dla danego elementu kopii zapasowej. |
| AddonAzureBackupJobs | Zadanie |  Reprezentuje rekord zawierający wszystkie szczegóły danego zadania. Na przykład operacja zadania, czas rozpoczęcia, stan itd. |
| AddonAzureBackupAlerts | Alerty | Reprezentuje rekord zawierający wszystkie szczegóły danego alertu. Na przykład czas utworzenia alertu, ważność, stan itd.  |
| AddonAzureBackupStorage | Storage | Reprezentuje rekord zawierający wszystkie szczegóły danej jednostki magazynu. Na przykład nazwa magazynu, typ itp. |
| AddonAzureBackupStorage | StorageAssociation | Reprezentuje mapowanie między elementem kopii zapasowej a łącznym magazynem w chmurze zużywanym przez element kopii zapasowej. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | Reprezentuje rekord zawierający liczbę chronionych wystąpień dla każdego kontenera lub elementu kopii zapasowej. W przypadku kopii zapasowej maszyny wirtualnej platformy Azure liczba chronionych wystąpień jest dostępna na poziomie elementu kopii zapasowej dla innych obciążeń, które są dostępne na chronionym poziomie kontenera. |
| AddonAzureBackupPolicy | Zasady |  Reprezentuje rekord zawierający wszystkie szczegóły zasad tworzenia kopii zapasowej i przechowywania. Na przykład identyfikator, nazwa, ustawienia przechowywania itp. |
| AddonAzureBackupPolicy | PolicyAssociation | Reprezentuje mapowanie między elementem kopii zapasowej a zastosowanymi do niego zasadami tworzenia kopii zapasowych. |   

Często należy wykonać sprzężenia między różnymi tabelami, a także różne zestawy rekordów, które są częścią tej samej tabeli (różnią się nazwami operacji), aby uzyskać wszystkie pola wymagane do analizy. Zapoznaj się z [przykładowymi zapytaniami](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) , aby rozpocząć pracę. 

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak wysyłać dane diagnostyczne do Log Analytics](./backup-azure-diagnostic-events.md)
- [Dowiedz się, jak pisać zapytania w tabelach specyficznych dla zasobów](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)
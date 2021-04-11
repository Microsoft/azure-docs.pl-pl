---
title: Funkcje systemowe w dziennikach Azure Monitor
description: Zapisuj niestandardowe zapytania dotyczące dzienników Azure Monitor przy użyciu funkcji systemowych
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564912"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Funkcje systemowe w dziennikach Azure Monitor

Azure Backup udostępnia zestaw funkcji, nazywanych funkcjami systemowymi lub funkcjami rozwiązania, które są domyślnie dostępne w obszarach roboczych Log Analytics (LA).
 
Te funkcje działają na danych w [nieprzetworzonych tabelach Azure Backup](./backup-azure-reports-data-model.md) w La i zwracają sformatowane dane, które ułatwiają uzyskiwanie informacji o wszystkich jednostkach związanych z kopiami zapasowymi przy użyciu prostych zapytań. Użytkownicy mogą przekazywać parametry do tych funkcji, aby filtrować dane zwracane przez te funkcje. 

Zalecane jest używanie funkcji systemowych do wykonywania zapytań dotyczących danych kopii zapasowych w programie LA Workspaces w celu tworzenia raportów niestandardowych, ponieważ zapewniają one wiele korzyści, zgodnie z opisem w poniższej sekcji.

## <a name="benefits-of-using-system-functions"></a>Zalety korzystania z funkcji systemowych

* **Prostsze zapytania**: korzystanie z funkcji pomaga w zmniejszeniu liczby sprzężeń potrzebnych w zapytaniach. Domyślnie funkcje zwracają schematy "spłaszczone", które obejmują wszystkie informacje dotyczące jednostki (wystąpienie kopii zapasowej, zadanie, magazyn itd.), w którym są wysyłane zapytania. Jeśli na przykład chcesz uzyskać listę pomyślnych zadań tworzenia kopii zapasowej według nazwy elementu kopii zapasowej i skojarzonego z nim kontenera, proste wywołanie funkcji **_AzureBackup_getJobs ()** udostępni wszystkie te informacje dla każdego zadania. Z drugiej strony wykonywanie zapytania dotyczącego nieprzetworzonych tabel bezpośrednio wymagało wykonania wielu sprzężeń między tabelami [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) i [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) .

* **Płynne przejście ze starszego zdarzenia diagnostyki**: funkcja system Functions pomaga w bezproblemowym przejściu ze [starszego zdarzenia diagnostyki](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport w trybie AzureDiagnostics) do [zdarzeń specyficznych dla zasobów](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Wszystkie funkcje systemowe zapewniane przez Azure Backup umożliwiają określenie parametru, który umożliwia wybranie, czy funkcja powinna wykonywać zapytania dotyczące danych tylko z tabel specyficznych dla zasobów, czy też wykonywać zapytania dotyczące danych z tabeli ze starszą wersją i tabel specyficznych dla zasobów (z deduplikacją rekordów).
    * Jeśli pomyślnie przeprowadzono migrację do tabel specyficznych dla zasobów, możesz zdecydować się na wykluczenie starszej tabeli z kwerendy przez funkcję.
    * Jeśli obecnie trwa proces migracji i masz pewne dane w starszych tabelach, które są wymagane do analizy, możesz dołączyć starszą tabelę. Po zakończeniu przejścia i przeniesieniu danych z starszej tabeli, można po prostu zaktualizować wartość parametru przekazaną do funkcji w zapytaniach, aby wykluczyć starszą tabelę.
    * Jeśli nadal używasz tylko starszej tabeli, funkcje będą nadal działać, jeśli wybierzesz opcję dołączenia starszej tabeli za pośrednictwem tego samego parametru. Jednak zaleca się [przełączenie do tabel specyficznych dla zasobów](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) w najkrótszym czasie.

* **Zmniejsza prawdopodobieństwo przerwania zapytań niestandardowych**: Jeśli Azure Backup wprowadza ulepszenia schematu bazowych tabel La, aby uwzględnić przyszłe scenariusze raportowania, definicja funkcji zostanie również zaktualizowana w celu uwzględnienia zmian schematu. W takim przypadku, jeśli używasz funkcji systemowych do tworzenia kwerend niestandardowych, zapytania nie będą przerywane, nawet jeśli istnieją zmiany w źródłowym schemacie tabel.

> [!NOTE]
> Funkcje systemowe są obsługiwane przez firmę Microsoft, a ich definicje nie mogą być edytowane przez użytkowników. Jeśli potrzebujesz funkcji edytowalnych, możesz tworzyć [zapisane funkcje](../azure-monitor/logs/functions.md) w La.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Typy funkcji systemowych oferowane przez Azure Backup

* **Podstawowe funkcje**: są to funkcje, które ułatwiają wykonywanie zapytań dotyczących dowolnych kluczowych jednostek Azure Backup, takich jak wystąpienia kopii zapasowej, magazyny, zasady, zadania i jednostki rozliczeń. Na przykład funkcja **_AzureBackup_getBackupInstances** zwraca listę wszystkich wystąpień kopii zapasowej istniejących w danym środowisku od ostatniego dnia ukończenia (w formacie UTC). Parametry i zwracany schemat dla każdej z tych podstawowych funkcji zostały podsumowane poniżej w tym artykule.

* **Funkcje trendu**: są to funkcje, które zwracają rekordy historyczne dla jednostek związanych z kopiami zapasowymi (na przykład wystąpienia kopii zapasowej, grupy rozliczeń) i umożliwiają codzienne, cotygodniowe i miesięczne informacje o trendach dotyczące kluczowych metryk (na przykład liczba użytych magazynów) związanych z tymi jednostkami. Parametry i zwracany schemat dla każdej z tych funkcji trendu zestawiono poniżej w tym artykule.

> [!NOTE]
> Obecnie funkcje systemowe zwracają dane dla maksymalnie ostatniego dnia ukończenia (w formacie UTC). Dane bieżącego dnia nie są zwracane. Dlatego jeśli chcesz pobrać rekordy dla bieżącego dnia, musisz użyć tabel RAW LA.


## <a name="list-of-system-functions"></a>Lista funkcji systemowych

### <a name="core-functions"></a>Podstawowe funkcje

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Ta funkcja zwraca listę wszystkich magazynów Recovery Services w środowisku platformy Azure, które są skojarzone z obszarem roboczym LA.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tego parametru należy używać razem z parametrem RangeEnd tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z magazynem w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdego magazynu. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tego parametru należy używać razem z parametrem RangeStart tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z magazynem w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdego magazynu. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji ułatwia pobieranie tylko tych magazynów, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie tylko tych magazynów, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie listy nazw magazynów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie rekordów związanych tylko z określonymi magazynami. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator magazynu |
| Id | Identyfikator Azure Resource Manager (ARM) magazynu |
| Nazwa | Nazwa magazynu |
| SubscriptionId | Identyfikator subskrypcji, w której znajduje się magazyn |
| Lokalizacja | Lokalizacja, w której znajduje się magazyn |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzony z magazynem |
| Tagi | Tagi magazynu |
| TimeGenerated | Sygnatura czasowa rekordu |
| Typ |  Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Ta funkcja zwraca listę zasad tworzenia kopii zapasowych używanych w środowisku platformy Azure wraz ze szczegółowymi informacjami o każdej z tych zasad, takimi jak typ źródła danych, typ replikacji magazynu i tak dalej.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tego parametru należy używać wraz z parametrem RangeStart tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z zasadami w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdej zasady. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tego parametru należy używać razem z parametrem RangeStart tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z zasadami w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdej zasady. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji ułatwia pobieranie tylko tych zasad, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie tylko tych zasad, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie listy nazw magazynów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobranie rekordów zasad odnoszących się tylko do określonych magazynów. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy zasad we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator zasad |
| Id | Identyfikator Azure Resource Manager (ARM) zasad |
| Nazwa | Nazwa zasad |
| Rozwiązanie do tworzenia kopii zapasowych | Rozwiązanie do tworzenia kopii zapasowych, z którym są skojarzone zasady. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| TimeGenerated | Sygnatura czasowa rekordu |
| VaultUniqueId | Klucz obcy odwołujący się do magazynu skojarzonego z zasadami |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z zasadami |
| VaultName | Nazwa magazynu skojarzonego z zasadami |
| VaultTags | Tagi magazynu skojarzonego z zasadami |
| VaultLocation | Lokalizacja magazynu skojarzonego z zasadami |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z zasadami |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z zasadami |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| ExtendedProperties | Dodatkowe właściwości zasad |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Ta funkcja zwraca listę wszystkich zadań związanych z tworzeniem kopii zapasowych i przywracania, które zostały wyzwolone w określonym przedziale czasu, wraz ze szczegółowymi informacjami o poszczególnych zadaniach, takimi jak stan zadania, czas trwania zadania, przesłane dane itd.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Użyj tego parametru wraz z parametrem RangeEnd, aby pobrać listę wszystkich zadań, które zostały uruchomione w okresie od RangeStart do RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Użyj tego parametru wraz z parametrem RangeStart, aby pobrać listę wszystkich zadań, które zostały uruchomione w okresie od RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji pomaga pobrać tylko te zadania, które są skojarzone z magazynami w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielanych przecinkami jako parametru tej funkcji pomaga pobrać tylko te zadania, które są skojarzone z magazynami w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie listy nazw magazynów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie zadań związanych tylko z określonymi magazynami. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje zadania we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |
| JobOperationList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego typu zadania. Na przykład kopia zapasowa lub przywracanie. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje zarówno zadania tworzenia kopii zapasowej, jak i przywracania. | N | Kopii zapasowej | 
| JobStatusList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego stanu zadania. Na przykład zakończone, zakończone niepowodzeniem i tak dalej. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje wszystkie zadania niezależnie od stanu. | N | "Nie powiodło się, CompletedWithWarnings" |
| JobFailureCodeList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego kodu błędu. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje wszystkie zadania niezależnie od kodu błędu. | N | Prawnego
| DatasourceSetName | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji w określonym zasobie nadrzędnym. Na przykład aby zwrócić SQL w wystąpieniach kopii zapasowych maszyny wirtualnej platformy Azure należących do maszyny wirtualnej "TestVM", określ _TestVM_ jako wartość tego parametru. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich wystąpieniach kopii zapasowych. | N | TestVM |
| BackupInstanceName | Użyj tego parametru, aby wyszukać zadania dla konkretnego wystąpienia kopii zapasowej według nazwy. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich wystąpieniach kopii zapasowych. | N | TestVM |
| ExcludeLog | Użyj tego parametru, aby wykluczyć zadania dziennika z zwracania przez funkcję (pomaga w wydajności zapytania). Domyślnie wartością tego parametru jest true, co oznacza, że funkcja wyklucza zadania dziennika. | N | true


**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator zadania |
| OperationCategory | Kategoria wykonywanej operacji. Na przykład kopia zapasowa, przywracanie |
| Operacja | Szczegóły wykonywanej operacji. Na przykład dziennik (dla kopii zapasowej dziennika)|
| Stan | Stan zadania. Na przykład ukończone, zakończone niepowodzeniem, CompletedWithWarnings |
| ErrorTitle | Kod błędu zadania |
| StartTime | Data i godzina rozpoczęcia zadania |
| DurationInSecs | Czas trwania zadania (w sekundach) |
| DataTransferredInMBs | Dane przesłane przez zadanie w MB |
| RestoreJobRPDateTime | Data i godzina utworzenia odzyskiwanego punktu odzyskiwania |
| RestoreJobRPLocation | Lokalizacja, w której zapisano odzyskiwany punkt odzyskiwania |
| BackupInstanceUniqueId | Klucz obcy odwołujący się do wystąpienia kopii zapasowej skojarzonego z zadaniem |
| BackupInstanceId | Identyfikator Azure Resource Manager (ARM) wystąpienia kopii zapasowej skojarzonego z zadaniem |
| BackupInstanceFriendlyName | Nazwa wystąpienia kopii zapasowej skojarzonego z zadaniem |
| DatasourceResourceId | Identyfikator Azure Resource Manager (ARM) źródłowego źródła danych skojarzonego z zadaniem. Na przykład identyfikator Azure Resource Manager (ARM) maszyny wirtualnej |
| DatasourceFriendlyName | Przyjazna nazwa źródłowego źródła danych skojarzonego z zadaniem |
| DatasourceType | Typ źródła danych skojarzonego z zadaniem. Na przykład "Microsoft. COMPUTE/virtualMachines" |
| BackupSolution | Rozwiązanie do tworzenia kopii zapasowych, z którym skojarzone jest zadanie. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| DatasourceSetResourceId | Azure Resource Manager (ARM) identyfikator zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma to zastosowanie). Na przykład w przypadku źródła danych SQL w usłudze Azure VM to pole będzie zawierać identyfikator Azure Resource Manager (ARM) maszyny wirtualnej, w której istnieje SQL Database |
| DatasourceSetType | Typ zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma zastosowanie). Na przykład dla SAP HANA w źródle danych maszyny wirtualnej platformy Azure to pole będzie miało wartość Microsoft. COMPUTE/virtualMachines, ponieważ zasób nadrzędny jest maszyną wirtualną platformy Azure |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z zadaniem |
| VaultUniqueId | Klucz obcy odwołujący się do magazynu skojarzonego z zadaniem |
| VaultName | Nazwa magazynu skojarzonego z zadaniem |
| VaultTags | Tagi magazynu skojarzonego z zadaniem |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z zadaniem |
| VaultLocation | Lokalizacja magazynu skojarzonego z zadaniem |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z zadaniem |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| TimeGenerated | Sygnatura czasowa rekordu |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Ta funkcja zwraca listę wystąpień kopii zapasowych, które są skojarzone z magazynami Recovery Services, wraz ze szczegółowymi informacjami o poszczególnych wystąpieniach kopii zapasowych, takimi jak użycie magazynu w chmurze, skojarzone zasady i tak dalej.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tego parametru należy używać razem z parametrem RangeEnd tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z wystąpieniem kopii zapasowej w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdego wystąpienia kopii zapasowej. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tego parametru należy używać razem z parametrem RangeStart tylko wtedy, gdy konieczne jest pobranie wszystkich rekordów związanych z wystąpieniem kopii zapasowej w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdego wystąpienia kopii zapasowej. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji pomaga pobrać tylko te wystąpienia kopii zapasowych, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielonych przecinkami jako parametru tej funkcji pomaga pobrać tylko te wystąpienia kopii zapasowych, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie rozdzielanej przecinkami listy nazw magazynów jako parametru tej funkcji ułatwia pobieranie rekordów wystąpień kopii zapasowych odnoszących się tylko do określonych magazynów. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy wystąpień kopii zapasowych we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |
| ProtectionInfoList | Użyj tego parametru, aby określić, czy mają być uwzględniane tylko te wystąpienia kopii zapasowych, które są aktywnie chronione, czy też obejmują te wystąpienia, dla których ochrona została zatrzymana, oraz wystąpienia, dla których trwa oczekiwanie na początkową kopię zapasową. Obsługiwane wartości to "Protected", "ProtectionStopped", "InitialBackupPending" lub rozdzielona przecinkami kombinacja dowolnej z tych wartości. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie wystąpienia kopii zapasowej niezależnie od szczegółów ochrony. | N | Chronione |
| DatasourceSetName | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji w określonym zasobie nadrzędnym. Na przykład aby zwrócić SQL w wystąpieniach kopii zapasowych maszyny wirtualnej platformy Azure należących do maszyny wirtualnej "TestVM", określ _TestVM_ jako wartość tego parametru. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich wystąpieniach kopii zapasowych. | N | TestVM |
| BackupInstanceName | Użyj tego parametru, aby wyszukać konkretne wystąpienie kopii zapasowej według nazwy. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie wystąpienia kopii zapasowej. | N | TestVM |
| DisplayAllFields | Użyj tego parametru, aby wybrać, czy ma być pobierany tylko podzbiór pól zwracanych przez funkcję. Jeśli wartością tego parametru jest false, funkcja eliminuje informacje dotyczące magazynu i punktu przechowywania z danych wyjściowych funkcji. Jest to przydatne, jeśli używasz tej funkcji jako pośredniego kroku w większej kwerendzie i chcesz zoptymalizować wydajność zapytania, eliminując kolumny, które nie są wymagane do analizy. Domyślnie wartością tego parametru jest true, co oznacza, że funkcja zwraca wszystkie pola odnoszące się do wystąpienia kopii zapasowej. | N | true |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator wystąpienia kopii zapasowej |
| Id | Identyfikator Azure Resource Manager (ARM) wystąpienia kopii zapasowej |
| FriendlyName | Przyjazna nazwa wystąpienia kopii zapasowej |
| ProtectionInfo | Informacje na temat ustawień ochrony wystąpienia kopii zapasowej. Na przykład skonfigurowano ochronę, Zatrzymano ochronę, oczekiwanie na początkową kopię zapasową |
| LatestRecoveryPoint | Data i godzina najnowszego punktu odzyskiwania skojarzonego z wystąpieniem kopii zapasowej |
| OldestRecoveryPoint | Data i godzina najstarszego punktu odzyskiwania skojarzonego z wystąpieniem kopii zapasowej |
| SourceSizeInMBs | Rozmiar frontonu wystąpienia kopii zapasowej w MB |
| VaultStore_StorageConsumptionInMBs | Łączny magazyn w chmurze zużyty przez wystąpienie kopii zapasowej w warstwie Standardowa magazynu |
| DataSourceFriendlyName | Przyjazna nazwa źródła danych odpowiadającego wystąpieniu kopii zapasowej |
| BackupSolution | Rozwiązanie do tworzenia kopii zapasowej, z którym jest skojarzone wystąpienie kopii zapasowej. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| DatasourceType | Typ źródła danych odpowiadającego wystąpieniu kopii zapasowej. Na przykład "Microsoft. COMPUTE/virtualMachines" |
| DatasourceResourceId | Identyfikator Azure Resource Manager (ARM) bazowego źródła danych odpowiadającego wystąpieniu kopii zapasowej. Na przykład identyfikator Azure Resource Manager (ARM) maszyny wirtualnej |
| DatasourceSetFriendlyName | Przyjazna nazwa zasobu nadrzędnego źródła danych (tam gdzie ma to zastosowanie). Na przykład w przypadku źródła danych SQL w usłudze Azure VM to pole będzie zawierać nazwę maszyny wirtualnej, w której istnieje SQL Database |
| DatasourceSetResourceId | Azure Resource Manager (ARM) identyfikator zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma to zastosowanie). Na przykład w przypadku źródła danych SQL w usłudze Azure VM to pole będzie zawierać identyfikator Azure Resource Manager (ARM) maszyny wirtualnej, w której istnieje SQL Database |
| DatasourceSetType | Typ zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma zastosowanie). Na przykład dla SAP HANA w źródle danych maszyny wirtualnej platformy Azure to pole będzie miało wartość Microsoft. COMPUTE/virtualMachines, ponieważ zasób nadrzędny jest maszyną wirtualną platformy Azure |
| PolicyName | Nazwa zasad skojarzonych z wystąpieniem kopii zapasowej |
| PolicyUniqueId | Klucz obcy odwołujący się do zasad skojarzonych z wystąpieniem kopii zapasowej  |
| PolicyId | Identyfikator Azure Resource Manager (ARM) zasad skojarzonych z wystąpieniem kopii zapasowej |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultUniqueId | Klucz obcy, który odwołuje się do magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultName | Nazwa magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultTags | Tagi magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultLocation | Lokalizacja magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z wystąpieniem kopii zapasowej |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| TimeGenerated | Sygnatura czasowa rekordu |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Ta funkcja zwraca listę wszystkich jednostek rozliczeń związanych z kopiami zapasowymi (grup rozliczeń) wraz z informacjami na temat kluczowych składników rozliczeń, takich jak rozmiar frontonu i łączny magazyn w chmurze.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tego parametru należy używać razem z parametrem RangeEnd tylko wtedy, gdy trzeba pobrać wszystkie rekordy powiązane z grupą rozliczeniową w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdej grupy rozliczeń. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tego parametru należy używać razem z parametrem RangeStart tylko wtedy, gdy trzeba pobrać wszystkie rekordy powiązane z grupą rozliczeniową w okresie od RangeStart do RangeEnd. Domyślnie wartość RangeStart i RangeEnd mają wartość null, co spowoduje, że funkcja pobierze tylko najnowszy rekord dla każdej grupy rozliczeń. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji ułatwia pobieranie tylko tych grup rozliczeń, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie tylko tych grup rozliczeń, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie rozdzielanej przecinkami listy nazw magazynów jako parametru tej funkcji ułatwia pobieranie rekordów wystąpień kopii zapasowych odnoszących się tylko do określonych magazynów. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy grup rozliczeń we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |
| BillingGroupName | Użyj tego parametru, aby wyszukać konkretną grupę rozliczeń według nazwy. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie grupy rozliczeń. | N | TestVM |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator grupy rozliczeń |
| FriendlyName | Przyjazna nazwa grupy rozliczeń |
| Nazwa | Nazwa grupy rozliczeń |
| Typ | Typ grupy rozliczeń. Na przykład ProtectedContainer lub BackupItem |
| SourceSizeInMBs | Rozmiar frontonu grupy rozliczeń w MB |
| VaultStore_StorageConsumptionInMBs | Łączny magazyn w chmurze zużyty przez grupę rozliczeń w ramach warstwy Standardowa magazynu |
| BackupSolution | Rozwiązanie do tworzenia kopii zapasowych, z którym jest skojarzona Grupa rozliczeń. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z grupą rozliczeń |
| VaultUniqueId | Klucz obcy, który odwołuje się do magazynu skojarzonego z grupą rozliczeń |
| VaultName | Nazwa magazynu skojarzonego z grupą rozliczeń |
| VaultTags | Tagi magazynu skojarzonego z grupą rozliczeń |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z grupą rozliczeń |
| VaultLocation | Lokalizacja magazynu skojarzonego z grupą rozliczeń |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z grupą rozliczeń |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| TimeGenerated | Sygnatura czasowa rekordu |
| ExtendedProperties | Dodatkowe właściwości grupy rozliczeń |

### <a name="trend-functions"></a>Funkcje trendów

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Ta funkcja zwraca rekordy historyczne dla każdego wystąpienia kopii zapasowej, co pozwala na wyświetlanie kluczowych codziennych, tygodniowych i miesięcznych trendów związanych z liczbą wystąpień kopii zapasowych i użyciem magazynu na wielu poziomach szczegółowości.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Użyj tego parametru wraz z parametrem RangeEnd, aby pobrać wszystkie rekordy związane z wystąpieniem kopii zapasowej w okresie od RangeStart do RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Użyj tego parametru wraz z parametrem RangeStart, aby pobrać wszystkie rekordy związane z wystąpieniem kopii zapasowej w okresie od RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji pomaga pobrać tylko te wystąpienia kopii zapasowych, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielonych przecinkami jako parametru tej funkcji pomaga pobrać tylko te wystąpienia kopii zapasowych, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie rozdzielanej przecinkami listy nazw magazynów jako parametru tej funkcji ułatwia pobieranie rekordów wystąpień kopii zapasowych odnoszących się tylko do określonych magazynów. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy wystąpień kopii zapasowych we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |
| ProtectionInfoList | Użyj tego parametru, aby określić, czy mają być uwzględniane tylko te wystąpienia kopii zapasowych, które są aktywnie chronione, czy też obejmują te wystąpienia, dla których ochrona została zatrzymana, oraz wystąpienia, dla których trwa oczekiwanie na początkową kopię zapasową. Obsługiwane wartości to "Protected", "ProtectionStopped", "InitialBackupPending" lub rozdzielona przecinkami kombinacja dowolnej z tych wartości. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie wystąpienia kopii zapasowej niezależnie od szczegółów ochrony. | N | Chronione |
| DatasourceSetName | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji w określonym zasobie nadrzędnym. Na przykład aby zwrócić SQL w wystąpieniach kopii zapasowych maszyny wirtualnej platformy Azure należących do maszyny wirtualnej "TestVM", określ _TestVM_ jako wartość tego parametru. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich wystąpieniach kopii zapasowych. | N | TestVM |
| BackupInstanceName | Użyj tego parametru, aby wyszukać konkretne wystąpienie kopii zapasowej według nazwy. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie wystąpienia kopii zapasowej. | N | TestVM |
| DisplayAllFields | Użyj tego parametru, aby wybrać, czy ma być pobierany tylko podzbiór pól zwracanych przez funkcję. Jeśli wartością tego parametru jest false, funkcja eliminuje informacje dotyczące magazynu i punktu przechowywania z danych wyjściowych funkcji. Jest to przydatne, jeśli używasz tej funkcji jako pośredniego kroku w większej kwerendzie i chcesz zoptymalizować wydajność zapytania, eliminując kolumny, które nie są wymagane do analizy. Domyślnie wartością tego parametru jest true, co oznacza, że funkcja zwraca wszystkie pola odnoszące się do wystąpienia kopii zapasowej. | N | true |
| Agregacja | Użyj tego parametru, aby określić stopień szczegółowości czasu, w którym mają zostać pobrane dane. Jeśli wartością tego parametru jest "codziennie", funkcja zwraca rekord na wystąpienie kopii zapasowej dziennie, umożliwiając analizowanie codziennych trendów użycia magazynu i liczby wystąpień kopii zapasowych. Jeśli wartością tego parametru jest "Weekly", funkcja zwraca rekord na wystąpienie kopii zapasowej na tydzień, co pozwala analizować trendy tygodniowo. Analogicznie, można określić wartość "miesięcznie", aby analizować miesięczne trendy. Wartość domyślna to "dzienna". W przypadku wyświetlania danych w większych zakresach czasu zaleca się użycie wartości "cotygodniowe" lub "Monthly" w celu uzyskania lepszej wydajności zapytań oraz łatwiejszej analizy trendów. | N | Co tydzień |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator wystąpienia kopii zapasowej |
| Id | Identyfikator Azure Resource Manager (ARM) wystąpienia kopii zapasowej |
| FriendlyName | Przyjazna nazwa wystąpienia kopii zapasowej |
| ProtectionInfo | Informacje na temat ustawień ochrony wystąpienia kopii zapasowej. Na przykład skonfigurowano ochronę, Zatrzymano ochronę, oczekiwanie na początkową kopię zapasową |
| LatestRecoveryPoint | Data i godzina najnowszego punktu odzyskiwania skojarzonego z wystąpieniem kopii zapasowej |
| OldestRecoveryPoint | Data i godzina najstarszego punktu odzyskiwania skojarzonego z wystąpieniem kopii zapasowej |
| SourceSizeInMBs | Rozmiar frontonu wystąpienia kopii zapasowej w MB |
| VaultStore_StorageConsumptionInMBs | Łączny magazyn w chmurze zużyty przez wystąpienie kopii zapasowej w warstwie Standardowa magazynu |
| DataSourceFriendlyName | Przyjazna nazwa źródła danych odpowiadającego wystąpieniu kopii zapasowej |
| BackupSolution | Rozwiązanie do tworzenia kopii zapasowej, z którym jest skojarzone wystąpienie kopii zapasowej. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| DatasourceType | Typ źródła danych odpowiadającego wystąpieniu kopii zapasowej. Na przykład "Microsoft. COMPUTE/virtualMachines" |
| DatasourceResourceId | Identyfikator Azure Resource Manager (ARM) bazowego źródła danych odpowiadającego wystąpieniu kopii zapasowej. Na przykład identyfikator Azure Resource Manager (ARM) maszyny wirtualnej |
| DatasourceSetFriendlyName | Przyjazna nazwa zasobu nadrzędnego źródła danych (tam gdzie ma to zastosowanie). Na przykład w przypadku źródła danych SQL w usłudze Azure VM to pole będzie zawierać nazwę maszyny wirtualnej, w której istnieje SQL Database |
| DatasourceSetResourceId | Azure Resource Manager (ARM) identyfikator zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma to zastosowanie). Na przykład w przypadku źródła danych SQL w usłudze Azure VM to pole będzie zawierać identyfikator Azure Resource Manager (ARM) maszyny wirtualnej, w której istnieje SQL Database |
| DatasourceSetType | Typ zasobu nadrzędnego źródła danych (wszędzie tam, gdzie ma zastosowanie). Na przykład dla SAP HANA w źródle danych maszyny wirtualnej platformy Azure to pole będzie miało wartość Microsoft. COMPUTE/virtualMachines, ponieważ zasób nadrzędny jest maszyną wirtualną platformy Azure |
| PolicyName | Nazwa zasad skojarzonych z wystąpieniem kopii zapasowej |
| PolicyUniqueId | Klucz obcy odwołujący się do zasad skojarzonych z wystąpieniem kopii zapasowej  |
| PolicyId | Identyfikator Azure Resource Manager (ARM) zasad skojarzonych z wystąpieniem kopii zapasowej |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultUniqueId | Klucz obcy, który odwołuje się do magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultName | Nazwa magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultTags | Tagi magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultLocation | Lokalizacja magazynu skojarzonego z wystąpieniem kopii zapasowej |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z wystąpieniem kopii zapasowej |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| TimeGenerated | Sygnatura czasowa rekordu |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Ta funkcja zwraca rekordy historyczne dla każdej jednostki rozliczeniowej, co pozwala na wyświetlanie kluczowych codziennych, tygodniowych i miesięcznych trendów związanych z rozmiarem frontonu i użyciem magazynu, na różnych poziomach szczegółowości.

**Parametry**

| **Nazwa parametru** | **Opis** | **Wymagane?** | **Przykładowa wartość** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Użyj tego parametru wraz z parametrem RangeEnd, aby pobrać wszystkie rekordy powiązane z grupą rozliczeniową w przedziale czasu od RangeStart do RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Użyj tego parametru wraz z parametrem RangeStart, aby pobrać wszystkie rekordy powiązane z grupą rozliczeniową w przedziale czasu od RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu subskrypcji, w których istnieją dane kopii zapasowej. Określenie rozdzielanej przecinkami listy identyfikatorów subskrypcji jako parametru tej funkcji ułatwia pobieranie tylko tych grup rozliczeń, które znajdują się w określonych subskrypcjach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich subskrypcjach. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu regionów, w których istnieją dane kopii zapasowej. Określenie listy regionów rozdzielanych przecinkami jako parametru tej funkcji ułatwia pobieranie tylko tych grup rozliczeń, które znajdują się w określonych regionach. Domyślnie wartością tego parametru jest "*", co sprawia, że funkcja wyszukuje rekordy we wszystkich regionach. | N | "Wschód, zachodnie"|
| VaultList    |Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla określonego zestawu magazynów. Określenie rozdzielanej przecinkami listy nazw magazynów jako parametru tej funkcji ułatwia pobieranie rekordów wystąpień kopii zapasowych odnoszących się tylko do określonych magazynów. Domyślnie wartością tego parametru jest ' * ', co sprawia, że funkcja wyszukuje rekordy grup rozliczeń we wszystkich magazynach. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji do rekordów odnoszących się do określonego typu magazynu. Obecnie jedynym obsługiwanym typem magazynu jest "Microsoft. RecoveryServices/magazyny", który jest wartością domyślną tego parametru. | N | "Microsoft. RecoveryServices/magazyny"|
| ExcludeLegacyEvent  | Użyj tego parametru, aby określić, czy dane mają być zapytania w starszej tabeli AzureDiagnostics, czy nie. Jeśli wartością tego parametru jest false, funkcja wysyła zapytanie do danych z tabeli AzureDiagnostics i tabel specyficznych dla zasobów. Jeśli wartością tego parametru jest true, funkcja wysyła zapytanie do danych tylko z tabel specyficznych dla zasobów. Wartość domyślna to True. | N | true |
| BackupSolutionList | Użyj tego parametru, aby odfiltrować dane wyjściowe funkcji dla pewnego zestawu rozwiązań do tworzenia kopii zapasowych używanych w środowisku platformy Azure. Na przykład w przypadku wybrania wartości tego parametru "kopia zapasowa maszyny wirtualnej platformy Azure, SQL w usłudze Azure VM Backup, DPM", funkcja zwróci tylko rekordy powiązane z elementami, których kopia zapasowa jest wykonywana za pomocą kopii zapasowej maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure lub programu DPM do usługi Azure Backup. Domyślnie wartością tego parametru jest "*", co powoduje, że funkcja zwraca rekordy odnoszące się do wszystkich rozwiązań do tworzenia kopii zapasowych obsługiwanych przez raporty kopii zapasowych (obsługiwane wartości to "kopia zapasowa maszyny wirtualnej platformy Azure", "Tworzenie kopii zapasowej bazy danych SQL na maszynie wirtualnej platformy Azure", "SAP HANA w kopii zapasowej maszyny wirtualnej platformy Azure", "kopia zapasowa Azure (Azure Files)", "Azure Backup Agent", "DPM", "Azure Backup Server" lub kombinacja oddzielonych przecinkami dowolnej z tych wartości). | N | "Kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure, DPM, Azure Backup Agent" |
| BillingGroupName | Użyj tego parametru, aby wyszukać konkretną grupę rozliczeń według nazwy. Domyślnie wartość jest równa "*", co sprawia, że funkcja wyszukuje wszystkie grupy rozliczeń. | N | TestVM |
| Agregacja | Użyj tego parametru, aby określić stopień szczegółowości czasu, w którym mają zostać pobrane dane. Jeśli wartością tego parametru jest "codziennie", funkcja zwraca rekord dla grupy rozliczeń dziennie, co pozwala analizować dzienne trendy użycia magazynu i rozmiaru frontonu. Jeśli wartością tego parametru jest "Weekly", funkcja zwraca rekord na wystąpienie kopii zapasowej na tydzień, co pozwala analizować trendy tygodniowo. Analogicznie, można określić wartość "miesięcznie", aby analizować miesięczne trendy. Wartość domyślna to "dzienna". W przypadku wyświetlania danych w większych zakresach czasu zaleca się użycie wartości "cotygodniowe" lub "Monthly" w celu uzyskania lepszej wydajności zapytań oraz łatwiejszej analizy trendów. | N | Co tydzień |

**Zwrócone pola**

| **Nazwa pola** | **Opis** |
| -------------- | --------------- |
| Unikatowy identyfikator | Klucz podstawowy oznaczający unikatowy identyfikator grupy rozliczeń |
| FriendlyName | Przyjazna nazwa grupy rozliczeń |
| Nazwa | Nazwa grupy rozliczeń |
| Typ | Typ grupy rozliczeń. Na przykład ProtectedContainer lub BackupItem |
| SourceSizeInMBs | Rozmiar frontonu grupy rozliczeń w MB |
| VaultStore_StorageConsumptionInMBs | Łączny magazyn w chmurze zużyty przez grupę rozliczeń w ramach warstwy Standardowa magazynu |
| BackupSolution | Rozwiązanie do tworzenia kopii zapasowych, z którym jest skojarzona Grupa rozliczeń. Na przykład kopia zapasowa maszyny wirtualnej platformy Azure, SQL w kopii zapasowej maszyny wirtualnej platformy Azure i tak dalej. |
| VaultResourceId | Identyfikator Azure Resource Manager (ARM) magazynu skojarzonego z grupą rozliczeń |
| VaultUniqueId | Klucz obcy, który odwołuje się do magazynu skojarzonego z grupą rozliczeń |
| VaultName | Nazwa magazynu skojarzonego z grupą rozliczeń |
| VaultTags | Tagi magazynu skojarzonego z grupą rozliczeń |
| VaultSubscriptionId | Identyfikator subskrypcji magazynu skojarzonego z grupą rozliczeń |
| VaultLocation | Lokalizacja magazynu skojarzonego z grupą rozliczeń |
| VaultStore_StorageReplicationType | Typ replikacji magazynu skojarzonego z grupą rozliczeń |
| Magazyn | Typ magazynu, czyli "Microsoft. RecoveryServices/magazyny" |
| TimeGenerated | Sygnatura czasowa rekordu |
| ExtendedProperties | Dodatkowe właściwości grupy rozliczeń |

## <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka przykładowych zapytań, które ułatwiają rozpoczęcie korzystania z funkcji systemowych.

- Wszystkie zadania kopii zapasowej maszyny wirtualnej platformy Azure zakończone niepowodzeniem w danym przedziale czasu

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Wszystkie zadania kopii zapasowej dziennika SQL w danym przedziale czasu

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tygodniowy trend magazynu kopii zapasowych zużyty dla maszyny wirtualnej "TestVM"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o raportach kopii zapasowych](./configure-reports.md)
---
title: Monitoruj Azure Backup z Azure Monitor
description: Monitoruj Azure Backup obciążenia i twórz niestandardowe alerty przy użyciu Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580745"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorowanie na dużą skalę przy użyciu Azure Monitor

Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie Recovery Services. Funkcje te są dostępne bez żadnej dodatkowej infrastruktury zarządzania. Jednak ta wbudowana usługa jest ograniczona w następujących scenariuszach:

- W przypadku monitorowania danych z wielu Recovery Services magazynów między subskrypcjami
- Jeśli preferowany kanał powiadomień *nie* jest adresem e-mail
- Jeśli użytkownicy chcą mieć alerty w celu uzyskania większej liczby scenariuszy
- Jeśli chcesz wyświetlić informacje z składnika lokalnego, takiego jak System Center Data Protection Manager na platformie Azure, które nie są wyświetlane w obszarze [**zadania tworzenia kopii zapasowej**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) lub [**alerty kopii zapasowych**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Używanie Log Analytics obszaru roboczego

### <a name="create-alerts-by-using-log-analytics"></a>Tworzenie alertów przy użyciu Log Analytics

W Azure Monitor można tworzyć własne alerty w obszarze roboczym Log Analytics. W obszarze roboczym możesz użyć *grup akcji platformy Azure* , aby wybrać preferowany mechanizm powiadamiania.

> [!IMPORTANT]
> Aby uzyskać informacje na temat kosztów tworzenia tego zapytania, zobacz [Azure monitor Cennik](https://azure.microsoft.com/pricing/details/monitor/).

Otwórz sekcję **Logs** w obszarze roboczym log Analytics i Utwórz zapytanie dotyczące własnych dzienników. Po wybraniu **nowej reguły alertu** zostanie otwarta strona tworzenie alertu Azure monitor, jak pokazano na poniższej ilustracji.

![Tworzenie alertu w obszarze roboczym Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

W tym miejscu zasób został już oznaczony jako obszar roboczy Log Analytics, a podano integrację z grupą akcji.

![Strona tworzenia alertu Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Warunek alertu

Definiowanie charakterystyki alertu to jego warunek wyzwalania. Wybierz opcję **warunek** , aby automatycznie załadować zapytanie Kusto na stronie **dzienników** , jak pokazano na poniższej ilustracji. W tym miejscu możesz edytować warunek, aby odpowiadał Twoim potrzebom. Aby uzyskać więcej informacji, zobacz [przykładowe zapytania Kusto](#sample-kusto-queries).

![Konfigurowanie warunku alertu](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

W razie potrzeby można edytować zapytanie Kusto. Wybierz próg, okres i częstotliwość. Próg określa, kiedy zostanie zgłoszony alert. Okres jest oknem czasu, w którym jest uruchamiane zapytanie. Na przykład jeśli próg jest większy niż 0, okres wynosi 5 minut, a częstotliwość wynosi 5 minut, a następnie reguła uruchamia kwerendę co 5 minut, przeglądając poprzedni 5 minut. Jeśli liczba wyników jest większa od 0, otrzymasz powiadomienie za pomocą wybranej grupy akcji.

> [!NOTE]
> Aby uruchomić regułę alertów raz dziennie, dla wszystkich zdarzeń/dzienników utworzonych w danym dniu, Zmień wartość obu wartości "period" i "częstotliwość" na 1440, czyli 24 godziny.

#### <a name="alert-action-groups"></a>Grupy akcji alertów

Użyj grupy akcji, aby określić kanał powiadomień. Aby wyświetlić dostępne mechanizmy powiadomień, w obszarze **grupy akcji** wybierz pozycję **Utwórz nowy**.

![Dostępne mechanizmy powiadamiania w oknie "Dodawanie grupy akcji"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Wszystkie wymagania związane z alertami i monitorowaniem można spełnić wyłącznie przed Log Analytics lub użyć Log Analytics, aby uzupełnić wbudowane powiadomienia.

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi przy użyciu Azure monitor](../azure-monitor/alerts/alerts-log.md) i [Tworzenie grup akcji w Azure Portal oraz zarządzanie nimi](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Wykresy domyślne dają Kusto zapytania dotyczące podstawowych scenariuszy, w których można tworzyć alerty. Możesz również zmodyfikować zapytania, aby pobrać dane, które mają być wyzwalane. Wklej następujące przykładowe zapytania Kusto na stronie **dzienniki** , a następnie utwórz alerty dla zapytań:

- Wszystkie zadania tworzenia kopii zapasowej zakończone powodzeniem

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Wszystkie zadania tworzenia kopii zapasowej zakończone niepowodzeniem

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Wszystkie pomyślne zadania tworzenia kopii zapasowej dziennika SQL

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Wszystkie pomyślne zadania agenta Azure Backup

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Magazyn kopii zapasowych zużyty na element kopii zapasowej

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Częstotliwość aktualizacji danych diagnostycznych

Dane diagnostyczne z magazynu są napompowane do obszaru roboczego Log Analytics z pewnym opóźnieniem. Każde zdarzenie dociera do obszaru roboczego Log Analytics od *20 do 30 minut* po wypchnięciu z magazynu Recovery Services. Poniżej znajdują się dalsze szczegółowe informacje na temat opóźnienia:

- W przypadku wszystkich rozwiązań alerty wbudowane w usłudze Backup są wypychane po ich utworzeniu. Tak więc zazwyczaj pojawiają się w obszarze roboczym Log Analytics po 20 – 30 minutach.
- W przypadku wszystkich rozwiązań zadania tworzenia kopii zapasowej na żądanie i zadania przywracania są wypychane po ich *zakończeniu*.
- W przypadku wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowane zadania tworzenia kopii zapasowej są wypychane zaraz po *zakończeniu*.
- W przypadku kopii zapasowej SQL, ponieważ kopie zapasowe dzienników mogą odbywać się co 15 minut, informacje dotyczące wszystkich ukończonych zadań tworzenia kopii zapasowej, w tym dzienników, są przetwarzane wsadowe i wypychane co 6 godzin.
- W przypadku wszystkich rozwiązań, inne informacje, takie jak element kopii zapasowej, zasady, punkty odzyskiwania, magazyn itd., są wypychane co najmniej *raz dziennie.*
- Zmiana konfiguracji kopii zapasowej (na przykład zmiana zasad lub zasad edycji) wyzwala wypychanie wszystkich powiązanych informacji o kopii zapasowej.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Korzystanie z dzienników aktywności magazynu Recovery Services

> [!CAUTION]
> Poniższe kroki dotyczą tylko *kopii zapasowych maszyn wirtualnych platformy Azure.* Nie można użyć tych kroków dla rozwiązań takich jak Agent Azure Backup, kopie zapasowe SQL na platformie Azure lub Azure Files.

Dzienników aktywności można także użyć do uzyskania powiadomień o zdarzeniach, takich jak powodzenie wykonywania kopii zapasowej. Aby rozpocząć, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure Portal.
2. Otwórz odpowiedni magazyn Recovery Services.
3. W oknie właściwości magazynu Otwórz sekcję **Dziennik aktywności** .

Aby zidentyfikować odpowiedni dziennik i utworzyć alert:

1. Sprawdź, czy otrzymujesz dzienniki aktywności dla udanych kopii zapasowych, stosując filtry pokazane na poniższej ilustracji. W razie potrzeby zmień wartość **TimeSpan** , aby wyświetlić rekordy.

   ![Filtrowanie w poszukiwaniu dzienników aktywności dla kopii zapasowych maszyny wirtualnej platformy Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Wybierz nazwę operacji, aby wyświetlić odpowiednie szczegóły.
3. Wybierz pozycję **Nowa reguła alertu** , aby otworzyć stronę **Tworzenie reguły** .
4. Utwórz alert, wykonując czynności opisane w temacie [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure monitor](../azure-monitor/alerts/alerts-activity-log.md).

   ![Nowa reguła alertu](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

W tym miejscu zasób jest magazynem Recovery Services. Powtórz te same kroki dla wszystkich magazynów, w których chcesz otrzymywać powiadomienia za pomocą dzienników aktywności. Warunek nie będzie miał wartości progowej, kropki lub częstotliwości, ponieważ ten alert jest oparty na zdarzeniach. Po wygenerowaniu odpowiedniego dziennika aktywności zostanie zgłoszony alert.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Monitorowanie na dużą skalę przy użyciu Log Analytics

Można wyświetlić wszystkie alerty utworzone na podstawie dzienników aktywności i obszarów roboczych Log Analytics w Azure Monitor. Po prostu otwórz okienko **alerty** po lewej stronie.

Mimo że można otrzymywać powiadomienia za pośrednictwem dzienników aktywności, zdecydowanie zalecamy używanie Log Analytics, a nie dzienników aktywności do monitorowania w odpowiedniej skali. Oto dlaczego:

- **Ograniczone scenariusze**: powiadomienia za pomocą dzienników aktywności dotyczą tylko kopii zapasowych maszyn wirtualnych platformy Azure. Powiadomienia muszą zostać skonfigurowane dla każdego magazynu Recovery Services.
- **Dopasowanie definicji**: działanie zaplanowanej kopii zapasowej nie jest zgodne z najnowszą definicją dzienników aktywności. Zamiast tego są wyrównane z [dziennikami zasobów](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). To wyrównanie powoduje nieoczekiwane skutki, gdy dane przepływają przez kanał dziennika aktywności są zmieniane.
- **Problemy z kanałem dziennika aktywności**: w magazynach Recovery Services, dzienniki aktywności, które są pompy Azure Backup podążają za nowym modelem. Niestety ta zmiana ma wpływ na generowanie dzienników aktywności w Azure Government, na platformie Azure (Niemcy) i na platformie Azure (Chiny). Jeśli użytkownicy tych usług w chmurze tworzą lub konfigurują alerty z dzienników aktywności w Azure Monitor, alerty nie zostaną wyzwolone. Ponadto w przypadku wszystkich regionów publicznych platformy Azure, jeśli użytkownik [zbiera Recovery Services dzienników aktywności w obszarze roboczym log Analytics](../azure-monitor/essentials/activity-log.md), te dzienniki nie będą wyświetlane.

Użyj Log Analytics obszaru roboczego do monitorowania i generowania alertów na dużą skalę dla wszystkich obciążeń chronionych przez Azure Backup.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć niestandardowe zapytania, zobacz [log Analytics model danych](backup-azure-reports-data-model.md).

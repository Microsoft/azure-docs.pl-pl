---
title: Użyj ustawień diagnostycznych dla Recovery Services magazynów
description: W tym artykule opisano, jak używać starych i nowych zdarzeń diagnostycznych dla Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575419"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Użyj ustawień diagnostycznych dla Recovery Services magazynów

Azure Backup wysyła zdarzenia diagnostyczne, które mogą być zbierane i używane na potrzeby analiz, alertów i raportów.

Ustawienia diagnostyczne dla magazynu Recovery Services można skonfigurować za pośrednictwem Azure Portal, przechodząc do magazynu i wybierając pozycję **Ustawienia diagnostyki**. Wybranie pozycji **+ Dodaj ustawienie diagnostyczne** umożliwia wysłanie co najmniej jednego zdarzenia diagnostycznego do konta magazynu, centrum zdarzeń lub obszaru roboczego log Analytics.

![Okienko ustawień diagnostycznych](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Zdarzenia diagnostyki dostępne dla Azure Backup użytkowników

Azure Backup udostępnia następujące zdarzenia diagnostyczne. Każde zdarzenie zawiera szczegółowe dane w określonym zestawie artefaktów związanych z kopiami zapasowymi:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Jeśli nadal używasz [starszej wersji usługi Event](#legacy-event) AzureBackupReport, zalecamy przełączenie do korzystania z powyższych zdarzeń.

Aby uzyskać więcej informacji, zobacz [model danych dla zdarzeń diagnostyki Azure Backup](./backup-azure-reports-data-model.md).

Dane dla tych zdarzeń można wysłać do konta magazynu, obszaru roboczego Log Analytics lub centrum zdarzeń. Jeśli dane są wysyłane do obszaru roboczego Log Analytics, wybierz pozycję przełącznik **specyficzny dla zasobów** na ekranie **Ustawienia diagnostyki** . Aby uzyskać więcej informacji, zobacz następujące sekcje.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Korzystanie z ustawień diagnostycznych w Log Analytics

Teraz można używać Azure Backup do wysyłania danych diagnostycznych magazynu do tabel dedykowanych Log Analytics na potrzeby tworzenia kopii zapasowych. Te tabele są nazywane [tabelami specyficznymi dla zasobów](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

Aby wysłać dane diagnostyczne magazynu do Log Analytics:

1. Przejdź do magazynu, a następnie wybierz pozycję **Ustawienia diagnostyczne**. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
1. Nadaj nazwę ustawieniu diagnostyki.
1. Zaznacz pole wyboru **Wyślij do log Analytics** a następnie wybierz obszar roboczy log Analytics.
1. Wybierz pozycję **zasób specyficzny** dla przełącznika i wybierz następujące sześć zdarzeń: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** i **AddonAzureBackupProtectedInstance**.
1. Wybierz pozycję **Zapisz**.

   ![Tryb specyficzny dla zasobów](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Po przeniesieniu danych do obszaru roboczego Log Analytics dedykowane tabele dla każdego z tych zdarzeń są tworzone w obszarze roboczym. Wszystkie te tabele można badać bezpośrednio. W razie potrzeby można także wykonać sprzężenia lub związki między tymi tabelami.

> [!IMPORTANT]
> Sześć zdarzeń, takich jak CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage i AddonAzureBackupProtectedInstance, są obsługiwane *tylko* w trybie specyficznym dla zasobów w [raportach kopii zapasowych](./configure-reports.md). *Jeśli spróbujesz wysłać dane dla tych sześciu zdarzeń w trybie Diagnostyka platformy Azure, w raportach kopii zapasowych nie będą widoczne żadne dane.*

## <a name="legacy-event"></a>Starsze zdarzenie

Tradycyjnie wszystkie dane diagnostyczne powiązane z kopią zapasową magazynu zostały zawarte w pojedynczym zdarzeniu o nazwie AzureBackupReport. Sześć zdarzeń opisanych poniżej to, w zasadzie, dekompozycja wszystkich danych zawartych w AzureBackupReport.

Obecnie nadal obsługujemy zdarzenia AzureBackupReport na potrzeby zgodności z poprzednimi wersjami w przypadkach, w których użytkownicy mają istniejące zapytania niestandardowe na tym zdarzeniu. Przykłady to niestandardowe alerty dzienników i niestandardowe wizualizacje. *Zalecamy przechodzenie do [nowych zdarzeń](#diagnostics-events-available-for-azure-backup-users) tak szybko, jak to możliwe*. Nowe zdarzenia:

* Znacznie Ułatwiaj pracę z danymi w kwerendach dzienników.
* Zapewnij lepszą wykrywalność schematów i ich strukturę.
* Zwiększ wydajność w przypadku opóźnień i czasów wykonywania zapytań.

*Starsze zdarzenie w trybie diagnostyki platformy Azure będzie ostatecznie przestarzałe. Wybranie nowych zdarzeń może pomóc w uniknięciu złożonych migracji w późniejszym terminie*. Nasze [rozwiązanie do raportowania](./configure-reports.md) , które używa log Analytics, będzie również zatrzymywać dane pomocnicze ze starszego zdarzenia.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Procedura przenoszenia do nowych ustawień diagnostycznych dla obszaru roboczego Log Analytics

1. Zidentyfikuj magazyny wysyłające dane do obszarów roboczych Log Analytics przy użyciu starszego zdarzenia i subskrypcji, do których należą. Uruchom następujące zapytanie w każdym z obszarów roboczych, aby zidentyfikować te magazyny i subskrypcje.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Poniżej znajduje się zrzut ekranu przedstawiający zapytanie uruchamiane w jednym z obszarów roboczych:

    ![Zapytanie obszaru roboczego](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Użyj [wbudowanych definicji Azure Policy](./azure-policy-configure-diagnostics.md) w Azure Backup, aby dodać nowe ustawienie diagnostyki dla wszystkich magazynów w określonym zakresie. Ta zasada dodaje nowe ustawienie diagnostyczne do magazynów, które nie mają ustawienia diagnostyki lub mają tylko starsze ustawienia diagnostyki. Te zasady można przypisywać jednocześnie do całej subskrypcji lub grupy zasobów. Musisz mieć dostęp właściciela do każdej subskrypcji, do której zasady są przypisane.

Możesz wybrać oddzielne ustawienia diagnostyki dla AzureBackupReport i sześciu nowych zdarzeń, dopóki nie zostaną zmigrowane wszystkie niestandardowe zapytania, aby używać danych z nowych tabel. Na poniższej ilustracji przedstawiono przykładowy magazyn, który ma dwa ustawienia diagnostyczne. Pierwsze ustawienie o nazwie **Setting1** wysyła dane zdarzenia AzureBackupReport do obszaru roboczego log Analytics w trybie diagnostyki platformy Azure. Drugie ustawienie o nazwie **Setting2** wysyła dane z sześciu nowych zdarzeń Azure Backup do log Analytics obszaru roboczego w trybie specyficznym dla zasobów.

![Dwa ustawienia](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Zdarzenie AzureBackupReport jest obsługiwane *tylko* w trybie diagnostyki platformy Azure. *Jeśli spróbujesz wysłać dane dla tego zdarzenia w trybie specyficznym dla zasobów, żadne dane nie będą przepływać do obszaru roboczego Log Analytics.*

> [!NOTE]
> Przełącznik dla **diagnostyki platformy Azure** lub **konkretnego zasobu** pojawia się tylko wtedy, gdy użytkownik wybierze opcję **Wyślij do log Analytics**. Aby wysłać dane na konto magazynu lub do centrum zdarzeń, użytkownik wybiera wymagane miejsce docelowe i zaznacza pola wyboru dla dowolnego z żądanych zdarzeń bez dodatkowych danych wejściowych. Firma Microsoft zaleca, aby nie wybierać starszego zdarzenia AzureBackupReport.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Wyślij Azure Site Recovery zdarzenia do Log Analytics

Zdarzenia Azure Backup i Azure Site Recovery są wysyłane z tego samego magazynu Recovery Services. Azure Site Recovery nie jest obecnie dostępna dla tabel specyficznych dla zasobów. Użytkownicy, którzy chcą wysyłać zdarzenia Azure Site Recovery do Log Analytics są kierowani do korzystania *tylko* z trybu diagnostyki platformy Azure, jak pokazano na obrazie. *Wybranie trybu specyficznego dla zasobów dla zdarzeń Azure Site Recovery uniemożliwi wysyłanie wymaganych danych do obszaru roboczego log Analytics*.

![Zdarzenia Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Podsumowując:

* Jeśli masz już skonfigurowaną Log Analytics diagnostykę z Diagnostyka Azure i masz na niej zapisaną kwerendę niestandardową, Zachowaj to ustawienie *bez zmian* , dopóki nie zostaną zmigrowane zapytania, aby używać danych z nowych zdarzeń.
* Jeśli chcesz również dodać do nowych tabel, zalecamy utworzenie **nowego** ustawienia diagnostycznego, wybierz opcję **specyficzne dla zasobów** i wybierz sześć nowych zdarzeń.
* Jeśli aktualnie wysyłasz zdarzenia Azure Site Recovery do Log Analytics, *nie* wybieraj trybu specyficznego dla zasobów dla tych zdarzeń. W przeciwnym razie dane dla tych zdarzeń nie będą przepływać do obszaru roboczego Log Analytics. Zamiast tego Utwórz dodatkowe ustawienie diagnostyczne, wybierz pozycję **Diagnostyka Azure** i wybierz odpowiednie zdarzenia Azure Site Recovery.

Na poniższej ilustracji przedstawiono przykład użytkownika, który ma trzy ustawienia diagnostyki dla magazynu. Pierwsze ustawienie o nazwie **Setting1** wysyła dane ze zdarzenia AzureBackupReport do obszaru roboczego log Analytics w trybie diagnostyki platformy Azure. Drugie ustawienie o nazwie **Setting2** wysyła dane z sześciu nowych zdarzeń Azure Backup do obszaru roboczego log Analytics w trybie specyficznym dla zasobu. Trzecie ustawienie o nazwie **Setting3** wysyła dane ze zdarzeń Azure Site Recovery do obszaru roboczego log Analytics w trybie diagnostycznym platformy Azure.

![Trzy ustawienia](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Następne kroki

[Poznaj model danych Log Analytics dla zdarzeń diagnostycznych](./backup-azure-reports-data-model.md)

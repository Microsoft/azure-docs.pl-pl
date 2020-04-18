---
title: Używanie ustawień diagnostyki dla magazynów usług odzyskiwania
description: W tym artykule opisano sposób korzystania ze starych i nowych zdarzeń diagnostycznych dla usługi Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617308"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Używanie ustawień diagnostyki dla magazynów usług odzyskiwania

Usługa Azure Backup wysyła zdarzenia diagnostyczne, które mogą być zbierane i używane do celów analizy, alertów i raportowania.

Ustawienia diagnostyki magazynu usług odzyskiwania można skonfigurować za pośrednictwem witryny Azure portal, przechodząc do magazynu i wybierając **ustawienia diagnostyki**. Wybranie **+ Dodawanie ustawienia diagnostycznego** umożliwia wysyłanie co najmniej jednego zdarzenia diagnostycznego do konta magazynu, centrum zdarzeń lub obszaru roboczego usługi Log Analytics.

![Okienko ustawień diagnostycznych](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Zdarzenia diagnostyczne dostępne dla użytkowników usługi Azure Backup

Usługa Azure Backup udostępnia następujące zdarzenia diagnostyczne. Każde zdarzenie zawiera szczegółowe dane dotyczące określonego zestawu artefaktów związanych z tworzeniem kopii zapasowych:

* CoreAzureBackup (właśc.
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolityka
* AddonAzureBackupStorage

Aby uzyskać więcej informacji, zobacz [Model danych dla zdarzeń diagnostycznych usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

Dane dla tych zdarzeń mogą być wysyłane do konta magazynu, obszaru roboczego usługi Log Analytics lub centrum zdarzeń. Jeśli wysyłasz te dane do obszaru roboczego usługi Log Analytics, wybierz przełącznik **Specyficzne dla zasobów** na ekranie Ustawienia **diagnostyki.** Aby uzyskać więcej informacji, zobacz następujące sekcje.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Używanie ustawień diagnostyki w usłudze Log Analytics

Teraz można użyć usługi Azure Backup do wysyłania danych diagnostycznych magazynu do dedykowanych tabel usługi Log Analytics do tworzenia kopii zapasowych. Tabele te są nazywane [tabelami specyficznymi dla zasobów](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Aby wysłać dane diagnostyczne przechowalni do usługi Log Analytics:

1. Przejdź do przechowalni i wybierz **pozycję Ustawienia diagnostyczne**. Wybierz **+ Dodaj ustawienie diagnostyczne**.
1. Nadaj nazwę ustawieniu diagnostycznemu.
1. Zaznacz pole wyboru **Wyślij do usługi Log Analytics** i wybierz obszar roboczy usługi Log Analytics.
1. Wybierz opcję **Specyficzne dla zasobu** w przełączniku i wybierz następujące sześć zdarzeń: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**i **AddonAzureBackupProtectedInstance**.
1. Wybierz pozycję **Zapisz**.

   ![Tryb specyficzny dla zasobu](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Po przepływie danych do obszaru roboczego usługi Log Analytics dedykowane tabele dla każdego z tych zdarzeń są tworzone w obszarze roboczym. Można zbadać dowolną z tych tabel bezpośrednio. W razie potrzeby można również wykonać sprzężenia lub związki między tymi tabelami.

> [!IMPORTANT]
> Sześć zdarzeń, a mianowicie CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage i AddonureBackupProtectedInstance, są obsługiwane *tylko* w trybie specyficznym dla zasobów w [raportach kopii zapasowej](https://docs.microsoft.com/azure/backup/configure-reports). *Jeśli spróbujesz wysłać dane dla tych sześciu zdarzeń w trybie diagnostyki platformy Azure, żadne dane nie będą widoczne w raportach kopii zapasowej.*

## <a name="legacy-event"></a>Zdarzenie legacy

Tradycyjnie wszystkie dane diagnostyczne związane z tworzeniem kopii zapasowych dla magazynu została zawarta w jednym zdarzeniu o nazwie AzureBackupReport. Sześć zdarzeń opisanych w tym miejscu są w istocie rozkładu wszystkich danych zawartych w AzureBackupReport. 

Obecnie nadal obsługuje zdarzenie AzureBackupReport dla zgodności z powrotem w przypadkach, gdy użytkownicy mają istniejące zapytania niestandardowe w tym zdarzeniu. Przykładami są niestandardowe alerty dziennika i wizualizacje niestandardowe. *Zalecamy jak najszybsze przejście do [nowych wydarzeń.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) * Nowe wydarzenia:

- Znacznie ułatwij pracę z danymi w kwerendach dziennika.
- Zapewnij lepszą wykrywalność schematów i ich struktury.
- Zwiększ wydajność zarówno w czasie opóźnienia pozyskiwania, jak i czasu wykonywania zapytań. 

*Starsze zdarzenie w trybie diagnostyki platformy Azure zostanie ostatecznie przestarzałe. Wybranie nowych zdarzeń może pomóc uniknąć złożonych migracji w późniejszym terminie.* Nasze [rozwiązanie raportowania,](https://docs.microsoft.com/azure/backup/configure-reports) które używa usługi Log Analytics, również przestanie obsługiwać dane ze starszego zdarzenia.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Kroki, aby przejść do nowych ustawień diagnostycznych dla obszaru roboczego usługi Log Analytics

1. Określ, które magazyny wysyłają dane do obszarów roboczych usługi Log Analytics przy użyciu starszego zdarzenia i subskrypcji, do których należą. Uruchom następujące obszary robocze, aby zidentyfikować te magazyny i subskrypcje.

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
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
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

1. Użyj [wbudowanych zasad platformy Azure w](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) usłudze Azure Backup, aby dodać nowe ustawienie diagnostyki dla wszystkich magazynów w określonym zakresie. Ta zasada dodaje nowe ustawienie diagnostyki do magazynów, które nie mają ustawienia diagnostyki lub mają tylko starsze ustawienie diagnostyczne. Te zasady można przypisać do całej subskrypcji lub grupy zasobów w czasie. Musisz mieć dostęp właściciela do każdej subskrypcji, do której jest przypisana zasada.

Możesz wybrać oddzielne ustawienia diagnostyki dla usługi AzureBackupReport i sześć nowych zdarzeń, dopóki nie zostaną zmigrowane wszystkie zapytania niestandardowe, aby użyć danych z nowych tabel. Na poniższej ilustracji przedstawiono przykład przechowalni, która ma dwa ustawienia diagnostyczne. Pierwsze ustawienie o nazwie **Setting1**wysyła dane zdarzenia Usługi AzureBackupReport do obszaru roboczego usługi Log Analytics w trybie diagnostyki platformy Azure. Drugie ustawienie o nazwie **Setting2**wysyła dane sześciu nowych zdarzeń usługi Azure Backup do obszaru roboczego usługi Log Analytics w trybie specyficznym dla zasobu.

![Dwa ustawienia](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Zdarzenie AzureBackupReport jest obsługiwane *tylko* w trybie diagnostyki platformy Azure. *Jeśli spróbujesz wysłać dane dla tego zdarzenia w trybie specyficznym dla zasobu, żadne dane nie będą przepływać do obszaru roboczego usługi Log Analytics.*

> [!NOTE]
> Przełącznik **diagnostyki platformy Azure** lub **specyficzne dla zasobu** pojawia się tylko wtedy, gdy użytkownik wybierze **wyślij do usługi Log Analytics**. Aby wysłać dane do konta magazynu lub centrum zdarzeń, użytkownik wybiera wymagane miejsce docelowe i wybiera pola wyboru dla dowolnego żądanego zdarzenia, bez żadnych dodatkowych danych wejściowych. Ponownie zaleca się, aby nie wybrać starsze zdarzenie AzureBackupReport w przyszłości.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Wysyłanie zdarzeń usługi Azure Site Recovery do usługi Log Analytics

Zdarzenia usługi Azure Backup i Azure Site Recovery są wysyłane z tego samego magazynu usług odzyskiwania. Usługa Azure Site Recovery nie jest obecnie dostępna dla tabel specyficznych dla zasobów. Użytkownicy, którzy chcą wysłać zdarzenia usługi Azure Site Recovery do usługi Log Analytics, są kierowani do korzystania *tylko*z trybu diagnostyki platformy Azure , jak pokazano na obrazie. *Wybranie trybu specyficznego dla zasobów dla zdarzeń usługi Azure Site Recovery uniemożliwi przesyłanie wymaganych danych do obszaru roboczego usługi Log Analytics.*

![Zdarzenia odzyskiwania witryny](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Podsumowując:

* Jeśli masz już diagnostykę usługi Log Analytics skonfigurowany z diagnostyki platformy Azure i pisemne zapytania niestandardowe na nim, zachować to ustawienie *nienaruszone,* dopóki nie migracji zapytań do korzystania z danych z nowych zdarzeń.
* Jeśli chcesz również przywlecić nowe tabele, zgodnie z zaleceniami, utwórz **nowe** ustawienie diagnostyki, wybierz **opcję Specyficzne dla zasobu**i wybierz sześć nowych zdarzeń.
* Jeśli obecnie wysyłasz zdarzenia usługi Azure Site Recovery do usługi Log Analytics, *nie* należy wybierać trybu specyficznego dla tych zdarzeń. W przeciwnym razie dane dla tych zdarzeń nie będą przepływać do obszaru roboczego usługi Log Analytics. Zamiast tego utwórz dodatkowe ustawienie diagnostyczne, wybierz **diagnostykę platformy Azure**i wybierz odpowiednie zdarzenia usługi Azure Site Recovery.

Na poniższej ilustracji przedstawiono przykład użytkownika, który ma trzy ustawienia diagnostyki dla przechowalni. Pierwsze ustawienie o nazwie **Setting1**wysyła dane ze zdarzenia AzureBackupReport do obszaru roboczego usługi Log Analytics w trybie diagnostyki platformy Azure. Drugie ustawienie o nazwie **Setting2**wysyła dane z sześciu nowych zdarzeń usługi Azure Backup do obszaru roboczego usługi Log Analytics w trybie specyficznym dla zasobu. Trzecie ustawienie o nazwie **Setting3**wysyła dane ze zdarzeń usługi Azure Site Recovery do obszaru roboczego usługi Log Analytics w trybie diagnostyki platformy Azure.

![Trzy ustawienia](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Następne kroki

[Poznaj model danych usługi Log Analytics dla zdarzeń diagnostycznych](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

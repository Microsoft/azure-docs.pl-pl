---
title: Dzienniki Update Management Azure Automation zapytań
description: W tym artykule opisano sposób wykonywania zapytań w dziennikach pod kątem Update Management w obszarze roboczym Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592868"
---
# <a name="query-update-management-logs"></a>Wykonywanie zapytań względem dzienników rozwiązania Update Management

Oprócz szczegółów, które są dostępne podczas wdrażania Update Management, można wyszukiwać dzienniki przechowywane w Log Analytics obszarze roboczym. Aby wyszukać dzienniki na koncie usługi Automation, wybierz pozycję **Update Management** i Otwórz obszar roboczy log Analytics skojarzony z danym wdrożeniem.

Możesz również dostosować zapytania dziennika lub użyć ich od różnych klientów. Zobacz [dokumentację interfejsu API wyszukiwania log Analytics](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Zapytanie rekordów aktualizacji

Update Management zbiera rekordy dotyczące maszyn wirtualnych z systemami Windows i Linux oraz typów danych, które są wyświetlane w wynikach przeszukiwania dzienników. W poniższych sekcjach opisano te rekordy.

### <a name="query-required-updates"></a>Zapytanie dotyczące wymaganych aktualizacji

Tworzony jest rekord typu `RequiredUpdate` , który reprezentuje aktualizacje wymagane przez komputer. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. |
| PublishDate | Data gotowości do pobrania i zainstalowania aktualizacji z Windows Update. |
| Serwer | | 
| SourceHealthServiceId | Unikatowy identyfikator reprezentujący identyfikator Log Analytics agenta systemu Windows. |
| SourceSystem | *OperationsManager* |
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie organizacji Azure Active Directory. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Typ | *Aktualizowanie* |
| UpdateClassification | Wskazuje typ aktualizacji, które mogą być stosowane. W przypadku systemu Windows:<br> *Aktualizacje krytyczne*<br> *Aktualizacje zabezpieczeń*<br> *Pakiety zbiorcze aktualizacji*<br> *Pakiety funkcji*<br> *Dodatki Service Pack*<br> *Aktualizacje definicji*<br> *Narzędzia*<br> *Aktualizacje*. W przypadku systemu Linux:<br> *Aktualizacje krytyczne i zabezpieczeń*<br> *Inne* |
| UpdateSeverity | Klasyfikacja ważności luki w zabezpieczeniach. Wartości to:<br> *Krytyczne*<br> *Ważne*<br> *Umiarkowane*<br> *Niski* |
| UpdateTitle | Tytuł aktualizacji.|

### <a name="query-update-record"></a>Kwerenda aktualizacji rekordu

Tworzony jest rekord typu `Update` , który reprezentuje dostępne aktualizacje i ich stan instalacji dla komputera. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|----------|-------------|
| ApprovalSource | Dotyczy tylko systemu operacyjnego Windows. Źródło zatwierdzenia dla rekordu. Wartość jest Microsoft Update. |
| Approved (Zatwierdzono) | Ma wartość true, jeśli rekord został zatwierdzony lub w przeciwnym razie ma wartość false. |
| Klasyfikacja | Klasyfikacja zatwierdzania. Wartość to aktualizacje. |
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | Naturalne. Możliwe wartości to Azure lub non-Azure. |
| MSRCBulletinID | Numer IDENTYFIKACYJNy biuletynu zabezpieczeń. |
| MSRCSeverity | Klasyfikacja ważności luki w zabezpieczeniach. Wartości to:<br> Krytyczne<br> Ważne<br> Umiarkowane<br> Niski |  
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| UpdateID | Unikatowy identyfikator aktualizacji oprogramowania. |
| RevisionNumber | Numer poprawki określonej poprawki aktualizacji. |
| Opcjonalne | Ma wartość true, jeśli rekord jest opcjonalny lub w przeciwnym razie ma wartość false. |
| RebootBehavior | Zachowanie ponownego uruchomienia po zainstalowaniu/odinstalowaniu aktualizacji. |
| _ResourceId | Unikatowy identyfikator zasobu skojarzonego z rekordem. |
| Typ | Typ rekordu. Wartość to Update. |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| MG | Unikatowy identyfikator grupy zarządzania lub Log Analytics obszaru roboczego. |
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie Azure Active Directory organizacji. |
| SourceSystem | System źródłowy dla rekordu. Wartość to `OperationsManager`. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. |
| Tytuł | Tytuł aktualizacji. |
| PublishedDate (UTC) | Data, kiedy aktualizacja jest gotowa do pobrania i zainstalowania z Windows Update.  |
| UpdateState | Bieżący stan aktualizacji. |
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. |
| ResourceGroup | Nazwa grupy zasobów, do której należy zasób. |
| ResourceProvider | Dostawca zasobów. |
| Zasób | Nazwa zasobu. |
| ResourceType | Typ zasobu. |

### <a name="query-update-agent-record"></a>Kwerenda aktualizacji rekordu agenta

Tworzony jest rekord typu `UpdateAgent` , który zawiera szczegółowe informacje o agencie aktualizacji na komputerze. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| OSVersion | Wersja systemu operacyjnego. |
| Serwer | |
| SourceHealthServiceId | Unikatowy identyfikator reprezentujący identyfikator Log Analytics agenta systemu Windows. |
| SourceSystem | System źródłowy dla rekordu. Wartość to `OperationsManager`. |
| TenantId | Unikatowy identyfikator reprezentujący wystąpienie Azure Active Directory organizacji. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Typ | Typ rekordu. Wartość to Update. |
| WindowsUpdateAgentVersion | Wersja agenta Windows Update. |
| WSUSServer | Błędy, jeśli wystąpił problem z agentem Windows Update w celu ułatwienia rozwiązywania problemów. |

### <a name="query-update-deployment-status-record"></a>Rekord stanu wdrożenia aktualizacji zapytania

Tworzony jest rekord typu `UpdateRunProgress` , który zapewnia stan wdrożenia aktualizacji zaplanowane wdrożenie według maszyny. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | Naturalne. Wartości to Azure lub non-Azure. |
| CorrelationId | Unikatowy identyfikator zadania elementu Runbook uruchomionego dla aktualizacji. |
| EndTime | Godzina, o której zakończył się proces synchronizacji. *Ta właściwość nie jest obecnie używana. Zobacz TimeGenerated.* |
| ErrorResult | Windows Update wygenerowany kod błędu, jeśli instalacja aktualizacji nie powiedzie się. |
| InstallationStatus | Możliwe stany instalacji aktualizacji na komputerze klienckim,<br> `NotStarted` -zadanie nie zostało jeszcze wyzwolone.<br> `FailedToStart` -nie można uruchomić zadania na komputerze.<br> `Failed` — zadanie zostało uruchomione, ale nie powiodło się z powodu wyjątku.<br> `InProgress` — zadanie jest w toku.<br> `MaintenanceWindowExceeded` -Jeśli wykonywanie pozostało, ale osiągnięto interwał okna obsługi.<br> `Succeeded` — zadanie zakończyło się pomyślnie.<br> `InstallFailed` -nie można pomyślnie zainstalować aktualizacji.<br> `NotIncluded`<br> `Excluded` |
| KBID | Identyfikator artykułu bazy wiedzy dla usługi Windows Update. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| OSType | Typ systemu operacyjnego. Wartości to Windows lub Linux. |
| Produkt | Produkty, dla których ma zastosowanie aktualizacja. |
| Zasób | Nazwa zasobu. |
| ResourceId | Unikatowy identyfikator zasobu skojarzonego z rekordem. |
| ResourceProvider | Dostawca zasobów. |
| ResourceType | Typ zasobu. |
| SourceComputerId | Unikatowy identyfikator reprezentujący komputer źródłowy. |
| SourceSystem | System źródłowy dla rekordu. Wartość to `OperationsManager`. |
| StartTime | Godzina, o której zaplanowano zainstalowanie aktualizacji. *Ta właściwość nie jest obecnie używana. Zobacz TimeGenerated.* |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. |
| SucceededOnRetry | Wartość wskazująca, czy wykonanie aktualizacji nie powiodło się przy pierwszej próbie, a bieżąca operacja jest ponowieniem próby. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| Tytuł | Tytuł aktualizacji. |
| Typ | Typ aktualizacji. Wartość to `UpdateRunProgress`. |
| UpdateId | Unikatowy identyfikator aktualizacji oprogramowania. |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| ResourceId | Unikatowy identyfikator zasobu skojarzonego z rekordem. |

### <a name="query-update-summary-record"></a>Rekord podsumowania aktualizacji zapytania

Tworzony jest rekord typu `UpdateSummary` , który zawiera podsumowanie aktualizacji według maszyn. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|----------|-------------|
| Computer (Komputer) | W pełni kwalifikowana nazwa domeny komputera raportowania. |
| ComputerEnvironment | Naturalne. Wartości to Azure lub non-Azure. |
| CriticalUpdatesMissing | Liczba dostępnych ważnych aktualizacji krytycznych. |
| ManagementGroupName | Nazwa grupy zarządzania Operations Manager lub Log Analytics obszaru roboczego. |
| NETRuntimeVersion | Wersja .NET Framework zainstalowana na komputerze z systemem Windows. |
| OldestMissingSecurityUpdateBucket | Specyfikator najstarszego brakującego zasobnika zabezpieczeń. Wartości to:<br> Ostatnie Jeśli wartość jest mniejsza niż 30 dni<br> 30 dni temu<br> 60 dni temu<br> 90 dni temu<br> 120 dni temu<br> 150 dni temu<br> 180 dni temu<br> Starsze, gdy wartość jest większa niż 180 dni. |
| OldestMissingSecurityUpdateInDays | Całkowita liczba dni dla najstarszej wykrytej aktualizacji, która nie została zainstalowana. |
| OsVersion | Wersja systemu operacyjnego. |
| OtherUpdatesMissing | Brak liczby wykrytych aktualizacji. |
| Zasób | Nazwa zasobu dla rekordu. |
| ResourceGroup | Nazwa grupy zasobów zawierającej zasób. |
| ResourceId | Unikatowy identyfikator zasobu skojarzonego z rekordem. |
| ResourceProvider | Dostawca zasobów. |
| ResourceType | Typ zasobu. |
| RestartPending | Ma wartość true, jeśli oczekiwanie na ponowne uruchomienie lub w przeciwnym razie ma wartość false. |
| SecurityUpdatesMissing | Liczba obowiązujących brakujących aktualizacji zabezpieczeń.|
| SourceComputerId | Unikatowy identyfikator dla maszyny wirtualnej. |
| SourceSystem | System źródłowy dla rekordu. Wartość to `OpsManager`. |
| SubscriptionId | Unikatowy identyfikator subskrypcji platformy Azure. |
| TimeGenerated | Data i godzina utworzenia rekordu. |
| TotalUpdatesMissing | Łączna liczba dostępnych aktualizacji, które mają zastosowanie. |
| Typ | Typ rekordu. Wartość to `UpdateSummary`. |
| VMUUID | Unikatowy identyfikator dla maszyny wirtualnej. |
| WindowsUpdateAgentVersion | Wersja agenta Windows Update. |
| WindowsUpdateSetting | Stan agenta Windows Update. Możliwe wartości:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Błędy, jeśli wystąpił problem z agentem Windows Update w celu ułatwienia rozwiązywania problemów. |
| _ResourceId | Unikatowy identyfikator zasobu skojarzonego z rekordem. |

## <a name="sample-queries"></a>Przykładowe zapytania

Poniższe sekcje zawierają przykładowe zapytania dzienników dla rekordów aktualizacji, które są zbierane dla Update Management.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Upewnij się, że dla Update Management są włączone maszyny spoza platformy Azure

Aby upewnić się, że bezpośrednio połączone maszyny komunikują się z dziennikami Azure Monitor, Uruchom jedno z poniższych wyszukiwań w dziennikach.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na komputerze z systemem Windows można przejrzeć następujące informacje w celu zweryfikowania łączności agentów z dziennikami Azure Monitor:

1. W panelu sterowania Otwórz **Microsoft Monitoring Agent**. Na karcie **log Analytics Azure** Agent wyświetli następujący komunikat: **Microsoft Monitoring Agent pomyślnie nawiązał połączenie z log Analytics**.

1. Otwórz dziennik zdarzeń systemu Windows. Przejdź do pozycji **Application and Services Logs\Operations Manager** i Wyszukaj zdarzenia o identyfikatorze 3000 i identyfikatorze 5002 z **łącznika usługi** źródłowej. Te zdarzenia informują o tym, że komputer został zarejestrowany w obszarze roboczym usługi Log Analytics i odbiera konfigurację.

Jeśli Agent nie może komunikować się z dziennikami Azure Monitor i Agent jest skonfigurowany do komunikacji z Internetem za pomocą zapory lub serwera proxy, upewnij się, że zapora lub serwer proxy zostały prawidłowo skonfigurowane. Aby dowiedzieć się, jak upewnić się, że zapora lub serwer proxy są prawidłowo skonfigurowane, zobacz [Konfiguracja sieci dla agenta systemu Windows](../../azure-monitor/agents/agent-windows.md) lub [Konfiguracja sieci dla agentów](../../azure-monitor/vm/quick-collect-linux-computer.md)z systemem Linux.

> [!NOTE]
> Jeśli systemy Linux są skonfigurowane do komunikacji z serwerem proxy lub Log Analytics bramą i włączasz Update Management, zaktualizuj uprawnienia, `proxy.conf` Aby przyznać grupie omiuser uprawnienia do odczytu w pliku przy użyciu następujących poleceń:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodani agenci systemu Linux pokazują stan **aktualizacji** po wykonaniu oceny. Ten proces może potrwać do 6 godzin.

Aby upewnić się, że Operations Manager grupy zarządzania komunikuje się z dziennikami Azure Monitor, zobacz temat [Weryfikuj integrację Operations Manager z dziennikami Azure monitor](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (system Windows)

Zastąp wartość VMUUID wartością GUID maszyny wirtualnej, która jest używana do wykonywania zapytań. Możesz znaleźć VMUUID, które powinny być używane, uruchamiając następujące zapytanie w Azure Monitor dziennikach: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (Linux)

W przypadku niektórych dystrybucje systemu Linux występuje niezgodność [bajtów](https://en.wikipedia.org/wiki/Endianness) z wartością VMUUID, która pochodzi z Azure Resource Manager i co jest przechowywane w dziennikach Azure monitor. Poniższe zapytanie sprawdza zgodność z dowolną przyciągiem. Zastąp wartości VMUUID wartościami formatu big-endian i little-endian identyfikatora GUID, aby prawidłowo zwrócić wyniki. Możesz znaleźć VMUUID, które powinny być używane, uruchamiając następujące zapytanie w Azure Monitor dziennikach: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Zapytania oceny z obsługą wiele maszyn wirtualnych

#### <a name="computers-summary"></a>Podsumowanie komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje dotyczące dzienników Azure Monitor, zobacz [dzienniki Azure monitor](../../azure-monitor/logs/log-query-overview.md).
* Aby uzyskać pomoc dotyczącą alertów, zobacz [Konfigurowanie alertów](configure-alerts.md).

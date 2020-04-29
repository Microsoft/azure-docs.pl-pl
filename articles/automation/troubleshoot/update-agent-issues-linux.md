---
title: Rozwiązywanie problemów z usługą Linux Update Agent w Azure Automation Update Management
description: Informacje na temat rozwiązywania problemów z usługą Windows Update Agent przy użyciu rozwiązania Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679070"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Rozwiązywanie problemów z usługą Linux Update Agent

Może istnieć wiele przyczyn, dla których maszyna nie jest wyświetlana jako gotowa (dobra kondycja) w Update Management. W Update Management można sprawdzić kondycję agenta hybrydowego procesu roboczego elementu Runbook w celu ustalenia podstawowego problemu. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla maszyn platformy Azure z poziomu maszyn Azure Portal i spoza platformy Azure w [scenariuszu w trybie offline](#troubleshoot-offline). 

Na poniższej liście przedstawiono trzy Stany gotowości, w których może znajdować się maszyna:

* Gotowy — został wdrożony hybrydowy proces roboczy elementu Runbook, który był ostatnio widoczny poniżej 1 godzinę temu.
* Rozłączono — został wdrożony hybrydowy proces roboczy elementu Runbook, który był ostatnio widoczny ponad 1 godzinę temu.
* Nie skonfigurowano — nie można odnaleźć hybrydowego procesu roboczego elementu Runbook lub nie zakończono dołączania.

> [!NOTE]
> Może istnieć niewielkie opóźnienie między elementami Azure Portal a bieżącym stanem maszyny.

## <a name="start-the-troubleshooter"></a>Uruchom narzędzie do rozwiązywania problemów

W przypadku maszyn platformy Azure kliknij link **Rozwiązywanie problemów** w kolumnie **Aktualizacja gotowości agenta** w portalu spowoduje uruchomienie strony Rozwiązywanie problemów z aktualizacją agenta. W przypadku maszyn spoza platformy Azure Link umożliwia przełączenie do tego artykułu. Zapoznaj się z instrukcjami w trybie offline, aby rozwiązać problem z maszyną spoza platformy Azure.

![Strona listy maszyn wirtualnych](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Sprawdzanie wymaga, aby maszyna wirtualna była uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, zobaczysz przycisk **Uruchom maszynę wirtualną** .

Na stronie Rozwiązywanie problemów z aktualizacją agenta kliknij pozycję **Uruchom testy**, aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [polecenia Uruchom](../../virtual-machines/linux/run-command.md) , aby uruchomić skrypt na komputerze w celu zweryfikowania zależności. Po ukończeniu narzędzia do rozwiązywania problemów zwraca wynik kontroli.

![Strona rozwiązywania problemów](../media/update-agent-issues-linux/troubleshoot-page.png)

Po zakończeniu wyniki są zwracane w oknie. Sekcje sprawdzania zawierają informacje o tym, co szuka każdy test.

![Strona sprawdzania agentów aktualizacji](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego sprawdza, czy hybrydowy proces roboczy elementu Runbook uruchamia jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Stosowanie poprawek opartych na klasyfikacji wymaga, aby element "yum" zwracał dane zabezpieczeń, które nie znajdują się w polu CentOS.         |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 LTS (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

## <a name="monitoring-agent-service-health-checks"></a>Sprawdzanie kondycji usługi agenta monitorowania

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Ten test zapewnia, że Agent Log Analytics dla systemu Linux jest zainstalowany. Aby uzyskać instrukcje dotyczące sposobu instalacji, zobacz [Instalowanie agenta dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Stan agenta Log Analytics

Ten test zapewnia, że Agent Log Analytics dla systemu Linux jest uruchomiony. Jeśli Agent nie jest uruchomiony, możesz uruchomić następujące polecenie, aby spróbować uruchomić go ponownie. Aby uzyskać więcej informacji na temat rozwiązywania problemów z agentem, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Wieloadresowości

Ta kontrola określa, czy Agent wysyła raporty do wielu obszarów roboczych. Obsługa multihostingu nie jest obsługiwana przez Update Management.

### <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

Ten test sprawdza, czy Agent Log Analytics dla systemu Linux ma pakiet hybrydowego procesu roboczego elementu Runbook. Ten pakiet jest wymagany do pracy Update Management.

### <a name="hybrid-runbook-worker-status"></a>Stan hybrydowego procesu roboczego elementu Runbook

To sprawdzenie gwarantuje, że hybrydowy proces roboczy elementu Runbook jest uruchomiony na komputerze. Jeśli hybrydowy proces roboczy elementu Runbook działa prawidłowo, powinny być obecne następujące procesy. Aby dowiedzieć się więcej, zobacz [Rozwiązywanie problemów z agentem log Analytics dla systemu Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Sprawdzenia łączności

### <a name="general-internet-connectivity"></a>Ogólna łączność z Internetem

Ten test sprawdza, czy komputer ma dostęp do Internetu.

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

Ta kontrola określa, czy hybrydowy proces roboczy elementu Runbook może prawidłowo komunikować się z Azure Automation Log Analytics obszaru roboczego.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego elementu Runbook na komunikowanie się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Punkt końcowy operacji

Ta kontrola określa, czy Agent może prawidłowo komunikować się z usługą danych czasu wykonywania zadania.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego elementu Runbook na komunikowanie się z usługą danych czasu wykonywania zadania. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics punkt końcowy 1

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics punkt końcowy 2

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics punkt końcowy 3

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Aby użyć narzędzia do rozwiązywania problemów w trybie offline w hybrydowym procesie roboczym elementu Runbook, należy uruchomić skrypt lokalnie. Skrypt w języku Python, [update_mgmt_health_check. PR](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) , można znaleźć w centrum skryptów. Przykład danych wyjściowych tego skryptu pokazano w następującym przykładzie:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać dodatkowe problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów — hybrydowe procesy robocze elementów Runbook](hybrid-runbook-worker.md).

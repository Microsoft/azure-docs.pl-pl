---
title: Rozwiązywanie problemów z agentem aktualizacji systemu Linux w usłudze Azure Automation Update Management
description: Dowiedz się, jak rozwiązywać problemy z agentem aktualizacji systemu Linux i windows przy użyciu rozwiązania Do zarządzania aktualizacjami.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679070"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Rozwiązywanie problemów z agentem aktualizacji systemu Linux

Może istnieć wiele powodów, dla których komputer nie jest wyświetlany jako gotowy (zdrowy) w zarządzaniu aktualizacjami. W zarządzanie aktualizacjami można sprawdzić kondycję agenta administracyjnego hybrydowego systemu runbook, aby określić podstawowy problem. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla komputerów platformy Azure z witryny Azure portal i komputerów innych niż Azure w [scenariuszu offline.](#troubleshoot-offline) 

Poniższa lista to trzy stany gotowości, w których może znajdować się maszyna:

* Gotowy — hybrydowy proces roboczy żyła pracy jest wdrażany i był ostatnio widziany mniej niż 1 godzinę temu.
* Rozłączony — hybrydowy proces roboczy uruchomieniu jest wdrożony i był ostatnio widziany ponad 1 godzinę temu.
* Nie skonfigurowano — nie znaleziono lub nie zostało ukończone dołączanie.

> [!NOTE]
> Może wystąpić niewielkie opóźnienie między tym, co pokazuje portal Azure i bieżącego stanu komputera.

## <a name="start-the-troubleshooter"></a>Uruchamianie narzędzia do rozwiązywania problemów

W przypadku komputerów platformy Azure kliknięcie łącza **Rozwiązywanie problemów** w kolumnie **Gotowość agenta aktualizacji** w portalu uruchamia stronę Agent aktualizacji rozwiązywania problemów. W przypadku komputerów innych niż platformy Azure łącze prowadzi do tego artykułu. Zapoznaj się z instrukcjami offline, aby rozwiązać problem z komputerem nienawiązanym z platformą Azure.

![strona listy maszyn wirtualnych](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrole wymagają maszyny Wirtualnej do uruchomienia. Jeśli maszyna wirtualna nie jest uruchomiona, zostanie wyświetlony przycisk **Rozpocznij maszynę wirtualną.**

Na stronie Agent rozwiązywania problemów z aktualizacją kliknij pozycję **Uruchom czeki**, aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [polecenia Uruchom](../../virtual-machines/linux/run-command.md) do uruchomienia skryptu na komputerze w celu zweryfikowania zależności. Po zakończeniu narzędzia do rozwiązywania problemów zwraca wynik kontroli.

![Strona Rozwiązywanie problemów](../media/update-agent-issues-linux/troubleshoot-page.png)

Po zakończeniu wyniki są zwracane w oknie. Sekcje kontrolne zawierają informacje o tym, czego szuka każdy czek.

![Strona sprawdzania agenta aktualizacji](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego sprawdza, czy hybrydowy proces roboczy systemu runbook jest uruchomiony jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Łatanie oparte na klasyfikacji wymaga "mniam", aby zwrócić dane bezpieczeństwa, których CentOS nie ma po wyjęciu z pudełka.         |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS, 16.04 LTS i 18.04 LTS (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

## <a name="monitoring-agent-service-health-checks"></a>Kontrole kondycji usługi agenta monitorującego

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Ten check zapewnia, że agent usługi Log Analytics dla systemu Linux jest zainstalowany. Aby uzyskać instrukcje dotyczące instalowania go, zobacz [Instalowanie agenta dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Stan agenta usługi Log Analytics

Ten sprawdzanie gwarantuje, że agent usługi Log Analytics dla systemu Linux jest uruchomiony. Jeśli agent nie jest uruchomiony, można uruchomić następujące polecenie, aby spróbować go ponownie uruchomić. Aby uzyskać więcej informacji na temat rozwiązywania problemów z agentem, zobacz [Rozwiązywanie problemów z systemem Linux Hybrid Runbook worker](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

To sprawdzenie określa, czy agent zgłasza się do wielu obszarów roboczych. Multi-homing nie jest obsługiwany przez zarządzanie aktualizacjami.

### <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

To sprawdzenie sprawdza, czy agent usługi Log Analytics dla systemu Linux ma pakiet hybrydowego procesu roboczego systemu runbook. Ten pakiet jest wymagany do działania zarządzania aktualizacjami.

### <a name="hybrid-runbook-worker-status"></a>Stan hybrydowego procesu roboczego egojca

Ten czek upewnia się, że hybrydowy proces roboczy uruchomieniu jest na komputerze. Następujące procesy powinny być obecne, jeśli hybrydowy proces roboczy elementów runbook działa poprawnie. Aby dowiedzieć się więcej, zobacz [rozwiązywanie problemów z agentem analizy dzienników dla systemu Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kontrole łączności

### <a name="general-internet-connectivity"></a>Ogólna łączność z Internetem

Ta kontrola zapewnia, że urządzenie ma dostęp do Internetu.

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

Ten czek określa, czy hybrydowy proces roboczy niustrów może poprawnie komunikować się z usługą Azure Automation w obszarze roboczym usługi Log Analytics.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego systemu runbook na komunikowanie się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla pracowników hybrydowych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Punkt końcowy operacji

Ten czek określa, czy agent może poprawnie komunikować się z usługą danych środowiska wykonawczego zadania.

Konfiguracje serwera proxy i zapory muszą zezwalać agentowi hybrydowego procesu roboczego uruchomieniu na środowisko pracy na komunikowanie się z usługą danych środowiska wykonawczego zadania. Aby uzyskać listę adresów i portów do otwarcia, zobacz [Planowanie sieci dla pracowników hybrydowych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Punkt końcowy analizy dzienników 1

To sprawdzenie sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Punkt końcowy analizy dzienników 2

To sprawdzenie sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Punkt końcowy analizy dzienników 3

To sprawdzenie sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Narzędzia do rozwiązywania problemów można używać w trybie offline w hybrydowym ustępuje procesowi roboczemu podręcznika runbooka, uruchamiając skrypt lokalnie. Skrypt [pythona, update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) można znaleźć w Centrum skryptów. Przykład danych wyjściowych tego skryptu jest pokazany w poniższym przykładzie:

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

Aby rozwiązać dodatkowe problemy z hybrydowymi pracownikami uruchomieniu, zobacz [Rozwiązywanie problemów — Hybrydowe środowiska owe dzienników ceń](hybrid-runbook-worker.md).

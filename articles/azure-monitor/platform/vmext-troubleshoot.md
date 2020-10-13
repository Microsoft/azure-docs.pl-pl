---
title: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej Log Analytics platformy Azure
description: Opisz objawy, przyczyny i rozwiązywanie typowych problemów z rozszerzeniem maszyny wirtualnej Log Analytics dla maszyn wirtualnych platformy Azure z systemem Windows i Linux.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 2e96ac5052221475d9aec11d4ed96e8f9c308d70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710111"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej usługi Log Analytics w usłudze Azure Monitor
Ten artykuł zawiera informacje dotyczące rozwiązywania problemów, które mogą wystąpić w przypadku Log Analytics rozszerzenia maszyny wirtualnej dla maszyn wirtualnych z systemem Windows i Linux działających w systemie Microsoft Azure, a także sugeruje rozwiązania, które pozwolą rozwiązać te problemy.

Aby sprawdzić stan rozszerzenia, wykonaj następujące kroki w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W Azure Portal kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **maszyny wirtualne**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Maszyny wirtualne**.
3. Na liście maszyn wirtualnych Znajdź i wybierz ją.
3. Na maszynie wirtualnej kliknij pozycję **rozszerzenia**.
4. Sprawdź, czy na liście jest włączone rozszerzenie Log Analytics.  W przypadku systemu Linux Agent jest wymieniony jako **OMSAgentforLinux** i w systemie Windows Agent jest wymieniony jako **MicrosoftMonitoringAgent**.

   ![Widok rozszerzenia maszyny wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknij rozszerzenie, aby wyświetlić szczegóły. 

   ![Szczegóły rozszerzenia maszyny wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej systemu Windows Azure

Jeśli rozszerzenie maszyny wirtualnej *Microsoft Monitoring Agent* nie jest instalowane ani raportowania, można wykonać poniższe kroki, aby rozwiązać problem.

1. Sprawdź, czy Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa poprawnie, wykonując czynności opisane w temacie [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Możesz również przejrzeć plik dziennika agenta maszyny wirtualnej `C:\WindowsAzure\logs\WaAppAgent.log`
   * Jeśli dziennik nie istnieje, Agent maszyny wirtualnej nie jest zainstalowany.
   * [Zainstaluj agenta maszyny wirtualnej platformy Azure](../learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Przejrzyj pliki dziennika rozszerzenia maszyny wirtualnej Microsoft Monitoring Agent w `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Upewnij się, że maszyna wirtualna może uruchamiać skrypty programu PowerShell
4. Upewnij się, że uprawnienia do C:\Windows\Temp. nie zostały zmienione
5. Aby wyświetlić stan Microsoft Monitoring Agent, wpisz następujące polecenie w oknie programu PowerShell z podwyższonym poziomem uprawnień na maszynie wirtualnej. `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Przejrzyj pliki dziennika instalacji Microsoft Monitoring Agent w `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami systemu Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej systemu Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Jeśli rozszerzenie maszyny wirtualnej *log Analytics Agent dla systemu Linux* nie jest instalowane ani raportowane, można wykonać poniższe kroki, aby rozwiązać problem.

1. Jeśli stan rozszerzenia jest *nieznany* , sprawdź, czy Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa poprawnie, przeglądając plik dziennika agenta maszyny wirtualnej `/var/log/waagent.log`
   * Jeśli dziennik nie istnieje, Agent maszyny wirtualnej nie jest zainstalowany.
   * [Instalowanie agenta maszyny wirtualnej platformy Azure na maszynach wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#installation)
2. W przypadku innych stanów złej kondycji Przejrzyj pliki dzienników rozszerzenia maszyny wirtualnej Log Analytics Agent for Linux. `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log``/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Jeśli stan rozszerzenia jest w dobrej kondycji, ale dane nie są przekazywane Przejrzyj pliki dziennika Log Analytics Agent dla systemu Linux w `/var/opt/microsoft/omsagent/log/omsagent.log`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami systemu Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Następne kroki

Dodatkowe wskazówki dotyczące rozwiązywania problemów dotyczące agenta Log Analytics dla systemu Linux hostowanego na komputerach poza platformą Azure można znaleźć w temacie [Rozwiązywanie problemów z agentem usługi azure log Analytics Linux](agent-linux-troubleshoot.md).  


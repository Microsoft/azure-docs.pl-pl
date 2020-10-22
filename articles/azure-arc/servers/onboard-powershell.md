---
title: Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszynę z platformą Azure przy użyciu serwerów z obsługą usługi Azure ARC przy użyciu programu PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376296"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell

Możesz włączyć serwery z obsługą usługi Azure ARC dla jednej lub małej liczby maszyn z systemem Windows lub Linux w środowisku, wykonując ręcznie zestaw kroków. Można też użyć polecenia cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine)programu PowerShell. To polecenie cmdlet wykonuje następujące akcje:

- Konfiguruje maszynę hosta do pobrania agenta systemu Windows z centrum pobierania Microsoft oraz pakiet agenta z systemem Linux z packages.microsoft.com.
- Instaluje agenta połączonej maszyny.
- Rejestruje maszynę przy użyciu usługi Azure Arc

Ta metoda wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do lokalnej grupy administratorów.

Przed rozpoczęciem należy zapoznać się z wymaganiami [wstępnymi](agent-overview.md#prerequisites) i upewnić się, że Twoja subskrypcja i zasoby spełniają wymagania. Aby uzyskać informacje o obsługiwanych regionach i innych powiązanych zagadnieniach, zobacz [Obsługiwane regiony platformy Azure](overview.md#supported-regions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer z Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).

Przed użyciem Azure PowerShell do zarządzania rozszerzeniami maszyn wirtualnych na serwerze hybrydowym zarządzanym przez serwery z obsługą łuku należy zainstalować `Az.ConnectedMachine` moduł. Uruchom następujące polecenie na serwerze z włączonym Łukem:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po zakończeniu instalacji zostanie zwrócony następujący komunikat:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Instalowanie i weryfikowanie agenta w systemie Windows

1. Otwórz konsolę programu PowerShell jako administrator.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta połączonej maszyny, użyj `Connect-AzConnectedMachine` z `-Name` `-ResourceGroupName` parametrami, i `-Location` . Użyj `-SubscriptionId` parametru, aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu się.

    Aby zainstalować agenta podłączonej maszyny na maszynie docelowej, która może bezpośrednio komunikować się z platformą Azure, uruchom następujące polecenie::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Jeśli komputer docelowy komunikuje się za pomocą serwera proxy, uruchom następujące polecenie:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. W systemie Windows pod adresem *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*i w systemie Linux w witrynie */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z serwerami z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Wyświetl maszyny w witrynie [Azure Portal](https://portal.azure.com).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).
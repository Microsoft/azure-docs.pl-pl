---
title: Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszynę z platformą Azure przy użyciu serwerów z obsługą usługi Azure ARC przy użyciu programu PowerShell.
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: bb114ec3e279a7ea696d834af8eb7240cb892dc1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891945"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell

Możesz włączyć serwery z obsługą usługi Azure ARC dla co najmniej jednej maszyny z systemem Windows lub Linux w środowisku, wykonując zestaw kroków ręcznych. Możesz też użyć polecenia cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) programu PowerShell, aby pobrać agenta połączonej maszyny, zainstalować agenta i zarejestrować maszynę przy użyciu usługi Azure Arc. Polecenie cmdlet pobierze pakiet Instalator Windows agenta systemu Windows z centrum pobierania Microsoft oraz pakiet agenta z systemem Linux z repozytorium pakietu firmy Microsoft.

Ta metoda wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do lokalnej grupy administratorów. Ten proces można wykonać interaktywnie lub zdalnie na serwerze z systemem Windows przy użyciu [komunikacji zdalnej programu PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

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

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

1. Otwórz konsolę programu PowerShell z podniesionymi uprawnieniami.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta połączonej maszyny, użyj `Connect-AzConnectedMachine` z `-Name` `-ResourceGroupName` parametrami, i `-Location` . Użyj `-SubscriptionId` parametru, aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu się. Uruchom jedno z następujących poleceń:

    * Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który może bezpośrednio komunikować się z platformą Azure, uruchom polecenie:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    * Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który komunikuje się za pomocą serwera proxy, uruchom polecenie:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. W systemie Windows pod adresem *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* i w systemie Linux w witrynie */var/opt/azcmagent/log/himds.log* .

## <a name="install-and-connect-using-powershell-remoting"></a>Instalowanie i nawiązywanie połączenia przy użyciu komunikacji zdalnej programu PowerShell

Wykonaj następujące kroki, aby skonfigurować docelowy serwer lub komputer z systemem Windows przy użyciu serwerów z obsługą usługi Azure Arc. Komunikacja zdalna programu PowerShell musi być włączona na komputerze zdalnym. Użyj `Enable-PSRemoting` polecenia cmdlet, aby włączyć obsługę zdalną programu PowerShell.

1. Otwórz konsolę programu PowerShell jako administrator.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta połączonej maszyny, użyj `Connect-AzConnectedMachine` z `-Name` `-ResourceGroupName` parametrami, i `-Location` . Użyj `-SubscriptionId` parametru, aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu się.

Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który może bezpośrednio komunikować się z platformą Azure, uruchom następujące polecenie:

```azurepowershell
$session = Connect-PSSession -ComputerName myMachineName
Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
```

Poniższy przykład jest wynikiem polecenia:

```azurepowershell
time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941

Name           Location OSName   Status     ProvisioningState
----           -------- ------   ------     -----------------
myMachineName  eastus   windows  Connected  Succeeded
```

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z serwerami z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Wyświetl maszyny w witrynie [Azure Portal](https://portal.azure.com).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia, zarządzać nimi za pomocą elementów Runbook lub funkcji usługi Automation, takich jak Update Management, lub korzystać z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).
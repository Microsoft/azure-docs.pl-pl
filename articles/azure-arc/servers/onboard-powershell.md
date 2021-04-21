---
title: Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell
description: Z tego artykułu dowiesz się, jak zainstalować agenta i połączyć maszynę z platformą Azure przy użyciu Azure Arc serwerów. Można to zrobić za pomocą programu PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831622"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell

W przypadku serwerów z Azure Arc można wykonać kroki ręczne, aby włączyć je dla co najmniej jednej maszyny z systemem Windows lub Linux w środowisku. Alternatywnie możesz użyć polecenia cmdlet programu PowerShell [Connect-AzConnectedMachine,](/powershell/module/az.connectedmachine/remove-azconnectedmachine) aby pobrać agenta Connected Machine, zainstalować agenta i zarejestrować maszynę w Azure Arc. Polecenie cmdlet pobiera pakiet agenta systemu Windows (Instalator Windows) z Centrum pobierania Microsoft oraz pakiet agenta systemu Linux z repozytorium pakietów firmy Microsoft.

Ta metoda wymaga uprawnień administratora na maszynie w celu zainstalowania i skonfigurowania agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do grupy Administratorzy lokalni. Ten proces można wykonać interaktywnie lub zdalnie na serwerze z systemem Windows przy użyciu komunikacji zdalnej programu [PowerShell.](/powershell/scripting/learn/ps101/08-powershell-remoting)

Przed rozpoczęciem zapoznaj się z wymaganiami [wstępnymi](agent-overview.md#prerequisites) i sprawdź, czy Twoja subskrypcja i zasoby spełniają wymagania. Aby uzyskać informacje o obsługiwanych regionach i innych powiązanych zagadnieniach, zobacz [obsługiwane regiony platformy Azure.](overview.md#supported-regions)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna z Azure PowerShell. Aby uzyskać instrukcje, [zobacz Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).

Program PowerShell umożliwia zarządzanie rozszerzeniami maszyn wirtualnych na serwerach hybrydowych zarządzanych przez Azure Arc serwerów z włączoną obsługą. Przed rozpoczęciem korzystania z programu PowerShell zainstaluj `Az.ConnectedMachine` moduł . Uruchom następujące polecenie na serwerze z włączoną obsługą Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta Connected Machine, użyj `Connect-AzConnectedMachine` polecenia z `-Name` parametrami , i `-ResourceGroupName` `-Location` . Użyj `-SubscriptionId` parametru , aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu. Uruchom jedno z następujących poleceń:

    * Aby zainstalować agenta Connected Machine na maszynie docelowej, która może bezpośrednio komunikować się z platformą Azure, uruchom:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Aby zainstalować agenta Connected Machine na maszynie docelowej, która komunikuje się za pośrednictwem serwera proxy, uruchom:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W systemie Windows sprawdź ten plik: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log.* W systemie Linux sprawdź ten plik: */var/opt/azcmagent/log/himds.log.*

## <a name="install-and-connect-by-using-powershell-remoting"></a>Instalowanie i nawiązywanie połączenia przy użyciu komunikacji zdalnej programu PowerShell

Poniżej opisano sposób konfigurowania jednego lub większej liczby serwerów z systemem Windows z włączonymi serwerami Azure Arc. Należy włączyć zdalną obsługę zdalną programu PowerShell na maszynie zdalnej. W tym celu użyj polecenia cmdlet `Enable-PSRemoting`.

1. Otwórz konsolę programu PowerShell jako administrator.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta Connected Machine, użyj `Connect-AzConnectedMachine` polecenia z `-ResourceGroupName` parametrami i `-Location` . Nazwy zasobów platformy Azure będą automatycznie używać nazwy hosta każdego serwera. Użyj `-SubscriptionId` parametru , aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu.

    * Aby zainstalować agenta Connected Machine na maszynie docelowej, która może bezpośrednio komunikować się z platformą Azure, uruchom następujące polecenie:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Aby jednocześnie zainstalować agenta Connected Machine na wielu maszynach zdalnych, dodaj listę nazw maszyn zdalnych rozdzielonych przecinkami.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    W poniższym przykładzie pokazano wyniki polecenia przeznaczonego dla jednej maszyny:
    
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

Po zainstalowaniu i skonfigurowaniu agenta do rejestrowania na Azure Arc serwerów, przejdź do Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetl maszynę w [Azure Portal](https://portal.azure.com).

![Zrzut ekranu przedstawiający pulpit nawigacyjny Serwery z pomyślnym połączeniem z serwerem.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

* W razie potrzeby zapoznaj się z [przewodnikiem Rozwiązywanie problemów z agentem połączonej maszyny.](troubleshoot-agent-onboard.md)

* Dowiedz się, jak zarządzać maszyną przy użyciu [Azure Policy](../../governance/policy/overview.md). Możesz użyć konfiguracji [gościa maszyny](../../governance/policy/concepts/guest-configuration.md)wirtualnej, sprawdzić, czy maszyna raportuje do oczekiwanego obszaru roboczego usługi Log Analytics, i włączyć monitorowanie za pomocą Azure Monitor [maszyn wirtualnych.](../../azure-monitor/vm/vminsights-enable-policy.md)

* Dowiedz się więcej o [agencie usługi Log Analytics.](../../azure-monitor/agents/log-analytics-agent.md) Agent usługi Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia albo zarządzać nimi za pomocą elementów Runbook lub funkcji programu Azure Automation, takich jak Update Management. Ten agent jest również wymagany do korzystania z innych usług platformy Azure, takich [jak Azure Security Center](../../security-center/security-center-introduction.md).

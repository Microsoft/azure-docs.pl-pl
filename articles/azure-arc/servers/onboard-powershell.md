---
title: Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszynę z platformą Azure przy użyciu serwerów z obsługą usługi Azure Arc. Można to zrobić za pomocą programu PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0218235179e1a8a883360d0061e685c04079cbf4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492945"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Łączenie maszyn hybrydowych z platformą Azure przy użyciu programu PowerShell

W przypadku serwerów z funkcją Azure Arc można wykonać ręczne kroki, aby włączyć je dla co najmniej jednej maszyny z systemem Windows lub Linux w danym środowisku. Alternatywnie, możesz użyć polecenia cmdlet programu PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) , aby pobrać agenta połączonej maszyny, zainstalować agenta i zarejestrować maszynę przy użyciu usługi Azure Arc. Polecenie cmdlet pobierze pakiet agenta systemu Windows (Instalator Windows) z centrum pobierania Microsoft oraz pakiet agenta z systemem Linux z repozytorium pakietu Microsoft.

Ta metoda wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do lokalnej grupy administratorów. Proces ten można wykonać interaktywnie lub zdalnie w systemie Windows Server za pomocą [komunikacji zdalnej programu PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Przed rozpoczęciem Sprawdź [wymagania wstępne](agent-overview.md#prerequisites) i sprawdź, czy Twoja subskrypcja i zasoby spełniają wymagania. Aby uzyskać informacje o obsługiwanych regionach i innych powiązanych zagadnieniach, zobacz [Obsługiwane regiony platformy Azure](overview.md#supported-regions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna z Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).

Program PowerShell służy do zarządzania rozszerzeniami maszyn wirtualnych na serwerach hybrydowych zarządzanych przez serwery z obsługą usługi Azure Arc. Przed rozpoczęciem korzystania z programu PowerShell należy zainstalować `Az.ConnectedMachine` moduł. Uruchom następujące polecenie na serwerze z włączonym użyciem usługi Azure ARC:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

1. Otwórz konsolę programu PowerShell z podniesionymi uprawnieniami.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta połączonej maszyny, użyj `Connect-AzConnectedMachine` z `-Name` `-ResourceGroupName` parametrami, i `-Location` . Użyj `-SubscriptionId` parametru, aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu się. Uruchom jedno z następujących poleceń:

    * Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który może bezpośrednio komunikować się z platformą Azure, uruchom polecenie:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który komunikuje się za pomocą serwera proxy, uruchom polecenie:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. W systemie Windows zaznacz ten plik: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. W systemie Linux zaznacz ten plik: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Instalowanie i nawiązywanie połączenia przy użyciu komunikacji zdalnej programu PowerShell

Poniżej przedstawiono sposób konfigurowania co najmniej jednego serwera z systemem Windows z włączonymi usługami Azure Arc. Należy włączyć obsługę zdalną programu PowerShell na maszynie zdalnej. W tym celu użyj polecenia cmdlet `Enable-PSRemoting`.

1. Otwórz konsolę programu PowerShell jako administrator.

2. Zaloguj się do platformy Azure, uruchamiając polecenie `Connect-AzAccount` .

3. Aby zainstalować agenta połączonej maszyny, użyj `Connect-AzConnectedMachine` z `-ResourceGroupName` `-Location` parametrami i. Nazwy zasobów platformy Azure będą automatycznie używać nazwy hosta każdego serwera. Użyj `-SubscriptionId` parametru, aby zastąpić domyślną subskrypcję w wyniku kontekstu platformy Azure utworzonego po zalogowaniu się.

    * Aby zainstalować agenta podłączonego maszyny na komputerze docelowym, który może bezpośrednio komunikować się z platformą Azure, uruchom następujące polecenie:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Aby zainstalować agenta połączonego komputera na wielu maszynach zdalnych w tym samym czasie, należy dodać listę nazw maszyn zdalnych, z których każda oddzielona przecinkami.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Poniższy przykład pokazuje wyniki polecenia docelowego dla jednej maszyny:
    
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

Po zainstalowaniu i skonfigurowaniu agenta do rejestracji przy użyciu serwerów z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Zapoznaj się z maszyną w [Azure Portal](https://portal.azure.com).

![Zrzut ekranu przedstawiający pulpit nawigacyjny serwerów z pomyślnym połączeniem z serwerem.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

* W razie potrzeby zapoznaj się z [przewodnikiem Rozwiązywanie problemów z agentem podłączonych komputerów](troubleshoot-agent-onboard.md).

* Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md). Możesz użyć [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzić, czy maszyna zgłasza do oczekiwanego log Analyticsego obszaru roboczego, i włączyć monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md).

* Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy chcesz zbierać dane monitorowania systemu operacyjnego i obciążenia albo zarządzać nimi za pomocą Azure Automation elementów Runbook lub funkcji, takich jak Update Management. Ten agent jest również wymagany do korzystania z innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-introduction.md).

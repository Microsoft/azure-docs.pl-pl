---
title: Rozwiązywanie problemów i monitorowanie bram sieci VPN — Azure Automation
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób diagnozowania łączności lokalnej przy użyciu Azure Automation i Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011816"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorowanie bram sieci VPN za pomocą Network Watcher rozwiązywania problemów

Uzyskanie szczegółowych informacji o wydajności sieci jest niezwykle ważne, aby zapewnić klientom niezawodne usługi. W związku z tym ważne jest, aby szybko wykrywać warunki awarii sieci i podejmować działania naprawcze w celu ograniczenia stanu przestoju. Azure Automation umożliwia zaimplementowanie i uruchamianie zadania w sposób programistyczny za pomocą elementów Runbook. Przy użyciu Azure Automation jest tworzony idealny przepis dotyczący ciągłego i aktywnego monitorowania sieci oraz tworzenia alertów.

## <a name="scenario"></a>Scenariusz

Scenariusz na poniższej ilustracji to aplikacja wielowarstwowa z łącznością lokalną ustanowioną przy użyciu VPN Gateway i tunelu. Upewnienie się, że VPN Gateway działa, ma kluczowe znaczenie dla wydajności aplikacji.

Element Runbook jest tworzony za pomocą skryptu w celu sprawdzenia stanu połączenia tunelu sieci VPN przy użyciu interfejsu API rozwiązywania problemów z zasobami w celu sprawdzenia stanu tunelu połączenia. Jeśli stan nie jest w dobrej kondycji, do administratorów zostanie wysłany wyzwalacz e-mail.

![Przykładowy scenariusz][scenario]

W tym scenariuszu zostaną:

- Utwórz element Runbook wywołujący `Start-AzureRmNetworkWatcherResourceTroubleshooting` polecenie cmdlet w celu rozwiązania problemu ze stanem połączenia
- Łączenie harmonogramu z elementem Runbook

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem tego scenariusza wymagane są następujące wymagania wstępne:

- Konto usługi Azure Automation na platformie Azure. Upewnij się, że konto usługi Automation ma Najnowsze moduły, a także moduł AzureRM. Network. Moduł AzureRM. Network jest dostępny w galerii modułów, jeśli trzeba ją dodać do konta usługi Automation.
- Musisz mieć zestaw poświadczeń skonfigurowany w Azure Automation. Dowiedz się więcej pod [Azure Automation zabezpieczeniami](../automation/automation-security-overview.md)
- Prawidłowy serwer SMTP (Microsoft 365, lokalny adres e-mail lub inny) i poświadczenia zdefiniowane w Azure Automation
- Skonfigurowano bramę Virtual Network na platformie Azure.
- Istniejące konto magazynu z istniejącym kontenerem do przechowywania dzienników.

> [!NOTE]
> Infrastruktura przedstawiona na powyższym obrazie ma na celu ilustrację i nie została utworzona za pomocą czynności zawartych w tym artykule.

### <a name="create-the-runbook"></a>Tworzenie elementu Runbook

Pierwszym krokiem w celu skonfigurowania przykładu jest utworzenie elementu Runbook. W tym przykładzie używa się konta Uruchom jako. Aby dowiedzieć się więcej o kontach Uruchom jako, odwiedź stronę [uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/manage-runas-account.md)

### <a name="step-1"></a>Krok 1

Przejdź do Azure Automation w [Azure Portal](https://portal.azure.com) i kliknij pozycję **elementy Runbook** .

![Przegląd konta usługi Automation][1]

### <a name="step-2"></a>Krok 2

Kliknij pozycję **Dodaj element Runbook** , aby rozpocząć proces tworzenia elementu Runbook.

![blok elementów Runbook][2]

### <a name="step-3"></a>Krok 3

W obszarze **szybkie tworzenie** kliknij pozycję **Utwórz nowy element Runbook** , aby utworzyć element Runbook.

![Dodawanie bloku elementu Runbook][3]

### <a name="step-4"></a>Krok 4

W tym kroku przydajemy nazwę elementu Runbook, w tym przykładzie nazywamy **Get-VPNGatewayStatus**. Ważne jest, aby nadać elementowi Runbook nazwę opisową, i zaleca się nadanie jej nazwy zgodnej ze standardowymi standardami nazewnictwa programu PowerShell. Typem elementu Runbook dla tego przykładu jest program **PowerShell**, inne opcje to grafika, przepływ pracy programu PowerShell i graficzny przepływ pracy programu PowerShell.

![blok elementu Runbook][4]

### <a name="step-5"></a>Krok 5

W tym kroku jest tworzony element Runbook, Poniższy przykład kodu zawiera kod, który jest wymagany do tego przykładu. Elementy w kodzie, które zawierają, \<value\> muszą zostać zastąpione wartościami z subskrypcji.

Użyj następującego kodu jako kliknij pozycję **Zapisz**

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Krok 6

Po zapisaniu elementu Runbook należy do niego połączyć harmonogram w celu zautomatyzowania uruchamiania elementu Runbook. Aby rozpocząć proces, kliknij przycisk **harmonogram**.

![Krok 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Łączenie harmonogramu z elementem Runbook

Należy utworzyć nowy harmonogram. Kliknij pozycję **Połącz harmonogram z elementem Runbook**.

![Krok 7][7]

### <a name="step-1"></a>Krok 1

W bloku **harmonogram** kliknij pozycję **Utwórz nowy harmonogram**

![Krok 8][8]

### <a name="step-2"></a>Krok 2

W bloku **Nowy harmonogram** Wypełnij informacje o harmonogramie. Wartości, które można ustawić, znajdują się na poniższej liście:

- **Nazwa** — przyjazna nazwa harmonogramu.
- **Opis** — opis harmonogramu.
- **Początek** — ta wartość jest kombinacją daty, godziny i strefy czasowej, które składają się na czas wyzwalacza harmonogramu.
- **Cykl** — ta wartość określa powtarzanie harmonogramów.  Prawidłowe wartości są **jednokrotne** lub **cykliczne**.
- **Powtarzaj co** — interwał cyklu harmonogramu w godzinach, dniach, tygodniach lub miesiącach.
- **Ustawianie wygaśnięcia** — wartość określa, czy harmonogram powinien wygasnąć. Można ustawić na wartość **tak** lub **nie**. Jeśli wybrano opcję tak, należy podać prawidłową datę i godzinę.

> [!NOTE]
> Jeśli konieczne jest Uruchamianie elementu Runbook częściej niż co godzinę, należy utworzyć wiele harmonogramów w różnych interwałach (czyli 15, 30, 45 minut po godzinie)

![Krok 9][9]

### <a name="step-3"></a>Krok 3

Kliknij przycisk Zapisz, aby zapisać harmonogram do elementu Runbook.

![Krok 10][10]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zintegrować Network Watcher Rozwiązywanie problemów z Azure Automation, Dowiedz się, jak wyzwolić przechwycenia pakietów na podstawie alertów maszyny wirtualnej, odwiedzając opcję [Utwórz alert wyzwalający przechwytywanie pakietów za pomocą usługi Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
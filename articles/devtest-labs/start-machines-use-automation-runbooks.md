---
title: Uruchamianie maszyn przy użyciu elementów Runbook usługi Automation w programie Azure DevTest Labs
description: Dowiedz się, jak uruchamiać maszyny wirtualne w laboratorium w Azure DevTest Labs przy użyciu Azure Automation elementów Runbook.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282266"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Uruchamianie maszyn wirtualnych w laboratorium w kolejności przy użyciu Azure Automation elementów Runbook
Funkcja [Autostart](devtest-lab-set-lab-policy.md#set-autostart) programu DevTest Labs umożliwia skonfigurowanie maszyn wirtualnych do automatycznego uruchamiania w określonym czasie. Jednak ta funkcja nie obsługuje maszyn do uruchomienia w określonej kolejności. Istnieje kilka scenariuszy, w których ten typ automatyzacji będzie przydatny.  Jeden scenariusz polega na tym, że przed innymi maszynami wirtualnymi serwera przesiadkowego maszynę wirtualną w środowisku laboratoryjnym, jako punkt dostępu do innych maszyn wirtualnych.  W tym artykule opisano sposób konfigurowania konta Azure Automation za pomocą elementu Runbook programu PowerShell, który wykonuje skrypt. Skrypt używa tagów na maszynach wirtualnych w laboratorium, aby umożliwić sterowanie kolejnością uruchamiania bez konieczności zmiany skryptu.

## <a name="setup"></a>Konfigurowanie
W tym przykładzie maszyny wirtualne w laboratorium muszą mieć dodany tag **StartupOrder** z odpowiednią wartością (0, 1, 2 itd.). Wyznaczanie wszystkich maszyn, które nie muszą być uruchamiane jako-1.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Utwórz konto Azure Automation, wykonując instrukcje przedstawione w [tym artykule](../automation/automation-create-standalone-account.md). Wybierz opcję **konta Uruchom jako** podczas tworzenia konta. Po utworzeniu konta usługi Automation Otwórz stronę **moduły** , a następnie wybierz pozycję **Aktualizuj moduły platformy Azure** na pasku menu. Domyślne moduły mają kilka wersji starych i bez aktualizacji skrypt może nie działać.

## <a name="add-a-runbook"></a>Dodawanie elementu Runbook
Teraz, aby dodać element Runbook do konta usługi Automation, wybierz pozycję **elementy Runbook** w menu po lewej stronie. Wybierz pozycję **Dodaj element Runbook** w menu i postępuj zgodnie z instrukcjami, aby [utworzyć element Runbook programu PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skrypt programu PowerShell
Następujący skrypt przyjmuje nazwę subskrypcji, nazwę laboratorium jako parametry. Przepływ skryptu pozwala uzyskać wszystkie maszyny wirtualne w laboratorium, a następnie analizować informacje o znacznikach w celu utworzenia listy nazw maszyn wirtualnych i ich kolejności uruchamiania. Skrypt przechodzi przez maszyny wirtualne w kolejności i uruchamia maszyny wirtualne. Jeśli istnieje wiele maszyn wirtualnych o określonym numerze zamówienia, są one uruchamiane asynchronicznie przy użyciu zadań programu PowerShell. Dla tych maszyn wirtualnych, które nie mają znacznika, ustaw wartość startową jako ostatnią (10), zostaną one domyślnie uruchomione jako ostatnie.  Jeśli laboratorium nie chce, aby maszyna wirtualna była uruchamiana jako Autostart, ustaw wartość tagu na 11 i zostanie ona zignorowana.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Tworzenie harmonogramu
Aby ten skrypt był wykonywany codziennie, należy [utworzyć harmonogram](../automation/shared-resources/schedules.md#create-a-schedule) na koncie usługi Automation. Po utworzeniu harmonogramu [Połącz go z elementem Runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

W sytuacji dużej skali, w której istnieje wiele subskrypcji z wieloma laboratoriami, Zapisz informacje o parametrach w pliku dla różnych laboratoriów i Przekaż plik do skryptu zamiast poszczególnych parametrów. Należy zmodyfikować skrypt, ale jego wykonanie będzie takie samo. Chociaż w tym przykładzie użyto Azure Automation do wykonania skryptu programu PowerShell, dostępne są inne opcje, takie jak użycie zadania w potoku kompilacji/wydania.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem, aby dowiedzieć się więcej na temat Azure Automation: [wprowadzenie do Azure Automation](../automation/automation-intro.md).

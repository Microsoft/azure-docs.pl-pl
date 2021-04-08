---
title: Dodawanie artefaktu do maszyny wirtualnej w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak dodać artefakt do maszyny wirtualnej w laboratorium w Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483894"
---
# <a name="add-an-artifact-to-a-vm"></a>Dodawanie artefaktu do maszyny wirtualnej
Podczas tworzenia maszyny wirtualnej można dodać do niej istniejące artefakty. Te artefakty mogą pochodzić z [publicznego repozytorium git usługi DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub z własnego repozytorium git. W tym artykule pokazano, jak dodać artefakty w Azure Portal i przy użyciu Azure PowerShell. 

Azure DevTest Labs *artefakty* umożliwiają określanie *akcji* wykonywanych podczas aprowizacji maszyny wirtualnej, takich jak uruchamianie skryptów programu Windows PowerShell, uruchamianie poleceń bash i instalowanie oprogramowania. *Parametry* artefaktów umożliwiają dostosowanie artefaktu dla konkretnego scenariusza.

Aby dowiedzieć się więcej o sposobach tworzenia niestandardowych artefaktów, zobacz artykuł: [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz laboratorium zawierające maszynę wirtualną, której chcesz użyć.  
1. Wybierz pozycję **moje maszyny wirtualne**.
1. Wybierz odpowiednią maszynę wirtualną.
1. Wybierz pozycję **Zarządzaj artefaktami**. 
1. Wybierz pozycję **Zastosuj artefakty**.
1. W okienku **Zastosuj artefakty** wybierz artefakt, który chcesz dodać do maszyny wirtualnej.
1. W okienku **Dodawanie artefaktu** wprowadź wymagane wartości parametrów i wszelkie parametry opcjonalne, które są potrzebne.  
1. Wybierz pozycję **Dodaj** , aby dodać artefakt i wrócić do okienka **stosowanie artefaktów** .
1. Kontynuuj dodawanie artefaktów zgodnie z potrzebami dla maszyny wirtualnej.
1. Po dodaniu artefaktów możesz [zmienić kolejność, w której są uruchamiane artefakty](#change-the-order-in-which-artifacts-are-run). Możesz również wrócić do [wyświetlania lub modyfikowania artefaktu](#view-or-modify-an-artifact).
1. Po zakończeniu dodawania artefaktów wybierz pozycję **Zastosuj** .

### <a name="change-the-order-in-which-artifacts-are-run"></a>Zmień kolejność, w której są uruchamiane artefakty
Domyślnie działania artefaktów są wykonywane w kolejności, w jakiej są dodawane do maszyny wirtualnej. Poniższe kroki ilustrują sposób zmiany kolejności, w której są uruchamiane artefakty.

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny wirtualnej.
   
    ![Liczba artefaktów dodanych do maszyny wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **wybrane artefakty** przeciągnij i upuść artefakty do żądanej kolejności. Jeśli masz problem z przeciąganiem artefaktu, upewnij się, że przeciągniesz od lewej strony artefaktu. 
1. Po zakończeniu wybierz przycisk **OK**.  

### <a name="view-or-modify-an-artifact"></a>Wyświetlanie lub modyfikowanie artefaktu
Poniższe kroki ilustrują sposób wyświetlania lub modyfikowania parametrów artefaktu:

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny wirtualnej.
   
    ![Liczba artefaktów dodanych do maszyny wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **wybrane artefakty** wybierz artefakt, który chcesz wyświetlić lub edytować.  
1. W okienku **Dodaj artefakt** Wprowadź wszelkie potrzebne zmiany, a następnie wybierz **przycisk OK** , aby zamknąć okienko **Dodawanie artefaktu** .
1. Wybierz **przycisk OK** , aby zamknąć **wybrane okienko artefaktów** .

## <a name="use-powershell"></a>Korzystanie z programu PowerShell
Poniższy skrypt stosuje określony artefakt do określonej maszyny wirtualnej. Polecenie [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) jest tym, który wykonuje operację.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi artefaktów:

- [Określ obowiązkowe artefakty dla laboratorium](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md)
- [Dodawanie repozytorium artefaktów do laboratorium](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów](devtest-lab-troubleshoot-artifact-failure.md)

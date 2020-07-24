---
title: Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu programu PowerShell lub szablonów
description: W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych dla co najmniej jednej maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych przy użyciu szablonów Azure PowerShell lub Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 021e5e8ee01745dd0b534e53dbb6d75b5b78c419
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049618"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu szablonów Azure PowerShell lub Menedżer zasobów

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak włączyć Azure Monitor dla maszyn wirtualnych dla maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu szablonów Azure PowerShell lub Azure Resource Manager. Po zakończeniu tego procesu będzie można pomyślnie zacząć monitorować wszystkie maszyny wirtualne i dowiedzieć się, czy występują problemy z wydajnością lub dostępnością.

## <a name="set-up-a-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego Log Analytics

Jeśli nie masz obszaru roboczego Log Analytics, musisz go utworzyć. Zapoznaj się z metodami sugerowanymi w sekcji [wymagania wstępne](vminsights-enable-overview.md#log-analytics) przed wykonaniem czynności, aby ją skonfigurować. Następnie można zakończyć wdrażanie Azure Monitor dla maszyn wirtualnych przy użyciu metody szablonu Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Zainstaluj rozwiązanie VMInsights

Ta metoda obejmuje szablon JSON, który określa konfigurację włączania składników rozwiązania w obszarze roboczym Log Analytics.

Jeśli nie wiesz, jak wdrażać zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.jsw* folderze lokalnym.

1. Przechwyć wartości dla *obszarów roboczych*, *ResourceGroupName*i *WorkspaceLocation*. Wartość dla *obszaru roboczegoname* jest nazwą obszaru roboczego log Analytics. Wartość parametru *WorkspaceLocation* to region, w którym jest zdefiniowany obszar roboczy.

1. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

    * Użyj następujących poleceń programu PowerShell w folderze, który zawiera szablon:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

        ```output
        provisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Włącz z szablonami Azure Resource Manager

Utworzyliśmy przykładowe szablony Azure Resource Manager do dołączania maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Szablony te obejmują scenariusze, których można użyć do włączenia monitorowania istniejącego zasobu i utworzenia nowego zasobu z włączonym monitorowaniem.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co zasób, który ma zostać przeniesiony na tablicę.

Jeśli nie wiesz, jak wdrażać zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Pobierz szablony

Szablony Azure Resource Manager są dostępne w pliku archiwum (zip), który można [pobrać](https://aka.ms/VmInsightsARMTemplates) z naszego repozytorium GitHub. Zawartość pliku zawiera foldery, które reprezentują każdy scenariusz wdrożenia z szablonem i plikiem parametrów. Przed uruchomieniem programu należy zmodyfikować plik parametrów i określić wymagane wartości. Nie należy modyfikować pliku szablonu, chyba że trzeba go dostosować do obsługi określonych wymagań. Po zmodyfikowaniu pliku parametrów można go wdrożyć przy użyciu następujących metod opisanych w dalszej części tego artykułu.

Plik pobierania zawiera następujące szablony dla różnych scenariuszy:

- Szablon **ExistingVmOnboarding** umożliwia Azure monitor dla maszyn wirtualnych, jeśli maszyna wirtualna już istnieje.
- Szablon **NewVmOnboarding** tworzy maszynę wirtualną i umożliwia Azure monitor dla maszyn wirtualnych monitorowania jej.
- Szablon **ExistingVmssOnboarding** umożliwia Azure monitor dla maszyn wirtualnych, jeśli zestaw skalowania maszyn wirtualnych już istnieje.
- Szablon **NewVmssOnboarding** tworzy zestawy skalowania maszyn wirtualnych i umożliwia Azure monitor dla maszyn wirtualnych monitorowania ich.
- Szablon **ConfigureWorkspace** umożliwia skonfigurowanie obszaru roboczego log Analytics do obsługi Azure monitor dla maszyn wirtualnych przez włączenie rozwiązań i kolekcji liczników wydajności systemu operacyjnego Linux i Windows.

>[!NOTE]
>Jeśli zestaw skalowania maszyn wirtualnych już istnieje, a zasady uaktualniania są ustawione na **Ręczne**, Azure monitor dla maszyn wirtualnych nie będzie domyślnie włączone dla wystąpień po uruchomieniu szablonu Azure Resource Manager **ExistingVmssOnboarding** . Musisz ręcznie uaktualnić wystąpienia.

### <a name="deploy-by-using-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Poniższy krok umożliwia monitorowanie za pomocą Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższy krok umożliwia monitorowanie za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Dane wyjściowe są podobne do następujących:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Włączanie przy użyciu programu PowerShell

Aby włączyć Azure Monitor dla maszyn wirtualnych dla wielu maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, użyj [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights)skryptu programu PowerShell. Jest ona dostępna z Galerii Azure PowerShell. Ten skrypt wykonuje iterację w następujący sposób:

- Każdej maszyny wirtualnej i zestawu skalowania maszyn wirtualnych w ramach subskrypcji.
- Grupa zasobów należących do zakresu, która jest określona przez grupę *resources*.
- Pojedyncza maszyna wirtualna lub zestaw skalowania maszyn wirtualnych, który jest określony przez *nazwę*.

W przypadku każdej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych skrypt sprawdza, czy rozszerzenie maszyny wirtualnej jest już zainstalowane. Jeśli rozszerzenie maszyny wirtualnej jest zainstalowane, skrypt spróbuje ją zainstalować ponownie. Jeśli rozszerzenie maszyny wirtualnej nie jest zainstalowane, skrypt instaluje rozszerzenia maszyny wirtualnej Log Analytics i agenta zależności.

Sprawdź, czy używasz modułu Azure PowerShell AZ Version 1.0.0 lub nowszego z `Enable-AzureRM` włączonymi aliasami zgodności. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Aby uzyskać listę szczegółów argumentów skryptu i przykładowego użycia, uruchom polecenie `Get-Help` .

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Poniższy przykład ilustruje użycie poleceń programu PowerShell w folderze, aby włączyć Azure Monitor dla maszyn wirtualnych i zrozumieć oczekiwane dane wyjściowe:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą Azure Monitor dla maszyn wirtualnych.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [view Azure monitor dla maszyn wirtualnych map](vminsights-maps.md).

- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).

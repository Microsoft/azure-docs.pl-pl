---
title: Wdrażanie szablonu Azure Resource Manager w elemencie Runbook Azure Automation
description: Jak wdrożyć szablon Azure Resource Manager przechowywany w usłudze Azure Storage z elementu Runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: PowerShell, Runbook, JSON, Automatyzacja Azure
ms.openlocfilehash: 2a6652c988eb77a1c5c7dbf800586b1c5fb756c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392210"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation

Można napisać [Azure Automation element Runbook programu PowerShell](automation-first-runbook-textual-powershell.md) , który wdraża zasób platformy Azure za pomocą [szablonu usługi Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Dzięki temu można zautomatyzować wdrażanie zasobów platformy Azure. Szablony Menedżer zasobów można obsługiwać w centralnej, bezpiecznej lokalizacji, takiej jak usługa Azure Storage.

W tym artykule opisano Tworzenie elementu Runbook programu PowerShell, który używa szablonu Menedżer zasobów przechowywanego w [usłudze Azure Storage](../storage/common/storage-introduction.md) w celu wdrożenia nowego konta usługi Azure Storage.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli jeszcze jej nie masz, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub [utworzyć bezpłatne konto](https://azure.microsoft.com/free/).
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto magazynu platformy Azure](../storage/common/storage-create-storage-account.md) , w którym ma być przechowywany szablon Menedżer zasobów
* Azure PowerShell zainstalowany na komputerze lokalnym. Aby uzyskać informacje na temat pobierania Azure PowerShell, zobacz temat [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) .

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu Menedżer zasobów, który wdraża nowe konto usługi Azure Storage.

W edytorze tekstów Skopiuj następujący tekst:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Zapisz plik lokalnie jako **TemplateTest. JSON**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisz szablon Menedżer zasobów w usłudze Azure Storage

Teraz używamy programu PowerShell do utworzenia udziału plików usługi Azure Storage i przekazania pliku **TemplateTest. JSON** .
Aby uzyskać instrukcje dotyczące sposobu tworzenia udziału plików i przekazywania pliku w Azure Portal, zobacz Rozpoczynanie [pracy z usługą Azure File Storage w systemie Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Uruchom program PowerShell na maszynie lokalnej i uruchom następujące polecenia, aby utworzyć udział plików i przekazać szablon Menedżer zasobów do tego udziału plików.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Utwórz skrypt elementu Runbook programu PowerShell

Teraz tworzymy skrypt programu PowerShell, który pobiera plik **TemplateTest. JSON** z usługi Azure Storage i wdraża szablon w celu utworzenia nowego konta usługi Azure Storage.

W edytorze tekstów wklej następujący tekst:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Zapisz plik lokalnie jako **DeployTemplate. ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i publikowanie elementu Runbook na koncie Azure Automation

Teraz używamy programu PowerShell do zaimportowania elementu Runbook do konta Azure Automation, a następnie opublikowania elementu Runbook. Aby uzyskać informacje o sposobach importowania i publikowania elementu Runbook w Azure Portal, zobacz [Zarządzanie elementami Runbook w programie Azure Automation](manage-runbooks.md).

Aby zaimportować **DeployTemplate. ps1** do konta usługi Automation jako element Runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Teraz uruchamiamy element Runbook przez wywołanie polecenia cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) . Aby uzyskać informacje o sposobach uruchamiania elementu Runbook w Azure Portal, zobacz [Uruchamianie elementu Runbook w programie Azure Automation](automation-starting-a-runbook.md).

Uruchom następujące polecenia w konsoli programu PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Działa element Runbook, a jego stan można sprawdzić, uruchamiając `$job.Status`polecenie.

Element Runbook pobierze szablon Menedżer zasobów i użyje go do wdrożenia nowego konta usługi Azure Storage.
Aby zobaczyć, że nowe konto magazynu zostało utworzone, należy uruchomić następujące polecenie:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Podsumowanie

Gotowe. Teraz możesz używać Azure Automation i usługi Azure Storage z szablonami Menedżer zasobów do wdrażania wszystkich zasobów platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat szablonów Menedżer zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Aby rozpocząć pracę z usługą Azure Storage, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).
* Aby znaleźć inne przydatne Azure Automation elementy Runbook, zobacz [elementy Runbook i galerie modułów dla Azure Automation](automation-runbook-gallery.md).
* Aby znaleźć inne przydatne Menedżer zasobów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).

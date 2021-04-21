---
title: Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation
description: W tym artykule opisano sposób wdrażania szablonu Azure Resource Manager przechowywanego w usłudze Azure Storage z elementem Runbook programu PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 20dbf9f9bbf97ed0c24ea3a525c56c7cde2db428
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834844"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Wdrażanie szablonu Azure Resource Manager w elementach Runbook programu PowerShell

Za pomocą szablonu usługi Azure Resource Management możesz napisać Azure Automation Runbook programu [PowerShell,](./learn/automation-tutorial-runbook-textual-powershell.md) który wdraża [zasób platformy Azure.](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) Szablony umożliwiają używanie Azure Automation do automatyzowania wdrażania zasobów platformy Azure. Szablony aplikacji można Resource Manager w centralnej, bezpiecznej lokalizacji, takiej jak usługa Azure Storage.

W tym artykule utworzymy runbook programu PowerShell, który używa szablonu usługi Resource Manager przechowywanego w usłudze [Azure Storage](../storage/common/storage-introduction.md) do wdrożenia nowego konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz aktywować korzyści dla subskrybentów [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto.](https://azure.microsoft.com/free/)
* [Konto usługi Automation](./automation-security-overview.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto usługi Azure Storage,](../storage/common/storage-account-create.md) na którym ma być Resource Manager szablonu.
* Azure PowerShell zainstalowana na komputerze lokalnym. Aby [uzyskać informacje na temat Azure PowerShell,](/powershell/azure/install-az-ps) zobacz Instalowanie modułu Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu Resource Manager, który wdraża nowe konto usługi Azure Storage.

W edytorze tekstów skopiuj następujący tekst:

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

Zapisz plik lokalnie jako plik w **TemplateTest.jspliku**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisywanie szablonu Resource Manager w usłudze Azure Storage

Teraz użyjemy programu PowerShell do utworzenia udziału plików usługi Azure Storage i przekazania **TemplateTest.jspliku.** Aby uzyskać instrukcje dotyczące tworzenia udziału plików i przekazywania pliku w usłudze Azure Portal, zobacz Rozpoczynanie pracy z [usługą Azure File Storage w systemie Windows.](../storage/files/storage-dotnet-how-to-use-files.md)

Uruchom program PowerShell na komputerze lokalnym i uruchom następujące polecenia, aby utworzyć udział plików i przekazać Resource Manager do tego udziału plików.

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

## <a name="create-the-powershell-runbook-script"></a>Tworzenie skryptu runbook programu PowerShell

Teraz utworzymy skrypt programu PowerShell, który pobiera **plikTemplateTest.jsz** usługi Azure Storage i wdraża szablon w celu utworzenia nowego konta usługi Azure Storage.

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

Zapisz plik lokalnie jako **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i publikowanie runbook na Azure Automation konta

Teraz za pomocą programu PowerShell zaimportujemy ten Azure Automation, a następnie opublikujemy go. Aby uzyskać informacje na temat importowania i publikowania runbook w programie Azure Portal, zobacz Manage [runbook in Azure Automation](manage-runbooks.md)(Zarządzanie Azure Automation ).

Aby **zaimportowaćDeployTemplate.ps1** na konto usługi Automation jako elementy Runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

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

Teraz uruchamiamy ten runbook, wywołując polecenie cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Aby uzyskać informacje na temat sposobu uruchamiania runbook w Azure Portal, zobacz Uruchamianie runbook w [programie Azure Automation](./start-runbooks.md).

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

Po uruchomieniu obiektu Runbook możesz sprawdzić jego stan, pobierania wartości właściwości obiektu zadania `$job.Status` .

Ten runbook pobiera szablon Resource Manager i używa go do wdrożenia nowego konta usługi Azure Storage. Nowe konto magazynu zostało utworzone, uruchamiając następujące polecenie:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Resource Manager szablonów, zobacz [Azure Resource Manager omówienie](../azure-resource-manager/management/overview.md).
* Aby rozpocząć pracę z usługą Azure Storage, [zobacz Wprowadzenie do usługi Azure Storage.](../storage/common/storage-introduction.md)
* Aby znaleźć inne przydatne Azure Automation runbook, zobacz [Używanie modułów i Azure Automation](automation-runbook-gallery.md).
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation#automation).

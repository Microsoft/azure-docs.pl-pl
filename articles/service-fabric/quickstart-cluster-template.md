---
title: Tworzenie klastra Service Fabric przy użyciu Azure Resource Manager szablonu
description: W tym przewodniku Szybki start utworzysz klaster testowy usługi Azure Service Fabric przy użyciu Azure Resource Manager szablonu.
author: erikadoyle
ms.author: edoyle
ms.date: 07/29/2020
ms.topic: quickstart
ms.service: service-fabric
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: cb3758ae837dc03789371d00f768b257a9ef295d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535316"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Szybki start: tworzenie klastra Service Fabric przy użyciu szablonu usługi ARM

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi. Klaster Service Fabric *to* połączony z siecią zestaw maszyn wirtualnych, w którym mikrousługi są wdrażane i zarządzane. W tym artykule opisano sposób wdrażania klastra Service Fabric testowego na platformie Azure przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten pięciowęzłowy klaster systemu Windows jest zabezpieczony za pomocą certyfikatu z podpisem własnym i dlatego jest przeznaczony tylko do celów informacyjnych (a nie obciążeń produkcyjnych). Użyjemy Azure PowerShell do wdrożenia szablonu. Oprócz interfejsu Azure PowerShell można również użyć interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Instalowanie Service Fabric SDK i modułów programu PowerShell

Aby ukończyć ten przewodnik Szybki start, musisz:

* Zainstaluj zestaw [SDK Service Fabric moduł programu PowerShell.](service-fabric-get-started.md)

* Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Pobieranie przykładowego szablonu i skryptu pomocnika certyfikatu

Sklonuj lub [pobierz Azure Resource Manager szablonów szybkiego](https://github.com/Azure/azure-quickstart-templates) startu. Alternatywnie skopiuj lokalnie następujące pliki, których będziemy używać z folderu *service-fabric-secure-cluster-5-node-1-nodetype:*

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i wyznaczyć subskrypcję do użycia na Service Fabric klastra.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Tworzenie certyfikatu z podpisem własnym przechowywanego w Key Vault

Service Fabric używa certyfikatów X.509 do zabezpieczania klastra i zapewnienia funkcji zabezpieczeń aplikacji, [a](./service-fabric-cluster-security.md) Key Vault [do](../key-vault/general/overview.md) zarządzania tymi certyfikatami. Pomyślne utworzenie klastra wymaga certyfikatu klastra w celu umożliwienia komunikacji między węzłami. Na potrzeby tworzenia tego klastra testowego szybkiego startu utworzymy certyfikat z podpisem własnym na potrzeby uwierzytelniania klastra. Obciążenia produkcyjne wymagają certyfikatów utworzonych przy użyciu prawidłowo skonfigurowanej usługi certyfikatów systemu Windows Server lub jednego z zatwierdzonych urzędu certyfikacji.

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

Skrypt wyświetli monit o następujące informacje (pamiętaj o zmodyfikowaniu wartości *CertDNSName* i *KeyVaultName* na przykładowych wartościach poniżej):

* **Hasło:** Hasło!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Po zakończeniu skrypt poda wartości parametrów wymagane do wdrożenia szablonu. Pamiętaj, aby przechowywać je w następujących zmiennych, ponieważ będą one potrzebne do wdrożenia szablonu klastra:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). Szablon dla tego artykułu jest zbyt długi, aby go tutaj pokazać. Aby wyświetlić szablon, zobaczazuredeploy.js[pliku.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Aby znaleźć więcej szablonów związanych z usługą Azure Service Fabric, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric)

### <a name="customize-the-parameters-file"></a>Dostosowywanie pliku parametrów

Otwórz *azuredeploy.parameters.jsi* edytuj wartości parametrów, aby:

* **Nazwa_klastra** odpowiada wartości podanej dla *nazwy CertDNSName* podczas tworzenia certyfikatu klastra
* **Parametr adminUserName** jest wartością inną niż domyślny token *GEN-UNIQUE*
* **Wartość adminPassword** jest inna niż domyślny token *GEN-PASSWORD*
* **Wartości certificateThumbprint,** **sourceVaultResourceId** i **certificateUrlValue** są pustymi ciągami ( `""` )

Na przykład:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Przechowuj ścieżki szablonu usługi ARM i plików parametrów w zmiennych, a następnie wdychuj szablon.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po zakończeniu wdrażania znajdź wartość w danych wyjściowych i otwórz adres w przeglądarce internetowej, aby wyświetlić `managementEndpoint` [klaster](./service-fabric-visualizing-your-cluster.md)w Service Fabric Explorer .

![Service Fabric Explorer nowy klaster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Możesz również znaleźć punkt końcowy Service Fabric Explorer bloku zasobów programu Service Explorer w Azure Portal.

![Service Fabric zasobów przedstawiający Service Fabric Explorer punkt końcowy](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobów w grupie zasobów.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Następnie usuń certyfikat klastra z magazynu lokalnego. Aby znaleźć odcisk palca klastra, należy wyświetlić listę zainstalowanych certyfikatów:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Następnie usuń certyfikat:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu niestandardowego szablonu klastra usługi Azure Service Fabric, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu Service Fabric klastra Resource Manager klastra](service-fabric-cluster-creation-create-template.md)

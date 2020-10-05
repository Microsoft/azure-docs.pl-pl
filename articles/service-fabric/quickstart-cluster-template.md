---
title: Tworzenie klastra Service Fabric przy użyciu szablonu Azure Resource Manager
description: W tym przewodniku szybki start utworzysz klaster testowy usługi Azure Service Fabric przy użyciu szablonu Azure Resource Manager.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 07/29/2020
ms.openlocfilehash: 359b527733ee8eebf7e1e7d12c40a0c74ec1c9bd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87460307"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Szybki Start: Tworzenie klastra Service Fabric przy użyciu szablonu ARM

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi. *Klaster* Service Fabric to połączony z siecią zestaw maszyn wirtualnych, w których są wdrażane i zarządzane mikrousługi. W tym artykule opisano sposób wdrażania klastra testowego Service Fabric na platformie Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten klaster systemu Windows z pięcioma węzłami jest zabezpieczony za pomocą certyfikatu z podpisem własnym i w ten sposób przeznaczony tylko do celów informacyjnych (a nie obciążeń produkcyjnych). Użyjemy Azure PowerShell, aby wdrożyć szablon. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Zainstaluj Service Fabric zestaw SDK i moduły programu PowerShell

Aby ukończyć ten przewodnik Szybki Start, musisz wykonać następujące czynności:

* Zainstaluj [zestaw Service Fabric SDK i moduł programu PowerShell](service-fabric-get-started.md).

* Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Pobierz przykładowy szablon i skrypt pomocnika certyfikatów

Klonuj lub Pobierz repozytorium [szablonów szybkiego startu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates) . Alternatywnie Skopiuj lokalnie następujące pliki, z których będziemy korzystać z folderu *Service-Fabric-Secure-Cluster-5-Node-1-NodeType* :

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i Wyznacz subskrypcję, która ma zostać użyta do utworzenia klastra Service Fabric.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Utwórz certyfikat z podpisem własnym przechowywany w Key Vault

Service Fabric używa certyfikatów X. 509 do [zabezpieczania klastra](./service-fabric-cluster-security.md) i zapewnia funkcje zabezpieczeń aplikacji oraz [Key Vault](../key-vault/general/overview.md) do zarządzania tymi certyfikatami. Pomyślne utworzenie klastra wymaga certyfikatu klastra w celu włączenia komunikacji między węzłami. Na potrzeby tworzenia tego klastra testowego szybkiego startu utworzymy certyfikat z podpisem własnym na potrzeby uwierzytelniania klastra. Obciążenia produkcyjne wymagają, aby certyfikaty były tworzone przy użyciu poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub jednej z zatwierdzonych urzędów certyfikacji (CA).

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

Skrypt wyświetli monit o podanie następujących danych (należy zmodyfikować *CertDNSName* i identyfikator *magazynu* kluczy z przykładowych wartości poniżej):

* **Hasło:** Hasło! 1
* **CertDNSName:** *sfquickstart*. southcentralus.cloudapp.Azure.com
* **Identyfikator magazynu kluczy:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Po zakończeniu skrypt poda wartości parametrów, które są odpowiednie do wdrożenia szablonu. Pamiętaj, aby przechowywać te elementy w następujących zmiennych, ponieważ będą one konieczne do wdrożenia szablonu klastra:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). Szablon tego artykułu jest zbyt długi, aby można go było wyświetlić w tym miejscu. Aby wyświetlić szablon, zobacz [azuredeploy.js](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) pliku.

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft. servicefabric/klastry](/azure/templates/microsoft.servicefabric/clusters)

Aby znaleźć więcej szablonów związanych z usługą Azure Service Fabric, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>Dostosuj plik parametrów

Otwórz *azuredeploy.parameters.jsna* i edytuj wartości parametrów, tak aby:

* wartość **ClusterName** pasuje do wartości podanej dla *CertDNSName* podczas tworzenia certyfikatu klastra
* **adminUserName** jest wartością inną niż domyślny token *Gen-Unique*
* **adminPassword** jest wartością inną niż domyślny token ogólnego *hasła*
* **certificateThumbprint**, **sourceVaultResourceId**i **certificateUrlValue** są pustym ciągiem ( `""` )

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

Zapisz ścieżki szablonu ARM i plików parametrów w zmiennych, a następnie wdróż szablon.

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

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrożenia Znajdź `managementEndpoint` wartość w danych wyjściowych i Otwórz adres w przeglądarce sieci Web, aby wyświetlić klaster w [Service Fabric Explorer](./service-fabric-visualizing-your-cluster.md).

![Service Fabric Explorer pokazujący nowy klaster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Punkt końcowy Service Fabric Explorer można również znaleźć w bloku zasobów programu Service Explorer w Azure Portal.

![Blok zasobów Service Fabric pokazujący Service Fabric Explorer punkt końcowy](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobów z grupy zasobów.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Następnie Usuń certyfikat klastra z lokalnego magazynu. Wyświetl listę zainstalowanych certyfikatów, aby znaleźć odcisk palca dla klastra:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Następnie Usuń certyfikat:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu niestandardowego szablonu klastra Service Fabric platformy Azure, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu Menedżer zasobów klastra Service Fabric](service-fabric-cluster-creation-create-template.md)

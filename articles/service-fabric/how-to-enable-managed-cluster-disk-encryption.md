---
title: Włącz szyfrowanie dysków dla węzłów klastra zarządzanego (wersja zapoznawcza) Service Fabric
description: Dowiedz się, jak włączyć szyfrowanie dysków dla węzłów klastra zarządzanych przez platformę Azure Service Fabric w systemie Windows przy użyciu szablonu ARM.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642511"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Włącz szyfrowanie dysków dla węzłów klastra zarządzanego (wersja zapoznawcza) Service Fabric

W tym przewodniku dowiesz się, jak włączyć szyfrowanie dysków na Service Fabric zarządzanych węzłach klastra w systemie Windows przy użyciu funkcji [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) dla [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) za pośrednictwem szablonów Azure Resource Manager (ARM).

> [!IMPORTANT]
> Wersja zapoznawcza szyfrowania dysków zestawu skalowania maszyn wirtualnych nie obsługuje jeszcze uaktualnienia lub obrazu obrazu. Nie należy używać w przypadku konieczności uaktualnienia obrazu systemu operacyjnego.

## <a name="register-for-azure-disk-encryption"></a>Zarejestruj się, aby uzyskać Azure Disk Encryption

Wersja zapoznawcza szyfrowania dysków dla zestawu skalowania maszyn wirtualnych wymaga samorejestracji. Uruchom następujące polecenie:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Sprawdź stan rejestracji, uruchamiając:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Gdy stan zmieni się na *zarejestrowane*, możesz kontynuować pracę.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Inicjowanie obsługi Key Vault na potrzeby szyfrowania dysków

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Klaster zarządzany Key Vault i Service Fabric musi znajdować się w tym samym regionie i tej samej subskrypcji platformy Azure. Tak długo, jak te wymagania są spełnione, można użyć nowego lub istniejącego Key Vault przez włączenie go do szyfrowania dysków.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Tworzenie Key Vault z włączonym szyfrowaniem dysku

Uruchom następujące polecenia, aby utworzyć nowe Key Vault na potrzeby szyfrowania dysków. Upewnij się, że region Key Vault jest [obsługiwany dla Service Fabric zarządzanych klastrów](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) i znajduje się w tym samym regionie co klaster.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Aktualizowanie istniejących Key Vault w celu włączenia szyfrowania dysków

Uruchom następujące polecenia, aby włączyć szyfrowanie dysków dla istniejącego Key Vault.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Aktualizowanie plików szablonów i parametrów na potrzeby szyfrowania dysków

Poniższy krok przeprowadzi Cię przez wymagane zmiany szablonu, aby włączyć szyfrowanie dysków w [istniejącym zarządzanym klastrze](tutorial-managed-cluster-deploy.md). Alternatywnie można wdrożyć nowy klaster zarządzany Service Fabric z włączonym szyfrowaniem dysków przy użyciu tego szablonu: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Dodaj następujące parametry do szablonu, zastępując własną subskrypcję, nazwę grupy zasobów i nazwę magazynu w obszarze `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Następnie Dodaj `AzureDiskEncryption` rozszerzenie maszyny wirtualnej do typów zarządzanych węzłów klastra w szablonie:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Na koniec Zaktualizuj plik parametrów, zastępując własną subskrypcję, grupę zasobów i nazwę magazynu kluczy w *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Wdróż i Sprawdź zmiany

Gdy wszystko będzie gotowe, wdróż zmiany, aby włączyć szyfrowanie dysków w zarządzanym klastrze.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Stan szyfrowania dysku można sprawdzić w podstawowym zestawie skalowania za pomocą `Get-AzVmssDiskEncryption` polecenia. Najpierw należy znaleźć nazwę pomocniczej grupy zasobów klastra (zawierającej źródłową sieć wirtualną, moduł równoważenia obciążenia, publiczny adres IP, sieciowej grupy zabezpieczeń, zestawy skalowania i konta magazynu). Pamiętaj, aby zmienić `VmssName` nazwę dowolnego typu węzła klastra, który chcesz sprawdzić (jak określono w szablonie wdrożenia).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

Dane wyjściowe powinny wyglądać podobnie do tego:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Następne kroki

[Przykład: standardowa jednostka SKU Service Fabric zarządzany klaster, 1 typ węzła z włączonym szyfrowaniem dysku](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Szyfruj zestawy skalowania maszyn wirtualnych za pomocą Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)

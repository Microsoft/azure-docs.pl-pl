---
title: Tworzenie szablonu klastra usługi Azure Service Fabric
description: Dowiedz się, jak utworzyć szablon Menedżer zasobów dla klastra Service Fabric. Skonfiguruj zabezpieczenia, Azure Key Vault i Azure Active Directory (Azure AD) do uwierzytelniania klientów.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260486"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Tworzenie szablonu Menedżer zasobów klastra Service Fabric

[Klaster Service Fabric platformy Azure](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych, w których są wdrażane i zarządzane mikrousługi. Klaster Service Fabric uruchomiony na platformie Azure to zasób platformy Azure, który jest wdrażany, zarządzany i monitorowany przy użyciu Menedżer zasobów.  W tym artykule opisano sposób tworzenia szablonu Menedżer zasobów dla klastra Service Fabric działającego na platformie Azure.  Po zakończeniu szablonu można [wdrożyć klaster na platformie Azure](service-fabric-cluster-creation-via-arm.md).

Zabezpieczenia klastra są konfigurowane podczas pierwszego konfigurowania klastra i nie można ich później zmienić. Przed skonfigurowaniem klastra zapoznaj się z artykułem [scenariusze zabezpieczeń klastra Service Fabric][service-fabric-cluster-security]. Na platformie Azure Service Fabric używa certyfikatu x509 do zabezpieczania klastra i jego punktów końcowych, uwierzytelniania klientów i szyfrowania danych. Azure Active Directory jest również zalecane do zabezpieczenia dostępu do punktów końcowych zarządzania. Przed utworzeniem klastra należy utworzyć dzierżawy i użytkowników usługi Azure AD.  Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

Przed wdrożeniem klastra produkcyjnego w celu uruchomienia obciążeń produkcyjnych należy najpierw przeczytać [listę kontrolną gotowości do produkcji](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
Przykładowe szablony Menedżer zasobów są dostępne w [przykładach platformy Azure w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony mogą służyć jako punkt początkowy dla szablonu klastra.

W tym artykule jest stosowany przykładowy szablon i szablon szablonu [bezpiecznego klastra z pięcioma węzłami][service-fabric-secure-cluster-5-node-1-nodetype] . Pobierz *azuredeploy.js* i *azuredeploy.parameters.jsna* komputer i Otwórz oba pliki w ulubionym edytorze tekstu.

> [!NOTE]
> W przypadku chmur narodowych (Azure Government platformy Azure, Chin, platformy Azure (Niemcy), należy również dodać następujące elementy `fabricSettings` do szablonu: `AADLoginEndpoint` , `AADTokenEndpointFormat` i `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Dodawanie certyfikatów
Należy dodać certyfikaty do szablonu Menedżer zasobów klastra, odwołując się do magazynu kluczy, który zawiera klucze certyfikatów. Dodaj te parametry i wartości magazynu kluczy w pliku parametrów szablonu Menedżer zasobów (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Dodaj wszystkie certyfikaty do zestawu skalowania maszyn wirtualnych osProfile
Każdy certyfikat zainstalowany w klastrze musi być skonfigurowany w sekcji **osProfile** zasobu zestawu skalowania (Microsoft. COMPUTE/virtualMachineScaleSets). Ta akcja powoduje, że dostawca zasobów instaluje certyfikat na maszynach wirtualnych. Ta instalacja obejmuje zarówno certyfikat klastra, jak i wszystkie certyfikaty zabezpieczeń aplikacji, które zamierzasz używać dla aplikacji:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurowanie certyfikatu klastra Service Fabric

Certyfikat uwierzytelniania klastra musi być skonfigurowany zarówno w ramach zasobu klastra Service Fabric (Microsoft. servicefabric/klastrów), jak i rozszerzenia Service Fabric dla zestawów skalowania maszyn wirtualnych w ramach zasobu zestawu skalowania maszyn wirtualnych. To rozmieszczenie pozwala dostawcy zasobów Service Fabric skonfigurować go do użycia na potrzeby uwierzytelniania klastra i uwierzytelniania serwera dla punktów końcowych zarządzania.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Dodawanie informacji o certyfikacie dla zasobu zestawu skalowania maszyn wirtualnych

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Dodawanie informacji o certyfikacie do zasobu klastra Service Fabric

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodawanie konfiguracji usługi Azure AD do użycia na potrzeby dostępu klientów

Konfigurację usługi Azure AD można dodać do szablonu Menedżer zasobów klastra, odwołując się do magazynu kluczy, który zawiera klucze certyfikatów. Dodaj te parametry i wartości usługi Azure AD w pliku parametrów szablonu Menedżer zasobów (*azuredeploy.parameters.json*). 

> [!NOTE]
> W systemie Linux przed utworzeniem klastra należy utworzyć dzierżawy i użytkowników usługi Azure AD.  Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Wypełnij plik parametrów wartościami

Na koniec użyj wartości wyjściowych z poleceń magazynu kluczy i programu PowerShell usługi Azure AD, aby wypełnić plik parametrów.

Jeśli planujesz używanie modułów usługi Azure Service Fabric w programie PowerShell, nie musisz wypełniać informacji o certyfikacie klastra. Jeśli chcesz, aby system generował certyfikat z podpisem własnym dla zabezpieczeń klastra, po prostu zachowaj je jako wartość null. 

> [!NOTE]
> Aby moduły RM mogli pobrać i wypełnić te puste wartości parametrów, nazwy parametrów są bardzo zgodne z poniższymi nazwami

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Jeśli używasz certyfikatów aplikacji lub korzystasz z istniejącego klastra, który został przekazany do magazynu kluczy, musisz uzyskać te informacje i wypełnić je.

Moduły RM nie mają możliwości wygenerowania konfiguracji usługi Azure AD, dlatego jeśli planujesz używać usługi Azure AD do uzyskiwania dostępu do klientów, musisz ją wypełnić.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testowanie szablonu
Użyj następującego polecenia programu PowerShell, aby przetestować szablon Menedżer zasobów przy użyciu pliku parametrów:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku wystawiania problemów i uzyskiwania komunikatów tajemnicze, użyj "-debug" jako opcji.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Na poniższym diagramie przedstawiono lokalizację magazynu kluczy i konfiguracji usługi Azure AD dopasowanego do szablonu Menedżer zasobów.

![Menedżer zasobów mapowanie zależności][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz szablon klastra, Dowiedz się, jak [wdrożyć klaster na platformie Azure](service-fabric-cluster-creation-via-arm.md).  Jeśli jeszcze tego nie zrobiono, przed wdrożeniem klastra produkcyjnego zapoznaj się z [listą kontrolną gotowości do produkcji](service-fabric-production-readiness-checklist.md) .

Aby dowiedzieć się więcej o składni i właściwościach JSON dla zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft. servicefabric/klastry](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png

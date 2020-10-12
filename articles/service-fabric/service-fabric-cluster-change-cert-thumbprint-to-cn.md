---
title: Zaktualizuj klaster, aby używał nazwy pospolitej certyfikatu
description: Dowiedz się, jak przełączyć klaster Service Fabric z używania odcisków palców certyfikatów do korzystania z nazwy pospolitej certyfikatu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: a90290430616302dbbe9ab9cf717510070936529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247918"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Change cluster from certificate thumbprint to common name (Zmienianie klastra z odcisku palca certyfikatu na nazwę pospolitą)
Dwa certyfikaty nie mogą mieć tego samego odcisku palca, co utrudnia zarzucanie certyfikatów klastra lub zarządzanie nimi. Jednak wiele certyfikatów może mieć taką samą nazwę pospolitą lub podmiot.  Przełączenie wdrożonego klastra z używania odcisków palca certyfikatu na używanie nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami jest znacznie prostsze. W tym artykule opisano, jak zaktualizować uruchomiony klaster Service Fabric, aby używał nazwy pospolitej certyfikatu zamiast odcisku palca certyfikatu.

>[!NOTE]
> Jeśli w szablonie zadeklarujesz dwa odciski palców, musisz wykonać dwa wdrożenia.  Pierwsze wdrożenie jest wykonywane przed wykonaniem kroków opisanych w tym artykule.  Pierwsze wdrożenie ustawia właściwość **odcisku palca** w szablonie na używany certyfikat i usuwa Właściwość **thumbprintSecondary** .  W przypadku drugiego wdrożenia wykonaj kroki opisane w tym artykule.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Pobierz certyfikat
Najpierw Pobierz certyfikat z [urzędu certyfikacji (CA)](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być dla domeny niestandardowej, która jest posiadana, i zakupiona z rejestratora domen. Na przykład "azureservicefabricbestpractices.com"; osoby, które nie są pracownikami firmy Microsoft, nie mogą udostępniać certyfikatów dla domen MS, dlatego nie można używać nazw DNS w obstawce lub Traffic Manager jako wspólnych nazw dla certyfikatu i należy udostępnić [Azure DNS strefę](../dns/dns-delegate-domain-azure-dns.md) , jeśli domena niestandardowa ma być rozpoznawalna na platformie Azure. Należy również zadeklarować domenę niestandardową jako "element ManagementEndpoint", jeśli chcesz, aby Portal odzwierciedlał niestandardowy alias domeny dla klastra.

Do celów testowych można uzyskać certyfikat podpisany przez urząd certyfikacji z bezpłatnego lub otwartego urzędu certyfikacji.

> [!NOTE]
> Certyfikaty z podpisem własnym, w tym wygenerowane podczas wdrażania klastra Service Fabric w Azure Portal, nie są obsługiwane. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Przekaż certyfikat i zainstaluj go w zestawie skalowania
Na platformie Azure klaster Service Fabric jest wdrażany w zestawie skalowania maszyn wirtualnych.  Przekaż certyfikat do magazynu kluczy, a następnie zainstaluj go w zestawie skalowania maszyn wirtualnych, na którym działa klaster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Wpisy tajne zestawu skalowania nie obsługują tego samego identyfikatora zasobu dla dwóch oddzielnych wpisów tajnych, ponieważ każdy klucz tajny jest w wersji, unikatowy zasób. 

## <a name="download-and-update-the-template-from-the-portal"></a>Pobieranie i aktualizowanie szablonu z poziomu portalu
Certyfikat został zainstalowany w podstawowym zestawie skalowania, ale należy również zaktualizować klaster Service Fabric, aby używał tego certyfikatu i jego nazwy pospolitej.  Teraz Pobierz szablon wdrożenia klastra.  Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do grupy zasobów, w której znajduje się klaster.  W obszarze **Ustawienia**wybierz pozycję **wdrożenia**.  Wybierz najnowsze wdrożenie i kliknij przycisk **Wyświetl szablon**.

![Wyświetl szablony][image1]

Pobierz pliki szablonu i parametrów JSON na komputer lokalny.

Najpierw Otwórz plik parametrów w edytorze tekstów i Dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnie otwórz plik szablonu w edytorze tekstów i wprowadź trzy aktualizacje do obsługi nazwy pospolitej certyfikatu.

1. W sekcji **Parametry** Dodaj parametr *certificateCommonName* :
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Rozważ również usunięcie *certificateThumbprint*, w którym nie można już odwoływać się do szablonu Menedżer zasobów.

2. W zasobie **Microsoft. COMPUTE/virtualMachineScaleSets** zaktualizuj rozszerzenie maszyny wirtualnej tak, aby używało nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  W oknie **virtualMachineProfile** -> **extensionProfile** -> **rozszerzenia** -> **Właściwości** -> **Ustawienia** -> **certyfikat**, Dodaj `"commonNames": ["[parameters('certificateCommonName')]"],` i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",` .
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  W zasobów **Microsoft. servicefabric/klastrów** zaktualizuj wersję interfejsu API do wersji "2018-02-01".  Dodaj również ustawienie **certificateCommonNames** z właściwością **commonNames** i Usuń ustawienie **certyfikatu** (z właściwością odcisku palca), jak w poniższym przykładzie:
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
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
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
    ```

Aby uzyskać dodatkowe informacje [, zobacz Wdrażanie klastra Service Fabric, który używa nazwy pospolitej certyfikatu zamiast odcisku palca.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Wdróż ponownie zaktualizowany szablon po wprowadzeniu zmian.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się [, jak przerzucać certyfikat klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png

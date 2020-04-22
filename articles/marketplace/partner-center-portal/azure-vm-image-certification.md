---
title: Certyfikacja maszyny wirtualnej platformy Azure — usługa Azure Marketplace
description: Dowiedz się, jak przetestować i przesłać ofertę maszyny wirtualnej w komercyjnym rynku.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731122"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certyfikacja obrazu maszyny wirtualnej platformy Azure

> [!NOTE]
> Przenosimy zarządzanie ofertami maszyn wirtualnych platformy Azure z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [artykule Tworzenie certyfikatów dla usługi Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) w portalu cloud partner, aby zarządzać ofertami.

W tym artykule opisano sposób testowania i przesyłania obrazu maszyny wirtualnej (VM) w komercyjnym portalu, aby upewnić się, że spełnia on najnowsze wymagania dotyczące publikowania w portalu Azure Marketplace.

Wykonaj następujące kroki przed przesłaniem oferty maszyny Wirtualnej:

1. Tworzenie i wdrażanie certyfikatów.
2. Wdrażanie maszyny Wirtualnej platformy Azure przy użyciu uogólnionego obrazu.
3. Uruchom sprawdzanie poprawności.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Tworzenie i wdrażanie certyfikatów dla usługi Azure Key Vault

W tej sekcji opisano sposób tworzenia i wdrażania certyfikatów z podpisem własnym wymaganym do skonfigurowania łączności zarządzania zdalnego systemu Windows (WinRM) na maszynie wirtualnej hostowanej przez platformę Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Tworzenie certyfikatów dla usługi Azure Key Vault

Proces ten składa się z trzech etapów:

1. Utwórz certyfikat zabezpieczeń.
2. Utwórz usługę Azure Key Vault do przechowywania certyfikatu.
3. Przechowuj certyfikaty w magazynie kluczy.

Można użyć nowej lub istniejącej grupy zasobów platformy Azure dla tej pracy.

#### <a name="create-the-security-certificate"></a>Tworzenie certyfikatu zabezpieczeń

Edytuj i uruchom następujący skrypt programu Azure PowerShell, aby utworzyć plik certyfikatu (pfx) w folderze lokalnym. Zastąp wartości parametrów pokazanych w poniższej tabeli.

| **Parametr** | **Opis** |
| --- | --- |
| $certroopath | Folder lokalny, w który można zapisać plik pfx. |
| $location | Jedna ze standardowych lokalizacji geograficznych platformy Azure. |
| $vmName | Nazwa planowanej maszyny wirtualnej platformy Azure. |
| $certname | Nazwa certyfikatu; musi być zgodna z w pełni kwalifikowaną nazwą domeny planowanej maszyny Wirtualnej. |
| $certpassword | Hasło certyfikatów musi być zgodne z hasłem używanym dla planowanej maszyny Wirtualnej. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Zachowaj tę samą sesję konsoli programu Azure PowerShell otwartą i uruchomień podczas tych kroków, aby zachować wartości różnych parametrów.

> [!WARNING]
> Jeśli zapiszesz ten skrypt, zapisz go tylko w bezpiecznej lokalizacji, ponieważ zawiera informacje zabezpieczające (hasło).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Tworzenie magazynu kluczy platformy Azure do przechowywania certyfikatu

Skopiuj zawartość poniższego szablonu do pliku na komputerze lokalnym. W poniższym przykładowym skrypcie ten zasób jest `C:\certLocation\keyvault.json`).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Edytuj i uruchom następujący skrypt programu Azure PowerShell, aby utworzyć usługę Azure Key Vault i skojarzoną grupę zasobów. Zastąp wartości parametrów pokazanych w poniższej tabeli

| **Parametr** | **Opis** |
| --- | --- |
| $postfix | Losowy ciąg numeryczny dołączony do identyfikatorów wdrożenia. |
| $rgName | Nazwa grupy zasobów platformy Azure (RG) do utworzenia. |
| $location | Jedna ze standardowych lokalizacji geograficznych platformy Azure. |
| $kvTemplateJson | Ścieżka pliku (keyvault.json) zawierająca szablon Menedżera zasobów dla magazynu kluczy. |
| $kvname | Nazwa nowego magazynu kluczy.|
|   |   |

```PowerShell
    # Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Przechowywanie certyfikatów w magazynie kluczy

Przechowuj certyfikaty zawarte w pliku pfx w nowym magazynie kluczy przy użyciu tego skryptu:

```PowerShell
     $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Wdrażanie maszyny Wirtualnej platformy Azure przy użyciu uogólnionego obrazu

W tej sekcji opisano sposób wdrażania uogólnionego obrazu VHD w celu utworzenia nowego zasobu maszyny wirtualnej platformy Azure. W tym procesie użyjemy dostarczonego szablonu usługi Azure Resource Manager i skryptu programu Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Przygotowywanie szablonu usługi Azure Resource Manager

Skopiuj następujący szablon usługi Azure Resource Manager dla wdrożenia VHD do pliku lokalnego o nazwie VHDtoImage.json. Następny skrypt zażąda lokalizacji na komputerze lokalnym, aby użyć tego JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Edytuj ten plik, aby podać wartości dla tych parametrów:

| **Parametr** | **Opis** |
| --- | --- |
| ResourceGroupName | Istniejąca nazwa grupy zasobów platformy Azure. Zazwyczaj należy użyć tego samego RG jako magazynu kluczy. |
| Plik szablonu | Pełna nazwa ścieżki do pliku VHDtoImage.json. |
| użytkownikStorageAccountName | Nazwa konta magazynu. |
| sNameForPublicIP | Nazwa DNS publicznego adresu IP; muszą być małe. |
| subscriptionId | Identyfikator subskrypcji platformy Azure. |
| Lokalizacja | Standardowa lokalizacja geograficzna platformy Azure grupy zasobów. |
| vmName | Nazwa maszyny wirtualnej. |
| nazwa skarbca | Nazwa magazynu kluczy. |
| vaultResourceGroup | Grupa zasobów magazynu kluczy. |
| certyfikatUrl | Adres internetowy (URL) certyfikatu, w tym wersja przechowywana `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`w magazynie kluczy, na przykład: . |
| vhdUrl ( vhdUrl ) | Adres internetowy wirtualnego dysku twardego. |
| vmSize | Rozmiar wystąpienia maszyny wirtualnej. |
| publicIPAddressName | Nazwa publicznego adresu IP. |
| virtualNetworkName | Nazwa sieci wirtualnej. |
| nazwa nicName | Nazwa karty interfejsu sieciowego dla sieci wirtualnej. |
| adminUserName | Nazwa użytkownika konta administratora. |
| adminPassword | Hasło administratora. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Wdrażanie maszyny Wirtualnej platformy Azure

Skopiuj i edytuj następujący `$storageaccount` `$vhdUrl` skrypt, aby podać wartości dla zmiennych i zmiennych. Wykonaj go, aby utworzyć zasób maszyny Wirtualnej platformy Azure z istniejącego uogólnionego dysku wirtualnego.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Uruchamianie weryfikacji

Istnieją dwa sposoby uruchamiania weryfikacji na wdrożonym obrazie:

- Korzystanie z narzędzia do testowania certyfikacji dla certyfikatu platformy Azure
- Korzystanie z interfejsu API autotestu

### <a name="download-and-run-the-certification-test-tool"></a>Pobieranie i uruchamianie narzędzia do testowania certyfikacji

Narzędzie testowe certyfikacji dla certyfikatu platformy Azure działa na lokalnym komputerze z systemem Windows, ale testuje maszynę wirtualną systemu Windows lub Linux opartą na platformie Azure. Zaświadcza, że obraz maszyny Wirtualnej użytkownika może być używany z platformą Microsoft Azure i że wskazówki i wymagania dotyczące przygotowywania dysku VHD zostały spełnione. Dane wyjściowe narzędzia to raport zgodności, który zostanie przekazany do portalu Centrum partnerów w celu zażądania certyfikacji maszyny Wirtualnej.

1. Pobierz i zainstaluj najnowsze [narzędzie do testowania certyfikacji dla platformy Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otwórz narzędzie certyfikacji, a następnie wybierz pozycję **Rozpocznij nowy test**.
3. Na ekranie **Informacje o teście** wprowadź **nazwę testu** dla przebiegu testu.
4. Wybierz **platformę** dla maszyny Wirtualnej, w systemie Windows Server lub Linux. Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej usługi bazy danych, zaznacz pole wyboru **Testuj dla usługi Azure SQL Database.**

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Podłączanie narzędzia certyfikacji do obrazu maszyny Wirtualnej

Narzędzie łączy się z maszynami wirtualnymi opartymi na systemie Windows za pomocą [programu Azure PowerShell](https://docs.microsoft.com/powershell/) i łączy się z maszynami wirtualnymi z systemem Linux za pośrednictwem [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Podłączanie narzędzia certyfikacji do obrazu maszyny Wirtualnej z systemem Linux

1. Wybierz tryb **uwierzytelniania SSH:** Uwierzytelnianie hasłem lub Uwierzytelnianie za pomocą klucza.
2. W przypadku korzystania z uwierzytelniania opartego na hasłach wprowadź wartości **nazwy DNS maszyny Wirtualnej,** **nazwy użytkownika**i **hasła**. Można również zmienić domyślny numer **portu SSH.**

    ![Narzędzie testowe z certyfikatem platformy Azure, uwierzytelnianie hasłem obrazu maszyny wirtualnej systemu Linux](media/avm-cert2.png)

3. W przypadku korzystania z uwierzytelniania opartego na plikach kluczy wprowadź wartości **dla nazwy DNS maszyny Wirtualnej,** nazwy **użytkownika**i lokalizacji **klucza prywatnego.** Można również dołączyć hasło lub zmienić **domyślny** numer **portu SSH.**

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Łączenie narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Windows**

1. Wprowadź w pełni kwalifikowaną **nazwę DNS maszyny Wirtualnej** (na przykład MyVMName.Cloudapp.net).
2. Wprowadź wartości **nazwy użytkownika** i **hasła**.

    ![Narzędzie testowe z certyfikatem platformy Azure, uwierzytelnianie hasłem obrazu maszyny wirtualnej z systemem Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Uruchamianie testu certyfikacyjnego

Po po podaniem wartości parametrów obrazu maszyny Wirtualnej w narzędziu certyfikacji wybierz opcję **Testuj połączenie,** aby utworzyć prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia wybierz przycisk **Dalej,** aby rozpocząć test. Po zakończeniu testu wyniki badań są wyświetlane w tabeli. Kolumna Stan jest wyświetlana (Pass/Fail/Warning) dla każdego testu. Jeśli którykolwiek z testów nie powiedzie się, obraz _nie_ jest certyfikowany. W takim przypadku przejrzyj wymagania i komunikaty o błędach, wykonuj sugerowane zmiany i uruchom test ponownie.

Po zakończeniu automatycznego testu podaj dodatkowe informacje o obrazie maszyny Wirtualnej na dwóch kartach ekranu **Kwestionariusz,** **Ocena ogólna** i **Dostosowywanie jądra,** a następnie wybierz przycisk **Dalej**.

Ostatni ekran umożliwia dostarczenie więcej informacji, takich jak informacje o dostępie do SSH dla obrazu maszyny Wirtualnej systemu Linux i wyjaśnienie wszelkich nieudanych ocen, jeśli szukasz wyjątków.

Na koniec wybierz pozycję **Wygeneruj raport,** aby pobrać wyniki testów i pliki dziennika dla wykonanych przypadków testowych wraz z odpowiedziami na kwestionariusz. Zapisz wyniki w tym samym kontenerze co veds.

## <a name="next-step"></a>Następny krok

- [Generowanie jednolitych identyfikatorów zasobów (URI) dla każdego dysku VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)

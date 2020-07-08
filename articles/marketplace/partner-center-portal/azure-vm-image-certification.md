---
title: Certyfikacja maszyn wirtualnych platformy Azure — Azure Marketplace
description: Dowiedz się, jak testować i przesyłać ofertę maszyny wirtualnej w komercyjnej witrynie Marketplace.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 63f18556847a717322b00092b973f59877102a1d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963907"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certyfikat obrazu maszyny wirtualnej platformy Azure

W tym artykule opisano sposób testowania i przesyłania obrazu maszyny wirtualnej (VM) w komercyjnej witrynie Marketplace, aby upewnić się, że spełnia on najnowsze wymagania dotyczące publikowania w witrynie Azure Marketplace.

Przed przesłaniem oferty maszyny wirtualnej wykonaj następujące kroki:

1. Tworzenie i wdrażanie certyfikatów.
2. Wdróż maszynę wirtualną platformy Azure przy użyciu uogólnionego obrazu.
3. Uruchamianie walidacji.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Tworzenie i wdrażanie certyfikatów dla Azure Key Vault

W tej sekcji opisano sposób tworzenia i wdrażania certyfikatów z podpisem własnym wymaganych do skonfigurowania łączności Windows Remote Management (WinRM) z maszyną wirtualną hostowaną na platformie Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Tworzenie certyfikatów dla Azure Key Vault

Proces ten składa się z trzech kroków:

1. Utwórz certyfikat zabezpieczeń.
2. Utwórz Azure Key Vault do przechowywania certyfikatu.
3. Przechowywanie certyfikatów w magazynie kluczy.

Do tej pracy można użyć nowej lub istniejącej grupy zasobów platformy Azure.

#### <a name="create-the-security-certificate"></a>Tworzenie certyfikatu zabezpieczeń

Edytuj i uruchom poniższy skrypt Azure PowerShell, aby utworzyć plik certyfikatu (pfx) w folderze lokalnym. Zastąp wartości parametrów przedstawionych w poniższej tabeli.

| **Parametr** | **Opis** |
| --- | --- |
| $certroopath | Folder lokalny, w którym ma zostać zapisany plik PFX. |
| $location | Jedna z lokalizacji geograficznych platformy Azure w warstwie Standardowa. |
| $vmName | Nazwa planowanej maszyny wirtualnej platformy Azure. |
| $certname | Nazwa certyfikatu; musi być zgodna z w pełni kwalifikowaną nazwą domeny planowanej maszyny wirtualnej. |
| $certpassword | Hasło dla certyfikatów musi być zgodne z hasłem używanym dla planowanej maszyny wirtualnej. |
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
> Po wykonaniu tych kroków należy zachować tę samą Azure PowerShell sesji konsoli, aby zachować wartości różnych parametrów.

> [!WARNING]
> Jeśli zapiszesz ten skrypt, Zapisz go tylko w bezpiecznej lokalizacji, ponieważ zawiera informacje o zabezpieczeniach (hasło).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Utwórz magazyn kluczy platformy Azure, aby zapisać certyfikat

Skopiuj zawartość szablonu poniżej do pliku na komputerze lokalnym. W poniższym przykładzie skryptu ten zasób ma wartość `C:\certLocation\keyvault.json` ).

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

Edytuj i uruchom poniższy skrypt Azure PowerShell, aby utworzyć Azure Key Vault i skojarzoną grupę zasobów. Zastąp wartości parametrów przedstawionych w poniższej tabeli.

| **Parametr** | **Opis** |
| --- | --- |
| $postfix | Losowy ciąg liczbowy dołączony do identyfikatorów wdrożenia. |
| $rgName | Nazwa grupy zasobów platformy Azure (RG) do utworzenia. |
| $location | Jedna z lokalizacji geograficznych platformy Azure w warstwie Standardowa. |
| $kvTemplateJson | Ścieżka pliku (keyvault.json) zawierającego szablon Menedżer zasobów dla magazynu kluczy. |
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

Zapisz certyfikaty zawarte w pliku PFX do nowego magazynu kluczy przy użyciu tego skryptu:

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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Wdróż maszynę wirtualną platformy Azure przy użyciu uogólnionego obrazu

W tej sekcji opisano sposób wdrażania uogólnionego obrazu wirtualnego dysku twardego w celu utworzenia nowego zasobu maszyny wirtualnej platformy Azure. W przypadku tego procesu użyjemy podanego szablonu Azure Resource Manager i Azure PowerShell skryptu.

### <a name="prepare-an-azure-resource-manager-template"></a>Przygotowywanie szablonu Azure Resource Manager

Skopiuj następujący szablon Azure Resource Manager dla wdrożenia wirtualnego dysku twardego do pliku lokalnego o nazwie VHDtoImage.jsna. Następny skrypt wyśle żądanie lokalizacji na komputerze lokalnym w celu użycia tego pliku JSON.

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

Edytuj ten plik, aby podać wartości tych parametrów:

| **Parametr** | **Opis** |
| --- | --- |
| ResourceGroupName | Istniejąca nazwa grupy zasobów platformy Azure. Zazwyczaj należy używać tego samego RG, co Magazyn kluczy. |
| TemplateFile | Pełna nazwa ścieżki do pliku VHDtoImage.jsna. |
| userStorageAccountName | Nazwa konta magazynu. |
| sNameForPublicIP | Nazwa DNS publicznego adresu IP; musi być małymi literami. |
| subscriptionId | Identyfikator subskrypcji platformy Azure. |
| Lokalizacja | Standardowa lokalizacja geograficzna platformy Azure grupy zasobów. |
| vmName | Nazwa maszyny wirtualnej. |
| vaultName | Nazwa magazynu kluczy. |
| vaultResourceGroup | Grupa zasobów magazynu kluczy. |
| certificateUrl | Adres sieci Web (URL) certyfikatu, w tym wersja przechowywana w magazynie kluczy, na przykład: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` . |
| vhdUrl | Adres internetowy wirtualnego dysku twardego. |
| vmSize | Rozmiar wystąpienia maszyny wirtualnej. |
| publicIPAddressName | Nazwa publicznego adresu IP. |
| virtualNetworkName | Nazwa sieci wirtualnej. |
| nicName | Nazwa karty sieciowej sieci wirtualnej. |
| adminUserName | Nazwa użytkownika konta administratora. |
| adminPassword | Hasło administratora. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Wdróż maszynę wirtualną platformy Azure

Skopiuj i edytuj następujący skrypt, aby podać wartości `$storageaccount` `$vhdUrl` zmiennych i. Wykonaj tę operację, aby utworzyć zasób maszyny wirtualnej platformy Azure na podstawie istniejącego uogólnionego wirtualnego dysku twardego.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Wykonaj walidacje

Istnieją dwa sposoby uruchamiania walidacji na wdrożonym obrazie:

- Używanie narzędzia testowego certyfikacji dla certyfikatu platformy Azure
- Korzystanie z interfejsu API samoobsługowego testowania

### <a name="download-and-run-the-certification-test-tool"></a>Pobieranie i uruchamianie narzędzia testowego certyfikacji

Narzędzie Test certyfikacji dla certyfikatu platformy Azure jest uruchamiane na lokalnym komputerze z systemem Windows, ale testuje maszynę wirtualną z systemem Windows lub Linux opartą na platformie Azure. Zaświadcza on, że obraz maszyny wirtualnej użytkownika może być używany z Microsoft Azure i że zostały spełnione wskazówki i wymagania dotyczące przygotowywania wirtualnego dysku twardego. Dane wyjściowe narzędzia to raport zgodności, który zostanie przekazany do portalu Centrum partnerskiego w celu zażądania certyfikatu maszyny wirtualnej.

1. Pobierz i zainstaluj najnowsze [Narzędzie testowania certyfikacji dla certyfikatu platformy Azure](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otwórz narzędzie certyfikacji, a następnie wybierz pozycję **Rozpocznij nowy test**.
3. Na ekranie **Informacje o testach** wprowadź **nazwę testu** dla przebiegu testu.
4. Wybierz **platformę** dla maszyny wirtualnej, system Windows Server lub Linux. Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej usługi bazy danych, zaznacz pole wyboru **Testuj dla Azure SQL Database** .

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej

Narzędzie łączy się z maszynami wirtualnymi z systemem Windows przy użyciu [Azure PowerShell](https://docs.microsoft.com/powershell/) i łączy z maszynami wirtualnymi z systemem Linux za pośrednictwem [SSH.NET](https://www.ssh.com/ssh/protocol/)

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Linux

1. Wybierz tryb **uwierzytelniania SSH** : uwierzytelnianie hasła lub uwierzytelnianie przy użyciu pliku klucza.
2. W przypadku korzystania z uwierzytelniania opartego na hasłach wprowadź wartości w polu Nazwa DNS, **Nazwa użytkownika**i **hasło** **maszyny wirtualnej**. Możesz również zmienić domyślny numer **portu SSH** .

    ![Narzędzie testowe z certyfikatem platformy Azure, uwierzytelnianie hasła dla obrazu maszyny wirtualnej z systemem Linux](media/avm-cert2.png)

3. W przypadku użycia uwierzytelniania opartego na plikach klucza wpisz wartości w polu **nazwa DNS maszyny wirtualnej**, **Nazwa użytkownika**i lokalizacja **klucza prywatnego** . Możesz również uwzględnić **hasło** lub zmienić domyślny numer **portu SSH** .

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Windows**

1. Wprowadź w pełni kwalifikowaną **nazwę DNS maszyny wirtualnej** (na przykład MyVMName.cloudapp.NET).
2. Wprowadź wartości w polu **Nazwa użytkownika** i **hasło**.

    ![Narzędzie testowe z certyfikatem platformy Azure, uwierzytelnianie hasła dla obrazu maszyny wirtualnej z systemem Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Uruchamianie testu certyfikacji

Po podaniu wartości parametrów dla obrazu maszyny wirtualnej w narzędziu certyfikacji wybierz pozycję **Testuj połączenie** , aby utworzyć prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia wybierz pozycję **dalej** , aby rozpocząć test. Po zakończeniu testu wyniki testu są wyświetlane w tabeli. W kolumnie Stan są wyświetlane (pass/Fail/ostrzeżenie) dla każdego testu. Jeśli którykolwiek z testów zakończy się niepowodzeniem, obraz _nie_ zostanie certyfikowany. W takim przypadku Przejrzyj wymagania i komunikaty o błędach, wprowadź sugerowane zmiany i ponownie uruchom test.

Po zakończeniu testu automatycznego podaj dodatkowe informacje o obrazie maszyny wirtualnej na karcie dwie karty ekranu **kwestionariusza** , **Ogólne oceny** i **dostosowanie jądra**, a następnie wybierz przycisk **dalej**.

Ostatni ekran umożliwia podanie dodatkowych informacji, takich jak informacje o dostępie SSH dla obrazu maszyny wirtualnej z systemem Linux, oraz wyjaśnienie ewentualnych ocen zakończonych niepowodzeniem, jeśli szukasz wyjątków.

Na koniec wybierz pozycję **Generuj raport** , aby pobrać wyniki testów i pliki dziennika dla wykonanych przypadków testowych wraz z odpowiedziami na kwestionariusz. Zapisz wyniki w tym samym kontenerze co wirtualne dyski twarde.

## <a name="next-step"></a>Następny krok

- [Typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki](common-sas-uri-issues.md)

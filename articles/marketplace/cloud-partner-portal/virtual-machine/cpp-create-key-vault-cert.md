---
title: Tworzenie certyfikatu Azure Key Vault | Portal Azure Marketplace
description: Wyjaśnia, jak zarejestrować maszynę wirtualną na podstawie wirtualnego dysku twardego wdrożonego na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148254"
---
# <a name="create-certificates-for-azure-key-vault"></a>Tworzenie certyfikatów dla Azure Key Vault

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [certyfikat obrazu maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano sposób aprowizacji certyfikatów z podpisem własnym, które są wymagane do nawiązania połączenia z usługą Windows Remote Management (WinRM) z maszyną wirtualną (VM) hostowaną na platformie Azure. Proces ten składa się z trzech kroków:

1.    Utwórz certyfikat zabezpieczeń. 
2.    Utwórz Azure Key Vault do zapisania tego certyfikatu. 
3.    Zapisz certyfikaty w tym magazynie kluczy. 

Do tej pracy można użyć nowej lub istniejącej grupy zasobów platformy Azure.  Poprzednie podejście jest używane w poniższym wyjaśnieniu.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Utwórz certyfikat

Edytuj i uruchom poniższy skrypt programu Azure PowerShell, aby utworzyć plik certyfikatu (pfx) w folderze lokalnym.  Należy zastąpić wartości następujących parametrów:

|  **Konstruktora**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Folder lokalny, w którym ma zostać zapisany plik PFX  |
| `$location`    | Jedna z lokalizacji geograficznych platformy Azure w warstwie Standardowa  |
| `$vmName`      | Nazwa planowanej maszyny wirtualnej platformy Azure   |
| `$certname`    | Nazwa certyfikatu; musi być zgodna z w pełni kwalifikowaną nazwą domeny planowanej maszyny wirtualnej  |
| `$certpassword` | Hasło dla certyfikatów musi być zgodne z hasłem używanym dla planowanej maszyny wirtualnej  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> Po wykonaniu tych kroków należy zachować tę samą sesję konsoli programu PowerShell, dzięki czemu wartości różnych parametrów zostaną zachowane.

> [!WARNING]
> Zapisz ten skrypt tylko w bezpiecznej lokalizacji, ponieważ zawiera on informacje o zabezpieczeniach (hasło).


## <a name="create-the-key-vault"></a>Tworzenie magazynu kluczy

Skopiuj zawartość [szablonu wdrożenia magazynu kluczy](./cpp-key-vault-deploy-template.md) do pliku na komputerze lokalnym. (w przykładowym skrypcie poniżej znajduje się `C:\certLocation\keyvault.json`ten zasób).  Edytuj i uruchom poniższy skrypt programu Azure PowerShell, aby utworzyć wystąpienie Azure Key Vault i skojarzoną grupę zasobów.  Należy zastąpić wartości następujących parametrów:

|  **Konstruktora**        |   **Opis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Dowolny ciąg liczbowy dołączony do identyfikatorów wdrożenia                     |
| `$rgName`             | Nazwa grupy zasobów platformy Azure (RG) do utworzenia                                        |
|  `$location`          | Jedna z lokalizacji geograficznych platformy Azure w warstwie Standardowa                                  |
| `$kvTemplateJson`     | Ścieżka pliku (. JSON) zawierającego szablon Menedżer zasobów dla magazynu kluczy |
| `$kvname`             | Nazwa nowego magazynu kluczy                                                       |
|  |  |

```powershell
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

## <a name="store-the-certificate"></a>Przechowywanie certyfikatu

Teraz można przechowywać certyfikaty znajdujące się w pliku PFX do nowego magazynu kluczy, uruchamiając następujący skrypt. 

```powershell
    #push certificate to key vault secret

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


## <a name="next-steps"></a>Następne kroki

Następnie zostanie [wdrożona maszyna wirtualna z obrazu maszyny wirtualnej użytkownika](./cpp-deploy-vm-user-image.md).

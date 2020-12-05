---
title: Konfigurowanie dostępu do usługi WinRM dla maszyny wirtualnej platformy Azure
description: Skonfiguruj dostęp do usługi WinRM do użycia z maszyną wirtualną platformy Azure utworzoną w modelu wdrażania Menedżer zasobów.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: fbba14980083287c91fdd3defec78b229808fb15
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621692"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie dostępu do usługi WinRM dla Virtual Machines w Azure Resource Manager

Poniżej przedstawiono kroki, które należy wykonać, aby skonfigurować maszynę wirtualną z łącznością usługi WinRM

1. Tworzenie magazynu kluczy
2. Tworzenie certyfikatu z podpisem własnym
3. Przekaż certyfikat z podpisem własnym do Key Vault
4. Pobierz adres URL certyfikatu z podpisem własnym w Key Vault
5. Odwoływanie się do adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny wirtualnej

 

## <a name="step-1-create-a-key-vault"></a>Krok 1. Tworzenie Key Vault
Możesz użyć poniższego polecenia, aby utworzyć Key Vault

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Krok 2. Tworzenie certyfikatu z podpisem własnym
Możesz utworzyć certyfikat z podpisem własnym za pomocą tego skryptu programu PowerShell

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Krok 3. Przekaż certyfikat z podpisem własnym do Key Vault
Przed przekazaniem certyfikatu do Key Vault utworzonego w kroku 1 musi on zostać przekonwertowany do formatu dostawcy zasobów Microsoft. COMPUTE. Poniższy skrypt programu PowerShell umożliwi wykonanie tej czynności

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $filecontentencoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$JSONObject = $TableForJSON | ConvertTo-Json

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Krok 4. pobieranie adresu URL certyfikatu z podpisem własnym w Key Vault
Dostawca zasobów Microsoft. COMPUTE wymaga adresu URL dla wpisu tajnego w Key Vault podczas aprowizacji maszyny wirtualnej. Umożliwia to pobranie klucza tajnego dostawcy zasobów Microsoft. COMPUTE i utworzenie równoważnego certyfikatu na maszynie wirtualnej.

> [!NOTE]
> Adres URL klucza tajnego musi zawierać również wersję. Przykładowy adres URL wygląda jak poniżej https: \/ /contosovault.Vault.Azure.NET:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Szablony
Możesz uzyskać link do adresu URL w szablonie przy użyciu poniższego kodu

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
Ten adres URL można uzyskać przy użyciu poniższego polecenia programu PowerShell

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Krok 5. odwoływanie się do adresu URL certyfikatów z podpisem własnym podczas tworzenia maszyny wirtualnej
#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Podczas tworzenia maszyny wirtualnej za pomocą szablonów do certyfikatu jest przywoływana sekcja wpisy tajne i usługa winRM w następujący sposób:

```json
"osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "<resource id of the Key Vault containing the secret>"
          },
          "vaultCertificates": [
            {
              "certificateUrl": "<URL for the certificate you got in Step 4>",
              "certificateStore": "<Name of the certificate store on the VM>"
            }
          ]
        }
      ],
      "windowsConfiguration": {
        ...
        "winRM": {
          "listeners": [
            {
              "protocol": "http"
            },
            {
              "protocol": "https",
              "certificateUrl": "<URL for the certificate you got in Step 4>"
            }
          ]
        },
        ...
      }
    },
```

Przykładowy szablon dla powyższych można znaleźć w tym miejscu na stronie [201-VM-WinRM-kluczy — Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Kod źródłowy tego szablonu można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>Krok 6. Nawiązywanie połączenia z maszyną wirtualną
Przed nawiązaniem połączenia z maszyną wirtualną należy upewnić się, że komputer jest skonfigurowany do zdalnego zarządzania usługą WinRM. Uruchom program PowerShell jako administrator i uruchom poniższe polecenie, aby upewnić się, że jest skonfigurowane.

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> Może być konieczne upewnienie się, że usługa WinRM działa, jeśli powyższe nie działa. Można to zrobić za pomocą polecenia `Get-Service WinRM`
> 
> 

Po zakończeniu instalacji możesz nawiązać połączenie z maszyną wirtualną przy użyciu poniższego polecenia

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```

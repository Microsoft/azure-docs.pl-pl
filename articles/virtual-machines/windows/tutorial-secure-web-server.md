---
title: 'Samouczek: Zabezpieczanie serwera sieci Web systemu Windows przy użyciu certyfikatów TLS/SSL na platformie Azure'
description: W ramach tego samouczka nauczysz się używać Azure PowerShell do zabezpieczenia maszyny wirtualnej z systemem Windows, na której działa serwer sieci Web usług IIS z certyfikatami TLS/SSL przechowywanymi w Azure Key Vault.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a1009d24c1fb8712e88075097460ad179629f478
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557407"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Samouczek: Zabezpieczanie serwera sieci Web na maszynie wirtualnej z systemem Windows na platformie Azure przy użyciu certyfikatów TLS/SSL przechowywanych w Key Vault

> [!NOTE]
> Obecnie ten dokument działa tylko w przypadku obrazów uogólnionych. Próba wykonania tego samouczka przy użyciu wyspecjalizowanego dysku spowoduje wystąpienie błędu. 

Aby zabezpieczyć serwery sieci Web, Transport Layer Security (TLS), wcześniej znany jako Secure Sockets Layer (SSL), można użyć certyfikatu do szyfrowania ruchu w sieci Web. Te certyfikaty TLS/SSL mogą być przechowywane w Azure Key Vault i umożliwiają bezpieczne wdrażanie certyfikatów na maszynach wirtualnych z systemem Windows na platformie Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie internetowego serwera usług IIS
> * Wprowadzanie certyfikatu do maszyny wirtualnej i Konfigurowanie usług IIS przy użyciu powiązania TLS


## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="overview"></a>Omówienie
Usługa Azure Key Vault chroni klucze kryptograficzne i klucze tajne, takie jak certyfikaty lub hasła. Usługa Key Vault pomaga uprościć proces zarządzania certyfikatami i pozwala zachować kontrolę nad kluczami, które uzyskują dostęp do tych certyfikatów. Możesz utworzyć certyfikat z podpisem własnym wewnątrz usługi Key Vault lub przekazać istniejący zaufany certyfikat, który już posiadasz.

Zamiast używania niestandardowego obrazu maszyny wirtualnej, który zawiera wbudowane certyfikaty, należy wstrzyknąć certyfikaty do uruchomionej maszyny wirtualnej. Ten proces zapewnia, że podczas wdrażania na serwerze sieci Web zostaną zainstalowane najbardziej aktualne certyfikaty. Jeśli odnowisz lub zamienisz certyfikat, nie musisz też tworzyć nowego niestandardowego obrazu maszyny wirtualnej. Najnowsze certyfikaty są automatycznie wstrzykiwane podczas tworzenia dodatkowych maszyn wirtualnych. W trakcie całego procesu certyfikaty nigdy nie opuszczają platformy Azure oraz nie są udostępniane w skrypcie, historii wiersza polecenia ani w szablonie.


## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault
Aby można było utworzyć usługę Key Vault i certyfikaty, utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupSecureWeb* w lokalizacji *Wschodnie stany USA*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Następnie utwórz Key Vault przy użyciu polecenie [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Każda usługa Key Vault wymaga unikatowej nazwy, która powinna zawierać tylko małe litery. Zamień wartość `mykeyvault` w poniższym przykładzie na własną unikatową nazwę usługi Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generowanie certyfikatu i zapisywanie go w usłudze Key Vault
Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę przy użyciu elementu [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate). W tym samouczku Poniższy przykład pokazuje, jak można wygenerować certyfikat z podpisem własnym za pomocą instrukcji [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) , która używa domyślnych zasad certyfikatów z poziomu funkcji [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM* w lokalizacji *EastUS*. Jeśli zasoby sieciowe jeszcze nie istnieją, zostaną utworzone w tym momencie. To polecenie cmdlet otwiera również port *443* w celu obsługi bezpiecznego ruchu internetowego.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. W ostatnim kroku niestandardowe rozszerzenie skryptu platformy Azure jest używane w celu zainstalowania internetowego serwera usług IIS przy użyciu polecenia [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Dodawanie certyfikatu do maszyny wirtualnej z poziomu usługi Key Vault
Aby dodać certyfikat z Key Vault do maszyny wirtualnej, Uzyskaj identyfikator certyfikatu za pomocą [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret). Dodaj certyfikat do maszyny wirtualnej przy użyciu polecenia [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurowanie usług IIS do korzystania z certyfikatu
Użyj ponownie niestandardowego rozszerzenia skryptu z poleceniem [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension), aby zaktualizować konfigurację usług IIS. Ta aktualizacja dotyczy certyfikatu dodanego z usługi Key Vault do usług IIS i konfiguruje powiązanie internetowe:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testowanie bezpiecznej aplikacji internetowej
Uzyskaj publiczny adres IP maszyny wirtualnej za pomocą polecenia [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP `myPublicIP`:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `https://<myPublicIP>` na pasku adresu. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli został użyty certyfikat z podpisem własnym, wybierz pozycję **szczegóły** , a następnie **Przejdź do strony sieci Web**:

![Akceptowanie ostrzeżenia dotyczącego zabezpieczeń w przeglądarce sieci Web](./media/tutorial-secure-web-server/browser-warning.png)

Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

![Wyświetlanie uruchomionej zabezpieczonej witryny usług IIS](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku serwer sieci Web usług IIS został zabezpieczony za pomocą certyfikatu TLS/SSL przechowywanego w Azure Key Vault. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie usługi Azure Key Vault
> * Generowanie lub przekazywanie certyfikatu do usługi Key Vault
> * Tworzenie maszyny wirtualnej i instalowanie internetowego serwera usług IIS
> * Wprowadzanie certyfikatu do maszyny wirtualnej i Konfigurowanie usług IIS przy użyciu powiązania TLS

Użyj tego linku, aby wyświetlić przykłady wstępnie utworzonych skryptów maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Przykłady skryptów maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
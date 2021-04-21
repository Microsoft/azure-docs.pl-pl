---
title: Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące używania Microsoft Azure Disk Encryption dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 4a01429e938b09752fa93b440877a5a4928b9d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750936"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release-for-linux-vms"></a>Tworzenie i konfigurowanie magazynu kluczy dla maszyn wirtualnych z Azure Disk Encryption Azure AD (poprzednia wersja)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie podania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje dotyczące włączania szyfrowania dysków maszyny wirtualnej przy użyciu nowej wersji, [zobacz Azure Disk Encryption](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane przy użyciu składni usługi AAD.**

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Get started with Azure Key Vault](../../key-vault/general/overview.md) and Secure your key vault (Rozpoczynanie pracy z magazynem kluczy i [zabezpieczanie magazynu kluczy).](../../key-vault/general/security-overview.md) 

Tworzenie i konfigurowanie magazynu kluczy do użycia z usługą Azure Disk Encryption z usługą Azure AD (poprzednia wersja) obejmuje trzy kroki:

1. Utwórz magazyn kluczy. 
2. Skonfiguruj aplikację usługi Azure AD i jednostkę usługi.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.
 
Jeśli chcesz, możesz również wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

Zobacz główny artykuł Tworzenie i konfigurowanie magazynu kluczy dla usługi [Azure Disk Encryption,](disk-encryption-key-vault.md) aby uzyskać instrukcje dotyczące sposobu instalowania narzędzi i nawiązywania połączenia [z platformą Azure.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w skrypcie interfejsu wiersza [polecenia](https://github.com/ejarvi/ade-cli-getting-started) Azure Disk Encryption wymagań wstępnych i Azure Disk Encryption [wymagań wstępnych programu PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy 
Azure Disk Encryption jest zintegrowana z [usługą Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w ramach subskrypcji magazynu kluczy oraz zarządzanie nimi. Możesz utworzyć magazyn kluczy lub użyć istniejącego magazynu na Azure Disk Encryption. Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Get started with Azure Key Vault](../../key-vault/general/overview.md) and Secure your key vault (Rozpoczynanie pracy z magazynem kluczy i [zabezpieczanie magazynu kluczy).](../../key-vault/general/security-overview.md) Do utworzenia magazynu kluczy Resource Manager szablonu, interfejsu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. 


>[!WARNING]
>Aby mieć pewność, że wpisy tajne szyfrowania nie przekroczy granic regionalnych, Azure Disk Encryption muszą mieć Key Vault i maszyny wirtualne, aby znajdowały się w tym samym regionie. Utwórz i użyj maszyny Key Vault w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="create-a-key-vault-with-powershell"></a><a name="bkmk_KVPSH"></a> Tworzenie magazynu kluczy przy użyciu programu PowerShell

Magazyn kluczy można utworzyć za pomocą Azure PowerShell polecenia cmdlet [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Aby uzyskać dodatkowe polecenia cmdlet dla Key Vault, zobacz [Az.KeyVault](/powershell/module/az.keyvault/). 

1. W razie potrzeby utwórz nową grupę zasobów za pomocą [new-AzResourceGroup.](/powershell/module/az.Resources/New-azResourceGroup)  Aby wyświetlić listę lokalizacji centrów danych, użyj [get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Tworzenie nowego magazynu kluczy przy użyciu [narzędzia New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. **Zanotuj** nazwę magazynu, nazwę grupy **zasobów,** identyfikator **zasobu,** identyfikator **URI** magazynu i identyfikator **obiektu** zwracany do późniejszego użycia podczas szyfrowania dysków. 


### <a name="create-a-key-vault-with-azure-cli"></a><a name="bkmk_KVCLI"></a> Tworzenie magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure
Magazynem kluczy można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure za pomocą [poleceń az keyvault.](/cli/azure/keyvault#commands) Aby utworzyć magazyn kluczy, użyj [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. W razie potrzeby utwórz nową grupę zasobów za pomocą [az group create](/cli/azure/group#az-group-create). Aby wyświetlić listę lokalizacji, użyj [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Utwórz nowy magazyn kluczy za pomocą [narzędzia az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Zwróć uwagę na pola **Nazwa** magazynu (nazwa), Nazwa grupy **zasobów,** **Identyfikator zasobu (ID),** **Identyfikator URI** magazynu i Identyfikator **obiektu,** które zostaną zwrócone do późniejszego użycia. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a><a name="bkmk_KVRM"></a> Tworzenie magazynu kluczy przy użyciu Resource Manager magazynu

Magazyn kluczy można utworzyć przy użyciu szablonu [Resource Manager .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wd wdrażaj na platformie Azure.**
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij pozycję **Kup.** 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a><a name="bkmk_ADapp"></a> Konfigurowanie aplikacji i jednostki usługi Azure AD 
Jeśli chcesz włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure, program Azure Disk Encryption i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. W tym celu utwórz aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub uwierzytelniania opartego na certyfikatach klienta [w usłudze Azure AD.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a><a name="bkmk_ADappPSH"></a> Konfigurowanie aplikacji i jednostki usługi usługi Azure AD za pomocą Azure PowerShell 
Aby wykonać następujące polecenia, pobierz moduł [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)i użyj go. 

1. Użyj polecenia cmdlet [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) programu PowerShell, aby utworzyć aplikację usługi Azure AD. Właściwości MyApplicationHomePage i MyApplicationUri mogą być dowolną wartością.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. Klucz $azureAdApplication.ApplicationId to Azure AD ClientID, a klucz $aadClientSecret klucz tajny klienta, który zostanie później Azure Disk Encryption. Odpowiednio zabezpiecz klucz tajny klienta usługi Azure AD. Uruchomienie `$azureAdApplication.ApplicationId` spowoduje wyświetlanie applicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a><a name="bkmk_ADappCLI"></a> Konfigurowanie aplikacji i jednostki usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure

Jednostkami usługi można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure za pomocą [poleceń az ad sp.](/cli/azure/ad/sp) Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Utwórz nową jednostkę usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Zwracany jest appId clientID usługi Azure AD używany w innych poleceniach. Jest to również spn, która będzie używać dla az keyvault set-policy. Hasło jest kluczem tajnym klienta, który powinien zostać później Azure Disk Encryption. Odpowiednio zabezpiecz klucz tajny klienta usługi Azure AD.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a><a name="bkmk_ADappRM"></a> Skonfiguruj aplikację i jednostkę usługi Azure AD za pomocą Azure Portal
Aby utworzyć aplikację usługi Azure AD, należy wykonać kroki opisane w artykule Use portal to create an Azure Active Directory application [and service principal that can access resources](../../active-directory/develop/howto-create-service-principal-portal.md) (Tworzenie aplikacji usługi Azure AD przy użyciu portalu). Każdy krok wymieniony poniżej spowoduje bezpośrednie ukończenie sekcji artykułu. 

1. [Weryfikowanie wymaganych uprawnień](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Tworzenie Azure Active Directory aplikacji](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Podczas tworzenia aplikacji możesz użyć dowolnej nazwy i adresu URL logowania.
3. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). 
     - Klucz uwierzytelniania jest kluczem tajnym klienta i jest używany jako AadClientSecret dla zestawu AzVMDiskEncryptionExtension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczenie do logowania się do usługi Azure AD. W Azure Portal ten klucz tajny jest nazywany kluczami, ale nie ma związku z magazynami kluczy. Zabezpiecz odpowiednio ten klucz tajny. 
     - Identyfikator aplikacji będzie później używany jako identyfikator AadClientId dla Set-AzVMDiskEncryptionExtension i jako servicePrincipalName dla set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a><a name="bkmk_KVAP"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Aby można było zapisywać wpisy tajne szyfrowania w określonym Key Vault, Azure Disk Encryption musi mieć identyfikator klienta i klucz tajny klienta aplikacji Azure Active Directory, która ma uprawnienia do zapisu wpisów tajnych w Key Vault. 

> [!NOTE]
> Azure Disk Encryption wymaga skonfigurowania następujących zasad dostępu do aplikacji klienckiej usługi Azure AD: _WrapKey_ i _Ustaw_ uprawnienia.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a><a name="bkmk_KVAPPSH"></a> Ustaw zasady dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu Azure PowerShell
Aplikacja usługi Azure AD wymaga praw dostępu do kluczy lub wpisów tajnych w magazynie. Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby udzielić uprawnień aplikacji przy użyciu identyfikatora klienta (który został wygenerowany podczas rejestrowania aplikacji) jako wartości parametru _–ServicePrincipalName._ Aby dowiedzieć się więcej, zobacz wpis w blogu [Azure Key Vault — krok po kroku.](/archive/blogs/kv/azure-key-vault-step-by-step) 

1. Ustaw zasady dostępu magazynu kluczy dla aplikacji usługi AD przy użyciu programu PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a><a name="bkmk_KVAPCLI"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [az keyvault set-policy,](/cli/azure/keyvault#az-keyvault-set-policy) aby ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz Manage Key Vault using CLI 2.0 (Zarządzanie Key Vault [przy użyciu interfejsu wiersza polecenia 2.0).](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)

Nadaj jednostki usługi utworzonej za pośrednictwem interfejsu wiersza polecenia platformy Azure dostęp w celu uzyskania wpisów tajnych i opakowania kluczy za pomocą następującego polecenia:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a><a name="bkmk_KVAPRM"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu portalu

1. Otwórz grupę zasobów z magazynem kluczy.
2. Wybierz magazyn kluczy, przejdź do **opcji Zasady dostępu,** a następnie kliknij **pozycję Dodaj nowy**.
3. W **obszarze Wybierz podmiot** zabezpieczeń wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. W **obszarze Uprawnienia klucza** zaznacz pole Wyboru klucza **w** obszarze **Operacje kryptograficzne.**
5. W **obszarze Uprawnienia do tajnych** zaznacz pole **ustaw** w obszarze Secret Management **Operations (Operacje zarządzania kluczami tajnymi).**
6. Kliknij **przycisk OK,** aby zapisać zasady dostępu. 

![Azure Key Vault operacji kryptograficznych — zawijanie klucza](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault tajne — ustaw](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a><a name="bkmk_KVper"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy
Platforma Azure musi mieć dostęp do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je maszynie wirtualnej na potrzeby rozruchu i odszyfrowywania woluminów. Włącz szyfrowanie dysków w magazynie kluczy lub wdrożenia nie powiodą się.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a><a name="bkmk_KVperPSH"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure PowerShell
 Użyj polecenia cmdlet programu PowerShell dla magazynu kluczy [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby włączyć szyfrowanie dysków dla magazynu kluczy.

  - **Włącz Key Vault szyfrowania dysków:** Opcja EnabledForDiskEncryption jest wymagana do szyfrowania dysków platformy Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Włącz Key Vault wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywołyny podczas tworzenia zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Włącz Key Vault wdrażania szablonu, jeśli to konieczne:** Umożliwia Azure Resource Manager wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywołyny we wdrożeniu szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a><a name="bkmk_KVperCLI"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [narzędzia az keyvault update,](/cli/azure/keyvault#az-keyvault-update) aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysków:** Wymagana jest opcja włączonego szyfrowania dysków. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Włącz Key Vault wdrożenia, jeśli to konieczne:** Zezwalaj Virtual Machines na pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Włącz Key Vault wdrażania szablonu, jeśli to konieczne:** Zezwalaj Resource Manager na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a><a name="bkmk_KVperrm"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pośrednictwem Azure Portal

1. Wybierz swój klucz, przejdź do opcji **Zasady dostępu** i kliknij, aby wyświetlić zaawansowane **zasady dostępu.**
2. Zaznacz pole z etykietą **Włącz dostęp do Azure Disk Encryption szyfrowania woluminów.**
3. Wybierz **pozycję Włącz dostęp do usługi Azure Virtual Machines na** potrzeby wdrażania i/lub Włącz dostęp do Azure Resource Manager na potrzeby wdrożenia **szablonu,** jeśli to konieczne. 
4. Kliknij pozycję **Zapisz**.

![Zaawansowane zasady dostępu usługi Azure Key Vault](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a><a name="bkmk_KEK"></a> Konfigurowanie klucza szyfrowania klucza (opcjonalnie)
Jeśli chcesz użyć klucza szyfrowania klucza (KEK) dla dodatkowej warstwy zabezpieczeń kluczy szyfrowania, dodaj klucz szyfrowania kluczy do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można również zaimportować klucz szyfrowania kluczy z lokalnego modułu HSM do zarządzania kluczami. Aby uzyskać więcej informacji, [zobacz dokumentację Key Vault .](../../key-vault/keys/hsm-protected-keys.md) Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w Key Vault. 

* Podczas generowania kluczy użyj typu klucza RSA. Azure Disk Encryption nie obsługuje jeszcze używania klawiszy krzywej eliptycznej.

* Klucz tajny magazynu kluczy i adresy URL klucza szyfrowania kluczy muszą mieć wersje. Platforma Azure wymusza to ograniczenie dotyczące wersji. Prawidłowy klucz tajny i adresy URL KEK można znaleźć w następujących przykładach:

  * Przykład prawidłowego adresu URL tajnego:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL klucza szyfrowania kluczy. Przykłady nie obsługiwanych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Nieakceptowalny adres URL magazynu kluczy  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Dopuszczalny adres URL magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a><a name="bkmk_KEKPSH"></a> Konfigurowanie klucza szyfrowania klucza przy użyciu Azure PowerShell 
Przed rozpoczęciem korzystania ze skryptu programu PowerShell należy zapoznać się z podstawowymi Azure Disk Encryption, aby zrozumieć kroki opisane w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku. Ten skrypt tworzy wszystkie Azure Disk Encryption wymagań wstępnych i szyfruje istniejącą maszynę wirtualną IaaS, opakowując klucz szyfrowania dysku przy użyciu klucza szyfrowania klucza. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a><a name="bkmk_Cert"></a> Uwierzytelnianie oparte na certyfikatach (opcjonalnie)
Jeśli chcesz użyć uwierzytelniania certyfikatu, możesz przekazać je do magazynu kluczy i wdrożyć na kliencie. Przed rozpoczęciem korzystania ze skryptu programu PowerShell należy zapoznać się z podstawowymi Azure Disk Encryption, aby zrozumieć kroki opisane w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a><a name="bkmk_CertKEK"></a> Uwierzytelnianie oparte na certyfikatach i KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu i opakować klucz szyfrowania kluczem KEK, możesz użyć poniższego skryptu jako przykładu. Przed rozpoczęciem korzystania ze skryptu programu PowerShell należy zapoznać się ze wszystkimi poprzednimi Azure Disk Encryption wstępnymi, aby zrozumieć kroki opisane w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku.

> [!IMPORTANT]
> Uwierzytelnianie oparte na certyfikatach usługi Azure AD nie jest obecnie obsługiwane na komputerach wirtualnych z systemem Linux.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Następne kroki

[Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Linux (poprzednia wersja)](disk-encryption-linux-aad.md)

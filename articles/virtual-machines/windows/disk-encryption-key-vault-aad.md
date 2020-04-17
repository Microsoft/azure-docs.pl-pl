---
title: Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b80d2e5e7c9157da2a3b05a7b422f0c0a950f90c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452047"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)

**Nowa wersja szyfrowania dysków platformy Azure eliminuje konieczność dostarczania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysku maszyny Wirtualnej. W nowej wersji nie są już wymagane do podawania poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysku maszyny Wirtualnej przy użyciu nowej wersji, zobacz [Szyfrowanie dysków platformy Azure](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane ze składnią usługi AAD.**

Usługa Azure Disk Encryption używa usługi Azure Key Vault do kontrolowania kluczy i wpisów tajnych szyfrowania dysków i zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md) i [zabezpieczanie magazynu kluczy](../../key-vault/general/secure-your-key-vault.md). 

Tworzenie i konfigurowanie magazynu kluczy do użycia z szyfrowaniem dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja) obejmuje trzy kroki:

1. Tworzenie magazynu kluczy. 
2. Konfigurowanie aplikacji i jednostki usługi Azure AD.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.
 
Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

Zobacz główny Artykuł [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure,](disk-encryption-key-vault.md) aby dowiedzieć się, jak [zainstalować narzędzia i połączyć się z platformą Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [skrypcie interfejsu wiersza polecenia szyfrowania dysków platformy Azure](https://github.com/ejarvi/ade-cli-getting-started) i [skrypcie wstępnym szyfrowania dysków platformy Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy 
Usługa Azure Disk Encryption jest zintegrowana z [usługą Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) która ułatwia kontrolowanie kluczy i wpisów tajnych szyfrowania dysków i zarządzanie nimi w subskrypcji magazynu kluczy. Można utworzyć magazyn kluczy lub użyć istniejącego dla szyfrowania dysków platformy Azure. Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz [Wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md) i [zabezpieczanie magazynu kluczy](../../key-vault/general/secure-your-key-vault.md). Do utworzenia magazynu kluczy można użyć szablonu usługi Resource Manager, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. 


>[!WARNING]
>Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, szyfrowanie dysków platformy Azure wymaga magazynu kluczy i maszyn wirtualnych, które mają współudział w tym samym regionie. Tworzenie i używanie magazynu kluczy, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="create-a-key-vault-with-powershell"></a>Tworzenie magazynu kluczy za pomocą programu PowerShell

Magazyn kluczy można utworzyć za pomocą programu Azure PowerShell przy użyciu polecenia cmdlet [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Aby uzyskać dodatkowe polecenia cmdlet dla magazynu kluczy, zobacz [Az.KeyVault](/powershell/module/az.keyvault/). 

1. W razie potrzeby utwórz nową grupę zasobów za pomocą [grupy New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Aby wyświetlić listę lokalizacji centrów danych, należy użyć funkcji [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Tworzenie nowego magazynu kluczy przy użyciu [funkcji New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Zanotuj **nazwę przechowalni,** **nazwę grupy zasobów,** **identyfikator zasobu,** **identyfikator URI magazynu**i identyfikator **obiektu,** które są zwracane do późniejszego użycia podczas szyfrowania dysków. 


### <a name="create-a-key-vault-with-azure-cli"></a>Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Magazyn kluczy można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [az keyvault.](/cli/azure/keyvault#commands) Aby utworzyć magazyn kluczy, użyj [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. W razie potrzeby utwórz nową grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group#az-group-create). Aby wyświetlić listę lokalizacji, użyj [lokalizacji listy kont az](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Utwórz nowy magazyn kluczy przy użyciu [programu az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Zanotuj **nazwę przechowalni** (nazwę), **nazwę grupy zasobów,** **identyfikator zasobu** (ID), **identyfikator URI programu Vault**i identyfikator **obiektu,** które są zwracane do późniejszego użycia. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Tworzenie magazynu kluczy za pomocą szablonu Menedżera zasobów

Przechowalnia kluczy można utworzyć za pomocą [szablonu Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybki start platformy Azure kliknij pozycję **Wdrażanie na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę magazynu kluczy, identyfikator obiektu, warunki prawne i umowę, a następnie kliknij pozycję **Zakup**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Konfigurowanie aplikacji i jednostki usługi Azure AD 
Jeśli potrzebujesz szyfrowania, aby włączyć na uruchomionej maszynie Wirtualnej na platformie Azure, szyfrowanie dysków platformy Azure generuje i zapisuje klucze szyfrowania do magazynu kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. W tym celu utwórz aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Konfigurowanie aplikacji i jednostki usługi Azure AD za pomocą programu Azure PowerShell 
Aby wykonać następujące polecenia, pobierz i użyj [modułu programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Użyj polecenia cmdlet [programu PowerShell programu New-AzADApplication,](/powershell/module/az.resources/new-azadapplication) aby utworzyć aplikację usługi Azure AD. MyApplicationHomePage i MyApplicationUri mogą być dowolnymi wartościami, które chcesz.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. identyfikator $azureAdApplication.ApplicationId jest identyfikatorem klienta usługi Azure AD, a $aadClientSecret jest kluczem tajnym klienta, którego użyjesz później, aby włączyć szyfrowanie dysków platformy Azure. Odpowiednio zabezpiecz klucz tajny klienta usługi Azure AD. Uruchomione `$azureAdApplication.ApplicationId` pokaże Ci ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Konfigurowanie aplikacji i jednostki usługi Azure AD za pomocą interfejsu wiersza polecenia platformy Azure

Podmioty usługi można zarządzać za pomocą narzędzia Azure CLI za pomocą poleceń [az ad sp.](/cli/azure/ad/sp) Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Utwórz nową jednostkę usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId zwrócony jest identyfikator klienta usługi Azure AD używane w innych poleceń. Jest to również nazwa SPN, której użyjesz dla az keyvault set-policy. Hasło jest kluczem tajnym klienta, którego należy użyć później, aby włączyć szyfrowanie dysków platformy Azure. Odpowiednio zabezpiecz klucz tajny klienta usługi Azure AD.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Konfigurowanie aplikacji i jednostki usługi Azure AD w portalu Azure
Użyj [portalu, aby utworzyć aplikację i jednostkę usługi Azure Active Directory, która może uzyskać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md) artykułu do tworzenia aplikacji usługi Azure AD. Każdy krok wymieniony poniżej spowoduje, że przejdziesz bezpośrednio do sekcji artykułu, aby zakończyć. 

1. [Weryfikowanie wymaganych uprawnień](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Tworzenie aplikacji usługi Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Podczas tworzenia aplikacji można użyć dowolnej nazwy i adresu URL logowania.
3. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Klucz uwierzytelniania jest kluczem tajnym klienta i jest używany jako AadClientSecret dla Set-AzVMDiskEncryptionExtension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczenie do logowania się do usługi Azure AD. W witrynie Azure portal ten klucz tajny jest nazywany kluczami, ale nie ma związku z magazynami kluczy. Zabezpiecz ten sekret odpowiednio. 
     - Identyfikator aplikacji będzie używany później jako AadClientId dla Set-AzVMDiskEncryptionExtension i jako ServicePrincipalName dla Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Aby zapisać wpisy tajne szyfrowania w określonej przechowalni kluczy, szyfrowanie dysków platformy Azure wymaga identyfikatora klienta i klucza tajnego klienta aplikacji usługi Azure Active Directory, która ma uprawnienia do zapisywania wpisów tajnych w magazynie kluczy. 

> [!NOTE]
> Szyfrowanie dysków platformy Azure wymaga skonfigurowania następujących zasad dostępu do aplikacji klienckiej usługi Azure AD: _WrapKey_ i _Ustaw_ uprawnienia.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Ustawianie zasad dostępu do magazynu kluczy dla aplikacji usługi Azure AD za pomocą programu Azure PowerShell
Aplikacja usługi Azure AD potrzebuje praw dostępu do kluczy lub wpisów tajnych w magazynie. Polecenie cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) służy do udzielania uprawnień aplikacji przy użyciu identyfikatora klienta (który został wygenerowany podczas rejestracji aplikacji) jako wartości parametru _–ServicePrincipalName._ Aby dowiedzieć się więcej, zobacz wpis w blogu [Azure Key Vault — krok po kroku](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Ustaw zasady dostępu do magazynu kluczy dla aplikacji AD za pomocą programu PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Ustawianie zasad dostępu do magazynu kluczy dla aplikacji usługi Azure AD za pomocą interfejsu wiersza polecenia platformy Azure
Użyj [az keyvault set-policy,](/cli/azure/keyvault#az-keyvault-set-policy) aby ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz [Zarządzanie magazynem kluczy przy użyciu interfejsu CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Podaj jednostkę usługi utworzoną za pośrednictwem interfejsu wiersza polecenia Azure, aby uzyskać wpisy tajne i zawijać klucze za pomocą następującego polecenia:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Ustawianie zasad dostępu do magazynu kluczy dla aplikacji usługi Azure AD za pomocą portalu

1. Otwórz grupę zasobów za pomocą magazynu kluczy.
2. Wybierz przechowalnię kluczy, przejdź do **witryny Zasady dostępu,** a następnie kliknij pozycję **Dodaj nowy**.
3. W obszarze **Wybierz podmiot główny**wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. Aby uzyskać **uprawnienia klucza,** zaznacz pole **wyboru Zawijanie klucza** w obszarze **Operacje kryptograficzne**.
5. W przypadku **uprawnień tajnych**zaznacz pole **wyboru Ustaw w** obszarze Tajne operacje **zarządzania**.
6. Kliknij **przycisk OK,** aby zapisać zasady dostępu. 

![Operacje kryptograficzne usługi Azure Key Vault — klucz zawijany](../media/disk-encryption/keyvault-portal-fig3.png)

![Uprawnienia tajnego magazynu kluczy platformy Azure — zestaw](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy
Platforma Azure potrzebuje dostępu do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je maszynie Wirtualnej do uruchamiania i odszyfrowywania woluminów. Włącz szyfrowanie dysku w magazynie kluczy lub wdrożenia zakończy się niepowodzeniem.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą programu Azure PowerShell
 Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) magazynu kluczy, aby włączyć szyfrowanie dysku dla magazynu kluczy.

  - **Włącz magazyn kluczy do szyfrowania dysku:** EnabledForDiskEncryption jest wymagany do szyfrowania dysku Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Włącz magazyn kluczy do wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy odwołuje się w tworzeniu zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Włącz magazyn kluczy do wdrożenia szablonów, jeśli to konieczne:** Umożliwia usługi Azure Resource Manager, aby uzyskać wpisy tajne z tego magazynu kluczy, gdy ten magazyn kluczy odwołuje się do wdrożenia szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [aktualizacji az keyvault,](/cli/azure/keyvault#az-keyvault-update) aby włączyć szyfrowanie dysku dla magazynu kluczy. 

 - **Włącz magazyn kluczy do szyfrowania dysku:** Wymagane jest włączone szyfrowanie dysku. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Włącz magazyn kluczy do wdrożenia, jeśli to konieczne:** Zezwalaj maszynom wirtualnym na pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Włącz magazyn kluczy do wdrożenia szablonów, jeśli to konieczne:** Zezwalaj Menedżerowi zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pośrednictwem witryny Azure Portal

1. Wybierz keyvault, przejdź do **zasad dostępu**i **Kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole oznaczone jako **Włącz dostęp do szyfrowania dysków platformy Azure w celu szyfrowania woluminów**.
3. Wybierz **włącz dostęp do maszyn wirtualnych platformy Azure do wdrożenia** i/lub Włącz dostęp do usługi Azure Resource Manager do wdrożenia **szablonu,** jeśli to konieczne. 
4. Kliknij pozycję **Zapisz**.

![Zaawansowane zasady dostępu magazynu kluczy platformy Azure](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Konfigurowanie klucza szyfrowania klucza (opcjonalnie)
Jeśli chcesz użyć klucza szyfrowania klucza (KEK) dla dodatkowej warstwy zabezpieczeń kluczy szyfrowania, dodaj KEK do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można również zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [Dokumentacja magazynu kluczy](../../key-vault/keys/hsm-protected-keys.md). Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania klucza tajne szyfrowania przed zapisaniem do usługi Key Vault. 

* Podczas generowania kluczy należy użyć typu klucza RSA. Szyfrowanie dysków platformy Azure nie obsługuje jeszcze przy użyciu kluczy krzywej eliptycznej.

* Klucz tajny magazynu kluczy i adresy URL KEK muszą być wersjonetowane. Platforma Azure wymusza to ograniczenie przechowywania wersji. W przypadku prawidłowych adresów URL kluczy tajnych i KEK zobacz następujące przykłady:

  * Przykład prawidłowego tajnego adresu URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL KEK:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Szyfrowanie dysków platformy Azure nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL KEK. Przykłady nieobjętych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Niedopuszczalny adres URL magazynu kluczy*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Akceptowalny adres URL magazynu kluczy:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Konfigurowanie klucza szyfrowania klucza za pomocą programu Azure PowerShell 
Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi szyfrowania dysków platformy Azure, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku. Ten skrypt tworzy wszystkie wymagania wstępne szyfrowania dysków platformy Azure i szyfruje istniejącą maszynę wirtualną IaaS, zawijając klucz szyfrowania dysku przy użyciu klucza szyfrowania klucza. 

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

## <a name="certificate-based-authentication-optional"></a>Uwierzytelnianie oparte na certyfikatach (opcjonalnie)
Jeśli chcesz użyć uwierzytelniania certyfikatów, możesz przekazać go do magazynu kluczy i wdrożyć go do klienta. Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi szyfrowania dysków platformy Azure, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Uwierzytelnianie oparte na certyfikatach i KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu i zawinąć klucz szyfrowania kek, możesz użyć poniższego skryptu jako przykładu. Przed użyciem skryptu programu PowerShell należy zapoznać się ze wszystkimi poprzednimi wymaganiami wstępnymi szyfrowania dysków platformy Azure, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmian w twoim środowisku.

     
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

[Włącz szyfrowanie dysków platformy Azure za pomocą usługi Azure AD na maszynach wirtualnych z systemem Windows (poprzednia wersja)](disk-encryption-windows-aad.md)

---
title: Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące używania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3862a07eea2dcec3e67c0145fcdcff8140d19ec3
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746779"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release-for-linux-vms"></a>Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption z usługą Azure AD (poprzednią wersją) dla maszyn wirtualnych z systemem Linux

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/general/overview.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/general/secure-your-key-vault.md). 

Tworzenie i Konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption z usługą Azure AD (poprzednią wersją) obejmuje trzy kroki:

1. Utwórz magazyn kluczy. 
2. Skonfiguruj aplikację usługi Azure AD i nazwę główną usługi.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.
 
Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

Instrukcje dotyczące sposobu [instalowania narzędzi i łączenia się z platformą Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)można znaleźć w artykule głównym [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md) .

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [Azure Disk Encryption skrypt interfejsu wiersza polecenia wymagania wstępne](https://github.com/ejarvi/ade-cli-getting-started) i [Azure Disk Encryption skrypt programu PowerShell dotyczące wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy 
Azure Disk Encryption jest zintegrowana z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków oraz wpisami tajnymi w ramach subskrypcji magazynu kluczy. Możesz utworzyć magazyn kluczy lub użyć istniejącego do Azure Disk Encryption. Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/general/overview.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/general/secure-your-key-vault.md). Do utworzenia magazynu kluczy można użyć szablonu Menedżer zasobów, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. 


>[!WARNING]
>Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption potrzebują Key Vault i maszyn wirtualnych, które mają być umieszczone w tym samym regionie. Utwórz i użyj Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="create-a-key-vault-with-powershell"></a><a name="bkmk_KVPSH"></a> Tworzenie magazynu kluczy przy użyciu programu PowerShell

Można utworzyć magazyn kluczy z Azure PowerShell przy użyciu polecenia cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Aby uzyskać dodatkowe polecenia cmdlet dla Key Vault, zobacz [AZ. datamagazyn](/powershell/module/az.keyvault/). 

1. Utwórz nową grupę zasobów, w razie konieczności z poleceniem [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Aby wyświetlić listę lokalizacji centrów danych, użyj polecenie [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Tworzenie nowego magazynu kluczy przy użyciu polecenia [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Zanotuj **nazwę magazynu** , **nazwę grupy zasobów** , **Identyfikator zasobu** , identyfikator **URI magazynu** i **Identyfikator obiektu** , który jest zwracany do późniejszego użycia podczas szyfrowania dysków. 


### <a name="create-a-key-vault-with-azure-cli"></a><a name="bkmk_KVCLI"></a> Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Możesz zarządzać swoim magazynem kluczy za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ Key magazyn](/cli/azure/keyvault#commands) . Aby utworzyć magazyn kluczy, użyj [AZ Key magazyn Create](/cli/azure/keyvault#az-keyvault-create).

1. Utwórz nową grupę zasobów, w razie konieczności, za pomocą [AZ Group Create](/cli/azure/group#az-group-create). Aby wyświetlić listę lokalizacji, użyj [AZ Account List-Locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Utwórz nowy magazyn kluczy za pomocą polecenia [AZ Key webcreate](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Zanotuj **nazwę magazynu** (nazwę), **nazwę grupy zasobów** , **Identyfikator zasobu** (identyfikator), **Identyfikator URI magazynu** i **Identyfikator obiektu** , który jest zwracany do użycia później. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a><a name="bkmk_KVRM"></a> Tworzenie magazynu kluczy przy użyciu szablonu Menedżer zasobów

Magazyn kluczy można utworzyć przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure** .
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij przycisk **Kup** . 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a><a name="bkmk_ADapp"></a> Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi 
Aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure, Azure Disk Encryption generuje i zapisuje klucze szyfrowania do magazynu kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz w tym celu aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a><a name="bkmk_ADappPSH"></a> Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi przy użyciu Azure PowerShell 
Aby wykonać następujące polecenia, Pobierz [moduł Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)i użyj go. 

1. Użyj polecenia cmdlet [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) programu PowerShell, aby utworzyć aplikację usługi Azure AD. MyApplicationHomePage i MyApplicationUri mogą być dowolną wartością.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication. Identyfikator aplikacji to ClientID usługi Azure AD, a $aadClientSecret jest kluczem tajnym klienta, który będzie używany później do włączania Azure Disk Encryption. Odpowiednio Zabezpiecz klucz tajny klienta usługi Azure AD. `$azureAdApplication.ApplicationId`W systemie zostanie wyświetlony Identyfikator aplikacji.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a><a name="bkmk_ADappCLI"></a> Konfigurowanie aplikacji usługi Azure AD i jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Jednostkami usługi można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ AD Sp](/cli/azure/ad/sp) . Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Utwórz nową nazwę główną usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Zwrócony identyfikator appId to usługa Azure AD ClientID użyta w innych poleceniach. Jest to również nazwa SPN, która będzie używana przez AZ "Set-Policy". Hasło jest kluczem tajnym klienta, którego należy użyć później do włączenia Azure Disk Encryption. Odpowiednio Zabezpiecz klucz tajny klienta usługi Azure AD.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a><a name="bkmk_ADappRM"></a> Skonfiguruj aplikację usługi Azure AD i nazwę główną usługi, chociaż Azure Portal
Wykonaj kroki z [portalu use, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, która będzie mogła uzyskać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md) artykułu w celu utworzenia aplikacji usługi Azure AD. Każdy krok wymieniony poniżej przeprowadzi Cię bezpośrednio do sekcji artykułu, aby zakończyć. 

1. [Weryfikuj wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Tworzenie aplikacji Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - W przypadku tworzenia aplikacji możesz użyć dowolnej nazwy i adresu URL logowania.
3. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). 
     - Klucz uwierzytelniania jest kluczem tajnym klienta i jest używany jako AadClientSecret dla elementu Set-AzVMDiskEncryptionExtension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczenia do logowania się do usługi Azure AD. W Azure Portal ten klucz tajny nosi nazwę klucze, ale nie ma relacji do magazynów kluczy. Odpowiednio Zabezpiecz ten klucz tajny. 
     - Identyfikator aplikacji będzie później używany jako AadClientId dla Set-AzVMDiskEncryptionExtension i jako ServicePrincipalName dla właściwości Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a><a name="bkmk_KVAP"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Aby zapisać wpisy tajne szyfrowania do określonego Key Vault, Azure Disk Encryption potrzebuje identyfikatora klienta i klucza tajnego klienta aplikacji Azure Active Directory, która ma uprawnienia do zapisu wpisów tajnych do Key Vault. 

> [!NOTE]
> Azure Disk Encryption wymaga skonfigurowania następujących zasad dostępu do aplikacji klienckiej usługi Azure AD: _WrapKey_ i _Ustaw_ uprawnienia.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a><a name="bkmk_KVAPPSH"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD za pomocą Azure PowerShell
Aplikacja usługi Azure AD wymaga uprawnień dostępu do kluczy lub wpisów tajnych w magazynie. Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby udzielić uprawnień do aplikacji przy użyciu identyfikatora klienta (który został wygenerowany podczas rejestrowania aplikacji) jako wartość parametru _– ServicePrincipalName_ . Aby dowiedzieć się więcej, zobacz wpis w blogu [Azure Key Vault — krok](/archive/blogs/kv/azure-key-vault-step-by-step)po kroku. 

1. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi AD przy użyciu programu PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a><a name="bkmk_KVAPCLI"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [azexception Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) , aby ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2,0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Podaj nazwę główną usługi utworzoną za pośrednictwem interfejsu wiersza polecenia platformy Azure, aby pobrać klucze tajne i otoczyć je kluczem przy użyciu następującego polecenia:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a><a name="bkmk_KVAPRM"></a> Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu portalu

1. Otwórz grupę zasobów z magazynem kluczy.
2. Wybierz magazyn kluczy, przejdź do pozycji **zasady dostępu** , a następnie kliknij przycisk **Dodaj nowe** .
3. W obszarze **Wybierz podmiot zabezpieczeń** Wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. W przypadku **uprawnień kluczowych** zaznacz pole **Zawijanie klucza** w obszarze **operacje kryptograficzne** .
5. W przypadku uprawnień do wpisów **tajnych** zaznacz pozycję **Ustaw** w obszarze **operacje zarządzania kluczami tajnymi** .
6. Kliknij przycisk **OK** , aby zapisać zasady dostępu. 

![Azure Key Vault operacji kryptograficznych — klawisz zawijania](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault uprawnień tajnych — Ustawianie](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a><a name="bkmk_KVper"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy
Platforma Azure wymaga dostępu do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je na maszynie wirtualnej w celu rozruchu i odszyfrowywania woluminów. Włączenie szyfrowania dysków w magazynie kluczy lub wdrożenie zakończy się niepowodzeniem.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a><a name="bkmk_KVperPSH"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a><a name="bkmk_KVperCLI"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby włącz Key Vault wdrożenia:** Zezwól Virtual Machines na pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a><a name="bkmk_KVperrm"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure Portal

1. Wybierz swój magazyn kluczy, przejdź do pozycji **zasady dostępu** , a **następnie kliknij, aby wyświetlić zaawansowane zasady dostępu** .
2. Zaznacz pole o nazwie **Włącz dostęp do Azure Disk Encryption na potrzeby szyfrowania woluminów** .
3. Wybierz pozycję **Włącz dostęp do usługi Azure Virtual Machines w celu wdrożenia** i/lub **włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu** , jeśli jest to konieczne. 
4. Kliknij pozycję **Zapisz** .

![Zaawansowane zasady dostępu magazynu kluczy platformy Azure](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a><a name="bkmk_KEK"></a> Skonfiguruj klucz szyfrowania klucza (opcjonalnie)
Jeśli chcesz użyć klucza szyfrowania klucza (KEK) w celu uzyskania dodatkowej warstwy zabezpieczeń dla kluczy szyfrowania, Dodaj KEK do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Możesz również zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../../key-vault/keys/hsm-protected-keys.md). W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault. 

* Podczas generowania kluczy należy użyć typu klucza RSA. Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

* Klucze tajne magazynu kluczy i adresy URL KEK muszą mieć wersję. System Azure wymusza to ograniczenie wersji. Aby uzyskać prawidłowe tajne i KEK adresy URL, zobacz następujące przykłady:

  * Przykładowy prawidłowy tajny adres URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL KEK. Przykłady nieobsługiwanych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Nieakceptowalny adres URL magazynu kluczy  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Akceptowalny adres URL magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a><a name="bkmk_KEKPSH"></a> Skonfiguruj klucz szyfrowania klucza z Azure PowerShell 
Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi Azure Disk Encryption, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku. Ten skrypt tworzy wszystkie Azure Disk Encryption wymagania wstępne i szyfruje istniejącą maszynę wirtualną IaaS, zawijając klucz szyfrowania dysku przy użyciu klucza szyfrowania klucza. 

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
Jeśli chcesz użyć uwierzytelniania przy użyciu certyfikatu, możesz przekazać jeden do magazynu kluczy i wdrożyć go na kliencie. Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi Azure Disk Encryption, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a><a name="bkmk_CertKEK"></a> Uwierzytelnianie oparte na certyfikacie i KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu i otoczyć klucz szyfrowania KEK, możesz użyć poniższego skryptu jako przykładu. Przed użyciem skryptu programu PowerShell należy zapoznać się ze wszystkimi poprzednimi Azure Disk Encryption wstępnie wymaganymi elementami, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku.

> [!IMPORTANT]
> Uwierzytelnianie oparte na certyfikatach usługi Azure AD nie jest obecnie obsługiwane na maszynach wirtualnych z systemem Linux.



     
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

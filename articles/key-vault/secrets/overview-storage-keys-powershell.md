---
title: Azure Key Vault magazynu zarządzanego — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748488"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Zarządzanie kluczami konta magazynu przy użyciu Key Vault i Azure PowerShell
> [!IMPORTANT]
> Zalecamy korzystanie z integracji usługi Azure Storage z Azure Active Directory (Azure AD), usługą firmy Microsoft do zarządzania tożsamościami i dostępem w chmurze. Integracja z usługą Azure AD jest dostępna dla obiektów blob i kolejek platformy [Azure](../../storage/common/storage-auth-aad.md)oraz zapewnia oparty na tokenach dostęp OAuth2 do usługi Azure Storage (podobnie jak Azure Key Vault).
> Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika zamiast poświadczeń konta magazynu. Tożsamości zarządzanej usługi [Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) można używać podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub z aplikacją. Użyj poniższego rozwiązania tylko wtedy, gdy uwierzytelnianie usługi Azure AD nie jest możliwe.

Konto usługi Azure Storage używa poświadczeń składających się z nazwy konta i klucza. Klucz jest automatycznie generowany i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu, okresowo ponownie generując je na koncie magazynu i udostępnia tokeny sygnatury dostępu współdzielone w celu uzyskania delegowanego dostępu do zasobów na koncie magazynu.

Za pomocą funkcji Key Vault klucza konta magazynu zarządzanego można wyświetlać (synchronizować) klucze z kontem usługi Azure Storage i okresowo ponownie generować (obracać). Kluczami można zarządzać zarówno dla kont magazynu, jak i klasycznych kont magazynu.

W przypadku korzystania z funkcji klucza zarządzanego konta magazynu należy wziąć pod uwagę następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołanie.
- Tylko Key Vault zarządzać kluczami konta magazynu. Nie zarządzaj kluczami samodzielnie i unikaj zakłócania Key Vault procesów.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie zezwalaj na zarządzanie kluczami z wielu obiektów.
- Wygeneruj ponownie klucze tylko Key Vault generuj ponownie. Nie należy ręcznie ponownie generować kluczy konta magazynu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Identyfikator aplikacji jednostki usługi

Dzierżawa usługi Azure AD udostępnia każdej zarejestrowanej aplikacji [jednostkę usługi](../../active-directory/develop/developer-glossary.md#service-principal-object). Jednostką usługi jest identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem kontroli RBAC platformy Azure.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest rejestrowany pod tym samym identyfikatorem aplikacji w każdej chmurze platformy Azure.

| Dzierżawy | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj moduł Azure PowerShell .](/powershell/azure/install-az-ps)
- [Tworzenie magazynu kluczy](quick-create-powershell.md)
- [Utwórz konto usługi Azure Storage.](../../storage/common/storage-account-create.md?tabs=azure-powershell) Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi zawierać od 3 do 24 znaków.


## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```azurepowershell-interactive
Connect-AzAccount
```
Jeśli masz wiele subskrypcji platformy Azure, możesz wyświetlić je za pomocą polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) i określić subskrypcję, która ma być używania z poleceniem cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ustawianie zmiennych

Najpierw ustaw zmienne, które mają być używane przez polecenia cmdlet programu PowerShell w poniższych krokach. Pamiętaj o zaktualizowaniu symboli zastępczych "YourResourceGroupName", "YourStorageAccountName" i "YourKeyVaultName" oraz o wartości $keyVaultSpAppId (zgodnie z powyższym identyfikatorem aplikacji jednostki `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` usługi). [](#service-principal-application-id)

Użyjemy również Azure PowerShell cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) i [Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) aby uzyskać identyfikator użytkownika i kontekst konta usługi Azure Storage.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> W przypadku klasycznego konta magazynu użyj "podstawowego" i "pomocniczego" dla $storageAccountKey <br>
> Użyj parametru "Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName" zamiast "Get-AzStorageAccount" dla klasycznego konta magazynu

### <a name="give-key-vault-access-to-your-storage-account"></a>Zapewnianie Key Vault dostępu do konta magazynu

Aby Key Vault uzyskać dostęp do kluczy konta magazynu i zarządzać nimi, musisz autoryzować jego dostęp do konta magazynu. Aplikacja Key Vault wymaga uprawnień do generowania *listy* i *ponownego* generowania kluczy dla konta magazynu. Te uprawnienia są włączane za pośrednictwem roli wbudowanej platformy Azure Operator usługi klucza [konta magazynu.](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)

Przypisz tę rolę do jednostki Key Vault, ograniczając zakres do konta magazynu za pomocą Azure PowerShell cmdlet [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment)

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po pomyślnym przypisaniu roli powinny zostać wyświetlony dane wyjściowe podobne do następujących:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Jeśli Key Vault został już dodany do roli na koncie magazynu, otrzymasz *komunikat "Przypisanie roli już istnieje".* . Możesz również sprawdzić przypisanie roli, korzystając ze strony konta magazynu "Kontrola dostępu (IAM)" w Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj kontu użytkownika uprawnienia do kont magazynu zarządzanego

Użyj Azure PowerShell cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby zaktualizować zasady dostępu Key Vault magazynu i udzielić uprawnień konta magazynu do konta użytkownika.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Należy pamiętać, że uprawnienia dla kont magazynu nie są dostępne na stronie "Zasady dostępu" konta magazynu w Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Dodawanie zarządzanego konta magazynu do Key Vault magazynu

Użyj Azure PowerShell cmdlet [Add-AzKeyVaultManagedStorageAccount,](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) aby utworzyć konto magazynu zarządzanego w Key Vault wystąpienia. Przełącznik  `-DisableAutoRegenerateKey` określa WARTOŚĆ NIE, aby ponownie wygenerować klucze konta magazynu.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po pomyślnym dodaniem konta magazynu bez potrzeby odtłaniania klucza powinny zostać wyświetlony dane wyjściowe podobne do następującego przykładu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>Włączanie funkcji regeneruj klucz

Jeśli chcesz Key Vault okresowego ponownego generowania kluczy konta magazynu, możesz użyć polecenia cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount,](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) aby ustawić okres ponownego generowania. W tym przykładzie ustawimy okres generyzację na trzy dni. Gdy jest czas rotacji, Key Vault ponownie generuje klucz, który nie jest aktywny, a następnie ustawia nowo utworzony klucz jako aktywny. Tylko jeden z kluczy jest używany do wydawania tokenów SAS w dowolnym momencie. Jest to klucz aktywny.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po pomyślnym dodaniem konta magazynu przy użyciu regenerowanych kluczy powinny zostać wyświetlony dane wyjściowe podobne do następującego przykładu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>Tokeny sygnatury dostępu współdzielonych

Możesz również poprosić Key Vault o wygenerowanie tokenów sygnatury dostępu współdzielonych. Sygnatura dostępu współdzielona zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielona zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji wykonaj następujące czynności:

- Ustaw definicję sygnatury dostępu współdzielowego konta.
- Utwórz token sygnatury dostępu współdzielonych dla usług Blob, File, Table i Queue. Token jest tworzony dla typów zasobów Service, Container i Object. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault sygnatury dostępu współdzielonych magazynu zarządzanego w magazynie. Definicja zawiera szablon URI utworzonego tokenu sygnatury dostępu współdzielonych. Definicja ma typ sygnatury dostępu współdzielonych `account` i jest ważna przez N dni.
- Sprawdź, czy sygnatura dostępu współdzielona została zapisana w magazynie kluczy jako klucz tajny.
-
### <a name="set-variables"></a>Ustawianie zmiennych

Najpierw ustaw zmienne, które mają być używane przez polecenia cmdlet programu PowerShell w poniższych krokach. Pamiętaj, aby zaktualizować <YourStorageAccountName> symbole <YourKeyVaultName> zastępcze i .

Użyjemy również polecenia cmdlet Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) w celu uzyskania kontekstu konta usługi Azure Storage.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonych

Utwórz definicję sygnatury dostępu współdzielonego przy użyciu Azure PowerShell cmdlet [New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken)

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Wartość właściwości $sasToken będzie wyglądać podobnie do tej.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji sygnatury dostępu współdzielonych

Użyj polecenia cmdlet [Set-Azure PowerShell AzKeyVaultManagedStorageSasDefinition,](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) aby utworzyć definicję sygnatury dostępu współdzielonych.  Możesz podać nazwę dla wybranego `-Name` parametru.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji sygnatury dostępu współdzielonych

Możesz sprawdzić, czy definicja sygnatury dostępu współdzielonego została zapisana w magazynie kluczy przy użyciu polecenia cmdlet Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret)

Najpierw znajdź definicję sygnatury dostępu współdzielonych w magazynie kluczy.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Klucz tajny odpowiadający definicji sygnatury dostępu współdzielonego będzie miał następujące właściwości:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Teraz możesz użyć polecenia cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) z parametrami i , aby wyświetlić `VaultName` zawartość tego klucza `Name` tajnego.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Dane wyjściowe tego polecenia będą zawierały ciąg definicji sygnatury dostępu współdzielonego.

## <a name="next-steps"></a>Następne kroki

- [Przykłady kluczy konta magazynu zarządzanego](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault programu PowerShell](/powershell/module/az.keyvault/#key_vault)

---
title: Konto magazynu zarządzanego usługi Azure Key Vault — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między usługą Azure Key Vault a kontem magazynu platformy Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427785"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Zarządzanie kluczami kont magazynu za pomocą usługi Key Vault i azure PowerShell

Konto magazynu platformy Azure używa poświadczeń zawierających nazwę konta i klucz. Klucz jest automatyczniegenerowany i służy jako hasło, a nie jako klucz kryptograficzny. Usługa Key Vault zarządza kluczami kont magazynu, przechowując je jako wpisy tajne usługi Key Vault. 

Funkcji klucza konta zarządzanego magazynu usługi Key Vault można używać do wystawiania (synchronizowania) kluczy z kontem magazynu platformy Azure i okresowego ponownego generowania (obracania) kluczy. Można zarządzać kluczami zarówno dla kont magazynu, jak i kont magazynu klasycznego.

Korzystając z funkcji klucza zarządzanego konta magazynu, należy wziąć pod uwagę następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołującego.
- Tylko Usługa Key Vault powinna zarządzać kluczami konta magazynu. Nie zarządzaj kluczami samodzielnie i unikaj zakłócania procesów usługi Key Vault.
- Tylko jeden obiekt usługi Key Vault powinien zarządzać kluczami konta magazynu. Nie zezwalaj na zarządzanie kluczami z wielu obiektów.
- Można zażądać usługi Key Vault do zarządzania kontem magazynu z jednostką użytkownika, ale nie z jednostką usługi.
- Ponowne generowanie kluczy przy użyciu tylko magazynu kluczy. Nie należy ręcznie ponownie wygenerować kluczy konta magazynu.

Zalecamy korzystanie z integracji usługi Azure Storage z usługą Azure Active Directory (Azure AD), chmurową usługą zarządzania tożsamościami i dostępem firmy Microsoft. Integracja usługi Azure AD jest dostępna dla [obiektów blob i kolejek platformy Azure](../../storage/common/storage-auth-aad.md)i zapewnia dostęp do usługi Azure Storage oparty na tokenach OAuth2 (podobnie jak usługa Azure Key Vault).

Usługa Azure AD umożliwia uwierzytelnienie aplikacji klienckiej przy użyciu aplikacji lub tożsamości użytkownika, zamiast poświadczeń konta magazynu. Podczas uruchamiania na platformie Azure można użyć [tożsamości zarządzanej usługi Azure AD.](/azure/active-directory/managed-identities-azure-resources/) Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub z aplikacją.

Usługa Azure AD używa kontroli dostępu opartej na rolach (RBAC) do zarządzania autoryzacją, która jest również obsługiwana przez usługę Key Vault.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Identyfikator aplikacji głównej usługi

Dzierżawa usługi Azure AD udostępnia każdej zarejestrowanej aplikacji z [jednostką usługi.](/azure/active-directory/develop/developer-glossary#service-principal-object) Podmiot usługi służy jako identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem funkcji RBAC.

Usługa Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Usługa Key Vault jest zarejestrowana pod tym samym identyfikatorem aplikacji w każdej chmurze platformy Azure.

| Dzierżawy | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj moduł programu Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Tworzenie magazynu kluczy](quick-create-powershell.md)
- [Utwórz konto magazynu platformy Azure](../../storage/common/storage-account-create.md?tabs=azure-powershell). Nazwa konta magazynu musi używać tylko małych liter i cyfr. Długość nazwy musi wynosić od 3 do 24 znaków.
      

## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami kont magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnianie sesji programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Jeśli masz wiele subskrypcji platformy Azure, możesz wyświetlić je przy użyciu polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) i określić subskrypcję, której chcesz używać z poleceniem cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ustawianie zmiennych

Najpierw ustaw zmienne, które mają być używane przez polecenia cmdlet programu PowerShell w poniższych krokach. <YourResourceGroupName>Należy zaktualizować symbole <YourStorageAccountName>zastępcze i <YourKeyVaultName> symbole zastępcze i `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` ustawić $keyVaultSpAppId (zgodnie z [powyższym identyfikatorem aplikacji głównej usługi).](#service-principal-application-id)

Firma Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) i [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) będzie również używać poleceń cmdlet usługi Azure PowerShell, aby uzyskać identyfikator użytkownika i kontekst konta usługi Azure storage.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Udzielanie dostępu do konta magazynu usługi Key Vault

Aby usługa Key Vault mogła uzyskiwać dostęp do kluczy konta magazynu i zarządzać nimi, należy autoryzować jego dostęp do konta magazynu. Aplikacja Usługi Key Vault wymaga uprawnień do *wyświetlania* i *ponownego generowania* kluczy dla konta magazynu. Uprawnienia te są włączone za pośrednictwem wbudowanej roli RBAC [roli Operator klucza konta magazynu roli operatora klucza](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Assign this role to the Key Vault service principal, limiting scope to your storage account, using the Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet.

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po pomyślnym przypisaniu roli powinny zostać wyświetlene dane wyjściowe podobne do następującego przykładu:

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

Jeśli Magazyn kluczy został już dodany do roli na koncie magazynu, otrzymasz *"Przypisanie roli już istnieje".* . Można również zweryfikować przypisanie roli, używając konta magazynu "Kontrola dostępu (IAM)" strona w witrynie Azure portal.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj uprawnienia konta użytkownika zarządzanym kontom magazynu

Użyj polecenia cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) aby zaktualizować zasady dostępu usługi Key Vault i udzielić uprawnień konta magazynu do konta użytkownika.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Należy zauważyć, że uprawnienia dla kont magazynu nie są dostępne na stronie "Zasady dostępu" konta magazynu w witrynie Azure portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Dodawanie zarządzanego konta magazynu do wystąpienia usługi Key Vault

Użyj polecenia cmdlet [dodatku](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) Programu Azure PowerShell, aby utworzyć konto zarządzanego magazynu w wystąpieniu usługi Key Vault. Przełącznik `-DisableAutoRegenerateKey` określa NIE do ponownego generowania kluczy konta magazynu.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po pomyślnym dodaniu konta magazynu bez regeneracji klucza, powinieneś zobaczyć dane wyjściowe podobne do następującego przykładu:

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

### <a name="enable-key-regeneration"></a>Włącz regenerację kluczy

Jeśli chcesz, aby usługa Key Vault okresowo regenerować klucze konta magazynu, możesz użyć polecenia cmdlet konta dodatku Azure PowerShell [Add-AzKeyVaultManagedStorageAccount,](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) aby ustawić okres regeneracji. W tym przykładzie ustawiliśmy okres regeneracji wynoszący trzy dni. Po trzech dniach Magazyn kluczy zregeneruje "key2" i zamieni aktywny klucz z "key2" na "key1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po pomyślnym dodaniu konta magazynu z regeneracją kluczy, powinien zostać wyświetlony wynik podobny do następującego przykładu:

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

## <a name="shared-access-signature-tokens"></a>Tokeny podpisu dostępu współdzielonego

Można również poprosić Magazyn kluczy o wygenerowanie tokenów podpisu dostępu współdzielonego. Podpis dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz przyznać klientom dostęp do zasobów na koncie magazynu bez udostępniania kluczy konta. Podpis dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji uzupełniają następujące czynności:

- Ustawianie definicji podpisu dostępu współdzielonego konta. 
- Utwórz token podpisu udostępnionego dostępu do konta dla usług obiektów Blob, File, Table i Queue. Token jest tworzony dla typów zasobów Usługi, kontenera i obiektu. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem https i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw definicję sygnatury dostępu współdzielonego magazynu zarządzanego magazynu kluczy w przechowalni. Definicja ma identyfikator URI szablonu tokenu podpisu dostępu udostępnionego, który został utworzony. Definicja ma typ `account` podpisu dostępu współdzielonego i jest prawidłowa dla N dni.
- Sprawdź, czy podpis dostępu współdzielonego został zapisany w magazynie kluczy jako klucz tajny.
- 
### <a name="set-variables"></a>Ustawianie zmiennych

Najpierw ustaw zmienne, które mają być używane przez polecenia cmdlet programu PowerShell w poniższych krokach. Pamiętaj, aby <YourStorageAccountName> zaktualizować <YourKeyVaultName> symbole zastępcze i zastępcze.

Firma Microsoft będzie również używać poleceń cmdlet azure powershell [new-AzStorageContext,](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) aby uzyskać kontekst konta usługi Azure storage.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu podpisu dostępu udostępnionego

Utwórz definicję podpisu dostępu udostępnionego przy użyciu poleceń cmdlet Azure PowerShell [New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Wartość $sasToken będzie wyglądać podobnie do tego.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji podpisu dostępu współdzielonego

Użyj polecenia cmdlet azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition,](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) aby utworzyć definicję podpisu dostępu udostępnionego.  Można podać nazwę wybranego parametru. `-Name`

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji podpisu dostępu współdzielonego

Można sprawdzić, czy definicja podpisu dostępu udostępnionego została przechowywana w magazynie kluczy przy użyciu polecenia cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) usługi Azure PowerShell.

Najpierw znajdź definicję podpisu dostępu współdzielonego w magazynie kluczy.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Klucz tajny odpowiadający definicji sygnatury dostępu Współdzielonego będzie miał następujące właściwości:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Teraz można użyć polecenia cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) i właściwości tajnej, `Name` aby wyświetlić zawartość tego klucza tajnego.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Dane wyjściowe tego polecenia będą wyświetlane ciąg definicji sygnatury dostępu Współdzielonego.


## <a name="next-steps"></a>Następne kroki

- [Próbki kluczy kont zarządzanego magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Odwołanie do programu Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

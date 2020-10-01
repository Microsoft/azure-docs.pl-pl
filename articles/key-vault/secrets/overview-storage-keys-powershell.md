---
title: Azure Key Vault zarządzane konto magazynu — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: b667254ece93c083de95728abe0ddecd5cfed197
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612374"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Zarządzanie kluczami konta magazynu przy użyciu Key Vault i Azure PowerShell

Konto usługi Azure Storage używa poświadczeń składających się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu przez okresowe ponowne generowanie ich na koncie magazynu i zapewnia tokeny sygnatury dostępu współdzielonego dla delegowanego dostępu do zasobów na koncie magazynu.

Za pomocą funkcji klucza zarządzanego magazynu Key Vault można wyświetlać (synchronizować) klucze za pomocą konta usługi Azure Storage i ponownie generować (obrócić) klucze okresowo. Można zarządzać kluczami zarówno dla kont magazynu, jak i klasycznych kont magazynu.

Korzystając z funkcji zarządzanego klucza konta magazynu, należy rozważyć następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Tylko Key Vault powinny zarządzać kluczami konta magazynu. Nie Zarządzaj kluczami samodzielnie i unikaj zakłócania procesów Key Vault.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie Zezwalaj na zarządzanie kluczami z wielu obiektów.
- Wygeneruj ponownie klucze, używając tylko Key Vault. Nie należy ręcznie generować ponownie kluczy konta magazynu.

Zalecamy korzystanie z integracji usługi Azure Storage z usługą Azure Active Directory (Azure AD), opartą na chmurze firmą Microsoft do zarządzania tożsamościami i dostępem. Integracja z usługą Azure AD jest dostępna dla [obiektów blob i kolejek platformy Azure](../../storage/common/storage-auth-aad.md)oraz zapewnia dostęp oparty na tokenach OAuth2 do usługi Azure Storage (podobnie jak Azure Key Vault).

Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczeń konta magazynu. Możesz użyć [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub w aplikacjach.

Usługa Azure AD używa kontroli dostępu opartej na rolach (RBAC) do zarządzania autoryzacją, która również jest obsługiwana przez Key Vault.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Identyfikator aplikacji głównej usługi

Dzierżawa usługi Azure AD udostępnia każdą zarejestrowaną aplikację z jednostką [usługi](/azure/active-directory/develop/developer-glossary#service-principal-object). Jednostka usługi służy jako identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem RBAC.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest zarejestrowany w ramach tego samego identyfikatora aplikacji w każdej chmurze platformy Azure.

| Dzierżawy | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj moduł Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Tworzenie magazynu kluczy](quick-create-powershell.md)
- [Utwórz konto usługi Azure Storage](../../storage/common/storage-account-create.md?tabs=azure-powershell). Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi wynosić od 3 do 24 znaków.
      

## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 

```azurepowershell-interactive
Connect-AzAccount
```
Jeśli masz wiele subskrypcji platformy Azure, możesz je wyświetlić za pomocą polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) i określić subskrypcję, której chcesz używać za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ustaw zmienne

Najpierw Ustaw zmienne, które będą używane przez polecenia cmdlet programu PowerShell w poniższych krokach. Pamiętaj, aby zaktualizować symbole zastępcze "YourResourceGroupName", "YourStorageAccountName" i "YourKeyVaultName", a następnie ustaw $keyVaultSpAppId na `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (zgodnie z opisem w [identyfikatorze aplikacji nazwy głównej usługi](#service-principal-application-id)).

Będziemy również Azure PowerShell używać poleceń cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) i [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) w celu uzyskania identyfikatora użytkownika i kontekstu konta usługi Azure Storage.

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
> W przypadku klasycznego konta magazynu Użyj "podstawowe" i "pomocnicze" dla $storageAccountKey <br>
> Użyj polecenia "Get-AzResource-Name" ClassicStorageAccountName "-ResourceGroupName $resourceGroupName" zamiast of'Get-AzStorageAccount "dla klasycznego konta magazynu

### <a name="give-key-vault-access-to-your-storage-account"></a>Przyznaj Key Vault dostęp do konta magazynu

Aby Key Vault mógł uzyskać dostęp do kluczy konta magazynu i zarządzać nimi, musisz autoryzować swój dostęp do konta magazynu. Aplikacja Key Vault wymaga uprawnień do *wyświetlania* i ponownego *generowania* kluczy dla konta magazynu. Te uprawnienia są włączane za pomocą wbudowanej [roli usługi operatora kluczy konta magazynu](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)ról platformy Azure. 

Przypisz tę rolę do jednostki usługi Key Vault, ograniczając zakres do konta magazynu przy użyciu polecenia cmdlet Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) .

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po pomyślnym przypisaniu roli powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

Jeśli Key Vault został już dodany do roli na koncie magazynu, zostanie wyświetlony komunikat *"przypisanie roli już istnieje".* . Możesz również zweryfikować przypisanie roli przy użyciu strony "kontrola dostępu (IAM)" konta magazynu w Azure Portal.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj kontu użytkownika uprawnienia do zarządzanych kont magazynu

Użyj polecenia cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) , aby zaktualizować zasady dostępu Key Vault i przyznać uprawnienia konta magazynu dla konta użytkownika.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Należy pamiętać, że uprawnienia dla kont magazynu nie są dostępne na stronie "zasady dostępu" konta magazynu w Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Dodawanie zarządzanego konta magazynu do wystąpienia Key Vault

Użyj Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) polecenia cmdlet, aby utworzyć zarządzane konto magazynu w wystąpieniu Key Vault. `-DisableAutoRegenerateKey`Przełącznik określa, aby nie generować ponownie kluczy konta magazynu.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po pomyślnym dodaniu konta magazynu bez ponownego wygenerowania klucza powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

### <a name="enable-key-regeneration"></a>Włącz ponowne generowanie klucza

Jeśli chcesz, aby Key Vault okresowo generować ponownie klucze konta magazynu, możesz użyć polecenia cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) Azure PowerShell w celu ustawienia okresu regeneracji. W tym przykładzie ustawimy okres regeneracji o trzy dni. Gdy czas jest obracany, Key Vault ponownie generuje klucz, który nie jest aktywny, a następnie ustawia nowo utworzony klucz jako aktywny. Tylko jeden z kluczy jest używany do wystawiania tokenów SAS w dowolnym momencie. Jest to klucz aktywny.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po pomyślnym dodaniu konta magazynu z ponownym generowaniem kluczy powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

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

## <a name="shared-access-signature-tokens"></a>Tokeny sygnatury dostępu współdzielonego

Możesz również poproszenie Key Vault o generowanie tokenów sygnatury dostępu współdzielonego. Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji pełnią następujące czynności:

- Ustaw definicję sygnatury dostępu współdzielonego konta. 
- Utwórz token sygnatury dostępu współdzielonego konta dla usług obiektów blob, plików, tabel i kolejek. Token jest tworzony dla usług, kontenerów i obiektów typu zasób. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault zarządzaną definicję sygnatury dostępu współdzielonego magazynu w magazynie. Definicja zawiera identyfikator URI szablonu utworzonego tokenu sygnatury dostępu współdzielonego. Definicja zawiera typ sygnatury dostępu współdzielonego `account` i jest ważna przez N dni.
- Sprawdź, czy sygnatura dostępu współdzielonego została zapisana w magazynie kluczy jako wpis tajny.
- 
### <a name="set-variables"></a>Ustaw zmienne

Najpierw Ustaw zmienne, które będą używane przez polecenia cmdlet programu PowerShell w poniższych krokach. Pamiętaj, aby zaktualizować <YourStorageAccountName> <YourKeyVaultName> symbole zastępcze i.

Użyjemy również Azure PowerShell polecenia cmdlet [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) , aby uzyskać kontekst konta usługi Azure Storage.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonego

Utwórz definicję sygnatury dostępu współdzielonego za pomocą poleceń cmdlet Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) .
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Wartość $sasToken będzie wyglądać podobnie do tego.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji sygnatury dostępu współdzielonego

Za pomocą polecenia cmdlet Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) Utwórz definicję sygnatury dostępu współdzielonego.  Możesz podać wybraną nazwę `-Name` parametru.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji sygnatury dostępu współdzielonego

Możesz sprawdzić, czy definicja sygnatury dostępu współdzielonego została zapisana w magazynie kluczy za pomocą polecenia cmdlet Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) .

Najpierw Znajdź definicję sygnatury dostępu współdzielonego w magazynie kluczy.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Wpis tajny odpowiadający definicji sygnatury dostępu współdzielonego będzie miał następujące właściwości:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Teraz można użyć polecenia cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) i właściwości Secret, `Name` Aby wyświetlić zawartość tego klucza tajnego.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Dane wyjściowe tego polecenia będą zawierać ciąg definicji sygnatury dostępu współdzielonego.


## <a name="next-steps"></a>Następne kroki

- [Przykłady kluczy zarządzanego konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Dokumentacja programu Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

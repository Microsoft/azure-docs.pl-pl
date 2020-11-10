---
title: Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację między Azure Key Vault i dostępem opartym na kluczach do konta usługi Azure Storage.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 47427f8d3690218060fd1e6221b1b089c68d6e1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441838"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami konta magazynu przy użyciu Key Vault i interfejsu wiersza polecenia platformy Azure

Konto usługi Azure Storage używa poświadczeń składających się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu przez okresowe ponowne generowanie ich na koncie magazynu i zapewnia tokeny sygnatury dostępu współdzielonego dla delegowanego dostępu do zasobów na koncie magazynu.

Za pomocą funkcji klucza zarządzanego magazynu Key Vault można wyświetlać (synchronizować) klucze za pomocą konta usługi Azure Storage i ponownie generować (obrócić) klucze okresowo. Można zarządzać kluczami zarówno dla kont magazynu, jak i klasycznych kont magazynu.

Korzystając z funkcji zarządzanego klucza konta magazynu, należy rozważyć następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Tylko Key Vault powinny zarządzać kluczami konta magazynu. Nie Zarządzaj kluczami samodzielnie i unikaj zakłócania procesów Key Vault.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie Zezwalaj na zarządzanie kluczami z wielu obiektów.
- Wygeneruj ponownie klucze, używając tylko Key Vault. Nie należy ręcznie generować ponownie kluczy konta magazynu.

Zalecamy korzystanie z integracji usługi Azure Storage z usługą Azure Active Directory (Azure AD), opartą na chmurze firmą Microsoft do zarządzania tożsamościami i dostępem. Integracja z usługą Azure AD jest dostępna dla [obiektów blob i kolejek platformy Azure](../../storage/common/storage-auth-aad.md)oraz zapewnia dostęp oparty na tokenach OAuth2 do usługi Azure Storage (podobnie jak Azure Key Vault).

Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczeń konta magazynu. Możesz użyć [tożsamości zarządzanej usługi Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub w aplikacjach.

Usługa Azure AD używa kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure do zarządzania autoryzacją, która również jest obsługiwana przez Key Vault.

## <a name="service-principal-application-id"></a>Identyfikator aplikacji głównej usługi

Dzierżawa usługi Azure AD udostępnia każdą zarejestrowaną aplikację z jednostką [usługi](../../active-directory/develop/developer-glossary.md#service-principal-object). Jednostka usługi służy jako identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem usługi Azure RBAC.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest zarejestrowany w ramach tego samego identyfikatora aplikacji w każdej chmurze platformy Azure.

| Dzierżawy | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- [Tworzenie magazynu kluczy](quick-create-cli.md)
- [Utwórz konto usługi Azure Storage](../../storage/common/storage-account-create.md?tabs=azure-cli). Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi wynosić od 3 do 24 znaków.
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję interfejsu wiersza polecenia platformy Azure za pomocą polecenia [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Przyznaj Key Vault dostęp do konta magazynu

Użyj interfejsu wiersza polecenia platformy Azure [AZ role Create](/cli/azure/role/assignment?view=azure-cli-latest) , aby nadać Key Vault dostęp do konta magazynu. Podaj następujące wartości parametrów polecenia:

- `--role`: Przekaż rolę usługi roli Azure "operator klucza konta magazynu". Ta rola ogranicza zakres dostępu do konta magazynu. W przypadku klasycznego konta magazynu należy zamiast tego przekazać "rolę usługi operatora klucza klasycznego konta magazynu".
- `--assignee`: Przekaż wartość " https://vault.azure.net ", czyli adres url Key Vault w chmurze publicznej platformy Azure. (W przypadku usługi Azure rządu Cloud należy użyć polecenia "--asingee-Object-ID"), zobacz [Identyfikator aplikacji głównej usługi](#service-principal-application-id).)
- `--scope`: Przekaż identyfikator zasobu konta magazynu, który znajduje się w formularzu `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Aby znaleźć identyfikator subskrypcji, użyj polecenia [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) usługi Azure CLI, Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj kontu użytkownika uprawnienia do zarządzanych kont magazynu

Użyj interfejsu wiersza polecenia platformy Azure AZ destorage [-Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , aby zaktualizować zasady dostępu Key Vault i przyznać uprawnienia konta magazynu dla konta użytkownika.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Należy pamiętać, że uprawnienia dla kont magazynu nie są dostępne na stronie "zasady dostępu" konta magazynu w Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie konta magazynu zarządzanego Key Vault

 Utwórz konto magazynu zarządzanego przez usługę Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure AZ datamagazyn [Storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) polecenie. Ustaw okres regeneracji wynoszący 90 dni. Gdy czas ma być obracany, Magazyn kluczy generuje ponownie klucz, który nie jest aktywny, a następnie ustawia nowo utworzony klucz jako aktywny. Tylko jeden z kluczy jest używany do wystawiania tokenów SAS w dowolnym momencie, jest to klucz aktywny. Podaj następujące wartości parametrów polecenia:

- `--vault-name`: Przekaż nazwę magazynu kluczy. Aby znaleźć nazwę magazynu kluczy, użyj polecenia [AZ Key list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`: Przekaż nazwę konta magazynu. Aby znaleźć nazwę konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.
- `--resource-id`: Przekaż identyfikator zasobu konta magazynu, który znajduje się w formularzu `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Aby znaleźć identyfikator subskrypcji, użyj polecenia [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) usługi Azure CLI, Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny sygnatury dostępu współdzielonego

Możesz również poproszenie Key Vault o generowanie tokenów sygnatury dostępu współdzielonego. Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji pełnią następujące czynności:

- Ustaw definicję sygnatury dostępu współdzielonego konta `<YourSASDefinitionName>` . Definicja jest ustawiana na Key Vault zarządzanym koncie magazynu `<YourStorageAccountName>` w magazynie kluczy `<YourKeyVaultName>` .
- Utwórz token sygnatury dostępu współdzielonego konta dla usług obiektów blob, plików, tabel i kolejek. Token jest tworzony dla usług, kontenerów i obiektów typu zasób. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault zarządzaną definicję sygnatury dostępu współdzielonego magazynu w magazynie. Definicja zawiera identyfikator URI szablonu utworzonego tokenu sygnatury dostępu współdzielonego. Definicja zawiera typ sygnatury dostępu współdzielonego `account` i jest ważna przez N dni.
- Sprawdź, czy sygnatura dostępu współdzielonego została zapisana w magazynie kluczy jako wpis tajny.

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonego

Utwórz definicję sygnatury dostępu współdzielonego przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Storage account Generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Ta operacja wymaga `storage` uprawnień i `setsas` .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po pomyślnym uruchomieniu operacji skopiuj dane wyjściowe.

```console
"se=2020-01-01&sp=***"
```

Te dane wyjściowe będą przekazywane do `--template-uri` parametru w następnym kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji sygnatury dostępu współdzielonego

Aby utworzyć definicję sygnatury dostępu współdzielonego, użyj interfejsu wiersza polecenia platformy Azure [AZ the Storage sygnatur SAS-Definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) , przekazując dane wyjściowe z poprzedniego kroku do `--template-uri` parametru.  Możesz podać wybraną nazwę `-n` parametru.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji sygnatury dostępu współdzielonego

Możesz sprawdzić, czy definicja sygnatury dostępu współdzielonego została zapisana w magazynie kluczy przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Key Storage magazyn SAS-Definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) .

Teraz można użyć polecenia [AZ Storage magazynu SAS-Definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) i `id` właściwości, aby wyświetlić zawartość tego klucza tajnego.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](/rest/api/keyvault/).
- Zapoznaj się z artykułami na [blogu zespołu Azure Key Vault](/archive/blogs/kv/).
- Zapoznaj się z dokumentacją [AZ magazynu Storage](/cli/azure/keyvault/storage?view=azure-cli-latest) Reference.

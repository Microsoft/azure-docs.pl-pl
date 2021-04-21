---
title: Zarządzanie kluczami konta magazynu za pomocą Azure Key Vault i interfejsu wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację Azure Key Vault dostępem opartym na kluczach do konta usługi Azure Storage.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 573e4c9d8db3f07f223826ab648f2ef57e1d9c58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766321"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami konta magazynu za pomocą Key Vault i interfejsu wiersza polecenia platformy Azure
> [!IMPORTANT]
> Zalecamy korzystanie z integracji usługi Azure Storage z Azure Active Directory (Azure AD), usługą firmy Microsoft do zarządzania tożsamościami i dostępem w chmurze. Integracja z usługą Azure AD jest dostępna dla obiektów blob i kolejek platformy [Azure](../../storage/common/storage-auth-aad.md)oraz zapewnia oparty na tokenach dostęp OAuth2 do usługi Azure Storage (podobnie jak Azure Key Vault). Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika zamiast poświadczeń konta magazynu. Tożsamości zarządzanej usługi [Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) można używać podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub z aplikacją. Użyj poniższego rozwiązania tylko wtedy, gdy uwierzytelnianie usługi Azure AD nie jest możliwe.

Konto usługi Azure Storage używa poświadczeń składających się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu, okresowo ponownie generując je na koncie magazynu i udostępnia tokeny sygnatury dostępu współdzielone w celu uzyskania delegowanego dostępu do zasobów na koncie magazynu.

Za pomocą funkcji Key Vault klucza konta magazynu zarządzanego można wyświetlać (synchronizować) klucze z kontem usługi Azure Storage i okresowo ponownie generować (obracać). Kluczami można zarządzać zarówno dla kont magazynu, jak i klasycznych kont magazynu.

W przypadku korzystania z funkcji klucza zarządzanego konta magazynu należy wziąć pod uwagę następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołanie.
- Tylko Key Vault zarządzać kluczami konta magazynu. Nie zarządzaj kluczami samodzielnie i unikaj zakłócania Key Vault procesów.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie zezwalaj na zarządzanie kluczami z wielu obiektów.
- Wygeneruj ponownie klucze tylko Key Vault generuj ponownie. Nie należy ręcznie ponownie generować kluczy konta magazynu.

## <a name="service-principal-application-id"></a>Identyfikator aplikacji jednostki usługi

Dzierżawa usługi Azure AD udostępnia każdej zarejestrowanej aplikacji [jednostkę usługi](../../active-directory/develop/developer-glossary.md#service-principal-object). Jednostką usługi jest identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskiwania dostępu do innych zasobów platformy Azure za pośrednictwem kontroli dostępu na poziomie ról platformy Azure.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest rejestrowany pod tym samym identyfikatorem aplikacji w każdej chmurze platformy Azure.

| Dzierżawy | Chmura | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure — publiczna | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
- [Tworzenie magazynu kluczy](quick-create-cli.md)
- [Utwórz konto usługi Azure Storage.](../../storage/common/storage-account-create.md?tabs=azure-cli) Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi zawierać od 3 do 24 znaków.
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję interfejsu wiersza polecenia platformy Azure przy użyciu [poleceń az login.](/powershell/module/az.accounts/connect-azaccount)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Zapewnianie Key Vault dostępu do konta magazynu

Użyj polecenia [az role assignment create](/cli/azure/role/assignment) interfejsu wiersza polecenia platformy Azure, Key Vault uzyskać dostęp do konta magazynu. Podaj następujące wartości parametrów polecenia:

- `--role`: przekaż rolę platformy Azure "Rola usługi operatora klucza konta magazynu". Ta rola ogranicza zakres dostępu do konta magazynu. W przypadku klasycznego konta magazynu zamiast tego przekaż "Rolę usługi klasycznego operatora klucza konta magazynu".
- `--assignee`: przekaż wartość " https://vault.azure.net ", która jest adresem URL Key Vault w chmurze publicznej Platformy Azure. (W przypadku chmury usługi Azure Goverment użyj zamiast tego identyfikatora "--asingee-object-id", zobacz Service principal application ID (Identyfikator aplikacji [jednostki usługi).](#service-principal-application-id)
- `--scope`: przekaż identyfikator zasobu konta magazynu w postaci `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Aby znaleźć identyfikator subskrypcji, użyj polecenia [az account list interfejsu wiersza polecenia platformy](/cli/azure/account?#az_account_list) Azure; Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [az storage account list](/cli/azure/storage/account?#az_storage_account_list) interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj kontu użytkownika uprawnienia do kont magazynu zarządzanego

Użyj polecenia cmdlet [az keyvault-set-policy](/cli/azure/keyvault?#az_keyvault_set_policy) interfejsu wiersza polecenia platformy Azure, aby zaktualizować zasady dostępu Key Vault i udzielić uprawnień konta magazynu do konta użytkownika.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Należy pamiętać, że uprawnienia dla kont magazynu nie są dostępne na stronie "Zasady dostępu" konta magazynu w Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie konta Key Vault magazynu zarządzanego

 Utwórz zarządzane Key Vault magazynu za pomocą polecenia [az keyvault storage](/cli/azure/keyvault/storage?#az_keyvault_storage_add) interfejsu wiersza polecenia platformy Azure. Ustaw okres odnawiania na 90 dni. Gdy jest czas rotacji, usługa KeyVault ponownie generuje klucz, który nie jest aktywny, a następnie ustawia nowo utworzony klucz jako aktywny. Tylko jeden z kluczy jest używany do wydawania tokenów SAS w dowolnym momencie. Jest to klucz aktywny. Podaj następujące wartości parametrów polecenia:

- `--vault-name`: przekaż nazwę magazynu kluczy. Aby znaleźć nazwę magazynu kluczy, użyj polecenia [az keyvault list](/cli/azure/keyvault?#az_keyvault_list) interfejsu wiersza polecenia platformy Azure.
- `-n`: przekaż nazwę konta magazynu. Aby znaleźć nazwę konta magazynu, użyj polecenia [az storage account list](/cli/azure/storage/account?#az_storage_account_list) interfejsu wiersza polecenia platformy Azure.
- `--resource-id`: przekaż identyfikator zasobu konta magazynu w postaci `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Aby znaleźć identyfikator subskrypcji, użyj polecenia [az account list interfejsu wiersza polecenia platformy](/cli/azure/account?#az_account_list) Azure; Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [az storage account list](/cli/azure/storage/account?#az_storage_account_list) interfejsu wiersza polecenia platformy Azure.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny sygnatury dostępu współdzielonych

Możesz również poprosić Key Vault o wygenerowanie tokenów sygnatury dostępu współdzielonych. Sygnatura dostępu współdzielona zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielona zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji wykonaj następujące czynności:

- Ustaw definicję sygnatury dostępu współdzielonych konta `<YourSASDefinitionName>` . Definicja jest ustawiana na Key Vault magazynu zarządzanego `<YourStorageAccountName>` w magazynie `<YourKeyVaultName>` kluczy.
- Utwórz token sygnatury dostępu współdzielonych dla usług Blob, File, Table i Queue. Token jest tworzony dla typów zasobów Service, Container i Object. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault sygnatury dostępu współdzielonych magazynu zarządzanego w magazynie. Definicja zawiera szablon URI utworzonego tokenu sygnatury dostępu współdzielonych. Definicja ma typ sygnatury dostępu współdzielonych `account` i jest ważna przez N dni.
- Sprawdź, czy sygnatura dostępu współdzielona została zapisana w magazynie kluczy jako klucz tajny.

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonych

Utwórz definicję sygnatury dostępu współdzielonego za pomocą polecenia [az storage account generate-sas](/cli/azure/storage/account?#az_storage_account_generate_sas) interfejsu wiersza polecenia platformy Azure. Ta operacja wymaga uprawnień `storage` `setsas` i .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po pomyślnym zakończeniu operacji skopiuj dane wyjściowe.

```console
"se=2020-01-01&sp=***"
```

Te dane wyjściowe będą przekazywane do `--template-uri` parametru w następnym kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji sygnatury dostępu współdzielonych

Użyj polecenia [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_create) interfejsu wiersza polecenia platformy Azure, przekazując dane wyjściowe z poprzedniego kroku do parametru , aby utworzyć definicję sygnatury dostępu `--template-uri` współdzielonego.  Możesz podać nazwę dla wybranego `-n` parametru.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji sygnatury dostępu współdzielonych

Możesz sprawdzić, czy definicja sygnatury dostępu współdzielonego została zapisana w magazynie kluczy, używając polecenia [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) interfejsu wiersza polecenia platformy Azure.

Teraz możesz użyć polecenia [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) i właściwości , aby wyświetlić `id` zawartość tego klucza tajnego.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o kluczach, wpisach tajnych i certyfikatach.](/rest/api/keyvault/)
- Przejrzyj artykuły na [blogu Azure Key Vault zespołu.](/archive/blogs/kv/)
- Zobacz [dokumentację z dokumentacją dotyczącą magazynu az keyvault.](/cli/azure/keyvault/storage)

---
title: Zarządzanie kluczami kont magazynu za pomocą usługi Azure Key Vault i interfejsu wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację między usługą Azure Key Vault i dostęp oparty na kluczach do konta magazynu platformy Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685432"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami kont magazynu za pomocą usługi Key Vault i interfejsu wiersza polecenia platformy Azure

Konto magazynu platformy Azure używa poświadczeń zawierających nazwę konta i klucz. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Usługa Key Vault zarządza kluczami kont magazynu, przechowując je jako wpisy tajne usługi Key Vault. 

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

- [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- [Tworzenie magazynu kluczy](quick-create-cli.md)
- [Utwórz konto magazynu platformy Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). Nazwa konta magazynu musi używać tylko małych liter i cyfr. Długość nazwy musi wynosić od 3 do 24 znaków.
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami kont magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [logowania az.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Udzielanie dostępu do konta magazynu usługi Key Vault

Użyj polecenia [tworzenia przypisania roli](/cli/azure/role/assignment?view=azure-cli-latest) interfejsu wiersza polecenia Azure CLI az, aby przyznać magazynowi Klucz dostępu do konta magazynu. Podaj polecenie następującymi wartościami parametrów:

- `--role`: Przekazać rolę RBAC roli "Rola usługi operatora klucza magazynu". Ta rola ogranicza zakres dostępu do konta magazynu. W przypadku klasycznego konta magazynu przekaż "Rolę usługi operatora klucza konta magazynu klasycznego".
- `--assignee`: Przekaż wartośćhttps://vault.azure.net" ", który jest adresem URL dla usługi Key Vault w chmurze publicznej platformy Azure. (W przypadku chmury usługi Azure Goverment użyj '--asingee-object-id' zamiast tego, zobacz [Identyfikator aplikacji głównej usługi](#service-principal-application-id).)
- `--scope`: Przekaż identyfikator zasobu konta magazynu, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`który jest w formularzu . Aby znaleźć identyfikator subskrypcji, użyj polecenia [listy kont az](/cli/azure/account?view=azure-cli-latest#az-account-list) interfejsu wiersza polecenia platformy Azure; aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [listy kont magazynu az](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) platformy Azure.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Nadaj uprawnienia konta użytkownika zarządzanym kontom magazynu

Użyj polecenia cmdlet zasad zestawu [kluczy az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) usługi Azure, aby zaktualizować zasady dostępu usługi Key Vault i udzielić uprawnień konta magazynu do konta użytkownika.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Należy zauważyć, że uprawnienia dla kont magazynu nie są dostępne na stronie "Zasady dostępu" konta magazynu w witrynie Azure portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie konta magazynu zarządzanego w przechowalni kluczy

 Utwórz zarządzane konto magazynu usługi Key Vault przy użyciu polecenia magazynu [az keyvault](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) usługi Azure. Ustaw okres regeneracji 90 dni. Po 90 dniach Magazyn `key1` kluczy regeneruje `key2` i `key1`zamienia aktywny klucz z do . `key1`jest następnie oznaczony jako aktywny klawisz. Podaj polecenie następującymi wartościami parametrów:

- `--vault-name`: Przekaż nazwę magazynu kluczy. Aby znaleźć nazwę magazynu kluczy, użyj polecenia [listy kluczy az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) usługi Azure.
- `-n`: Przekaż nazwę swojego konta magazynu. Aby znaleźć nazwę konta magazynu, użyj polecenia [listy kont magazynu az](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) platformy Azure.
- `--resource-id`: Przekaż identyfikator zasobu konta magazynu, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`który jest w formularzu . Aby znaleźć identyfikator subskrypcji, użyj polecenia [listy kont az](/cli/azure/account?view=azure-cli-latest#az-account-list) interfejsu wiersza polecenia platformy Azure; aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [listy kont magazynu az](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) platformy Azure.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny podpisu dostępu współdzielonego

Można również poprosić Magazyn kluczy o wygenerowanie tokenów podpisu dostępu współdzielonego. Podpis dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz przyznać klientom dostęp do zasobów na koncie magazynu bez udostępniania kluczy konta. Podpis dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji uzupełniają następujące czynności:

- Ustawianie definicji `<YourSASDefinitionName>`podpisu dostępu współdzielonego konta . Definicja jest ustawiana na zarządzanym `<YourStorageAccountName>` koncie `<YourKeyVaultName>`magazynu usługi Key Vault w magazynie kluczy .
- Utwórz token podpisu udostępnionego dostępu do konta dla usług obiektów Blob, File, Table i Queue. Token jest tworzony dla typów zasobów Usługi, kontenera i obiektu. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem https i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw definicję sygnatury dostępu współdzielonego magazynu zarządzanego magazynu kluczy w przechowalni. Definicja ma identyfikator URI szablonu tokenu podpisu dostępu udostępnionego, który został utworzony. Definicja ma typ `account` podpisu dostępu współdzielonego i jest prawidłowa dla N dni.
- Sprawdź, czy podpis dostępu współdzielonego został zapisany w magazynie kluczy jako klucz tajny.

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu podpisu dostępu udostępnionego

Utwórz definicję sygnatury dostępu współdzielonego przy użyciu polecenia generowania konta magazynu usługi Azure [az storage.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Ta operacja `storage` wymaga `setsas` i uprawnienia.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po pomyślnym uruchomieniu operacji skopiuj dane wyjściowe.

```console
"se=2020-01-01&sp=***"
```

To dane wyjściowe będą `--template-id` przekazywane do parametru w następnym kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji podpisu dostępu współdzielonego

Użyj polecenia [azure cli az keyvault storage sas-definition create,](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) przekazując `--template-id` dane wyjściowe z poprzedniego kroku do parametru, aby utworzyć definicję podpisu dostępu współdzielonego.  Można podać nazwę wybranego parametru. `-n`

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji podpisu dostępu współdzielonego

Można sprawdzić, czy definicja podpisu dostępu udostępnionego została przechowywana w magazynie kluczy przy użyciu [listy tajnej klucza azure az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) i poleceń [tajnego pokazu az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Najpierw znajdź definicję podpisu dostępu współdzielonego w magazynie kluczy za pomocą polecenia [az keyvault secret list.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Klucz tajny odpowiadający definicji sygnatury dostępu Współdzielonego będzie miał następujące właściwości:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Teraz można użyć [az keyvault tajne](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) `id` show polecenia i właściwości, aby wyświetlić zawartość tego klucza tajnego.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Dane wyjściowe tego polecenia będą wyświetlane`value`jako ciąg definicji sygnatury dostępu Współdzielonego jako .


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/).
- Przejrzyj artykuły w [blogu zespołu usługi Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Zapoznaj się z dokumentacją referencyjną [magazynu az keyvault.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

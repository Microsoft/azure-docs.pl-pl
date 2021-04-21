---
title: Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika dla kontenera lub obiektu blob przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika Azure Active Directory poświadczeniami przy użyciu interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788551"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika dla kontenera lub obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) w celu utworzenia sygnatury dostępu współdzielonego delegowania użytkownika dla kontenera lub obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Zainstalowanie najnowszej wersji interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do zabezpieczenia sygnatury dostępu współdzielonego przy użyciu poświadczeń usługi Azure AD, najpierw upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure, upewnij się, że zainstalowano wersję 2.0.78 lub nowszą. Aby sprawdzić zainstalowaną wersję, użyj `az --version` polecenia .

## <a name="sign-in-with-azure-ad-credentials"></a>Logowanie się przy użyciu poświadczeń usługi Azure AD

Zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Przypisywanie uprawnień przy użyciu kontroli RBAC platformy Azure

Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika z usługi Azure PowerShell, konto usługi Azure AD używane do logowania się do interfejsu wiersza polecenia platformy Azure musi mieć przypisaną rolę, która obejmuje akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** To uprawnienie umożliwia temu kontu usługi Azure AD zażądanie *klucza delegowania użytkownika.* Klucz delegowania użytkownika jest używany do podpisywania sygnatury dostępu współdzielonego delegowania użytkownika. Rola udostępniaca akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** musi być przypisana na poziomie konta magazynu, grupy zasobów lub subskrypcji.

Jeśli nie masz wystarczających uprawnień do przypisywania ról platformy Azure do podmiotu zabezpieczeń usługi Azure AD, może być konieczne poproszę właściciela lub administratora konta o przypisanie niezbędnych uprawnień.

W poniższym przykładzie przypisano rolę Współautor danych obiektu blob usługi **Storage,** która obejmuje akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Zakres roli jest ograniczony do poziomu konta magazynu.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Aby uzyskać więcej informacji o rolach wbudowanych, które obejmują akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** zobacz Wbudowane role [platformy Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Zabezpieczanie sygnatury dostępu współdzielonego przy użyciu poświadczeń usługi Azure AD

Podczas tworzenia sygnatury dostępu współdzielonego delegowania użytkownika za pomocą interfejsu wiersza polecenia platformy Azure jest tworzony niejawnie klucz delegowania użytkownika używany do podpisania sygnatury dostępu współdzielonego. Czas rozpoczęcia i czas wygaśnięcia określony dla sygnatury dostępu współdzielonego są również używane jako czas rozpoczęcia i czas wygaśnięcia klucza delegowania użytkownika.

Ponieważ maksymalny interwał, w którym klucz delegowania użytkownika jest prawidłowy, wynosi 7 dni od daty rozpoczęcia, należy określić czas wygaśnięcia sygnatury dostępu współdzielonego w ciągu 7 dni od daty rozpoczęcia. Sygnatura dostępu współdzielonego jest nieprawidłowa po wygaśnięciu klucza delegowania użytkownika, więc sygnatura dostępu współdzielonego z czasem wygaśnięcia większym niż 7 dni będzie nadal ważna tylko przez 7 dni.

Podczas tworzenia sygnatury dostępu współdzielonego delegowania użytkownika `--auth-mode login` wymagane `--as-user parameters` są identyfikatory i . Określ *nazwę* logowania `--auth-mode` dla parametru , aby żądania do usługi Azure Storage zostały autoryzowane przy użyciu poświadczeń usługi Azure AD. Określ parametr `--as-user` , aby wskazać, że zwrócona sygnatura dostępu współdzielonego powinna być sygnaturą dostępu współdzielonego delegowania użytkownika.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika dla kontenera

Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika dla kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage container generate-sas.](/cli/azure/storage/container#az_storage_container_generate_sas)

Obsługiwane uprawnienia sygnatury dostępu współdzielonego delegowania użytkownika w kontenerze obejmują dodawanie, tworzenie, usuwanie, tworzenie, tworzenie, tworzenie, tworzenie, odczytywanie i zapis. Uprawnienia można określać w sposób tylko dosyć często lub łącznie. Aby uzyskać więcej informacji na temat tych uprawnień, zobacz [Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika.](/rest/api/storageservices/create-user-delegation-sas)

Poniższy przykład zwraca token SAS delegowania użytkownika dla kontenera. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Zwrócony token SAS delegowania użytkownika będzie podobny do:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika dla obiektu blob

Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika dla obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage blob generate-sas.](/cli/azure/storage/blob#az_storage_blob_generate_sas)

Obsługiwane uprawnienia sygnatury dostępu współdzielonego delegowania użytkownika w obiekcie blob obejmują dodawanie, tworzenie, usuwanie, odczyt i zapis. Uprawnienia można określać w sposóbingly lub połączyć. Aby uzyskać więcej informacji na temat tych uprawnień, zobacz [Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika.](/rest/api/storageservices/create-user-delegation-sas)

Następująca składnia zwraca sygnaturę dostępu współdzielonego delegowania użytkownika dla obiektu blob. W przykładzie określono `--full-uri` parametr , który zwraca wartość URI obiektu blob z dołączonym tokenem SAS. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

Zwrócony identyfikator URI sygnatury dostępu współdzielonego delegowania użytkownika będzie podobny do:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Sygnatura dostępu współdzielonego delegowania użytkownika nie obsługuje definiowania uprawnień z zapisanymi zasadami dostępu.

## <a name="revoke-a-user-delegation-sas"></a>Odwoływanie sygnatury dostępu współdzielonego delegowania użytkownika

Aby odwołać sygnaturę dostępu współdzielonego delegowania użytkownika z interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account revoke-delegation-keys.](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) To polecenie odwołuje wszystkie klucze delegowania użytkownika skojarzone z określonym kontem magazynu. Wszelkie sygnatury dostępu współdzielone skojarzone z tymi kluczami są unieważniane.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zarówno klucz delegowania użytkownika, jak i przypisania ról platformy Azure są buforowane przez usługę Azure Storage, więc może wystąpić opóźnienie między zainicjowaniem procesu odwoływania a nieprawidłowym stanie się istniejąca sygnatura dostępu współdzielonego delegowania użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika (interfejs API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operacja pobierz klucz delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)

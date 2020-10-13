---
title: Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego z poświadczeniami Azure Active Directory przy użyciu programu PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714519"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu programu PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu współdzielonego delegowania użytkowników dla kontenera lub obiektu BLOB z Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

W celu utworzenia sygnatury dostępu współdzielonego użytkownika w programie PowerShell należy zainstalować w wersji 1.10.0 lub nowszej modułu AZ. Storage. Wykonaj następujące kroki, aby zainstalować najnowszą wersję modułu:

1. Odinstaluj wszystkie poprzednie instalacje Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje Azure PowerShell z systemu Windows za pomocą ustawienia **aplikacje & funkcje** w obszarze **Ustawienia**.
    - Usuń wszystkie moduły **platformy Azure** z programu `%Program Files%\WindowsPowerShell\Modules` .

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu PowerShellGet.

1. Zainstaluj najnowszą wersję Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Upewnij się, że zainstalowano Azure PowerShell w wersji 3.2.0 lub nowszej. Uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu programu PowerShell usługi Azure Storage:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell.

Aby sprawdzić, która wersja modułu AZ. Storage jest zainstalowana, uruchom następujące polecenie:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Aby uzyskać więcej informacji o instalowaniu Azure PowerShell, zobacz [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Logowanie się do Azure PowerShell za pomocą usługi Azure AD

Wywołaj polecenie [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się przy użyciu konta usługi Azure AD:

```powershell
Connect-AzAccount
```

Aby uzyskać więcej informacji na temat logowania przy użyciu programu PowerShell, zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Przypisywanie uprawnień za pomocą usługi Azure RBAC

Aby można było utworzyć sygnaturę dostępu współdzielonego delegowania użytkownika z Azure PowerShell, konto usługi Azure AD używane do logowania się do programu PowerShell musi mieć przypisaną rolę obejmującą akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . To uprawnienie umożliwia kontu usługi Azure AD zażądanie *klucza delegowania użytkownika*. Klucz delegowania użytkownika służy do podpisywania sygnatury dostępu współdzielonego delegowania użytkownika. Rola dostarczająca akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** musi być przypisana na poziomie konta magazynu, grupy zasobów lub subskrypcji. Aby uzyskać więcej informacji o uprawnieniach usługi Azure RBAC do tworzenia sygnatury dostępu współdzielonego delegowania użytkowników, zobacz sekcję **przypisywanie uprawnień za pomocą usługi Azure RBAC** w temacie [Tworzenie skojarzenia zabezpieczeń delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

Jeśli nie masz wystarczających uprawnień do przypisywania ról platformy Azure do podmiotu zabezpieczeń usługi Azure AD, może być konieczne poproszenie właściciela konta lub administratora, aby przypisał odpowiednie uprawnienia.

Poniższy przykład przypisuje rolę **współautor danych obiektów blob magazynu** , która obejmuje akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Rola jest objęta zakresem na poziomie konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Aby uzyskać więcej informacji na temat wbudowanych ról zawierających akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Zabezpieczanie sygnatury dostępu współdzielonego przy użyciu poświadczeń usługi Azure AD

Podczas tworzenia sygnatury dostępu współdzielonego użytkownika z Azure PowerShell, klucz delegowania użytkownika używany do podpisywania sygnatury dostępu współdzielonego jest tworzony niejawnie. Czas rozpoczęcia i czas wygaśnięcia określony dla sygnatury dostępu współdzielonego są również używane jako czas rozpoczęcia i czas wygaśnięcia klucza delegowania użytkownika. 

Ze względu na to, że maksymalny interwał ważności klucza delegowania użytkownika wynosi 7 dni od daty rozpoczęcia, należy określić czas wygaśnięcia dla sygnatury dostępu współdzielonego w ciągu 7 dni od czasu rozpoczęcia. Sygnatura dostępu współdzielonego jest nieprawidłowa po wygaśnięciu klucza delegowania użytkownika, więc sygnatura dostępu współdzielonego o godzinie przekraczającej 7 dni będzie nadal ważna tylko przez 7 dni.

Aby utworzyć sygnaturę dostępu współdzielonego dla kontenera lub obiektu BLOB z Azure PowerShell, należy najpierw utworzyć nowy obiekt kontekstu usługi Azure Storage, określając `-UseConnectedAccount` parametr. `-UseConnectedAccount`Parametr określa, że polecenie tworzy obiekt kontekstu w ramach konta usługi Azure AD, za pomocą którego zalogowano się.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera

Aby zwrócić token użytkownika delegowania sygnatury dostępu współdzielonego dla kontenera, wywołaj polecenie [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) , przechodząc do utworzonego wcześniej obiektu kontekstu usługi Azure Storage.

Poniższy przykład zwraca token użytkownika delegowania sygnatury dostępu współdzielonego dla kontenera. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Zwrócony token sygnatury dostępu współdzielonego użytkownika będzie podobny do:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla obiektu BLOB

Aby zwrócić token użytkownika delegowania sygnatury dostępu współdzielonego dla obiektu BLOB, wywołaj polecenie [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) , przechodząc do utworzonego wcześniej obiektu kontekstu usługi Azure Storage.

Poniższa składnia zwraca sygnaturę dostępu współdzielonego delegowania użytkownika dla obiektu BLOB. W przykładzie określono `-FullUri` parametr, który zwraca identyfikator URI obiektu BLOB z dołączonym tokenem SAS. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Zwrócony identyfikator URI sygnatury dostępu współdzielonego użytkownika będzie wyglądać podobnie do:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Sygnatura dostępu współdzielonego użytkownika nie obsługuje definiowania uprawnień z przechowywanymi zasadami.

## <a name="revoke-a-user-delegation-sas"></a>Odwoływanie sygnatury dostępu współdzielonego użytkownika

Aby odwołać sygnaturę dostępu współdzielonego delegowania użytkownika z Azure PowerShell, wywołaj polecenie **odwołaj-AzStorageAccountUserDelegationKeys** . To polecenie odwołuje wszystkie klucze delegowania użytkowników skojarzone z określonym kontem magazynu. Wszystkie sygnatury dostępu współdzielonego skojarzone z tymi kluczami są unieważnione.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Zarówno klucz delegowania użytkownika, jak i przydziały ról platformy Azure są buforowane przez usługę Azure Storage, dzięki czemu może wystąpić opóźnienie między zainicjowaniem procesu odwoływania a istniejącym skojarzeniem SAS delegowania użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)

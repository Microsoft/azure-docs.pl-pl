---
title: Korzystanie z platformy .NET do tworzenia sygnatury dostępu współdzielonego użytkownika dla kontenera, katalogu lub obiektu BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego delegowania użytkowników z poświadczeniami Azure Active Directory przy użyciu biblioteki klienckiej platformy .NET dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13491735f73cb1696f3c36f3434cc781a1e2b739
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526809"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera, katalogu lub obiektu BLOB z platformą .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać poświadczeń usługi Azure Active Directory (Azure AD) do tworzenia sygnatury dostępu współdzielonego użytkownika dla kontenera, katalogu lub obiektu BLOB za pomocą biblioteki klienta usługi Azure Storage dla programu .NET w wersji 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Przypisywanie ról platformy Azure na potrzeby dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektów blob, musi mieć uprawnienia do tego zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy konto użytkownika usługi Azure AD z uruchomionym kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę platformy Azure, która umożliwia dostęp do danych obiektów BLOB w usłudze Azure Storage. Informacje o przypisywaniu uprawnień za pośrednictwem usługi Azure RBAC zawiera sekcja zatytułowana **Przypisywanie ról platformy Azure na potrzeby dostępu** w artykule [Autoryzuj dostęp do obiektów blob i kolejek platformy azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Aby dowiedzieć się więcej o sposobie uwierzytelniania przy użyciu biblioteki klienta tożsamości platformy Azure z usługi Azure Storage, zapoznaj się z sekcją **uwierzytelnianie przy użyciu biblioteki tożsamości platformy Azure** w artykule [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Pobieranie poświadczeń uwierzytelnionego tokenu

Aby uzyskać poświadczenia tokenu, których kod może użyć do autoryzacji żądań do usługi Azure Storage, Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Poniższy fragment kodu przedstawia sposób pobierania poświadczeń tokenu uwierzytelnionego i używania go do tworzenia klienta usługi dla magazynu obiektów blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Pobierz klucz delegowania użytkownika

Każde sygnatura dostępu współdzielonego jest podpisana przy użyciu klucza. Aby utworzyć sygnaturę dostępu współdzielonego delegowania użytkowników, musisz najpierw zażądać klucza delegowania użytkownika, który jest następnie używany do podpisywania sygnatury dostępu współdzielonego. Klucz delegowania użytkownika jest analogiczny do klucza konta używanego do podpisywania sygnatury dostępu współdzielonego usługi lub konta SAS, z tą różnicą, że używa poświadczeń usługi Azure AD. Gdy klient zażąda klucza delegowania użytkownika przy użyciu tokenu OAuth 2,0, usługa Azure Storage zwraca klucz delegowania użytkownika w imieniu użytkownika.

Po uzyskaniu klucza delegowania użytkownika można użyć tego klucza do utworzenia dowolnej liczby sygnatur dostępu współdzielonego delegowania użytkowników w okresie istnienia klucza. Klucz delegowania użytkownika jest niezależny od tokenu OAuth 2,0 użytego do jego uzyskania, więc token nie musi być odnawiany, dopóki klucz jest nadal ważny. Możesz określić, że klucz jest ważny przez okres do 7 dni.

Użyj jednej z następujących metod, aby zażądać klucza delegowania użytkownika:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Poniższy fragment kodu Pobiera klucz delegowania użytkownika i zapisuje jego właściwości:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Uzyskiwanie sygnatury dostępu współdzielonego użytkownika dla obiektu BLOB

Poniższy przykład kodu pokazuje kompletny kod uwierzytelniania podmiotu zabezpieczeń i tworzenia sygnatury dostępu współdzielonego delegowania użytkowników dla obiektu BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

Poniższy przykład sprawdza, czy w poprzednim przykładzie utworzono sygnaturę dostępu współdzielonego delegowania użytkownika z symulowanej aplikacji klienckiej. Jeśli sygnatura dostępu współdzielonego jest prawidłowa, aplikacja kliencka może odczytać zawartość obiektu BLOB. Jeśli sygnatura dostępu współdzielonego jest nieprawidłowa, na przykład jeśli wygasła, usługa Azure Storage zwraca kod błędu 403 (dostęp zabroniony).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Uzyskaj sygnaturę dostępu współdzielonego użytkownika dla kontenera

Poniższy przykład kodu pokazuje, jak wygenerować sygnaturę dostępu współdzielonego delegowania użytkowników dla kontenera:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

Poniższy przykład sprawdza, czy w poprzednim przykładzie utworzono sygnaturę dostępu współdzielonego delegowania użytkownika z symulowanej aplikacji klienckiej. Jeśli sygnatura dostępu współdzielonego jest prawidłowa, aplikacja kliencka może odczytać zawartość obiektu BLOB. Jeśli sygnatura dostępu współdzielonego jest nieprawidłowa, na przykład jeśli wygasła, usługa Azure Storage zwraca kod błędu 403 (dostęp zabroniony).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Uzyskaj sygnaturę dostępu współdzielonego użytkownika dla katalogu

Poniższy przykład kodu pokazuje, jak generować sygnaturę dostępu współdzielonego delegowania użytkowników dla katalogu, gdy dla konta magazynu jest włączona hierarchiczna przestrzeń nazw:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

Poniższy przykład sprawdza, czy w poprzednim przykładzie utworzono sygnaturę dostępu współdzielonego delegowania użytkownika z symulowanej aplikacji klienckiej. Jeśli sygnatura dostępu współdzielonego jest prawidłowa, aplikacja kliencka może wyświetlić listę ścieżek plików dla tego katalogu. Jeśli sygnatura dostępu współdzielonego jest nieprawidłowa, na przykład jeśli wygasła, usługa Azure Storage zwraca kod błędu 403 (dostęp zabroniony).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md)
- [Pobierz operację klucza delegowania użytkownika](/rest/api/storageservices/get-user-delegation-key)
- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)

---
title: Autoryzowanie dostępu do danych przy użyciu tożsamości zarządzanej
titleSuffix: Azure Storage
description: Użyj tożsamości zarządzanych dla zasobów platformy Azure, aby autoryzować dostęp do danych obiektów blob i kolejek z aplikacji działających na platformie Azure, w aplikacjach funkcji i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2aa6730759a9aa1aaab3156c55bf19e82641b8ea
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739336"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autoryzowanie dostępu do danych obiektów blob i kolejek przy użyciu tożsamości zarządzanych dla zasobów platformy Azure

Usługi Azure Blob Storage i Queue Storage obsługują Azure Active Directory (Azure AD) przy użyciu [tożsamości zarządzanych dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Tożsamości zarządzane dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji działających na maszynach wirtualnych platformy Azure, aplikacjach funkcji, zestawach skalowania maszyn wirtualnych i innych usługach. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem usługi Azure AD, można uniknąć przechowywania poświadczeń w aplikacjach uruchamianych w chmurze.  

W tym artykule pokazano, jak autoryzować dostęp do danych obiektów blob lub kolejek z maszyny wirtualnej platformy Azure przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Opisano w nim również sposób testowania kodu w środowisku dewelopera.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej

Przed użyciem tożsamości zarządzanych dla zasobów platformy Azure do autoryzowania dostępu do obiektów blob i kolejek z maszyny wirtualnej należy najpierw włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości zarządzane dla zasobów platformy Azure, zobacz jeden z tych artykułów:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager bibliotek klienckich](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Uwierzytelnianie za pomocą biblioteki tożsamości platformy Azure

Biblioteka klienta tożsamości platformy Azure zapewnia obsługę uwierzytelniania tokenu usługi Azure AD dla [zestawu Azure SDK.](https://github.com/Azure/azure-sdk) Najnowsze wersje bibliotek klienta usługi Azure Storage dla platformy .NET, Java, Python i JavaScript integrują się z biblioteką tożsamości platformy Azure, aby zapewnić prosty i bezpieczny sposób uzyskania tokenu OAuth 2.0 na celu autoryzację żądań usługi Azure Storage.

Zaletą biblioteki klienta tożsamości platformy Azure jest to, że umożliwia ona użycie tego samego kodu do uwierzytelniania bez względu na to, czy aplikacja działa w środowisku projektowym, czy na platformie Azure. Biblioteka klienta tożsamości platformy Azure dla platformy .NET uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest tożsamością zarządzaną dla zasobów platformy Azure. W środowisku projektowym tożsamość zarządzana nie istnieje, więc biblioteka klienta uwierzytelnia użytkownika lub jednostkę usługi do celów testowych.

Po uwierzytelnieniu biblioteka klienta tożsamości platformy Azure pobiera poświadczenia tokenu. To poświadczenie tokenu jest następnie hermetyzowane w obiekcie klienta usługi, który tworzysz w celu wykonywania operacji na usłudze Azure Storage. Biblioteka bezproblemowo sobie z tym radzi, uzyskanie odpowiednich poświadczeń tokenu.

Aby uzyskać więcej informacji na temat biblioteki klienta tożsamości platformy Azure dla platformy .NET, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) Aby uzyskać dokumentację referencyjną dotyczącą biblioteki klienta tożsamości platformy Azure, zobacz [Przestrzeń nazw Azure.Identity](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Przypisywanie ról platformy Azure w celu uzyskania dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektu blob lub kolejki, musi mieć uprawnienia do zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy kontem użytkownika usługi Azure AD, na którym działa kod w środowisku dewelopera, musi mieć przypisaną rolę platformy Azure, która udziela dostępu do danych obiektów blob lub kolejek w usłudze Azure Storage. Aby uzyskać informacje na temat przypisywania uprawnień za pośrednictwem funkcji RBAC platformy Azure, zobacz sekcję Przypisywanie ról platformy **Azure** w celu uzyskania praw dostępu w sekcji [Autoryzowanie](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory .

> [!NOTE]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Musisz jawnie przypisać sobie rolę platformy Azure dla usługi Azure Storage. Możesz przypisać go na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera bądź kolejki.
>
> Przed przypisaniem sobie roli dostępu do danych będziesz mieć dostęp do danych na koncie magazynu za pośrednictwem usługi Azure Portal, ponieważ Azure Portal może również używać klucza konta do uzyskiwania dostępu do danych. Aby uzyskać więcej informacji, [zobacz Choose how to authorize access to blob data in the Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Uwierzytelnianie użytkownika w środowisku dewelopera

Gdy kod jest uruchomiony w środowisku dewelopera, uwierzytelnianie może być obsługiwane automatycznie lub może wymagać logowania za pomocą przeglądarki, w zależności od tego, których narzędzi używasz. Na przykład usługa Microsoft Visual Studio logowanie jednokrotne, dzięki czemu aktywne konto użytkownika usługi Azure AD jest automatycznie używane do uwierzytelniania. Aby uzyskać więcej informacji na temat logowania jednokrotnego, zobacz [Logowanie jednokrotne do aplikacji.](../../active-directory/manage-apps/what-is-single-sign-on.md)

Inne narzędzia programskie mogą monitować o zalogowanie się za pośrednictwem przeglądarki internetowej.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Uwierzytelnianie jednostki usługi w środowisku dewelopera

Jeśli środowisko projektowe nie obsługuje logowania ani logowania się za pośrednictwem przeglądarki internetowej, możesz użyć jednostki usługi do uwierzytelniania w środowisku dewelopera.

#### <a name="create-the-service-principal"></a>Tworzenie jednostki usługi

Aby utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure i przypisać rolę platformy Azure, wywołaj [polecenie az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Podaj rolę dostępu do danych usługi Azure Storage do przypisania do nowej jednostki usługi. Ponadto podaj zakres przypisania roli. Aby uzyskać więcej informacji na temat wbudowanych ról usługi Azure Storage, zobacz Role wbudowane [platformy Azure.](../../role-based-access-control/built-in-roles.md)

Jeśli nie masz wystarczających uprawnień do przypisania roli do jednostki usługi, może być konieczne poproszę właściciela lub administratora konta o wykonanie przypisania roli.

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure, aby utworzyć nową jednostkę usługi i przypisać do niego rolę Czytelnik danych obiektu blob usługi **Storage** z zakresem konta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Polecenie `az ad sp create-for-rbac` zwraca listę właściwości jednostki usługi w formacie JSON. Skopiuj te wartości, aby można było ich użyć do utworzenia niezbędnych zmiennych środowiskowych w następnym kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Propagacja przypisań ról platformy Azure może potrwać kilka minut.

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Biblioteka klienta tożsamości platformy Azure odczytuje wartości z trzech zmiennych środowiskowych w czasie wykonywania w celu uwierzytelnienia jednostki usługi. W poniższej tabeli opisano wartość do ustawienia dla każdej zmiennej środowiskowej.

|Zmienna środowiskowa|Wartość
|-|-
|`AZURE_CLIENT_ID`|Identyfikator aplikacji dla jednostki usługi
|`AZURE_TENANT_ID`|Identyfikator dzierżawy usługi Azure AD jednostki usługi
|`AZURE_CLIENT_SECRET`|Hasło wygenerowane dla jednostki usługi

> [!IMPORTANT]
> Po skonfigurowaniu zmiennych środowiskowych zamknij i otwórz ponownie okno konsoli. Jeśli korzystasz z Visual Studio lub innego środowiska projektowego, może być konieczne ponowne uruchomienie środowiska projektowego w celu zarejestrowania nowych zmiennych środowiskowych.

Aby uzyskać więcej informacji, zobacz Create identity for Azure app in portal (Tworzenie [tożsamości dla aplikacji platformy Azure w portalu).](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu platformy .NET: Tworzenie blokowego obiektu blob

Dodaj następujące dyrektywy `using` do kodu, aby użyć bibliotek klienta usług Azure Identity i Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Aby uzyskać poświadczenia tokenu, których kod może użyć do autoryzowania żądań do usługi Azure Storage, utwórz wystąpienie klasy [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) Poniższy przykład kodu przedstawia sposób uzyskania uwierzytelnionego poświadczenia tokenu i użycia go do utworzenia obiektu klienta usługi, a następnie przekazania nowego obiektu blob za pomocą klienta usługi:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Aby autoryzować żądania względem danych obiektów blob lub kolejek w usłudze Azure AD, należy dla tych żądań użyć protokołu HTTPS.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie prawami dostępu do danych magazynu przy użyciu kontroli RBAC platformy Azure.](./storage-auth-aad-rbac-portal.md)
- [Używaj usługi Azure AD z aplikacjami magazynu.](storage-auth-aad-app.md)
- [Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob](../blobs/authorize-data-operations-powershell.md)
- [Samouczek: uzyskiwanie dostępu do magazynu z App Service przy użyciu tożsamości zarządzanych](../../app-service/scenario-secure-app-access-storage.md)

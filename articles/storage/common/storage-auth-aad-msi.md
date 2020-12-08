---
title: Autoryzuj dostęp do danych za pomocą tożsamości zarządzanej
titleSuffix: Azure Storage
description: Korzystaj z zarządzanych tożsamości dla zasobów platformy Azure, aby autoryzować dostęp do danych obiektów blob i kolejek z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacjach funkcji i innych.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ccc545b15f16879582c671b082cab40f6b11aa08
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778975"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure

Usługi Azure BLOB i queue storage obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawów skalowania maszyn wirtualnych i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.  

W tym artykule przedstawiono sposób autoryzacji dostępu do danych obiektu BLOB lub kolejki z maszyny wirtualnej platformy Azure przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Opisano w nim również sposób testowania kodu w środowisku deweloperskim.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej

Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania dostępu do obiektów blob i kolejek z poziomu maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Uwierzytelnianie przy użyciu biblioteki tożsamości platformy Azure

Biblioteka klienta tożsamości platformy Azure zapewnia obsługę uwierzytelniania tokenów usługi Azure AD dla [zestawu Azure SDK](https://github.com/Azure/azure-sdk). Najnowsze wersje bibliotek klienckich usługi Azure Storage dla platform .NET, Java, Python i JavaScript integrują się z biblioteką tożsamości platformy Azure, aby zapewnić prosty i bezpieczny sposób uzyskiwania tokenu OAuth 2,0 na potrzeby autoryzacji żądań usługi Azure Storage.

Zaletą biblioteki klienta tożsamości platformy Azure jest możliwość użycia tego samego kodu w celu uwierzytelnienia, czy aplikacja działa w środowisku programistycznym, czy na platformie Azure. Biblioteka klienta tożsamości platformy Azure dla platformy .NET uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest zarządzaną tożsamością dla zasobów platformy Azure. W środowisku programistycznym, zarządzana tożsamość nie istnieje, więc Biblioteka klienta uwierzytelnia użytkownika lub jednostkę usługi na potrzeby testowania.

Po uwierzytelnieniu Biblioteka klienta tożsamości platformy Azure Pobiera poświadczenia tokenu. To poświadczenie tokenu jest następnie hermetyzowane w obiekcie klienta usługi tworzonym w celu wykonywania operacji w usłudze Azure Storage. Biblioteka obsługuje ten sposób bezproblemowo, pobierając odpowiednie poświadczenia tokenu.

Aby uzyskać więcej informacji na temat biblioteki klienta tożsamości platformy Azure dla platformy .NET, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Aby uzyskać dokumentację referencyjną dla biblioteki klienta tożsamości platformy Azure, zobacz [przestrzeń nazw Azure. Identity](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Przypisywanie ról platformy Azure na potrzeby dostępu do danych

Gdy podmiot zabezpieczeń usługi Azure AD próbuje uzyskać dostęp do danych obiektu BLOB lub kolejki, musi mieć uprawnienia do tego zasobu. Niezależnie od tego, czy podmiot zabezpieczeń jest tożsamością zarządzaną na platformie Azure, czy konto użytkownika usługi Azure AD z uruchomionym kodem w środowisku deweloperskim, podmiot zabezpieczeń musi mieć przypisaną rolę platformy Azure, która umożliwia dostęp do danych obiektów blob lub kolejek w usłudze Azure Storage. Informacje o przypisywaniu uprawnień za pośrednictwem usługi Azure RBAC zawiera sekcja zatytułowana **Przypisywanie ról platformy Azure na potrzeby dostępu** w artykule [Autoryzuj dostęp do obiektów blob i kolejek platformy azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

> [!NOTE]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Musisz jawnie przypisać sobie rolę platformy Azure dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.
>
> Przed przypisaniem roli do dostępu do danych będzie można uzyskać dostęp do danych na koncie magazynu za pośrednictwem Azure Portal, ponieważ Azure Portal może także używać klucza konta do uzyskiwania dostępu do danych. Aby uzyskać więcej informacji, zobacz [Wybieranie metody autoryzacji dostępu do danych obiektów BLOB w Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Uwierzytelnianie użytkownika w środowisku programistycznym

Gdy kod jest uruchomiony w środowisku programistycznym, uwierzytelnianie może być obsługiwane automatycznie lub może wymagać logowania w przeglądarce, w zależności od tego, które narzędzia są używane. Na przykład Microsoft Visual Studio obsługuje logowanie jednokrotne (SSO), dzięki czemu aktywne konto użytkownika usługi Azure AD jest automatycznie używane do uwierzytelniania. Aby uzyskać więcej informacji na temat rejestracji jednokrotnej, zobacz Logowanie jednokrotne [do aplikacji](../../active-directory/manage-apps/what-is-single-sign-on.md).

Inne narzędzia programistyczne mogą monitować o zalogowanie się za pośrednictwem przeglądarki sieci Web.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Uwierzytelnianie jednostki usługi w środowisku deweloperskim

Jeśli środowisko programistyczne nie obsługuje logowania jednokrotnego lub logowania za pośrednictwem przeglądarki sieci Web, można użyć jednostki usługi do uwierzytelniania ze środowiska deweloperskiego.

#### <a name="create-the-service-principal"></a>Tworzenie jednostki usługi

Aby utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure i przypisać rolę platformy Azure, wywołaj polecenie [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Podaj rolę dostępu do danych usługi Azure Storage, która ma zostać przypisana do nowej nazwy głównej usługi. Ponadto Podaj zakres przypisania roli. Aby uzyskać więcej informacji na temat ról wbudowanych dla usługi Azure Storage, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli nie masz wystarczających uprawnień do przypisania roli do jednostki usługi, może być konieczne poproszenie właściciela konta lub administratora o wykonanie przypisania roli.

Poniższy przykład używa interfejsu wiersza polecenia platformy Azure, aby utworzyć nową nazwę główną usługi i przypisać do niej rolę **czytnika danych obiektów blob magazynu** z zakresem konta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac`Polecenie zwraca listę właściwości nazwy głównej usługi w formacie JSON. Skopiuj te wartości, aby można było użyć ich do utworzenia niezbędnych zmiennych środowiskowych w następnym kroku.

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
> Propagowanie przypisań ról platformy Azure może potrwać kilka minut.

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Biblioteka klienta tożsamości platformy Azure odczytuje wartości z trzech zmiennych środowiskowych w czasie wykonywania w celu uwierzytelnienia nazwy głównej usługi. W poniższej tabeli opisano wartość ustawioną dla każdej zmiennej środowiskowej.

|Zmienna środowiskowa|Wartość
|-|-
|`AZURE_CLIENT_ID`|Identyfikator aplikacji dla jednostki usługi
|`AZURE_TENANT_ID`|Identyfikator dzierżawy usługi Azure AD w jednostce nazwy
|`AZURE_CLIENT_SECRET`|Hasło wygenerowane dla jednostki usługi

> [!IMPORTANT]
> Po ustawieniu zmiennych środowiskowych Zamknij i ponownie otwórz okno konsoli. Jeśli używasz programu Visual Studio lub innego środowiska programistycznego, może być konieczne ponowne uruchomienie środowiska programistycznego w celu zarejestrowania nowych zmiennych środowiskowych.

Aby uzyskać więcej informacji, zobacz [Tworzenie tożsamości dla aplikacji platformy Azure w portalu](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu platformy .NET: Tworzenie blokowego obiektu BLOB

Dodaj następujące `using` dyrektywy do kodu, aby użyć tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Aby uzyskać poświadczenia tokenu, których kod może użyć do autoryzacji żądań do usługi Azure Storage, Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Poniższy przykład kodu pokazuje, jak uzyskać uwierzytelnione poświadczenia tokenu i użyć go do utworzenia obiektu klienta usługi, a następnie użyć klienta usługi do przekazania nowego obiektu BLOB:

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
> Aby autoryzować żądania względem obiektów blob lub danych z kolejki za pomocą usługi Azure AD, musisz użyć protokołu HTTPS dla tych żądań.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie prawami dostępu do danych magazynu za pomocą usługi Azure RBAC](./storage-auth-aad-rbac-portal.md).
- [Korzystanie z usługi Azure AD z aplikacjami magazynu](storage-auth-aad-app.md).
- [Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB](../blobs/authorize-data-operations-powershell.md)

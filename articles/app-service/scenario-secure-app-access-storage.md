---
title: Samouczek — dostęp do aplikacji sieci Web za pomocą tożsamości zarządzanych | Azure
description: W tym samouczku dowiesz się, jak uzyskać dostęp do usługi Azure Storage dla aplikacji za pomocą zarządzanych tożsamości.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435845"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage z poziomu aplikacji sieci Web

Dowiedz się, jak uzyskać dostęp do usługi Azure Storage dla aplikacji sieci Web (a nie zalogowanego użytkownika) działającego na Azure App Service przy użyciu tożsamości zarządzanych.

:::image type="content" alt-text="Diagram przedstawiający sposób uzyskiwania dostępu do magazynu." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Chcesz dodać dostęp do płaszczyzny danych platformy Azure (Azure Storage, Azure SQL Database, Azure Key Vault lub innych usług) z aplikacji sieci Web. Możesz użyć klucza współużytkowanego, ale musisz się martwić o bezpieczeństwo operacyjne użytkowników, którzy mogą tworzyć, wdrażać i zarządzać wpisami tajnymi. Możliwe jest również, że klucz można zaewidencjonować w serwisie GitHub, którego hakerzy wiedzą, jak przeprowadzić skanowanie. Bezpieczniejszym sposobem zapewnienia dostępu aplikacji sieci Web do danych jest użycie [tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/overview.md).

Zarządzana tożsamość z usługi Azure Active Directory (Azure AD) umożliwia App Service dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC), bez konieczności używania poświadczeń aplikacji. Po przypisaniu zarządzanej tożsamości do aplikacji sieci Web, platforma Azure bierze pod uwagę tworzenie i dystrybucję certyfikatu. Osoby nie muszą martwić się o zarządzanie wpisami tajnymi lub poświadczeniami aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie tożsamości zarządzanej przypisanej przez system w aplikacji sieci Web.
> * Utwórz konto magazynu i kontener Blob Storage platformy Azure.
> * Dostęp do magazynu z aplikacji sieci Web przy użyciu tożsamości zarządzanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja sieci Web działająca na Azure App Service z [włączonym modułem uwierzytelniania App Service/autoryzacji](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Włączanie tożsamości zarządzanej w aplikacji

Jeśli utworzysz i opublikujesz aplikację sieci Web za pomocą programu Visual Studio, zarządzana tożsamość została włączona w Twojej aplikacji. W usłudze App Service wybierz pozycję **Identity (tożsamość** ) w okienku po lewej stronie, a następnie wybierz pozycję **przypisane do systemu**. Sprawdź, czy **stan** jest ustawiony na wartość **włączone**. W przeciwnym razie wybierz pozycję **Zapisz** , a następnie wybierz pozycję **tak** , aby włączyć zarządzane przez system tożsamość zarządzaną. Gdy zarządzana tożsamość jest włączona, stan jest ustawiony na **włączone** , a identyfikator obiektu jest dostępny.

:::image type="content" alt-text="Zrzut ekranu pokazujący opcję tożsamości przypisanej do systemu." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Ten krok powoduje utworzenie nowego obiektu o IDENTYFIKATORze innym niż identyfikator aplikacji utworzony w okienku **uwierzytelnianie/autoryzacja** . Skopiuj identyfikator obiektu zarządzanej tożsamości przypisanej do systemu. Będzie on potrzebny później.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Tworzenie konta magazynu i kontenera Blob Storage

Teraz możesz przystąpić do tworzenia konta magazynu i kontenera Blob Storage.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

Konto magazynu ogólnego przeznaczenia, wersja 2 zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Kroki opisane tutaj tworzą konto magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne.

Obiekty blob w usłudze Azure Storage są zorganizowane w kontenery. Zanim będzie możliwe przekazanie obiektu BLOB w dalszej części tego samouczka, należy najpierw utworzyć kontener.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w Azure Portal, wykonaj następujące kroki.

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wprowadź **konto magazynu**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **konta magazynu**.

1. W wyświetlonym oknie **konta magazynu** wybierz pozycję **Dodaj**.

1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.

1. W polu **Grupa zasobów** wybierz grupę zasobów zawierającą aplikację sieci Web z menu rozwijanego.

1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.

1. Pozostaw poniższe pola ustawione na wartości domyślne:

    |Pole|Wartość|
    |--|--|
    |Model wdrażania|Resource Manager|
    |Wydajność|Standardowa|
    |Rodzaj konta|StorageV2 (ogólnego przeznaczenia wersja 2)|
    |Replikacja|Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)|
    |Warstwa dostępu|Gorąca|

1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.

1. Wybierz przycisk **Utwórz**.

Aby utworzyć kontener Blob Storage w usłudze Azure Storage, wykonaj następujące kroki.

1. Przejdź do nowego konta magazynu w Azure Portal.

1. W menu po lewej stronie konta magazynu przewiń do sekcji **BLOB Service** , a następnie wybierz pozycję **Containers (kontenery**).

1. Wybierz przycisk **+ Kontener**.

1. Wpisz nazwę nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

1. Ustaw poziom dostępu publicznego do kontenera. Domyślny poziom to **Prywatny (bez dostępu anonimowego)**.

1. Wybierz przycisk **OK**, aby utworzyć kontener.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 i kontener Blob Storage, uruchom następujący skrypt. Określ nazwę grupy zasobów zawierającej aplikację sieci Web. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

Określ lokalizację dla konta magazynu. Aby wyświetlić listę lokalizacji prawidłowych dla Twojej subskrypcji, uruchom polecenie ```Get-AzLocation | select Location``` . Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 i kontener Blob Storage, uruchom następujący skrypt. Określ nazwę grupy zasobów zawierającej aplikację sieci Web. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery. 

Określ lokalizację dla konta magazynu. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

W poniższym przykładzie używane jest konto usługi Azure AD do autoryzacji operacji tworzenia kontenera. Przed utworzeniem kontenera Przypisz do siebie rolę współautor danych obiektu blob magazynu. Nawet jeśli jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Udzielanie dostępu do konta magazynu

Musisz udzielić aplikacji sieci Web dostępu do konta magazynu, aby można było tworzyć, odczytywać i usuwać obiekty blob. W poprzednim kroku została skonfigurowana aplikacja internetowa działająca na App Service z tożsamością zarządzaną. Korzystając z funkcji RBAC systemu Azure, można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. Rola współautor danych obiektów blob magazynu udostępnia aplikację internetową (reprezentowaną przez zarządzaną przez system tożsamość skojarzoną) do odczytu, zapisu i usuwania dla kontenera obiektów blob i danych.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu, aby udzielić dostępu do aplikacji sieci Web. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz pozycję **przypisania ról**. Zostanie wyświetlona lista osób mających dostęp do konta magazynu. Teraz chcesz dodać przypisanie roli do robotów, usługi App Service, która wymaga dostępu do konta magazynu. Wybierz pozycję **Dodaj**  >  **Dodaj przypisanie roli**.

W obszarze **rola** wybierz opcję **współautor danych obiektów blob magazynu** , aby umożliwić aplikacji sieci Web dostęp do odczytu obiektów blob magazynu. W obszarze **Przypisz dostęp do** wybierz pozycję **App Service**. W obszarze **subskrypcja** wybierz swoją subskrypcję. Następnie wybierz usługę App Service, do której chcesz uzyskać dostęp. Wybierz pozycję **Zapisz**.

:::image type="content" alt-text="Zrzut ekranu przedstawiający ekran Dodawanie przypisania roli." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Aplikacja sieci Web ma teraz dostęp do konta magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Uruchom następujący skrypt, aby przypisać aplikację sieci Web (reprezentowana przez przypisaną przez system tożsamość zarządzaną) rolę współautor danych obiektów blob magazynu na koncie magazynu.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom następujący skrypt, aby przypisać aplikację sieci Web (reprezentowana przez przypisaną przez system tożsamość zarządzaną) rolę współautor danych obiektów blob magazynu na koncie magazynu.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Blob Storage dostępu (.NET)

Klasa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) jest używana do uzyskiwania poświadczeń tokenu dla kodu w celu autoryzowania żądań do usługi Azure Storage. Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , która używa tożsamości zarządzanej do pobierania tokenów i dołączania ich do klienta usługi. Poniższy przykład kodu Pobiera poświadczenia uwierzytelnionego tokenu i używa go do utworzenia obiektu klienta usługi, który przekazuje nowy obiekt BLOB.

Aby wyświetlić ten kod jako część przykładowej aplikacji, zobacz [przykład w witrynie GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Zainstaluj pakiety biblioteki klienta

Zainstaluj [pakiet nuget BLOB Storage](https://www.nuget.org/packages/Azure.Storage.Blobs/) , aby współpracował z BLOB Storage i [biblioteką klienta usługi Azure Identity dla platformy .NET na potrzeby](https://www.nuget.org/packages/Azure.Identity/) uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Zainstaluj biblioteki klienckie przy użyciu interfejsu wiersza polecenia platformy .NET Core lub konsoli Menedżera pakietów w programie Visual Studio.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Otwórz wiersz polecenia i przejdź do katalogu, który zawiera plik projektu.

Uruchom polecenia instalacji.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Menedżer pakietów](#tab/package-manager)

Otwórz projekt lub rozwiązanie w programie Visual Studio i Otwórz konsolę programu przy użyciu narzędzia Menedżer   >  **pakietów NuGet**  >  **konsola Menedżera pakietów** .

Uruchom polecenia instalacji.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Przykład

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
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
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli skończysz pracę z tym samouczkiem i nie potrzebujesz już aplikacji sieci Web ani skojarzonych zasobów, [Wyczyść utworzone zasoby](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz tożsamość zarządzaną przypisaną przez system.
> * Utwórz konto magazynu i kontener Blob Storage.
> * Dostęp do magazynu z aplikacji sieci Web przy użyciu tożsamości zarządzanych.

> [!div class="nextstepaction"]
> [App Service dostępu Microsoft Graph w imieniu użytkownika](scenario-secure-app-access-microsoft-graph-as-user.md)
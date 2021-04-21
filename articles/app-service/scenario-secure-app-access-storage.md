---
title: Samouczek — aplikacja internetowa uzyskuje dostęp do magazynu przy użyciu tożsamości zarządzanych | Azure
description: Z tego samouczka dowiesz się, jak uzyskać dostęp do usługi Azure Storage dla aplikacji przy użyciu tożsamości zarządzanych.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a37b189ae98332b2d6c557b6bdfad98266002e9e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833908"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage z aplikacji internetowej

Dowiedz się, jak uzyskać dostęp do usługi Azure Storage dla aplikacji internetowej (nie zalogował się użytkownik) działającej na platformie Azure App Service przy użyciu tożsamości zarządzanych.

:::image type="content" alt-text="Diagram przedstawiający sposób uzyskiwania dostępu do magazynu." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Chcesz dodać dostęp do płaszczyzny danych platformy Azure (Azure Storage, Azure SQL Database, Azure Key Vault lub innych usług) z aplikacji internetowej. Możesz użyć klucza wspólnego, ale wtedy musisz martwić się o bezpieczeństwo operacyjne osób, które mogą tworzyć i wdrażać klucz tajny oraz zarządzać nim. Możliwe jest również, że klucz można zaewidencjonować w usłudze GitHub. Hakerzy wiedzą, jak go skanować. Bezpieczniejszym sposobem na nadaj aplikacji internetowej dostęp do danych jest użycie [tożsamości zarządzanych.](../active-directory/managed-identities-azure-resources/overview.md)

Tożsamość zarządzana z usługi Azure Active Directory (Azure AD) umożliwia usłudze App Service dostęp do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC) bez konieczności poświadczeń aplikacji. Po przypisaniu tożsamości zarządzanej do aplikacji internetowej platforma Azure zajmuje się tworzeniem i dystrybucją certyfikatu. Ludzie nie muszą martwić się o zarządzanie wpisami tajnymi lub poświadczeniami aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz tożsamość zarządzaną przypisaną przez system w aplikacji internetowej.
> * Utwórz konto magazynu i kontener Azure Blob Storage magazynu.
> * Uzyskiwanie dostępu do magazynu z aplikacji internetowej przy użyciu tożsamości zarządzanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja internetowa uruchomiona na Azure App Service z włączonym [modułem App Service uwierzytelniania/autoryzacji.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-an-app"></a>Włączanie tożsamości zarządzanej w aplikacji

Jeśli utworzysz i opublikujesz aplikację internetową za pośrednictwem Visual Studio, tożsamość zarządzana została włączona w Twojej aplikacji. W usłudze App Service wybierz pozycję **Tożsamość w** okienku po lewej stronie, a następnie wybierz **pozycję Przypisana przez system**. Sprawdź, czy **dla ustawienia Stan** ustawiono wartość **Wł.** Jeśli nie, wybierz **pozycję Zapisz,** a następnie wybierz pozycję **Tak,** aby włączyć tożsamość zarządzaną przypisaną przez system. Gdy tożsamość zarządzana jest włączona, stan jest ustawiony na **Wł.,** a identyfikator obiektu jest dostępny.

:::image type="content" alt-text="Zrzut ekranu przedstawiający opcję Tożsamość przypisana przez system." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Ten krok powoduje utworzenie nowego identyfikatora obiektu innego niż identyfikator aplikacji utworzony w **okienku Uwierzytelnianie/autoryzacja.** Skopiuj identyfikator obiektu tożsamości zarządzanej przypisanej przez system. Będzie on potrzebny później.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Tworzenie konta magazynu i Blob Storage kontenera

Teraz możesz utworzyć konto magazynu i kontener Blob Storage magazynu.

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

Konto magazynu ogólnego przeznaczenia, wersja 2 zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Kroki opisane tutaj tworzą konto magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne.

Obiekty blob w usłudze Azure Storage są zorganizowane w kontenery. Aby można było przekazać obiekt blob w dalszej części tego samouczka, należy najpierw utworzyć kontener.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 w Azure Portal, wykonaj następujące kroki.

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wprowadź wartość **Konta magazynu.** Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **pozycję Konta magazynu.**

1. W **wyświetlonym oknie** Konta magazynu wybierz pozycję **Dodaj**.

1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.

1. W polu **Grupa zasobów** wybierz grupę zasobów zawierającą aplikację internetową z menu rozwijanego.

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

Aby utworzyć kontener Blob Storage usłudze Azure Storage, wykonaj następujące kroki.

1. Przejdź do nowego konta magazynu w Azure Portal.

1. W menu po lewej stronie dla konta magazynu przewiń do **sekcji Blob service,** a następnie wybierz pozycję **Kontenery.**

1. Wybierz przycisk **+ Kontener**.

1. Wpisz nazwę nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

1. Ustaw poziom dostępu publicznego do kontenera. Domyślny poziom to **Prywatny (bez dostępu anonimowego)**.

1. Wybierz przycisk **OK**, aby utworzyć kontener.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 Blob Storage kontenera, uruchom następujący skrypt. Określ nazwę grupy zasobów zawierającej aplikację internetową. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi również mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

Określ lokalizację konta magazynu. Aby wyświetlić listę lokalizacji prawidłowych dla Twojej subskrypcji, ```Get-AzLocation | select Location``` uruchom . Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach kątowych własnymi wartościami.

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

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 i kontener Blob Storage, uruchom następujący skrypt. Określ nazwę grupy zasobów zawierającej aplikację internetową. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Nazwa musi również mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery. 

Określ lokalizację konta magazynu. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).

W poniższym przykładzie użyto konta usługi Azure AD do autoryzowania operacji tworzenia kontenera. Przed utworzeniem kontenera przypisz do siebie rolę Współautor danych obiektu blob usługi Storage. Nawet jeśli jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach kątowych własnymi wartościami.

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

Aby można było tworzyć, odczytywać i usuwać obiekty blob, musisz udzielić aplikacji internetowej dostępu do konta magazynu. W poprzednim kroku skonfigurowano aplikację internetową uruchamianą na platformie App Service przy użyciu tożsamości zarządzanej. Przy użyciu kontroli RBAC platformy Azure możesz udzielić tożsamości zarządzanej dostępu do innego zasobu, podobnie jak do dowolnego podmiotu zabezpieczeń. Rola Współautor danych obiektu blob usługi Storage zapewnia aplikacji internetowej (reprezentowanej przez tożsamość zarządzaną przypisaną przez system) prawa do odczytu, zapisu i usuwania do kontenera obiektów blob i danych.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [witrynie Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu, aby udzielić dostępu aplikacji internetowej. Wybierz **pozycję Kontrola dostępu (IAM)** w okienku po lewej stronie, a następnie wybierz pozycję Przypisania **ról.** Zostanie wyświetlona lista osób, które mają dostęp do konta magazynu. Teraz chcesz dodać przypisanie roli do robota, czyli usługi app service, która wymaga dostępu do konta magazynu. Wybierz **pozycję Dodaj** dodaj  >  **przypisanie roli.**

W **roli** wybierz pozycję **Współautor danych obiektu blob magazynu,** aby zapewnić aplikacji internetowej dostęp do odczytu obiektów blob magazynu. Na **stronie Przypisywanie dostępu do** wybierz pozycję **App Service**. W **skrypcie** wybierz swoją subskrypcję. Następnie wybierz usługę App Service, do której chcesz udzielić dostępu. Wybierz pozycję **Zapisz**.

:::image type="content" alt-text="Zrzut ekranu przedstawiający ekran Dodawanie przypisania roli." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Aplikacja internetowa ma teraz dostęp do konta magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Uruchom następujący skrypt, aby przypisać aplikację internetową (reprezentowaną przez tożsamość zarządzaną przypisaną przez system) rolę Współautor danych obiektu blob usługi Storage na koncie magazynu.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom następujący skrypt, aby przypisać aplikację internetową (reprezentowaną przez tożsamość zarządzaną przypisaną przez system) rolę Współautor danych obiektu blob usługi Storage na koncie magazynu.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Dostęp Blob Storage (.NET)

Klasa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) służy do uzyskania poświadczeń tokenu dla kodu w celu autoryzowania żądań do usługi Azure Storage. Utwórz wystąpienie klasy [DefaultAzureCredential,](/dotnet/api/azure.identity.defaultazurecredential) które używa tożsamości zarządzanej do pobierania tokenów i dołączania ich do klienta usługi. Poniższy przykład kodu pobiera poświadczenia uwierzytelnionego tokenu i używa go do utworzenia obiektu klienta usługi, który przekaże nowy obiekt blob.

Aby zobaczyć ten kod jako część przykładowej aplikacji, zobacz przykład [w witrynie GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

Zainstaluj pakiet [NuGet Blob Storage do](https://www.nuget.org/packages/Azure.Storage.Blobs/) pracy z usługą Blob Storage i biblioteką klienta tożsamości platformy Azure dla pakietu NuGet platformy [.NET](https://www.nuget.org/packages/Azure.Identity/) w celu uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Zainstaluj biblioteki klienckie przy użyciu interfejsu wiersza polecenia .NET Core lub konsoli Menedżer pakietów w Visual Studio.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Otwórz wiersz polecenia i przejdź do katalogu zawierającego plik projektu.

Uruchom polecenia instalacji.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Menedżer pakietów](#tab/package-manager)

Otwórz projekt lub rozwiązanie w Visual Studio, a następnie otwórz konsolę przy użyciu polecenia Narzędzia  >  **NuGet Menedżer pakietów**  >  **Menedżer pakietów Console.**

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

Jeśli po zakończeniu pracy z tym samouczkiem nie potrzebujesz już aplikacji internetowej ani skojarzonych zasobów, wyczyść [utworzone zasoby.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz tożsamość zarządzaną przypisaną przez system.
> * Utwórz konto magazynu i Blob Storage kontenera.
> * Uzyskiwanie dostępu do magazynu z aplikacji internetowej przy użyciu tożsamości zarządzanych.

> [!div class="nextstepaction"]
> [App Service uzyskuje Microsoft Graph do Microsoft Graph w imieniu użytkownika](scenario-secure-app-access-microsoft-graph-as-user.md)

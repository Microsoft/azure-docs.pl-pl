---
title: Ustawianie list ACL rekursywnie dla Azure Data Lake Storage Gen2 | Microsoft Docs
description: Można dodawać, aktualizować i usuwać listy kontroli dostępu rekursywnie dla istniejących elementów podrzędnych katalogu nadrzędnego.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/27/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 247742cac3dc24b062fc8e1cb5eceb6c1a6f3f8b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911617"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Ustawianie list kontroli dostępu (ACL) rekursywnie dla Azure Data Lake Storage Gen2

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Teraz można także dodawać, aktualizować i usuwać listy ACL rekursywnie dla istniejących elementów podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego.

> [!NOTE]
> Możliwość ustawiania list dostępu rekurencyjnie jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach.  

[Biblioteki](#libraries)  |  [Przykłady](#code-samples)  |  [Najlepsze rozwiązania](#best-practice-guidelines)  |  [Przekaż opinię](#provide-feedback)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

- Odpowiednie uprawnienia do wykonywania procesu cyklicznego listy ACL. Odpowiednie uprawnienie obejmuje jedną z następujących czynności: 

  - Przydzielona [podmiot zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) Azure Active Directory (AD), do którego przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.   

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować proces cyklicznej listy ACL. Obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym. 

- Zrozumienie, jak listy ACL są stosowane do katalogów i plików. Zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Zapoznaj się z sekcją **Konfigurowanie projektu** w tym artykule, aby wyświetlić wskazówki dotyczące instalacji programu PowerShell, zestawu .NET SDK i zestawu Python SDK.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj niezbędne biblioteki.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Upewnij się, że program .NET Framework jest zainstalowany. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Sprawdź, czy zainstalowana wersja programu PowerShell jest `5.1` lub nowsza przy użyciu następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. Zainstaluj najnowszą wersję modułu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Zamknij program, a następnie ponownie otwórz konsolę programu PowerShell.

5. Zainstaluj **AZ. Storage** Preview module.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Otwórz [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](https://docs.microsoft.com/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure z zainstalowanym systemem jest `2.14.0` lub nowsza przy użyciu następującego polecenia.

   ```azurecli
    az --version
   ```
   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.14.0` , zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Otwórz okno polecenia (na przykład: Windows PowerShell).

2. W katalogu projektu Zainstaluj pakiet Azure. Storage. Files. datalake Preview za pomocą `dotnet add package` polecenia.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Dodaj te instrukcje using na początku pliku kodu.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Aby rozpocząć, Otwórz [Tę stronę](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) i Znajdź najnowszą wersję biblioteki języka Java. Następnie otwórz plik *pom.xml* w edytorze tekstów. Dodaj element zależności, który odwołuje się do tej wersji.

Jeśli planujesz uwierzytelnienie aplikacji klienckiej za pomocą Azure Active Directory (AD), a następnie Dodaj zależność do biblioteki klienta usługi Azure Secret. Zobacz  [Dodawanie pakietu biblioteki tajnego klienta do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Następnie Dodaj te instrukcje Imports do pliku kodu.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Pobierz [bibliotekę kliencką Azure Data Lake Storage dla języka Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Zainstaluj bibliotekę, która została pobrana za pomocą narzędzia [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Dodaj te instrukcje importu na początku pliku kodu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Łączenie ze swoim kontem

Można nawiązać połączenie za pomocą Azure Active Directory (AD) lub klucza konta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otwórz okno polecenia programu Windows PowerShell, a następnie zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, w którym chcesz utworzyć katalogi i zarządzać nimi. W tym przykładzie Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Następnie wybierz, w jaki sposób polecenia mają uzyskać autoryzację na konto magazynu. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opcja 1: uzyskiwanie autoryzacji za pomocą Azure Active Directory (AD)

W ramach tego podejścia system gwarantuje, że konto użytkownika ma odpowiednie przypisania kontroli dostępu opartej na rolach (Azure RBAC) i listy ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

W poniższej tabeli przedstawiono każdą z obsługiwanych ról i ich możliwości ustawienia listy ACL.

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opcja 2: uzyskiwanie autoryzacji przy użyciu klucza konta magazynu

W tym podejściu system nie sprawdza uprawnień usługi Azure RBAC ani listy ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom polecenie logowania.

   ```azurecli
   az login
   ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

   W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

   Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](../common/authorize-data-operations-cli.md).

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

> [!NOTE]
> W przykładzie przedstawionym w tym artykule przedstawiono autoryzację Azure Active Directory (AD). Aby dowiedzieć się więcej o metodach autoryzacji, zobacz [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](../common/authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) reprezentujące konto magazynu.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

Za pomocą [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) można uwierzytelniać aplikację w usłudze Azure AD.

Po zainstalowaniu pakietu Dodaj tę instrukcję using na początku pliku kodu.

```csharp
using Azure.Identity;
```

Pobierz identyfikator klienta, klucz tajny klienta i identyfikator dzierżawy. Aby to zrobić, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md). W ramach tego procesu należy przypisać jedną z następujących ról [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) do podmiotu zabezpieczeń. 

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

Ten przykład tworzy wystąpienie [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

To podejście jest najprostszym sposobem nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) przy użyciu klucza konta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [biblioteki klienta tożsamości platformy Azure dla programu .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

#### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [usługi Azure Identity Client Library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Python](https://pypi.org/project/azure-identity/) .

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md). W ramach tego procesu należy przypisać jedną z następujących ról [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) do podmiotu zabezpieczeń. 

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [Biblioteka klienta tożsamości platformy Azure dla](https://pypi.org/project/azure-identity/) dokumentacji języka Python.

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Zastąp wartość symbolu zastępczego `storage_account_name` nazwą konta magazynu.

- Zastąp `storage_account_key` wartość symbolu zastępczego kluczem dostępu do konta magazynu.

---

## <a name="set-an-acl-recursively"></a>Ustawianie listy ACL rekursywnie

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [aktualizowanie listy ACL rekursywnie](#update-an-acl-recursively) w tym artykule.   

Ta sekcja zawiera przykłady dotyczące sposobu ustawiania listy ACL 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ustawianie listy ACL rekursywnie za pomocą polecenia cmdlet **Set-AzDataLakeGen2AclRecursive** .

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Ustawianie listy ACL rekursywnie przy użyciu polecenia [AZ Storage FS Access Set-rekursywny](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład: `default:user::rwx` lub `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Ustawianie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL. 

Jeśli chcesz ustawić **domyślny** wpis listy ACL, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true** . 

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="java"></a>[Java](#tab/java)

Ustawianie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. setAccessControlRecursive** . Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL. 

Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, możesz wywołać metodę **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true** . 

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Ten parametr jest używany w każdym wywołaniu metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Ustaw listę ACL rekursywnie, wywołując metodę **DataLakeDirectoryClient.set_access_control_recursive** .

Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku każdego ciągu wpisu listy ACL. 

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . 

Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Jeśli ten parametr ma wartość `True` , lista wpisów listy ACL jest poprzedzana ciągiem `default:` . 

Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania. Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Aktualizowanie rekursywnie listy ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę kontroli dostępu, zamiast aktualizować ją, zobacz sekcję [Ustawianie listy ACL rekursywnie](#set-an-acl-recursively) w tym artykule. 

Aby zaktualizować listę kontroli dostępu, Utwórz nowy obiekt listy ACL z wpisem listy ACL, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji aktualizowania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania.

Ta sekcja zawiera przykłady dotyczące aktualizowania listy ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aktualizowanie listy ACL rekursywnie za pomocą polecenia cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Jeśli chcesz zaktualizować **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aktualizowanie listy ACL rekursywnie za pomocą polecenia  [AZ Storage FS Access Update-rekursywnego](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Aktualizowanie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL. 

Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true** . 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy zaktualizować domyślną listę ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="java"></a>[Java](#tab/java)

Aktualizowanie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. updateAccessControlRecursive** .  Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL. 

Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, możesz użyć metody **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true** . 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy zaktualizować domyślną listę ACL. Ten parametr jest używany w wywołaniu metody **setDefaultScope** w [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Aktualizowanie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient.update_access_control_recursive** . Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku każdego ciągu wpisu listy ACL. 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu.

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy zaktualizować domyślną listę ACL. Jeśli ten parametr ma wartość `True` , zaktualizowany wpis ACL jest poprzedzony ciągiem `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Co najmniej jeden wpis listy ACL można usunąć cyklicznie. Aby usunąć wpis listy ACL, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia. 

Ta sekcja zawiera przykłady dotyczące usuwania listy ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usuń wpisy listy ACL za pomocą polecenia cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

Ten przykład usuwa wpis listy ACL z katalogu głównego kontenera.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Jeśli chcesz usunąć **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Usuń wpisy listy ACL za pomocą polecenia [AZ Storage FS Access Remove-rekursywny](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

Ten przykład usuwa wpis listy ACL z katalogu głównego kontenera.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL. 

Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true** . 

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy usunąć wpis z domyślnej listy ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="java"></a>[Java](#tab/java)

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient. removeAccessControlRecursive** . Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL. 

Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, możesz użyć metody **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true** .  

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy usunąć wpis z domyślnej listy ACL. Ten parametr jest używany w wywołaniu metody **setDefaultScope** w [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient.remove_access_control_recursive** . Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku ciągu wpisu listy ACL. 

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy usunąć wpis z domyślnej listy ACL. Jeśli ten parametr ma wartość `True` , zaktualizowany wpis ACL jest poprzedzony ciągiem `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zwróć wyniki do zmiennej. Wpisy potoków zakończonych niepowodzeniem do sformatowanej tabeli.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Na podstawie danych wyjściowych tabeli można naprawić wszelkie błędy uprawnień, a następnie wznowić wykonywanie przy użyciu tokenu kontynuacji.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku awarii można zwrócić token kontynuacji przez ustawienie `--continue-on-failure` parametru na `true` . Po rozpoczęciu błędów można wznowić proces od momentu awarii, ponownie uruchamiając polecenie, a następnie ustawiając `--continuation` parametr na token kontynuacji. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość `null` dla parametru tokenu kontynuacji. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="java"></a>[Java](#tab/java)

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość `null` dla parametru tokenu kontynuacji. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość ``None`` dla parametru tokenu kontynuacji. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Zasoby

Ta sekcja zawiera linki do bibliotek i przykładów kodu.

#### <a name="libraries"></a>Biblioteki

- [Program PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Przykłady kodu

- PowerShell: [Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [przykład](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) Readme

- Interfejs wiersza polecenia platformy Azure: [przykład](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0) Readme

- Python: [Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [przykład](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D) Readme

## <a name="best-practice-guidelines"></a>Wskazówki dotyczące najlepszych rozwiązań

W tej sekcji przedstawiono niektóre najlepsze rozwiązania dotyczące ustawiania list ACL rekursywnie. 

#### <a name="handling-runtime-errors"></a>Obsługa błędów środowiska uruchomieniowego

Błąd w czasie wykonywania może być spowodowany wieloma przyczynami (na przykład: awaria lub problem z łącznością klienta). Jeśli wystąpi błąd środowiska uruchomieniowego, należy ponownie uruchomić proces cyklicznej listy ACL. Listy ACL można ponownie zastosować do elementów bez powodowania negatywnego wpływu. 

#### <a name="handling-permission-errors-403"></a>Obsługa błędów uprawnień (403)

Jeśli wystąpi wyjątek kontroli dostępu podczas wykonywania cyklicznego procesu listy ACL, [podmiot zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) usługi AD może nie mieć wystarczających uprawnień, aby zastosować listę ACL do co najmniej jednego elementu podrzędnego w hierarchii katalogów. W przypadku wystąpienia błędu uprawnień proces zostaje zatrzymany, a podano token kontynuacji. Usuń problem z uprawnieniami, a następnie użyj tokenu kontynuacji, aby przetworzyć pozostały zestaw danych. Katalogi i pliki, które zostały już pomyślnie przetworzone, nie będą musiały zostać przetworzone ponownie. Możesz również wybrać opcję ponownego uruchomienia cyklicznego procesu listy ACL. Listy ACL można ponownie zastosować do elementów bez powodowania negatywnego wpływu. 

#### <a name="credentials"></a>Poświadczenia 

Zalecamy zainicjowanie podmiotu zabezpieczeń usługi Azure AD, który ma przypisaną rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie docelowego konta magazynu lub kontenera. 

#### <a name="performance"></a>Wydajność 

Aby zmniejszyć opóźnienie, zalecamy uruchomienie cyklicznego procesu listy ACL na maszynie wirtualnej platformy Azure, która znajduje się w tym samym regionie co konto magazynu. 

#### <a name="acl-limits"></a>Limity listy ACL

Maksymalna liczba list ACL, które można zastosować do katalogu lub pliku, to 32 listy ACL dostępu i domyślne listy ACL 32. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Prześlij opinię lub zgłoś problemy

Możesz podać swoją opinię lub zgłosić problem pod adresem  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Zobacz także

- [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Znane problemy](data-lake-storage-known-issues.md)



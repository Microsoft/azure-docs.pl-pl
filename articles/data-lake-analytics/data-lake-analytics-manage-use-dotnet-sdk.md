---
title: Zarządzanie Azure Data Lake Analytics przy użyciu zestawu Azure .NET SDK
description: W tym artykule opisano, jak używać zestawu Azure .NET SDK do pisania aplikacji, które zarządzają Data Lake Analytics zadaniami, źródłami danych & użytkownikami.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/18/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: b06530488e74120f3cc511694d290840269db453
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020069"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Zarządzanie usługą Azure Data Lake Analytics za pomocą aplikacji platformy .NET

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami za pomocą aplikacji zapisaną przy użyciu zestawu Azure .NET SDK. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Zainstalowany program Visual Studio 2015, Visual Studio 2013 Update 4 lub Visual Studio 2012 z językiem Visual C++**.
* **Zestaw Microsoft Azure SDK dla programu .NET w wersji 2.5 lub nowszej**.  Można go zainstalować przy użyciu [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).
* **Wymagane pakiety NuGet**

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

|Pakiet|Wersja|
|-------|-------|
|[Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| ppkt|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0 — wersja zapoznawcza|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0 — wersja zapoznawcza|

Te pakiety można zainstalować za pośrednictwem wiersza polecenia NuGet przy użyciu następujących poleceń:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Zmienne wspólne

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Uwierzytelnianie

Istnieje wiele opcji logowania do Azure Data Lake Analytics. Poniższy fragment kodu przedstawia przykład uwierzytelniania z interakcyjnym uwierzytelnianiem użytkowników przy użyciu okna podręcznego.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

Kod źródłowy dla **GetCreds_User_Popup** i kod dla innych opcji uwierzytelniania są opisane w [Data Lake Analytics opcji uwierzytelniania platformy .NET](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) .


## <a name="create-the-client-management-objects"></a>Tworzenie obiektów zarządzania klientami

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Zarządzanie kontami

### <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

Jeśli jeszcze tego nie zrobiono, musisz mieć grupę zasobów platformy Azure, aby utworzyć składniki Data Lake Analytics. Potrzebujesz poświadczeń uwierzytelniania, identyfikatora subskrypcji i lokalizacji. Poniższy kod przedstawia sposób tworzenia grupy zasobów:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Aby uzyskać więcej informacji, zobacz grupy zasobów platformy Azure i Data Lake Analytics.

### <a name="create-a-data-lake-store-account"></a>Tworzenie konta usługi Data Lake Store

Kiedykolwiek konto ADLA wymaga konta ADLS. Jeśli nie masz jeszcze takiego, który ma być używany, możesz go utworzyć przy użyciu następującego kodu:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics

Poniższy kod tworzy konto ADLS

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Wyświetlanie listy kont Data Lake Store

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Wyświetlanie listy kont Data Lake Analytics

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Sprawdzanie, czy konto istnieje

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Uzyskaj informacje o koncie

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Usuwanie konta

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Pobierz domyślne konto Data Lake Store

Każde konto Data Lake Analytics wymaga domyślnego konta Data Lake Store. Użyj tego kodu, aby określić domyślne konto magazynu dla konta usługi Analytics.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Data Lake Analytics obecnie obsługuje następujące źródła danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Konto usługi Azure Storage](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Link do konta usługi Azure Storage

Możesz tworzyć linki do kont usługi Azure Storage.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Wyświetlanie listy źródeł danych usługi Azure Storage

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Wyświetlanie listy Data Lake Store źródeł danych

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Przekazywanie i Pobieranie folderów i plików

Za pomocą obiektu zarządzania klientami systemu plików Data Lake Store można przekazywać i pobierać pojedyncze pliki lub foldery z platformy Azure na komputer lokalny przy użyciu następujących metod:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

Pierwszym parametrem tych metod jest nazwa konta Data Lake Store, a następnie parametry ścieżki źródłowej i ścieżki docelowej.

Poniższy przykład pokazuje, jak pobrać folder w Data Lake Store.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Tworzenie pliku na koncie Data Lake Store

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Weryfikowanie ścieżek kont usługi Azure Storage

Poniższy kod sprawdza, czy konto usługi Azure Storage (storageAccntName) istnieje na koncie Data Lake Analytics (analyticsAccountName) i czy kontener (ContainerName) istnieje na koncie usługi Azure Storage.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Zarządzanie katalogiem i zadaniami

Obiekt DataLakeAnalyticsCatalogManagementClient zapewnia metody zarządzania bazą danych SQL dostarczoną dla każdego konta Azure Data Lake Analytics. DataLakeAnalyticsJobManagementClient zapewnia metody przesyłania zadań i zarządzania nimi w bazie danych za pomocą skryptów U-SQL.

### <a name="list-databases-and-schemas"></a>Wyświetlanie listy baz danych i schematów

Wśród kilku elementów, które można wyświetlić, najczęściej są bazy danych i ich schemat. Poniższy kod pobiera kolekcję baz danych, a następnie wylicza schemat dla każdej bazy danych.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Lista kolumn tabeli

Poniższy kod pokazuje, jak uzyskać dostęp do bazy danych za pomocą klienta zarządzania wykazem Data Lake Analytics, aby wyświetlić kolumny w określonej tabeli.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Przesyłanie zadania U-SQL

Poniższy kod pokazuje, jak za pomocą Data Lake Analytics klienta zarządzania zadaniami przesłać zadanie.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Wyświetl listę zadań zakończonych niepowodzeniem

Poniższy kod zawiera informacje o zadaniach, które zakończyły się niepowodzeniem.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Potoki listy

Poniższy kod zawiera informacje dotyczące poszczególnych potoków zadań przesłanych do konta.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Wystawianie cykliczne

Poniższy kod zawiera informacje dotyczące poszczególnych cyklów zadań przesłanych do konta.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Typowe scenariusze grafu

### <a name="look-up-user-in-the-aad-directory"></a>Wyszukaj użytkownika w katalogu usługi AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Pobieranie identyfikatora ObjectId użytkownika w katalogu usługi AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeniowymi

Obiekt DataLakeAnalyticsAccountManagementClient zawiera metody zarządzania zasadami obliczeniowymi dla konta Data Lake Analytics.

### <a name="list-compute-policies"></a>Wyświetl listę zasad obliczeniowych

Poniższy kod pobiera listę zasad obliczeniowych dla konta Data Lake Analytics.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Tworzenie nowych zasad obliczeniowych

Poniższy kod tworzy nowe zasady obliczeń dla konta Data Lake Analytics, ustawiając maksymalną wartość dla określonego użytkownika na 50, a minimalny priorytet zadania na 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
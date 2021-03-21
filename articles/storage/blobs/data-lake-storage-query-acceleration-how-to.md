---
title: Filtrowanie danych za pomocą przyspieszania zapytań Azure Data Lake Storage | Microsoft Docs
description: Użyj przyspieszenia zapytania, aby pobrać podzestaw danych z konta magazynu.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a925d3f55395d094c7f19f65de4b72fd20a11a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213678"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrowanie danych za pomocą przyspieszania zapytań Azure Data Lake Storage

W tym artykule pokazano, jak za pomocą przyspieszenia zapytania pobrać podzestaw danych z konta magazynu. 

Przyspieszenie zapytań umożliwia aplikacjom i platformom analitycznym znaczne Optymalizowanie przetwarzania danych przez pobranie tylko tych danych, których potrzebują do wykonania danej operacji. Aby dowiedzieć się więcej, zobacz [Azure Data Lake Storage przyspieszeniem zapytań](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Konto magazynu **ogólnego przeznaczenia w wersji 2** . Zobacz [Tworzenie konta magazynu](../common/storage-account-create.md).

- Wybierz kartę, aby wyświetlić wszystkie wymagania wstępne dotyczące zestawu SDK.

  ### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

  Nie dotyczy

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [Zestaw SDK platformy .NET](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > W tym artykule przyjęto założenie, że projekt Java został utworzony za pomocą platformy Apache Maven. Przykład sposobu tworzenia projektu przy użyciu platformy Apache Maven można znaleźć w temacie [Konfigurowanie](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 lub nowszy.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Korzystanie z zestawu SDK Node.js nie wymaga żadnych dodatkowych wymagań wstępnych.

---

## <a name="enable-query-acceleration"></a>Włącz przyspieszenie zapytań

Aby używać przyspieszenia zapytań, należy zarejestrować funkcję przyspieszenia zapytań w ramach subskrypcji. Po sprawdzeniu, że funkcja jest zarejestrowana, należy zarejestrować dostawcę zasobów usługi Azure Storage. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Krok 1. rejestrowanie funkcji przyspieszenia zapytań

Aby użyć przyspieszenia zapytania, musisz najpierw zarejestrować funkcję przyspieszenia zapytań w ramach subskrypcji. 

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Otwórz okno poleceń programu Windows PowerShell.

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

3. Zarejestruj funkcję przyspieszenia zapytań za pomocą polecenia [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

3. Zarejestruj funkcję przyspieszenia zapytań za pomocą polecenia [AZ Feature Register](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2: sprawdzenie, czy funkcja jest zarejestrowana

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy rejestracja została ukończona, użyj polecenia [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy rejestracja została ukończona, użyj polecenia [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Krok 3. rejestrowanie dostawcy zasobów usługi Azure Storage

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. 

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować dostawcę zasobów, użyj polecenia [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować dostawcę zasobów, użyj polecenia [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

### <a name="step-1-install-packages"></a>Krok 1. Instalowanie pakietów 

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zainstaluj program AZ module w wersji 4.6.0 lub nowszej.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Aby zaktualizować ze starszej wersji polecenia AZ, uruchom następujące polecenie:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Otwórz wiersz polecenia i zmień katalog ( `cd` ) na folder projektu, na przykład:

   ```console
   cd myProject
   ```

2. Zainstaluj `12.5.0-preview.6` wersję lub nowszą bibliotekę klienta usługi Azure Blob Storage dla platformy .NET za pomocą `dotnet add package` polecenia. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu biblioteki [CsvHelper](https://www.nuget.org/packages/CsvHelper/) . Aby użyć tej biblioteki, użyj następującego polecenia.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Otwórz plik *pom.xml* projektu w edytorze tekstu. Dodaj następujące elementy zależności do grupy zależności. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Zainstaluj Azure Data Lake Storageą bibliotekę kliencką dla języka Python za pomocą narzędzia [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Zainstaluj Data Lake bibliotekę kliencką dla języka JavaScript, otwierając okno terminalu, a następnie wpisując następujące polecenie.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Krok 2. Dodawanie instrukcji

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Nie dotyczy

#### <a name="net"></a>[.NET](#tab/dotnet)

Dodaj te `using` instrukcje na początku pliku kodu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Przyspieszenie zapytań pobiera dane sformatowane w formacie CSV i JSON. W związku z tym Pamiętaj, aby dodać instrukcje using dla wszystkich bibliotek analizy woluminów CSV lub JSON, które będą używane. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu biblioteki [CsvHelper](https://www.nuget.org/packages/CsvHelper/) dostępnej w programie NuGet. W związku z tym dodamy te `using` instrukcje na początku pliku kodu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Aby skompilować Przykłady przedstawione w tym artykule, należy również dodać te `using` instrukcje.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Dodaj te `import` instrukcje na początku pliku kodu.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Dodaj te instrukcje importu na początku pliku kodu.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Dołącz `storage-blob` moduł, umieszczając tę instrukcję w górnej części pliku kodu. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Przyspieszenie zapytań pobiera dane sformatowane w formacie CSV i JSON. W związku z tym Pamiętaj, aby dodać instrukcje dla wszystkich modułów analizy woluminów CSV lub JSON, których chcesz użyć. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu modułu [Fast-CSV](https://www.npmjs.com/package/fast-csv) . W związku z tym należy dodać tę instrukcję na początku pliku kodu.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Pobieranie danych przy użyciu filtru

Aby określić predykaty filtru wierszy i projekcje kolumn w żądaniu przyspieszenia zapytania, można użyć języka SQL. Poniższy kod wysyła zapytanie do pliku CSV w magazynie i zwraca wszystkie wiersze danych, w których trzecia kolumna jest zgodna z wartością `Hemingway, Ernest` . 

- W zapytaniu SQL słowo kluczowe `BlobStorage` jest używane do określenia pliku, który jest wysyłany do zapytania.

- Odwołania do kolumn są określone, `_N` gdy pierwsza kolumna to `_1` . Jeśli plik źródłowy zawiera wiersz nagłówka, można odwoływać się do kolumn według nazwy określonej w wierszu nagłówka. 

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Metoda async `BlobQuickQueryClient.QueryAsync` wysyła zapytanie do interfejsu API przyspieszenia zapytania, a następnie strumieniuje wyniki z powrotem do aplikacji jako obiekt [strumienia](/dotnet/api/system.io.stream) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoda `BlobQuickQueryClient.openInputStream()` wysyła zapytanie do interfejsu API przyspieszenia zapytania, a następnie strumieniuje wyniki z powrotem do aplikacji jako `InputStream` obiekt, który można odczytać jak każdy inny obiekt InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ten przykład wysyła zapytanie do interfejsu API przyspieszenia zapytania, a następnie przesyła strumieniowo wyniki. `blob`Obiekt przeszedł do `queryHemingway` funkcji pomocnika jest typu [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Aby dowiedzieć się więcej o tym, jak uzyskać obiekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) , zobacz [Szybki Start: Zarządzanie obiektami BLOB za pomocą języka JavaScript v12 SDK w Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Pobieranie określonych kolumn

Możesz ograniczyć zakres wyników do podzbioru kolumn. W ten sposób można pobrać tylko te kolumny, które są potrzebne do wykonania danego obliczenia. Poprawia to wydajność aplikacji i zmniejsza koszty, ponieważ mniejsze ilości danych są przesyłane przez sieć. 

Ten kod pobiera tylko `BibNum` kolumnę dla wszystkich książek w zestawie danych. Używa również informacji z wiersza nagłówka w pliku źródłowym do odwoływania się do kolumn w zapytaniu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Poniższy kod łączy filtrowanie wierszy i projekcje kolumn w tym samym zapytaniu. 

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Następne kroki

- [Przyspieszenie kwerendy Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Dokumentacja języka SQL przyspieszania zapytań](query-acceleration-sql-reference.md)

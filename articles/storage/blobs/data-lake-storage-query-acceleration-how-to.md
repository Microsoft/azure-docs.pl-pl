---
title: Filtrowanie danych przy użyciu Azure Data Lake Storage zapytań | Microsoft Docs
description: Użyj przyspieszania zapytań, aby pobrać podzestaw danych z konta magazynu.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 756258db1c6e91002bf3a7c2bd0f71f921ce655d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769935"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrowanie danych przy użyciu Azure Data Lake Storage zapytań

W tym artykule pokazano, jak używać przyspieszania zapytań do pobierania podzestawu danych z konta magazynu. 

Przyspieszanie zapytań umożliwia aplikacjom i platformom analitycznym znaczącą optymalizację przetwarzania danych przez pobieranie tylko danych wymaganych do wykonania danej operacji. Aby dowiedzieć się więcej, [zobacz Azure Data Lake Storage Query Acceleration (Przyspieszanie zapytań).](data-lake-storage-query-acceleration.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) rozpoczęciem utwórz bezpłatne konto.

- Konto **magazynu ogólnego przeznaczenia w wersji 2.** Zobacz [Tworzenie konta magazynu.](../common/storage-account-create.md)

- Wybierz kartę, aby wyświetlić wymagania wstępne dotyczące zestawu SDK.

  ### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

  Nie dotyczy

  ### <a name="net"></a>[.NET](#tab/dotnet)

  Zestaw [SDK platformy .NET](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Zestaw Java Development Kit (JDK) w](/java/azure/jdk/) wersji 8 lub nowszej

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > W tym artykule przyjęto założenie, że utworzono projekt w języku Java przy użyciu narzędzia Apache Maven. Aby uzyskać przykład sposobu tworzenia projektu przy użyciu narzędzia Apache Maven, zobacz [Konfigurowanie programu](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Język Python](https://www.python.org/downloads/) w wersji 3.8 lub większej.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Korzystanie z zestawu SDK usługi Node.js wymaga dodatkowych wymagań wstępnych.

---

## <a name="enable-query-acceleration"></a>Włączanie przyspieszania zapytań

Aby korzystać z przyspieszania zapytań, musisz zarejestrować funkcję przyspieszania zapytań w swojej subskrypcji. Po sprawdzeniu, czy funkcja jest zarejestrowana, musisz zarejestrować dostawcę zasobów usługi Azure Storage. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Krok 1. Rejestrowanie funkcji przyspieszania zapytań

Aby korzystać z przyspieszania zapytań, należy najpierw zarejestrować funkcję przyspieszania zapytań w ramach subskrypcji. 

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Otwórz okno Windows PowerShell polecenia.

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

2. Jeśli Tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

3. Zarejestruj funkcję przyspieszania zapytań przy użyciu [polecenia Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs wiersza [](/cli/azure/install-azure-cli) polecenia platformy Azure został zainstalowany lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

3. Zarejestruj funkcję przyspieszania zapytań za pomocą [polecenia az feature register.](/cli/azure/feature#az_feature_register)

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2. Sprawdzenie, czy funkcja została zarejestrowana

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy rejestracja jest ukończona, użyj [polecenia Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy rejestracja została ukończona, użyj [polecenia az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Krok 3. Rejestrowanie dostawcy zasobów usługi Azure Storage

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. 

#### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować dostawcę zasobów, użyj [polecenia Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować dostawcę zasobów, użyj [polecenia az provider register.](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

### <a name="step-1-install-packages"></a>Krok 1. Instalowanie pakietów 

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zainstaluj moduł Az w wersji 4.6.0 lub wyższej.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Aby zaktualizować starszą wersję polecenia Az, uruchom następujące polecenie:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Otwórz wiersz polecenia i zmień katalog ( `cd` ) na folder projektu, na przykład:

   ```console
   cd myProject
   ```

2. Zainstaluj wersję lub nowszą biblioteki klienta usługi Azure Blob Storage dla `12.5.0-preview.6` platformy .NET za pomocą polecenia `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu [biblioteki CsvHelper.](https://www.nuget.org/packages/CsvHelper/) Aby użyć tej biblioteki, użyj następującego polecenia.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Otwórz *pom.xml* projektu w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności. 

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

Zainstaluj bibliotekę Azure Data Lake Storage klienta dla języka Python przy użyciu narzędzia [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Zainstaluj bibliotekę klienta usługi Data Lake dla języka JavaScript, otwierając okno terminalu, a następnie wpisując następujące polecenie.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Krok 2. Dodawanie instrukcji

#### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Nie dotyczy

#### <a name="net"></a>[.NET](#tab/dotnet)

Dodaj `using` te instrukcje na początku pliku kodu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Przyspieszanie zapytań pobiera dane w formacie CSV i JSON. W związku z tym pamiętaj o dodaniu instrukcji using dla wszystkich bibliotek analizy plików CSV lub JSON, które mają być przez Ciebie wybierane. Przykłady, które są wyświetlane w tym artykule, analizują plik CSV przy użyciu biblioteki [CsvHelper](https://www.nuget.org/packages/CsvHelper/) dostępnej w nuGet. W związku z tym dodamy te `using` instrukcje na początku pliku kodu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Aby skompilować przykłady przedstawione w tym artykule, należy również dodać `using` te instrukcje.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Dodaj `import` te instrukcje na początku pliku kodu.

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

Dołącz `storage-blob` moduł, umieszczając tę instrukcji w górnej części pliku kodu. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Przyspieszanie zapytań pobiera dane w formacie CSV i JSON. W związku z tym pamiętaj o dodaniu instrukcji dla wszystkich modułów analizy plików CSV lub JSON, które będą przez Ciebie wybierane. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu [modułu fast-csv.](https://www.npmjs.com/package/fast-csv) W związku z tym dodamy tę instrukcje na początku pliku kodu.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Pobieranie danych przy użyciu filtru

Za pomocą języka SQL można określić predykaty filtru wierszy i projekcje kolumn w żądaniu przyspieszenia zapytań. Poniższy kod wysyła zapytanie do pliku CSV w magazynie i zwraca wszystkie wiersze danych, w których trzecia kolumna odpowiada wartości `Hemingway, Ernest` . 

- W zapytaniu SQL słowo kluczowe jest używane do oznaczania pliku, `BlobStorage` który jest odpytyty.

- Odwołania do kolumn są określane jako `_N` , gdzie pierwsza kolumna to `_1` . Jeśli plik źródłowy zawiera wiersz nagłówka, możesz odwoływać się do kolumn według nazwy określonej w wierszu nagłówka. 

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

Metoda asynchroniczna wysyła zapytanie do interfejsu API przyspieszania zapytań, a następnie przesyła strumieniowo wyniki z powrotem do aplikacji `BlobQuickQueryClient.QueryAsync` jako [obiekt usługi Stream.](/dotnet/api/system.io.stream)

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

Metoda wysyła zapytanie do interfejsu API przyspieszania zapytań, a następnie przesyła strumieniowo wyniki z powrotem do aplikacji jako obiekt, który można odczytać jak każdy `BlobQuickQueryClient.openInputStream()` `InputStream` inny obiekt InputStream.

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

Ten przykład wysyła zapytanie do interfejsu API przyspieszania zapytań, a następnie przesyła strumieniowo wyniki z powrotem. Obiekt `blob` przekazany do funkcji `queryHemingway` pomocnika jest typu [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Aby dowiedzieć się więcej na temat sposobu uzyskania obiektu [BlockBlobClient,](/javascript/api/@azure/storage-blob/blockblobclient) zobacz Szybki start: zarządzanie obiektami blob za pomocą zestawu SDK języka JavaScript w wersji [12 ](storage-quickstart-blobs-nodejs.md)w Node.js.

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

Zakres wyników można określić dla podzbioru kolumn. W ten sposób można pobrać tylko kolumny potrzebne do wykonania danego obliczenia. Zwiększa to wydajność aplikacji i zmniejsza koszty, ponieważ za pośrednictwem sieci jest przesyłanych mniej danych. 

Ten kod pobiera tylko `BibNum` kolumnę dla wszystkich książek w zestawie danych. Używa również informacji z wiersza nagłówka w pliku źródłowym, aby odwoływać się do kolumn w zapytaniu.

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

Poniższy kod łączy filtrowanie wierszy i projekcje kolumn w to samo zapytanie. 

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

- [Azure Data Lake Storage przyspieszania zapytań](data-lake-storage-query-acceleration.md)
- [Informacje o języku SQL przyspieszania zapytań](query-acceleration-sql-reference.md)

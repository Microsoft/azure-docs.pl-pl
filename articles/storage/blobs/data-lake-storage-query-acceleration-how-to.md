---
title: Filtrowanie danych za pomocą przyspieszania zapytań Azure Data Lake Storage (wersja zapoznawcza) | Microsoft Docs
description: Użyj przyspieszenia zapytania (wersja zapoznawcza), aby pobrać podzestaw danych z konta magazynu.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: 22776d9498676ec77cd71845ca5e39f01926259d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137573"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrowanie danych przy użyciu przyspieszania zapytań Azure Data Lake Storage (wersja zapoznawcza)

W tym artykule pokazano, jak za pomocą przyspieszenia zapytania (wersja zapoznawcza) pobrać podzestaw danych z konta magazynu. 

Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja Azure Data Lake Storage, która umożliwia aplikacjom i platformom analitycznym znaczne Optymalizowanie przetwarzania danych przez pobranie tylko tych danych, których potrzebują do wykonania danej operacji. Aby dowiedzieć się więcej, zobacz [Azure Data Lake Storage przyspieszeniem zapytań (wersja zapoznawcza)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Funkcja przyspieszenia zapytań jest w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja środkowa. Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) . Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="net"></a>[.NET](#tab/dotnet)

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Konto magazynu **ogólnego przeznaczenia w wersji 2** . Zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

- [zestaw SDK platformy .NET](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Konto magazynu **ogólnego przeznaczenia w wersji 2** . Zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub nowszej.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > W tym artykule przyjęto założenie, że projekt Java został utworzony za pomocą platformy Apache Maven. Przykład sposobu tworzenia projektu przy użyciu platformy Apache Maven można znaleźć w temacie [Konfigurowanie](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Zainstaluj pakiety 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Pobierz pakiety przyspieszania zapytań. Można uzyskać skompresowany plik zip zawierający te pakiety za pomocą tego linku: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net). 

2. Wyodrębnij zawartość tego pliku do katalogu projektu.

3. Otwórz plik projektu (*. csproj*) w edytorze tekstów i Dodaj te odwołania do pakietu wewnątrz elementu \<projektu.\>

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Przywróć pakiety w wersji zapoznawczej zestawu SDK. To przykładowe polecenie przywraca pakiety zestawu SDK wersji zapoznawczej `dotnet restore` za pomocą polecenia. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Przywróć wszystkie inne zależności z publicznego repozytorium NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Utwórz katalog w folderze głównym projektu. Katalog główny jest katalogiem, który zawiera plik **pliku pom. XML** .

   > [!NOTE]
   > W przykładach w tym artykule przyjęto założenie, że nazwa katalogu to **lib**.

2. Pobierz pakiety przyspieszania zapytań. Można uzyskać skompresowany plik zip zawierający te pakiety za pomocą tego linku: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java). 

3. Wyodrębnij pliki z pliku zip do utworzonego katalogu. W naszym przykładzie katalog nosi nazwę **lib**. 

4. Otwórz plik *pliku pom. XML* w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Dodaj instrukcje


### <a name="net"></a>[.NET](#tab/dotnet)

Dodaj te `using` instrukcje na początku pliku kodu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Przyspieszenie zapytań pobiera dane sformatowane w formacie CSV i JSON. W związku z tym Pamiętaj, aby dodać instrukcje using dla wszystkich bibliotek analizy woluminów CSV lub JSON, które będą używane. Przykłady, które pojawiają się w tym artykule, analizują plik CSV przy użyciu biblioteki [CsvHelper](https://www.nuget.org/packages/CsvHelper/) dostępnej w programie NuGet. W związku z tym dodamy `using` te instrukcje na początku pliku kodu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Aby skompilować Przykłady przedstawione w tym artykule, należy również dodać te `using` instrukcje.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Dodaj te `import` instrukcje na początku pliku kodu.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Pobieranie danych przy użyciu filtru

Aby określić predykaty filtru wierszy i projekcje kolumn w żądaniu przyspieszenia zapytania, można użyć języka SQL. Poniższy kod wysyła zapytanie do pliku CSV w magazynie i zwraca wszystkie wiersze danych, w których trzecia kolumna jest zgodna z `Hemingway, Ernest`wartością. 

- W zapytaniu SQL słowo kluczowe `BlobStorage` jest używane do określenia pliku, który jest wysyłany do zapytania.

- Odwołania do kolumn są określone `_N` , gdy pierwsza kolumna to `_1`. Jeśli plik źródłowy zawiera wiersz nagłówka, można odwoływać się do kolumn według nazwy określonej w wierszu nagłówka. 

### <a name="net"></a>[.NET](#tab/dotnet)

Metoda `BlobQuickQueryClient.QueryAsync` Async wysyła zapytanie do interfejsu API przyspieszenia zapytania, a następnie strumieniuje wyniki z powrotem do aplikacji jako obiekt [strumienia](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoda `BlobQuickQueryClient.openInputStream()` wysyła zapytanie do interfejsu API przyspieszenia zapytania, a następnie strumieniuje wyniki z powrotem do aplikacji jako `InputStream` obiekt, który można odczytać jak każdy inny obiekt InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Pobieranie określonych kolumn

Możesz ograniczyć zakres wyników do podzbioru kolumn. W ten sposób można pobrać tylko te kolumny, które są potrzebne do wykonania danego obliczenia. Poprawia to wydajność aplikacji i zmniejsza koszty, ponieważ mniejsze ilości danych są przesyłane przez sieć. 

Ten kod pobiera tylko `PublicationYear` kolumnę dla wszystkich książek w zestawie danych. Używa również informacji z wiersza nagłówka w pliku źródłowym do odwoływania się do kolumn w zapytaniu.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Poniższy kod łączy filtrowanie wierszy i projekcje kolumn w tym samym zapytaniu. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Następne kroki

- [Formularz rejestracji przyspieszania zapytań](https://aka.ms/adls/queryaccelerationpreview)    
- [Przyspieszenie zapytań Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration.md)
- [Informacje dotyczące języka SQL przyspieszania zapytań (wersja zapoznawcza)](query-acceleration-sql-reference.md)

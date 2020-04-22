---
title: Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Lake Storage (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Użyj akceleracji zapytań (podglądu), aby pobrać podzbiór danych z konta magazynu.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771848"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)

W tym artykule pokazano, jak używać akceleracji zapytań (podglądu) do pobierania podzbioru danych z konta magazynu. 

Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja usługi Azure Data Lake Storage, która umożliwia aplikacjom i platformom analitycznym radykalną optymalizację przetwarzania danych przez pobranie tylko danych, które są potrzebne do wykonania danej operacji. Aby dowiedzieć się więcej, zobacz [Akceleracja zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Funkcja przyspieszania kwerend jest dostępna w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja Central. Aby przejrzeć ograniczenia, zobacz artykuł [Znane problemy.](data-lake-storage-known-issues.md) Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="net"></a>[.NET](#tab/dotnet)

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem subskrypcji.

- Ogólne przeznaczenie konta magazynu **w wersji 2.** zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem subskrypcji.

- Ogólne przeznaczenie konta magazynu **w wersji 2.** zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) w wersji 8 lub wyższej.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > W tym artykule przyjęto założenie, że projekt Java został utworzony przy użyciu apache Maven. Aby uzyskać przykład tworzenia projektu przy użyciu aplikacji Apache Maven, zobacz [Konfigurowanie](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Instalowanie pakietów 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Pobierz pakiety akceleracji zapytań. Skompresowany plik zip, który zawiera te pakiety, można uzyskać za pomocą tego łącza: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net). 

2. Wyodrębnij zawartość tego pliku do katalogu projektu.

3. Otwórz plik projektu (*csproj*) w edytorze tekstu i dodaj \<\> te odwołania do pakietu wewnątrz elementu Projekt.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Przywracanie pakietów SDK podglądu. To przykładowe polecenie przywraca pakiety sdk w wersji zapoznawczej `dotnet restore` za pomocą polecenia. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Przywróć wszystkie inne zależności z publicznego repozytorium NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Utwórz katalog w katalogu głównym projektu. Katalog główny jest katalogiem zawierającym plik **pom.xml.**

   > [!NOTE]
   > Przykłady w tym artykule zakładają, że nazwa katalogu jest **lib**.

2. Pobierz pakiety akceleracji zapytań. Skompresowany plik zip, który zawiera te pakiety, można uzyskać za pomocą tego łącza: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java). 

3. Wyodrębnij pliki z tego pliku zip do utworzonego katalogu. W naszym przykładzie ten katalog nosi nazwę **lib**. 

4. Otwórz plik *pom.xml* w edytorze tekstu. Dodaj następujące elementy zależności do grupy zależności. 

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

## <a name="add-statements"></a>Dodawanie instrukcji


### <a name="net"></a>[.NET](#tab/dotnet)

Dodaj `using` te instrukcje do górnej części pliku kodu.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Przyspieszenie kwerend pobiera dane sformatowane w formacie CSV i Json. W związku z tym należy dodać przy użyciu instrukcji dla wszystkich bibliotek CSV lub Json analizowania, które można wybrać do użycia. Przykłady, które pojawiają się w tym artykule przeanalizować plik CSV przy użyciu biblioteki [CsvHelper,](https://www.nuget.org/packages/CsvHelper/) który jest dostępny na NuGet. W związku z tym `using` chcielibyśmy dodać te instrukcje do górnej części pliku kodu.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Aby skompilować przykłady przedstawione w tym artykule, należy również dodać te `using` instrukcje, jak również.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Dodaj `import` te instrukcje do górnej części pliku kodu.

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

Za pomocą języka SQL można określić predykaty filtru wiersza i rzutowania kolumn w żądaniu przyspieszenia kwerendy. Poniższy kod wysyła zapytanie do pliku CSV w magazynie i zwraca `Hemingway, Ernest`wszystkie wiersze danych, w których trzecia kolumna jest zgodna z wartością . 

- W kwerendzie SQL `BlobStorage` słowo kluczowe służy do oznaczania pliku, który jest poszukiwany.

- Odwołania do kolumn `_N` są określane jako `_1`miejsca, w którym znajduje się pierwsza kolumna . Jeśli plik źródłowy zawiera wiersz nagłówka, można odwoływać się do kolumn o nazwie określonej w wierszu nagłówka. 

### <a name="net"></a>[.NET](#tab/dotnet)

Metoda `BlobQuickQueryClient.QueryAsync` asynchronicznej wysyła kwerendę do interfejsu API przyspieszania kwerendy, a następnie przesyła wyniki z powrotem do aplikacji jako obiekt [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

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

Metoda `BlobQuickQueryClient.openInputStream()` wysyła kwerendę do interfejsu API przyspieszania kwerendy, a następnie `InputStream` strumieniuje wyniki z powrotem do aplikacji jako obiekt, który może być odczytywany jak każdy inny obiekt InputStream.

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

Można zakres wyników do podzbioru kolumn. W ten sposób można pobrać tylko kolumny potrzebne do wykonania danego obliczenia. Zwiększa to wydajność aplikacji i zmniejsza koszty, ponieważ mniej danych jest przesyłanych przez sieć. 

Ten kod pobiera `PublicationYear` tylko kolumnę dla wszystkich książek w zestawie danych. Używa również informacji z wiersza nagłówka w pliku źródłowym do odwoływania się do kolumn w kwerendzie.


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

Poniższy kod łączy filtrowanie wierszy i rzutowania kolumn w tej samej kwerendzie. 

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

- [Formularz rejestracji akceleracji kwerend](https://aka.ms/adls/queryaccelerationpreview)    
- [Akceleracja zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration.md)
- [Odwołanie do języka SQL akceleracji kwerendy (wersja zapoznawcza)](query-acceleration-sql-reference.md)
- Odwołanie do interfejsu API REST akceleracji kwerend

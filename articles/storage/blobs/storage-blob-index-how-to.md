---
title: Używanie tagów indeksu obiektów BLOB do zarządzania danymi i znajdowania ich na platformie Azure Blob Storage
description: Zobacz przykłady użycia tagów indeksu obiektów BLOB do kategoryzowania i wykonywania zapytań dotyczących obiektów blob oraz zarządzania nimi.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/05/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: a820f7efc39af8c6ab66c883d285b507c7bc7368
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563272"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Użyj tagów indeksu obiektów BLOB (wersja zapoznawcza), aby zarządzać danymi na platformie Azure Blob Storage i znajdować je

Tagi indeksu obiektów BLOB klasyfikują dane na koncie magazynu przy użyciu atrybutów tagów klucz-wartość. Tagi te są automatycznie indeksowane i uwidaczniane jako wielowymiarowy indeks, który umożliwia łatwe wyszukiwanie danych. W tym artykule pokazano, jak ustawiać, pobierać i znajdować dane przy użyciu tagów indeksu obiektów BLOB.

> [!IMPORTANT]
> Tagi indeksów obiektów BLOB są obecnie w **wersji zapoznawczej** i są dostępne we wszystkich regionach publicznych. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.

Aby dowiedzieć się więcej na temat tej funkcji wraz ze znanymi problemami i ograniczeniami, zobacz [Zarządzanie danymi obiektów blob platformy Azure za pomocą tagów indeksu obiektów BLOB (wersja zapoznawcza) i znajdowanie](storage-manage-find-blobs.md)ich.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Subskrypcja platformy Azure zarejestrowana i zatwierdzona do uzyskiwania dostępu do podglądu indeksu obiektów BLOB
- Dostęp do [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Ponieważ indeks obiektów BLOB jest w wersji zapoznawczej, pakiet programu .NET Storage jest publikowany w kanale informacyjnym narzędzia NuGet. Ta biblioteka może ulec zmianie w okresie zapoznawczym.

1. Skonfiguruj projekt programu Visual Studio, aby rozpocząć pracę z biblioteką V12 klienta Blob Storage platformy Azure dla platformy .NET. Aby dowiedzieć się więcej, zobacz [.NET — szybki start](storage-quickstart-blobs-dotnet.md)

2. W Menedżerze pakietów NuGet Znajdź pakiet **Azure. Storage. blob** i Zainstaluj wersję **12.7.0-Preview. 1** lub nowszą dla projektu. Możesz również uruchomić polecenie programu PowerShell: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   Aby dowiedzieć się, jak to zrobić, zobacz [Znajdowanie i instalowanie pakietu](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Dodaj następujące instrukcje using na początku pliku kodu.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Przekaż nowy obiekt BLOB za pomocą tagów indeksu

To zadanie można wykonać przez [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub podmiot zabezpieczeń, który uzyskał uprawnienie do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu 

2. Przejdź do opcji **kontenery** w obszarze **BLOB Service**, wybierz kontener

3. Wybierz przycisk **Przekaż** i Przeglądaj lokalny system plików, aby znaleźć plik do przekazania jako blokowy obiekt BLOB.

4. Rozwiń listę rozwijaną **Zaawansowane** i przejdź do sekcji **Tagi indeksu obiektów BLOB**

5. Wprowadź Tagi indeksu obiektów BLOB klucza/wartości, które mają zostać zastosowane do danych

6. Wybierz przycisk **Przekaż** , aby przekazać obiekt BLOB

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Zrzut ekranu przedstawiający Azure Portal pokazujący, jak przekazać obiekt BLOB za pomocą tagów indeksu.":::

# <a name="net"></a>[.NET](#tab/net)

Poniższy przykład pokazuje, jak utworzyć obiekt BLOB dołączania z tagami ustawionymi podczas tworzenia.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Pobieranie, ustawianie i aktualizowanie tagów indeksów obiektów BLOB

Pobieranie tagów indeksów obiektów BLOB może odbywać się przez [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub podmiot zabezpieczeń, który otrzymał uprawnienia do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure.

Ustawienie i zaktualizowanie tagów indeksów obiektów BLOB może być wykonywane przez [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub podmiot zabezpieczeń, który otrzymał uprawnienia do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu 

2. Przejdź do opcji **kontenery** w obszarze **BLOB Service**, wybierz kontener

3. Wybierz obiekt BLOB z listy obiektów BLOB w wybranym kontenerze

4. Na karcie Przegląd obiektów BLOB zostaną wyświetlone właściwości obiektów blob, w tym wszystkie **Tagi indeksów obiektów BLOB**

5. Można pobierać, ustawiać, modyfikować lub usuwać dowolne Tagi indeksu klucz/wartość dla obiektu BLOB

6. Wybierz przycisk **Zapisz** , aby potwierdzić wszystkie aktualizacje obiektu BLOB

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Zrzut ekranu przedstawiający Azure Portal pokazujący, jak pobierać, ustawiać, aktualizować i usuwać Tagi indeksów w obiektach Blob.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrowanie i znajdowanie danych przy użyciu tagów indeksów obiektów BLOB

To zadanie można wykonać przez [właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) lub podmiot zabezpieczeń, który uzyskał uprawnienie do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [operacji dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) za pośrednictwem niestandardowej roli platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal filtr tagów indeksu obiektów BLOB automatycznie stosuje `@container` parametr w celu określania zakresu wybranego kontenera. Jeśli chcesz filtrować i znajdować oznakowane dane na całym koncie magazynu, użyj naszego interfejsu API REST, zestawów SDK lub narzędzi.

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu. 

2. Przejdź do opcji **kontenery** w obszarze **BLOB Service**, wybierz kontener

3. Wybierz przycisk **filtrowania tagów indeksu obiektów BLOB** , aby odfiltrować w wybranym kontenerze

4. Wprowadź klucz znacznika indeksu obiektów blob i wartość tagu

5. Wybierz przycisk **Filtr tagów indeksu obiektów BLOB** , aby dodać dodatkowe filtry tagów (do 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Zrzut ekranu przedstawiający Azure Portal pokazujący sposób filtrowania i znajdowania otagowanych obiektów BLOB przy użyciu tagów indeksu":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Zarządzanie cyklem życia z filtrami tagów indeksu obiektów BLOB

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu. 

2. Przejdź do opcji **zarządzania cyklem życia** w obszarze **BLOB Service**

3. Wybierz pozycję *Dodaj regułę* , a następnie wypełnij pola formularza zestawu akcji.

4. Wybierz zestaw **filtrów** , aby dodać opcjonalny filtr dla dopasowania prefiksu i dopasowania indeksu obiektów BLOB

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Zrzut ekranu przedstawiający Azure Portal pokazujący, jak dodać znaczniki indeksu do zarządzania cyklem życia.":::

5. Wybierz pozycję **Przegląd + Dodaj** , aby przejrzeć ustawienia reguły

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania reguły zarządzania cyklem życia z przykładem filtru tagów indeksu obiektów BLOB":::

6. Wybierz pozycję **Dodaj** , aby zastosować nową regułę do zasad zarządzania cyklem życia

# <a name="net"></a>[.NET](#tab/net)

Zasady [zarządzania cyklem życia](storage-lifecycle-management-concepts.md) są stosowane dla każdego konta magazynu na poziomie płaszczyzny kontroli. W przypadku platformy .NET zainstaluj [bibliotekę magazynów zarządzania Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) w wersji 16.0.0 lub nowszej.

---

## <a name="next-steps"></a>Następne kroki

 - Dowiedz się więcej o tagach indeksu obiektów blob, zobacz artykuł [Zarządzanie danymi obiektów blob platformy Azure i ich wyszukiwanie przy użyciu tagów indeksu obiektów BLOB](storage-manage-find-blobs.md )
 - Dowiedz się więcej o zarządzaniu cyklem życia, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)

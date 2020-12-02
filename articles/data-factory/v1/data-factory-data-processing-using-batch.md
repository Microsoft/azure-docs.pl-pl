---
title: Przetwarzanie zestawów danych na dużą skalę przy użyciu Data Factory i usługi Batch
description: Opisuje, jak przetwarzać ogromne ilości danych w potoku Azure Data Factory przy użyciu funkcji przetwarzania równoległego Azure Batch.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 365de79db450eda897621ccde3b92478ed93ecc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496164"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Przetwarzanie zestawów danych na dużą skalę przy użyciu Data Factory i usługi Batch
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [działania niestandardowe w Data Factory](../transform-data-using-dotnet-custom-activity.md).

W tym artykule opisano architekturę przykładowego rozwiązania, które przenosi i przetwarza zestawy danych na dużą skalę w sposób automatyczny i zaplanowany. Zawiera również kompleksowy przewodnik dotyczący implementowania rozwiązania przy użyciu Data Factory i Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten artykuł jest dłuższy niż typowy artykuł, ponieważ zawiera Przewodnik po całym przykładowym rozwiązaniu. Jeśli dopiero zaczynasz korzystać z usługi Batch i Data Factory, możesz dowiedzieć się więcej o tych usługach i sposobach ich współdziałania. Jeśli wiesz coś o usługach i projektujesz/opracowujesz rozwiązanie, możesz skupić się na sekcji architektury artykułu. Jeśli tworzysz prototyp lub rozwiązanie, możesz spróbować wykonać instrukcje krok po kroku w przewodniku. Zachęcamy do komentarzy dotyczących tej zawartości i sposobu jej używania.

Najpierw przyjrzyjmy się, jak Usługa Data Factory i usługi Batch mogą pomóc w przetwarzaniu dużych zestawów danych w chmurze.     


## <a name="why-azure-batch"></a>Dlaczego Azure Batch?
 Za pomocą usługi Batch można wydajnie uruchamiać aplikacje równoległe i o wysokiej wydajności obliczeniowe (HPC) w chmurze. Jest to usługa platformy, która planuje uruchamianie zadań intensywnie korzystających z obliczeń w zarządzanej kolekcji maszyn wirtualnych. Umożliwia automatyczne skalowanie zasobów obliczeniowych w celu spełnienia potrzeb zadań.

W usłudze Batch definiuje się zasoby obliczeniowe Azure do wykonywania aplikacji równolegle i na dużą skalę. Można uruchamiać zadania na żądanie lub zaplanowane. Nie ma potrzeby ręcznego tworzenia i konfigurowania klastra HPC, indywidualnych maszyn wirtualnych, sieci wirtualnych ani złożonej infrastruktury zadań i planowania w celu zarządzania nimi.

 Jeśli nie znasz programu Batch, następujące artykuły ułatwiają zrozumienie architektury/implementacji rozwiązania opisanego w tym artykule:   

* [Podstawowe informacje o usłudze Batch](../../azure-sql/database/sql-database-paas-overview.md)
* [Omówienie funkcji usługi Batch](../../batch/batch-service-workflow-features.md)

Opcjonalnie, aby dowiedzieć się więcej o usłudze Batch, zapoznaj [się z dokumentacją zbiorczą](../../batch/index.yml).

## <a name="why-azure-data-factory"></a>Dlaczego Azure Data Factory?
Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Za pomocą Data Factory można tworzyć potoki danych zarządzanych, które przenoszą dane ze sklepów lokalnych i magazynów danych w chmurze do scentralizowanego magazynu danych. Przykładem jest usługa Azure Blob Storage. Data Factory służy do przetwarzania/przekształcania danych przy użyciu usług takich jak Azure HDInsight i Azure Machine Learning. Można również zaplanować uruchamianie potoków danych zgodnie z harmonogramem (na przykład co godzinę, codziennie i co tydzień). Możesz szybko monitorować potoki i zarządzać nimi, aby identyfikować problemy i podejmować działania.

  Jeśli nie znasz Data Factory, następujące artykuły ułatwiają zrozumienie architektury/implementacji rozwiązania opisanego w tym artykule:  

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Tworzenie pierwszego potoku danych](data-factory-build-your-first-pipeline.md)   

Opcjonalnie, aby dowiedzieć się więcej na temat Data Factory, zapoznaj [się z dokumentacją Data Factory](/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory i Partia zadań
Data Factory obejmuje wbudowane działania. Na przykład działanie kopiowania służy do kopiowania/przenoszenia danych ze źródłowego magazynu danych do docelowego magazynu danych. Działanie Hive służy do przetwarzania danych przy użyciu klastrów Hadoop (HDInsight) na platformie Azure. Listę obsługiwanych działań transformacji można znaleźć w temacie [działania przekształcania danych](data-factory-data-transformation-activities.md).

Można również tworzyć niestandardowe działania platformy .NET, aby przenosić lub przetwarzać dane przy użyciu własnej logiki. Te działania można uruchamiać w klastrze usługi HDInsight lub w puli usługi Batch maszyn wirtualnych. Korzystając z programu Batch, można skonfigurować pulę do automatycznego skalowania (dodawać lub usuwać maszyny wirtualne na podstawie obciążenia) na podstawie podanych przez Ciebie formuły.     

## <a name="architecture-of-a-sample-solution"></a>Architektura przykładowego rozwiązania
  Architektura opisana w tym artykule dotyczy prostego rozwiązania. Ma również zastosowanie do złożonych scenariuszy, takich jak Modelowanie ryzyka przez usługi finansowe, przetwarzanie i renderowanie obrazów oraz analiza genomiki.

Na diagramie przedstawiono sposób, w jaki Data Factory organizować przenoszenie i przetwarzanie danych. Pokazuje również, jak przetwarzanie wsadowe przetwarza dane w sposób równoległy. Pobierz i wydrukuj diagram, aby uzyskać łatwą referencję (rozmiar 11 x 17 cali lub A3). Aby uzyskać dostęp do diagramu, aby można było go wydrukować, zobacz [HPC i aranżacja danych przy użyciu usługi Batch i Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram przetwarzania danych na dużą skalę](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Poniższa lista zawiera podstawowe kroki procesu. Rozwiązanie zawiera kod i wyjaśnienia w celu utworzenia kompleksowego rozwiązania.

* **Skonfiguruj zadanie wsadowe w puli węzłów obliczeniowych (maszyn wirtualnych).** Można określić liczbę węzłów i rozmiar każdego węzła.

* **Utwórz wystąpienie Data Factory** , które jest skonfigurowane przy użyciu jednostek, które reprezentują magazyn obiektów blob, usługi Batch COMPUTE, dane wejściowe/wyjściowe oraz przepływy pracy/potoku z działaniami, które przechodzą i przekształcają dane.

* **Utwórz niestandardowe działanie platformy .NET w potoku Data Factory.** Działanie to kod użytkownika, który jest uruchamiany w puli wsadowej.

* **Przechowywanie dużych ilości danych wejściowych jako obiektów BLOB w usłudze Azure Storage.** Dane są podzielone na wycinki logiczne (zazwyczaj przez czas).

* **Data Factory kopiuje dane, które są przetwarzane równolegle** do lokalizacji pomocniczej.

* **Data Factory uruchamia działanie niestandardowe przy użyciu puli przydzielonej przez partię.** Data Factory mogą uruchamiać działania współbieżnie. Każde działanie przetwarza wycinek danych. Wyniki są przechowywane w magazynie.

* **Data Factory przenosi końcowe wyniki do trzeciej lokalizacji** w celu dystrybucji za pośrednictwem aplikacji lub do dalszej obróbki przez inne narzędzia.

## <a name="implementation-of-the-sample-solution"></a>Implementacja przykładowego rozwiązania
Przykładowe rozwiązanie jest celowo proste. Zaprojektowano w celu pokazania sposobu używania Data Factory i partii do przetwarzania zestawów danych. Rozwiązanie zlicza liczbę wystąpień wyszukiwanego terminu "Microsoft" w plikach wejściowych, które są zorganizowane w szeregach czasowych. Następnie zwraca liczbę do plików wyjściowych.

**Czas:** Jeśli znasz podstawowe informacje dotyczące platformy Azure, Data Factory i usługi Batch i zostały spełnione następujące wymagania wstępne, to rozwiązanie zajmie od 1 do dwóch godzin.

### <a name="prerequisites"></a>Wymagania wstępne
#### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz subskrypcji platformy Azure, możesz szybko utworzyć bezpłatne konto próbne. Aby uzyskać więcej informacji, zobacz [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Konto magazynu Azure
Do przechowywania danych w tym samouczku służy konto magazynu. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md). Przykładowe rozwiązanie używa magazynu obiektów BLOB.

#### <a name="azure-batch-account"></a>Konto usługi Azure Batch
Utwórz konto usługi Batch przy użyciu [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Batch i zarządzanie](../../batch/batch-account-create-portal.md)nim. Zanotuj nazwę konta i klucz konta w usłudze Batch. Możesz również użyć polecenia cmdlet [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) , aby utworzyć konto w usłudze Batch. Aby uzyskać instrukcje dotyczące korzystania z tego polecenia cmdlet, zobacz [Rozpoczynanie pracy z poleceniami cmdlet programu PowerShell usługi Batch](../../batch/batch-powershell-cmdlets-get-started.md).

Przykładowe rozwiązanie używa partii (pośrednio za pośrednictwem potoku fabryki danych) do równoległego przetwarzania danych w puli węzłów obliczeniowych (zarządzanej kolekcji maszyn wirtualnych).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch pulę maszyn wirtualnych
Utwórz pulę wsadową z co najmniej dwoma węzłami obliczeniowymi.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Przeglądaj** w menu po lewej stronie, a następnie wybierz pozycję konta w usłudze **Batch**.

1. Wybierz konto w usłudze Batch, aby otworzyć blok konto w usłudze **Batch** .

1. Wybierz kafelek **Pule** .

1. W bloku **Pule** wybierz przycisk **Dodaj** na pasku narzędzi, aby dodać pulę.

   a. Wprowadź identyfikator puli (**Identyfikator puli**). Zanotuj identyfikator puli. Jest ona potrzebna podczas tworzenia rozwiązania do fabryki danych.

   b. Określ **system Windows Server 2012 R2** dla ustawienia **rodziny systemów operacyjnych** .

   c. Wybierz **warstwę cenową węzła**.

   d. Wprowadź **2** jako wartość ustawienia **dedykowanego elementu docelowego** .

   e. Wprowadź **2** jako wartość ustawienia **Maksymalna liczba zadań na węzeł** .

   f. Wybierz **przycisk OK** , aby utworzyć pulę.

#### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Aby sprawdzić i zmienić dane w projektach magazynu, należy użyć [Eksplorator usługi Azure Storage 6](https://azurestorageexplorer.codeplex.com/) lub [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (z oprogramowania ClumsyLeaf). Możesz również sprawdzić i zmienić dane w dziennikach aplikacji hostowanych w chmurze.

1. Utwórz kontener **o nazwie "Moja kontener"** z dostępem prywatnym (brak dostępu anonimowego).

1. Jeśli używasz CloudXplorer, Utwórz foldery i podfoldery o następującej strukturze:

   ![Struktura folderów i podfolderów](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` i `outputfolder` to foldery najwyższego poziomu w programie `mycontainer` . `inputfolder`Folder zawiera podfoldery z sygnaturami daty i godziny (rrrr-mm-dd-hh).

   Jeśli używasz Eksplorator usługi Storage, w następnym kroku przekażesz pliki o następujących nazwach: `inputfolder/2015-11-16-00/file.txt` , `inputfolder/2015-11-16-01/file.txt` i tak dalej. Ten krok powoduje automatyczne utworzenie folderów.

1. Utwórz plik tekstowy **file.txt** na swojej maszynie przy użyciu zawartości zawierającej słowo kluczowe **Microsoft**. Przykładem jest "testowanie niestandardowego działania testowego firmy Microsoft testowego firmy Microsoft".

1. Przekaż plik do następujących folderów wejściowych w usłudze BLOB Storage:

   ![Foldery wejściowe](./media/data-factory-data-processing-using-batch/image4.png)

   Jeśli używasz Eksplorator usługi Storage, Przekaż plik **file.txt** do **kontenera**. Wybierz pozycję **Kopiuj** na pasku narzędzi, aby utworzyć kopię obiektu BLOB. W oknie dialogowym **Kopiowanie obiektu BLOB** Zmień **nazwę docelowego obiektu BLOB** na `inputfolder/2015-11-16-00/file.txt` . Powtórz ten krok, aby utworzyć `inputfolder/2015-11-16-01/file.txt` , `inputfolder/2015-11-16-02/file.txt` , `inputfolder/2015-11-16-03/file.txt` , `inputfolder/2015-11-16-04/file.txt` i tak dalej. Ta akcja powoduje automatyczne utworzenie folderów.

1. Utwórz inny kontener o nazwie `customactivitycontainer` . Przekaż niestandardowy plik zip działania do tego kontenera.

#### <a name="visual-studio"></a>Visual Studio
Zainstaluj program Visual Studio 2012 lub nowszy, aby utworzyć niestandardową aktywność wsadową, która będzie używana w rozwiązaniu fabryki danych.

### <a name="high-level-steps-to-create-the-solution"></a>Ogólne kroki tworzenia rozwiązania
1. Utwórz niestandardowe działanie, które zawiera logikę przetwarzania danych.

1. Utwórz fabrykę danych korzystającą z działania niestandardowego.

### <a name="create-the-custom-activity"></a>Tworzenie działania niestandardowego
Niestandardowa aktywność fabryki danych to serce tego przykładowego rozwiązania. Przykładowe rozwiązanie używa programu Batch do uruchomienia działania niestandardowego. Aby uzyskać informacje na temat sposobu tworzenia niestandardowych działań i używania ich w potokach usługi Data Factory, zobacz [Korzystanie z działań niestandardowych w potoku usługi Data Factory](data-factory-use-custom-activities.md).

Aby utworzyć niestandardowe działanie platformy .NET, którego można użyć w potoku fabryki danych, należy utworzyć projekt biblioteki klas .NET z klasą, która implementuje interfejs IDotNetActivity. Ten interfejs ma tylko jedną metodę: Execute. Oto podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoda zawiera kilka najważniejszych składników, które należy zrozumieć:

* Metoda przyjmuje cztery parametry:

  * **linkedServices**. Ten parametr jest wyliczalną listą połączonych usług, które łączą źródła danych wejściowych/wyjściowych (np. Magazyn obiektów BLOB) do fabryki danych. W tym przykładzie istnieje tylko jedna połączona usługa typu magazyn platformy Azure używana dla danych wejściowych i wyjściowych.
  * **zestawy danych**. Ten parametr jest wyliczalną listą zestawów danych. Tego parametru można użyć, aby uzyskać lokalizacje i schematy zdefiniowane za pomocą wejściowych i wyjściowych zestawów danych.
  * **działanie**. Ten parametr reprezentuje bieżącą jednostkę obliczeniową. W tym przypadku jest to usługa Batch.
  * **Rejestrator**. Możesz użyć rejestratora, aby napisać Komentarze debugowania, które są widoczne jako dziennik "użytkownik" dla potoku.
* Metoda zwraca słownik, który może służyć do łańcucha działań niestandardowych w przyszłości. Ta funkcja nie została jeszcze zaimplementowana, dlatego po prostu zwróć pusty słownik z metody.

#### <a name="procedure-create-the-custom-activity"></a>Procedura: tworzenie działania niestandardowego
1. Utwórz projekt biblioteki klas .NET w programie Visual Studio.

   a. Uruchom program Visual Studio 2012/2013/2015.

   b. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

   c. Rozwiń węzeł **Szablony**, a następnie wybierz pozycję **Visual C \#**. W tym instruktażu należy użyć \# języka C, ale do opracowania niestandardowego działania można użyć dowolnych języków platformy .NET.

   d. Wybierz pozycję **Biblioteka klas** z listy typów projektów po prawej stronie.

   e. Wprowadź **wartość** w polu **Nazwa**.

   f. Wybierz pozycję **C: \\ ADF** dla **lokalizacji**. Utwórz plik **ADF** folderu, jeśli nie istnieje.

   przykład Wybierz przycisk **OK**, aby utworzyć projekt.

1. Wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.

1. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować pakiet Microsoft. Azure. Management. datafactors:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Zaimportuj pakiet NuGet **usługi Azure Storage** do projektu. Ten pakiet jest wymagany, ponieważ w tym przykładzie używasz interfejsu API Blob Storage:

    ```powershell
    Install-Package Az.Storage
    ```
1. Dodaj następujące dyrektywy using do pliku źródłowego w projekcie:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Zmień nazwę przestrzeni nazw na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Zmień nazwę **klasy na IDotNetActivity** i utwórz ją z interfejsu **IDotNetActivity** , jak pokazano poniżej:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Zaimplementuj (Dodaj) metodę **Execute** interfejsu **IDotNetActivity** do klasy. **MyDotNetActivity** Skopiuj następujący przykładowy kod do metody. Aby uzyskać wyjaśnienie logiki używanej w tej metodzie, zobacz sekcję [Execute Method](#execute-method) .

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Dodaj następujące metody pomocnika do klasy. Metody te są wywoływane przez metodę **Execute** . Najważniejszym, Metoda **Oblicz** izoluje kod, który iteruje przez każdy obiekt BLOB.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Metoda GetFolderPath zwraca ścieżkę do folderu, do którego wskazuje zestaw danych, i Metoda GetFilename zwraca nazwę obiektu blob/pliku, do którego wskazuje zestaw danych.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoda Oblicz oblicza liczbę wystąpień słowa kluczowego "Microsoft" w plikach wejściowych (obiekty blob w folderze). Termin wyszukiwania "Microsoft" jest zakodowany w kodzie.

1. Kompiluj projekt. Wybierz z menu pozycję **kompilacja** , a następnie wybierz pozycję **Kompiluj rozwiązanie**.

1. Uruchom Eksploratora Windows i przejdź do folderu **bin \\ Debug** lub **bin \\ Release** . Wybór folderu zależy od typu kompilacji.

1. Utwórz plik zip **MyDotNetActivity.zip** , który zawiera wszystkie pliki binarne w folderze **\\ \\ debugowania bin** . Może być konieczne dołączenie. plik **PDB** , aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, który spowodował problem w przypadku wystąpienia błędu.

   ![Lista folderów bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Przekaż **MyDotNetActivity.zip** jako obiekt BLOB do kontenera obiektów BLOB `customactivitycontainer` w magazynie obiektów blob, do którego StorageLinkedService połączona usługa w ADFTutorialDataFactory. Utwórz kontener obiektów blob, `customactivitycontainer` Jeśli jeszcze nie istnieje.

#### <a name="execute-method"></a>Execute — Metoda
Ta sekcja zawiera więcej szczegółów o kodzie w metodzie Execute.

1. Elementy członkowskie do iterowania za pomocą kolekcji wejściowej znajdują się w przestrzeni nazw [Microsoft. windowsazure. Storage. blob](/java/api/com.microsoft.azure.storage.blob) . Aby wykonać iterację kolekcji obiektów blob, należy użyć klasy **BlobContinuationToken** . W zasadzie należy użyć pętli do-while z tokenem jako mechanizmem do kończenia pętli. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi BLOB Storage z platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md). Pętla podstawowa jest pokazana tutaj:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Aby uzyskać więcej informacji, zobacz dokumentację metody [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) .

1. Kod służący do pracy przez zestaw obiektów BLOB logicznie przechodzi w obrębie pętli do-while. W metodzie **Execute** pętla do while przekazuje listę obiektów BLOB do metody o nazwie **Oblicz**. Metoda zwraca zmienną ciągu o nazwie **Output** , która jest wynikiem iteracji przez wszystkie obiekty blob w segmencie.

   Zwraca liczbę wystąpień wyszukiwanego terminu "Microsoft" w obiekcie blob przekazaną do metody **Oblicz** .

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Po zakończeniu metody **obliczeń** należy ją zapisać w nowym obiekcie blob. Dla każdego zestawu obiektów BLOB przetworzony nowy obiekt BLOB może być zapisany z wynikami. Aby zapisać do nowego obiektu BLOB, najpierw Znajdź wyjściowy zestaw danych.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kod wywołuje również metodę pomocnika **GetFolderPath** , aby pobrać ścieżkę folderu (nazwę kontenera magazynu).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoda GetFolderPath rzutuje obiekt DataSet na AzureBlobDataSet, który ma właściwość o nazwie FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Kod wywołuje metodę **GetFileName** , aby pobrać nazwę pliku (nazwę obiektu BLOB). Kod jest podobny do poprzedniego kodu, który został użyty do pobrania ścieżki folderu.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Nazwa pliku jest zapisywana przez utworzenie obiektu identyfikatora URI. Konstruktor URI używa właściwości **BlobEndpoint** , aby zwrócić nazwę kontenera. Ścieżka folderu i nazwa pliku są dodawane do konstruowania wyjściowego identyfikatora URI obiektu BLOB.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Po napisaniu nazwy pliku można napisać ciąg wyjściowy z metody **obliczeń** do nowego obiektu BLOB:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Tworzenie fabryki danych
W sekcji [Tworzenie niestandardowego działania](#create-the-custom-activity) utworzono działanie niestandardowe i przekazano plik zip z danymi binarnymi i plikiem PDB do kontenera obiektów BLOB. W tej sekcji utworzysz fabrykę danych z potokiem korzystającym z działania niestandardowego.

Wejściowy zestaw danych dla działania niestandardowego reprezentuje obiekty blob (pliki) w folderze wejściowym ( `mycontainer\\inputfolder` ) w usłudze BLOB Storage. Wyjściowy zestaw danych dla działania reprezentuje wyjściowe obiekty blob w folderze wyjściowym ( `mycontainer\\outputfolder` ) w usłudze BLOB Storage.

Upuść jeden lub więcej plików do folderów wejściowych:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Na przykład Porzuć jeden plik (file.txt) z następującą zawartością do każdego z folderów:

```
test custom activity Microsoft test custom activity Microsoft
```

Każdy folder wejściowy odnosi się do wycinka w fabryce danych, nawet jeśli folder ma dwa lub więcej plików. Gdy każdy wycinek jest przetwarzany przez potok, działanie niestandardowe wykonuje iterację przez wszystkie obiekty blob w folderze wejściowym dla tego wycinka.

Zobaczysz pięć plików wyjściowych o tej samej zawartości. Na przykład plik wyjściowy z przetwarzania pliku w folderze 2015-11-16-00 ma następującą zawartość:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

W przypadku porzucenia wielu plików (file.txt, file2.txt, file3.txt) z tą samą zawartością w folderze wejściowym w pliku wyjściowym zostanie wyświetlona następująca zawartość. Każdy folder (2015-11-16-00 itd.) odnosi się do wycinka w tym przykładzie, mimo że folder ma wiele plików wejściowych.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Plik wyjściowy ma trzy wiersze, po jednym dla każdego pliku wejściowego (BLOB) w folderze skojarzonym z wycinkem (2015-11-16-00).

Zadanie jest tworzone dla każdego uruchomienia działania. W tym przykładzie w potoku jest tylko jedno działanie. Gdy wycinek jest przetwarzany przez potok, działanie niestandardowe działa w usłudze Batch w celu przetworzenia wycinka. Ponieważ istnieje pięć wycinków (każdy wycinek może mieć wiele obiektów blob lub plik), w usłudze Batch tworzone są pięć zadań. Gdy zadanie jest uruchamiane w usłudze Batch, jest to działanie niestandardowe, które jest uruchomione.

Poniższy przewodnik zawiera dodatkowe szczegóły.

#### <a name="step-1-create-the-data-factory"></a>Krok 1. Tworzenie fabryki danych
1. Po zalogowaniu się do [Azure Portal](https://portal.azure.com/)wykonaj następujące czynności:

   a. Wybierz pozycję **Nowy** w menu po lewej stronie.

   b. Wybierz pozycję **dane + analiza** w **nowym** bloku.

   c. Wybierz pozycję **Data Factory** w bloku **Analiza danych** .

1. W bloku **Nowa fabryka danych** wprowadź **CustomActivityFactory** w polu Nazwa. Nazwa fabryki danych musi być globalnie unikatowa. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych CustomActivityFactory jest niedostępna", Zmień nazwę fabryki danych. Na przykład użyj yournameCustomActivityFactory i ponownie utwórz fabrykę danych.

1. Wybierz opcję **Nazwa grupy zasobów** i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.

1. Sprawdź, czy subskrypcja i region, w którym ma zostać utworzona Fabryka danych, są poprawne.

1. Wybierz pozycję **Utwórz** w bloku **Nowa fabryka danych** .

1. Fabryka danych jest tworzona na pulpicie nawigacyjnym portalu.

1. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych.

   ![Strona fabryki danych](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Krok 2. Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych. W tym kroku połączysz konto magazynu i konto wsadowe z fabryką danych.

#### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Wybierz kafelek **Tworzenie i wdrażanie** w bloku **Fabryka danych** dla **CustomActivityFactory**. Zostanie wyświetlony Edytor Data Factory.

1. Wybierz pozycję **nowy magazyn danych** na pasku poleceń, a następnie wybierz pozycję **Azure Storage.** Zostanie wyświetlony skrypt JSON, który służy do tworzenia połączonej usługi Storage w edytorze.

   ![Nowy magazyn danych](./media/data-factory-data-processing-using-batch/image7.png)

1. Zastąp **nazwę konta** nazwą konta magazynu. Zastąp **klucz konta** kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

   ![Wdrażanie](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Tworzenie połączonej usługi Azure Batch
W tym kroku utworzysz połączoną usługę dla konta usługi Batch, która jest używana do uruchamiania niestandardowego działania usługi Data Factory.

1. Wybierz pozycję **nowe obliczenie** na pasku poleceń, a następnie wybierz pozycję **Azure Batch.** Zostanie wyświetlony skrypt JSON, który służy do tworzenia połączonej usługi Batch w edytorze.

1. W skrypcie JSON:

   a. Zastąp **nazwę konta** nazwą konta w usłudze Batch.

   b. Zastąp **klucz dostępu** kluczem dostępu do konta w usłudze Batch.

   c. Wprowadź identyfikator puli dla właściwości **PoolName** . Dla tej właściwości można określić nazwę puli lub Identyfikator puli.

   d. Wprowadź identyfikator URI usługi Batch dla właściwości JSON **batchUri** .

      > [!IMPORTANT]
      > Adres URL w bloku **konta usługi Batch** ma następujący format: \<accountname\> . \<region\> . batch.azure.com. Dla właściwości **batchUri** w skrypcie JSON należy usunąć A88 "AccountName". * * w adresie URL. Może to być na przykład `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Blok konta w usłudze Batch](./media/data-factory-data-processing-using-batch/image9.png)

      Dla właściwości **PoolName** można także określić identyfikator puli, a nie nazwę puli.

      > [!NOTE]
      > Usługa Data Factory nie obsługuje opcji na żądanie dla usługi Batch, ponieważ jest ona w usłudze HDInsight. W fabryce danych można używać tylko własnej puli partii.
      >
      >

   e. Określ **StorageLinkedService** dla właściwości **linkedServiceName** . Ta połączona usługa została utworzona w poprzednim kroku. Ten magazyn jest używany jako obszar przejściowy dla plików i dzienników.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="step-3-create-datasets"></a>Krok 3. Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące dane wejściowe i wyjściowe.

#### <a name="create-the-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W edytorze Data Factory wybierz przycisk **Nowy zestaw danych** na pasku narzędzi. Z listy rozwijanej wybierz pozycję **Azure Blob Storage** .

1. Zastąp skrypt JSON w okienku po prawej stronie następującym fragmentem kodu JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Potok można utworzyć w dalszej części tego przewodnika z upływem czasu rozpoczęcia 2015-11-16T00:00:00Z i czasu zakończenia 2015-11-16T05:00:00Z. Zaplanowano tworzenie danych co godzinę, więc istnieje pięć wycinków danych wejściowych/wyjściowych (od **00**: 00:00 – \> **05**: 00:00).

    **Częstotliwość** i **Interwał** dla wejściowego zestawu danych są ustawione na wartość **Hour** i **1**, co oznacza, że wycinek danych wejściowych jest dostępny co godzinę.

    Godzina rozpoczęcia każdego wycinka jest reprezentowana przez zmienną systemową **parametru slicestart** w poprzednim FRAGMENCIE kodu JSON. Oto godziny rozpoczęcia każdego wycinka.

    | **Cinek** | **Godzina rozpoczęcia**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**: 00:00 |
    | 2         | 2015-11-16T **01**: 00:00 |
    | 3         | 2015-11-16T **02**: 00:00 |
    | 4         | 2015-11-16T **03**: 00:00 |
    | 5         | 2015-11-16T **04**: 00:00 |

    **FolderPath** jest obliczany przy użyciu części Year, month, Day i Hour czasu rozpoczęcia wycinka (**parametru slicestart**). Oto jak folder wejściowy jest mapowany na wycinek.

    | **Cinek** | **Godzina rozpoczęcia**          | **Folder wejściowy**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 –**00** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 –**01** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16 –**02** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 –**03** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16 –**04** |

1. Wybierz pozycję **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć tabelę **InputDataset** .

#### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
W tym kroku utworzysz kolejny zestaw danych typu AzureBlob do reprezentowania danych wyjściowych.

1. W edytorze Data Factory wybierz przycisk **Nowy zestaw danych** na pasku narzędzi. Z listy rozwijanej wybierz pozycję **Azure Blob Storage** .

1. Zastąp skrypt JSON w okienku po prawej stronie następującym fragmentem kodu JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Wyjściowy obiekt BLOB/plik jest generowany dla każdego wycinka danych wejściowych. Poniżej przedstawiono sposób, w jaki plik wyjściowy jest nazwany dla każdego wycinka. Wszystkie pliki wyjściowe są generowane w jednym folderze wyjściowym, `mycontainer\\outputfolder` .

    | **Cinek** | **Godzina rozpoczęcia**          | **Plik wyjściowy**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 —**00.txt** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 —**01.txt** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16 —**02.txt** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 —**03.txt** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16 —**04.txt** |

    Należy pamiętać, że wszystkie pliki w folderze wejściowym (na przykład 2015-11-16-00) są częścią wycinka o godzinie rozpoczęcia 2015-11-16-00. Po przetworzeniu tego wycinka działanie niestandardowe skanuje każdy plik i tworzy wiersz w pliku wyjściowym z liczbą wystąpień wyszukiwanego terminu "Microsoft". Jeśli w folderze 2015-11-16-00 znajdują się trzy pliki, w pliku wyjściowym znajdują się trzy wiersze 2015-11-16-00.txt.

1. Wybierz pozycję **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Krok 4. Tworzenie i uruchamianie potoku z niestandardowym działaniem
W tym kroku utworzysz potok z jednym działaniem, utworzonym wcześniej działaniem niestandardowym.

> [!IMPORTANT]
> Jeśli **file.txt** nie zostały przekazane do folderów wejściowych w kontenerze obiektów blob, zrób to przed utworzeniem potoku. Właściwość **IsPaused** została ustawiona na false w kodzie JSON potoku, więc potok jest uruchamiany natychmiast, ponieważ data **rozpoczęcia** przypada w przeszłości.
>
>

1. W edytorze Data Factory wybierz pozycję **Nowy potok** na pasku poleceń. Jeśli polecenie nie jest widoczne, wybierz symbol wielokropka, aby go wyświetlić.

1. Zastąp skrypt JSON w okienku po prawej stronie następującym fragmentem kodu JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Pamiętaj o następujących kwestiach:

   * W potoku jest tylko jedno działanie i jest ono typu **dotnet**.
   * Nazwa **AssemblyName** jest ustawiana na nazwę **MyDotNetActivity.dll** biblioteki DLL.
   * **Punkt wejścia** jest ustawiony na **MyDotNetActivityNS..** Zasadniczo \<namespace\> .\<classname\> w kodzie.
   * **PackageLinkedService** jest ustawiona na **StorageLinkedService**, która wskazuje na magazyn obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz innych kont magazynu dla plików wejściowych/wyjściowych i pliku zip działania niestandardowego, musisz utworzyć kolejną połączoną usługę Storage. W tym artykule przyjęto założenie, że jest używane to samo konto magazynu.
   * **PackageFile** jest ustawiona na **customactivitycontainer/MyDotNetActivity.zip**. Jest w formacie \<containerforthezip\> / \<nameofthezip.zip\> .
   * Działanie niestandardowe przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Właściwość **linkedServiceName** niestandardowego działania wskazuje wartość **AzureBatchLinkedService**, która informuje Data Factory, że działanie niestandardowe musi być uruchamiane w usłudze Batch.
   * Ustawienie **współbieżności** jest ważne. Jeśli zostanie użyta wartość domyślna, która jest równa 1, nawet jeśli w puli wsadowej znajdują się co najmniej dwa węzły obliczeniowe, wycinki są przetwarzane jeden po drugim. W związku z tym nie jest możliwe korzystanie z możliwości przetwarzania równoległego w usłudze Batch. Jeśli ustawisz **współbieżność** na wyższą wartość, powiedzmy 2, co oznacza, że dwa wycinki (odnoszą się do dwóch zadań w partii) mogą być przetwarzane w tym samym czasie. W takim przypadku wykorzystywane są zarówno maszyny wirtualne w puli usługi Batch. Ustaw odpowiednio Właściwość współbieżności.
   * Tylko jedno zadanie (wycink) jest wykonywane domyślnie na maszynie wirtualnej. Domyślnie **Maksymalna liczba zadań na maszynę wirtualną** jest ustawiona na 1 dla puli usługi Batch. W ramach wymagań wstępnych utworzono pulę z tą właściwością ustawioną na 2. W związku z tym dwa wycinki fabryki danych można uruchomić na maszynie wirtualnej w tym samym czasie.
     - Właściwość **IsPaused** jest domyślnie ustawiona na false. Potok jest uruchamiany natychmiast w tym przykładzie, ponieważ wycinki zaczynają się w przeszłości. Możesz ustawić tę właściwość na **wartość true** , aby wstrzymać potok i ustawić z powrotem **wartość false** , aby ponownie uruchomić system.
     -   Czasy **rozpoczęcia** i **zakończenia** są pięć godzin od siebie. Wycinki są generowane co godzinę, więc pięć wycinków jest generowanych przez potok.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć potok.

#### <a name="step-5-test-the-pipeline"></a>Krok 5. Testowanie potoku
Ten krok polega na przetestowaniu potoku przez upuszczenie plików do folderów wejściowych. Zacznij od przetestowania potoku przy użyciu jednego pliku dla każdego folderu wejściowego.

1. W bloku **Fabryka danych** w Azure Portal wybierz pozycję **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. W widoku **diagramu** kliknij dwukrotnie wejściowy zestaw danych **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. Blok **InputDataset** jest wyświetlany ze wszystkimi pięcioma wycinkami gotowymi. Zwróć uwagę na **czas rozpoczęcia wycinka** i **godzinę zakończenia wycinka** dla każdego wycinka.

   ![Czasy rozpoczęcia i zakończenia wycinka danych wejściowych](./media/data-factory-data-processing-using-batch/image12.png)

1. W widoku **diagramu** wybierz pozycję **OutputDataset**.

1. Pięć wycinków wyjściowych pojawia się w stanie **gotowe** , jeśli zostały utworzone.

   ![Czas rozpoczęcia i zakończenia wycinka danych wyjściowych](./media/data-factory-data-processing-using-batch/image13.png)

1. Użyj portalu, aby wyświetlić zadania skojarzone z wycinkami i sprawdzić, w jaki sposób ma być uruchomiona maszyna wirtualna. Aby uzyskać więcej informacji, zobacz sekcję [Data Factory i integrację usługi Batch](#data-factory-and-batch-integration) .

1. Pliki wyjściowe są wyświetlane w obszarze w obszarze programu `mycontainer` `outputfolder` w magazynie obiektów BLOB.

   ![Pliki wyjściowe w magazynie](./media/data-factory-data-processing-using-batch/image15.png)

   Na liście znajduje się pięć plików wyjściowych, po jednej dla każdego wycinka danych wejściowych. Każdy plik wyjściowy ma zawartość podobną do następującej:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Na poniższym diagramie przedstawiono sposób mapowania wycinków fabryki danych na zadania w usłudze Batch. W tym przykładzie plasterek ma tylko jeden przebieg.

   ![Diagram mapowania wycinków](./media/data-factory-data-processing-using-batch/image16.png)

1. Teraz spróbuj użyć wielu plików w folderze. Utwórz pliki **file2.txt**, **file3.txt**, **file4.txt** i **file5.txt** z tą samą zawartością jak w file.txt w folderze **2015-11-06-01**.

1. W folderze wyjściowym Usuń plik wyjściowy **2015-11-16-01.txt**.

1. W bloku **OutputDataset** kliknij prawym przyciskiem myszy wycinek z ustawionym **czasem rozpoczęcia wycinka** na **11/16/2015 01:00:00 am**. Wybierz pozycję **Uruchom** , aby ponownie uruchomić/przetworzyć wycinek. Plasterek ma teraz pięć plików, a nie jeden plik.

    ![Uruchom](./media/data-factory-data-processing-using-batch/image17.png)

1. Po uruchomieniu wycinka, gdy jego stan jest **gotowy**, sprawdź zawartość pliku wyjściowego dla tego wycinka (**2015-11-16-01.txt**). Plik wyjściowy jest wyświetlany w obszarze `mycontainer` w programie `outputfolder` w magazynie obiektów BLOB. Powinien istnieć wiersz dla każdego pliku wycinka.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Jeśli plik wyjściowy nie został usunięty 2015-11-16-01.txt przed wypróbowaniem pięciu plików wejściowych zostanie wyświetlony jeden wiersz z poprzedniego wycinka i pięć wierszy z bieżącego wycinka. Domyślnie zawartość jest dołączana do pliku wyjściowego, jeśli już istnieje.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integracja Data Factory i partii
Usługa Data Factory tworzy zadanie w usłudze Batch z nazwą `adf-poolname:job-xxx` .

![Zadania wsadowe](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Zadanie w zadaniu jest tworzone dla każdego uruchomienia wycinka. Jeśli 10 wycinków jest gotowych do przetworzenia, w zadaniu zostaną utworzone 10 zadań. W przypadku wielu węzłów obliczeniowych w puli można równolegle korzystać z więcej niż jednego wycinka. Jeśli maksymalna liczba zadań na węzeł obliczeniowy jest ustawiona na więcej niż jeden, można uruchomić więcej niż jeden wycinek w ramach tego samego obliczenia.

W tym przykładzie istnieje pięć wycinków, więc w usłudze Batch znajdują się pięć zadań. Dzięki funkcji **współbieżności** ustawionej na **5** w kodzie JSON potoku w fabryce danych i **Maksymalna liczba zadań na maszynę wirtualną** ustawioną na **2** w puli wsadowej z **2** maszynami wirtualnymi zadania są wykonywane szybko. (Sprawdź godziny rozpoczęcia i zakończenia zadań).

Użyj portalu, aby wyświetlić zadanie wsadowe i jego zadania, które są skojarzone z wycinkami, i sprawdź, w jaki sposób maszyna wirtualna jest uruchamiana dla każdego wycinka.

![Zadania zadań wsadowych](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Debugowanie potoku
Debugowanie składa się z kilku podstawowych technik.

1. Jeśli wycinek wejściowy nie jest ustawiony na wartość **gotowe**, upewnij się, że struktura folderu wejściowego jest poprawna i że file.txt istnieje w folderach wejściowych.

   ![Struktura folderu wejściowego](./media/data-factory-data-processing-using-batch/image3.png)

1. W metodzie **Execute** niestandardowego działania Użyj obiektu **IActivityLogger** , aby rejestrować informacje pomagające w rozwiązywaniu problemów. Zarejestrowane komunikaty pojawiają się w \_ pliku dziennika użytkownika 0. log.

   W bloku **OutputDataset** Wybierz wycinek, aby wyświetlić blok **wycinka danych** dla tego wycinka. W obszarze **uruchomienia działania** zobaczysz jedno uruchomienie działania dla wycinka. Jeśli wybierzesz pozycję **Uruchom** na pasku poleceń, możesz uruchomić kolejne uruchomienie działania dla tego samego wycinka.

   Po wybraniu uruchomienia działania zostanie wyświetlony blok **szczegóły uruchomienia działania** z listą plików dziennika. W \_ pliku dziennika użytkownika 0. log są wyświetlane zarejestrowane komunikaty. Gdy wystąpi błąd, zobaczysz trzy uruchomienia działania, ponieważ liczba ponownych prób jest ustawiona na 3 w formacie JSON potoku/działania. Po wybraniu uruchomienia działania zobaczysz pliki dziennika, które można przejrzeć, aby rozwiązać problem.

   ![OutputDataset i bloki wycinków danych](./media/data-factory-data-processing-using-batch/image18.png)

   Na liście plików dziennika wybierz pozycję **User-0. log**. W prawym panelu pojawiają się wyniki przy użyciu metody **IActivityLogger. Write** .

   ![Blok szczegółów uruchamiania działania](./media/data-factory-data-processing-using-batch/image19.png)

   Sprawdź system-0. log, aby uzyskać komunikaty o błędach systemowych i wyjątki.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Dołącz plik **PDB** do pliku zip, aby szczegóły błędu zawierały informacje, takie jak stos wywołań w przypadku wystąpienia błędu.

1. Wszystkie pliki w pliku ZIP dla działania niestandardowego muszą znajdować się na najwyższym poziomie bez podfolderów.

   ![Lista plików zip działania niestandardowego](./media/data-factory-data-processing-using-batch/image20.png)

1. Upewnij się, że **AssemblyName** (MyDotNetActivity.dll), **EntryPoint** (MyDotNetActivityNS. packageFile), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) i **packageLinkedService** (powinna wskazywać na magazyn obiektów blob, który zawiera plik zip), są ustawione na poprawne wartości.

1. Jeśli Naprawiono błąd i chcesz ponownie przetworzyć wycinek, kliknij prawym przyciskiem myszy wycinek w bloku **OutputDataset** i wybierz polecenie **Uruchom**.

   ![OutputDataset — opcja uruchamiania bloku](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Kontener znajduje się w magazynie obiektów BLOB o nazwie `adfjobs` . Ten kontener nie jest automatycznie usuwany, ale można go bezpiecznie usunąć po zakończeniu testowania rozwiązania. Podobnie rozwiązanie fabryki danych tworzy zadanie wsadowe o nazwie `adf-\<pool ID/name\>:job-0000000001` . To zadanie można usunąć po przetestowaniu rozwiązania.
   >
   >
1. Działanie niestandardowe nie używa pliku **app.config** z pakietu. W związku z tym, jeśli kod odczytuje wszystkie parametry połączenia z pliku konfiguracji, nie działa w czasie wykonywania. Najlepszym rozwiązaniem w przypadku korzystania z programu Batch jest przechowywanie wszelkich wpisów tajnych w Azure Key Vault. Następnie należy użyć nazwy głównej usługi opartej na certyfikatach w celu ochrony magazynu kluczy i dystrybucji certyfikatu do puli usługi Batch. Niestandardowe działanie programu .NET ma dostęp do wpisów tajnych z magazynu kluczy w czasie wykonywania. To rozwiązanie generyczne może być skalowane do dowolnego typu wpisu tajnego, a nie tylko parametrów połączenia.

    Istnieje łatwiejsze obejście tego problemu, ale nie jest to najlepsze rozwiązanie. Można utworzyć SQL Database połączoną z ustawieniami parametrów połączenia. Następnie można utworzyć zestaw danych, który używa połączonej usługi i łańcucha zestawu danych jako fikcyjny zestaw danych wejściowych do niestandardowego działania programu .NET. Następnie można uzyskać dostęp do parametrów połączenia połączonej usługi w niestandardowym kodzie działania. Powinien on pracować prawidłowo w czasie wykonywania.  

#### <a name="extend-the-sample"></a>Zwiększ przykład
Możesz zwiększyć ten przykład, aby dowiedzieć się więcej o funkcjach Data Factory i Batch. Na przykład, aby przetworzyć wycinków w innym zakresie czasu, wykonaj następujące czynności:

1. Dodaj następujące podfoldery w `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 i 2015-11-16-09. Umieść pliki wejściowe w tych folderach. Zmień godzinę zakończenia potoku z `2015-11-16T05:00:00Z` na `2015-11-16T10:00:00Z` . W widoku **diagramu** kliknij dwukrotnie pozycję **InputDataset** i upewnij się, że wycinki wejściowe są gotowe. Kliknij dwukrotnie pozycję **OutputDataset** , aby wyświetlić stan wycinków danych wyjściowych. Jeśli są w stanie **gotowe** , sprawdź folder wyjściowy dla plików wyjściowych.

1. Zwiększ lub Zmniejsz ustawienie **współbieżności** , aby zrozumieć, jak ma to wpływ na wydajność rozwiązania, szczególnie przetwarzanie wykonywane w ramach partii. Aby uzyskać więcej informacji na temat ustawienia **współbieżności** , zobacz "krok 4: Tworzenie i uruchamianie potoku za pomocą działania niestandardowego".

1. Utwórz pulę z wyższymi/niższymi **maksymalnymi zadaniami na maszynę wirtualną**. Aby użyć utworzonej nowej puli, zaktualizuj połączoną usługę Batch w rozwiązaniu fabryki danych. Aby uzyskać więcej informacji na temat **maksymalnego ustawienia zadań dla maszyny wirtualnej** , zobacz "krok 4: Tworzenie i uruchamianie potoku za pomocą działania niestandardowego".

1. Utwórz pulę zadań wsadowych za pomocą funkcji **automatycznego skalowania** . Automatyczne skalowanie węzłów obliczeniowych w puli wsadowej jest dynamicznym dopasowaniem mocy obliczeniowej używanej przez aplikację.

    Przykładowa formuła ma następujące zachowanie. Po utworzeniu puli rozpoczyna się ona od jednej maszyny wirtualnej. Metryka $PendingTasks definiuje liczbę zadań w Stanach uruchomione i aktywne (w kolejce). Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia TargetDedicated. Gwarantuje to, że TargetDedicated nigdy nie przekracza 25 maszyn wirtualnych. W miarę przesyłania nowych zadań Pula automatycznie rośnie. Po zakończeniu zadań maszyny wirtualne stają się bezpłatne po jednym, a skalowanie automatyczne zmniejsza te maszyny wirtualne. StartingNumberOfVMs i maxNumberofVMs można dostosować do własnych potrzeb.

    Formuła automatycznego skalowania:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli usługi Batch](../../batch/batch-automatic-scaling.md).

   Jeśli pula używa domyślnego [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale), usługa Batch może potrwać od 15 do 30 minut, aby przygotować maszynę wirtualną przed uruchomieniem działania niestandardowego. Jeśli pula używa różnych autoScaleEvaluationInterval, usługa Batch może przyjmować autoScaleEvaluationInterval plus 10 minut.

1. W przykładowym rozwiązaniu Metoda **Execute** wywołuje metodę **obliczeń** , która przetwarza wycinek danych wejściowych w celu utworzenia wycinka danych wyjściowych. Można napisać własną metodę, aby przetwarzać dane wejściowe i zamienić wywołanie metody **obliczeń** w metodzie **Execute** z wywołaniem metody.

### <a name="next-steps-consume-the-data"></a>Następne kroki: korzystanie z danych
Po przeprowadzeniu danych można korzystać z nich za pomocą narzędzi online, takich jak Power BI. Poniżej znajdują się linki ułatwiające zrozumienie Power BI i sposobu korzystania z niego na platformie Azure:

* [Eksplorowanie zestawu danych w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Wprowadzenie do programu Power BI Desktop](/power-bi/fundamentals/desktop-getting-started)
* [Odśwież dane w Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure i Power BI: omówienie podstawowe](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Odwołania
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
  * [Wprowadzenie do Data Factory](data-factory-build-your-first-pipeline.md)
  * [Korzystanie z działań niestandardowych w potoku Data Factory](data-factory-use-custom-activities.md)
* [Usługa Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Podstawowe informacje o usłudze Batch](../../azure-sql/database/sql-database-paas-overview.md)
  * [Omówienie funkcji usługi Batch](../../batch/batch-service-workflow-features.md)
  * [Utwórz konto wsadowe i Zarządzaj nim w Azure Portal](../../batch/batch-account-create-portal.md)
  * [Wprowadzenie do biblioteki klienta usługi Batch dla platformy .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough
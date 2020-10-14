---
title: Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
description: Dowiedz się, jak tworzyć niestandardowe działania i używać ich w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
manager: anandsub
robots: noindex
ms.openlocfilehash: f8cd72d34535ac3a2aec60aa3d2369da34dd7194
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017407"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Korzystanie z działań niestandardowych w potoku Azure Data Factory w wersji 1
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-use-custom-activities.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [działania niestandardowe w wersji 2](../transform-data-using-dotnet-custom-activity.md).

Istnieją dwa typy działań, których można użyć w potoku Azure Data Factory.

- [Działania przenoszenia danych](data-factory-data-movement-activities.md) w celu przenoszenia danych między [obsługiwanymi magazynami danych źródłowych i ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Działania przekształcania danych](data-factory-data-transformation-activities.md) w celu przekształcania danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight, Azure Batch i Azure Machine Learning Studio (klasyczne).

Aby przenieść dane do/z magazynu danych, który nie obsługuje Data Factory, Utwórz **niestandardowe działanie** z własną logiką przenoszenia danych i Użyj działania w potoku. Podobnie, aby przekształcić/przetwarzać dane w sposób, który nie jest obsługiwany przez Data Factory, Utwórz niestandardowe działanie z własną logiką transformacji danych i Użyj działania w potoku.

Działanie niestandardowe można skonfigurować do uruchamiania w puli **Azure Batch** maszyn wirtualnych. W przypadku korzystania z Azure Batch można użyć tylko istniejącej puli Azure Batch.

Poniższy przewodnik zawiera instrukcje krok po kroku dotyczące tworzenia niestandardowego działania programu .NET i używania niestandardowego działania w potoku. Przewodnik używa połączonej usługi **Azure Batch** .

> [!IMPORTANT]
> - Do uzyskiwania dostępu do lokalnych źródeł danych nie można używać bramy Zarządzanie danymi z działania niestandardowego. Obecnie [brama zarządzanie danymi](data-factory-data-management-gateway.md) obsługuje tylko działanie kopiowania i działania procedury składowanej w programie Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Przewodnik: tworzenie działania niestandardowego
### <a name="prerequisites"></a>Wymagania wstępne
* Program Visual Studio 2012/2013/2015/2017
* Pobierz i zainstaluj [zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Wymagania wstępne Azure Batch
W tym przewodniku uruchomiono niestandardowe działania programu .NET przy użyciu Azure Batch jako zasób obliczeniowy. **Azure Batch** to usługa platformy służąca do wydajnego uruchamiania aplikacji równoległych i o wysokiej wydajności obliczeniowych (HPC) w chmurze. Azure Batch planuje uruchamianie zadań intensywnie korzystających z mocy obliczeniowej na zarządzanych **kolekcjach maszyn wirtualnych**i umożliwia automatyczne skalowanie zasobów obliczeniowych w celu spełnienia potrzeb Twoich obowiązków. Aby zapoznać się z szczegółowym omówieniem usługi Azure Batch, zobacz artykuł dotyczący [Azure Batch podstawowych][batch-technical-overview] .

Aby zapoznać się z samouczkiem, Utwórz konto Azure Batch z pulą maszyn wirtualnych. Oto odpowiednie kroki:

1. Utwórz **konto Azure Batch** przy użyciu [Azure Portal](https://portal.azure.com). Instrukcje można znaleźć w artykule [Tworzenie artykułu konta Azure Batch i zarządzanie][batch-create-account] nim.
2. Zanotuj nazwę konta Azure Batch, klucz konta, identyfikator URI i nazwę puli. Są one potrzebne do utworzenia Azure Batch połączonej usługi.
    1. Na stronie głównej konta Azure Batch zostanie wyświetlony **adres URL** w następującym formacie: `https://myaccount.westus.batch.azure.com` . W tym przykładzie konto **jest nazwą konta Azure Batch** . Identyfikator URI, który jest używany w definicji połączonej usługi, to adres URL bez nazwy konta. Przykład: `https://<region>.batch.azure.com`.
    2. W menu po lewej stronie kliknij pozycję **klucze** , a następnie skopiuj **podstawowy klucz dostępu**.
    3. Aby użyć istniejącej puli, kliknij pozycję **Pule** w menu, a następnie zanotuj **Identyfikator** puli. Jeśli nie masz istniejącej puli, przejdź do następnego kroku.
2. Utwórz **pulę Azure Batch**.

   1. W [Azure Portal](https://portal.azure.com)kliknij przycisk **Przeglądaj** w menu po lewej stronie, a następnie kliknij pozycję konta w usłudze **Batch**.
   2. Wybierz konto Azure Batch, aby otworzyć blok **konto** w usłudze Batch.
   3. Kliknij kafelek **Pule** .
   4. W bloku **Pule** kliknij przycisk Dodaj na pasku narzędzi, aby dodać pulę.
      1. Wprowadź identyfikator puli (Identyfikator puli). Zanotuj **Identyfikator puli**; jest ona potrzebna podczas tworzenia rozwiązania Data Factory.
      2. Określ **system Windows Server 2012 R2** dla ustawienia rodziny systemów operacyjnych.
      3. Wybierz **warstwę cenową węzła**.
      4. Wprowadź **2** jako wartość dla ustawienia **dedykowanego elementu docelowego** .
      5. Wprowadź **2** jako wartość dla ustawienia **Maksymalna liczba zadań na węzeł** .
   5. Kliknij przycisk **OK**, aby utworzyć pulę.
   6. Zanotuj **Identyfikator** puli.

### <a name="high-level-steps"></a>Kroki ogólne
Poniżej przedstawiono dwa kroki wysokiego poziomu, które należy wykonać w ramach tego przewodnika:

1. Utwórz niestandardowe działanie, które zawiera prostą logikę przetwarzania i przekształcania danych.
2. Utwórz fabrykę danych Azure przy użyciu potoku korzystającego z działania niestandardowego.

### <a name="create-a-custom-activity"></a>Tworzenie działania niestandardowego
Aby utworzyć niestandardowe działanie platformy .NET, Utwórz projekt **biblioteki klas .NET** z klasą implementującą ten interfejs **IDotNetActivity** . Ten interfejs ma tylko jedną metodę: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) i jego sygnatura:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoda przyjmuje cztery parametry:

- **linkedServices**. Ta właściwość jest wyliczalną listą połączonych usług magazynu danych, do których odwołuje się zestaw danych wejściowych/wyjściowych dla działania.
- **zestawy danych**. Ta właściwość jest wyliczalną listą zestawów danych wejściowych/wyjściowych dla działania. Tego parametru można użyć, aby uzyskać lokalizacje i schematy zdefiniowane za pomocą wejściowych i wyjściowych zestawów danych.
- **działanie**. Ta właściwość reprezentuje bieżące działanie. Może służyć do uzyskiwania dostępu do właściwości rozszerzonych skojarzonych z niestandardowym działaniem. Aby uzyskać szczegółowe informacje, zobacz [dostęp do rozszerzonych właściwości](#access-extended-properties) .
- **Rejestrator**. Ten obiekt umożliwia zapisanie komentarzy debugowania, które są umieszczane w dzienniku użytkownika dla potoku.

Metoda zwraca słownik, który może służyć do łańcucha działań niestandardowych w przyszłości. Ta funkcja nie została jeszcze zaimplementowana, dlatego należy zwrócić pusty słownik z metody.

### <a name="procedure"></a>Procedura
1. Utwórz projekt **biblioteki klas .NET** .
   <ol type="a">
     <li>Uruchom program Visual Studio.</li>
     <li>Kliknij pozycję <b>Plik</b>, wskaż polecenie <b>Nowy</b> i kliknij pozycję <b>Projekt</b>.</li>
     <li>Rozwiń węzeł <b>Szablony</b> i wybierz opcję <b>Visual C#</b>. W tym instruktażu użyjesz języka C#, ale możesz użyć dowolnego języka platformy .NET do opracowania niestandardowego działania.</li>
     <li>Wybierz pozycję <b>Biblioteka klas</b> z listy typów projektów po prawej stronie. W programie Visual Studio wybierz pozycję <b>Biblioteka klas (.NET Framework)</b> </li>
     <li>Wprowadź <b>wartość</b> w polu <b>Nazwa</b>.</li>
     <li>Dla <b>lokalizacji</b>wybierz pozycję <b>C:\ADFGetStarted</b> .</li>
     <li>Kliknij przycisk <b>OK</b>, aby utworzyć projekt.</li>
   </ol>

2. Kliknij pozycję **Narzędzia**, wskaż pozycję **Menedżer pakietów NuGet**, a następnie kliknij pozycję **Konsola menedżera pakietów**.

3. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zaimportować **pakiet Microsoft. Azure. Management. Datafactors**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Zaimportuj pakiet NuGet **usługi Azure Storage** do projektu.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Moduł uruchamiający usługi Data Factory wymaga wersji 4,3 programu WindowsAzure. Storage. W przypadku dodania odwołania do nowszej wersji zestawu usługi Azure Storage w projekcie działań niestandardowych zostanie wyświetlony komunikat o błędzie, gdy działanie zostanie wykonane. Aby rozwiązać ten problem, zobacz sekcję [izolacja elementu AppDomain](#appdomain-isolation) .
5. Dodaj następujące instrukcje **using** do pliku źródłowego w projekcie.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Zmień nazwę **przestrzeni nazw** na **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Zmień nazwę klasy na **"i", aby** uzyskać ją z interfejsu **IDotNetActivity** , jak pokazano w poniższym fragmencie kodu:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Zaimplementuj (Dodaj) metodę **Execute** interfejsu **IDotNetActivity** do klasy moje **dotnet** i skopiuj następujący przykładowy kod do metody.

    Poniższy przykład zlicza liczbę wystąpień wyszukiwanego terminu ("Microsoft") w każdym obiekcie blob skojarzonym z wycinkem danych.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
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

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Dodaj następujące metody pomocnika:

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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    Metoda GetFolderPath zwraca ścieżkę do folderu, do którego wskazuje zestaw danych, i Metoda GetFilename zwraca nazwę obiektu blob/pliku, do którego wskazuje zestaw danych. Jeśli folderPath definiuje się przy użyciu zmiennych, takich jak {Year}, {month}, {Day} itp., metoda zwraca ciąg, ponieważ nie zastępuje ich wartościami środowiska uruchomieniowego. Aby uzyskać szczegółowe informacje na temat uzyskiwania dostępu do parametru slicestart, SliceEnd itp., zobacz sekcję dotyczącą [rozszerzonych właściwości](#access-extended-properties) .

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoda Oblicz oblicza liczbę wystąpień słowa kluczowego Microsoft w plikach wejściowych (obiekty blob w folderze). Wyszukiwany termin ("Microsoft") jest zakodowany w kodzie.
10. Kompiluj projekt. Kliknij przycisk **Kompiluj** z menu, a następnie kliknij przycisk **Kompiluj rozwiązanie**.

    > [!IMPORTANT]
    > Ustaw wersję 4.5.2 .NET Framework jako platformę docelową dla projektu: kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Właściwości** , aby ustawić platformę docelową. Data Factory nie obsługuje działań niestandardowych skompilowanych pod kątem .NET Framework wersji nowszych niż 4.5.2.

11. Uruchom **Eksploratora Windows**i przejdź do folderu **bin\Debug** lub **bin\Release** w zależności od typu kompilacji.
12. Utwórz plik zip **MyDotNetActivity.zip** , który zawiera wszystkie pliki binarne w \<project folder\> folderze \bin\debug. Uwzględnij plik **. pdb** programu "i", aby uzyskać dodatkowe szczegóły, takie jak numer wiersza w kodzie źródłowym, który spowodował problem w przypadku wystąpienia błędu.

    > [!IMPORTANT]
    > Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.

    ![Wyjściowe pliki binarne](./media/data-factory-use-custom-activities/Binaries.png)
14. Utwórz kontener obiektów BLOB o nazwie **customactivitycontainer** , jeśli jeszcze nie istnieje.
15. Przekaż MyDotNetActivity.zip jako obiekt BLOB do customactivitycontainer w **usłudze Azure Blob** Storage (nie gorącą/chłodny magazyn obiektów BLOB), która jest określana przez AzureStorageLinkedService.

> [!IMPORTANT]
> W przypadku dodania tego projektu działania .NET do rozwiązania w programie Visual Studio, które zawiera projekt Data Factory i dodanie odwołania do projektu działania .NET z projektu aplikacji Data Factory, nie trzeba wykonywać ostatnich dwóch kroków ręcznego tworzenia pliku zip i przekazywania go do magazynu obiektów blob platformy Azure ogólnego przeznaczenia. Podczas publikowania Data Factory jednostek przy użyciu programu Visual Studio te kroki są wykonywane automatycznie przez proces publikowania. Aby uzyskać więcej informacji, zobacz [Data Factory Project w programie Visual Studio](#data-factory-project-in-visual-studio) .

## <a name="create-a-pipeline-with-custom-activity"></a>Tworzenie potoku za pomocą działania niestandardowego
Utworzono działanie niestandardowe i przekazano plik zip z danymi binarnymi do kontenera obiektów BLOB na koncie usługi Azure Storage **ogólnego przeznaczenia** . W tej sekcji utworzysz fabrykę danych Azure przy użyciu potoku korzystającego z działania niestandardowego.

Wejściowy zestaw danych dla działania niestandardowego reprezentuje obiekty blob (pliki) w folderze customactivityinput kontenera adftutorial w magazynie obiektów BLOB. Wyjściowy zestaw danych dla działania reprezentuje wyjściowe obiekty blob w folderze customactivityoutput kontenera adftutorial w magazynie obiektów BLOB.

Utwórz plik **file.txt** z następującą zawartością i przekaż go do folderu **customactivityinput** w kontenerze **adftutorial** . Utwórz kontener adftutorial, jeśli jeszcze nie istnieje.

```
test custom activity Microsoft test custom activity Microsoft
```

Folder wejściowy odnosi się do wycinka w Azure Data Factory, nawet jeśli folder ma dwa lub więcej plików. Gdy każdy wycinek jest przetwarzany przez potok, działanie niestandardowe wykonuje iterację przez wszystkie obiekty blob w folderze wejściowym dla tego wycinka.

Zobaczysz jeden plik wyjściowy z w folderze adftutorial\customactivityoutput z co najmniej jednym wierszem (tak samo jak liczba obiektów BLOB w folderze wejściowym):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Poniżej przedstawiono kroki, które należy wykonać w tej sekcji:

1. Utwórz **fabrykę danych**.
2. Utwórz **połączone usługi** dla puli Azure Batch maszyn wirtualnych, na których uruchamiane jest działanie niestandardowe i magazyn platformy Azure, który zawiera obiekty blob wejściowe/wyjściowe.
3. Utwórz wejściowe i wyjściowe **zestawy** danych, które reprezentują dane wejściowe i wyjściowe działania niestandardowego.
4. Utwórz **potok** , który używa działania niestandardowego.

> [!NOTE]
> Utwórz **file.txt** i przekaż go do kontenera obiektów blob, jeśli jeszcze tego nie zrobiono. Zobacz instrukcje w poprzedniej sekcji.

### <a name="step-1-create-the-data-factory"></a>Krok 1. Tworzenie fabryki danych
1. Po zalogowaniu się do Azure Portal wykonaj następujące czynności:
   1. W menu po lewej stronie kliknij pozycję **Utwórz zasób** .
   2. W bloku **Nowy** kliknij pozycję **dane + analiza** .
   3. Kliknij przycisk **Fabryka danych** w bloku **Analiza danych**.

      ![Nowe menu Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. W bloku **Nowa fabryka danych** wprowadź **CustomActivityFactory** w polu Nazwa. Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd: **Nazwa fabryki danych "CustomActivityFactory" jest niedostępna**, Zmień nazwę fabryki danych (np. **yournameCustomActivityFactory**) i spróbuj utworzyć ją ponownie.

    ![Nowy blok Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kliknij opcję **Nazwa grupy zasobów**i wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów.
4. Sprawdź, czy używasz prawidłowej **subskrypcji** i **regionu** , w której chcesz utworzyć fabrykę danych.
5. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
6. Zostanie wyświetlona Fabryka danych utworzona na **pulpicie nawigacyjnym** Azure Portal.
7. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlony blok Data Factory, który pokazuje zawartość fabryki danych.

    ![Blok Fabryka danych](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Krok 2. Tworzenie połączonych usług
Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych Azure. W tym kroku połączysz konto usługi Azure Storage i konto Azure Batch z fabryką danych.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Kliknij kafelek **Tworzenie i wdrażanie** w bloku **Fabryka danych** dla **CustomActivityFactory**. Pojawi się Edytor fabryki danych.
2. Kliknij pozycję **nowy magazyn danych** na pasku poleceń i wybierz pozycję **Azure Storage**. Powinien zostać wyświetlony skrypt JSON do tworzenia połączonej usługi Azure Storage w edytorze.

    ![Nowy magazyn danych — Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Zastąp ciąg `<accountname>` nazwą konta usługi Azure Storage i `<accountkey>` kluczem dostępu do konta usługi Azure Storage. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

    ![Usługa polubienia usługi Azure Storage](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

#### <a name="create-azure-batch-linked-service"></a>Tworzenie połączonej usługi Azure Batch
1. W edytorze Data Factory kliknij pozycję **... Więcej** na pasku poleceń kliknij pozycję **nowe obliczenie**, a następnie wybierz pozycję **Azure Batch** z menu.

    ![Nowe obliczanie Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Wprowadź następujące zmiany w skrypcie JSON:

   1. Określ nazwę konta Azure Batch dla właściwości **AccountName** . **Adres URL** z **bloku konta Azure Batch** ma następujący format: `http://accountname.region.batch.azure.com` . Dla właściwości **batchUri** w kodzie JSON należy usunąć `accountname.` z adresu URL i użyć `accountname` dla `accountName` Właściwości JSON.
   2. Określ klucz konta Azure Batch dla właściwości **AccessKey** .
   3. Określ nazwę puli utworzonej w ramach wymagań wstępnych dla właściwości **PoolName** . Możesz również określić identyfikator puli zamiast nazwy puli.
   4. Określ Azure Batch identyfikator URI dla właściwości **batchUri** . Przykład: `https://westus.batch.azure.com`.
   5. Określ **AzureStorageLinkedService** dla właściwości **linkedServiceName** .

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Dla właściwości **PoolName** można także określić identyfikator puli, a nie nazwę puli.

### <a name="step-3-create-datasets"></a>Krok 3. Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące dane wejściowe i wyjściowe.

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **Edytorze** dla Data Factory kliknij pozycję **... Więcej** na pasku poleceń kliknij pozycję **Nowy zestaw danych**, a następnie wybierz pozycję **Azure Blob Storage** z menu rozwijanego.
2. Zastąp kod JSON w okienku po prawej stronie następującym fragmentem kodu JSON:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
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

   Potok można utworzyć w dalszej części tego przewodnika z czasem rozpoczęcia: 2016-11-16T00:00:00Z i godzina zakończenia: 2016-11-16T05:00:00Z. Planuje się wygenerowanie danych co godzinę, więc istnieje pięć wycinków danych wejściowych/wyjściowych (od **00**: 00:00-> **05**: 00:00).

   **Częstotliwość** i **Interwał** dla wejściowego zestawu danych jest ustawiony na wartość **Hour** i **1**, co oznacza, że wycinek danych wejściowych jest dostępny co godzinę. W tym przykładzie jest to ten sam plik (file.txt) w intputfolder.

   Oto czasy rozpoczęcia dla każdego wycinka reprezentowanego przez zmienną systemową parametru slicestart w powyższym fragmencie kodu JSON.
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć **InputDataset**. Upewnij się, że na pasku tytułu w edytorze wyświetlany jest komunikat **TABELA ZOSTAŁA UTWORZONA POMYŚLNIE**.

#### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
1. W **edytorze Data Factory**kliknij pozycję **... Więcej** na pasku poleceń kliknij pozycję **Nowy zestaw danych**, a następnie wybierz pozycję **Azure Blob Storage**.
2. Zastąp skrypt JSON w okienku po prawej stronie następującym skryptem JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Lokalizacja wyjściowa to **adftutorial/customactivityoutput/** i nazwa pliku wyjściowego jest yyyy-MM-dd-HH.txt gdzie rrrr-mm-dd-hh jest rokiem, miesiącem, datą i godziną generowanego wycinka. Aby uzyskać szczegółowe informacje, zobacz Dokumentacja [dewelopera][adf-developer-reference] .

    Wyjściowy obiekt BLOB/plik jest generowany dla każdego wycinka danych wejściowych. Poniżej przedstawiono sposób, w jaki plik wyjściowy jest nazwany dla każdego wycinka. Wszystkie pliki wyjściowe są generowane w jednym folderze wyjściowym: **adftutorial\customactivityoutput**.

   | Cinek | Godzina rozpoczęcia | Plik wyjściowy |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Należy pamiętać, że wszystkie pliki w folderze wejściowym są częścią wycinka o powyższym czasie początkowym. Po przetworzeniu tego wycinka działanie niestandardowe skanuje każdy plik i tworzy wiersz w pliku wyjściowym z liczbą wystąpień wyszukiwanego terminu ("Microsoft"). Jeśli w folderze wejściowym znajdują się trzy pliki, w pliku wyjściowym znajdują się trzy wiersze: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt itd.
3. Aby wdrożyć **OutputDataset**, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Tworzenie i uruchamianie potoku korzystającego z działania niestandardowego
1. W edytorze Data Factory kliknij pozycję **... Więcej**, a następnie wybierz pozycję **Nowy potok** na pasku poleceń.
2. Zastąp kod JSON w prawym okienku następującym skryptem JSON:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Pamiętaj o następujących kwestiach:

   * **Współbieżność** jest ustawiona na **2** , aby dwa wycinki były przetwarzane równolegle przez 2 maszyny wirtualne w puli Azure Batch.
   * W sekcji Activities istnieje jedno działanie i jest ono typu: **dotnet**.
   * Nazwa **AssemblyName** jest ustawiona na nazwę pliku DLL: **MyDotnetActivity.dll**.
   * **Punkt wejścia** jest ustawiony na **MyDotNetActivityNS..**
   * **PackageLinkedService** jest ustawiona na **AzureStorageLinkedService** , która wskazuje na magazyn obiektów blob, który zawiera plik zip działania niestandardowego. Jeśli używasz innych kont usługi Azure Storage dla plików wejściowych/wyjściowych i niestandardowego pliku zip działania, utworzysz kolejną połączoną usługę Azure Storage. W tym artykule przyjęto założenie, że używasz tego samego konta usługi Azure Storage.
   * **PackageFile** jest ustawiona na **customactivitycontainer/MyDotNetActivity.zip**. Ma format: containerforthezip/nameofthezip.zip.
   * Działanie niestandardowe przyjmuje **InputDataset** jako dane wejściowe i **OutputDataset** jako dane wyjściowe.
   * Właściwość linkedServiceName niestandardowego działania wskazuje **AzureBatchLinkedService**, która informuje Azure Data Factory, że działanie niestandardowe musi być uruchamiane na Azure Batch maszynach wirtualnych.
   * Właściwość **IsPaused** jest domyślnie ustawiona na **false** . Potok jest uruchamiany natychmiast w tym przykładzie, ponieważ wycinki zaczynają się w przeszłości. Możesz ustawić tę właściwość na wartość true, aby wstrzymać potok i ustawić z powrotem wartość false, aby ponownie uruchomić system.
   * Czas **rozpoczęcia** i czas **zakończenia** są **pięć** godzin od siebie, a wycinki są generowane co godzinę, więc do potoku są tworzone pięć wycinków.
3. Aby wdrożyć potok, kliknij przycisk **Wdróż** na pasku poleceń.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. W bloku Data Factory w Azure Portal kliknij pozycję **Diagram**.

    ![Kafelek Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. W widoku diagramu kliknij OutputDataset.

    ![Widok diagramu](./media/data-factory-use-custom-activities/diagram.png)
3. Powinno się zobaczyć, że pięć wycinków wyjściowych jest w stanie gotowe. Jeśli nie są one w stanie gotowe, nie zostały jeszcze utworzone.

   ![Wycinki wyjściowe](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Sprawdź, czy pliki wyjściowe są generowane w magazynie obiektów BLOB w kontenerze **adftutorial** .

   ![dane wyjściowe z działania niestandardowego][image-data-factory-output-from-custom-activity]
5. Jeśli otworzysz plik wyjściowy, powinny pojawić się dane wyjściowe podobne do następujących:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Użyj poleceń cmdlet [Azure Portal][azure-preview-portal] lub Azure PowerShell do monitorowania fabryki danych, potoków i zestawów danych. Komunikaty z **ActivityLogger** można zobaczyć w kodzie niestandardowego działania w dziennikach (w tym User-0. log), które można pobrać z portalu lub za pomocą poleceń cmdlet.

   ![Pobierz dzienniki z działania niestandardowego][image-data-factory-download-logs-from-custom-activity]

Aby zapoznać się ze szczegółowymi krokami monitorowania zestawów danych i potoków, zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) .

## <a name="data-factory-project-in-visual-studio"></a>Projekt Data Factory w programie Visual Studio
Możesz tworzyć i publikować jednostki Data Factory przy użyciu programu Visual Studio, zamiast korzystać z Azure Portal. Aby uzyskać szczegółowe informacje o tworzeniu i publikowaniu jednostek Data Factory przy użyciu programu Visual Studio, zobacz [Tworzenie pierwszego potoku przy użyciu programu Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) i [Kopiowanie danych z usługi Azure BLOB do artykułów SQL platformy Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) .

Wykonaj następujące dodatkowe kroki, jeśli tworzysz projekt Data Factory w programie Visual Studio:

1. Dodaj projekt Data Factory do rozwiązania programu Visual Studio, które zawiera projekt działania niestandardowego.
2. Dodaj odwołanie do projektu działania .NET z projektu Data Factory. Kliknij prawym przyciskiem myszy pozycję Projekt Data Factory, wskaż polecenie **Dodaj**, a następnie kliknij pozycję **odwołanie**.
3. W oknie dialogowym **Dodaj odwołanie** **Wybierz projekt,** a następnie kliknij przycisk **OK**.
4. Skompiluj i Opublikuj rozwiązanie.

    > [!IMPORTANT]
    > Gdy publikujesz Data Factory jednostki, plik zip jest automatycznie tworzony i przekazywany do kontenera obiektów blob: customactivitycontainer. Jeśli kontener obiektów BLOB nie istnieje, zostanie on utworzony automatycznie.

## <a name="data-factory-and-batch-integration"></a>Integracja Data Factory i partii
Usługa Data Factory tworzy zadanie w Azure Batch z nazwą: **ADF-PoolName: Job-XXX**. Kliknij pozycję **zadania** w menu po lewej stronie.

![Azure Data Factory — zadania wsadowe](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Zadanie jest tworzone dla każdego uruchomienia wycinka. Jeśli do przetworzenia jest gotowych pięć wycinków, w tym zadaniu są tworzone pięć zadań. W przypadku wielu węzłów obliczeniowych w puli partii co najmniej dwa wycinki mogą działać równolegle. Jeśli maksymalna liczba zadań na węzeł obliczeniowy jest ustawiona na > 1, można także mieć więcej niż jeden wycinek uruchomiony w ramach tego samego obliczenia.

![Azure Data Factory — zadania zadań wsadowych](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Na poniższym diagramie przedstawiono relację między Azure Data Factory i zadaniami wsadowymi.

![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Rozwiązywanie problemów
Rozwiązywanie problemów obejmuje kilka podstawowych technik:

1. Jeśli zobaczysz następujący błąd, możesz użyć gorąca/chłodnego magazynu obiektów blob, zamiast korzystać z usługi Azure Blob Storage. Przekaż plik zip do **konta usługi Azure Storage ogólnego przeznaczenia**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Jeśli zobaczysz następujący błąd, upewnij się, że nazwa klasy w pliku CS jest zgodna z nazwą określoną dla właściwości **punktu wejścia** w kodzie JSON potoku. W instruktażu nazwa klasy to: DotNet, a punkt wejścia w kodzie JSON to: MyDotNetActivityNS. **.**

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Jeśli nazwy są zgodne, potwierdź, że wszystkie pliki binarne znajdują się w **folderze głównym** pliku zip. Oznacza to, że po otwarciu pliku zip powinny zostać wyświetlone wszystkie pliki w folderze głównym, a nie w żadnym podfolderach.
3. Jeśli wycinek wejściowy nie jest ustawiony na wartość **gotowe**, upewnij się, że struktura folderu wejściowego jest poprawna, a **file.txt** istnieje w folderach wejściowych.
3. W metodzie **Execute** niestandardowego działania Użyj obiektu **IActivityLogger** , aby rejestrować informacje pomagające w rozwiązywaniu problemów. Zarejestrowane komunikaty są wyświetlane w plikach dziennika użytkownika (co najmniej jeden plik o nazwie: User-0. log, User-1. log, User-2. log itp.).

   W bloku **OutputDataset** kliknij wycinek, aby wyświetlić blok **wycinka danych** dla tego wycinka. Zobaczysz **uruchomienia aktywności** dla tego wycinka. Dla wycinka powinien zostać wyświetlony jeden przebieg działania. Kliknięcie przycisku Uruchom na pasku poleceń umożliwia uruchomienie innego uruchomienia działania dla tego samego wycinka.

   Po kliknięciu uruchomienia działania zostanie wyświetlony blok **szczegóły uruchomienia działania** z listą plików dziennika. W pliku user_0. log są wyświetlane zarejestrowane komunikaty. Gdy wystąpi błąd, zobaczysz trzy uruchomienia działania, ponieważ liczba ponownych prób jest ustawiona na 3 w formacie JSON potoku/działania. Po kliknięciu uruchomienia działania zobaczysz pliki dziennika, które można przejrzeć, aby rozwiązać problem.

   Na liście plików dziennika kliknij plik **User-0. log**. W prawym panelu są wyniki przy użyciu metody **IActivityLogger. Write** . Jeśli nie widzisz wszystkich komunikatów, sprawdź, czy masz więcej plików dziennika o nazwie: user_1. log, user_2. log itp. W przeciwnym razie kod może się nie powiódł po ostatnim zarejestrowanym komunikacie.

   Ponadto sprawdź **system-0. log** , aby uzyskać komunikaty o błędach i wyjątki systemu.
4. Dołącz plik **PDB** do pliku zip, aby szczegóły błędu zawierały informacje, takie jak **stos wywołań** w przypadku wystąpienia błędu.
5. Wszystkie pliki w archiwum ZIP działania niestandardowego muszą znajdować się na **najwyższym poziomie**, bez podfolderów.
6. Upewnij się, że obiekty **AssemblyName** (MyDotNetActivity.dll), **EntryPoint**(MyDotNetActivityNS. packageFile), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) i **packageLinkedService** (powinny wskazywać magazyn obiektów **blob platformy Azure**, który zawiera plik zip), są ustawione na poprawne wartości.
7. Jeśli naprawiono błąd i chcesz przetworzyć wycinek ponownie, kliknij prawym przyciskiem wycinek w bloku **OutputDataset** i kliknij polecenie **Uruchom**.
8. Jeśli zobaczysz następujący błąd, korzystasz z pakietu usługi Azure Storage w wersji > 4.3.0. Moduł uruchamiający usługi Data Factory wymaga wersji 4,3 programu WindowsAzure. Storage. Jeśli musisz użyć nowszej wersji zestawu Azure Storage, zobacz sekcję [Izolacja domeny aplikacji](#appdomain-isolation) .

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Jeśli możesz użyć wersji 4.3.0 pakietu usługi Azure Storage, Usuń istniejące odwołanie do pakietu usługi Azure Storage w wersji > 4.3.0. Następnie uruchom następujące polecenie w konsoli Menedżera pakietów NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Skompiluj projekt. Usuń zestaw Azure. Storage z wersji > 4.3.0 z folderu bin\Debug. Utwórz plik zip przy użyciu plików binarnych i pliku PDB. Zamień stary plik zip na ten, który znajduje się w kontenerze obiektów BLOB (customactivitycontainer). Uruchom ponownie wycinki, które nie powiodły się (wycinek kliknij prawym przyciskiem myszy, a następnie kliknij przycisk Uruchom).
8. Działanie niestandardowe nie używa pliku **app.config** z pakietu. W związku z tym, jeśli kod odczytuje wszystkie parametry połączenia z pliku konfiguracji, nie działa w czasie wykonywania. Najlepszym rozwiązaniem w przypadku korzystania z Azure Batch jest przechowywanie wszelkich wpisów tajnych w **magazynie kluczy platformy Azure**, użycie jednostki usługi opartej na certyfikatach w celu ochrony **magazynu**i dystrybuowanie certyfikatu do puli Azure Batch. Niestandardowe działanie .NET będzie miało w takiej sytuacji dostęp do danych poufnych z magazynu KeyVault w czasie uruchomienia. To rozwiązanie jest rozwiązaniem ogólnym i może być skalowane do dowolnego typu wpisu tajnego, a nie tylko parametrów połączenia.

   Istnieje łatwiejsze obejście (ale nie najlepsze rozwiązanie): można utworzyć **połączoną usługę Azure SQL** z ustawieniami parametrów połączenia, utworzyć zestaw danych, który używa połączonej usługi, i łańcuchować zestaw danych jako fikcyjny zestaw danych wejściowych do niestandardowego działania programu .NET. Następnie można uzyskać dostęp do parametrów połączenia połączonej usługi w niestandardowym kodzie działania.

## <a name="update-custom-activity"></a>Aktualizuj działanie niestandardowe
Jeśli zaktualizujesz kod dla działania niestandardowego, skompiluj go i Przekaż plik zip zawierający nowe pliki binarne do magazynu obiektów BLOB.

## <a name="appdomain-isolation"></a>Izolacja elementu AppDomain
Zobacz [przykład Cross AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) , który pokazuje, jak utworzyć niestandardowe działanie, które nie jest ograniczone do wersji zestawu używanych przez program do uruchamiania Data Factory (przykład: windowsazure. Storage v 4.3.0, Newtonsoft.Jsw wersji v 6.0. x itp.).

## <a name="access-extended-properties"></a>Dostęp do właściwości rozszerzonych
Można zadeklarować rozszerzone właściwości w kodzie JSON działania, jak pokazano w następującym przykładzie:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

W tym przykładzie istnieją dwie właściwości rozszerzone: **parametru slicestart** i **datafactoryname**. Wartość dla parametru slicestart jest oparta na zmiennej systemowej parametru slicestart. Zobacz [zmienne systemowe](data-factory-functions-variables.md) , aby uzyskać listę obsługiwanych zmiennych systemowych. Wartość dla datafactoryname jest trwale kodowana do CustomActivityFactory.

Aby uzyskać dostęp do tych rozszerzonych właściwości w metodzie **Execute** , należy użyć kodu podobnego do następującego kodu:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie Azure Batch
Możesz również utworzyć pulę Azure Batch za pomocą funkcji **automatycznego skalowania** . Można na przykład utworzyć pulę usługi Azure Batch z użyciem 0 dedykowanych maszyn wirtualnych i formułę skalowania automatycznego na podstawie liczby oczekujących zadań.

Przykładowa formuła ma następujące zachowanie: po początkowym utworzeniu puli rozpoczyna się od 1 maszyny wirtualnej. Metryka $PendingTasks definiuje liczbę zadań w stanie uruchomienia + aktywny (w kolejce).  Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia TargetDedicated. Gwarantuje to, że TargetDedicated nigdy nie przekracza 25 maszyn wirtualnych. Tak więc w miarę przesyłania nowych zadań Pula automatycznie rośnie i kończy zadania, maszyny wirtualne stają się bezpłatne po jednym, a skalowanie automatyczne zmniejsza te maszyny wirtualne. startingNumberOfVMs i maxNumberofVMs można dostosować do własnych potrzeb.

Formuła automatycznego skalowania:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Aby uzyskać szczegółowe informacje [, zobacz Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](../../batch/batch-automatic-scaling.md) .

Jeśli pula używa domyślnej [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), przed uruchomieniem działania niestandardowego może upłynąć 15-30 minut, aby przygotować maszynę wirtualną.  Jeśli pula korzysta z innego autoScaleEvaluationInterval, usługa Batch może wykonać autoScaleEvaluationInterval + 10 minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Tworzenie niestandardowego działania przy użyciu zestawu .NET SDK
W przewodniku w tym artykule opisano tworzenie fabryki danych z potokiem korzystającym z działania niestandardowego przy użyciu Azure Portal. Poniższy kod pokazuje, jak utworzyć fabrykę danych przy użyciu zestawu .NET SDK. Więcej informacji o używaniu zestawu SDK do programistycznego tworzenia potoków można znaleźć w artykule [Tworzenie potoku za pomocą działania kopiowania przy użyciu interfejsu API platformy .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) .

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Debuguj niestandardowe działanie w programie Visual Studio
Przykład [środowiska Azure Data Factory-Local](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) w witrynie GitHub zawiera narzędzie, które pozwala debugować niestandardowe działania programu .NET w programie Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Przykładowe działania niestandardowe w serwisie GitHub
| Przykład | Jakie działanie niestandardowe wykonuje |
| --- | --- |
| Narzędzie do [pobierania danych http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Pobiera dane z punktu końcowego HTTP do platformy Azure Blob Storage przy użyciu niestandardowej aktywności języka C# w programie Data Factory. |
| [Przykład analiza tonacji Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Wywołuje model Azure Machine Learning Studio (klasyczny) i tonacji analizę, ocenianie, prognozowanie itp. |
| [Uruchom skrypt języka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Wywołuje skrypt języka R, uruchamiając RScript.exe w klastrze usługi HDInsight, na którym jest już zainstalowany język R. |
| [Aktywność platformy .NET obejmującej wiele domen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Używa różnych wersji zestawu od tych, które są używane przez program uruchamiający Data Factory |
| [Przetwórz ponownie model w Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Przetwarza ponownie model w Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

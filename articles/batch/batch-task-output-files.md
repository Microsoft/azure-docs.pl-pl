---
title: Utrwalanie danych wyjściowych w usłudze Azure Storage za pomocą interfejsu API usługi Batch
description: Dowiedz się, jak za pomocą interfejsu API usługi Batch utrwalać dane wyjściowe zadania i zadania wsadowego w usłudze Azure Storage.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936932"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Interfejs API usługi Batch obsługuje utrwalanie danych wyjściowych w usłudze Azure Storage na potrzeby zadań i zadań Menedżera zadań, które są uruchamiane w pulach z konfiguracją maszyny wirtualnej. Po dodaniu zadania można określić kontener w usłudze Azure Storage jako miejsce docelowe dla danych wyjściowych zadania. Usługa Batch zapisuje wszystkie dane wyjściowe do tego kontenera po zakończeniu zadania.

Korzystanie z interfejsu API usługi Batch do utrwalania danych wyjściowych zadania polega na tym, że nie trzeba modyfikować aplikacji, w której uruchomiono zadanie. Zamiast tego za pomocą kilku modyfikacji aplikacji klienckiej można utrwalać dane wyjściowe zadania z tego samego kodu, który tworzy zadanie.

> [!IMPORTANT]
> Utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi Batch nie działa z pulami utworzonymi przed [1 lutego 2018](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204).

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Kiedy używać interfejsu API usługi Batch do utrwalania danych wyjściowych zadania?

Azure Batch zapewnia więcej niż jeden sposób utrwalania danych wyjściowych zadania. Korzystanie z interfejsu API usługi Batch to wygodne podejście, które najlepiej odpowiada tym scenariuszom:

- Chcesz napisać kod, aby utrzymać dane wyjściowe zadania z poziomu aplikacji klienckiej bez modyfikowania aplikacji, która jest uruchomiona.
- Chcesz utrwalać dane wyjściowe z zadań wsadowych i zadań Menedżera zadań w pulach utworzonych za pomocą konfiguracji maszyny wirtualnej.
- Chcesz utrwalać dane wyjściowe do kontenera usługi Azure Storage przy użyciu dowolnej nazwy.
- Chcesz utrwalać dane wyjściowe do kontenera usługi Azure Storage o nazwie zgodnej ze [standardem Konwencji plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files).

Jeśli Twój Scenariusz różni się od wymienionych powyżej, może być konieczne rozważenie innego podejścia. Na przykład interfejs API usługi Batch nie obsługuje obecnie przesyłania strumieniowego do usługi Azure Storage, gdy zadanie jest uruchomione. Aby przesłać strumieniowo dane wyjściowe, należy rozważyć użycie biblioteki Konwencji plików wsadowych dostępnej dla platformy .NET. W przypadku innych języków należy wdrożyć własne rozwiązanie. Aby uzyskać informacje na temat innych opcji utrwalania danych wyjściowych zadania, zobacz [utrwalanie zadań i zadań wyjściowych w usłudze Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Tworzenie kontenera w usłudze Azure Storage

Aby zachować dane wyjściowe zadania do usługi Azure Storage, należy utworzyć kontener, który służy jako miejsce docelowe dla plików wyjściowych. Utwórz kontener przed uruchomieniem zadania, najlepiej przed przesłaniem zadania. Aby utworzyć kontener, użyj odpowiedniej biblioteki klienta usługi Azure Storage lub zestawu SDK. Aby uzyskać więcej informacji na temat interfejsów API usługi Azure Storage, zobacz [dokumentację usługi Azure Storage](../storage/index.yml).

Na przykład jeśli piszesz aplikację w języku C#, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Poniższy przykład pokazuje, jak utworzyć kontener:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Uzyskaj sygnaturę dostępu współdzielonego dla kontenera

Po utworzeniu kontenera Uzyskaj sygnaturę dostępu współdzielonego (SAS) z dostępem do zapisu do kontenera. Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do kontenera. Sygnatura dostępu współdzielonego przyznaje dostęp z określonym zestawem uprawnień i w określonym przedziale czasu. Usługa Batch potrzebuje sygnatury dostępu współdzielonego z uprawnieniami do zapisu, aby zapisywać dane wyjściowe zadania do kontenera. Aby uzyskać więcej informacji na temat SYGNATURy [dostępu współdzielonego, zobacz Using SAS Access Signatures \( \) w usłudze Azure Storage](../storage/common/storage-sas-overview.md).

Gdy otrzymasz sygnaturę dostępu współdzielonego przy użyciu interfejsów API usługi Azure Storage, interfejs API zwraca ciąg tokenu SAS. Ten ciąg tokenu zawiera wszystkie parametry sygnatury dostępu współdzielonego, w tym uprawnienia i interwał, w jakim SYGNATURa czasowa jest prawidłowa. Aby uzyskać dostęp do kontenera w usłudze Azure Storage za pomocą sygnatury dostępu współdzielonego, należy dołączyć ciąg tokenu sygnatury dostępu współdzielonego do identyfikatora URI zasobu. Identyfikator URI zasobu, wraz z dołączonym tokenem SAS, zapewnia uwierzytelniony dostęp do usługi Azure Storage.

Poniższy przykład pokazuje, jak uzyskać ciąg tokenu sygnatury dostępu współdzielonego tylko do zapisu dla kontenera, a następnie dołącza sygnaturę dostępu współdzielonego do kontenera URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Określ pliki wyjściowe dla danych wyjściowych zadania

Aby określić pliki wyjściowe dla zadania, Utwórz kolekcję obiektów [plik_wyjściowy](/dotnet/api/microsoft.azure.batch.outputfile) i przypisz ją do właściwości [CloudTask. OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) podczas tworzenia zadania.

Poniższy przykład kodu w języku C# tworzy zadanie, które zapisuje liczby losowe do pliku o nazwie `output.txt` . Przykład tworzy plik wyjściowy do zapisu w `output.txt` kontenerze. Przykład tworzy również pliki wyjściowe dla plików dziennika, które pasują do wzorca pliku `std*.txt` (_np._ `stdout.txt` i `stderr.txt` ). Adres URL kontenera wymaga sygnatury dostępu współdzielonego, która została wcześniej utworzona dla kontenera. Usługa Batch używa sygnatury dostępu współdzielonego, aby uwierzytelniać dostęp do kontenera:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Jeśli używasz tego przykładu z systemem Linux, pamiętaj, aby zmienić ukośniki odwrotne na ukośniki.

### <a name="specify-a-file-pattern-for-matching"></a>Określ wzorzec pliku do dopasowania

Po określeniu pliku wyjściowego można użyć właściwości [plik_wyjściowy. FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) , aby określić wzorzec pliku do dopasowania. Wzorzec pliku może być zgodny z zerowymi plikami, pojedynczym plikiem lub zestawem plików, które są tworzone przez zadanie.

Właściwość **FilePattern** obsługuje standardowe symbole wieloznaczne systemu plików, takie jak `*` (dla dopasowań niecyklicznych) i `**` (dla dopasowań cyklicznych). Na przykład przykładowy kod określa wzorzec pliku do dopasowania `std*.txt` niecyklicznie:

`filePattern: @"..\std*.txt"`

Aby przekazać pojedynczy plik, określ wzorzec pliku bez symboli wieloznacznych. Na przykład przykładowy kod określa wzorzec pliku do dopasowania `output.txt` :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Określ warunek przekazywania

Właściwość [OutputFileUploadOptions. UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) zezwala na warunkowe przekazywanie plików wyjściowych. Typowym scenariuszem jest przekazanie jednego zestawu plików, jeśli zadanie zakończy się pomyślnie, a inny zestaw plików, jeśli zakończy się niepowodzeniem. Na przykład możesz chcieć przekazywać pełne pliki dziennika tylko wtedy, gdy zadanie kończy się niepowodzeniem i kończy pracę z niezerowym kodem zakończenia. Podobnie można przekazać pliki wynikowe tylko wtedy, gdy zadanie zakończy się pomyślnie, ponieważ te pliki mogą być niedostępne lub niekompletne, jeśli zadanie nie powiedzie się.

Powyższy przykład kodu ustawia właściwość **UploadCondition** na **TaskCompletion**. To ustawienie określa, że plik ma być przekazywany po zakończeniu zadań, niezależnie od wartości kodu zakończenia.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Aby poznać inne ustawienia, zobacz Wyliczenie [OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) .

### <a name="disambiguate-files-with-the-same-name"></a>Uściślanie plików o tej samej nazwie

Zadania w zadaniu mogą generować pliki o tej samej nazwie. Na przykład `stdout.txt` i `stderr.txt` są tworzone dla każdego zadania uruchamianego w ramach zadania. Ponieważ każde zadanie jest uruchamiane we własnym kontekście, te pliki nie powodują konfliktu w systemie plików węzła. Jednak podczas przekazywania plików z wielu zadań do udostępnionego kontenera należy rozróżnić pliki o tej samej nazwie.

Właściwość [OutputFileBlobContainerDestination. Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) określa docelowy obiekt BLOB lub katalog wirtualny dla plików wyjściowych. Możesz użyć właściwości **Path** , aby nazwać obiekt BLOB lub katalog wirtualny w taki sposób, że pliki wyjściowe o tej samej nazwie są jednoznacznie nazwane w usłudze Azure Storage. Użycie identyfikatora zadania w ścieżce jest dobrym sposobem na zapewnienie unikatowych nazw i łatwe identyfikowanie plików.

Jeśli właściwość **FilePattern** jest ustawiona na wyrażenie symbolu wieloznacznego, wszystkie pliki zgodne ze wzorcem są przekazywane do katalogu wirtualnego określonego przez właściwość **Path** . Na przykład, jeśli kontener to `mycontainer` , identyfikator zadania to `mytask` , a wzorzec pliku to `..\std*.txt` , bezwzględne identyfikatory URI plików wyjściowych w usłudze Azure Storage będą podobne do następujących:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Jeśli właściwość **FilePattern** jest ustawiona tak, aby odpowiadała pojedynczej nazwie pliku, co oznacza, że nie zawiera żadnych symboli wieloznacznych, a następnie wartość właściwości **Path** określa w pełni kwalifikowaną nazwę obiektu BLOB. Jeśli przewidujesz konflikty nazw z pojedynczym plikiem z wielu zadań, Dołącz nazwę katalogu wirtualnego jako część nazwy pliku, aby odróżnić te pliki. Na przykład ustaw właściwość **Path** na dołączenie identyfikatora zadania, znaku ogranicznika (zazwyczaj ukośnika dalej) i nazwy pliku:

`path: taskId + @"/output.txt"`

Bezwzględne identyfikatory URI dla plików wyjściowych dla zestawu zadań będą podobne do:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Aby uzyskać więcej informacji o katalogach wirtualnych w usłudze Azure Storage, zobacz [Wyświetlanie listy obiektów BLOB w kontenerze](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnozuj błędy przekazywania plików

W przypadku niepowodzenia przekazywania plików wyjściowych do usługi Azure Storage zadanie zostanie przesunięte do stanu **ukończono** i zostanie ustawiona właściwość [TaskExecutionInformation. FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) . Sprawdź Właściwość **FailureInformation** , aby określić, jaki błąd wystąpił. Na przykład poniżej znajduje się błąd podczas przekazywania pliku, jeśli nie można znaleźć kontenera:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

W przypadku każdego przekazywania plików Batch zapisuje dwa pliki dziennika w węźle COMPUTE `fileuploadout.txt` i `fileuploaderr.txt` . Możesz sprawdzić te pliki dziennika, aby dowiedzieć się więcej na temat konkretnego błędu. W przypadkach, gdy nigdy nie podjęto próby przekazania pliku, na przykład ponieważ nie można uruchomić samego zadania, te pliki dziennika nie będą istnieć.

## <a name="diagnose-file-upload-performance"></a>Diagnozowanie wydajności przekazywania plików

`fileuploadout.txt`Postęp ładowania dzienników plików. Możesz sprawdzić ten plik, aby dowiedzieć się więcej o tym, jak długo trwa przekazywanie plików. Należy pamiętać, że istnieje wiele współczynników wpływających na wydajność, w tym rozmiar węzła, inne działanie w węźle w czasie przekazywania, niezależnie od tego, czy kontener docelowy znajduje się w tym samym regionie co Pula partii, jak wiele węzłów jest przekazywanych do konta magazynu w tym samym czasie itd.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Korzystanie z interfejsu API usługi Batch w standardzie Konwencji plików wsadowych

W przypadku utrwalania danych wyjściowych zadania za pomocą interfejsu API usługi Batch można zmienić nazwę kontenera docelowego i obiektów BLOB. Można również nawiązać z nimi nazwy zgodnie ze [standardem Konwencji plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Standard Konwencji plików określa nazwy kontenera docelowego i obiektu BLOB w usłudze Azure Storage dla danego pliku wyjściowego na podstawie nazw zadania i zadania podrzędnego. Jeśli używasz standardu Konwencji plików do nazewnictwa plików wyjściowych, pliki wyjściowe są dostępne do wyświetlania w [Azure Portal](https://portal.azure.com).

Jeśli tworzysz program w języku C#, możesz użyć metod wbudowanych w [bibliotece Konwencji plików wsadowych dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ta biblioteka tworzy prawidłowo nazwane kontenery i ścieżki obiektów BLOB. Można na przykład wywołać interfejs API w celu uzyskania poprawnej nazwy dla kontenera na podstawie nazwy zadania:

```csharp
string containerName = job.OutputStorageContainerName();
```

Można użyć metody [CloudJobExtensions. GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) w celu zwrócenia adresu URL sygnatury dostępu współdzielonego, który jest używany do zapisu w kontenerze. Następnie można przekazać sygnaturę dostępu współdzielonego do konstruktora [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) .

Jeśli tworzysz w języku innym niż C#, musisz samodzielnie zaimplementować standardy plików.

## <a name="code-sample"></a>Przykład kodu

Przykładowy projekt [PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) jest jednym z [przykładów kodu Azure Batch](https://github.com/Azure/azure-batch-samples) w witrynie GitHub. W tym rozwiązaniu programu Visual Studio pokazano, jak używać biblioteki klienta usługi Batch dla platformy .NET do utrwalania danych wyjściowych zadań w magazynie trwałym. Aby uruchomić przykład, wykonaj następujące kroki:

1. Otwórz projekt w programie **Visual Studio 2019**.
2. Dodaj **poświadczenia konta** magazynu i partii do **AccountSettings. settings** w projekcie Microsoft.Azure.Batch. Samples. Common.
3. **Kompiluj** (ale nie uruchamiaj) rozwiązanie. Jeśli zostanie wyświetlony monit, Przywróć wszystkie pakiety NuGet.
4. Użyj Azure Portal, aby przekazać [pakiet aplikacji](batch-application-packages.md) dla **PersistOutputsTask**. Uwzględnij `PersistOutputsTask.exe` i jej zestawy zależne w pakiecie. zip, ustaw identyfikator aplikacji na "PersistOutputsTask" i wersję pakietu aplikacji na "1,0".
5. **Uruchom** (Uruchom) projekt **PersistOutputs** .
6. Po wyświetleniu monitu o wybranie technologii trwałości do uruchomienia przykładu wprowadź wartość **2** , aby uruchomić przykład za pomocą interfejsu API usługi Batch, aby utrzymać dane wyjściowe zadania.
7. W razie potrzeby uruchom ponownie próbkę, wprowadzając wartość **3** w celu utrwalenia danych wyjściowych za pomocą interfejsu API usługi Batch, a także Nadaj nazwę kontenerowi docelowemu i ścieżce obiektu BLOB zgodnie z standardem Konwencji plików.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat utrwalania danych wyjściowych zadań przy użyciu biblioteki Konwencji plików dla platformy .NET, zobacz [trwałe dane zadania i zadania do usługi Azure Storage za pomocą biblioteki Konwencji plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).
- Aby dowiedzieć się więcej na temat innych metod utrwalania danych wyjściowych w Azure Batch, zobacz [trwałe zadania i zadania do usługi Azure Storage](batch-task-output.md).

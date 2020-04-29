---
title: Tworzenie i zarządzanie migawką obiektów BLOB w programie .NET — Azure Storage
description: Dowiedz się, jak utworzyć migawkę obiektu BLOB w trybie tylko do odczytu, aby utworzyć kopię zapasową danych obiektów BLOB w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80474086"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią

Migawka to wersja obiektu BLOB tylko do odczytu, która jest wykonywana w danym momencie. Migawki są przydatne do tworzenia kopii zapasowych obiektów BLOB. W tym artykule przedstawiono sposób tworzenia migawek obiektów blob i zarządzania nimi przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Informacje o migawek obiektów BLOB

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Migawka obiektu BLOB jest taka sama jak jego podstawowy obiekt BLOB, z tą różnicą, że identyfikator URI obiektu BLOB ma dołączoną wartość **DateTime** do identyfikatora URI obiektu BLOB, aby wskazać godzinę utworzenia migawki. Na przykład jeśli identyfikator URI strony ma `http://storagesample.core.blob.windows.net/mydrives/myvhd`wartość, identyfikator URI migawki jest podobny do. `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`

> [!NOTE]
> Wszystkie migawki korzystają z identyfikatora URI podstawowego obiektu BLOB. Jedyną różnicą między bazowym obiektem blob a migawką jest dołączona wartość **DateTime** .
>

Obiekt BLOB może zawierać dowolną liczbę migawek. Migawki są utrwalane, dopóki nie zostaną jawnie usunięte, co oznacza, że migawka nie może na bieżąco odistnieć podstawowego obiektu BLOB. Można wyliczyć migawki skojarzone z podstawowym obiektem BLOB w celu śledzenia bieżących migawek.

Podczas tworzenia migawki obiektu BLOB właściwości systemu obiektu BLOB są kopiowane do migawki z tymi samymi wartościami. Metadane podstawowego obiektu BLOB są również kopiowane do migawki, o ile nie zostanie określone oddzielne metadane dla migawki podczas jej tworzenia. Po utworzeniu migawki można ją odczytywać, kopiować lub usuwać, ale nie można jej modyfikować.

Wszystkie dzierżawy skojarzone z podstawowym obiektem BLOB nie mają wpływu na migawkę. Nie można uzyskać dzierżawy na migawce.

Plik VHD jest używany do przechowywania bieżących informacji i stanu dla dysku maszyny wirtualnej. Możesz odłączyć dysk od maszyny wirtualnej lub zamknąć maszynę wirtualną, a następnie wykonać migawkę jego pliku VHD. Możesz użyć tego pliku migawek później, aby pobrać plik VHD w tym momencie i ponownie utworzyć maszynę wirtualną.

## <a name="create-a-snapshot"></a>Tworzenie migawki

Aby utworzyć migawkę blokowego obiektu BLOB, użyj jednej z następujących metod:

- [Utwórz migawkę](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Poniższy przykład kodu pokazuje, jak utworzyć migawkę. Ten przykład określa dodatkowe metadane podczas tworzenia migawki.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Usuwanie migawek

Aby usunąć obiekt BLOB, należy najpierw usunąć wszystkie migawki tego obiektu BLOB. Migawkę można usunąć pojedynczo lub określić, że wszystkie migawki zostaną usunięte po usunięciu źródłowego obiektu BLOB. Jeśli spróbujesz usunąć obiekt BLOB, który nadal zawiera migawki, zostanie zwrócony błąd.

Aby usunąć migawki obiektów blob, użyj jednej z następujących metod usuwania obiektów blob i Uwzględnij Wyliczenie [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) .

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Poniższy przykład kodu pokazuje, jak usunąć obiekt BLOB i jego migawki w programie .NET, gdzie `blockBlob` jest obiekt typu [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Zwróć bezwzględny identyfikator URI do migawki

Poniższy przykład kodu tworzy migawkę i zapisuje bezwzględny identyfikator URI dla lokalizacji głównej.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Zrozumienie, jak naliczane są opłaty za migawki

Utworzenie migawki, która jest kopią tylko do odczytu obiektu BLOB, może spowodować naliczenie dodatkowych opłat za magazyn danych dla Twojego konta. Podczas projektowania aplikacji ważne jest, aby mieć świadomość, jak te opłaty mogą zostać naliczone, aby można było zminimalizować koszty.

### <a name="important-billing-considerations"></a>Ważne zagadnienia dotyczące rozliczeń

Poniższa lista zawiera kluczowe kwestie, które należy wziąć pod uwagę podczas tworzenia migawki.

- Konto magazynu wiąże się z opłatami za unikatowe bloki lub strony, niezależnie od tego, czy znajdują się w obiekcie blob, czy w migawce. Twoje konto nie wiąże się z dodatkowymi opłatami za migawki skojarzone z obiektem BLOB do momentu zaktualizowania obiektu BLOB, na którym się znajdują. Po zaktualizowaniu podstawowego obiektu BLOB jest on rozbieżny od jego migawek. W takim przypadku opłata jest naliczana za unikatowe bloki lub strony w każdym obiekcie blob lub w migawce.
- Gdy zastąpisz blok w blokowym obiekcie blob, ten blok jest następnie naliczany jako unikatowy blok. Jest to prawdziwe, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które znajdują się w migawce. Po ponownym zatwierdzeniu bloku jest on rozbieżny od jego odpowiednika w dowolnej migawce i zostanie naliczona opłata za dane. Te same wartości mają wartość true dla strony w obiekcie blob stronicowania, która jest aktualizowana o identyczne dane.
- Zastępowanie blokowego obiektu BLOB przez wywołanie metody [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray][dotnet_UploadFromByteArray] zastępuje wszystkie bloki w obiekcie blob. Jeśli posiadasz migawkę skojarzoną z tym obiektem BLOB, wszystkie bloki w podstawowym obiekcie blob i migawek są teraz rozbieżne i zostanie naliczona opłata za wszystkie bloki w obu obiektach Blob. Jest to prawdziwe, nawet jeśli dane w podstawowym obiekcie blob i migawka pozostają identyczne.
- Blob service platformy Azure nie ma metody, aby określić, czy dwa bloki zawierają identyczne dane. Każdy przekazany i zatwierdzony blok jest traktowany jako unikatowy, nawet jeśli ma takie same dane i ten sam identyfikator bloku. Ze względu na to, że opłaty są naliczane dla unikatowych bloków, należy wziąć pod uwagę, że aktualizacja obiektu BLOB z migawką skutkuje dodatkowymi unikatowymi blokami i dodatkowymi opłatami.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizacja kosztów za pomocą zarządzania migawkami

Zalecamy staranne zarządzanie migawkami, aby uniknąć dodatkowych opłat. Poniższe najlepsze rozwiązania mogą pomóc zminimalizować koszty związane z magazynowaniem migawek:

- Usuń i ponownie utwórz migawki skojarzone z obiektem BLOB za każdym razem, gdy aktualizujesz obiekt BLOB, nawet jeśli aktualizujesz te same dane, chyba że projekt aplikacji wymaga utrzymania migawek. Przez usunięcie i ponowne utworzenie migawek obiektu BLOB można upewnić się, że obiekt BLOB i migawki nie są rozbieżne.
- Jeśli są zachowywane migawki obiektu BLOB, należy unikać wywoływania [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray][dotnet_UploadFromByteArray] w celu zaktualizowania obiektu BLOB. Te metody zastępują wszystkie bloki w obiekcie blob, co powoduje znaczne rozbieżność podstawowego obiektu BLOB i jego migawek. Zamiast tego należy zaktualizować możliwie najmniejszą liczbę bloków przy użyciu metod [PutBlock][dotnet_PutBlock] i [PutBlockList][dotnet_PutBlockList] .

### <a name="snapshot-billing-scenarios"></a>Scenariusze rozliczania migawek

W poniższych scenariuszach pokazano, jak naliczane są opłaty za blokowy obiekt BLOB i jego migawki.

#### <a name="scenario-1"></a>Scenariusz 1

W scenariuszu 1 podstawowy obiekt BLOB nie został zaktualizowany po wykonaniu migawki, więc opłaty są naliczane tylko dla unikatowych bloków 1, 2 i 3.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenariusz 2

W scenariuszu 2 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zaktualizowany, a mimo to zawiera te same dane i ten sam identyfikator, ale nie jest taki sam jak blok 3 w migawce. W związku z tym konto jest obciążane czterema blokami.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenariusz 3

W scenariuszu 3 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zastąpiony blokiem 4 w podstawowym obiekcie blob, ale migawka nadal odzwierciedla blok 3. W związku z tym konto jest obciążane czterema blokami.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenariusz 4

W scenariuszu 4 podstawowy obiekt BLOB został całkowicie zaktualizowany i nie zawiera żadnego z jego oryginalnych bloków. W związku z tym konto jest obciążane za wszystkie osiem unikatowych bloków. Ten scenariusz może wystąpić, jeśli używasz metody Update, takiej jak [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]lub [UploadFromByteArray][dotnet_UploadFromByteArray], ponieważ te metody zastępują całą zawartość obiektu BLOB.

![Zasoby usługi Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat pracy z migawkami dysków maszyn wirtualnych (VM) można znaleźć w temacie [Tworzenie kopii zapasowych dysków maszyny wirtualnej niezarządzanych przez platformę Azure przy użyciu migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md)

- Aby uzyskać dodatkowe przykłady kodu przy użyciu usługi BLOB Storage, zobacz [przykłady kodu platformy Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Możesz pobrać przykładową aplikację i uruchomić ją lub przejrzeć kod w witrynie GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext

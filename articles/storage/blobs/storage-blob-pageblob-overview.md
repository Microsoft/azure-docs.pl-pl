---
title: Omówienie obiektów BLOB na stronie platformy Azure | Microsoft Docs
description: Omówienie obiektów blob i ich zalet na stronie platformy Azure, w tym przypadków użycia z przykładowymi skryptami.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: add94fe05eecd2fb77ba0d6d79fe6765afe3baaa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091019"
---
# <a name="overview-of-azure-page-blobs"></a>Omówienie obiektów BLOB na stronie platformy Azure

Usługa Azure Storage oferuje trzy typy magazynu obiektów blob: blokowe obiekty blob, Dodawanie obiektów blob i stronicowe obiekty blob. Blokowe obiekty blob składają się z bloków i są idealnym rozwiązaniem do przechowywania plików tekstowych lub binarnych oraz wydajnego przekazywania dużych plików. Dołącz obiekty blob również składają się z bloków, ale są one zoptymalizowane pod kątem operacji dołączania, dzięki czemu są idealnym rozwiązaniem dla scenariuszy rejestrowania. Stronicowe obiekty blob składają się z 512-bajtowych stron o rozmiarze do 8 TB i są przeznaczone do częstego losowego wykonywania operacji odczytu i zapisu. Stronicowe obiekty blob są podstawą dysków usługi Azure IaaS. Ten artykuł koncentruje się na zrozumieniu funkcji i zalet stron obiektów BLOB.

Stronicowe obiekty blob to zbiór stron 512-bajtowych, co umożliwia odczytywanie i zapisywanie dowolnych zakresów bajtów. W związku z tym, stronicowe obiekty blob są idealne do przechowywania opartych na indeksach i rozrzedzonych struktur danych, takich jak dyski systemu operacyjnego i danych dla Virtual Machines i baz danych Na przykład usługa Azure SQL DB używa stronicowych obiektów BLOB jako podstawowego magazynu trwałego dla swoich baz danych. Ponadto stronicowe obiekty blob są również często używane dla plików z aktualizacjami Range-Based.  

Najważniejsze funkcje usługi Azure Page BLOB to interfejs REST, trwałość magazynu bazowego i bezproblemowe możliwości migracji na platformę Azure. Te funkcje zostały omówione bardziej szczegółowo w następnej sekcji. Ponadto obiekty blob na stronie platformy Azure są obecnie obsługiwane przez dwa typy magazynów: Premium Storage i magazyn w warstwie Standardowa. Premium Storage zaprojektowano specjalnie dla obciążeń wymagających spójnej wysokiej wydajności i małych opóźnień, dzięki czemu doskonałe obiekty blob są idealne dla scenariuszy magazynu o wysokiej wydajności. Standardowe konta magazynu są tańsze dla uruchamiania obciążeń niewrażliwych na opóźnienia.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Omówmy kilka przypadków użycia dla stronicowych obiektów blob, rozpoczynając od dysku IaaS platformy Azure. Obiekty blob na stronie platformy Azure są szkieletem platformy dysków wirtualnych dla usługi Azure IaaS. System operacyjny Azure i dyski danych są implementowane jako dyski wirtualne, na których dane są trwale trwałe na platformie Azure Storage, a następnie dostarczane do maszyn wirtualnych w celu zapewnienia maksymalnej wydajności. Dyski platformy Azure są utrwalane w [formacie wirtualnego dysku twardego](https://technet.microsoft.com/library/dd979539.aspx) funkcji Hyper-V i przechowywane jako [stronicowe obiekty blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) w usłudze Azure Storage. Oprócz używania dysków wirtualnych dla maszyn wirtualnych usługi Azure IaaS, stronicowe obiekty blob umożliwiają również scenariusze PaaS i DBaaS, takie jak usługa Azure SQL DB, która obecnie używa stronicowych obiektów BLOB do przechowywania danych SQL, umożliwiając szybkie losowe operacje odczytu i zapisu bazy danych. Innym przykładem jest to, że jeśli masz usługę PaaS do udostępniania dostępu do multimediów dla aplikacji do edycji filmów wideo, stronicowe obiekty blob umożliwiają szybki dostęp do losowo wybranych lokalizacji na nośniku. Umożliwia również szybkie i wydajne edytowanie i scalanie tego samego nośnika przez wielu użytkowników. 

Usługi firmy Microsoft w pierwszej kolejności, takie jak Azure Site Recovery, Azure Backup, a także wielu deweloperów innych firm wdrożyły wiodące w branży innowacje przy użyciu interfejsu REST obiektu BLOB strony. Poniżej przedstawiono niektóre z unikatowych scenariuszy wdrożonych na platformie Azure: 

* Zarządzanie migawkami przyrostowymi ukierunkowanymi na aplikacje: aplikacje mogą korzystać z migawek obiektów blob i interfejsów API REST w celu zapisywania punktów kontrolnych aplikacji bez ponoszenia kosztów duplikowania danych. Usługa Azure Storage obsługuje migawki lokalne dla stronicowych obiektów blob, które nie wymagają kopiowania całego obiektu BLOB. Te publiczne interfejsy API migawek umożliwiają również dostęp do i kopiowanie różnic między migawkami.
* Migracja na żywo aplikacji i danych z lokalizacji lokalnej do chmury: Skopiuj dane lokalne i Użyj interfejsów API REST, aby pisać bezpośrednio do obiektu BLOB na stronie platformy Azure, gdy na lokalnej maszynie wirtualnej nadal działa. Gdy obiekt docelowy zostanie przechwycony, możesz szybko przełączyć się do trybu failover na maszynie wirtualnej platformy Azure przy użyciu tych danych. W ten sposób można migrować maszyny wirtualne i dyski wirtualne z lokalizacji lokalnych do chmury z minimalnym przestojem, ponieważ migracja danych odbywa się w tle, a czas przestoju wymagany do przejścia w tryb failover będzie krótki (w minutach).
* Dostęp współdzielony [oparty na SAS](../common/storage-sas-overview.md) , który umożliwia scenariusze, takie jak wiele czytników i pojedynczy moduł zapisu z obsługą kontroli współbieżności.

## <a name="pricing"></a>Cennik

Oba typy magazynów oferowane przez stronicowe obiekty blob mają własny model cen. Na stronie Premium obiekty blob są zgodne z modelem cen dla dysków zarządzanych, podczas gdy standardowe obiekty blob na stronie standardowej są rozliczane przy użyciu rozmiaru i każdej transakcji. Aby uzyskać więcej informacji, zobacz [stronę cennika obiektów BLOB strony platformy Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Cechy stronicowego obiektu blob

### <a name="rest-api"></a>Interfejs API REST

Zapoznaj się z następującym dokumentem, aby rozpocząć [Programowanie przy użyciu stronicowych obiektów BLOB](storage-dotnet-how-to-use-blobs.md). Na przykład zapoznaj się z tematem jak uzyskać dostęp do stronicowych obiektów BLOB przy użyciu biblioteki klienta usługi Storage dla platformy .NET. 

Na poniższym diagramie opisano ogólne relacje między kontem, kontenerami i stronicowymi obiektami BLOB.

![Zrzut ekranu przedstawiający relacje między kontem, kontenerami i stronicowymi obiektami BLOB](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Tworzenie pustego obiektu BLOB stronicowego o określonym rozmiarze

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Najpierw Pobierz odwołanie do kontenera. Aby utworzyć stronicowy obiekt BLOB, wywołaj metodę [GetPageBlobClient](/dotnet/api/azure.storage.blobs.specialized.specializedblobextensions.getpageblobclient) , a następnie Wywołaj metodę [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) . Przekaż maksymalny rozmiar obiektu BLOB, który ma zostać utworzony. Ten rozmiar musi być wielokrotnością 512 bajtów.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

W celu utworzenia stronicowego obiektu BLOB najpierw tworzymy obiekt **CloudBlobClient** z podstawowym identyfikatorem URI uzyskiwania dostępu do magazynu obiektów BLOB dla konta magazynu (*pbaccount* na rysunku 1) wraz z obiektem **StorageCredentialsAccountAndKey** , jak pokazano w poniższym przykładzie. Przykład pokazuje, jak utworzyć odwołanie do obiektu **CloudBlobContainer** , a następnie utworzyć kontener (*testvhds*), jeśli jeszcze nie istnieje. Następnie za pomocą obiektu **CloudBlobContainer** Utwórz odwołanie do obiektu **CloudPageBlob** , określając nazwę obiektu BLOB (OS4. VHD), aby uzyskać dostęp. Aby utworzyć stronicowy obiekt BLOB, wywołaj [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), przekazując w maksymalnym rozmiarze dla obiektu BLOB do utworzenia. *BlobSize* musi być wielokrotnością 512 bajtów.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Zmienianie rozmiarów obiektu BLOB strony

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby zmienić rozmiar obiektu BLOB strony po utworzeniu, użyj metody [zmiany rozmiaru](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) . Żądany rozmiar powinien być wielokrotnością 512 bajtów.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby zmienić rozmiar obiektu BLOB strony po utworzeniu, użyj metody [zmiany rozmiaru](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . Żądany rozmiar powinien być wielokrotnością 512 bajtów.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Pisanie stron na stronie BLOB

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby pisać strony, użyj metody [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby pisać strony, użyj metody [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) .  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

Dzięki temu można napisać sekwencyjny zestaw stron do 4MBs. Przesunięcie, które jest zapisywane, musi rozpoczynać się od 512-bajtowej granicy (startingOffset %512 = = 0) i kończyć się na 512 granicy-1. 

Gdy tylko żądanie zapisu dla sekwencyjnego zestawu stron powiedzie się w usłudze BLOB Service i zostanie zreplikowane pod kątem trwałości i odporności, zapis zostanie zatwierdzony, a powodzenie zostanie zwrócone z powrotem do klienta.  

Poniższy diagram przedstawia 2 oddzielne operacje zapisu:

![Diagram przedstawiający dwie osobne opcje zapisu.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operacja zapisu rozpoczynająca się z przesunięciem 0 o długości 1024 bajtów 
2.  Operacja zapisu rozpoczynająca się o przesunięciu 4096 o długości 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Odczytywanie stron ze strony obiektu BLOB

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby odczytać strony, użyj metody [PageBlobClient. Download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) w celu odczytania zakresu bajtów ze strony obiektu BLOB. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby odczytać strony, należy użyć metody [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) w celu odczytania zakresu bajtów ze strony obiektu BLOB. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Dzięki temu można pobrać pełny obiekt BLOB lub zakres bajtów, zaczynając od dowolnego przesunięcia w obiekcie blob. Podczas odczytywania, przesunięcie nie musi rozpoczynać się od wielokrotności 512. Podczas odczytywania bajtów ze strony NUL usługa zwraca zero bajtów.

Na poniższej ilustracji przedstawiono operację odczytu z przesunięciem 256 i rozmiarem zakresu 4352. Zwrócone dane są wyróżnione w kolorze pomarańczowym. Dla stron NUL są zwracane wartości zerowe.

![Diagram przedstawiający operację odczytu z przesunięciem 256 i rozmiarem zakresu 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Jeśli masz rozrzedzonie wypełniony obiekt BLOB, możesz po prostu pobrać odpowiednie regiony stron, aby uniknąć płacenia za ruch wychodzący z zero bajtów i zmniejszyć czas oczekiwania na pobieranie.  

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby określić, które strony są obsługiwane przez dane, użyj [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). Następnie można wyliczyć zwrócone zakresy i pobrać dane z każdego zakresu. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby określić, które strony są obsługiwane przez dane, użyj [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Następnie można wyliczyć zwrócone zakresy i pobrać dane z każdego zakresu. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Dzierżawienie stronicowego obiektu BLOB

Operacja dzierżawy obiektu BLOB ustanawia i zarządza blokadą obiektu BLOB na potrzeby operacji zapisu i usuwania. Ta operacja jest przydatna w scenariuszach, w których dostęp do stronicowego obiektu BLOB jest uzyskiwany z wielu klientów w celu zapewnienia, że tylko jeden klient może zapisywać w obiekcie blob jednocześnie. Na przykład usługa Azure disks wykorzystuje ten mechanizm dzierżawienia w celu zapewnienia, że dysk jest zarządzany tylko przez jedną maszynę wirtualną. Czas trwania blokady może wynosić od 15 do 60 sekund lub może być nieskończony. [Aby uzyskać](/rest/api/storageservices/lease-blob) więcej informacji, zobacz dokumentację.

Oprócz rozbudowanych interfejsów API REST, stronicowe obiekty blob również zapewniają dostęp współużytkowany, trwałość i zwiększone zabezpieczenia. Te korzyści zostaną omówione bardziej szczegółowo w następnych akapitach. 

### <a name="concurrent-access"></a>Równoczesny dostęp

Interfejs API REST na stronie obiektów blob i jego mechanizm dzierżawienia umożliwiają aplikacjom dostęp do stronicowego obiektu BLOB z wielu klientów. Załóżmy na przykład, że konieczne jest skompilowanie rozproszonej usługi w chmurze, która współużytkuje obiekty magazynu z wieloma użytkownikami. Może to być aplikacja sieci Web obsługująca dużą kolekcję obrazów kilku użytkowników. Jedną z opcji wdrożenia tej usługi jest użycie maszyny wirtualnej z dołączonymi dyskami. Downsides tej dyrektywy obejmuje, (i) ograniczenie, że dysk może być dołączony tylko do jednej maszyny wirtualnej, co ogranicza skalowalność, elastyczność i zwiększanie ryzyka. Jeśli wystąpi problem z maszyną wirtualną lub usługą uruchomioną na maszynie wirtualnej, z powodu dzierżawy, obraz jest niedostępny do momentu wygaśnięcia dzierżawy lub jego przerwania. i (II) dodatkowe koszty związane z maszyną wirtualną IaaS. 

Alternatywną opcją jest użycie stronicowych obiektów BLOB bezpośrednio za pośrednictwem interfejsów API REST usługi Azure Storage. Ta opcja eliminuje konieczność kosztownych IaaS maszyn wirtualnych, oferuje pełną elastyczność bezpośredniego dostępu z wielu klientów, upraszcza klasyczny model wdrażania, eliminując konieczność dołączania/odłączania dysków i eliminuje ryzyko problemów z maszyną wirtualną. I zapewnia ten sam poziom wydajności dla losowych operacji odczytu/zapisu jako dysk

### <a name="durability-and-high-availability"></a>Trwałość i wysoka dostępność

Magazyny w warstwach Standardowa i Premium są trwałe, w których dane obiektów BLOB są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Aby uzyskać więcej informacji o nadmiarowości usługi Azure Storage, zobacz tę [dokumentację](../common/storage-redundancy.md). Platforma Azure stale dostarcza trwałość klasy korporacyjnej dla dysków IaaS i stronicowych obiektów blob, co wiodący w branży procentowy [Współczynnik błędów](https://en.wikipedia.org/wiki/Annualized_failure_rate)wynosi zero.

### <a name="seamless-migration-to-azure"></a>Bezproblemowe Migrowanie na platformę Azure

W przypadku klientów i deweloperów, którzy chcą zaimplementować własne dostosowane rozwiązanie do tworzenia kopii zapasowych, platforma Azure oferuje również migawki przyrostowe, które zawierają tylko te zmiany. Ta funkcja pozwala uniknąć ponoszenia kosztów początkowej pełnej kopii, co znacznie obniża koszt kopii zapasowej. Wraz z możliwością wydajnego odczytywania i kopiowania danych różnicowych jest to kolejna funkcja, która zapewnia jeszcze więcej innowacji od deweloperów, co prowadzi do najlepszych w swojej klasie środowiska tworzenia kopii zapasowych i odzyskiwania po awarii na platformie Azure. Możesz skonfigurować własne rozwiązanie do tworzenia kopii zapasowych lub odzyskiwania po awarii dla maszyn wirtualnych na platformie Azure przy użyciu [migawki obiektów BLOB](/rest/api/storageservices/snapshot-blob) wraz z interfejsem API [pobierania zakresów stron](/rest/api/storageservices/get-page-ranges) i API [przyrostowej kopii](/rest/api/storageservices/incremental-copy-blob) zapasowej, którego można użyć do łatwego kopiowania przyrostowych danych do odzyskiwania po awarii. 

Ponadto wiele przedsiębiorstw ma krytyczne obciążenia już uruchomione w lokalnych centrach danych. W przypadku migrowania obciążenia do chmury jednym z najważniejszych problemów będzie czas przestoju wymagany do skopiowania danych oraz ryzyko nieprzewidzianych problemów po przejściu. W wielu przypadkach przestoje mogą być showstopper do migracji do chmury. Korzystając z interfejsu API REST obiektów blob, platforma Azure rozwiązuje ten problem przez włączenie migracji w chmurze z minimalnym zakłóceniem do obciążeń krytycznych. 

Aby zapoznać się z przykładami dotyczącymi tworzenia migawek i sposobu przywracania stronicowego obiektu BLOB z migawki, zapoznaj się z artykułem [Konfigurowanie procesu tworzenia kopii zapasowej za pomocą migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md) .

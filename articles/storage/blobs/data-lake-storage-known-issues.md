---
title: Znane problemy dotyczące Azure Data Lake Storage Gen2 | Microsoft Docs
description: Poznaj ograniczenia i znane problemy dotyczące Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 1c887093972507904b007c696214708eb0e2b039
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282196"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy z Azure Data Lake Storage Gen2

W tym artykule opisano ograniczenia i znane problemy dotyczące Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje usługi Blob Storage

Rosnąca liczba funkcji magazynu obiektów BLOB działa teraz z kontami, które mają hierarchiczną przestrzeń nazw. Aby uzyskać pełną listę, zobacz [BLOB Storage funkcje dostępne w programie Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Azure Data Lake Storage Gen2 obsługuje kilka usług platformy Azure, których można użyć do pozyskiwania danych, wykonywania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy open source

Kilka platform Open Source obsługuje Data Lake Storage Gen2. Aby uzyskać pełną listę, zobacz [platformy typu "open source" obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Zobacz [platformę Open Source, która obsługuje Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Interfejsy API magazynu obiektów BLOB

Interfejsy API obiektów blob i interfejsy API Data Lake Storage Gen2 mogą działać na tych samych danych.

W tej sekcji opisano problemy i ograniczenia dotyczące używania interfejsów API obiektów blob i interfejsów API Data Lake Storage Gen2 do działania na tych samych danych.

* Do zapisu w tym samym wystąpieniu pliku nie można używać interfejsów API i Data Lake Storage. W przypadku zapisywania do pliku przy użyciu Data Lake Storage Gen2 interfejsów API, bloki tego pliku nie będą widoczne dla wywołań interfejsu API [pobierania listy zablokowanych](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Jedynym wyjątkiem jest zastępowanie. Można zastąpić plik/obiekt BLOB przy użyciu dowolnego interfejsu API.

* W przypadku korzystania z operacji [list obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez określania ogranicznika wyniki będą obejmować zarówno katalogi, jak i obiekty blob. Jeśli zdecydujesz się użyć ogranicznika, użyj tylko ukośnika ( `/` ). Jest to jedyny obsługiwany ogranicznik.

* Jeśli używasz interfejsu API [usuwania obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) do usuwania katalogu, ten katalog zostanie usunięty tylko wtedy, gdy jest pusty. Oznacza to, że nie można używać usługi Blob API Delete katalogów rekursywnie.

Te interfejsy API REST obiektów BLOB nie są obsługiwane:

* [Umieść obiekt BLOB (strona)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Umieść stronę](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Pobierz zakresy stron](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Obiekt BLOB kopiowania przyrostowego](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Umieść stronę na podstawie adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Dołącz blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Niezarządzane dyski maszyny wirtualnej nie są obsługiwane na kontach z hierarchiczną przestrzenią nazw. Jeśli chcesz włączyć hierarchiczną przestrzeń nazw na koncie magazynu, umieść niezarządzane dyski maszyn wirtualnych na koncie magazynu, w którym nie jest włączona funkcja hierarchicznej przestrzeni nazw.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Obsługa ustawiania listy kontroli dostępu (ACL) rekursywnie

Możliwość stosowania zmian listy ACL rekursywnie z katalogu nadrzędnego do elementów podrzędnych jest w [publicznej wersji zapoznawczej](recursive-access-control-lists.md). W bieżącej wersji tej możliwości można zastosować zmiany ACL przy użyciu programu PowerShell, zestawu .NET SDK i zestawu Python SDK. Pomoc techniczna nie jest jeszcze dostępna dla zestawu Java SDK, interfejsu wiersza polecenia platformy Azure, Azure Portal lub Eksplorator usługi Azure Storage.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>Narzędzie AzCopy

Użyj tylko najnowszej wersji AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Wcześniejsze wersje AzCopy, takie jak AzCopy v 8.1, nie są obsługiwane.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

Używaj tylko wersji  `1.6.0`   lub nowszej.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Eksplorator usługi Storage w Azure Portal

Listy ACL nie są jeszcze obsługiwane.

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>Aplikacje innych firm

Aplikacje innych firm, które używają interfejsów API REST do pracy, będą nadal działały w przypadku używania ich z Data Lake Storage Gen2 aplikacjami, które wywołują interfejsy API obiektów blob, prawdopodobnie będą działały.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listy kontroli dostępu (ACL) i anonimowy dostęp do odczytu

Jeśli [anonimowy dostęp do odczytu](storage-manage-access-to-resources.md) został przyznany do kontenera, listy ACL nie mają wpływu na ten kontener ani pliki w tym kontenerze.

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Ustawienie dni przechowywania nie jest jeszcze obsługiwane, ale można je usunąć ręcznie przy użyciu dowolnego obsługiwanego narzędzia, takiego jak Eksplorator usługi Azure Storage, REST lub zestaw SDK.

## <a name="issues-specific-to-premium-performance-blockblobstorage-storage-accounts"></a>Problemy specyficzne dla kont magazynu BlockBlobStorage wydajności Premium

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Nie można jeszcze włączyć dzienników diagnostycznych przy użyciu Azure Portal. Można je włączyć przy użyciu programu PowerShell. Na przykład:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

- Zasady zarządzania cyklem życia są obsługiwane tylko w przypadku kont ogólnego przeznaczenia w wersji 2. Nie są one jeszcze obsługiwane na kontach magazynu BlockBlobStorage w warstwie Premium.
- Nie można przenieść danych z warstwy Premium do niższej warstwy.


### <a name="hdinsight-support"></a>Obsługa usługi HDInsight

Podczas tworzenia klastra usługi HDInsight n nie można jeszcze wybrać konta BlockBlobStorage, na którym jest włączona funkcja hierarchicznej przestrzeni nazw. Można jednak dołączyć konto do klastra po jego utworzeniu.

### <a name="dremio-support"></a>Obsługa Dremio

Dremio jeszcze nie nawiązuje połączenia z kontem BlockBlobStorage, na którym jest włączona funkcja hierarchicznej przestrzeni nazw. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Sterownik systemu Windows Azure Storage Blob (WASB) (nieobsługiwany z Data Lake Storage Gen2)

Obecnie sterownik WASB, który został zaprojektowany do pracy z interfejsem API obiektów blob, napotyka problemy w kilku typowych scenariuszach. W przypadku klienta do konta magazynu z włączoną obsługą hierarchicznej przestrzeni nazw. Dostęp z dostępem do kilku protokołów Data Lake Storage nie będzie ograniczać tych problemów. 

Na czas (i najprawdopodobniej przyszłej przyszłości) nie będą obsługiwani klienci korzystający ze sterownika WASB jako klienta do konta magazynu o hierarchicznej przestrzeni nazw. Zamiast tego zaleca się, aby w środowisku Hadoop używać sterownika [systemu plików obiektów blob platformy Azure (ABFS)](data-lake-storage-abfs-driver.md) . Jeśli próbujesz przeprowadzić migrację z lokalnego środowiska Hadoop z wersją wcześniejszą niż usługa Hadoop, należy otworzyć bilet pomocy technicznej systemu Azure, dzięki czemu będziemy mogli skontaktować się z Tobą w sprawie właściwej ścieżki do przodu i Twojej organizacji.

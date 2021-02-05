---
title: Znane problemy dotyczące Azure Data Lake Storage Gen2 | Microsoft Docs
description: Poznaj ograniczenia i znane problemy dotyczące Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: db55a29e53c53e26fbb04d96ddf1864946bafbfd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581282"
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

* Nie można użyć interfejsu API obiektów blob i Data Lake Storage interfejsów API do zapisu w tym samym wystąpieniu pliku. W przypadku zapisywania do pliku przy użyciu Data Lake Storage Gen2 interfejsów API, bloki tego pliku nie będą widoczne dla wywołań interfejsu API [pobierania listy zablokowanych](/rest/api/storageservices/get-block-list) . Jedynym wyjątkiem jest zastępowanie. Można zastąpić plik/obiekt BLOB przy użyciu dowolnego interfejsu API.

* W przypadku korzystania z operacji [list obiektów BLOB](/rest/api/storageservices/list-blobs) bez określania ogranicznika wyniki będą obejmować zarówno katalogi, jak i obiekty blob. Jeśli zdecydujesz się użyć ogranicznika, użyj tylko ukośnika ( `/` ). Jest to jedyny obsługiwany ogranicznik.

* Jeśli używasz interfejsu API [usuwania obiektów BLOB](/rest/api/storageservices/delete-blob) do usuwania katalogu, ten katalog zostanie usunięty tylko wtedy, gdy jest pusty. Oznacza to, że nie można używać usługi Blob API Delete katalogów rekursywnie.

Te interfejsy API REST obiektów BLOB nie są obsługiwane:

* [Umieść obiekt BLOB (strona)](/rest/api/storageservices/put-blob)
* [Umieść stronę](/rest/api/storageservices/put-page)
* [Pobierz zakresy stron](/rest/api/storageservices/get-page-ranges)
* [Obiekt BLOB kopiowania przyrostowego](/rest/api/storageservices/incremental-copy-blob)
* [Umieść stronę na podstawie adresu URL](/rest/api/storageservices/put-page-from-url)

Niezarządzane dyski maszyny wirtualnej nie są obsługiwane na kontach z hierarchiczną przestrzenią nazw. Jeśli chcesz włączyć hierarchiczną przestrzeń nazw na koncie magazynu, umieść niezarządzane dyski maszyn wirtualnych na koncie magazynu, w którym nie jest włączona funkcja hierarchicznej przestrzeni nazw.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Obsługa ustawiania listy kontroli dostępu (ACL) rekursywnie

Możliwość stosowania zmian listy ACL rekurencyjnie z katalogu nadrzędnego do elementów podrzędnych jest ogólnie dostępna. W bieżącej wersji tej możliwości można zastosować zmiany ACL przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure oraz zestawu .NET, Java i Python SDK. Pomoc techniczna nie jest jeszcze dostępna dla Azure Portal lub Eksplorator usługi Azure Storage.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Użyj tylko najnowszej wersji AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Wcześniejsze wersje AzCopy, takie jak AzCopy v 8.1, nie są obsługiwane.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

Używaj tylko wersji `1.6.0` lub nowszej.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Eksplorator usługi Storage w Azure Portal

Listy ACL nie są jeszcze obsługiwane.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Aplikacje innych firm

Aplikacje innych firm, które używają interfejsów API REST do pracy, będą nadal działały w przypadku używania ich z Data Lake Storage Gen2 aplikacjami, które wywołują interfejsy API obiektów blob, prawdopodobnie będą działały.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listy kontroli dostępu (ACL) i anonimowy dostęp do odczytu

Jeśli [anonimowy dostęp do odczytu](./anonymous-read-access-configure.md) został przyznany do kontenera, listy ACL nie mają wpływu na ten kontener ani pliki w tym kontenerze.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Ustawienie dni przechowywania nie jest jeszcze obsługiwane, ale można je usunąć ręcznie przy użyciu dowolnego obsługiwanego narzędzia, takiego jak Eksplorator usługi Azure Storage, REST lub zestaw SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Zasady zarządzania cyklem życia z warstwą Premium dla Azure Data Lake Storage

Nie można przenieść danych przechowywanych w warstwie Premium między warstwami gorąca, chłodna i archiwalna. Można jednak skopiować dane z warstwy Premium do warstwy dostępu gorąca na innym koncie.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Obsługa Dremio z kontami magazynu BlockBlobStorage w warstwie Premium

Dremio jeszcze nie nawiązuje połączenia z kontem BlockBlobStorage, na którym jest włączona funkcja hierarchicznej przestrzeni nazw. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Sterownik systemu Windows Azure Storage Blob (WASB) (nieobsługiwany z Data Lake Storage Gen2)

Obecnie sterownik WASB, który został zaprojektowany do pracy z interfejsem API obiektów blob, napotyka problemy w kilku typowych scenariuszach. W przypadku klienta do konta magazynu z włączoną obsługą hierarchicznej przestrzeni nazw. Dostęp z dostępem do kilku protokołów Data Lake Storage nie będzie ograniczać tych problemów. 

Na czas (i najprawdopodobniej przyszłej przyszłości) nie będą obsługiwani klienci korzystający ze sterownika WASB jako klienta do konta magazynu o hierarchicznej przestrzeni nazw. Zamiast tego zaleca się, aby w środowisku Hadoop używać sterownika [systemu plików obiektów blob platformy Azure (ABFS)](data-lake-storage-abfs-driver.md) . Jeśli próbujesz przeprowadzić migrację z lokalnego środowiska Hadoop z wersją wcześniejszą niż usługa Hadoop, należy otworzyć bilet pomocy technicznej systemu Azure, dzięki czemu będziemy mogli skontaktować się z Tobą w sprawie właściwej ścieżki do przodu i Twojej organizacji.
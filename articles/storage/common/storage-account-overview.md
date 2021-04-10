---
title: Omówienie kont magazynu
titleSuffix: Azure Storage
description: Dowiedz się więcej o różnych typach kont magazynu w usłudze Azure Storage. Przejrzyj nazwy kont, warstwy wydajności, warstwy dostępu, nadmiarowość, szyfrowanie, punkty końcowe i nie tylko.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d7eca7d8f3cd40f4a3961f0ac478fba290be3041
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279667"
---
# <a name="storage-account-overview"></a>Omówienie kont magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie usługi Azure Storage są trwałe i wysoce dostępne, bezpieczne i skalowalne.

Aby dowiedzieć się, jak utworzyć konto magazynu usługi Azure Storage, zobacz [Tworzenie konta magazynu](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Te różnice należy wziąć pod uwagę przed utworzeniem konta magazynu w celu określenia typu konta najlepszego dla aplikacji. Oto typy kont magazynu:

- **Konta ogólnego przeznaczenia w wersji 2**: typ konta magazynu podstawowego dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy korzystających z usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1**: typ konta starsze dla obiektów blob, plików, kolejek i tabel. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.
- **Konta BlockBlobStorage**: konta magazynu z charakterystyką wydajności Premium dla blokowych obiektów blob i dołączanie obiektów BLOB. Zalecane w scenariuszach mających wysokie stawki transakcji lub scenariusze, które używają mniejszych obiektów lub wymagają spójnego niskiego opóźnienia magazynu.
- **Konta FileStorage**: tylko pliki magazynu z charakterystyką wydajności Premium. Zalecane w przypadku aplikacji do skalowania w przedsiębiorstwie lub wysokiej wydajności.
- **Konta BlobStorage**: starsze konta magazynu tylko na poziomie obiektów BLOB. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.

W poniższej tabeli opisano typy kont magazynu, obsługiwanych przez nich usług oraz obsługiwane modele wdrażania dla każdego typu konta:

| Typ konta magazynu | Obsługiwane usługi | Opcje nadmiarowości | Model wdrażania<sup>1</sup> |
|--|--|--|--|
| Ogólnego przeznaczenia w wersji 2 | Obiektów blob, plików, kolejek, tabel, dysków i Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| Ogólnego przeznaczenia w wersji 1 | Obiekt BLOB, plik, kolejka, tabela i dysk | LRS, GRS, RA-GRS | Menedżer zasobów, klasyczny |
| BlockBlobStorage | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | LRS, ZRS<sup>3</sup> | Resource Manager |
| FileStorage | Tylko plik | LRS, ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup> Zalecane jest korzystanie z modelu wdrażania Azure Resource Manager. Konta magazynu korzystające z klasycznego modelu wdrażania można nadal tworzyć w niektórych lokalizacjach, a istniejące konta klasyczne są nadal obsługiwane. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager a wdrożenie klasyczne: Omówienie modeli wdrażania i stanu zasobów](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na usłudze Azure Blob Storage. Data Lake Storage Gen2 jest obsługiwana tylko na kontach magazynu ogólnego przeznaczenia w wersji 2 z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji na temat Data Lake Storage Gen2, zobacz [wprowadzenie do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> Magazyn strefowo nadmiarowy (ZRS) i geograficznie nadmiarowy magazyn (GZRS/RA-GZRS) są dostępne tylko dla kont standardowych ogólnego przeznaczenia w wersji 2, BlockBlobStorage i FileStorage w określonych regionach. Aby uzyskać więcej informacji na temat opcji nadmiarowości usługi Azure Storage, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Nadmiarowość konta magazynu

Opcje nadmiarowości dla konta magazynu obejmują:

- **Magazyn lokalnie nadmiarowy (LRS)**: prosta, tania strategia nadmiarowości. Dane są kopiowane synchronicznie trzykrotnie w jednej lokalizacji fizycznej w regionie podstawowym.
- **Magazyn strefowo nadmiarowy (ZRS)**: nadmiarowość dla scenariuszy wymagających wysokiej dostępności. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym.
- **Magazyn Geograficznie nadmiarowy (GRS)**: wieloregionalna nadmiarowość do ochrony przed awarią regionalną. Dane są kopiowane synchronicznie do regionu podstawowego, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS).
- **Magazyn Geograficznie nadmiarowy (GZRS)**: nadmiarowość dla scenariuszy wymagających zarówno wysokiej dostępności, jak i maksymalnej trwałości. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz strefę geograficzną z dostępem do odczytu (RA-GZRS).

Aby uzyskać więcej informacji na temat opcji nadmiarowości w usłudze Azure Storage, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Konta ogólnego przeznaczenia, wersja 2

Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują najnowsze funkcje usługi Azure Storage i obejmują wszystkie funkcje kont w ramach ogólnego przeznaczenia w wersji 1 i usługi BLOB Storage. Konta ogólnego przeznaczenia w wersji 2 zapewniają najniższy poziom wydajności dla usługi Azure Storage, a także konkurencyjne w branży ceny transakcji. Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują te usługi Azure Storage:

- Obiekty blob (wszystkie typy: Block, append, Page)
- Data Lake Gen2
- Pliki
- Dyski
- Kolejki
- Tabele

> [!NOTE]
> Firma Microsoft zaleca korzystanie z konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Możesz łatwo uaktualnić konto do ogólnego przeznaczenia w wersji 1 lub do wersji 2, które nie ma przestoju i bez konieczności kopiowania danych. Nie można jednak cofnąć uaktualnienia.
>
> Aby uzyskać więcej informacji na temat uaktualniania do konta ogólnego przeznaczenia w wersji 2, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md).

Konta magazynu ogólnego przeznaczenia w wersji 2 oferują wiele warstw dostępu do przechowywania danych na podstawie wzorców użytkowania. Aby uzyskać więcej informacji, zobacz [warstwy dostępu dla danych blokowych obiektów BLOB](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Konta ogólnego przeznaczenia, wersja 1

Konta magazynu ogólnego przeznaczenia w wersji 1 zapewniają dostęp do wszystkich usług Azure Storage, ale mogą nie mieć najnowszych funkcji lub najniższych cen za gigabajt. Konta magazynu ogólnego przeznaczenia w wersji 1 obsługują te usługi Azure Storage:

- Obiekty blob (wszystkie typy)
- Pliki
- Dyski
- Kolejki
- Tabele

W tych scenariuszach można używać kont ogólnego przeznaczenia w wersji 1:

- Twoje aplikacje wymagają klasycznego modelu wdrażania platformy Azure. Konta ogólnego przeznaczenia w wersji 2 i konta magazynu obiektów BLOB obsługują tylko Azure Resource Manager model wdrażania.

- Aplikacje zajmują wiele transakcji lub wykorzystują znaczną przepustowość replikacji geograficznej, ale nie wymagają dużej pojemności. W takim przypadku, ogólnego przeznaczenia w wersji 1 może być najbardziej ekonomiczny.

- Używana jest wersja [interfejsu API REST usług Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) , która jest wcześniejsza niż 2014-02-14 lub Biblioteka kliencka o wersji niższej niż 4. x. Nie można uaktualnić aplikacji.

> [!NOTE]
> Mimo że firma Microsoft zaleca konta ogólnego przeznaczenia w wersji 2 dla większości scenariuszy, firma Microsoft będzie nadal obsługiwała konta ogólnego przeznaczenia w wersji 1 dla nowych i istniejących klientów. W nowych regionach można tworzyć konta magazynu ogólnego przeznaczenia w wersji 1, gdy usługa Azure Storage jest dostępna w tych regionach. Firma Microsoft nie ma obecnie planu na wycofanie pomocy technicznej dla kont ogólnego przeznaczenia w wersji 1 i udostępnienie co najmniej jednego roku przed wycofaniem jakiejkolwiek funkcji usługi Azure Storage. Firma Microsoft będzie nadal dostarczać aktualizacje zabezpieczeń dla kont ogólnego przeznaczenia w wersji 1, ale dla tego typu konta nie jest oczekiwane tworzenie nowych funkcji.
>
> Od 1 października 2020 ceny dla kont ogólnego przeznaczenia w wersji 1 w nowych regionach usługi Azure Storage są równoważne z cenami dla kont ogólnego przeznaczenia v2 w tych regionach. Cennik w istniejących regionach usługi Azure Storage nie został zmieniony. Szczegóły cennika kont ogólnego przeznaczenia w wersji 1 w określonym regionie znajdują się na stronie cennika usługi Azure Storage. Wybierz region, a następnie w obszarze **oferty cennika** wybierz pozycję **inne**.

### <a name="blockblobstorage-accounts"></a>Konta BlockBlobStorage

Konto BlockBlobStorage to wyspecjalizowane konto magazynu w warstwie wydajności Premium do przechowywania danych obiektów bez struktury jako blokowych obiektów blob lub dołączania obiektów BLOB. W porównaniu z kontami ogólnego przeznaczenia w wersji 2 i BlobStorage konta BlockBlobStorage zapewniają niskie, spójne opóźnienia i wyższe stawki transakcji.

Konta BlockBlobStorage nie obsługują obecnie warstw dostępu do warstwy gorąca, chłodna lub archiwalna. Ten typ konta magazynu nie obsługuje stronicowych obiektów blob, tabel lub kolejek.

### <a name="filestorage-accounts"></a>Konta FileStorage

Konto FileStorage to wyspecjalizowane konto magazynu używane do przechowywania i tworzenia udziałów plików w warstwie Premium. Ten rodzaj konta magazynu obsługuje pliki, ale nie blokuje obiektów blob, dołączanie obiektów blob, stronicowych obiektów blob, tabel lub kolejek.

Konta FileStorage oferują unikatowe cechy charakterystyczne dla wydajności, takie jak rozerwanie operacji we/wy. Aby uzyskać więcej informacji na temat tych cech, zobacz sekcję [warstwy magazynowania udziałów plików](../files/storage-files-planning.md#storage-tiers) w przewodniku planowania plików.

## <a name="naming-storage-accounts"></a>Nazewnictwo kont magazynu

Podczas określania nazwy konta magazynu należy pamiętać o następujących regułach:

- Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Każde konto magazynu musi mieć inną nazwę.

## <a name="performance-tiers"></a>Warstwy wydajności

W zależności od typu tworzonego konta magazynu można wybrać warstwę wydajności warstwy Standardowa i Premium. Poniższa tabela zawiera podsumowanie warstw wydajności dostępnych dla tego typu konta magazynu.

| Typ konta magazynu | Obsługiwane warstwy wydajności |
|--|--|
| Ogólnego przeznaczenia w wersji 2 | Standard, Premium<sup>1</sup> |
| Ogólnego przeznaczenia w wersji 1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standardowa (Standard) |

<sup>1</sup> Wydajność warstwy Premium dla kont ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1 jest dostępna tylko dla dysków i stron obiektów BLOB. Wydajność warstwy Premium dla obiektów BLOB bloków lub dołączania jest dostępna tylko na kontach BlockBlobStorage. Wydajność warstwy Premium dla plików jest dostępna tylko na kontach FileStorage.

### <a name="general-purpose-storage-accounts"></a>Konta magazynu ogólnego przeznaczenia

Konta magazynu ogólnego przeznaczenia można skonfigurować dla jednej z następujących warstw wydajności:

- Standardowa warstwa wydajności do przechowywania obiektów blob, plików, tabel, kolejek i dysków maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji o skalowalności dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](scalability-targets-standard-account.md).
- Warstwa wydajności Premium do przechowywania niezarządzanych dysków maszyn wirtualnych. Firma Microsoft zaleca używanie dysków zarządzanych z maszynami wirtualnymi platformy Azure zamiast dysków niezarządzanych. Aby uzyskać więcej informacji o skalowalności dla warstwy wydajności Premium, zobacz [elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Konta magazynu BlockBlobStorage

Konta magazynu BlockBlobStorage zapewniają warstwę wydajności Premium do przechowywania blokowych obiektów blob i dołączania obiektów BLOB. Aby uzyskać więcej informacji, zobacz [cele skalowalności dla kont magazynu blokowych obiektów BLOB w warstwie Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Konta magazynu FileStorage

Konta magazynu FileStorage zapewniają warstwę wydajności Premium dla udziałów plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Files celów skalowalności i wydajności](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Warstwy dostępu dla danych blokowych obiektów BLOB

Usługa Azure Storage oferuje różne opcje uzyskiwania dostępu do danych blokowych obiektów BLOB opartych na wzorcach użycia. Każda warstwa dostępu w usłudze Azure Storage jest zoptymalizowana pod kątem określonego wzorca użycia danych. Wybierając odpowiednią warstwę dostępu do Twoich potrzeb, możesz przechowywać dane blokowych obiektów BLOB w najbardziej opłacalny sposób.

Dostępne są następujące warstwy dostępu:

- Warstwa dostępu **gorąca** . Ta warstwa jest zoptymalizowana pod kątem częstego dostępu do obiektów na koncie magazynu. Uzyskiwanie dostępu do danych w warstwie gorąca jest najbardziej opłacalne, natomiast koszty magazynu są wyższe. Nowe konta magazynu są domyślnie tworzone w warstwie gorąca.
- Warstwa dostępu **chłodna** . Ta warstwa jest zoptymalizowana pod kątem przechowywania dużych ilości danych, które są rzadko używane i są przechowywane przez co najmniej 30 dni. Przechowywanie danych w warstwie chłodna jest tańsze, ale dostęp do tych danych może być droższy niż dostęp do danych w warstwie gorąca.
- Warstwa **Archiwum**. Ta warstwa jest dostępna tylko dla pojedynczych blokowych obiektów BLOB. Warstwa archiwum jest zoptymalizowana pod kątem danych, które mogą tolerować kilka godzin opóźnienia pobierania i które pozostaną w warstwie archiwum przez co najmniej 180 dni. Warstwa archiwum jest najtańszą opcją do przechowywania danych. Jednak dostęp do tych danych jest droższy niż dostęp do danych w warstwach gorąca lub chłodna.

W przypadku zmiany wzorca użycia danych można w dowolnym momencie przełączyć się między tymi warstwami dostępu. Aby uzyskać więcej informacji o warstwach dostępu, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md).

W poniższej tabeli przedstawiono warstwy dostępu dostępne dla obiektów BLOB w poszczególnych typach kont magazynu.

| Typ konta magazynu | Obsługiwane warstwy dostępu |
|--|--|
| Ogólnego przeznaczenia w wersji 2 | Gorąca, chłodna, Archiwum<sup>1</sup> |
| Ogólnego przeznaczenia w wersji 1 | Nie dotyczy |
| BlockBlobStorage | Nie dotyczy |
| FileStorage | Nie dotyczy |
| BlobStorage | Gorąca, chłodna, Archiwum<sup>1</sup> |

<sup>1</sup> magazyn archiwum i Obsługa warstw na poziomie obiektów BLOB obsługują tylko blokowe obiekty blob. Warstwa archiwum jest dostępna tylko na poziomie pojedynczego obiektu BLOB, a nie na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz [warstwy dostępu dla platformy Azure Blob Storage — gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Zmiana warstwy dostępu dla istniejącego konta magazynu lub obiektu BLOB może spowodować naliczenie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [rozliczenia kont magazynu](#storage-account-billing).

## <a name="storage-account-endpoints"></a>Punkty końcowe konta usługi Storage

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego usługi Azure Storage stanowi punkty końcowe konta magazynu.

W poniższej tabeli wymieniono punkty końcowe dla każdej usługi Azure Storage.

| Usługa magazynu | Punkt końcowy |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Usługa Azure Data Lake Storage 2. generacji | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> Blokowe obiekty blob i BLOB Storage uwidaczniają tylko Blob service punkt końcowy.

Utwórz adres URL do uzyskiwania dostępu do obiektu na koncie magazynu, dołączając lokalizację obiektu na koncie magazynu do punktu końcowego. Przykładowo adres obiektu Blob może mieć następujący format: http://*mojekontomagazynu*.blob.core.windows.net/*mojkontener*/*mojblob*.

Możesz również skonfigurować konto magazynu tak, aby korzystało z domeny niestandardowej dla obiektów BLOB. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Migrowanie konta magazynu

Poniższa tabela zawiera podsumowanie i wskazówki dotyczące przechodzenia, uaktualniania lub migrowania konta magazynu:

| Scenariusz migracji | Szczegóły |
|--|--|
| Przenoszenie konta magazynu do innej subskrypcji | Azure Resource Manager udostępnia opcje przeniesienia zasobu do innej subskrypcji. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Przenoszenie konta magazynu do innej grupy zasobów | Azure Resource Manager udostępnia opcje przeniesienia zasobu do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Przenoszenie konta magazynu do innego regionu | Aby przenieść konto magazynu, utwórz kopię konta magazynu w innym regionie. Następnie Przenieś dane do tego konta za pomocą AzCopy lub innego wybranego narzędzia. Aby uzyskać więcej informacji, zobacz [Przenoszenie konta usługi Azure Storage do innego regionu](storage-account-move.md). |
| Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2 | Możesz uaktualnić konto magazynu ogólnego przeznaczenia w wersji 1 lub konto magazynu obiektów BLOB do konta ogólnego przeznaczenia w wersji 2. Tej akcji nie można cofnąć. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md). |
| Migrowanie klasycznego konta magazynu do Azure Resource Manager | Model wdrażania Azure Resource Manager jest wyższy do klasycznego modelu wdrażania w zakresie funkcjonalności, skalowalności i zabezpieczeń. Aby uzyskać więcej informacji na temat migrowania klasycznego konta magazynu do Azure Resource Manager, zobacz [Migrowanie kont magazynu](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) w **ramach migracji obsługiwanej przez platformę zasobów IaaS z klasycznej do Azure Resource Manager**. |

## <a name="copying-data-into-a-storage-account"></a>Kopiowanie danych do konta magazynu

Firma Microsoft udostępnia narzędzia i biblioteki do importowania danych z lokalnych urządzeń magazynujących lub dostawców magazynu w chmurze innych firm. Używane rozwiązanie zależy od ilości danych, które są transferowane.

Po uaktualnieniu do konta ogólnego przeznaczenia w wersji 2 z poziomu konta ogólnego przeznaczenia w wersji 1 lub magazynu obiektów BLOB dane zostaną automatycznie zmigrowane. Firma Microsoft zaleca tej ścieżki do uaktualnienia konta. Jeśli jednak zdecydujesz się przenieść dane z konta ogólnego przeznaczenia w wersji 1 do konta usługi BLOB Storage, Przeprowadź migrację danych ręcznie przy użyciu narzędzi i bibliotek opisanych poniżej.

### <a name="azcopy"></a>AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia systemu Windows przeznaczone do kopiowania z wysoką wydajnością danych z i do usługi Azure Storage. Można użyć AzCopy do kopiowania danych do konta usługi BLOB Storage z istniejącego konta magazynu ogólnego zastosowania lub do przekazywania danych z lokalnych urządzeń magazynujących. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteka przenoszenia danych

Biblioteka przenoszenia danych usługi Azure Storage dla programu .NET jest oparta na podstawowym środowisku przenoszenia danych, w którym działa narzędzie AzCopy. Biblioteka została zaprojektowana na potrzeby przeprowadzania wysokowydajnych, niezawodnych i prostych operacji transferu danych w sposób podobny do narzędzia AzCopy. Możesz użyć biblioteki przenoszenia danych, aby korzystać z natywnych funkcji AzCopy. Aby uzyskać więcej informacji, zobacz [Biblioteka przenoszenia danych usługi Azure Storage dla platformy .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>Interfejs API REST lub biblioteka klienta

Możesz utworzyć niestandardową aplikację w celu migrowania danych z konta usługi Storage ogólnego przeznaczenia do konta usługi BLOB Storage. Użyj jednej z bibliotek klienta platformy Azure lub interfejsu API REST usług Azure Storage. Usługa Azure Storage udostępnia rozbudowane biblioteki dla wielu języków programowania i platform, takich jak .NET, Java, C++, Node.JS, PHP, Ruby i Python. Biblioteki klienta oferują zaawansowane możliwości, takie jak logika ponowień, rejestrowanie i przekazywanie równoległe. Możliwe jest również programowanie bezpośrednio przy użyciu interfejsu API REST, który może być wywoływany przez dowolny język programowania mający możliwość wysyłania żądań HTTP lub HTTPS.

Aby uzyskać więcej informacji na temat interfejsu API REST usługi Azure Storage, zobacz [Dokumentacja interfejsu API REST usług Azure Storage](/rest/api/storageservices/).

> [!IMPORTANT]
> Obiekty blob zaszyfrowane za pomocą szyfrowania po stronie klienta przechowują metadane związane z szyfrowaniem w ramach obiektu blob. W przypadku kopiowania obiektu blob zaszyfrowanego przy użyciu szyfrowania po stronie klienta upewnij się, że podczas operacji kopiowania są zachowywane metadane obiektu blob, a w szczególności metadane związane z szyfrowaniem. W przypadku kopiowania obiektu blob bez metadanych szyfrowania nie można ponownie pobrać zawartości tego obiektu. Aby uzyskać więcej informacji na temat metadanych związanych z szyfrowaniem, zobacz [Azure Storage Client-Side Encryption (Szyfrowanie po stronie klienta usługi Azure Storage)](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="encryption"></a>Szyfrowanie

Wszystkie dane na koncie magazynu są szyfrowane po stronie usługi. Aby uzyskać więcej informacji na temat szyfrowania, zobacz [Azure szyfrowanie usługi Storage w przypadku danych przechowywanych w spoczynku](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Rozliczanie konta usługi Storage

Rachunki usługi Azure Storage na podstawie użycia konta magazynu. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. Koszty magazynu są obliczane na podstawie następujących czynników:

- **Region** odnosi się do regionu geograficznego, w którym bazuje Twoje konto.
- **Typ konta** odnosi się do typu konta magazynu, którego używasz.
- **Warstwa dostępu** odnosi się do wzorca użycia danych określonego dla konta ogólnego przeznaczenia w wersji 2 lub BLOB Storage.
- **Pojemność** to stopień przydziału konta magazynu używanego do przechowywania danych.
- **Replikacja** określa liczbę kopii danych, które są przechowywane jednocześnie, oraz lokalizacje.
- **Transakcje** odnoszą się do wszystkich operacji odczytu i zapisu w usłudze Azure Storage.
- **Dane** wychodzące odnoszą się do wszystkich danych transferowanych z regionu platformy Azure. Po uzyskaniu dostępu do danych na koncie magazynu przez aplikację, która nie działa w tym samym regionie, opłaty są naliczone za wychodzące dane. Aby uzyskać informacje o korzystaniu z grup zasobów w celu grupowania danych i usług w tym samym regionie w celu ograniczenia opłat za ruch wychodzący, zobacz [co to jest Grupa zasobów platformy Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Strona [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) zawiera szczegółowe informacje o cenach w zależności od typu konta, pojemności magazynu, replikacji i transakcji. Artykuł [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/) zawiera szczegółowe informacje o cenach za wyjście danych. Artykuł [Kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) zawiera informacje, które ułatwią szacowanie kosztów.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](storage-account-create.md)
- [Tworzenie konta magazynu blokowych obiektów blob](../blobs/storage-blob-create-account-block-blob.md)
- [Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2](storage-account-upgrade.md)
- [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)
---
title: Omówienie kont magazynu
titleSuffix: Azure Storage
description: Zapoznaj się z omówieniem kont magazynu w usłudze Azure Storage. Przejrzyj nazwy kont, warstwy wydajności, warstwy dostępu, nadmiarowość, szyfrowanie, punkty końcowe i nie tylko.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 2c9c4cd643e2e4b89f9a7d8f44a6569d0dde2b37
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357385"
---
# <a name="storage-account-overview"></a>Omówienie kont magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie usługi Azure Storage są trwałe i wysoce dostępne, bezpieczne i skalowalne.

Aby dowiedzieć się, jak utworzyć konto magazynu usługi Azure Storage, zobacz [Tworzenie konta magazynu](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Konta ogólnego przeznaczenia, wersja 2

Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują najnowsze funkcje usługi Azure Storage i obejmują wszystkie funkcje kont w ramach ogólnego przeznaczenia w wersji 1 i usługi BLOB Storage. Konta ogólnego przeznaczenia w wersji 2 zapewniają najniższy poziom wydajności dla usługi Azure Storage, a także konkurencyjne w branży ceny transakcji. Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują te usługi Azure Storage:

- Obiekty blob (wszystkie typy: Block, append, Page)
- Data Lake Gen2
- Files
- Dyski
- Kolejki
- Tabele

> [!NOTE]
> Firma Microsoft zaleca korzystanie z konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Możesz łatwo uaktualnić konto do ogólnego przeznaczenia w wersji 1 lub do wersji 2, które nie ma przestoju i bez konieczności kopiowania danych.
>
> Aby uzyskać więcej informacji na temat uaktualniania do konta ogólnego przeznaczenia w wersji 2, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md).

Konta magazynu ogólnego przeznaczenia w wersji 2 oferują wiele warstw dostępu do przechowywania danych na podstawie wzorców użytkowania. Aby uzyskać więcej informacji, zobacz [warstwy dostępu dla danych blokowych obiektów BLOB](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Konta ogólnego przeznaczenia, wersja 1

Konta magazynu ogólnego przeznaczenia w wersji 1 zapewniają dostęp do wszystkich usług Azure Storage, ale mogą nie mieć najnowszych funkcji lub najniższych cen za gigabajt. Konta magazynu ogólnego przeznaczenia w wersji 1 obsługują te usługi Azure Storage:

- Obiekty blob (wszystkie typy)
- Files
- Dyski
- Kolejki
- Tabele

Firma Microsoft zaleca korzystanie z kont ogólnego przeznaczenia w wersji 2 w przypadku większości scenariuszy. W tych scenariuszach można używać kont ogólnego przeznaczenia w wersji 1:

- Twoje aplikacje wymagają klasycznego modelu wdrażania platformy Azure. Konta ogólnego przeznaczenia w wersji 2 i konta magazynu obiektów BLOB obsługują tylko Azure Resource Manager model wdrażania.

- Aplikacje zajmują wiele transakcji lub wykorzystują znaczną przepustowość replikacji geograficznej, ale nie wymagają dużej pojemności. W takim przypadku, ogólnego przeznaczenia w wersji 1 może być najbardziej ekonomiczny.

- Używana jest wersja [interfejsu API REST usług Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) , która jest wcześniejsza niż 2014-02-14 lub Biblioteka kliencka o wersji niższej niż 4. x. Nie można uaktualnić aplikacji.

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

W zależności od typu tworzonego konta magazynu można wybrać warstwę wydajności warstwy Standardowa i Premium.

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

> [!IMPORTANT]
> Zmiana warstwy dostępu dla istniejącego konta magazynu lub obiektu BLOB może spowodować naliczenie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [sekcję rozliczanie konta magazynu](#storage-account-billing).

## <a name="redundancy"></a>Nadmiarowość

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Szyfrowanie

Wszystkie dane na koncie magazynu są szyfrowane po stronie usługi. Aby uzyskać więcej informacji na temat szyfrowania, zobacz [Azure szyfrowanie usługi Storage w przypadku danych przechowywanych w spoczynku](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Punkty końcowe konta usługi Storage

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego usługi Azure Storage stanowi punkty końcowe konta magazynu.

Na przykład jeśli Twoje konto magazynu ogólnego przeznaczenia ma nazwę *mojekontomagazynu*, domyślne punkty końcowe dla tego konta są następujące:

- Magazyn obiektów blob: `https://*mystorageaccount*.blob.core.windows.net`
- Magazyn tabel: `https://*mystorageaccount*.table.core.windows.net`
- Kolejka magazynu: `https://*mystorageaccount*.queue.core.windows.net`
- Azure Files: `https://*mystorageaccount*.file.core.windows.net`
- Azure Data Lake Storage Gen2: `https://*mystorageaccount*.dfs.core.windows.net` (używa [sterownika ABFS zoptymalizowanego pod kątem danych Big Data](../blobs/data-lake-storage-introduction.md#key-features-of-data-lake-storage-gen2)).

> [!NOTE]
> Blokowe obiekty blob i BLOB Storage uwidaczniają tylko Blob service punkt końcowy.

Utwórz adres URL do uzyskiwania dostępu do obiektu na koncie magazynu, dołączając lokalizację obiektu na koncie magazynu do punktu końcowego. Przykładowo adres obiektu Blob może mieć następujący format: http://*mojekontomagazynu*.blob.core.windows.net/*mojkontener*/*mojblob*.

Możesz również skonfigurować konto magazynu tak, aby korzystało z domeny niestandardowej dla obiektów BLOB. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrolowanie dostępu do danych konta

Domyślnie dane na Twoim koncie są dostępne tylko dla Ciebie, tj. właściciela konta. Masz kontrolę nad tym, kto może uzyskiwać dostęp do danych i jakie posiadane uprawnienia.

Każde żądanie skierowane do konta magazynu musi mieć autoryzację. Na poziomie usługi żądanie musi zawierać prawidłowy nagłówek *autoryzacji* . W tym nagłówku uwzględniono wszystkie informacje niezbędne do zweryfikowania żądania przed jego wykonaniem przez usługę.

Dostęp do danych na koncie magazynu można udzielić przy użyciu dowolnych z następujących metod:

- **Azure Active Directory:** Użyj poświadczeń usługi Azure Active Directory (Azure AD) w celu uwierzytelnienia użytkownika, grupy lub innej tożsamości w celu uzyskania dostępu do danych obiektów blob i kolejek. W przypadku pomyślnego uwierzytelnienia tożsamości usługa Azure AD zwraca token do użycia w celu autoryzowania żądania do magazynu obiektów blob platformy Azure lub magazynu kolejek. Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](storage-auth-aad.md).
- **Autoryzacja klucza współużytkowanego:** Użyj klucza dostępu do konta magazynu, aby utworzyć parametry połączenia używane przez aplikację w czasie wykonywania w celu uzyskania dostępu do usługi Azure Storage. Wartości w parametrach połączenia są używane do konstruowania nagłówka *autoryzacji* , który jest przesyłany do usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Konfigurowanie parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md).
- **Sygnatura dostępu współdzielonego:** Sygnatura dostępu współdzielonego (SAS) to token, który umożliwia delegowany dostęp do zasobów na koncie magazynu. Token sygnatury dostępu współdzielonego hermetyzuje wszystkie informacje, które są konieczne do autoryzowania żądania do usługi Azure Storage przy użyciu adresu URL. Podczas tworzenia sygnatury dostępu współdzielonego można określić, które uprawnienia są przyznawane dla zasobu, a także interwał, w jakim uprawnienia są prawidłowe. Token sygnatury dostępu współdzielonego może być podpisany przy użyciu poświadczeń usługi Azure AD lub klucza współużytkowanego. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

> [!NOTE]
> Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w innych sposobach autoryzacji. Mimo że można nadal korzystać z autoryzacji klucza współużytkowanego w aplikacjach, korzystanie z usługi Azure AD powoduje obejście konieczności przechowywania klucza dostępu do konta w kodzie. Możesz również nadal używać sygnatur dostępu współdzielonego, aby udzielać szczegółowego dostępu do zasobów na koncie magazynu, ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami SAS ani martwić się o odwoływanie złamanych SAS.
>
> Firma Microsoft zaleca korzystanie z autoryzacji usługi Azure AD dla obiektów BLOB usługi Azure Storage i kolejkowanie aplikacji, o ile jest to możliwe.

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

## <a name="storage-account-billing"></a>Rozliczanie konta usługi Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](storage-account-create.md)
- [Tworzenie konta magazynu blokowych obiektów blob](../blobs/storage-blob-create-account-block-blob.md)
- [Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2](storage-account-upgrade.md)
- [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)
---
title: Omówienie kont magazynu
titleSuffix: Azure Storage
description: Dowiedz się więcej o różnych typach kont magazynu w usłudze Azure Storage. Zapoznaj się z nazwami kont, warstwami wydajności, warstwami dostępu, nadmiarowością, szyfrowaniem, punktami końcowymi i nie tylko.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714204"
---
# <a name="storage-account-overview"></a>Omówienie kont magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiekty blob, pliki, kolejki, tabele i dyski. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie usługi Azure Storage są trwałe, wysoce dostępne, bezpieczne i wysoce skalowalne.

Aby dowiedzieć się, jak utworzyć konto magazynu usługi Azure Storage, zobacz [Tworzenie konta magazynu](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Przed utworzeniem konta magazynu należy wziąć pod uwagę te różnice, aby określić typ konta, który jest najlepszy dla Twoich aplikacji.

W poniższej tabeli opisano typy kont magazynu zalecanych przez firmę Microsoft w przypadku większości scenariuszy:

| Typ konta magazynu | Obsługiwane usługi | Opcje nadmiarowości | Model wdrażania | Użycie |
|--|--|--|--|--|
| Standardowe ogólnego przeznaczenia, wersja 2 | Blob, File, Queue, Table i Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Podstawowy typ konta magazynu dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy korzystających z usługi Azure Storage. |
| Blokowe obiekty blob w wersji Premium<sup>4</sup> | Tylko blokowe obiekty blob | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Konta magazynu o charakterystyce wydajności Premium dla blokowych obiektów blob i uzupełnialne obiekty blob. Zalecane w przypadku scenariuszy z dużą prędkością transakcji lub scenariuszy, które używają mniejszych obiektów lub wymagają stale niskiego opóźnienia magazynu.<br />[Dowiedz się więcej...](../blobs/storage-blob-performance-tiers.md) |
| Udziały plików Premium<sup>4</sup> | Tylko udziały plików | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Konta magazynu tylko dla plików o charakterystyce wydajności Premium. Zalecane w przypadku aplikacji w skali przedsiębiorstwa lub o wysokiej wydajności.<br />[Dowiedz się więcej...](../files/storage-files-planning.md#management-concepts) |
| Stronicowe obiekty blob w premium<sup>4</sup> | Tylko stronicowe obiekty blob | LRS | Resource Manager<sup>3</sup> | Typ konta magazynu w chmurze Premium tylko dla stronicowych obiektów blob.<br />[Dowiedz się więcej...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage to zestaw funkcji przeznaczonych do analizy danych big data, zbudowany na usłudze Azure Blob Storage. Data Lake Storage jest obsługiwana tylko na kontach magazynu ogólnego przeznaczenia w wersji 2 z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji na Data Lake Storage Gen2, zobacz [Introduction to Data Lake Storage Gen2 (Wprowadzenie do Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)).

<sup>2</sup> Magazyn strefowo nadmiarowy (ZRS) i magazyn strefowo nadmiarowy geograficznie (GZRS/RA-GZRS) są dostępne tylko dla kont udziałów plików ogólnego przeznaczenia w standardowych wersjach 2, blokowych obiektów blob w chmurze Premium i premium w niektórych regionach. Aby uzyskać więcej informacji na temat opcji nadmiarowości usługi Azure Storage, zobacz [Nadmiarowość usługi Azure Storage.](storage-redundancy.md)

<sup>3</sup> Azure Resource Manager jest zalecanym modelem wdrażania zasobów platformy Azure, w tym kont magazynu. Aby uzyskać więcej informacji, [zobacz Resource Manager omówienie systemu](../../azure-resource-manager/management/overview.md).

<sup>4 Konta</sup> magazynu w warstwie wydajności Premium używają dysków półprzewodnikowych (SSD), aby uzyskać małe opóźnienia i wysoką przepływność.

Obsługiwane są również starsze konta magazynu. Aby uzyskać więcej informacji, zobacz [Starsze typy kont magazynu.](#legacy-storage-account-types)

## <a name="storage-account-endpoints"></a>Punkty końcowe konta usługi Storage

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego usługi Azure Storage stanowi punkty końcowe konta magazynu.

Podczas określania nazwy konta magazynu należy pamiętać o następujących regułach:

- Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Każde konto magazynu musi mieć inną nazwę.

W poniższej tabeli wymieniono format punktu końcowego dla każdej z usług Azure Storage.

| Usługa Magazynu | Punkt końcowy |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Skonstruuj adres URL do uzyskiwania dostępu do obiektu na koncie magazynu, dołączając lokalizację obiektu na koncie magazynu do punktu końcowego. Na przykład adres URL obiektu blob będzie podobny do:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Możesz również skonfigurować konto magazynu do używania domeny niestandardowej dla obiektów blob. Aby uzyskać więcej informacji, [zobacz Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage.](../blobs/storage-custom-domain-name.md)  

## <a name="migrating-a-storage-account"></a>Migrowanie konta magazynu

W poniższej tabeli przedstawiono podsumowanie i wskazówki dotyczące przenoszenia, uaktualniania lub migrowania konta magazynu:

| Scenariusz migracji | Szczegóły |
|--|--|
| Przenoszenie konta magazynu do innej subskrypcji | Azure Resource Manager opcje przenoszenia zasobu do innej subskrypcji. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Przenoszenie konta magazynu do innej grupy zasobów | Azure Resource Manager opcje przenoszenia zasobu do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Przenoszenie konta magazynu do innego regionu | Aby przenieść konto magazynu, utwórz kopię konta magazynu w innym regionie. Następnie przenieś dane na to konto przy użyciu narzędzia AzCopy lub innego wybranego narzędzia. Aby uzyskać więcej informacji, zobacz [Move an Azure Storage account to another region (Przenoszenie konta usługi Azure Storage do innego regionu).](storage-account-move.md) |
| Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2 | Możesz uaktualnić konto magazynu ogólnego przeznaczenia w wersji 1 lub konto usługi Blob Storage do konta ogólnego przeznaczenia w wersji 2. Należy pamiętać, że tej akcji nie można cofnąć. Aby uzyskać więcej informacji, zobacz [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2.](storage-account-upgrade.md) |
| Migrowanie klasycznego konta magazynu do Azure Resource Manager | Model Azure Resource Manager jest lepszy od klasycznego modelu wdrażania pod względem funkcjonalności, skalowalności i zabezpieczeń. Aby uzyskać więcej informacji na temat migrowania klasycznego [](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) konta magazynu do usługi Azure Resource Manager, zobacz Migracja kont magazynu w tece Migracja zasobów **IaaS** obsługiwana przez platformę z wersji klasycznej do usługi Azure Resource Manager . |

## <a name="transferring-data-into-a-storage-account"></a>Transferowanie danych na konto magazynu

Firma Microsoft udostępnia usługi i narzędzia do importowania danych z lokalnych urządzeń magazynujących lub dostawców magazynu w chmurze innych firm. To, którego rozwiązania używasz, zależy od ilości przesyłanych danych. Aby uzyskać więcej informacji, zobacz [Omówienie migracji do usługi Azure Storage.](storage-migration-overview.md)

## <a name="storage-account-encryption"></a>Wymaganie szyfrowania konta magazynu

Wszystkie dane na koncie magazynu są automatycznie szyfrowane po stronie usługi. Aby uzyskać więcej informacji na temat szyfrowania i zarządzania kluczami, zobacz Azure Storage encryption for data at rest (Szyfrowanie danych [magazynowych w usłudze Azure Storage).](storage-service-encryption.md)

## <a name="storage-account-billing"></a>Rozliczanie konta usługi Storage

Opłaty za usługę Azure Storage są naliczane na podstawie użycia konta magazynu. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. Koszty magazynowania są obliczane zgodnie z następującymi czynnikami:

- **Region** odnosi się do regionu geograficznego, w którym znajduje się Twoje konto.
- **Typ konta** odnosi się do typu konta magazynu, z których korzystasz.
- **Warstwa dostępu** odnosi się do wzorca użycia danych określonego dla konta ogólnego przeznaczenia w wersji 2 lub konta usługi Blob Storage.
- **Pojemność** odnosi się do tego, ile wyasygnowania konta magazynu używasz do przechowywania danych.
- **Nadmiarowość** określa, ile kopii danych jest utrzymywanych jednocześnie i w jakich lokalizacjach.
- **Transakcje odnoszą** się do wszystkich operacji odczytu i zapisu w usłudze Azure Storage.
- **Ruch wychodzący** danych odnosi się do wszystkich danych przesyłanych poza region świadczenia usługi Azure. Gdy dostęp do danych na koncie magazynu uzyskuje aplikacja, która nie jest uruchomiona w tym samym regionie, opłaty są naliczane za ruch wychodzący danych. Aby uzyskać informacje o używaniu grup zasobów do grupowania danych i usług w tym samym regionie w celu ograniczenia opłat za ruch wychodzący, zobacz Co to jest grupa zasobów [platformy Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Strona [cennika usługi Azure Storage zawiera](https://azure.microsoft.com/pricing/details/storage/) szczegółowe informacje o cenach na podstawie typu konta, pojemności magazynu, replikacji i transakcji. Szczegóły [cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/) zawierają szczegółowe informacje o cenach dla danych wychodzącego. Kalkulator cen [usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) umożliwia oszacowanie kosztów.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Starsze typy kont magazynu

W poniższej tabeli opisano starsze typy kont magazynu. Te typy kont nie są zalecane przez firmę Microsoft, ale mogą być używane w niektórych scenariuszach:

| Typ starszego konta magazynu | Obsługiwane usługi | Opcje nadmiarowości | Model wdrażania | Użycie |
|--|--|--|--|--|
| Standardowe ogólnego przeznaczenia, wersja 1 | Blob, File, Queue, Table i Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, klasyczny | Konta ogólnego przeznaczenia w wersji 1 mogą nie mieć najnowszych funkcji lub najniższych cen za gigabajt. Rozważ użycie w przypadku tych scenariuszy:<br /><ul><li>Aplikacje wymagają klasycznego modelu wdrażania platformy Azure.</li><li>Aplikacje intensywnie korzystają z transakcji lub używają znacznej przepustowości replikacji geograficznej, ale nie wymagają dużej pojemności. W takim przypadku najbardziej ekonomicznym wyborem może być wersja 1 ogólnego przeznaczenia.</li><li>Używasz wersji interfejsu API REST usługi Azure Storage wcześniejszej niż 2014-02-14 lub biblioteki klienta w wersji starszej niż 4.x i nie możesz uaktualnić aplikacji.</li></ul> |
| Magazyn obiektów blob w chmurze w chmurze | Obiekt blob (tylko blokowe i uzupełnialne obiekty blob) | LRS/GRS/RA-GRS | Resource Manager | Firma Microsoft zaleca używanie standardowych kont ogólnego przeznaczenia w wersji 2, jeśli jest to możliwe. |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](storage-account-create.md)
- [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md)
- [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)
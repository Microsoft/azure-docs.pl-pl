---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/20/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e2540bceab17e6f37fd94b28df3814ccffa1c81e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466843"
---
W poniższej tabeli opisano domyślne limity dla kont usługi Azure ogólnego przeznaczenia w wersji 1, v2, BLOB Storage i blokowych obiektów BLOB Storage. Limit *ruchu* przychodzącego odnosi się do wszystkich danych wysyłanych do konta magazynu. Limit *ruchu* wychodzącego odnosi się do wszystkich danych odebranych z konta magazynu.

| Zasób | Limit |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym konta usługi Standard i Premium Storage.| 250 |
| Maksymalna pojemność konta magazynu | 5 PiB <sup>1</sup>|
| Maksymalna liczba kontenerów obiektów blob, obiektów blob, udziałów plików, tabel, kolejek, jednostek lub komunikatów na konto magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (USA, regiony Europy) | 10 Gb/s |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (regiony inne niż Stany USA i Europa) | 5 GB/s, jeśli włączono RA-GRS/GRS, 10 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont ogólnego przeznaczenia w wersji 2 i BLOB Storage (wszystkie regiony) | 50 GB/s |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony USA) | 20 GB/s, jeśli włączono RA-GRS/GRS, 30 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony inne niż Stany USA) | 10 GB/s, jeśli włączono RA-GRS/GRS, 15 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba reguł sieci wirtualnej na konto magazynu | 200 |
| Maksymalna liczba reguł adresów IP na konto magazynu | 200 |

<sup>1</sup> standardowe konta usługi Azure Storage obsługują wyższe limity pojemności i wyższe limity dotyczące ruchu przychodzącego przez żądanie. Aby zażądać zwiększenia limitów kont, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Jeśli konto magazynu ma włączony dostęp do odczytu z magazynu geograficznie nadmiarowego (RA-GRS) lub strefy geograficznej nadmiarowej (Ra-GZRS), a następnie docelowe dane wyjściowe dla lokalizacji dodatkowej są identyczne z tymi, które znajdują się w lokalizacji głównej. Opcje [replikacji usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) obejmują:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na usłudze Azure Blob Storage.

> [!NOTE]
> Firma Microsoft zaleca użycie konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Możesz łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 1 lub Azure Blob Storage, aby nie było przestoju i bez konieczności kopiowania danych. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../articles/storage/common/storage-account-upgrade.md).

Jeśli wymagania aplikacji przekraczają tarcze skalowalności pojedynczego konta magazynu, możesz skompilować aplikację, aby używać wielu kont magazynu. Następnie można podzielić na partycje obiekty danych na kontach magazynu. Aby uzyskać informacje na temat cen zbiorczych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu są uruchamiane na płaskiej topologii sieci, niezależnie od tego, kiedy zostały utworzone. Aby uzyskać więcej informacji na temat architektury sieci płaskiej i skalowalności usługi Azure Storage, zobacz [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 

---
title: Przenoszenie magazynu mainframe do usługi Azure Storage
description: Wysoce skalowalne zasoby usługi Azure Storage mogą pomóc organizacjom opartym na mainframe, migrowaniu i modernizacji aplikacji IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954364"
---
# <a name="move-mainframe-storage-to-azure"></a>Przenoszenie magazynu mainframe na platformę Azure

Aby uruchamiać obciążenia mainframe na Microsoft Azure, musisz wiedzieć, jak możliwości komputera mainframe są porównywane z platformą Azure. Wysoce skalowalne zasoby magazynu mogą pomóc organizacjom rozpocząć modernizację bez opuszczania aplikacji, z których korzystają.

System Azure oferuje funkcje podobne do komputera typu mainframe i pojemność magazynu, które są porównywalne z systemami opartymi na systemie IBM z14 (najbardziej bieżącym modelem w przypadku tego zapisu). W tym artykule przedstawiono, jak uzyskać porównywalne wyniki na platformie Azure.

## <a name="mainframe-storage-at-a-glance"></a>Magazyn mainframe w skrócie

IBM mainframe charakteryzuje magazyn na dwa sposoby. Pierwszy to urządzenie magazynu bezpośredniego dostępu (DASD). Drugi to magazyn sekwencyjny. W celu zarządzania magazynem program mainframe zapewnia podsystem zarządzania magazynem (DFSMS). Umożliwia ona zarządzanie dostępem do danych do różnych urządzeń magazynujących.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odnosi się do oddzielnego urządzenia dla magazynu pomocniczego (nie znajdującego się w pamięci), który umożliwia używanie unikatowego adresu do bezpośredniego dostępu do danych. Pierwotnie termin DASD zastosowany do obracających się dysków, bębnów magnetycznych lub komórek danych. Teraz termin ten może również dotyczyć urządzeń magazynu półprzewodnikowego (dysków SSD), sieci magazynowania (San), magazynu podłączonego do sieci (NAS) i dysków optycznych. Na potrzeby tego dokumentu DASD odnoszą się do obracających się dysków, sieci SAN i dysków SSD.

W przeciwieństwie do magazynu DASD, sekwencyjny magazyn na komputerze mainframe odnosi się do urządzeń, takich jak stacje taśm, do których dane są dostępne z punktu początkowego, a następnie odczytywane lub zapisywane w wierszu.

Urządzenia magazynujące są zwykle dołączane przy użyciu połączenia Fiber (FICON) lub są dostępne bezpośrednio na magistrali we/wy komputera mainframe przy użyciu [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), technologii firmy IBM do szybkiej komunikacji między partycjami na serwerze z funkcją hypervisor.

Większość systemów mainframe oddziela magazyn do dwóch typów:

- *Magazyn online* (jest również znany jako magazyn gorąca) jest wymagany na potrzeby codziennych operacji. Magazyn DASD jest zwykle używany w tym celu. W tym celu można również użyć magazynu sekwencyjnego, takiego jak codzienne kopie zapasowe na taśmach (logiczny lub fizyczny).

- Nie ma gwarancji, że *Magazyn archiwum* (znany również jako chłodny magazyn) nie zostanie zainstalowany w danym momencie. Zamiast tego jest on instalowany i dostępny w razie konieczności. Magazyn archiwalny jest często implementowany przy użyciu sekwencyjnych kopii zapasowych na taśmach (logicznych lub fizycznych) dla magazynu.

## <a name="mainframe-versus-io-latency-and-iops"></a>Opóźnienie i czas operacji we/wy na sekundę

Komputery mainframe często są używane w przypadku aplikacji wymagających wysokiej wydajności operacji we/wy i niskiego opóźnienia we/wy. Można to zrobić przy użyciu połączeń FICON z urządzeniami we/wy i HiperSockets. Gdy HiperSockets są używane do łączenia aplikacji i urządzeń bezpośrednio z kanałem we/wy komputera mainframe, można osiągnąć opóźnienie w mikrosekundach.

## <a name="azure-storage-at-a-glance"></a>Usługa Azure Storage w skrócie

Opcje infrastruktura jako usługa ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) platformy Azure dla magazynu zapewniają porównywalną pojemność komputera mainframe.

Firma Microsoft oferuje petabajtów magazyn dla aplikacji hostowanych na platformie Azure i ma kilka opcji magazynu. Te zakresy z magazynu SSD w celu zapewnienia wysokiej wydajności magazynu obiektów BLOB o niskich kosztach na potrzeby magazynu i archiwów pamięci masowej. Ponadto platforma Azure zapewnia opcję nadmiarowości danych dla magazynu, co zwiększa nakład pracy w celu skonfigurowania w środowisku mainframe.

Usługa Azure Storage jest dostępna jako Azure [disks](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md)i [Azure Blobs](../../../../storage/blobs/storage-blobs-overview.md) , jak w poniższej tabeli podsumowano. Dowiedz się więcej o tym [, kiedy korzystać z każdego z nich](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Opis</th><th>Użyj, gdy chcesz:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Udostępnia interfejs SMB, biblioteki klienta i interfejs <a href="/rest/api/storageservices/file-service-rest-api">rest</a> , który umożliwia dostęp z dowolnego miejsca do przechowywanych plików.
</td>
<td><ul>
<li>Podnieś i przenieś aplikację do chmury, gdy aplikacja używa natywnych interfejsów API systemu plików do udostępniania danych między nimi a innymi aplikacjami uruchomionymi na platformie Azure.</li>
<li>Przechowuj narzędzia do programowania i debugowania, które muszą być dostępne z wielu maszyn wirtualnych.</li>
</ul>
</td>
</tr>
<tr><td>Obiekty blob platformy Azure
</td>
<td>Zapewnia biblioteki klienckie i interfejs <a href="/rest/api/storageservices/blob-service-rest-api">rest</a> , który umożliwia przechowywanie i dostęp do danych bez struktury w dużej skali w blokowych obiektach Blob. Program obsługuje również <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> w przypadku rozwiązań do analizy danych Big Data w przedsiębiorstwie.
</td>
<td><ul>
<li>Obsługuje scenariusze przesyłania strumieniowego i dostępu swobodnego w aplikacji.</li>
<li>Dostęp do danych aplikacji z dowolnego miejsca.</li>
<li>Utwórz usługę Data Lake w przedsiębiorstwie na platformie Azure i wykonaj analizę danych Big Data.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Zapewnia biblioteki klienckie i interfejs <a href="/rest/api/compute/disks">rest</a> , który umożliwia trwałe przechowywanie danych i uzyskiwanie do nich dostępu z dołączonego wirtualnego dysku twardego.
</td>
<td><ul>
<li>Podnieś i Przenieś aplikacje, które używają natywnych interfejsów API systemu plików do odczytu i zapisu danych na dyskach trwałych.</li>
<li>Przechowuj dane, które nie są wymagane, aby uzyskać do nich dostęp spoza maszyny wirtualnej, do której dysk jest podłączony.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Magazyn Azure gorąca (online) i zimny (Archiwum)

Typ magazynu dla danego systemu zależy od wymagań systemu, w tym rozmiaru magazynu, przepływności i liczby operacji we/wy na sekundę. W przypadku magazynu typu DASD na komputerze mainframe aplikacje na platformie Azure zwykle korzystają z magazynu dyskowego platformy Azure. W przypadku magazynu archiwum typu mainframe magazyn obiektów BLOB jest używany na platformie Azure.

Dysków SSD zapewniają najwyższą wydajność magazynu na platformie Azure. Dostępne są następujące opcje (w przypadku pisania tego dokumentu):

| Typ         | Rozmiar           | Liczba operacji we/wy na sekundę                  |
|--------------|----------------|-----------------------|
| Dysk SSD w warstwie Ultra    | 4 GB do 64 TB  | 1 200 do 160 000 operacji we/wy |
| Dysk SSD w warstwie Premium  | 32 GB do 32 TB | od 12 do 15 000 operacji we/wy     |
| Dysk SSD w warstwie Standardowa | 32 GB do 32 TB | od 12 do 2 000 operacji we/wy      |

Usługa BLOB Storage zapewnia największą ilość miejsca w magazynie na platformie Azure. Oprócz rozmiaru magazynu platforma Azure oferuje magazyn zarządzany i niezarządzany. Dzięki magazynowi zarządzanemu platforma Azure obsługuje zarządzanie podstawowymi kontami magazynu. W przypadku magazynu niezarządzanego użytkownik jest odpowiedzialny za skonfigurowanie kont usługi Azure Storage o odpowiednim rozmiarze w celu spełnienia wymagań dotyczących magazynu.

## <a name="next-steps"></a>Następne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehostowanie komputera mainframe na platformie Azure Virtual Machines](../overview.md)
- [Przenoszenie obliczeń mainframe na platformę Azure](mainframe-compute-Azure.md)
- [Podejmowanie decyzji o tym, kiedy korzystać z obiektów blob platformy Azure, Azure Files lub dysków platformy Azure](../../../../storage/common/storage-introduction.md)
- [SSD w warstwie Standardowa Managed Disks dla obciążeń maszyn wirtualnych platformy Azure](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>Zasoby IBM

- [Równoległa Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i funkcja sprzęgania: poza podstawowymi](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie wymaganych użytkowników dla instalacji funkcji programu DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — Utwórz wystąpienie polecenia](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Rozwiązanie klastrowanej bazy danych DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government w chmurze dla aplikacji dla komputerów mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Przewodnik po podnoszenia i przesunięć wirtualnego centrum danych platformy Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
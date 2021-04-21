---
title: Microsoft Azure Data Box Disk — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy usługi Azure Data Box Disk, rozwiązania w chmurze umożliwiającego przenoszenie dużych ilości danych na platformę Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770437"
---
# <a name="what-is-azure-data-box-disk"></a>Co to jest usługa Azure Data Box Disk?

Rozwiązanie Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych lokalnych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie od 1 do 5 dysków półprzewodnikowych (SSD). Te zaszyfrowane dyski o pojemności 8 TB są wysyłane do centrum danych za pośrednictwem regionalnego przewoźnika.

Dyski można szybko konfigurować, łączyć i odblokowywać za pośrednictwem usługi urządzenie Data Box w Azure Portal. Skopiuj dane na dyski i odeślij dyski z powrotem do platformy Azure. W centrum danych platformy Azure dane są automatycznie przekazywane z dysków do chmury przez szybki, prywatny link przekazywania do sieci.

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Disk umożliwia przenoszenie terabajtów danych w scenariuszach bez łączności lub z ograniczoną łącznością z siecią. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe.

- **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure. Może być to na przykład przenoszenie danych z taśm w trybie offline do danych archiwalnych w chłodnym magazynie platformy Azure.
- **Transfer przyrostowy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą urządzenia Data Box Disk (inicjatora) następują transfery przyrostowe za pośrednictwem sieci. Na przykład rozwiązania Commvault i Data Box Disk są używane do przenoszenia kopii zapasowych na platformę Azure. Po tej migracji następuje kopiowanie danych przyrostowych przy użyciu sieci do usługi Azure Storage.
- **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

### <a name="ingestion-of-data-from-data-box"></a>Pozyskiwanie danych z urządzenie Data Box

Dostawcy platformy Azure i dostawcy spoza platformy Azure mogą pozysać dane z Azure Data Box. Usługi platformy Azure, które zapewniają pozyskiwanie danych z Azure Data Box obejmują:

- **SharePoint Online** — użyj Azure Data Box i narzędzia do migracji programu SharePoint (SPMT), aby przeprowadzić migrację zawartości udziału plików do usługi SharePoint Online. Za urządzenie Data Box można usunąć zależność od łącza sieci WAN w celu transferu danych. Aby uzyskać więcej informacji, zobacz [Używanie Azure Data Box Heavy do migrowania zawartości udziału plików do usługi SharePoint Online.](data-box-heavy-migrate-spo.md)

- **Azure File Sync** — replikuje pliki z usługi urządzenie Data Box do udziału plików platformy Azure, umożliwiając scentralizowanie usług plików na platformie Azure przy zachowaniu lokalnego dostępu do danych. Aby uzyskać więcej informacji, zobacz [Wdrażanie Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md).

- **Magazyny HDFS** — migrowanie danych z lokalnego magazynu usługi Hadoop rozproszony system plików (HDFS) klastra Hadoop do usługi Azure Storage przy użyciu urządzenie Data Box. Aby uzyskać więcej informacji, zobacz [Migrate from on-prem HDFS store to Azure Storage with Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)(Migrowanie z magazynu w systemie plików HDFS w sieci Azure Data Box).

- **Azure Backup** — umożliwia przenoszenie dużych kopii zapasowych kluczowych danych przedsiębiorstwa za pośrednictwem mechanizmów trybu offline do magazynu usługi Azure Recovery Services. Aby uzyskać więcej informacji, [zobacz Azure Backup omówienie systemu](../backup/backup-overview.md).

Możesz używać swoich danych urządzenie Data Box u wielu dostawców usług spoza platformy Azure. Przykład:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** — umożliwia migrowanie dużych ilości danych do Microsoft Azure przy użyciu Azure Data Box.
- **[Rozwiązanie —](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** umożliwia tworzenie kopii zapasowych i replikowanie dużych ilości danych z maszyny funkcji Hyper-V do urządzenie Data Box.

Aby uzyskać listę innych dostawców usług spoza platformy Azure, którzy integrują się z usługą urządzenie Data Box, [zobacz Azure Data Box Partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamówienie** — utwórz zamówienie w Azure Portal, podaj informacje o wysyłce i docelowe konto usługi Azure Storage dla danych. Jeśli dyski są dostępne, platforma Azure szyfruje, przygotuje i wysyła dyski z identyfikatorem śledzenia wysyłki.

2. **Odbieranie** — rozpakowywanie i łączenie dostarczonych dysków z komputerem zawierającym dane do skopiowania. Odblokuj dyski.

3. **Kopiowanie danych** — przeciągnij i upuść, aby skopiować dane na dyskach.

4. **Zwracanie** — przygotuj i wyślij dyski z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z dysków na platformę Azure. Dyski są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu będziesz powiadamiać pocztą e-mail o wszystkich zmianach stanu. Aby uzyskać więcej informacji na temat szczegółowego przepływu, przejdź do tematu [Deploy Data Box Disks in Azure portal (Wdrażanie dysków usługi Data Box Disk w witrynie Azure Portal)](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Disk jest przeznaczone do przenoszenia dużych ilości danych na platformę Azure bez wpływu na sieć. Oferuje ono następujące korzyści:

- **Szybkość** — urządzenie Data Box Disk używa połączenia USB 3.0 i umożliwia przenoszenie do 35 TB danych na platformę Azure w czasie krótszym niż tydzień.

- **Łatwość użycia** — urządzenie Data Box to rozwiązanie proste do użycia.

  - Dyski używają połączeń USB, a czas ich instalacji jest minimalny.
  - Dyski mają niewielkie rozmiary, co ułatwia ich obsługę.
  - Dyski nie mają żadnych wymagań dotyczących zasilania zewnętrznego.
  - Dyski mogą być używane z serwerem centrum danych, komputerem stacjonarnym lub laptopem.
  - Rozwiązanie zapewnia szczegółowe śledzenie przy użyciu Azure Portal.

- **Zabezpieczanie** — usługa Data Box Disk ma wbudowane zabezpieczenia dysków, danych i usługi.
  - Dyski są odporne na modyfikacje i obsługują możliwość bezpiecznej aktualizacji.
  - Dane na dyskach są przez cały czas zabezpieczane za pomocą 128-bitowego szyfrowania AES.
  - Dyski można odblokować tylko przy użyciu klucza podanego w witrynie Azure Portal.
  - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure.
  - Po przekazaniu danych do platformy Azure dyski są czyszczone zgodnie ze standardami NIST 800-88r1.  

Aby uzyskać więcej informacji, przejdź do tematu [Zabezpieczenia i ochrona danych w usłudze Azure Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Funkcje i specyfikacje

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | < 2 funty na opakowanie. Maksymalnie 5 dysków w opakowaniu                |
| Wymiary                                              | Dysk — SSD, 2,5 cala |
| Kable                                                  | 1 kabel USB 3.1 na dysk|
| Pojemność magazynu na zamówienie                              | 40 TB (do wykorzystania ok. 35 TB)|
| Pojemność magazynu na dysku                                   | 8 TB (do wykorzystania ok. 7 TB)|
| Interfejs danych                                          | USB   |
| Zabezpieczenia                                                | Wstępne szyfrowanie za pomocą funkcji BitLocker i zabezpieczone aktualizacje <br> Dyski chronione za pomocą klucza dostępu <br> Dane są zaszyfrowane przez cały czas  |
| Szybkość transferu danych                                      | Do 430 MB/s w zależności od rozmiaru pliku      |
|Zarządzanie                                               | Azure Portal |

## <a name="region-availability"></a>Dostępność w danym regionie

Aby uzyskać informacje na temat dostępności regionów, przejdź do tematu [Dostępność produktów platformy Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Data Box Disk można również wdrożyć w chmurze Azure Government Cloud. Aby uzyskać więcej informacji, [zobacz Co to jest Azure Government?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Ceny

Aby uzyskać informacje o cenach, przejdź do [strony cennika](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami rozwiązania Data Box Disk](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.
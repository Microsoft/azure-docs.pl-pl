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
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 8e9b1faf2521e0698052dd1cdd1253191ae6f8bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84187403"
---
# <a name="what-is-azure-data-box-disk"></a>Co to jest usługa Azure Data Box Disk?

Rozwiązanie Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych lokalnych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie od 1 do 5 dysków półprzewodnikowych (SSD). Te dyski zaszyfrowane 8 TB są wysyłane do centrum danych za pomocą operatora regionalnego.

Można szybko skonfigurować, połączyć i odblokować dyski za pomocą usługi urządzenie Data Box w Azure Portal. Skopiuj dane na dyski i odeślij dyski z powrotem do platformy Azure. W centrum danych platformy Azure dane są automatycznie przekazywane z dysków do chmury przez szybki, prywatny link przekazywania do sieci.

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Disk umożliwia przenoszenie terabajtów danych w scenariuszach bez łączności lub z ograniczoną łącznością z siecią. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe.

- **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure. Może być to na przykład przenoszenie danych z taśm w trybie offline do danych archiwalnych w chłodnym magazynie platformy Azure.
- **Transfer przyrostowy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą urządzenia Data Box Disk (inicjatora) następują transfery przyrostowe za pośrednictwem sieci. Na przykład rozwiązania Commvault i Data Box Disk są używane do przenoszenia kopii zapasowych na platformę Azure. Po migracji następuje kopiowanie przyrostowych danych przy użyciu sieci do usługi Azure Storage.
- **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

### <a name="ingestion-of-data-from-data-box"></a>Pozyskiwanie danych z urządzenie Data Box

Dostawcy platformy Azure i dostawcy spoza platformy Azure mogą pozyskiwanie danych z Azure Data Box. Usługi platformy Azure, które zapewniają pozyskiwanie danych z Azure Data Box obejmują:

- **SharePoint Online** — Użyj Azure Data Box i narzędzia do migracji programu SharePoint (SPMT) do migrowania zawartości udziału plików do usługi SharePoint Online. Za pomocą urządzenie Data Box można usunąć zależność od linku sieci WAN do transferu danych. Aby uzyskać więcej informacji, zobacz [używanie Azure Data Box Heavy do migrowania zawartości udziału plików do usługi SharePoint Online](data-box-heavy-migrate-spo.md).

- **Azure File Sync** — replikuje pliki z urządzenie Data Box do udziału plików platformy Azure, co pozwala na scentralizowanie usług plików na platformie Azure przy zachowaniu lokalnego dostępu do danych. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md).

- System plików **HDFS** — migruje dane z lokalnego magazynu usługi Hadoop rozproszony system plików (HDFS) do magazynu platformy Azure przy użyciu urządzenie Data Box. Aby uzyskać więcej informacji, zobacz [Migrowanie ze sklepu premium HDFS do usługi Azure Storage za pomocą Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** — umożliwia przenoszenie dużych kopii zapasowych krytycznych danych przedsiębiorstwa za pomocą mechanizmów offline do magazynu Recovery Services platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Backup przegląd](../backup/backup-overview.md).

Możesz użyć danych urządzenie Data Box z wieloma dostawcami usług spoza platformy Azure. Przykład:

- **[CommVault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** — umożliwia migrowanie dużych ilości danych do Microsoft Azure przy użyciu Azure Data Box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** — umożliwia tworzenie kopii zapasowych i replikowanie dużych ilości danych z komputera funkcji Hyper-V do urządzenie Data Box.

Listę innych dostawców usług spoza platformy Azure, które integrują się z urządzenie Data Box, można znaleźć w temacie [Azure Data Box Partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamówienie** — Utwórz zamówienie w Azure Portal, podaj informacje o wysyłce i docelowe konto usługi Azure Storage dla swoich danych. Jeśli dyski są dostępne, platforma Azure szyfruje, przygotuje i wysyła dyski z identyfikatorem śledzenia wysyłki.

2. **Odbieranie** — rozpakowywanie i łączenie dostarczonych dysków z komputerem zawierającym dane do skopiowania. Odblokuj dyski.

3. **Kopiowanie danych** — przeciągnij i upuść, aby skopiować dane na dyskach.

4. **Zwracanie** — przygotuj i wyślij dyski z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z dysków na platformę Azure. Dyski są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu otrzymujesz powiadomienie pocztą e-mail o wszystkich zmianach stanu. Aby uzyskać więcej informacji na temat szczegółowego przepływu, przejdź do tematu [Deploy Data Box Disks in Azure portal (Wdrażanie dysków usługi Data Box Disk w witrynie Azure Portal)](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Disk jest przeznaczone do przenoszenia dużych ilości danych na platformę Azure bez wpływu na sieć. Oferuje ono następujące korzyści:

- **Szybkość** — urządzenie Data Box Disk używa połączenia USB 3.0 i umożliwia przenoszenie do 35 TB danych na platformę Azure w czasie krótszym niż tydzień.

- **Łatwość użycia** — urządzenie Data Box to rozwiązanie proste do użycia.

  - Dyski używają połączeń USB, a czas ich instalacji jest minimalny.
  - Dyski mają niewielkie rozmiary, co ułatwia ich obsługę.
  - Dyski nie mają żadnych wymagań dotyczących zasilania zewnętrznego.
  - Dyski mogą być używane z serwerem centrum danych, komputerem stacjonarnym lub laptopem.
  - Rozwiązanie to kompleksowe śledzenie przy użyciu Azure Portal.

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

Aby uzyskać informacje na temat dostępności regionów, przejdź do pozycji [produkty platformy Azure dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk można również wdrożyć w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, przejdź do [strony cennika](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami rozwiązania Data Box Disk](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.

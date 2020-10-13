---
title: Przenoszenie zawartości udziału plików do usługi SharePoint Online za pomocą Azure Data Box Heavy
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak migrować zawartość udziału plików, aby udostępnić punkt online za pomocą Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: a87181e7d1c776648781a2672dfd73ac2eaab1b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396862"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Użyj Azure Data Box Heavy do migrowania zawartości udziału plików do usługi SharePoint Online

Użyj Azure Data Box Heavy i narzędzia do migracji programu SharePoint (SPMT), aby z łatwością migrować zawartość udziału plików do usługi SharePoint Online i OneDrive. Za pomocą Data Box Heavy można usunąć zależność od łącza sieci rozległej (WAN) do transferu danych.

Urządzenie Data Box Microsoft Azure to usługa, która umożliwia zamawianie urządzenia z Microsoft Azure Portal. Następnie można skopiować terabajty danych z serwerów na urządzenie. Po wysłaniu jej z powrotem do firmy Microsoft dane zostaną skopiowane na platformę Azure. W zależności od rozmiaru danych, które mają być transferowane, możesz wybrać jedną z opcji:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) z możliwością użycia 35 TB na zamówienie w przypadku małych i średnich zestawów danych.
- [Urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) z pojemnością 80 TB na urządzenie w przypadku średnich i dużych zestawów danych.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) z 770 TB pojemności na urządzenie dla dużych zestawów danych.

W tym artykule opisano, jak używać Data Box Heavy do migrowania zawartości udziału plików do usługi SharePoint Online.

## <a name="requirements-and-costs"></a>Wymagania i koszty

### <a name="for-data-box-heavy"></a>Usługa Data Box Heavy

- Data Box Heavy jest dostępna tylko dla Umowa Enterprise (EA), dostawcy rozwiązań w chmurze (CSP) lub usług sponsorowanych przez platformę Azure. Jeśli Twoja subskrypcja nie należy do żadnego z powyższych typów, skontaktuj się z firmą pomoc techniczna firmy Microsoft, aby uaktualnić subskrypcję lub zobaczyć [Cennik subskrypcji platformy Azure](https://azure.microsoft.com/pricing/).
- Istnieje opłata za korzystanie z Data Box Heavy. Zapoznaj się z [cennikiem Data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Dla usługi SharePoint Online

- Zapoznaj się z [minimalnymi wymaganiami dotyczącymi narzędzia do migracji programu SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Ten przepływ pracy wymaga wykonania czynności na Azure Data Box Heavy jak również w usłudze SharePoint Online.
Poniższe kroki odnoszą się do Azure Data Box Heavy.

1. Azure Data Box Heavy kolejności.
2. Odbierz i Skonfiguruj urządzenie.
3. Skopiuj dane z lokalnego udziału plików do folderu w celu Azure Files na urządzeniu.
4. Po zakończeniu kopiowania należy wysłać urządzenie z powrotem zgodnie z instrukcjami.
5. Poczekaj na całkowite przekazanie danych na platformę Azure.

Poniższe kroki odnoszą się do usługi SharePoint Online.

6. Utwórz maszynę wirtualną w Azure Portal i zainstaluj na niej udział plików platformy Azure.
7. Zainstaluj narzędzie SPMT na maszynie wirtualnej platformy Azure.
8. Uruchom narzędzie SPMT, używając udziału plików platformy Azure jako *źródła*.
9. Wykonaj ostatnie kroki tego narzędzia.
10. Sprawdź i Potwierdź swoje dane.

## <a name="use-data-box-heavy-to-copy-data"></a>Kopiowanie danych za pomocą Data Box Heavy

Wykonaj następujące kroki, aby skopiować dane do Data Box Heavy.

1. [Zamów Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Po otrzymaniu Data Box Heavy [skonfiguruj Data Box Heavy](data-box-heavy-deploy-set-up.md). Będziesz mieć kabel i skonfigurować oba węzły na urządzeniu.
3. [Skopiuj dane do Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Podczas kopiowania upewnij się, że:

    - Aby skopiować dane, użyj tylko folderu *StorageAccountName_AzFile* w Data Box Heavy. Dzieje się tak, ponieważ chcesz, aby dane były kończyć się w udziale plików platformy Azure, a nie w blokowych obiektach Blob lub stronicowych obiektach Blob.
    - Skopiuj pliki do folderu w folderze *StorageAccountName_AzFile* . Podfolder w folderze *StorageAccountName_AzFile* tworzy udział plików. Pliki skopiowane bezpośrednio do folderu *StorageAccountName_AzFile* nie powiodą się i są przekazywane jako blokowe obiekty blob. Jest to udział plików, który zostanie zainstalowany na maszynie wirtualnej w następnym kroku.
    - Kopiuj dane do obu węzłów Data Box Heavy.
3. Uruchom [przygotowanie do wysłania](data-box-heavy-deploy-picked-up.md#prepare-to-ship) na urządzeniu. Pomyślne przygotowanie do wysłania gwarantuje pomyślne przesłanie plików na platformę Azure.
4. [Zwróć urządzenie](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Sprawdź przekazywanie danych na platformę Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Migrowanie danych za pomocą SPMT

Po otrzymaniu potwierdzenia od zespołu danych platformy Azure, że kopia danych została ukończona, należy przeprowadzić migrację danych do usługi SharePoint Online.

Aby uzyskać najlepszą wydajność i łączność, zalecamy utworzenie maszyny wirtualnej platformy Azure.

1. Zaloguj się do Azure Portal, a następnie [Utwórz maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).
2. [Zainstaluj udział plików platformy Azure na maszynie wirtualnej](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Pobierz narzędzie do migracji programu SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) i zainstaluj je na maszynie wirtualnej platformy Azure.
4. Uruchom narzędzie do migracji programu SharePoint. Kliknij przycisk **Zaloguj** i wprowadź nazwę użytkownika oraz hasło do konta służbowego.
5. Po wyświetleniu monitu, **gdzie są Twoje dane?** wybierz pozycję **udział plików**. Wprowadź ścieżkę do udziału plików platformy Azure, w którym znajdują się Twoje dane.
6. Postępuj zgodnie z pozostałymi monitami, w tym z lokalizacją docelową. Aby uzyskać więcej informacji, zapoznaj się z [tematem jak używać narzędzia do migracji programu SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Szybkość, z jaką dane są pozyskiwane w usłudze SharePoint Online, ma wpływ kilka czynników, bez względu na to, czy dane znajdują się już na platformie Azure. Zrozumienie tych czynników ułatwi zaplanowanie i zmaksymalizowanie wydajności migracji.  Aby uzyskać więcej informacji, przejdź do [witryny SharePoint Online i szybkość migracji do usługi OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Podczas migrowania danych do usługi SharePoint Online istnieje ryzyko utraty istniejących uprawnień do plików. Możesz również utracić pewne metadane, takie jak *utworzone przez* i *daty modyfikacji przez*.

## <a name="next-steps"></a>Następne kroki

[Zamów Data Box Heavy](./data-box-heavy-deploy-ordered.md)
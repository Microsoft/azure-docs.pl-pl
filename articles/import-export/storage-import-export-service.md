---
title: Transferowanie danych do i z usługi Azure Storage za pomocą usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania importu i eksportu w Azure Portal na potrzeby przesyłania danych do i z usługi Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181860"
---
# <a name="what-is-azure-importexport-service"></a>Co to jest usługa Azure Import/Export?

Usługa Azure Import/Export służy do bezpiecznego importowania dużych ilości danych do usługi Azure Blob Storage i Azure Files przez wysyłkę dysków do centrum danych platformy Azure. Ta usługa może również służyć do transferu danych z usługi Azure Blob Storage na stacje dysków wysyłane do lokacji lokalnych. Dane z co najmniej jednego dysku można zaimportować do usługi Azure Blob Storage lub Azure Files.

Dostarczaj własne dyski i przesyłaj dane za pomocą usługi Azure Import/Export. Możesz również użyć stacji dysków dostarczonych przez firmę Microsoft.

Jeśli chcesz przenieść dane przy użyciu stacji dysków dostarczonych przez firmę Microsoft, możesz użyć [Azure Data Box Disk](../databox/data-box-disk-overview.md) do zaimportowania danych do platformy Azure. Firma Microsoft dostarcza do 5 szyfrowanych dysków półprzewodnikowych (dysków SSD) z 40 TB całkowitej pojemności na zamówienie do centrum danych za pomocą operatora regionalnego. Można szybko skonfigurować stacje dysków, skopiować dane na dyski za pośrednictwem połączenia USB 3,0 i dostarczyć dyski z powrotem do platformy Azure. Aby uzyskać więcej informacji, przejdź do [omówienia Azure Data Box Disk](../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Azure Import/Export — przypadki użycia

Rozważ użycie usługi Azure Import/Export, gdy przekazywanie lub pobieranie danych za pośrednictwem sieci jest zbyt wolne lub nie ma dodatkowej przepustowości sieci. Tej usługi należy używać w następujących scenariuszach:

* **Migracja danych do chmury**: szybkie i ekonomiczne przenoszenie dużych ilości danych na platformę Azure.
* **Dystrybucja zawartości**: szybkie wysyłanie danych do lokacji klienta.
* **Kopia zapasowa**: Twórz kopie zapasowe danych lokalnych do przechowywania w usłudze Azure Storage.
* **Odzyskiwanie danych**: odzyskiwanie dużej ilości danych przechowywanych w magazynie i dostarczanie ich do lokalizacji lokalnej.

## <a name="importexport-components"></a>Składniki importu/eksportu

Usługa Import/Export korzysta z następujących składników:

* **Usługa importu/eksportu**: Ta usługa dostępna w Azure Portal ułatwia użytkownikowi tworzenie i śledzenie zadań importowania danych (przekazywania) i eksportowania (pobierania).  

* **Narzędzie WAImportExport**: jest to narzędzie wiersza polecenia, które wykonuje następujące czynności:
  * Przygotowuje stacje dysków, które są dostarczane do zaimportowania.
  * Ułatwia kopiowanie danych na dysk.
  * Szyfruje dane na dysku przy użyciu algorytmu AES 256-bit funkcji BitLocker. Aby chronić klucz funkcji BitLocker, można użyć funkcji ochrony klucza zewnętrznego.
  * Generuje pliki dziennika dysku używane podczas tworzenia importu.
  * Pomaga identyfikować liczbę dysków wymaganych do eksportowania zadań.

> [!NOTE]
> Narzędzie WAImportExport jest dostępne w dwóch wersjach, w wersji 1 i 2. Zalecamy korzystanie z programu:
>
> * Wersja 1 do zaimportowania/wyeksportowania do usługi Azure Blob Storage.
> * Wersja 2 do importowania danych do usługi Azure Files.
>
> Narzędzie WAImportExport jest zgodne tylko z 64-bitowym systemem operacyjnym Windows. W przypadku określonych wersji systemu operacyjnego przejdź do [wymagań dotyczących importowania/eksportowania na platformie Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Stacje dysków**: dyski półprzewodnikowe (dysków SSD) lub dyski twarde (HDD) można dostarczyć do centrum danych platformy Azure. Podczas tworzenia zadania importowania są dostarczane dyski zawierające dane. Podczas tworzenia zadania eksportu można dostarczyć puste dyski do centrum danych platformy Azure. Dla określonych typów dysków przejdź do pozycji [obsługiwane typy dysków](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Jak działa import/eksport?

Usługa Azure Import/Export umożliwia przesyłanie danych do obiektów blob platformy Azure i Azure Files przez tworzenie zadań. Użyj Azure Portal lub Azure Resource Manager interfejsu API REST do tworzenia zadań. Każde zadanie jest skojarzone z jednym kontem magazynu.

Zadania mogą być importowane lub eksportowane. Zadanie importowania umożliwia importowanie danych do obiektów blob platformy Azure lub plików platformy Azure, a zadanie eksportu umożliwia eksportowanie danych z obiektów blob platformy Azure. W przypadku zadania importowania dostarczasz dyski zawierające dane. Po utworzeniu zadania eksportu należy dostarczyć puste dyski do centrum danych platformy Azure. W każdym przypadku można dostarczyć do 10 dysków na każde zadanie.

### <a name="inside-an-import-job"></a>Wewnątrz zadania importowania

Na wysokim poziomie zadanie importu obejmuje następujące kroki:

1. Określ dane do zaimportowania, wymaganą liczbę dysków, docelową lokalizację obiektu BLOB dla danych w usłudze Azure Storage.
2. Użyj narzędzia WAImportExport, aby skopiować dane na dyski. Szyfruj stacje dysków za pomocą funkcji BitLocker.
3. Utwórz zadanie importu na docelowym koncie magazynu w Azure Portal. Przekaż pliki dziennika stacji.
4. Podaj adres zwrotny i numer konta nośnego do wysyłania dysków z powrotem do Ciebie.
5. Wyślij dyski na adres wysyłkowy podany podczas tworzenia zadania.
6. Zaktualizuj numer śledzenia dostarczania w szczegółach zadania importowania i Prześlij zadanie importu.
7. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
8. Dyski są dostarczane przy użyciu konta operatora na adres zwrotny podany w zadaniu importu.

> [!NOTE]
> W przypadku wydań lokalnych (w ramach kraju/regionu centrum danych) należy udostępnić konto przewoźnika krajowego.
>
> W przypadku danych o wydaniach (poza krajem/regionem centrum danych) należy udostępnić międzynarodowe konto operatora.

 ![Rysunek 1. Importowanie przepływu zadań](./media/storage-import-export-service/import-job.png)

Aby uzyskać instrukcje krok po kroku dotyczące importowania danych, przejdź do:

* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Importuj dane do Azure Files](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Wewnątrz zadania eksportu

> [!IMPORTANT]
> Usługa obsługuje tylko eksportowanie obiektów blob platformy Azure. Eksportowanie plików platformy Azure nie jest obsługiwane.

Na wysokim poziomie zadanie eksportu obejmuje następujące kroki:

1. Określ dane, które mają zostać wyeksportowane, wymagane dyski, źródłowe obiekty blob lub ścieżki kontenerów danych w usłudze BLOB Storage.
2. Utwórz zadanie eksportu na źródłowym koncie magazynu w Azure Portal.
3. Określ źródłowe obiekty blob lub ścieżki kontenerów dla danych do wyeksportowania.
4. Podaj adres zwrotny i numer konta nośnego do wysyłania dysków z powrotem do Ciebie.
5. Wyślij dyski na adres wysyłkowy podany podczas tworzenia zadania.
6. Zaktualizuj numer śledzenia dostarczania w szczegółach zadania eksportu i Prześlij zadanie eksportu.
7. Dyski są odbierane i przetwarzane w centrum danych platformy Azure.
8. Dyski są szyfrowane za pomocą funkcji BitLocker, a klucze są dostępne za pośrednictwem Azure Portal.  
9. Dyski są dostarczane przy użyciu konta operatora na adres zwrotny podany w zadaniu importu.

> [!NOTE]
> W przypadku wydań lokalnych (w ramach kraju/regionu centrum danych) należy udostępnić konto przewoźnika krajowego.
>
> W przypadku danych o wydaniach (poza krajem/regionem centrum danych) należy udostępnić międzynarodowe konto operatora.
  
 ![Rysunek 2. eksportowanie przepływu zadań](./media/storage-import-export-service/export-job.png)

Aby uzyskać instrukcje krok po kroku dotyczące eksportowania danych, przejdź do obszaru [Eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Azure Import/Export obsługuje kopiowanie danych do i ze wszystkich kont usługi Azure Storage. Dyski można wydać do jednej z wymienionych lokalizacji. Jeśli Twoje konto magazynu znajduje się w lokalizacji platformy Azure, która nie została określona w tym miejscu, podczas tworzenia zadania zostanie udostępniona alternatywna lokalizacja wysyłki.

### <a name="supported-shipping-locations"></a>Obsługiwane lokalizacje wysyłki

|Kraj/region  |Kraj/region  |Kraj/region  |Kraj/region  |
|---------|---------|---------|---------|
|East US    | Europa Północna        | Indie Środkowe        |US Gov Iowa         |
|Zachodnie stany USA     |West Europe         | Indie Południowe        | US DoD (region wschodni)        |
|Wschodnie stany USA 2    | Azja Wschodnia        |  Indie Zachodnie        | US DoD (region środkowy)        |
|Zachodnie stany USA 2     | Southeast Asia        | Kanada Środkowa        | Chiny Wschodnie         |
|Central US     | Australia Wschodnia        | Kanada Wschodnia        | Chiny Północne        |
|Północno-środkowe stany USA     |  Australia Południowo-Wschodnia       | Brazylia Południowa        | Południowe Zjednoczone Królestwo        |
|South Central US     | Japonia Zachodnia        |Korea Środkowa         | Niemcy Środkowe        |
|Zachodnio-środkowe stany USA     |  Japonia Wschodnia       | US Gov Wirginia        | Niemcy Północno-Wschodnie        |
|Zachodnia Republika Południowej Afryki   |  Północna Republika Południowej Afryki |

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Dane na dysku są szyfrowane przy użyciu algorytmu AES 256-bit szyfrowanie dysków funkcją BitLocker. To szyfrowanie chroni dane podczas przesyłania.

W przypadku zadań importowania dyski są szyfrowane na dwa sposoby.  

* Określ opcję przy użyciu pliku *dataset.csv* podczas uruchamiania narzędzia WAImportExport podczas przygotowywania dysku.

* Ręcznie Włącz szyfrowanie funkcji BitLocker na dysku. Określ klucz szyfrowania w *driveset.csv* w przypadku uruchamiania wiersza polecenia narzędzia WAImportExport podczas przygotowywania dysku. Klucz szyfrowania BitLocker można dodatkowo chronić przy użyciu zewnętrznej ochrony klucza (znanej również jako klucz zarządzany przez firmę Microsoft) lub klucza zarządzanego przez klienta. Aby uzyskać więcej informacji, zobacz jak [używać klucza zarządzanego przez klienta do ochrony klucza funkcji BitLocker](storage-import-export-encryption-key-portal.md).

W przypadku zadań eksportu po skopiowaniu danych na dyski Usługa szyfruje dysk przy użyciu funkcji BitLocker przed wysłaniem jej z powrotem do użytkownika. Klucz szyfrowania jest dostarczany przez Azure Portal. Dysk należy odblokować przy użyciu narzędzia WAImporExport przy użyciu klucza.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Ceny

**Opłata za obsługę dysku**

Dla każdego dysku przetworzonego w ramach zadania importowania lub eksportowania występuje opłata za obsługę dysku. Zobacz szczegóły [cennika usługi Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Koszty wysyłki**

Podczas dostarczania dysków na platformę Azure koszt wysyłki jest obciążany przez firmę wysyłkową. Gdy firma Microsoft zwraca dyski do Ciebie, koszt wysyłki jest naliczany na koncie przewoźnika, które podano podczas tworzenia zadania.

**Koszty transakcji**

[Opłata za transakcje magazynu w warstwie Standardowa](https://azure.microsoft.com/pricing/details/storage/) jest stosowana podczas importowania, a także do eksportowania danych. W przypadku eksportowania danych z usługi Azure Storage opłaty za wychodzące normy są również stosowane wraz z opłatami za transakcje magazynu. Aby uzyskać więcej informacji o kosztach ruchu wychodzącego, zobacz [Cennik transferu danych.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać usługi Import/Export w celu:

* [Importowanie danych do obiektów blob platformy Azure](storage-import-export-data-to-blobs.md)
* [Eksportowanie danych z obiektów blob platformy Azure](storage-import-export-data-from-blobs.md)
* [Importuj dane do Azure Files](storage-import-export-data-to-files.md)

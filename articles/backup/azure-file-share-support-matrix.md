---
title: Macierz obsługi dla kopii zapasowej udziału plików platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 38c066c20399b39d676fb0c25aa158993258b979
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370992"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Macierz obsługi dla kopii zapasowej udziału plików platformy Azure

Za pomocą [usługi Azure Backup](./backup-overview.md) można tworzyć kopie zapasowe udziałów plików platformy Azure. Ten artykuł podsumowuje ustawienia pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure za pomocą Azure Backup.

> [!NOTE]
> Azure Backup obecnie nie obsługuje udziałów NFS.

## <a name="supported-regions"></a>Obsługiwane regiony

### <a name="ga-regions-for-azure-file-shares-backup"></a>Obszary odnoszące się do tworzenia kopii zapasowych udziałów plików platformy Azure

Kopie zapasowe udziałów plików platformy Azure są dostępne we wszystkich regionach **z wyjątkiem** : Niemcy środkowe (suwerenne), Niemcy Wschodnie (suwerenne), Chiny Wschodnie, Chiny Wschodnie 2, Chiny Północne Chiny Północne 2, US gov Iowa

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

| Szczegóły konta magazynu | Pomoc techniczna                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Rodzaj konta            | Azure Backup obsługuje udziały plików platformy Azure obecne w ogólnego przeznaczenia w wersji 1, ogólnego przeznaczenia w wersji 2 i w magazynie plików |
| Wydajność              | Azure Backup obsługuje udziały plików zarówno na kontach standardowych, jak i Premium Storage |
| Replikacja              | Obsługiwane są udziały plików platformy Azure w ramach kont magazynu z dowolnym typem replikacji |
| Zapora włączona         | Obsługiwane są udziały plików platformy Azure w ramach kont magazynu z regułami zapory zezwalających na dostęp do konta magazynu przez usługi Microsoft Azure Services|

## <a name="supported-file-shares"></a>Obsługiwane udziały plików

| Typ udziału plików                                   | Pomoc techniczna   |
| -------------------------------------------------- | --------- |
| Standardowa                                           | Obsługiwane |
| Duży                                              | Obsługiwane |
| Premium                                            | Obsługiwane |
| Udziały plików połączone z usługą Azure File Sync | Obsługiwane |

## <a name="protection-limits"></a>Limity ochrony

| Ustawienie                                                      | Limit |
| ------------------------------------------------------------ | ----- |
| Maksymalna liczba udziałów plików, które mogą być chronione na magazyn dziennie| 200   |
| Maksymalna liczba kont magazynu, które można zarejestrować na magazyn dziennie | 50    |
| Maksymalna liczba udziałów plików, które mogą być chronione na magazyn | 2000   |
| Maksymalna liczba kont magazynu, które można zarejestrować na magazyn | 200   |

## <a name="backup-limits"></a>Limity kopii zapasowych

| Ustawienie                                      | Limit |
| -------------------------------------------- | ----- |
| Maksymalna liczba kopii zapasowych na żądanie dziennie | 10   |
| Maksymalna liczba zaplanowanych kopii zapasowych dziennie | 1     |

## <a name="restore-limits"></a>Limity przywracania

| Ustawienie                                                      | Limit   |
| ------------------------------------------------------------ | ------- |
| Maksymalna liczba operacji przywracania dziennie                           | 10      |
| Maksymalna liczba plików na przywracanie                         | 99      |
| Maksymalny zalecany rozmiar przywracania na przywracanie dużych udziałów plików | 15 TiB |

## <a name="retention-limits"></a>Limity przechowywania

| Ustawienie                                                      | Limit    |
| ------------------------------------------------------------ | -------- |
| Maksymalna łączna liczba punktów odzyskiwania na udział plików w dowolnym momencie | 200      |
| Maksymalne przechowywanie punktu odzyskiwania utworzonego przez kopię zapasową na żądanie | 10 lat |
| Maksymalne przechowywanie codziennych punktów odzyskiwania (migawek) na udział plików| 200 dni |
| Maksymalne przechowywanie tygodniowych punktów odzyskiwania (migawek) na udział plików | 200 tygodni |
| Maksymalne przechowywanie miesięcznych punktów odzyskiwania (migawek) na udział plików | 120 miesięcy |
| Maksymalne przechowywanie rocznych punktów odzyskiwania (migawek) na udział plików | 10 lat |

## <a name="supported-restore-methods"></a>Obsługiwane metody przywracania

| Restore, metoda     | Szczegóły                                                      |
| ------------------ | ------------------------------------------------------------ |
| Przywracanie z pełnymi udziałami | Pełny udział plików można przywrócić do oryginalnej lub alternatywnej lokalizacji |
| Przywracanie na poziomie elementu | Można przywrócić pojedyncze pliki i foldery do oryginalnej lub alternatywnej lokalizacji |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć kopię zapasową udziałów plików platformy Azure](backup-afs.md)
* Dowiedz się, jak [przywrócić udziały plików platformy Azure](restore-afs.md)
* Dowiedz się, jak [zarządzać kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md)

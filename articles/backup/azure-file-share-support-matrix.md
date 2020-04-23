---
title: Macierz obsługi dla kopii zapasowej udziału plików platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: e74d04cf8ae9010a860b8467d0de771524bd3f3a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103206"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Macierz obsługi dla kopii zapasowej udziału plików platformy Azure

Za pomocą [usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) można tworzyć kopie zapasowe udziałów plików platformy Azure. Ten artykuł podsumowuje ustawienia pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure za pomocą Azure Backup.

## <a name="supported-geos"></a>Obsługiwane GEOREGIONY

Kopia zapasowa dla udziałów plików platformy Azure jest dostępna w następujących GEOREGIONY:

| Regiony | Obsługiwane regiony, ale nie ogólnie                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Australia Wschodnia (AE), Kanada Środkowa (CNC), zachodnio-środkowe stany USA (WCUS), Indie Południowe (INS), Północno-środkowe stany USA (NCUS), Japonia Wschodnia (JPE), Brazylia Południowa (BRS)                                                     |Australia Południowo-Wschodnia (ASE), Kanada Wschodnia (CE), środkowe stany USA (CUS), Azja Wschodnia (EA), Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Japonia Zachodnia (JPW), Indie Środkowe (INC), Korea Środkowa (KRC), Korea Południowa (KRS), Europa Północna (NE), Południowo-środkowe stany USA (SCUS), Południowe Azja Wschodnia (morze), Południowe Zjednoczone Królestwo (UKS), Zachodnie Zjednoczone Królestwo (UKW), Europa Zachodnia (my), zachodnie stany USA (WUS), US Gov Arizona (UGA), US Gov Teksas (UGT) , US Gov Wirginia (UGV), Australia Środkowa (ACL), Indie Zachodnie (INW), Północna Republika Południowej Afryki (SAN), Zjednoczone Emiraty Arabskie (UAN), Francja Środkowa (FRC), Niemcy Północne (GN), Niemcy Środkowo-Zachodnie (GWC), Zachodnia Republika Południowej Afryki (piły), Zjednoczone Emiraty Arabskie (UAC), Norwegia Wschodnia (NWE), Norwegia Zachodnia (NWW), (Szwajcaria Północna SZN), zachodnie stany USA 2 (WUS 2)             |

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

| Szczegóły konta magazynu | Pomoc techniczna                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Rodzaj konta            | Azure Backup obsługuje udziały plików platformy Azure zarówno w przypadku kont magazynu ogólnego przeznaczenia w wersji 1, jak i w wersji 2. |
| Wydajność              | Azure Backup obsługuje udziały plików zarówno na kontach standardowych, jak i Premium Storage |
| Replikacja              | Obsługiwane są udziały plików platformy Azure w ramach kont magazynu z dowolnym typem replikacji |

## <a name="supported-file-shares"></a>Obsługiwane udziały plików

| Typ udziału plików                                   | Pomoc techniczna   |
| -------------------------------------------------- | --------- |
| Standardowa                                           | Obsługiwane |
| Large                                              | Obsługiwane |
| Premium                                            | Obsługiwane |
| Udziały plików połączone z usługą Azure File Sync | Obsługiwane |

## <a name="protection-limits"></a>Limity ochrony

| Ustawienie                                                      | Limit |
| ------------------------------------------------------------ | ----- |
| Maksymalna liczba udziałów plików, które mogą być chronione dziennie na magazyn | 200   |
| Maksymalna liczba kont magazynu, które można zarejestrować na magazyn dziennie | 50    |

## <a name="backup-limits"></a>Limity kopii zapasowych

| Ustawienie                                      | Limit |
| -------------------------------------------- | ----- |
| Maksymalna liczba kopii zapasowych na żądanie dziennie | 4     |
| Maksymalna liczba zaplanowanych kopii zapasowych dziennie | 1     |

## <a name="restore-limits"></a>Limity przywracania

| Ustawienie                                                      | Limit   |
| ------------------------------------------------------------ | ------- |
| Maksymalna liczba operacji przywracania dziennie                           | 10      |
| Maksymalna liczba plików na przywracanie                         | 10      |
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

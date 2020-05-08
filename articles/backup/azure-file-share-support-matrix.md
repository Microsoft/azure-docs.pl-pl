---
title: Macierz obsługi dla kopii zapasowej udziału plików platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 4da17bb591e94a0eaf26f95210a3e841ad17973b
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890718"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Macierz obsługi dla kopii zapasowej udziału plików platformy Azure

Za pomocą [usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) można tworzyć kopie zapasowe udziałów plików platformy Azure. Ten artykuł podsumowuje ustawienia pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure za pomocą Azure Backup.

## <a name="supported-geos"></a>Obsługiwane GEOREGIONY

Kopia zapasowa dla udziałów plików platformy Azure jest dostępna w następujących GEOREGIONY:

| Regiony | Obsługiwane regiony (w ramach wersji zapoznawczej), ale jeszcze nie GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Australia Południowo-Wschodnia (ASE), Kanada Środkowa (CNC), zachodnio-środkowe stany USA (WCUS), zachodnie stany USA 2 (WUS 2), Indie Południowe (INS), Północno-środkowe stany USA (NCUS), Japonia Wschodnia (JPE), Brazylia Południowa (BRS), Południowe Azja Wschodnia (morze), Szwajcaria Zachodnia (SZW), Zjednoczone Emiraty Arabskie (UAC), Norwegia Wschodnia (NWE), Indie Zachodnie (INW), Australia Środkowa (ACL), Korea Środkowa (KRC), Japonia Zachodnia (JPW), Północna Republika Południowej Afryki (SAN), Zachodnie Zjednoczone Królestwo , Korea Południowa (KRS), Niemcy Północne (GN), Norwegia Zachodnia (NWW), Zachodnia Republika Południowej Afryki (Piła), Szwajcaria Północna (SZN), Niemcy Środkowo-Zachodnie (GWC), Zjednoczone Emiraty Arabskie (UAN), Francja Środkowa (FRC), Indie Środkowe (INC), Kanada Wschodnia (CNE), (EA), Azja Wschodnia Australia Wschodnia (AE), środkowe stany USA (ci), zachodnie stany USA (WUS)                                                  |  Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Europa Północna (NE), Południowo-środkowe stany USA (SCUS), Południowe Zjednoczone Królestwo (UKS), Europa Zachodnia (my), US Gov Arizona (UGA), US Gov Teksas (UGT), US Gov Wirginia (UGV)           |

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

| Szczegóły konta magazynu | Pomoc techniczna                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Rodzaj konta            | Azure Backup obsługuje udziały plików platformy Azure obecne w ogólnego przeznaczenia w wersji 1, w wersji 2 i w przypadku typów magazynu plików |
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

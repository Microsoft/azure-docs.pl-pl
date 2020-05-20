---
title: Macierz obsługi dla kopii zapasowej udziału plików platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 42578cc83ef193801fa700ec7d136385411e5f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684625"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Macierz obsługi dla kopii zapasowej udziału plików platformy Azure

Za pomocą [usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) można tworzyć kopie zapasowe udziałów plików platformy Azure. Ten artykuł podsumowuje ustawienia pomocy technicznej podczas tworzenia kopii zapasowych udziałów plików platformy Azure za pomocą Azure Backup.

## <a name="supported-geos"></a>Obsługiwane GEOREGIONY

Kopia zapasowa dla udziałów plików platformy Azure jest dostępna w następujących GEOREGIONY:

**Regiony ga**:<br>
Australia Południowo-Wschodnia (ASE), Kanada Środkowa (CNC), zachodnio-środkowe stany USA (WCUS), Południowo-środkowe stany USA (SCUS), zachodnie stany USA 2 (WUS 2), Indie Południowe (INS), Północno-środkowe stany USA (NCUS), Japonia Wschodnia (JPE), Brazylia Południowa (BRS), Południowe Azja Wschodnia (morski), Szwajcaria Zachodnia (szw), Zjednoczone Emiraty Arabskie (UAC), Norwegia Wschodnia (NWE), Indie Zachodnie (inw), Australia Środkowa (ACL), Korea Środkowa (KRC), Japonia Zachodnia Północna Republika Południowej Afryki , Południowe Zjednoczone Królestwo (UKS), Zachodnie Zjednoczone Królestwo (UKW), Korea Południowa (KRS), Europa Północna (NE), Niemcy Północne (GN), Norwegia Zachodnia (NWW), Zachodnia Republika Południowej Afryki (Piła), Szwajcaria Północna (SZN), Niemcy Środkowo-Zachodnie (GWC), Zjednoczone Emiraty Arabskie (UAN), Francja Środkowa (FRC), Indie Środkowe (INC), Kanada Wschodnia (CNE), (EA), Australia Wschodnia (AE), środkowe stany USA (CUS), zachodnie stany USA (WUS) Azja Wschodnia, US gov Arizona (uga) , US Gov Wirginia (UGV), US DoD (region środkowy) (UDC), US DoD (region wschodni) (LUCZ)

**Obsługiwane regiony (w ramach wersji zapoznawczej), ale nie są jeszcze ga**:<br>
Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Europa Zachodnia (my)

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
| Duży                                              | Obsługiwane |
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

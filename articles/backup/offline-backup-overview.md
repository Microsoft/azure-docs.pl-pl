---
title: Przegląd kopii zapasowej offline
description: Dowiedz się więcej o składnikach kopii zapasowej offline. Obejmują one kopie zapasowe offline na podstawie Azure Data Box i kopii zapasowej offline na podstawie usługi Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704139"
---
# <a name="overview-of-offline-backup"></a>Przegląd kopii zapasowej offline

Ten artykuł zawiera omówienie kopii zapasowej offline.

Początkowe pełne kopie zapasowe na platformie Azure zazwyczaj przesyłają duże ilości danych w trybie online i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko przyrostowe zmiany. Biura zdalne lub centra danych w niektórych lokalizacje geograficzne nie zawsze mają wystarczającą przepustowość sieci. Z tego powodu te początkowe kopie zapasowe trwają kilka dni. W tym czasie kopie zapasowe stale używają tej samej sieci, która została zainicjowana dla aplikacji działających w lokalnym centrum danych.

Azure Backup obsługuje kopię zapasową offline, która przenosi dane początkowej kopii zapasowej w trybie offline bez użycia przepustowości sieci. Zapewnia mechanizm kopiowania danych kopii zapasowej na fizyczne urządzenia magazynujące. Urządzenia są następnie dostarczane do centrum danych platformy Azure w pobliżu i przekazywane do magazynu Recovery Servicesowego. Ten proces zapewnia niezawodny transfer danych kopii zapasowej bez użycia przepustowości sieci.

## <a name="offline-backup-options"></a>Opcje kopii zapasowej offline

Kopia zapasowa offline jest oferowana w dwóch trybach na podstawie własności urządzeń magazynujących:

- Kopia zapasowa offline oparta na Azure Data Box (wersja zapoznawcza)
- Kopia zapasowa offline oparta na usłudze Azure Import/Export

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Kopia zapasowa offline oparta na Azure Data Box (wersja zapoznawcza)

Ten tryb jest obecnie obsługiwany przez agenta Microsoft Azure Recovery Services (MARS) w wersji zapoznawczej. Ta opcja wykorzystuje [Azure Data Box](https://azure.microsoft.com/services/databox/) do dostarczania urządzeń do transferowania własnościowego firmy Microsoft, bezpiecznych i odpornych na manipulację z łącznikami USB do centrum danych lub biura zdalnego. Dane kopii zapasowej są bezpośrednio zapisywane na tych urządzeniach. Ta opcja umożliwia zaoszczędzenie nakładu pracy wymaganego do pozyskania własnych dysków i łączników zgodnych z platformą Azure albo w celu udostępnienia tymczasowego magazynu jako lokalizacji tymczasowej. Firma Microsoft obsługuje również kompleksową logistykę transferu, którą można śledzić za pomocą Azure Portal.

Poniżej przedstawiono architekturę opisującą przenoszenie danych kopii zapasowej za pomocą tej opcji.

![Architektura urządzenie Data Box Azure Backup](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Oto podsumowanie architektury:

1. Azure Backup bezpośrednio kopiuje dane kopii zapasowej do tych wstępnie skonfigurowanych urządzeń.
2. Następnie można wysłać te urządzenia z powrotem do centrum danych platformy Azure.
3. Azure Data Box kopiuje dane na konto magazynu należące do klienta.
4. Azure Backup automatycznie kopiuje dane kopii zapasowej z konta magazynu do wyznaczono magazynu Recovery Services. Planowane są przyrostowe kopie zapasowe w trybie online.

Aby użyć kopii zapasowej offline w oparciu o Azure Data Box, zobacz [kopia zapasowa offline przy użyciu Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Kopia zapasowa offline oparta na usłudze Azure Import/Export

Ta opcja jest obsługiwana przez Microsoft Azure Backup Server (serwera usługi MAB), System Center Data Protection Manager (DPM) DPM-A i Agent MARS. Korzysta ona z [usługi Azure Import/Export](../import-export/storage-import-export-service.md). Dane początkowej kopii zapasowej można przenieść na platformę Azure przy użyciu własnych dysków i łączników zgodnych z platformą Azure. Takie podejście wymaga udostępnienia tymczasowego magazynu znanego jako lokalizacja przejściowa i użycia wstępnie skompilowanych narzędzi do formatowania i kopiowania danych kopii zapasowej na dyski należące do klienta.

Poniżej przedstawiono architekturę opisującą przenoszenie danych kopii zapasowej za pomocą tej opcji.

![Azure Backup importowania/eksportowania architektury usług](./media/offline-backup-overview/azure-backup-import-export.png)

Oto podsumowanie architektury:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, Azure Backup zapisuje dane kopii zapasowej w lokalizacji tymczasowej.
2. Dane w lokalizacji tymczasowej są zapisywane na jednym lub większej liczbie dysków SATA przy użyciu niestandardowego narzędzia.
3. W ramach pracy przygotowawczej narzędzie tworzy zadanie importu platformy Azure. Dyski SATA są dostarczane do najbliższego centrum danych platformy Azure i odwołują się do zadania importowania w celu połączenia działań.
4. W centrum danych Azure dane na dyskach są kopiowane do konta usługi Azure Storage.
5. Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu Recovery Services. Przyrostowe kopie zapasowe są zaplanowane.

Aby użyć kopii zapasowej offline opartej na usłudze Azure Import/Export za pomocą agenta MARS, zobacz [przepływ pracy kopii zapasowej offline w Azure Backup](./backup-azure-backup-import-export.md).

Aby użyć tego samego programu wraz z serwera usługi MAB lub DPM-A, zobacz [przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Podsumowanie obsługi kopii zapasowych w trybie offline

Poniższa tabela zawiera porównanie dwóch dostępnych opcji, dzięki czemu można wybrać odpowiednie opcje na podstawie Twojego scenariusza.

| **Kwestie do rozważenia**                                            | **Kopia zapasowa offline oparta na Azure Data Box**                     | **Kopia zapasowa offline oparta na usłudze Azure Import/Export**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup modele wdrażania                              | Agent MARS (wersja zapoznawcza)                                              | Agent MARS, serwera usługi MAB, DPM-A                                           |
| Maksymalna ilość danych kopii zapasowej na serwer (MARS) lub na grupę ochrony (serwera usługi MAB, DPM-A) | [Azure Data Box dysk](../databox/data-box-disk-overview.md) — 7,2 TB <br> [Azure Data Box](../databox/data-box-overview.md) – 80 TB       | 80 TB (do 10 dysków o pojemności 8 TB)                          |
| Zabezpieczenia (dane, urządzenia i usługa)                           | [Data](../databox/data-box-security.md#data-box-data-protection) -AES 256-bit szyfrowany <br> Przypadek niezależny od [urządzenia](../databox/data-box-security.md#data-box-device-protection) , własnościowy interfejs oparty na poświadczeniach do kopiowania danych <br> [Usługa](../databox/data-box-security.md#data-box-service-protection) chroniona przez funkcje zabezpieczeń platformy Azure | Dane — szyfrowanie funkcją BitLocker                                 |
| Tymczasowa lokalizacja tymczasowa aprowizacji                     | Niewymagane                                                | Większy niż lub równy szacowanemu rozmiarowi danych kopii zapasowej        |
| Obsługiwane regiony                                           | [Azure Data Box regionów dysków](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box regiony](../databox/data-box-disk-overview.md#region-availability) | [Regiony usługi Azure Import/Export](../import-export/storage-import-export-service.md#region-availability) |
| Wysyłka między krajami                                     | Nieobsługiwane  <br>    Adres źródłowy i docelowy centrum danych platformy Azure muszą znajdować się w tym samym kraju/regionie * | Obsługiwane                                                    |
| Przenoszenie logistyki (dostarczanie, transport, odbiór)           | W pełni zarządzane firmy Microsoft                                     | Zarządzane przez klienta                                            |
| Ceny                                                      | [Cennik Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box ceny dysku](https://azure.microsoft.com/pricing/details/databox/disk/) | [Cennik usługi Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Jeśli kraj/region nie ma centrum danych platformy Azure, musisz dostarczyć dyski do centrum danych platformy Azure w innym kraju/regionie.

## <a name="next-steps"></a>Następne kroki

- [Azure Backup kopii zapasowej offline przy użyciu Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Przepływ pracy kopii zapasowej offline w Azure Backup](backup-azure-backup-import-export.md)
- [Przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server](backup-azure-backup-server-import-export.md)

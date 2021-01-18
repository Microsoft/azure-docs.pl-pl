---
title: Tabela obsługi kopii zapasowych platformy SAP HANA
description: W tym artykule omówiono obsługiwane scenariusze i ograniczenia dotyczące tworzenia kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure za pomocą Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: 5065f4cf3d9ab75c6e3b682b6a24387ce7449b0f
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562662"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Macierz obsługi dla kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Azure Backup obsługuje tworzenie kopii zapasowych baz danych SAP HANA na platformie Azure. W tym artykule przedstawiono podsumowanie obsługiwanych scenariuszy i ograniczeń występujących podczas tworzenia kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure za pomocą Azure Backup.

> [!NOTE]
> Częstotliwość wykonywania kopii zapasowych dziennika może teraz wynosić co najmniej 15 minut. Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej bazy danych.

## <a name="scenario-support"></a>Obsługa scenariuszy

| **Scenariusz**               | **Obsługiwane konfiguracje**                                | **Nieobsługiwane konfiguracje**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA działa tylko na maszynach wirtualnych z systemem Linux Azure                    | Duże wystąpienia HANA (HLI)                                   |
| **Regiony**                   | **POWSZECHNE**<br> **Ameryki** — środkowe stany USA, Wschodnie stany USA 2, Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, zachodnie stany USA, Europa Zachodnia, Kanada środkowa, Kanada Wschodnia, Brazylia Południowa <br> **Azja i Pacyfik** — Australia Środkowa, Australia Środkowa 2, Australia Wschodnia, Australia Południowo-Wschodnia, Japonia Zachodnia, Japonia zachodni, Korea środkowa, Korea Południowa, Republika Azja Wschodnia Południowej południowo-wschodnia, Chiny Północne Chiny Północne Chiny Wschodnie Indie Południowe <br> **Europa** — Europa Zachodnia, Europa Północna, Francja środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Niemcy Północne, Niemcy Środkowo-Zachodnie, Szwajcaria Północna, Szwajcaria Zachodnia, środkowe Szwajcaria Północna, Norwegia Wschodnia, Norwegia Zachodnia <br> **Afryka/Me** -Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki, Zjednoczone Emiraty Arabskie, środkowe Zjednoczone Emiraty Arabskie  <BR>  **Regiony Azure Government** | Francja Południowa, Niemcy środkowe, Niemcy północny, US Gov IOWA |
| **Wersje systemu operacyjnego**            | SLES 12 z dodatkiem SP2, SP3, SP4 i SP5; SLES 15 z SP0, SP1, SP2 <br><br>  Od 1 sierpnia 2020 SAP HANA kopia zapasowa RHEL (7,4, 7,6, 7,7 & 8,1) jest ogólnie dostępna.                |                                             |
| **Wersje platformy HANA**          | SDC na platformie HANA 1. x, MDC w systemie HANA 2. x <= SPS04 Rev 53, SPS05 (jeszcze nie można sprawdzić poprawności scenariuszy z włączoną obsługą szyfrowania)      |                                                            |
| **Wdrożenia HANA**       | SAP HANA pojedynczą maszynę wirtualną platformy Azure — skalowanie w górę. <br><br> W przypadku wdrożeń o wysokiej dostępności oba węzły są traktowane jako pojedyncze węzły z osobnymi łańcuchami danych.               | Skalowanie w poziomie <br><br> W przypadku wdrożeń o wysokiej dostępności kopia zapasowa nie jest automatycznie przełączena w tryb failover do węzła pomocniczego. Konfigurowanie kopii zapasowej należy wykonać oddzielnie dla każdego węzła.                                           |
| **Wystąpienia HANA**         | Pojedyncze wystąpienie SAP HANA na pojedynczej maszynie wirtualnej platformy Azure — skalowanie w górę | Wiele wystąpień SAP HANA na jednej maszynie wirtualnej. W danym momencie można chronić tylko jeden z tych wielu wystąpień.                  |
| **Typy baz danych HANA**    | Kontener pojedyncza baza danych (SDC) na 1. x, kontener wielobaza danych (MDC) w 2. x | MDC w HANA 1. x                                              |
| **Rozmiar bazy danych HANA**     | Bazy danych HANA o rozmiarze <= 2 TB (nie jest to rozmiar pamięci systemu HANA)               |                                                              |
| **Typy kopii zapasowych**           | Pełne, różnicowe, przyrostowe (wersja zapoznawcza) i dzienniki kopii zapasowych                          |  Migawki                                       |
| **Typy przywracania**          | Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby dowiedzieć się więcej o obsługiwanych typach przywracania |                                                              |
| **Limity kopii zapasowych**          | Do 2 TB rozmiaru pełnej kopii zapasowej na wystąpienie SAP HANA (limit elastyczny)         |                                                              |
| **Konfiguracje specjalne** |                                                              | SAP HANA + warstwa dynamiczna <br>  Klonowanie za poorednictwem LaMa        |

------

>[!NOTE]
>Azure Backup nie dostosowuje się automatycznie podczas tworzenia kopii zapasowej bazy danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure.
>
>Zmodyfikuj zasady ręcznie w razie konieczności.

> [!NOTE]
> Teraz można [monitorować zadania tworzenia kopii zapasowej i przywracania](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (na tym samym komputerze) wyzwalane przez klientów natywnych platformy HANA (SAP HANA Studio/Panel sterowania/dba) w Azure Portal.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA na maszynach wirtualnych platformy Azure](./backup-azure-sap-hana-database.md)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](./sap-hana-db-restore.md)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md)

---
title: Azure Backup macierz obsługi dla usługi SQL Server Backup na platformie Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dotyczących SQL Server kopii zapasowej maszyn wirtualnych platformy Azure za pomocą Azure Backup usługi.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: 354f64eb86cd545860c47562fba7ff43babe72ca
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714150"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Macierz obsługi dla usługi SQL Server Backup na platformie Azure

Za pomocą Azure Backup kopii zapasowej baz danych SQL Server maszyn wirtualnych platformy Azure hostowanych na platformie Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie ogólnych ustawień pomocy technicznej i ograniczeń dotyczących scenariuszy i wdrożeń usługi SQL Server Backup na platformie Azure.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane regiony** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE), Australia Środkowa (AC), Australia Środkowa 2 (AC) <br> Brazylia Południowa (BRS)<br> Kanada Środkowa (KANADA), Kanada Wschodnia (CE)<br> South Azja Wschodnia (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Zachodnio-środkowe stany USA (WCUS), Zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2) Północno-środkowe stany USA (NCUS) Środkowe stany USA (CUS) Południowo-środkowe stany USA (SCUS) <br> Indie Środkowe (INC), Indie Południowe (INS), Indie Zachodnie <br> Japonia Wschodnia (JPE), Japonia Zachodnia (JPW) <br> Korea Środkowa (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Południowe Zjednoczone Królestwo (UKS), Zachodnie Zjednoczone Królestwo (UKW) <br> US Gov Arizona, US Gov Wirginia, US Gov Teksas, US DoD (region środkowy), US DoD (region wschodni) <br> Niemcy Północne, Niemcy Zachodnio-środkowe <br> Korea Północna, Zachodnia Szwajcaria <br> Francja Środkowa <br> Chiny Wschodnie, Chiny Wschodnie 2, Chiny Północne, Chiny Północne 2
**Obsługiwane systemy operacyjne** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2019 r. SQL Server 2017 r. zgodnie ze szczegółowymi informacjami na stronie Cykl życia produktu search [,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)SQL Server 2016 i SPs zgodnie ze szczegółowymi informacjami na stronie Cykl życia produktu wyszukiwania [,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Wersje lokalnej bazy danych Express nie są obsługiwane.
**Obsługiwane wersje programu .NET** | .NET Framework zainstalowaną na maszynie wirtualnej w programie 4.5.2 lub nowszym

## <a name="feature-considerations-and-limitations"></a>Zagadnienia i ograniczenia dotyczące funkcji

|Ustawienie  |Limit maksymalny |
|---------|---------|
|Liczba baz danych, które mogą być chronione na serwerze (i w magazynie)    |   2000      |
|Obsługiwany rozmiar bazy danych (poza tym mogą wystąpić problemy z wydajnością)   |   6 TB*      |
|Liczba plików obsługiwanych w bazie danych    |   1000      |

_*Limit rozmiaru bazy danych zależy od szybkości transferu danych, która jest przez nas wspierana, oraz od konfiguracji limitu czasu wykonywania kopii zapasowej. Nie jest to trudny limit. [Dowiedz się więcej na](#backup-throughput-performance) temat wydajności przepływności kopii zapasowych._

* SQL Server kopii zapasowej można skonfigurować w programie Azure Portal lub **PowerShell.** Interfejs wiersza polecenia nie jest obsługiwany.
* Rozwiązanie jest obsługiwane w obu [rodzajach wdrożeń](../azure-resource-manager/management/deployment-models.md) — na Azure Resource Manager wirtualnych i klasycznych maszyn wirtualnych.
* Obsługiwane są wszystkie typy kopii zapasowych (pełne/różnicowe/dziennika) i modele odzyskiwania (proste/pełne/zarejestrowane zbiorczo).
* W **przypadku baz danych tylko do** odczytu: pełne i tylko do kopiowania pełne kopie zapasowe są jedynymi obsługiwanymi typami kopii zapasowych.
* Natywna kompresja SQL jest obsługiwana, jeśli została jawnie włączona przez użytkownika w zasadach tworzenia kopii zapasowych. Azure Backup zastępuje wartości domyślne na poziomie wystąpienia klauzulą COMPRESSION/NO_COMPRESSION, w zależności od wartości tej kontrolki ustawionej przez użytkownika.
* TDE — obsługiwana jest kopia zapasowa bazy danych z włączoną obsługą programu . Aby przywrócić bazę danych zaszyfrowaną przy użyciu szyfrowania TDE do SQL Server, należy najpierw przywrócić certyfikat [na serwerze docelowym.](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server) Kompresja kopii zapasowych baz danych z obsługą funkcji TDE dla SQL Server 2016 i nowszej jest dostępna, ale przy niższym rozmiarze transferu, jak [wyjaśniono tutaj.](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* Operacje tworzenia kopii zapasowych i przywracania dublowania baz danych i migawek baz danych nie są obsługiwane.
* SQL Server klastra **trybu failover (FCI)** nie jest obsługiwane.
* Używanie więcej niż jednego rozwiązania do tworzenia kopii zapasowych w celu tworzenia kopii zapasowej autonomicznego wystąpienia SQL Server lub zawsze wł. grupy dostępności SQL może prowadzić do niepowodzenia tworzenia kopii zapasowej. Nie należy tego robić. Tworzenie kopii zapasowej dwóch węzłów grupy dostępności indywidualnie przy użyciu tego samego lub różnych rozwiązań może również prowadzić do niepowodzenia tworzenia kopii zapasowej.
* Po skonfigurowaniu grup dostępności kopie zapasowe są podejmowane z różnych węzłów na podstawie kilku czynników. Poniżej przedstawiono podsumowanie zachowania tworzenia kopii zapasowej dla grupy dostępności.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Zachowanie podczas tworzenia kopii zapasowej w przypadku zawsze włączonych grup dostępności

Zaleca się skonfigurowanie kopii zapasowej tylko w jednym węźle grupy dostępności. Zawsze konfiguruj kopię zapasową w tym samym regionie co węzeł podstawowy. Innymi słowy, zawsze potrzebny jest węzeł podstawowy, który będzie obecny w regionie, w którym konfigurujesz kopię zapasową. Jeśli wszystkie węzły agd znajdują się w tym samym regionie, w którym skonfigurowano kopię zapasową, nie ma żadnych obaw.

#### <a name="for-cross-region-ag"></a>W przypadku między regionami ( AG)

* Niezależnie od preferencji tworzenia kopii zapasowych kopie zapasowe będą uruchamiane tylko z węzłów, które znajdują się w tym samym regionie, w którym skonfigurowano kopię zapasową. Wynika to z tego, że kopie zapasowe między regionami nie są obsługiwane. Jeśli masz tylko dwa węzły, a węzeł pomocniczy znajduje się w innym regionie, kopie zapasowe będą nadal uruchamiane z węzła podstawowego (chyba że preferencja tworzenia kopii zapasowych jest "tylko pomocnicza").
* Jeśli węzeł ulegnie awarii w regionie innym niż ten, w którym skonfigurowano kopię zapasową, tworzenie kopii zapasowych w węzłach w regionie pracy awaryjnej zakończy się niepowodzeniem.

W zależności od preferencji tworzenia kopii zapasowych i typów kopii zapasowych (pełny/różnicowy/dziennik/tylko kopia-pełna) kopie zapasowe są podejmowane z określonego węzła (podstawowego/pomocniczego).

#### <a name="backup-preference-primary"></a>Preferencja tworzenia kopii zapasowej: Podstawowa

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicowe | Podstawowe
Dziennik |  Podstawowe
Copy-Only pełne |  Podstawowe

#### <a name="backup-preference-secondary-only"></a>Preferencja tworzenia kopii zapasowych: Tylko pomocnicza

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicowe | Podstawowe
Dziennik |  Pomocniczy
Copy-Only pełne |  Pomocniczy

#### <a name="backup-preference-secondary"></a>Preferencja tworzenia kopii zapasowych: pomocnicza

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicowe | Podstawowe
Dziennik |  Pomocniczy
Copy-Only pełne |  Pomocniczy

#### <a name="no-backup-preference"></a>Brak preferencji tworzenia kopii zapasowych

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicowe | Podstawowe
Dziennik |  Pomocniczy
Copy-Only pełne |  Pomocniczy

## <a name="backup-throughput-performance"></a>Wydajność przepływności kopii zapasowej

Azure Backup obsługuje spójny transfer danych o szybkości 200 Mb/s dla pełnych i różnicowych kopii zapasowych dużych baz danych SQL (500 GB). Aby wykorzystać optymalną wydajność, upewnij się, że:

- Podstawowa maszyna wirtualna (zawierająca SQL Server, które hostuje bazę danych) jest skonfigurowana z wymaganą przepływnością sieci. Jeśli maksymalna przepływność maszyny wirtualnej jest mniejsza niż 200 Mb/s, Azure Backup nie może przesyłać danych z optymalną szybkością.<br>Ponadto dysk, który zawiera pliki bazy danych, musi mieć wystarczającą przepływność aprowizowana. [Dowiedz się więcej](../virtual-machines/disks-performance.md) o przepływności i wydajności dysków na platformie Azure. 
- Procesy działające na maszynie wirtualnej nie zużywają przepustowości maszyny wirtualnej. 
- Harmonogramy tworzenia kopii zapasowych są rozłożone na podzestaw baz danych. Wiele kopii zapasowych uruchomionych jednocześnie na maszynie wirtualnej współużli szybkość zużycia sieci między kopiami zapasami. [Dowiedz się więcej](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) o tym, jak kontrolować liczbę współbieżnych kopii zapasowych.

>[!NOTE]
> [Pobierz szczegółowy planista zasobów,](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) aby obliczyć przybliżoną liczbę chronionych baz danych zalecanych na serwer na podstawie zasobów maszyny wirtualnej, przepustowości i zasad tworzenia kopii zapasowych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak wrócić do SQL Server bazy](backup-azure-sql-database.md) danych działającej na maszynie wirtualnej platformy Azure.
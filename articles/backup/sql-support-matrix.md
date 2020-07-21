---
title: Macierz obsługi Azure Backup dla SQL Server tworzenia kopii zapasowych na maszynach wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej podczas tworzenia kopii zapasowych SQL Server na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 4d197f8b3c1ed74ef45c1f7942ead52ccef0c14a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513187"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Macierz obsługi SQL Server tworzenia kopii zapasowych na maszynach wirtualnych platformy Azure

Za pomocą Azure Backup można tworzyć kopie zapasowe baz danych SQL Server na maszynach wirtualnych platformy Azure hostowanych na platformie Microsoft Azure platformy w chmurze. Ten artykuł zawiera podsumowanie ogólnych ustawień pomocy technicznej i ograniczeń dotyczących scenariuszy i wdrożeń SQL Server kopii zapasowych na maszynach wirtualnych platformy Azure.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane regiony** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE), Australia Środkowa (AC), Australia Środkowa 2 (AC) <br> Brazylia Południowa (BRS)<br> Kanada Środkowa (CNC), Kanada Wschodnia (CE)<br> Azja Wschodnia Południowe (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), zachodnio-środkowe stany USA (WCUS), zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2) Północno-środkowe stany USA (NCUS) środkowe stany USA (CUS) Południowo-środkowe stany USA (SCUS) <br> Indie Środkowe (INC.), Indie Południowe, Indie Zachodnie <br> Japonia Wschodnia (JPE), Japonia Zachodnia (JPW) <br> Korea Środkowa (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Południowe Zjednoczone Królestwo (UKS), Zachodnie Zjednoczone Królestwo (UKW) <br> US Gov Arizona, US Gov Wirginia, US Gov Teksas, US DoD (region środkowy), US DoD (region wschodni) <br> Niemcy Północne, Niemcy Środkowo-Zachodnie <br> Szwajcaria Północna, Szwajcaria Zachodnia <br> Francja Środkowa <br> Chiny Wschodnie, Chiny Wschodnie 2, Chiny Północne, Chiny Północne 2
**Obsługiwane systemy operacyjne** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 z dodatkiem SP1 <br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2019, SQL Server 2017 zgodnie z opisem na [stronie cykl życia produktu](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 i SPS zgodnie ze szczegółowymi informacjami na [stronie cykl życia produktu search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Obsługiwane wersje platformy .NET** | .NET Framework 4.5.2 lub nowszy jest zainstalowany na maszynie wirtualnej

## <a name="feature-consideration-and-limitations"></a>Zagadnienia i ograniczenia dotyczące funkcji

* SQL Server kopii zapasowej można skonfigurować w Azure Portal lub **PowerShell**. Interfejs wiersza polecenia nie jest obsługiwany.
* Rozwiązanie jest obsługiwane w przypadku obu rodzajów [wdrożeń](../azure-resource-manager/management/deployment-models.md) — Azure Resource Manager maszyn wirtualnych i klasycznych maszyn wirtualnych.
* Maszyna wirtualna z systemem SQL Server wymaga łączności z Internetem, aby uzyskać dostęp do publicznych adresów IP platformy Azure.
* SQL Server **wystąpienie klastra trybu failover (FCI)** nie jest obsługiwane.
* Operacje tworzenia kopii zapasowych i przywracania dla duplikatów baz danych i migawek baz danych nie są obsługiwane.
* Użycie więcej niż jednego rozwiązania do tworzenia kopii zapasowych w celu utworzenia kopii zapasowej autonomicznego wystąpienia SQL Server lub zawsze włączona Grupa dostępności programu SQL Server może prowadzić do awarii kopii zapasowej; nie należy tego robić.
* Tworzenie kopii zapasowej dwóch węzłów grupy dostępności indywidualnie z tymi samymi lub różnymi rozwiązaniami może również prowadzić do niepowodzenia kopii zapasowych.
* Azure Backup obsługuje tylko pełne i pełne kopie zapasowe tylko dla baz danych **tylko do odczytu**
* Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba obsługiwanych plików to **~ 1000**.  
* Można utworzyć kopię zapasową do **~ 2000** SQL Server baz danych w magazynie. Możesz utworzyć wiele magazynów, jeśli masz większą liczbę baz danych.
* Można skonfigurować kopie zapasowe dla maksymalnie **50** baz danych w jednym z nich. to ograniczenie ułatwia optymalizację obciążeń kopii zapasowych.
* Obsługiwane są bazy danych o rozmiarze do **2 TB** . w przypadku większych rozmiarów mogą wystąpić problemy z wydajnością.
* Aby określić, jak wiele baz danych może być chronionych na serwer, należy wziąć pod uwagę takie czynniki jak przepustowość, rozmiar maszyny wirtualnej, częstotliwość tworzenia kopii zapasowych, rozmiar bazy danych i tak dalej. [Pobierz](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) planistę zasobów, aby obliczyć przybliżoną liczbę baz danych, które mogą być dostępne dla każdego serwera na podstawie zasobów maszyny wirtualnej i zasad tworzenia kopii zapasowych.
* Po skonfigurowaniu grup dostępności kopie zapasowe są pobierane z różnych węzłów w oparciu o kilka czynników. Zachowanie tworzenia kopii zapasowej dla grupy dostępności znajduje się poniżej.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Zachowanie podczas tworzenia kopii zapasowej w przypadku zawsze włączonych grup dostępności

Zaleca się, aby kopie zapasowe zostały skonfigurowane tylko na jednym węźle grupy dostępności (AG). Zawsze Konfiguruj kopię zapasową w tym samym regionie co węzeł podstawowy. Innymi słowy, zawsze potrzebny jest węzeł podstawowy, który ma być obecny w regionie, w którym jest konfigurowana kopia zapasowa. Jeśli wszystkie węzły w AG znajdują się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa, nie ma żadnego problemu.

#### <a name="for-cross-region-ag"></a>W przypadku międzyregionowej AG

* Niezależnie od preferencji tworzenia kopii zapasowej kopie zapasowe będą uruchamiane tylko z węzłów znajdujących się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa. Wynika to z faktu, że kopie zapasowe między regionami nie są obsługiwane. Jeśli masz tylko dwa węzły, a węzeł pomocniczy znajduje się w innym regionie, kopie zapasowe będą nadal uruchamiane z węzła podstawowego (chyba że preferencja kopii zapasowej to "tylko pomocnicze").
* Jeśli węzeł jest przełączany w tryb failover do regionu innego niż ten, w którym jest skonfigurowana kopia zapasowa, kopie zapasowe będą kończyć się niepowodzeniem w węzłach w regionie przełączenia w tryb failover.

W zależności od preferencji tworzenia kopii zapasowych i typów kopii zapasowych (pełna/różnicowa/log/Copy-Only) kopie zapasowe są pobierane z określonego węzła (podstawowy/pomocniczy).

#### <a name="backup-preference-primary"></a>Preferencja kopii zapasowej: podstawowa

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicy | Podstawowe
Log |  Podstawowe
Tylko kopiowanie pełne |  Podstawowe

#### <a name="backup-preference-secondary-only"></a>Preferencja kopii zapasowej: tylko pomocnicza

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicy | Podstawowe
Log |  Pomocniczy
Tylko kopiowanie pełne |  Pomocniczy

#### <a name="backup-preference-secondary"></a>Preferencja kopii zapasowej: pomocnicza

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicy | Podstawowe
Log |  Pomocniczy
Tylko kopiowanie pełne |  Pomocniczy

#### <a name="no-backup-preference"></a>Brak preferencji dotyczących kopii zapasowych

**Typ kopii zapasowej** | **Węzeł**
--- | ---
Pełne | Podstawowe
Różnicy | Podstawowe
Log |  Pomocniczy
Tylko kopiowanie pełne |  Pomocniczy

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć kopię zapasową bazy danych SQL Server](backup-azure-sql-database.md) działającej na maszynie wirtualnej platformy Azure.

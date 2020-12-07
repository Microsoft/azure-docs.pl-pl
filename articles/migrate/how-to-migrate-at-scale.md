---
title: Automatyzowanie migracji maszyn migracji w Azure Migrate
description: Opisuje sposób używania skryptów do migrowania dużej liczby maszyn w Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754236"
---
# <a name="scale-migration-of-vms"></a>Skalowanie migracji maszyn wirtualnych 

Ten artykuł pomaga zrozumieć, jak używać skryptów do migrowania dużej liczby maszyn wirtualnych. Do skalowania migracji służy [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Skrypty Site Recovery są dostępne do pobrania w [Azure PowerShell repozytorium przykładów](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) w witrynie GitHub. Skrypty te mogą służyć do migrowania maszyn wirtualnych VMware, AWS, GCP i serwerów fizycznych do dysków zarządzanych na platformie Azure. Można również użyć tych skryptów do migrowania maszyn wirtualnych funkcji Hyper-V w przypadku migrowania maszyn wirtualnych jako serwerów fizycznych. Skrypty wykorzystujące Azure Site Recovery PowerShell są udokumentowane w [tym miejscu](../site-recovery/vmware-azure-disaster-recovery-powershell.md).

## <a name="current-limitations"></a>Bieżące ograniczenia
- Obsługa określania statycznego adresu IP tylko dla podstawowej karty sieciowej docelowej maszyny wirtualnej
- Skrypty nie pobierają Korzyść użycia hybrydowego platformy Azure powiązanych danych wejściowych, trzeba ręcznie zaktualizować właściwości zreplikowanej maszyny wirtualnej w portalu

## <a name="how-does-it-work"></a>Jak to działa?

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy wykonać następujące czynności:
- Upewnij się, że magazyn Site Recovery został utworzony w ramach subskrypcji platformy Azure
- Upewnij się, że serwer konfiguracji i serwer przetwarzania są zainstalowane w środowisku źródłowym, a magazyn jest w stanie odnaleźć środowisko
- Upewnij się, że zasady replikacji zostały utworzone i skojarzone z serwerem konfiguracji
- Upewnij się, że dodano konto administratora maszyny wirtualnej do serwera konfiguracji (który będzie używany do replikowania lokalnych maszyn wirtualnych)
- Upewnij się, że są tworzone artefakty docelowe na platformie Azure
    - Docelowa Grupa zasobów
    - Docelowe konto magazynu (i jego grupa zasobów) — Tworzenie konta magazynu w warstwie Premium, jeśli planujesz migrację do dysków zarządzanych w warstwie Premium
    - Konto magazynu pamięci podręcznej (i jego grupa zasobów) — Tworzenie konta magazynu w warstwie Standardowa w tym samym regionie co magazyn
    - Virtual Network docelowe dla trybu failover (i jego grupy zasobów)
    - Podsieć docelowa
    - Virtual Network docelowe dla testowego przełączania do trybu failover (i jego grupy zasobów)
    - Zestaw dostępności (w razie konieczności)
    - Docelowa sieciowa Grupa zabezpieczeń i jej Grupa zasobów
- Upewnij się, że wybrano właściwości docelowej maszyny wirtualnej
    - Nazwa docelowej maszyny wirtualnej
    - Docelowy rozmiar maszyny wirtualnej na platformie Azure (można podjąć decyzje przy użyciu oceny Azure Migrate)
    - Prywatny adres IP podstawowej karty sieciowej w maszynie wirtualnej
- Pobierz skrypty z repozytorium [przykładów Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) w witrynie GitHub

### <a name="csv-input-file"></a>Plik wejściowy CSV
Po zakończeniu wszystkich wymagań wstępnych należy utworzyć plik CSV zawierający dane dla każdej maszyny źródłowej, która ma zostać zmigrowana. Wejściowy plik CSV musi mieć wiersz nagłówka z danymi wejściowymi i wiersz zawierający szczegóły dla każdej maszyny, która ma zostać zmigrowana. Wszystkie skrypty zostały zaprojektowane tak, aby działały w tym samym pliku CSV. Przykładowy szablon CSV jest dostępny w folderze skryptów dla odwołania.

### <a name="script-execution"></a>Wykonywanie skryptu
Gdy wolumin CSV jest gotowy, możesz wykonać następujące kroki, aby przeprowadzić migrację lokalnych maszyn wirtualnych:

**Czynności #** | **Nazwa skryptu** | **Opis**
--- | --- | ---
1 | asr_startmigration.ps1 | Włącz replikację dla wszystkich maszyn wirtualnych wymienionych w woluminie CSV, skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej
2 | asr_replicationstatus.ps1 | Sprawdź stan replikacji, skrypt tworzy wolumin CSV ze stanem dla każdej maszyny wirtualnej
3 | asr_updateproperties.ps1 | Po replikacji/zabezpieczeniu maszyn wirtualnych Użyj tego skryptu, aby zaktualizować właściwości docelowe maszyny wirtualnej (właściwości obliczeniowe i sieciowe).
4 | asr_propertiescheck.ps1 | Sprawdź, czy właściwości zostały odpowiednio zaktualizowane
5 | asr_testmigration.ps1 |  Rozpocznij Test pracy w trybie failover maszyn wirtualnych znajdujących się w woluminie CSV, skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej
6 | asr_cleanuptestmigration.ps1 | Po ręcznym zweryfikowaniu maszyn wirtualnych, które przeprowadzono test zakończony niepowodzeniem, można użyć tego skryptu do oczyszczenia maszyn wirtualnych z testem pracy w trybie failover
7 | asr_migration.ps1 | Wykonaj niezaplanowaną pracę w trybie failover dla maszyn wirtualnych wymienionych w woluminie CSV skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej. Skrypt nie wyłącza lokalnych maszyn wirtualnych przed wyzwoleniem trybu failover w celu zapewnienia spójności aplikacji zaleca się ręczne wyłączenie maszyn wirtualnych przed wykonaniem skryptu.
8 | asr_completemigration.ps1 | Wykonywanie operacji zatwierdzania na maszynach wirtualnych i usuwanie Azure Site Recovery jednostek
9 | asr_postmigration.ps1 | Jeśli planujesz przypisywać sieciowe grupy zabezpieczeń do kart sieciowych po przejściu w tryb failover, możesz to zrobić za pomocą tego skryptu. Przypisuje sieciowej grupy zabezpieczeń do dowolnej karty sieciowej na docelowej maszynie wirtualnej.

## <a name="how-to-migrate-to-managed-disks"></a>Jak przeprowadzić migrację do dysków zarządzanych?
Skrypt domyślnie migruje maszyny wirtualne do dysków zarządzanych na platformie Azure. Jeśli podane konto magazynu jest kontem magazynu w warstwie Premium, dyski zarządzane w warstwie Premium są tworzone po migracji. Konto magazynu pamięci podręcznej nadal może być kontem standardowym. Jeśli docelowe konto magazynu jest kontem magazynu w warstwie Standardowa, dyski standardowe są tworzone po migracji. 

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](../site-recovery/migrate-tutorial-on-premises-azure.md) na temat migrowania serwerów na platformę Azure przy użyciu Azure Site Recovery

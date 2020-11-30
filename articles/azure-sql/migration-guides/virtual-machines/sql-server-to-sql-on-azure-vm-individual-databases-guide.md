---
title: SQL Server SQL Server na maszynach wirtualnych platformy Azure (Przewodnik migracji)
description: Postępuj zgodnie z tym przewodnikiem, aby przeprowadzić migrację pojedynczych baz danych SQL Server do SQL Server na platformie Azure Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 3b0fdccd3eaf6e6bd94b595107022f738bdd8382
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325925"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Przewodnik migracji: SQL Server SQL Server na maszynach wirtualnych platformy Azure 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

W tym przewodniku migracji nauczysz się **odkrywać**, **oceniać** i **migrować** bazy danych użytkowników z SQL Server do wystąpienia SQL Server na platformie Azure Virtual Machines (maszyny wirtualne) za pomocą kopii zapasowych i przywracania oraz wysyłania dzienników przy użyciu [Asystent migracji bazy danych (DMA)](/sql/dma/dma-overview) do oceny. 

Możesz migrować SQL Server uruchomione lokalnie lub na:

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)

Aby uzyskać informacje na temat dodatkowych strategii migracji, zobacz [Omówienie migracji maszyn wirtualnych SQL Server](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Przepływ procesu migracji":::

## <a name="prerequisites"></a>Wymagania wstępne

Migrowanie do SQL Server na maszynach wirtualnych platformy Azure wymaga następujących czynności: 

- [Asystent migracji bazy danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- [Projekt Azure Migrate](../../../migrate/create-manage-projects.md).
- Przygotowana [SQL Server ukierunkowana na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/create-sql-vm-portal.md) , która jest taka sama lub większa niż SQL Server źródłowa.
- [Łączność między platformą Azure i](/azure/architecture/reference-architectures/hybrid-networking)środowiskiem lokalnym.
- [Wybór odpowiedniej strategii migracji](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Przed migracją

Przed rozpoczęciem migracji Znajdź topologię środowiska SQL i Oceń wykonalność zamierzonej migracji. 

### <a name="discover"></a>Odnajdywanie

Azure Migrate ocenia przydatność migracji komputerów lokalnych, wykonuje ustalanie rozmiarów na podstawie wydajności i zapewnia oszacowanie kosztów do działania w środowisku lokalnym. Aby zaplanować migrację, użyj Azure Migrate, aby [zidentyfikować istniejące źródła danych i informacje o funkcjach](../../../migrate/concepts-assessment-calculation.md) używanych przez wystąpienia SQL Server. Ten proces polega na skanowaniu sieci w celu zidentyfikowania wszystkich wystąpień SQL Server w organizacji z użyciem używanej wersji i funkcji. 

> [!IMPORTANT]
> Podczas wybierania docelowej maszyny wirtualnej platformy Azure dla wystąpienia SQL Server należy wziąć pod uwagę [wytyczne dotyczące wydajności dla SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Dodatkowe narzędzia do odnajdywania znajdują się w temacie [usługi i narzędzia](../../../dms/dms-tools-matrix.md#business-justification-phase) dostępne dla scenariuszy migracji danych.


### <a name="assess"></a>Ocena

Po wykryciu wszystkich źródeł danych Użyj [Data Migration Assistant (DMA)](/sql/dma/dma-overview) do oceny wystąpień SQL Server lokalnych migracji do wystąpienia SQL Server na maszynie wirtualnej platformy Azure, aby zrozumieć luki między wystąpieniami źródłowym i docelowym. 


> [!NOTE]
> Jeśli _nie_ uaktualniasz wersji SQL Server, Pomiń ten krok i przejdź do sekcji [Migrowanie](#migrate) . 


#### <a name="assess-user-databases"></a>Ocenianie baz danych użytkowników 

Data Migration Assistant (DMA) pomaga przeprowadzić migrację do nowoczesnej platformy danych przez wykrywanie problemów ze zgodnością, które mogą mieć wpływ na funkcjonalność bazy danych w nowej wersji SQL Server. DMA zaleca ulepszenia wydajności i niezawodności dla środowiska docelowego, a także umożliwia przenoszenie schematu, danych i obiektów logowania z serwera źródłowego na serwer docelowy.

Zobacz [ocenę](/sql/dma/dma-migrateonpremsql) , aby dowiedzieć się więcej. 


> [!IMPORTANT]
>W oparciu o typ oceny uprawnienia wymagane na SQL Server źródłowym mogą być różne. 
   > - W przypadku doradcy z **parzystością funkcji** poświadczenia podane w celu nawiązania połączenia z bazą danych SQL Server źródłowej muszą być członkami roli serwera *sysadmin* .
   > - W przypadku klasyfikatora problemów ze zgodnością podane poświadczenia muszą mieć co najmniej `CONNECT SQL` `VIEW SERVER STATE` i `VIEW ANY DEFINITION` uprawnienia.
   > - Funkcja DMA będzie wyróżnić uprawnienia wymagane dla wybranego klasyfikatora przed uruchomieniem oceny.


#### <a name="assess-applications"></a>Ocenianie aplikacji

Zazwyczaj warstwa aplikacji uzyskuje dostęp do baz danych użytkowników w celu utrwalania i modyfikowania danych.  Dostęp DMA może ocenić warstwę dostępu do danych aplikacji na dwa sposoby: 

- Używanie przechwyconych [zdarzeń rozszerzonych](/sql/relational-databases/extended-events/extended-events) lub [SQL Server Profiler śladów ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) baz danych użytkownika. Możesz również użyć [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-capture-trace) , aby utworzyć dziennik śledzenia, który może być również używany do testowania a/B.

- Za pomocą [zestawu narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), który umożliwia odnajdywanie i ocenianie zapytań SQL w kodzie i służy do migrowania kodu źródłowego aplikacji z jednej platformy bazy danych do innej. To narzędzie obsługuje różne popularne typy plików, w tym C# i Java, XML i zwykły tekst. Przewodnik dotyczący sposobu przeprowadzania oceny DAMT można znaleźć w blogu [use DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Za pomocą funkcji DMA można [zaimportować](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) przechwycone pliki śledzenia lub pliki DAMT podczas oceny baz danych użytkowników. 


#### <a name="scale-assessments"></a>Oceny skali

Jeśli masz wiele serwerów, które wymagają oceny DMA, Możesz zautomatyzować proces za pomocą [interfejsu wiersza polecenia](/sql/dma/dma-commandline). Za pomocą interfejsu można z wyprzedzeniem przygotować polecenia oceny dla każdego wystąpienia SQL Server w zakresie migracji. 

W przypadku raportów podsumowujących w ramach dużych Estates można teraz skonsolidować oceny Data Migration Assistant (DMA) [do Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Refaktoryzacja baz danych za pomocą DMA

W oparciu o wyniki oceny DMA może istnieć szereg zaleceń zapewniających, że bazy danych użytkowników działają i działają prawidłowo po migracji. Dostęp DMA zawiera szczegółowe informacje o obiektach, których dotyczy problem, a także o sposobach rozwiązywania poszczególnych problemów. Zaleca się, aby wszystkie istotne zmiany i zmiany zachowania zostały rozwiązane przed migracją w środowisku produkcyjnym.

W przypadku przestarzałych funkcji można wybrać opcję uruchamiania baz danych użytkownika w ich oryginalnym trybie [zgodności](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) , jeśli chcesz uniknąć wprowadzania tych zmian i przyspieszenia migracji. Uniemożliwi to jednak [uaktualnienie zgodności bazy danych](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) do momentu rozwiązania przestarzałych elementów.

Zdecydowanie zaleca się, aby wszystkie poprawki DMA były skryptowe i stosowane do docelowej bazy danych SQL Server podczas [migracji](#post-migration).

> [!CAUTION]
> Nie wszystkie wersje SQL Server obsługują wszystkie tryby zgodności. Sprawdź, czy [docelowa wersja SQL Server](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) obsługuje wybraną zgodność bazy danych. Na przykład SQL Server 2019 nie obsługuje baz danych z poziomem zgodności poziomu 90 (czyli SQL Server 2005). Te bazy danych wymagają co najmniej uaktualnienia do poziomu zgodności 100.
>

## <a name="migrate"></a>Migrate (Migracja)

Po ukończeniu kroków poprzedzających migrację można przystąpić do migracji baz danych i składników użytkownika. Przeprowadź migrację baz danych przy użyciu preferowanej [metody migracji](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).  

Poniżej przedstawiono procedurę przeprowadzania migracji przy użyciu funkcji tworzenia kopii zapasowych i przywracania albo minimalnej czasu przestoju przy użyciu funkcji tworzenia kopii zapasowych i przywracania oraz wysyłania dzienników. 

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Aby przeprowadzić migrację standardową przy użyciu funkcji tworzenia kopii zapasowych i przywracania, wykonaj następujące kroki: 

1. Skonfiguruj łączność z SQL Server docelowego na maszynie wirtualnej platformy Azure zgodnie z wymaganiami. Zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Wstrzymaj/Zatrzymaj wszystkie aplikacje, które korzystają z baz danych przeznaczonych do migracji. 
1. Upewnij się, że bazy danych użytkowników są nieaktywne przy użyciu [trybu jednego użytkownika](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej.
1. Skopiuj lokalne pliki kopii zapasowej do maszyny wirtualnej za pomocą pulpitu zdalnego, [platformy Azure Eksplorator danych](/azure/data-explorer/data-explorer-overview)lub [narzędzia wiersza polecenia AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (zalecane > 2 TB kopii zapasowych).
1. Przywróć pełne kopie zapasowe bazy danych do SQL Server na maszynie wirtualnej platformy Azure.

### <a name="log-shipping--minimize-downtime"></a>Wysyłanie dziennika (Minimalizuj czas przestoju)

Aby przeprowadzić minimalną migrację za pomocą kopii zapasowych, przywracania i wysyłania dziennika, wykonaj następujące kroki: 

1. Skonfiguruj łączność do SQL Server docelowej na maszynie wirtualnej platformy Azure zgodnie z wymaganiami. Zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Upewnij się, że lokalne bazy danych użytkowników, które mają zostać zmigrowane, są w modelu odzyskiwania pełnego lub z pełnymi zabezpieczeniami.
1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej i zmodyfikuj wszystkie istniejące zadania pełnej kopii zapasowej bazy danych, aby użyć słowa kluczowego [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) w celu zachowania łańcucha dzienników.
1. Skopiuj lokalne pliki kopii zapasowej do maszyny wirtualnej za pomocą pulpitu zdalnego, [platformy Azure Eksplorator danych](/azure/data-explorer/data-explorer-overview)lub [narzędzia wiersza polecenia AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (zalecane >1 TB kopii zapasowych).
1. Przywróć pełne kopie zapasowe bazy danych na SQL Server na maszynie wirtualnej platformy Azure.
1. Skonfiguruj [wysyłanie dzienników](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) między lokalną bazą danych i SQL Server docelowy na maszynie wirtualnej platformy Azure. Nie należy ponownie inicjować baz danych, ponieważ zostały one już wykonane w poprzednich krokach.
1. **Wytnij** do serwera docelowego. 
   1. Wstrzymywanie/zatrzymywanie aplikacji przy użyciu baz danych, które mają zostać zmigrowane. 
   1. Upewnij się, że bazy danych użytkowników są nieaktywne przy użyciu [trybu jednego użytkownika](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Gdy wszystko będzie gotowe, przeprowadź kontrolę wysyłania dziennika w celu przełączenia w [tryb failover](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) lokalnych baz danych do SQL Server na maszynie wirtualnej platformy Azure.



### <a name="migrating-objects-outside-user-databases"></a>Migrowanie obiektów poza bazami danych użytkownika

Mogą istnieć dodatkowe obiekty SQL Server, które są wymagane do bezproblemowej pracy z bazami danych użytkownika po migracji. 

W poniższej tabeli przedstawiono składniki list i zalecane metody migracji, które można wykonać przed migracją baz danych użytkownika lub po niej: 


| **Funkcja** | **Składnik** | **Metody migracji** |
| --- | --- | --- |
| **Bazy danych** | Model  | Skrypt z SQL Server Management Studio |
|| TempDB | Zaplanuj przeniesienie bazy danych TempDB na [dysk tymczasowy maszyny wirtualnej platformy Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)w celu uzyskania najlepszej wydajności. Upewnij się, że wybrano rozmiar maszyny wirtualnej, który ma wystarczający lokalny dysk SSD, aby pomieścić bazę danych TempDB. |
|| Bazy danych użytkowników z FILESTREAM |  Użyj metod [tworzenia kopii zapasowych i przywracania](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) dla migracji. Funkcja DMA nie obsługuje baz danych z FILESTREAM. |
| **Bezpieczeństwo** | SQL Server i nazwy logowania systemu Windows | [Przeprowadź migrację logowań użytkowników](/sql/dma/dma-migrateserverlogins)przy użyciu konta DMA. |
|| Role SQL Server | Skrypt z SQL Server Management Studio |
|| Dostawcy usług kryptograficznych | Zalecamy [konwersję, aby używać usługi Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Ta procedura powoduje użycie [dostawcy zasobów maszyny wirtualnej SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Obiekty serwera** | Tworzenie kopii zapasowych urządzeń | Zamień na kopię zapasową bazy danych za pomocą [usługi Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md) lub Zapisz kopie zapasowe w usłudze [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 zastosujesz pakietu CU2 +). Ta procedura powoduje użycie [dostawcy zasobów maszyny wirtualnej SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Serwery połączone | Skrypt z SQL Server Management Studio. |
|| Wyzwalacze serwera | Skrypt z SQL Server Management Studio. |
| **Replikacja** | Publikacje lokalne | Skrypt z SQL Server Management Studio. |
|| Subskrybenci lokalni | Skrypt z SQL Server Management Studio. |
| **PolyBase** | PolyBase | Skrypt z SQL Server Management Studio. |
| **Zarządzanie** | Poczta bazy danych | Skrypt z SQL Server Management Studio. |
| **Program SQL Server Agent** | Stanowiska | Skrypt z SQL Server Management Studio. |
|| Alerty | Skrypt z SQL Server Management Studio. |
|| Operatory | Skrypt z SQL Server Management Studio. |
|| Proxy | Skrypt z SQL Server Management Studio. |
| **System operacyjny** | Pliki, udziały plików | Zanotuj wszelkie dodatkowe pliki lub udziały plików, które są używane przez serwery SQL, i Replikuj na serwerze docelowym maszyny wirtualnej platformy Azure. |


## <a name="post-migration"></a>Po migracji

Po pomyślnym ukończeniu etapu migracji przejdź przez serię zadań wykonywanych po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach może to spowodować konieczność wprowadzenia zmian w aplikacjach.

Zastosuj dowolną bazę danych Asystent migracji zalecanych poprawek do baz danych użytkowników. Zalecane jest, aby zapewnić spójność i umożliwienie automatyzacji.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje wykonywanie następujących czynności:

1. **Opracowuj testy weryfikacyjne.**  Testowanie migracji bazy danych za pomocą zapytań SQL. Utwórz zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
2. **Konfigurowanie środowiska testowego.**  Środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
3. **Uruchom testy weryfikacyjne.**  Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
4. **Uruchom testy wydajnościowe.**  Uruchom test wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

> [!TIP]
> Użyj [Asystent eksperymentowania z bazą danych (DEA)](/sql/dea/database-experimentation-assistant-overview) , aby pomóc w ocenie docelowej SQL Server wydajności.
>

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa do uzgadniania problemów z dokładnością i kompletnością danych, a także do rozwiązywania potencjalnych problemów z wydajnością w ramach obciążenia.

Aby uzyskać więcej informacji na temat tych problemów i konkretnych kroków, które należy rozwiązać, zobacz następujące zasoby:

- [Sprawdzanie poprawności po migracji i przewodnik optymalizacji.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Dostrajanie wydajności w usłudze Azure SQL Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Centrum optymalizacji kosztów platformy Azure](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić dostępność usług mających zastosowanie do SQL Server zobacz [centrum infrastruktury globalnej platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database) .

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Program SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać informacje o licencjonowaniu, zobacz
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
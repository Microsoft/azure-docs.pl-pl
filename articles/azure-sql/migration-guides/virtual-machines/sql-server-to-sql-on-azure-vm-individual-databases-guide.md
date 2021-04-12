---
title: 'SQL Server SQL Server na platformie Azure Virtual Machines: Przewodnik migracji'
description: W tym przewodniku dowiesz się, jak przeprowadzić migrację poszczególnych baz danych SQL Server do SQL Server na platformie Azure Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550901"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Przewodnik migracji: SQL Server SQL Server na platformie Azure Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

W tym przewodniku dowiesz się, jak *odkrywać*, *oceniać* i *migrować* bazy danych użytkowników z SQL Server do wystąpienia SQL Server na platformie Azure Virtual Machines przy użyciu funkcji tworzenia kopii zapasowych i przywracania oraz wysyłania dzienników, które używają [Data Migration Assistant](/sql/dma/dma-overview) do oceny.

Możesz migrować SQL Server uruchomione lokalnie lub na:

- SQL Server na maszynach wirtualnych.
- Amazon Web Services (AWS) EC2.
- Usługa Amazon relacyjnej bazy danych (AWS RDS).
- Aparat obliczeniowy (Google Cloud Platform [GCP]).

Aby uzyskać informacje na temat dodatkowych strategii migracji, zobacz [Omówienie migracji maszyn wirtualnych SQL Server](sql-server-to-sql-on-azure-vm-migration-overview.md). Aby poznać inne przewodniki dotyczące migracji, zobacz [przewodniki dotyczące migracji bazy danych Azure](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagram przedstawiający przepływ procesu migracji.":::

## <a name="prerequisites"></a>Wymagania wstępne

Migracja do SQL Server na platformie Azure Virtual Machines wymaga następujących zasobów:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- [Projekt Azure Migrate](../../../migrate/create-manage-projects.md).
- Przygotowana [SQL Server docelowego w wystąpieniu platformy Azure Virtual Machines](../../virtual-machines/windows/create-sql-vm-portal.md) jest taka sama lub większa niż wersja źródła SQL Server.
- [Łączność między platformą Azure i](/azure/architecture/reference-architectures/hybrid-networking)środowiskiem lokalnym.
- [Wybór odpowiedniej strategii migracji](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Przed migracją

Przed rozpoczęciem migracji należy wykryć topologię środowiska SQL i ocenić możliwość zamierzonej migracji.

### <a name="discover"></a>Odnajdywanie

Azure Migrate ocenia przydatność migracji komputerów lokalnych, wykonuje ustalanie rozmiarów na podstawie wydajności i zapewnia oszacowanie kosztów do działania w środowisku lokalnym. Aby zaplanować migrację, użyj Azure Migrate, aby [zidentyfikować istniejące źródła danych i informacje o funkcjach](../../../migrate/concepts-assessment-calculation.md) używanych przez wystąpienia SQL Server. Ten proces polega na skanowaniu sieci w celu zidentyfikowania wszystkich wystąpień SQL Server w organizacji z użyciem używanej wersji i funkcji.

> [!IMPORTANT]
> Po wybraniu docelowej maszyny wirtualnej platformy Azure dla wystąpienia SQL Server należy wziąć pod uwagę [wytyczne dotyczące wydajności SQL Server na platformie Azure Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Aby uzyskać więcej narzędzi do odnajdywania, zobacz [usługi i narzędzia](../../../dms/dms-tools-matrix.md#business-justification-phase) dostępne dla scenariuszy migracji danych.

### <a name="assess"></a>Ocena

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Po wykryciu wszystkich źródeł danych Użyj [Data Migration Assistant](/sql/dma/dma-overview) do oceny wystąpień lokalnych SQL Server migrowanych do wystąpienia SQL Server na platformie Azure Virtual Machines, aby zrozumieć luki między wystąpieniami źródłowym i docelowym.

> [!NOTE]
> Jeśli _nie_ uaktualniasz wersji SQL Server, Pomiń ten krok i przejdź do sekcji [Migrowanie](#migrate) .

#### <a name="assess-user-databases"></a>Ocenianie baz danych użytkowników

Data Migration Assistant pomaga przeprowadzić migrację do nowoczesnej platformy danych przez wykrywanie problemów ze zgodnością, które mogą mieć wpływ na funkcjonalność bazy danych w nowej wersji SQL Server. Data Migration Assistant zaleca usprawnienia wydajności i niezawodności dla środowiska docelowego, a także umożliwia przenoszenie schematu, danych i obiektów logowania z serwera źródłowego na serwer docelowy.

Aby dowiedzieć się więcej, zobacz [ocenę](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>W oparciu o typ oceny uprawnienia wymagane na SQL Server źródłowym mogą być różne:
   > - W przypadku klasyfikatora z *parzystością funkcji* poświadczenia podane w celu nawiązania połączenia z źródłową bazą danych SQL Server muszą być członkami roli serwera *sysadmin* .
   > - W przypadku klasyfikatora *problemów ze zgodnością* podane poświadczenia muszą mieć uprawnienia co najmniej `CONNECT SQL` , `VIEW SERVER STATE` i `VIEW ANY DEFINITION` .
   > - Data Migration Assistant będzie wyróżnić uprawnienia wymagane dla wybranego klasyfikatora przed uruchomieniem oceny.

#### <a name="assess-the-applications"></a>Ocenianie aplikacji

Zazwyczaj warstwa aplikacji uzyskuje dostęp do baz danych użytkowników w celu utrwalania i modyfikowania danych. Data Migration Assistant można ocenić warstwę dostępu do danych aplikacji na dwa sposoby:

- Za pomocą przechwyconych [zdarzeń rozszerzonych](/sql/relational-databases/extended-events/extended-events) lub [SQL Server Profiler śladów](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) baz danych użytkownika. Możesz również użyć [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-capture-trace) , aby utworzyć dziennik śledzenia, który może być również używany do testowania a/B.
- Za pomocą [zestawu narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), który umożliwia odnajdywanie i ocenianie zapytań SQL w kodzie i służy do migrowania kodu źródłowego aplikacji z jednej platformy bazy danych do innej. To narzędzie obsługuje popularne typy plików, takie jak C#, Java, XML i zwykłego tekstu. Aby uzyskać informacje na temat sposobu przeprowadzania oceny zestawu narzędzi do migracji dostępu do danych, zobacz wpis w blogu [Use Data Migration Assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Podczas oceny baz danych użytkowników należy używać Data Migration Assistant do [importowania](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) przechwyconych plików śledzenia lub plików zestawu narzędzi do migracji dostępu do danych.

#### <a name="assessments-at-scale"></a>Oceny na dużą skalę

Jeśli masz wiele serwerów, które wymagają oceny Data Migration Assistant, Możesz zautomatyzować ten proces za pomocą [interfejsu wiersza polecenia](/sql/dma/dma-commandline). Za pomocą interfejsu można z wyprzedzeniem przygotować polecenia oceny dla każdego wystąpienia SQL Server w zakresie migracji.

W przypadku raportowania podsumowania w ramach dużych Estates oceny Data Migration Assistant można teraz [skonsolidować do Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Refaktoryzacja baz danych za pomocą Data Migration Assistant

W oparciu o wyniki oceny Data Migration Assistant może istnieć szereg zaleceń, aby zapewnić, że bazy danych użytkowników działają i działają prawidłowo po migracji. Data Migration Assistant zawiera szczegółowe informacje o obiektach i zasobach, które mają wpływ na sposób rozwiązania poszczególnych problemów. Przed rozpoczęciem migracji produkcyjnej upewnij się, że zostały rozwiązane wszystkie zmiany związane z istotnymi zmianami i zachowaniem.

W przypadku przestarzałych funkcji można wybrać opcję uruchamiania baz danych użytkownika w ich oryginalnym trybie [zgodności](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) , jeśli chcesz uniknąć wprowadzania tych zmian i przyspieszenia migracji. Ta akcja uniemożliwi [uaktualnienie zgodności bazy danych](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) do momentu rozwiązania przestarzałych elementów.

Należy wykonać skrypt wszystkich Data Migration Assistant poprawek i zastosować je do docelowej bazy danych SQL Server podczas fazy [po migracji](#post-migration) .

> [!CAUTION]
> Nie wszystkie wersje SQL Server obsługują wszystkie tryby zgodności. Sprawdź, czy [docelowa wersja SQL Server](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) obsługuje wybraną zgodność bazy danych. Na przykład SQL Server 2019 nie obsługuje baz danych z poziomem zgodności poziomu 90 (czyli SQL Server 2005). Te bazy danych wymagają co najmniej uaktualnienia do poziomu zgodności 100.
>

## <a name="migrate"></a>Migrate

Po wykonaniu kroków poprzedzających migrację można przeprowadzić migrację baz danych i składników użytkownika. Przeprowadź migrację baz danych przy użyciu preferowanej [metody migracji](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

Poniższe sekcje zawierają instrukcje dotyczące przeprowadzania migracji za pomocą kopii zapasowych i przywracania lub minimalnej migracji przestojów przy użyciu funkcji tworzenia kopii zapasowych i przywracania oraz wysyłania dzienników.

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Aby przeprowadzić migrację standardową przy użyciu funkcji tworzenia kopii zapasowych i przywracania:

1. Skonfiguruj łączność do SQL Server na platformie Azure Virtual Machines zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Wstrzymywanie lub zatrzymywanie wszystkich aplikacji, które korzystają z baz danych przeznaczonych do migracji.
1. Upewnij się, że bazy danych użytkownika są nieaktywne przy użyciu [trybu jednego użytkownika](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej.
1. Skopiuj lokalne pliki kopii zapasowej na maszynę wirtualną przy użyciu pulpitu zdalnego, [platformy Azure Eksplorator danych](/azure/data-explorer/data-explorer-overview)lub [narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Zalecane jest więcej niż 2 TB kopii zapasowych).
1. Przywróć pełne kopie zapasowe bazy danych do SQL Server na platformie Azure Virtual Machines.

### <a name="log-shipping-minimize-downtime"></a>Wysyłanie dziennika (Minimalizuj czas przestoju)

Aby przeprowadzić minimalną migrację, korzystając z funkcji tworzenia kopii zapasowych i przywracania oraz wysyłania dzienników:

1. Skonfiguruj łączność z SQL Server na platformie Azure Virtual Machines zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Upewnij się, że lokalne bazy danych użytkownika do migracji znajdują się w modelu odzyskiwania pełnego lub z pełnymi zabezpieczeniami.
1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej i zmodyfikuj wszystkie istniejące zadania pełnej kopii zapasowej bazy danych, aby użyć słowa kluczowego [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) w celu zachowania łańcucha dzienników.
1. Skopiuj lokalne pliki kopii zapasowej na maszynę wirtualną przy użyciu pulpitu zdalnego, [platformy Azure Eksplorator danych](/azure/data-explorer/data-explorer-overview)lub [narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Zalecane jest więcej niż 1 TB kopii zapasowych).
1. Przywracanie pełnych kopii zapasowych bazy danych na SQL Server na platformie Azure Virtual Machines.
1. Skonfiguruj [wysyłanie dzienników](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) między lokalną bazą danych a SQL Server na platformie Azure Virtual Machines. Nie należy ponownie inicjować baz danych, ponieważ to zadanie zostało już wykonane w poprzednich krokach.
1. Wytnij do serwera docelowego.
   1. Wstrzymywanie lub zatrzymywanie aplikacji przy użyciu baz danych do migracji.
   1. Upewnij się, że bazy danych użytkownika są nieaktywne przy użyciu [trybu jednego użytkownika](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Gdy wszystko będzie gotowe, wykonaj wysyłanie dziennika w [trybie failover](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) z lokalnymi bazami danych w celu SQL Server na platformie Azure Virtual Machines.

### <a name="migrate-objects-outside-user-databases"></a>Migrowanie obiektów poza bazami danych użytkownika

Do bezproblemowej pracy z bazami danych użytkownika po migracji może być wymagana większa liczba obiektów SQL Server.

Poniższa tabela zawiera listę składników i zalecane metody migracji, które można wykonać przed migracją baz danych użytkownika lub po niej.

| **Funkcja** | **Składnik** | **Metody migracji** |
| --- | --- | --- |
| **Bazy danych** | Model | Skrypt z SQL Server Management Studio. |
|| TempDB | Zaplanuj przeniesienie bazy danych tempDB na [dysk tymczasowy maszyny wirtualnej platformy Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)w celu uzyskania najlepszej wydajności. Upewnij się, że wybrano rozmiar maszyny wirtualnej, który ma wystarczający lokalny dysk SSD, aby pomieścić bazę danych tempDB. |
|| Bazy danych użytkowników z FileStream | Użyj metod [tworzenia kopii zapasowych i przywracania](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) dla migracji. Data Migration Assistant nie obsługuje baz danych z FileStream. |
| **Bezpieczeństwo** | SQL Server i nazwy logowania systemu Windows | Użyj Data Migration Assistant do [migracji logowań użytkowników](/sql/dma/dma-migrateserverlogins). |
|| Role SQL Server | Skrypt z SQL Server Management Studio. |
|| Dostawcy usług kryptograficznych | Zalecamy [przekonwertowanie na Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Ta procedura powoduje użycie [dostawcy zasobów maszyny wirtualnej SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Obiekty serwera** | Tworzenie kopii zapasowych urządzeń | Zamień na kopię zapasową bazy danych za pomocą [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)lub Zapisz kopie zapasowe w [usłudze Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 zastosujesz pakietu CU2 +). Ta procedura powoduje użycie [dostawcy zasobów maszyny wirtualnej SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
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
| **System operacyjny** | Pliki, udziały plików | Zanotuj wszystkie inne pliki lub udziały plików, które są używane przez serwery SQL, i Replikuj na serwerze docelowym usługi Azure Virtual Machines. |

## <a name="post-migration"></a>Po migracji

Po pomyślnym zakończeniu etapu migracji należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. Wykonanie tego zadania może wymagać zmian w aplikacjach w niektórych przypadkach.

Zastosuj wszelkie poprawki zalecane przez Data Migration Assistant do baz danych użytkowników. Aby zapewnić spójność i umożliwić automatyzację, należy zadbać o te poprawki.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Utwórz zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe**: Uruchom testy wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

> [!TIP]
> Użyj [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview) , aby pomóc w ocenie docelowej SQL Server wydajności.

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych, sprawdzania kompletności i rozwiązywania potencjalnych problemów z wydajnością w ramach obciążenia.

Aby uzyskać więcej informacji o tych problemach i krokach, które należy rozwiązać, zobacz:

- [Sprawdzanie poprawności po migracji i przewodnik optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Dostrajanie wydajności na maszynach wirtualnych Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Centrum optymalizacji kosztów platformy Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić dostępność usług, które mają zastosowanie do SQL Server, zobacz [centrum infrastruktury globalnej platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- W przypadku macierzy usług i narzędzi firmy Microsoft i innych firm, które są dostępne, aby pomóc w różnych scenariuszach migracji bazy danych i danych oraz zadaniach specjalnych, zobacz temat [usługi i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).
- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Program SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania (TCO) na platformie Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   - [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Aby uzyskać informacje o licencjonowaniu, zobacz:
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Aby uzyskać informacje na temat przeprowadzania testów A/B dla warstwy dostępu do danych, zobacz [omówienie asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).

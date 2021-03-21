---
title: Migrowanie z SQL Server do wystąpienia zarządzanego usługi Azure SQL
description: Dowiedz się, jak przeprowadzić migrację bazy danych z SQL Server do wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 49d37a5537ada260eae453bbb5f81716d42657a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565826"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server migracji wystąpień do wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera informacje o metodach migrowania wystąpienia programu SQL Server 2005 lub nowszego do [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md). Aby uzyskać informacje na temat migracji do pojedynczej bazy danych lub puli elastycznej, zobacz [Omówienie migracji: SQL Server do SQL Database](../migration-guides/database/sql-server-to-sql-database-overview.md). Informacje o migracji z innych platform i wskazówki dotyczące narzędzi i opcji można znaleźć w temacie [Migrowanie do usługi Azure SQL](../migration-guides/index.yml).

> [!NOTE]
> Jeśli chcesz szybko rozpocząć i wypróbować wystąpienie zarządzane Azure SQL, możesz chcieć przejść do [przewodnika Szybki Start](quickstart-content-reference-guide.md) , a nie na tej stronie.

Na wysokim poziomie proces migracji bazy danych wygląda następująco:

![Proces migracji](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Oceń zgodność z wystąpieniem zarządzanym SQL](#assess-sql-managed-instance-compatibility) , aby upewnić się, że nie ma żadnych problemów z blokowaniem, które mogą uniemożliwiać migracje.
  
  Ten krok obejmuje również tworzenie [linii bazowej wydajności](#create-a-performance-baseline) w celu określenia użycia zasobów w wystąpieniu SQL Server źródłowym. Ten krok jest zbędny, jeśli chcesz wdrożyć wystąpienie zarządzane o odpowiednim rozmiarze i sprawdzić, czy wydajność po migracji nie jest konieczna.
- [Wybierz opcje łączności aplikacji](connect-application-instance.md).
- [Wdróż w optymalnie przyskalowanej instancji zarządzanej, w](#deploy-to-an-optimally-sized-managed-instance) której będą wybierane parametry techniczne (liczba rdzeni wirtualnych, ilość pamięci) i warstwa wydajności (Krytyczne dla działania firmy, ogólnego przeznaczenia) wystąpienia zarządzanego.
- [Wybierz metodę migracji i Przeprowadź migrację](#select-a-migration-method-and-migrate) do migracji baz danych za pomocą migracji w trybie offline (natywne kopie zapasowe/przywracanie, import/eksport bazy danych lub migracja online (Azure Data Migration Service, replikacja transakcyjna).
- [Monitoruj aplikacje](#monitor-applications) , aby upewnić się, że masz oczekiwaną wydajność.

> [!NOTE]
> Aby przeprowadzić migrację pojedynczej bazy danych do pojedynczej bazy danych lub puli elastycznej, zobacz [Migrowanie bazy danych SQL Server do Azure SQL Database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Oceń zgodność wystąpienia zarządzanego SQL

Najpierw Ustal, czy wystąpienie zarządzane SQL jest zgodne z wymaganiami dotyczącymi bazy danych aplikacji. Wystąpienie zarządzane SQL zostało zaprojektowane w celu zapewnienia łatwego podnoszenia i migracji w celu uzyskania większości istniejących aplikacji, które używają SQL Server. Czasami jednak może być wymagana funkcja lub możliwości, które nie są jeszcze obsługiwane, a koszt wdrożenia obejścia jest zbyt duży.

Użyj [Data Migration Assistant](/sql/dma/dma-overview) , aby wykryć potencjalne problemy ze zgodnością mające wpływ na funkcjonalność bazy danych na Azure SQL Database. Jeśli występują pewne problemy z blokowaniem, może być konieczne rozważenie alternatywnej opcji, takiej jak [SQL Server na maszynie wirtualnej platformy Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Oto kilka przykładów:

- Jeśli wymagany jest bezpośredni dostęp do systemu operacyjnego lub systemu plików, na przykład w celu zainstalowania agentów innych firm lub niestandardowych na tej samej maszynie wirtualnej z SQL Server.
- Jeśli masz ścisłą zależność od funkcji, które nadal nie są obsługiwane, takich jak transakcje typu FileStream/FileTable, wielopodstawowy i międzywystąpień.
- W przypadku absolutnej konieczności pozostawania w określonej wersji SQL Server (na przykład 2012).
- Jeśli wymagania dotyczące obliczeń są znacznie mniejsze niż oferty wystąpienia zarządzanego (jedna rdzeń wirtualny, na przykład), a konsolidacja bazy danych nie jest akceptowalną opcją.

Jeśli zostały rozpoznane wszystkie zidentyfikowane blokady migracji i kontynuują migrację do wystąpienia zarządzanego SQL, należy pamiętać, że niektóre zmiany mogą wpłynąć na wydajność obciążeń:

- Obowiązkowy model odzyskiwania pełnego i regularne automatyczne harmonogramy tworzenia kopii zapasowych mogą mieć wpływ na wydajność zadań związanych z obciążeniem lub konserwacją/ETL, jeśli okresowo używany jest prosty/zarejestrowany w całości model lub zatrzymane kopie zapasowe na żądanie.
- Różne konfiguracje na poziomie serwera lub bazy danych, takie jak flagi śledzenia lub poziomy zgodności.
- Nowe funkcje, takie jak szyfrowanie przezroczystej bazy danych (TDE) lub grupy autopracy awaryjnej, mogą mieć wpływ na użycie procesora CPU i operacji we/wy.

Wystąpienie zarządzane SQL gwarantuje dostępność na 99,99% nawet w scenariuszach krytycznych, dlatego nie można wyłączyć narzutów spowodowanych przez te funkcje. Aby uzyskać więcej informacji, zobacz [główne przyczyny, które mogą spowodować inną wydajność na SQL Server i wystąpienie zarządzane usługi Azure SQL](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabele zoptymalizowane pod kątem pamięci)

SQL Server zapewnia In-Memoryą funkcję OLTP, która umożliwia użycie tabel zoptymalizowanych pod kątem pamięci, typów tabel zoptymalizowanych pod kątem ilości danych i natywnie skompilowanych modułów SQL do uruchamiania obciążeń o wysokiej przepływności i wymaganiach dotyczących przetwarzania transakcyjnego o małym opóźnieniu. 

> [!IMPORTANT]
> In-Memory OLTP jest obsługiwana tylko w warstwie Krytyczne dla działania firmy w wystąpieniu zarządzanym usługi Azure SQL (i nie jest obsługiwana w warstwie Ogólnego przeznaczenia).

Jeśli masz tabele zoptymalizowane pod kątem pamięci lub typy tabel zoptymalizowane pod kątem pamięci w SQL Server lokalnym i chcesz przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL, należy wykonać następujące instrukcje:

- Wybierz Krytyczne dla działania firmy warstwę dla docelowego wystąpienia zarządzanego Azure SQL, które obsługuje In-Memory OLTP lub
- Jeśli chcesz przeprowadzić migrację do warstwy Ogólnego przeznaczenia w wystąpieniu zarządzanym usługi Azure SQL, Usuń tabele zoptymalizowane pod kątem pamięci, typy tabel zoptymalizowane pod kątem pamięci i natywnie skompilowane moduły SQL, które współpracują z obiektami zoptymalizowanymi pod kątem pamięci przed migracją baz danych. Poniższe zapytanie T-SQL może służyć do identyfikowania wszystkich obiektów, które należy usunąć przed migracją do warstwy Ogólnego przeznaczenia:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Aby dowiedzieć się więcej o technologiach w pamięci, zobacz [Optymalizowanie wydajności przy użyciu technologii w pamięci w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)

### <a name="create-a-performance-baseline"></a>Utwórz linię bazową wydajności

Jeśli potrzebujesz porównać wydajność obciążeń z wystąpieniem zarządzanym z pierwotnym obciążeniem uruchomionym na SQL Server, musisz utworzyć linię bazową wydajności, która będzie używana do porównywania.

Linia bazowa wydajności to zestaw parametrów, takich jak średnie/maksymalne użycie procesora CPU, średnie/maksymalne opóźnienie operacji we/wy dysku, przepływność, liczba IOPS, Średnia/maks. stron życia stron i średni maksymalny rozmiar bazy danych tempdb. Po migracji chcesz mieć podobne lub jeszcze lepsze parametry, dlatego ważne jest, aby mierzyć i rejestrować wartości bazowe dla tych parametrów. Oprócz parametrów systemowych należy wybrać zestaw reprezentatywnych zapytań lub najważniejszych zapytań w obciążeniu, a także mierzyć minimalny/średni/maksymalny czas trwania i użycie procesora dla wybranych zapytań. Te wartości umożliwiają porównanie wydajności obciążeń uruchomionych w wystąpieniu zarządzanym z oryginalnymi wartościami w wystąpieniu SQL Server źródłowym.

Niektóre parametry, które należy zmierzyć w wystąpieniu SQL Server, to:

- [Monitoruj użycie procesora CPU w wystąpieniu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) i zarejestruj średnie i szczytowe użycie procesora CPU.
- [Monitoruj użycie pamięci w wystąpieniu SQL Server](/sql/relational-databases/performance-monitor/monitor-memory-usage) i określ ilość pamięci używanej przez różne składniki, takie jak pula buforów, pamięć podręczna planu, pula magazynu kolumn, przetwarzanie [OLTP w pamięci](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)itp. Ponadto należy znaleźć wartości średnie i szczytowe licznika wydajności stron pamięci na stronie.
- Monitoruj użycie operacji we/wy dysku na źródłowym wystąpieniu SQL Server przy użyciu widoku [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) lub [liczników wydajności](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitoruj obciążenia i wydajność zapytań lub wystąpienie SQL Server, badając dynamiczne widoki zarządzania lub magazyn zapytań w przypadku migrowania z wersji SQL Server 2016 +. Zidentyfikuj średni czas trwania i użycie procesora przez najważniejsze zapytania w obciążeniu, aby porównać je z kwerendami uruchomionymi w wystąpieniu zarządzanym.

> [!Note]
> Jeśli zauważysz dowolny problem związany z obciążeniem, SQL Server taki jak duże użycie procesora CPU, stałe wykorzystanie pamięci lub problemy z bazą danych tempdb lub parametryzacja, spróbuj je rozwiązać na źródłowym wystąpieniu SQL Server, zanim przeniesiesz linię bazową i migrację. Migrowanie znanych problemów do dowolnego nowego systemu może spowodować nieoczekiwane wyniki i unieważnić wszystkie porównania wydajności.

W wyniku tego działania należy mieć udokumentowane średnie i szczytowe wartości użycia procesora CPU, pamięci i operacji we/wy w systemie źródłowym, a także średni i maksymalny czas trwania i użycie procesora CPU oraz najważniejsze zapytania w obciążeniu. Te wartości powinny być używane później w celu porównania wydajności obciążeń w wystąpieniu zarządzanym z wydajnością bazową obciążenia w wystąpieniu SQL Server źródłowym.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Wdróż w optymalnie przyskalowanej instancji zarządzanej

Wystąpienie zarządzane SQL jest dostosowane do obciążeń lokalnych, które planują przejście do chmury. Wprowadzono [Nowy model zakupu](../database/service-tiers-vcore.md) , który zapewnia większą elastyczność w wyborze odpowiedniego poziomu zasobów dla obciążeń. W lokalnym świecie prawdopodobnie masz do rozbudowy te obciążenia przy użyciu rdzeni fizycznych i przepustowości we/wy. Model zakupu wystąpienia zarządzanego jest oparty na rdzeniach wirtualnych lub "rdzeni wirtualnych" z dodatkowym magazynem i funkcją we/wy. Model rdzeń wirtualny to prostszy sposób, aby zrozumieć wymagania dotyczące obliczeń w chmurze, a także to, co jest używane lokalnie. Ten nowy model umożliwia prawidłowe dopasowanie środowiska docelowego do chmury. Niektóre ogólne wskazówki, które mogą pomóc w wyborze odpowiedniej warstwy usług i charakterystyce, zostały opisane tutaj:

- W oparciu o użycie procesora bazowego, można zainicjować zarządzane wystąpienie, które jest zgodne z liczbą rdzeni, które są używane w SQL Server, biorąc pod uwagę, że konieczne może być skalowanie charakterystyki procesora w celu dopasowania ich do [charakterystyki maszyny wirtualnej, w której zainstalowano wystąpienie zarządzane](resource-limits.md#hardware-generation-characteristics).
- Na podstawie użycia pamięci bazowej wybierz [warstwę usług, która ma zgodną pamięć](resource-limits.md#hardware-generation-characteristics). Ilości pamięci nie można wybrać bezpośrednio, dlatego należy wybrać wystąpienie zarządzane z ilością rdzeni wirtualnych, która ma pasującą pamięć (na przykład 5,1 GB/rdzeń wirtualny w 5 rdzeń).
- W oparciu o opóźnienie wejścia/wyjścia punktu odniesienia w podsystemie plików wybierz między Ogólnego przeznaczenia (opóźnienie większe niż 5 ms) i Krytyczne dla działania firmy (opóźnienie mniejsze niż 3 ms).
- Na podstawie przepływności linii bazowej należy wstępnie przydzielić rozmiar danych lub plików dziennika, aby uzyskać oczekiwaną wydajność operacji we/wy.

Podczas wdrażania można wybrać zasoby obliczeniowe i magazynowe, a następnie zmienić je później bez wprowadzania przestojów aplikacji przy użyciu [Azure Portal](../database/scale-resources.md):

![Ustalanie rozmiarów wystąpienia zarządzanego](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Aby dowiedzieć się, jak utworzyć infrastrukturę sieci wirtualnej i wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).

> [!IMPORTANT]
> Ważne jest utrzymywanie docelowej sieci wirtualnej i podsieci zgodnie z wymaganiami sieci [wirtualnej wystąpienia zarządzanego](connectivity-architecture-overview.md#network-requirements). Niezgodność może uniemożliwić tworzenie nowych wystąpień lub korzystanie z tych, które zostały już utworzone. Dowiedz się więcej na temat [tworzenia nowych](virtual-network-subnet-create-arm-template.md) i [konfigurowania istniejących](vnet-existing-add-subnet.md) sieci.

## <a name="select-a-migration-method-and-migrate"></a>Wybierz metodę migracji i Przeprowadź migrację

Wystąpienia zarządzane SQL są przeznaczone dla scenariuszy użytkownika wymagających migracji bazy danych ze środowisk lokalnych lub implementacji bazy danych maszyn wirtualnych platformy Azure. Jest to optymalny wybór w przypadku konieczności podnoszenia i przesunięcia zaplecza aplikacji, które regularnie korzystają z poziomu wystąpienia i/lub funkcjonalności między bazami danych. Jeśli jest to Twój scenariusz, możesz przenieść całe wystąpienie do odpowiedniego środowiska na platformie Azure bez konieczności ponownego tworzenia architektury aplikacji.

Aby przenieść wystąpienia bazy danych SQL, należy uważnie zaplanować:

- Migracja wszystkich baz danych, które muszą być rozmieszczone w tym samym wystąpieniu.
- Migracja obiektów na poziomie wystąpienia, od których zależy aplikacja, w tym logowania, poświadczeń, zadań i operatorów programu SQL Agent oraz wyzwalaczy na poziomie serwera.

Wystąpienie zarządzane SQL to usługa zarządzana, która umożliwia delegowanie niektórych zwykłych działań administratorów do platformy w miarę ich wbudowania. W związku z tym niektóre dane na poziomie wystąpienia nie muszą być migrowane, na przykład zadania konserwacji regularnego tworzenia kopii zapasowych lub zawsze w konfiguracji, ponieważ [wysoka dostępność](../database/high-availability-sla.md) jest wbudowana.

Wystąpienie zarządzane SQL obsługuje następujące opcje migracji bazy danych (obecnie są to jedyne obsługiwane metody migracji):

- Azure Database Migration Service — migracja z niezerowym przestojem.
- Natywne `RESTORE DATABASE FROM URL` — używa natywnych kopii zapasowych z SQL Server i wymaga pewnego przestoju.

### <a name="azure-database-migration-service"></a>Usługa Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md) to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących baz danych innych firm i SQL Server na platformę Azure. Opcje wdrażania w publicznej wersji zapoznawczej obejmują bazy danych Azure SQL Database i SQL Server baz danych na maszynie wirtualnej platformy Azure. Database Migration Service jest zalecaną metodą migracji obciążeń przedsiębiorstwa.

Jeśli używasz SQL Server Integration Services (SSIS) na SQL Server lokalnym, Database Migration Service nie obsługuje jeszcze migracji wykazu usług SSIS (SSISDB), który przechowuje pakiety SSIS, ale możesz udostępnić Azure-SSIS Integration Runtime (IR) w Azure Data Factory, co spowoduje utworzenie nowego SSISDB w wystąpieniu zarządzanym, aby umożliwić ponowne wdrożenie pakietów. Zobacz [tworzenie Azure-SSIS IR w Azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Aby dowiedzieć się więcej na temat tego scenariusza i kroków konfiguracji dla Database Migration Service, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natywne polecenie RESTORE z adresu URL

Przywracanie natywnych kopii zapasowych (pliki. bak) pobierane z wystąpienia SQL Server, dostępne w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/), jest jednym z najważniejszych funkcji wystąpienia zarządzanego SQL, które umożliwiają szybką i łatwą migrację bazy danych w trybie offline.

Poniższy diagram przedstawia ogólny przegląd procesu:

![Diagram przedstawia SQL Server ze strzałką z etykietą kopia ZAPASowa/przekazywanie do usługi Azure Storage, a druga strzałka z etykietą Przywróć z adresu URL przepływania z usługi Azure Storage do zarządzanego wystąpienia SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

Poniższa tabela zawiera więcej informacji na temat metod, których można użyć w zależności od uruchomionej wersji SQL Server źródłowej:

|Krok|Aparat i wersja SQL|Metoda tworzenia kopii zapasowej/przywracania|
|---|---|---|
|Umieszczanie kopii zapasowej w usłudze Azure Storage|Przed 2012 SP1 ZASTOSUJESZ pakietu CU2|Przekaż plik. bak bezpośrednio do usługi Azure Storage|
||2012 SP1 ZASTOSUJESZ PAKIETU CU2-2016|Bezpośrednia kopia zapasowa przy użyciu przestarzałej składni [poświadczeń](/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 i więcej|Bezpośrednia kopia zapasowa przy użyciu [poświadczeń SAS](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Przywracanie z usługi Azure Storage do wystąpienia zarządzanego|[Przywróć z adresu URL przy użyciu poświadczeń sygnatury dostępu współdzielonego](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - W przypadku migrowania bazy danych chronionej przez [transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) do wystąpienia zarządzanego przy użyciu opcji przywracania natywnego należy zmigrować odpowiedni certyfikat z lokalnego lub SQL Server maszyny wirtualnej platformy Azure przed przywróceniem bazy danych. Aby uzyskać szczegółowe instrukcje, zobacz [Migrowanie certyfikatu TDE do wystąpienia zarządzanego](tde-certificate-migrate.md).
> - Przywracanie systemowych baz danych nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywanych w bazach danych Master lub msdb), zalecamy wykonanie skryptów i wykonanie skryptów T-SQL w wystąpieniu docelowym.

Przewodnik Szybki Start przedstawiający sposób przywracania kopii zapasowej bazy danych do wystąpienia zarządzanego przy użyciu poświadczeń sygnatury dostępu współdzielonego znajduje się w sekcji [przywracanie z kopii zapasowej do wystąpienia zarządzanego](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorowanie aplikacji

Po zakończeniu migracji do wystąpienia zarządzanego należy śledzić zachowanie aplikacji i wydajność obciążeń. Ten proces obejmuje następujące działania:

- [Porównanie wydajności obciążenia uruchomionego w wystąpieniu zarządzanym](#compare-performance-with-the-baseline) z [linią bazową wydajności utworzoną w wystąpieniu źródła SQL Server](#create-a-performance-baseline).
- Ciągłe [monitorowanie wydajności obciążeń](#monitor-performance) w celu identyfikowania potencjalnych problemów i ulepszeń.

### <a name="compare-performance-with-the-baseline"></a>Porównanie wydajności z linią bazową

Pierwsze działanie, które należy wykonać natychmiast po pomyślnej migracji, ma na celu porównanie wydajności obciążeń z podstawową wydajnością obciążeń. Celem tego działania jest upewnienie się, że wydajność obciążeń w wystąpieniu zarządzanym spełnia Twoje potrzeby.

Migracja bazy danych do wystąpienia zarządzanego utrzymuje ustawienia bazy danych i jego oryginalny poziom zgodności w większości przypadków. Oryginalne ustawienia są zachowywane, jeśli jest to możliwe, aby zmniejszyć ryzyko obniżenia wydajności w porównaniu do wystąpienia źródłowego SQL Server. Jeśli poziom zgodności bazy danych użytkownika był 100 lub wyższy przed migracją, pozostaje taki sam po migracji. Jeśli poziom zgodności bazy danych użytkownika był 90 przed migracją, w uaktualnionej bazie danych poziom zgodności ma wartość 100, czyli najniższy obsługiwany poziom zgodności w wystąpieniu zarządzanym. Poziom zgodności systemowych baz danych to 140. Ponieważ migracja do wystąpienia zarządzanego jest w rzeczywistości migrowana do najnowszej wersji aparatu bazy danych SQL Server, należy pamiętać, że należy ponownie przetestować wydajność obciążenia, aby uniknąć niektórych problemów z wydajnością zaskakujące.

Jako warunek wstępny upewnij się, że zostały wykonane następujące działania:

- Wyrównaj ustawienia do wystąpienia zarządzanego przy użyciu ustawień z wystąpienia źródłowego SQL Server, badając różne wystąpienia, bazę danych, ustawienia tempdb i konfiguracje. Przed uruchomieniem pierwszego porównania wydajności upewnij się, że nie zmieniono ustawień, takich jak poziomy zgodności lub szyfrowanie, lub zaakceptuj ryzyko, że niektóre nowe funkcje, które zostały włączone, mogą mieć wpływ na niektóre zapytania. Aby zmniejszyć ryzyko związane z migracją, należy zmienić poziom zgodności bazy danych tylko po monitorowaniu wydajności.
- Zaimplementuj [wskazówki dotyczące najlepszych rozwiązań w zakresie magazynu dla ogólnego przeznaczenia](https://techcommunity.microsoft.com), na przykład wstępnie przydzielić rozmiar plików, aby uzyskać lepszą wydajność.
- Zapoznaj się z [najważniejszymi różnicami w środowisku, które mogą spowodować różnice w wydajności między wystąpieniem zarządzanym i SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), i zidentyfikuj czynniki ryzyka, które mogą mieć wpływ na wydajność.
- Upewnij się, że włączono obsługę magazynu zapytań i dostrajania automatycznego na zarządzanym wystąpieniu. Te funkcje umożliwiają mierzenie wydajności obciążeń i automatyczne rozwiązanie potencjalnych problemów z wydajnością. Dowiedz się, jak używać magazynu zapytań jako optymalnego narzędzia do uzyskiwania informacji o wydajności obciążeń przed i po zmianie poziomu zgodności bazy danych, zgodnie z opisem w temacie [zachowanie stabilności wydajności podczas uaktualniania do nowszej wersji SQL Server](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po przygotowaniu środowiska, które jest porównywalne, jak to możliwe, z środowiskiem lokalnym, możesz rozpocząć pracę z obciążeniem i zmierzyć wydajność. Proces pomiaru powinien zawierać te same parametry, które są mierzone [podczas tworzenia linii bazowej wydajności miar obciążenia w wystąpieniu SQL Server źródłowym](#create-a-performance-baseline).
W związku z tym należy porównać parametry wydajności z linią bazową i identyfikować różnice krytyczne.

> [!NOTE]
> W wielu przypadkach nie jest możliwe uzyskanie dokładnej wydajności na zarządzanym wystąpieniu i SQL Server. Wystąpienie zarządzane Azure SQL jest aparatem bazy danych SQL Server, ale konfiguracja infrastruktury i wysokiej dostępności w wystąpieniu zarządzanym może spowodować pewne różnice. Może się zdarzyć, że niektóre zapytania będą szybsze, a niektóre inne mogą być wolniejsze. Celem porównania jest upewnienie się, że wydajność obciążenia w wystąpieniu zarządzanym jest zgodna z wydajnością na SQL Server (średnio) i identyfikuje wszystkie krytyczne zapytania z wydajnością, która nie jest zgodna z oryginalną wydajnością.

Wynikiem porównania wydajności mogą być:

- Wydajność obciążeń w wystąpieniu zarządzanym jest wyrównana lub lepsza od wydajności obciążeń SQL Server. W takim przypadku pomyślnie potwierdzono, że migracja się powiodła.
- Większość parametrów wydajności i zapytań w obciążeniu działa prawidłowo, z pewnymi wyjątkami o obniżonej wydajności. W takim przypadku należy zidentyfikować różnice i ich znaczenie. Jeśli istnieją ważne zapytania o obniżonej wydajności, należy sprawdzić, czy źródłowe plany SQL uległy zmianie, lub czy zapytania mają wpływ na niektóre ograniczenia zasobów. W tym przypadku środki zaradcze mogą być stosowane w przypadku zapytań krytycznych (na przykład zmiany poziomu zgodności, starszej kardynalności szacowania) albo bezpośrednio lub za pomocą przewodników planu, odbudowania lub utworzenia statystyk i indeksów, które mogą mieć wpływ na plany.
- Większość zapytań jest wolniejsza w wystąpieniu zarządzanym w porównaniu do wystąpienia źródłowego SQL Server. W takim przypadku spróbuj zidentyfikować główne przyczyny różnicy, takie jak [osiągnięcie limitu zasobów](resource-limits.md#service-tier-characteristics) , takich jak limity operacji wejścia/wyjścia, limit pamięci, Limit szybkości dziennika wystąpienia itd. Jeśli nie ma limitów zasobów, które mogą spowodować różnicę, spróbuj zmienić poziom zgodności bazy danych lub zmienić ustawienia bazy danych, takie jak starsze oszacowanie kardynalności i ponownie uruchom test. Zapoznaj się z zaleceniami podanymi w widokach wystąpienia zarządzanego lub magazynu zapytań, aby zidentyfikować zapytania, które uległa pogorszeniuą wydajność.

> [!IMPORTANT]
> Wystąpienie zarządzane Azure SQL ma wbudowaną funkcję automatycznego korygowania planu, która jest domyślnie włączona. Ta funkcja zapewnia, że zapytania, które działały prawidłowo w trakcie wklejania, nie obniżą się w przyszłości. Upewnij się, że ta funkcja jest włączona i że obciążenie zostało wykonane wystarczająco długo przy użyciu starych ustawień, zanim zmienisz nowe ustawienia, aby umożliwić wystąpieniu zarządzanemu informacje o wydajności i planach bazowych.

Zmień parametry lub warstwy usługi uaktualniania na zbieżność z optymalną konfiguracją do momentu uzyskania wydajności, która odpowiada Twoim potrzebom.

### <a name="monitor-performance"></a>Monitorowanie wydajności

Wystąpienie zarządzane SQL udostępnia wiele zaawansowanych narzędzi do monitorowania i rozwiązywania problemów, a następnie należy używać ich do monitorowania wydajności w wystąpieniu. Oto niektóre z parametrów, które należy monitorować:

- Użycie procesora CPU w wystąpieniu, aby określić, czy liczba zainicjowanych rdzeni wirtualnych jest właściwym dopasowaniem dla obciążenia.
- Stron życia strony na wystąpieniu zarządzanym, aby określić [, czy potrzebna jest dodatkowa pamięć](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Dane statystyczne, takie jak `INSTANCE_LOG_GOVERNOR` lub `PAGEIOLATCH` , które będą informować o problemach we/wy magazynu, szczególnie w warstwie ogólnego przeznaczenia, w których może być konieczne wstępne przydzielenie plików w celu uzyskania lepszej wydajności operacji we/wy.

## <a name="leverage-advanced-paas-features"></a>Korzystanie z zaawansowanych funkcji PaaS

Gdy korzystasz z w pełni zarządzanej platformy i sprawdzono, że osiągi obciążeń są zgodne z wydajnością obciążeń SQL Server, Skorzystaj z zalet, które są udostępniane automatycznie w ramach usługi.

Nawet jeśli nie wprowadzisz zmian w wystąpieniu zarządzanym podczas migracji, istnieje duże prawdopodobieństwo, że niektóre nowe funkcje zostaną włączone podczas pracy z wystąpieniem, aby korzystać z najnowszych ulepszeń aparatu bazy danych. Niektóre zmiany są włączone tylko po [zmianie poziomu zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Na przykład nie trzeba tworzyć kopii zapasowych w wystąpieniu zarządzanym — usługa automatycznie wykonuje kopie zapasowe. Nie musisz już martwić się o planowanie i tworzenie kopii zapasowych oraz zarządzanie nimi. Wystąpienie zarządzane SQL umożliwia przywracanie do dowolnego punktu w czasie w tym okresie przechowywania przy użyciu funkcji odzyskiwania do momentu [(kopie)](../database/recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba martwić się o skonfigurowanie wysokiej dostępności, ponieważ [wysoka dostępność](../database/high-availability-sla.md) jest wbudowana.

Aby zwiększyć bezpieczeństwo, należy rozważyć użycie [uwierzytelniania Azure Active Directory](../database/security-overview.md), [inspekcji](auditing-configure.md), [wykrywania zagrożeń](../database/azure-defender-for-sql.md), [zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security)i [dynamicznego maskowania danych](/sql/relational-databases/security/dynamic-data-masking).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń, wystąpienie zarządzane udostępnia zestaw zaawansowanych narzędzi, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](../database/monitor-tune-overview.md). [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych i scentralizowanie monitorowania dużej liczby wystąpień i baz danych. [Dostrajanie automatyczne](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) w wystąpieniach zarządzanych stale monitoruje wydajność statystyk wykonywania planu SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat wystąpienia zarządzanego usługi Azure SQL, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Aby zapoznać się z samouczkiem zawierającym przywracanie z kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym migracji przy użyciu Database Migration Service, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego usługi Azure SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
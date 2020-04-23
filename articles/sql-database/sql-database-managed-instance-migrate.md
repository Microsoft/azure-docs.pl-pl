---
title: Migrowanie z SQL Server do wystąpienia zarządzanego
description: Dowiedz się, jak przeprowadzić migrację bazy danych z wystąpienia SQL Server do wystąpienia zarządzanego przez Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 1af0161edb0f833cdd14d8157e6edd9644e21467
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100281"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server migracji wystąpień do Azure SQL Database wystąpienia zarządzanego

Ten artykuł zawiera informacje na temat metod migrowania wystąpienia SQL Server 2005 lub nowszej wersji do [Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance.md). Aby uzyskać informacje na temat migracji do pojedynczej bazy danych lub puli elastycznej, zobacz [Migrowanie do jednej lub puli baz danych](sql-database-cloud-migrate.md). Informacje o migracji z innych platform znajdują się w temacie [Przewodnik po migracji bazy danych Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Jeśli chcesz szybko uruchomić i spróbować użyć wystąpienia zarządzanego, możesz chcieć przejść do [przewodnika Szybki Start](sql-database-managed-instance-quickstart-guide.md) , a nie na tej stronie. 

Na wysokim poziomie proces migracji bazy danych wygląda następująco:

![proces migracji](./media/sql-database-managed-instance-migration/migration-process.png)

- [Oceń zgodność z wystąpieniem zarządzanym](#assess-managed-instance-compatibility) , aby upewnić się, że nie ma żadnych problemów z blokowaniem, które mogą uniemożliwiać migracje.
  - Ten krok obejmuje również tworzenie [linii bazowej wydajności](#create-performance-baseline) w celu określenia użycia zasobów w wystąpieniu SQL Server źródłowym. Ten krok jest zbędny, jeśli chcesz wdrożyć wystąpienie zarządzane o prawidłowym rozmiarze i sprawdzić, czy nie ma to zastosowania.
- [Wybierz opcje łączności aplikacji](sql-database-managed-instance-connect-app.md)
- [Wdróż w optymalnie przyskalowanej instancji zarządzanej, w](#deploy-to-an-optimally-sized-managed-instance) której będą wybierane parametry techniczne (liczba rdzeni wirtualnych, ilość pamięci) i warstwa wydajności (Krytyczne dla działania firmy, ogólnego przeznaczenia) wystąpienia zarządzanego.
- [Wybierz metodę migracji i Przeprowadź migrację](#select-migration-method-and-migrate) do migracji baz danych za pomocą migracji w trybie offline (natywne kopie zapasowe/przywracanie, import/eksport bazy danych) lub migracja online (usługa migracji danych, replikacja transakcyjna).
- [Monitoruj aplikacje](#monitor-applications) , aby upewnić się, że masz oczekiwaną wydajność.

> [!NOTE]
> Aby przeprowadzić migrację pojedynczej bazy danych do pojedynczej bazy danych lub puli elastycznej, zobacz [Migrowanie bazy danych SQL Server do Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Oceń zgodność wystąpienia zarządzanego

Najpierw Ustal, czy wystąpienie zarządzane jest zgodne z wymaganiami bazy danych aplikacji. Opcja wdrożenia wystąpienia zarządzanego została zaprojektowana w celu zapewnienia łatwego przenoszenia i przesunięcia w przypadku większości istniejących aplikacji, które używają SQL Server lokalnie lub na maszynach wirtualnych. Czasami jednak może być wymagana funkcja lub możliwości, które nie są jeszcze obsługiwane, a koszt wdrożenia obejścia jest zbyt duży.

Użyj [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) , aby wykryć potencjalne problemy ze zgodnością, które mają wpływ na funkcjonalność bazy danych na Azure SQL Database. Usługa DMA nie obsługuje jeszcze wystąpienia zarządzanego jako miejsca docelowego migracji, ale zaleca się przeprowadzenie oceny pod kątem Azure SQL Database i starannie przejrzeć listę raportowanych problemów z obsługą funkcji i zgodności z dokumentacją produktu. Zobacz [Azure SQL Database funkcje](sql-database-features.md) do sprawdzania, czy zgłoszono pewne problemy z blokowaniem, które nie są blokowane w wystąpieniu zarządzanym, ponieważ większość problemów z blokowaniem, które uniemożliwiają migrację do Azure SQL Database, zostały usunięte z wystąpienia zarządzanego. Na przykład funkcje, takie jak zapytania między bazami danych, transakcje między bazami danych w tym samym wystąpieniu, połączonego serwera z innymi źródłami SQL, CLR, globalne tabele tymczasowe, widoki na poziomie wystąpienia, Service Broker i podobne są dostępne w wystąpieniach zarządzanych.

W przypadku problemów z blokowaniem, które nie zostały usunięte z opcją wdrożenia wystąpienia zarządzanego, może być konieczne rozważenie alternatywnej opcji, takiej jak [SQL Server na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Oto kilka przykładów:

- Jeśli wymagany jest bezpośredni dostęp do systemu operacyjnego lub systemu plików, na przykład w celu zainstalowania niestandardowych agentów na tej samej maszynie wirtualnej z SQL Server.
- Jeśli masz ścisłą zależność od funkcji, które nadal nie są obsługiwane, takich jak transakcje typu FileStream/FileTable, wielopodstawowy i międzywystąpień.
- Jeśli absolutnie potrzeba pozostawać w określonej wersji SQL Server (na przykład 2012).
- Jeśli wymagania dotyczące obliczeń są znacznie niższe, w przypadku których oferty wystąpienia zarządzanego (jedna rdzeń wirtualny, na przykład) i konsolidacja bazy danych nie są akceptowalne.

Jeśli zostały rozpoznane wszystkie zidentyfikowane blokady migracji i kontynuowanie migracji do wystąpienia zarządzanego, należy pamiętać, że niektóre zmiany mogą wpływać na wydajność obciążeń:
- Obowiązkowy model odzyskiwania pełnego i regularne automatyczne harmonogramy tworzenia kopii zapasowych mogą mieć wpływ na wydajność zadań związanych z obciążeniem lub konserwacją/ETL, jeśli okresowo używany jest prosty/zarejestrowany w całości model lub zatrzymane kopie zapasowe na żądanie.
- Różne konfiguracje na poziomie serwera lub bazy danych, takie jak flagi śledzenia lub poziomy zgodności
- Nowe funkcje, takie jak szyfrowanie przezroczystej bazy danych (TDE) lub grupy autopracy awaryjnej, mogą mieć wpływ na użycie procesora CPU i operacji we/wy.

Wystąpienie zarządzane gwarantuje dostępność na 99,99% nawet w scenariuszach krytycznych, dlatego nie można wyłączyć narzutów spowodowanych przez te funkcje. Aby uzyskać więcej informacji, zobacz [główne przyczyny, które mogą powodować różne wydajności na SQL Server i wystąpienia zarządzanego](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Utwórz linię bazową wydajności

Jeśli potrzebujesz porównać wydajność obciążeń z wystąpieniem zarządzanym z pierwotnym obciążeniem uruchomionym na SQL Server, musisz utworzyć linię bazową wydajności, która będzie używana do porównywania. 

Linia bazowa wydajności to zestaw parametrów, takich jak średnie/maksymalne użycie procesora CPU, średnie/maksymalne opóźnienie operacji we/wy dysku, przepływność, liczba IOPS, Średnia/maks. stron życia stron, średni maksymalny rozmiar bazy danych tempdb. Po migracji chcesz mieć podobne lub jeszcze lepsze parametry, dlatego ważne jest, aby mierzyć i rejestrować wartości bazowe dla tych parametrów. Oprócz parametrów systemowych należy wybrać zestaw reprezentatywnych zapytań lub najważniejszych zapytań w obciążeniu oraz mierzyć minimalny/średni/maksymalny czas trwania użycia procesora dla wybranych zapytań. Te wartości umożliwiają porównanie wydajności obciążeń uruchomionych na wystąpieniu zarządzanym z oryginalnymi wartościami w SQL Server źródłowym.

Niektóre parametry, które należy zmierzyć w wystąpieniu SQL Server, to: 
- [Monitoruj użycie procesora CPU w wystąpieniu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) i zarejestruj średnie i szczytowe użycie procesora CPU.
- [Monitoruj użycie pamięci w wystąpieniu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) i określ ilość pamięci używanej przez różne składniki, takie jak pula buforów, pamięć podręczna planu, pula magazynu kolumn, przetwarzanie [OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)itp. Ponadto należy znaleźć wartości średnie i szczytowe licznika wydajności stron pamięci na stronie.
- Monitoruj użycie operacji we/wy dysku na źródłowym wystąpieniu SQL Server przy użyciu wykazu [sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) widoku lub [liczników wydajności](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitoruj obciążenia i wydajność zapytań lub wystąpienie SQL Server, badając dynamiczne widoki zarządzania lub magazyn zapytań w przypadku migrowania z wersji SQL Server 2016 lub nowszego. Zidentyfikuj średni czas trwania i użycie procesora przez najważniejsze zapytania w obciążeniu, aby porównać je z kwerendami uruchomionymi w wystąpieniu zarządzanym.

> [!Note]
> Jeśli zauważysz dowolny problem związany z obciążeniem, SQL Server taki jak duże użycie procesora CPU, stałe wykorzystanie pamięci, baza danych tempdb lub problemy parametryzacja, należy spróbować rozwiązać je na źródłowym wystąpieniu SQL Server, zanim przeniesiesz linię bazową i migrację. Migracja informacji o znanych problemach do nowych migh systemu powoduje nieoczekiwane wyniki i unieważnienie wszystkich porównania wydajności.

W wyniku tego działania należy mieć udokumentowane średnie i szczytowe wartości użycia procesora CPU, pamięci i operacji we/wy w systemie źródłowym, a także średni i maksymalny czas trwania i użycie procesora CPU oraz najważniejsze zapytania w obciążeniu. Tych wartości należy używać później do porównywania wydajności obciążeń z wystąpieniem zarządzanym z wydajnością bazową obciążenia na SQL Server źródłowym.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Wdróż w optymalnie rozmieszczonym wystąpieniu zarządzanym

Wystąpienie zarządzane jest dostosowane do obciążeń lokalnych, które planują przejście do chmury. Wprowadzono [Nowy model zakupu](sql-database-service-tiers-vcore.md) , który zapewnia większą elastyczność w wyborze odpowiedniego poziomu zasobów dla obciążeń. W lokalnym świecie prawdopodobnie masz do rozbudowy te obciążenia przy użyciu rdzeni fizycznych i przepustowości we/wy. Model zakupu wystąpienia zarządzanego jest oparty na rdzeniach wirtualnych lub "rdzeni wirtualnych" z dodatkowym magazynem i funkcją we/wy. Model rdzeń wirtualny to prostszy sposób, aby zrozumieć wymagania dotyczące obliczeń w chmurze, a także to, co jest używane lokalnie. Ten nowy model umożliwia prawidłowe dopasowanie środowiska docelowego do chmury. Niektóre ogólne wskazówki, które mogą pomóc w wyborze odpowiedniej warstwy usług i charakterystyce, zostały opisane tutaj:
- W oparciu o użycie procesora bazowego można zainicjować obsługę wystąpienia zarządzanego, które jest zgodne z liczbą rdzeni, które są używane w SQL Server, biorąc pod uwagę, że konieczne może być skalowanie charakterystyki procesora w celu dopasowania ich do [charakterystyki maszyny wirtualnej, w której jest zainstalowane wystąpienie zarządzane](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Na podstawie użycia pamięci bazowej wybierz [warstwę usług, która ma zgodną pamięć](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Ilości pamięci nie można wybrać bezpośrednio, dlatego należy wybrać wystąpienie zarządzane z ilością rdzeni wirtualnych, która ma pasującą pamięć (na przykład 5,1 GB/rdzeń wirtualny w 5 rdzeń). 
- W oparciu o opóźnienie wejścia/wyjścia punktu odniesienia dla podsystemu plików wybierz między Ogólnego przeznaczenia (opóźnienie większe niż 5 ms) i Krytyczne dla działania firmy warstwami usług (opóźnienie mniejsze niż 3 ms).
- W oparciu o przepływność linii bazowej wstępnie przydzielić rozmiar danych lub plików dziennika, aby uzyskać oczekiwaną wydajność operacji we/wy.

Możesz wybrać zasoby obliczeniowe i magazynowe w czasie wdrażania, a następnie zmienić je później, bez wprowadzania przestojów aplikacji przy użyciu [Azure Portal](sql-database-scale-resources.md):

![ustalanie rozmiarów wystąpienia zarządzanego](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Aby dowiedzieć się, jak utworzyć infrastrukturę sieci wirtualnej i wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Ważne jest, aby zapewnić, że docelowa sieć wirtualna sieci wirtualnej jest zawsze zgodna z [wymaganiami sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Niezgodność może uniemożliwić tworzenie nowych wystąpień lub korzystanie z tych, które zostały już utworzone. Dowiedz się więcej na temat [tworzenia nowych](sql-database-managed-instance-create-vnet-subnet.md) i [konfigurowania istniejących](sql-database-managed-instance-configure-vnet-subnet.md) sieci.

## <a name="select-migration-method-and-migrate"></a>Wybierz metodę migracji i Przeprowadź migrację

Opcja wdrażania wystąpienia zarządzanego kieruje scenariusze użytkownika wymagające migracji masowej bazy danych z implementacji lokalnych lub IaaS baz danych. Jest to optymalny wybór w przypadku konieczności podnoszenia i przesunięcia zaplecza aplikacji, które regularnie używają poziomu wystąpienia i/lub funkcji między bazami danych. Jeśli jest to Twój scenariusz, możesz przenieść całe wystąpienie do odpowiedniego środowiska na platformie Azure bez konieczności ponownego tworzenia architektury aplikacji.

Aby przenieść wystąpienia bazy danych SQL, należy uważnie zaplanować:

- Migracja wszystkich baz danych, które muszą być rozmieszczone (działają w tym samym wystąpieniu)
- Migracja obiektów na poziomie wystąpienia, od których zależy aplikacja, w tym logowania, poświadczeń, zadań i operatorów programu SQL Agent oraz wyzwalaczy na poziomie serwera.

Wystąpienie zarządzane to usługa zarządzana, która umożliwia delegowanie niektórych zwykłych działań administratorów do platformy w miarę ich wbudowania. W związku z tym niektóre dane na poziomie wystąpienia nie muszą być migrowane, na przykład zadania konserwacji regularnego tworzenia kopii zapasowych lub zawsze w konfiguracji, ponieważ [wysoka dostępność](sql-database-high-availability.md) jest wbudowana.

Wystąpienie zarządzane obsługuje następujące opcje migracji bazy danych (obecnie są to jedyne obsługiwane metody migracji):

- Azure Database Migration Service — migracja z niezerowym przestojem,
- Natywne `RESTORE DATABASE FROM URL` — używa natywnych kopii zapasowych z SQL Server i wymaga pewnego przestoju.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących stron trzecich i SQL Server baz danych na platformę Azure. Opcje wdrażania w publicznej wersji zapoznawczej obejmują bazy danych Azure SQL Database i SQL Server baz danych na maszynie wirtualnej platformy Azure. DMS jest zalecaną metodą migracji obciążeń przedsiębiorstwa.

W przypadku korzystania z SQL Server Integration Services (SSIS) na SQL Server lokalnie usługa DMS nie obsługuje jeszcze migracji wykazu usług SSIS (SSISDB), który przechowuje pakiety SSIS, ale można udostępnić Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF), który utworzy nowy SSISDB w wystąpieniu zarządzanym, a następnie można ponownie wdrożyć pakiety, w temacie [create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Aby dowiedzieć się więcej na temat tego scenariusza i kroków konfiguracji dla usługi DMS, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natywne polecenie RESTORE z adresu URL

Przywracanie natywnych kopii zapasowych (plików. bak) pobranych z SQL Server lokalnych lub [program SQL Server w usłudze Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/), dostępnych w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/), jest jednym z najważniejszych funkcji wdrożenia wystąpienia zarządzanego, które umożliwiają szybką i łatwą migrację bazy danych w trybie offline.

Poniższy diagram przedstawia ogólny przegląd procesu:

![Migracja — przepływ](./media/sql-database-managed-instance-migration/migration-flow.png)

Poniższa tabela zawiera więcej informacji na temat metod, których można użyć w zależności od uruchomionej wersji SQL Server źródłowej:

|Krok|Aparat i wersja SQL|Metoda tworzenia kopii zapasowej/przywracania|
|---|---|---|
|Umieszczanie kopii zapasowej w usłudze Azure Storage|Wcześniejsza ZASTOSUJESZ pakietu CU2 programu SQL 2012 z dodatkiem SP1|Przekaż plik. bak bezpośrednio do usługi Azure Storage|
||2012 SP1 ZASTOSUJESZ PAKIETU CU2-2016|Bezpośrednia kopia zapasowa przy użyciu przestarzałej składni [poświadczeń](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 i więcej|Bezpośrednia kopia zapasowa przy użyciu [poświadczeń SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Przywracanie z usługi Azure Storage do wystąpienia zarządzanego|[Przywróć z adresu URL przy użyciu poświadczeń sygnatury dostępu współdzielonego](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Podczas migrowania bazy danych chronionej przez [transparent Data Encryption](transparent-data-encryption-azure-sql.md) do wystąpienia zarządzanego przy użyciu opcji przywracania natywnego należy zmigrować odpowiedni certyfikat z SQL Server lokalnego lub IaaS przed przywróceniem bazy danych. Aby uzyskać szczegółowe instrukcje, zobacz [Migrowanie certyfikatu TDE do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md)
> - Przywracanie systemowych baz danych nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywanych w bazach danych Master lub msdb), zalecamy wykonanie skryptów dla nich i uruchomienie skryptów T-SQL w wystąpieniu docelowym.

Przewodnik Szybki Start przedstawiający sposób przywracania kopii zapasowej bazy danych do wystąpienia zarządzanego przy użyciu poświadczeń sygnatury dostępu współdzielonego znajduje się w sekcji [przywracanie z kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorowanie aplikacji

Po zakończeniu migracji do wystąpienia zarządzanego należy śledzić zachowanie aplikacji i wydajność obciążeń. Ten proces obejmuje następujące działania:
- [Porównanie wydajności obciążenia uruchomionego w wystąpieniu zarządzanym](#compare-performance-with-the-baseline) z [linią bazową wydajności utworzoną w SQL Server źródłowym](#create-performance-baseline).
- Ciągłe [monitorowanie wydajności obciążeń](#monitor-performance) w celu identyfikowania potencjalnych problemów i ulepszeń.

### <a name="compare-performance-with-the-baseline"></a>Porównanie wydajności z linią bazową

Pierwsze działanie, które należy wykonać natychmiast po pomyślnej migracji, ma na celu porównanie wydajności obciążeń z podstawową wydajnością obciążeń. Celem tego działania jest upewnienie się, że wydajność obciążeń w wystąpieniu zarządzanym spełnia Twoje potrzeby. 

Migracja bazy danych do wystąpienia zarządzanego utrzymuje ustawienia bazy danych i jego oryginalny poziom zgodności w większości przypadków. Oryginalne ustawienia są zachowywane, jeśli jest to możliwe, aby zmniejszyć ryzyko obniżenia wydajności w porównaniu do SQL Server źródłowej. Jeśli poziom zgodności bazy danych użytkownika był 100 lub wyższy przed migracją, pozostaje taki sam po migracji. Jeśli poziom zgodności bazy danych użytkownika był 90 przed migracją, w uaktualnionej bazie danych poziom zgodności ma wartość 100, czyli najniższy obsługiwany poziom zgodności w wystąpieniu zarządzanym. Poziom zgodności systemowych baz danych to 140. Ponieważ migracja do wystąpienia zarządzanego jest w rzeczywistości migrowana do najnowszej wersji aparatu bazy danych SQL Server, należy pamiętać, że należy ponownie przetestować wydajność obciążenia, aby uniknąć niektórych problemów z wydajnością zaskakujące.

Jako warunek wstępny upewnij się, że zostały wykonane następujące działania:
- Wyrównaj ustawienia wystąpienia zarządzanego przy użyciu ustawień z wystąpienia źródłowego SQL Server, badając różne wystąpienia, bazę danych, ustawienia temdb i konfiguracje. Przed uruchomieniem pierwszego porównania wydajności upewnij się, że nie zmieniono ustawień, takich jak poziomy zgodności lub szyfrowanie, lub zaakceptuj ryzyko, że niektóre nowe funkcje, które zostały włączone, mogą mieć wpływ na niektóre zapytania. Aby zmniejszyć ryzyko związane z migracją, należy zmienić poziom zgodności bazy danych tylko po monitorowaniu wydajności.
- Zaimplementuj [wskazówki dotyczące najlepszych rozwiązań dotyczących magazynu dla ogólnego przeznaczenia](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) takich jak wstępne przydzielanie rozmiaru plików, aby uzyskać lepszą wydajność.
- Zapoznaj się z [najważniejszymi różnicami w środowisku, które mogą spowodować różnice w wydajności między wystąpieniem zarządzanym i SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) i zidentyfikować zagrożenia, które mogą mieć wpływ na wydajność.
- Upewnij się, że włączono obsługę magazynu zapytań i dostrajania automatycznego na zarządzanym wystąpieniu. Te funkcje umożliwiają mierzenie wydajności obciążeń i automatyczne rozwiązanie potencjalnych problemów z wydajnością. Dowiedz się, jak używać magazynu zapytań jako optymalnego narzędzia do uzyskiwania informacji o wydajności obciążeń przed i po zmianie poziomu zgodności bazy danych, zgodnie z opisem w temacie [zachowanie stabilności wydajności podczas uaktualniania do nowszej wersji SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po przygotowaniu środowiska, które jest porównywalne, jak to możliwe, z środowiskiem lokalnym, możesz rozpocząć pracę z obciążeniem i zmierzyć wydajność. Proces pomiaru powinien zawierać te same parametry, które są mierzone [podczas tworzenia bazowej wydajności miar obciążenia na SQL Server źródłowej](#create-performance-baseline).
W związku z tym należy porównać parametry wydajności z linią bazową i identyfikować różnice krytyczne.

> [!NOTE]
> W wielu przypadkach nie jest możliwe uzyskanie dokładnego dopasowania wydajności na wystąpieniu zarządzanym i SQL Server. Wystąpienie zarządzane to SQL Server aparat bazy danych, ale konfiguracja infrastruktury i wysokiej dostępności na wystąpieniu zarządzanym może spowodować pewne różnice. Może się zdarzyć, że niektóre zapytania będą szybsze, a niektóre inne mogą być wolniejsze. Celem porównania jest upewnienie się, że wydajność obciążenia w wystąpieniu zarządzanym jest zgodna z wydajnością na SQL Server (średnia), a identyfikacja zawiera krytyczne zapytania o wydajności, które nie są zgodne z oryginalną wydajnością.

Wynikiem porównania wydajności mogą być:
- Wydajność obciążeń w wystąpieniu zarządzanym jest wyrównana lub lepsza, ponieważ wydajność obciążeń SQL Server. W takim przypadku pomyślnie potwierdzono, że migracja się powiodła.
- Większość parametrów wydajności i zapytań w obciążeniu działa prawidłowo, z pewnymi wyjątkami o obniżonej wydajności. W takim przypadku należy zidentyfikować różnice i ich znaczenie. Jeśli istnieją ważne zapytania o obniżonej wydajności, należy zbadać, czy zostały zmienione bazowe plany SQL lub czy zapytania będą powodować pewne limity zasobów. W takim przypadku środki zaradcze mogą być stosowane w przypadku zapytań krytycznych (na przykład zmieniony poziom zgodności, Starsza Kardynalność szacowania) albo bezpośrednio lub za pomocą przewodników planu, odbudowania lub utworzenia statystyk i indeksów, które mogą mieć wpływ na plany. 
- Większość zapytań jest wolniejsza w wystąpieniu zarządzanym w porównaniu z SQL Serverą źródłową. W takim przypadku spróbuj zidentyfikować główne przyczyny różnicy, takie jak [osiągnięcie limitu zasobów]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) , takich jak limity operacji wejścia/wyjścia, limit pamięci, Limit szybkości dziennika wystąpienia itd. Jeśli nie ma limitów zasobów, które mogą spowodować różnicę, spróbuj zmienić poziom zgodności bazy danych lub zmienić ustawienia bazy danych, takie jak starsze oszacowanie kardynalności i ponownie uruchom test. Zapoznaj się z zaleceniami podanymi w widokach wystąpienia zarządzanego lub magazynu zapytań, aby zidentyfikować zapytania, które uległa pogorszeniuą wydajność.

> [!IMPORTANT]
> Wystąpienie zarządzane ma wbudowaną funkcję automatycznego korygowania planu, która jest domyślnie włączona. Ta funkcja zapewnia, że zapytania, które działały prawidłowo w trakcie wklejania, nie obniżą się w przyszłości. Upewnij się, że ta funkcja jest włączona i że obciążenie zostało wykonane wystarczająco długo przy użyciu starych ustawień, zanim zmienisz nowe ustawienia w celu włączenia wystąpienia zarządzanego, aby dowiedzieć się więcej o wydajności i planach bazowych.

Zmień parametry lub warstwy usługi uaktualniania na zbieżność z optymalną konfiguracją do momentu uzyskania wydajności, która odpowiada Twoim potrzebom.

### <a name="monitor-performance"></a>Monitorowanie wydajności

Wystąpienie zarządzane udostępnia wiele zaawansowanych narzędzi do monitorowania i rozwiązywania problemów oraz służy do monitorowania wydajności w wystąpieniu. Oto niektóre z parametrów, które muszą być monitorowane:
- Użycie procesora CPU w wystąpieniu w celu ustalenia, czy liczba zainicjowanych rdzeni wirtualnych jest właściwym dopasowaniem dla obciążenia.
- Stron życia strony na wystąpieniu zarządzanym, aby określić [, czy potrzebna jest dodatkowa pamięć](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Zaczekaj `INSTANCE_LOG_GOVERNOR` na `PAGEIOLATCH` to, że dane statystyczne zawierają problemy ze stanem operacji we/wy magazynu, szczególnie w przypadku warstwy ogólnego przeznaczenia, w której może być konieczne wstępne przydzielenie plików w celu uzyskania lepszej wydajności operacji we/wy.

## <a name="leverage-advanced-paas-features"></a>Korzystanie z zaawansowanych funkcji PaaS

Gdy korzystasz z w pełni zarządzanej platformy i sprawdzono, że osiągi obciążeń są zgodne z wydajnością SQL Server obciążenia, Skorzystaj z zalet, które są udostępniane automatycznie w ramach usługi SQL Database. 

Nawet jeśli nie wprowadzisz zmian w wystąpieniu zarządzanym podczas migracji, istnieje duże prawdopodobieństwo, że niektóre nowe funkcje zostaną włączone podczas pracy z wystąpieniem w celu skorzystania z najnowszych ulepszeń aparatu bazy danych. Niektóre zmiany są włączone tylko po [zmianie poziomu zgodności bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Na przykład nie trzeba tworzyć kopii zapasowych w wystąpieniu zarządzanym — usługa automatycznie wykonuje kopie zapasowe. Nie musisz już martwić się o planowanie i tworzenie kopii zapasowych oraz zarządzanie nimi. Wystąpienie zarządzane umożliwia przywracanie do dowolnego punktu w czasie w tym okresie przechowywania przy użyciu funkcji odzyskiwania do [punktu w czasie (kopie)](sql-database-recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba martwić się o skonfigurowanie wysokiej dostępności, ponieważ [wysoka dostępność](sql-database-high-availability.md) jest wbudowana.

Aby zwiększyć bezpieczeństwo, należy rozważyć użycie [uwierzytelniania Azure Active Directory](sql-database-security-overview.md), [inspekcji](sql-database-managed-instance-auditing.md), [wykrywania zagrożeń](sql-database-advanced-data-security.md), [zabezpieczeń na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)i [dynamicznego maskowania danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń, wystąpienie zarządzane udostępnia zestaw zaawansowanych narzędzi, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](sql-database-monitor-tune-overview.md). [Usługa Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych i scentralizowanie monitorowania dużej liczby wystąpień i baz danych. [Dostrajanie automatyczne](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) w wystąpieniu zarządzanym stale monitoruje wydajność statystyk wykonywania planu SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Aby zapoznać się z samouczkiem zawierającym przywracanie z kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem dotyczącym migracji za pomocą usługi DMS, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

---
title: 'Oracle do usługi Azure Synapse Analytics: Przewodnik migracji'
description: W poniższych sekcjach zawarto informacje na temat tego, czego dotyczy Migrowanie istniejącego rozwiązania bazy danych Oracle do usługi Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: bcabb11d67e621ebd2449f2148a0a86ba32d27d9
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419205"
---
# <a name="migration-guide-migrate-oracle-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Przewodnik migracji: Migrowanie magazynu danych Oracle do dedykowanej puli SQL w usłudze Azure Synapse Analytics
W poniższych sekcjach zawarto informacje na temat tego, czego dotyczy Migrowanie istniejącego rozwiązania magazynu danych firmy Oracle do usługi Azure Synapse Analytics.

## <a name="overview"></a>Omówienie
Przed przeprowadzeniem migracji należy sprawdzić, czy usługa Azure Synapse Analytics jest najlepszym rozwiązaniem dla obciążenia. Azure Synapse Analytics to rozproszony system przeznaczony do przeprowadzania analiz w przypadku dużych ilości danych. Migrowanie do usługi Azure Synapse Analytics wymaga pewnych zmian projektowych, które nie są trudne do zrozumienia, ale mogą one zająć trochę czasu. Jeśli Twoja firma wymaga hurtowni danych klasy korporacyjnej, korzyści są nadające się do tego nakładu pracy. Jeśli jednak nie potrzebujesz możliwości usługi Azure Synapse Analytics, jest to bardziej ekonomiczne użycie [SQL Server](https://docs.microsoft.com/sql/sql-server/) lub [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Rozważ użycie usługi Azure Synapse Analytics:
- Mieć co najmniej jeden terabajt danych.
- Zaplanuj Uruchamianie analizy w przypadku dużych ilości danych.
- Potrzebna jest możliwość skalowania zasobów obliczeniowych i magazynu.
- Chcesz zaoszczędzić na kosztach przez wstrzymanie zasobów obliczeniowych, gdy nie są one potrzebne.

Zamiast usługi Azure Synapse Analytics należy rozważyć inne opcje obciążeń operacyjnych (OLTP), które mają:
- Odczyty i zapisy o wysokiej częstotliwości.
- Wybrano dużą liczbę elementów pojedynczo.
- Duże ilości pojedynczych wierszy wstawiania.
- Potrzeby przetwarzania wierszy w wierszach.
- Niezgodne formaty (JSON, XML).

## <a name="prerequisites"></a>Wymagania wstępne
Aby przeprowadzić migrację bazy danych Oracle do usługi Azure Synapse Analytics, upewnij się, że masz następujące wymagania wstępne: 

- Magazyn danych lub obciążenie analizy 
- ASYSTENCIE migracji for Oracle do konwersji obiektów Oracle na SQL Server. Aby uzyskać więcej informacji [, zobacz Migrowanie baz danych Oracle do SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) . 
- Najnowsza wersja narzędzia do [ścieżki usługi Azure Synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) do migracji obiektów SQL Server do obiektów Synapse platformy Azure.
- [Dedykowana Pula SQL](../get-started-create-workspace.md) w obszarze roboczym usługi Azure Synapse.  


## <a name="pre-migration"></a>Przed migracją
Po podjęciu decyzji o migracji istniejącego rozwiązania do usługi Azure Synapse Analytics ważne jest zaplanowanie migracji przed rozpoczęciem pracy. Głównym celem planowania jest upewnienie się, że dane, schematy tabel i kod są zgodne z usługą Azure Synapse Analytics. Istnieją pewne różnice zgodności między bieżącym systemem i SQL Data Warehouse, które należy obejść. Ponadto Migrowanie dużych ilości danych na platformę Azure trwa. Staranne planowanie przyspiesza proces pobierania danych na platformę Azure. Innym najważniejszym celem planowania jest dostosowanie projektu, aby upewnić się, że rozwiązanie w pełni korzysta z wysokiej wydajności zapytań, którą zapewnia usługa Azure Synapse Analytics. Projektowanie magazynów danych na potrzeby skalowania wprowadza unikatowe wzorce projektowe, dlatego tradycyjne podejścia nie zawsze są najlepszym rozwiązaniem. Niektóre dostosowania projektu można wykonać po migracji, dzięki czemu zmiany wprowadzone we wcześniejszej części procesu spowodują zaoszczędzenie czasu.

## <a name="azure-synapse-pathway"></a>Ścieżka usługi Azure Synapse
Jeden z najważniejszych użytkowników z zażądanymi członkami jest tłumaczy swój kod SQL podczas migracji z jednego systemu do drugiego. [Usługa Azure Synapseing](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ułatwia uaktualnienie do nowoczesnej platformy magazynu danych przez Automatyzowanie translacji kodu istniejącego magazynu danych. Jest to bezpłatne, intuicyjne i łatwe w użyciu narzędzie, które automatyzuje tłumaczenie kodu umożliwiające szybszą migrację do usługi Azure Synapse Analytics.

## <a name="migrate"></a>Migrate
Wykonanie pomyślnej migracji wymaga przeprowadzenia migracji schematów, kodu i danych tabeli. Aby uzyskać bardziej szczegółowe wskazówki dotyczące tych tematów, zobacz:
- Artykuł [migruje schematy](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- W artykule [Migrowanie kodu](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- Artykuł [migruje dane](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Dodatkowe zasoby 
- Ta część (zespół doradczy klienta) ma pewne wspaniałe wskazówki dotyczące usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) opublikowane jako ogłoszenia na blogu. Zapoznaj się z artykułem [Migrowanie danych do Azure SQL Data Warehouse w](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)ramach programu w celu uzyskania dodatkowych wskazówek dotyczących migracji.
- Zapoznaj się z oficjalnym dokumentem dotyczącym [wybierania ścieżki migracji bazy danych na platformę Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) , aby uzyskać dodatkowe informacje i zalecenia.
- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

## <a name="migration-assets-from-real-world-engagements"></a>Zasoby migracji z rzeczywistych zaangażowania
Aby uzyskać dodatkową pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w ramach obsługi projektu migracji rzeczywistej.

| Tytuł/link                              | Opis                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które znacznie ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej. |
| [Obsługa problemów z kodowaniem danych podczas ładowania danych do usługi Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ten blog ma na celu zapewnienie wglądu w informacje na temat niektórych problemów z kodowaniem danych, które mogą wystąpić podczas korzystania z bazy danych, aby załadować dane do SQL Data Warehouse. Ten artykuł zawiera również opcje, których można użyć do pokonania tych problemów i załadowania danych. |
| [Pobieranie rozmiarów tabel w puli SQL usługi Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednym z najważniejszych zadań, które musi wykonać architekt, jest uzyskanie metryk dotyczących nowego środowiska po migracji: gromadzenie czasów ładowania z lokalizacji lokalnej do chmury, zbieranie podstawowych czasów ładowania itp. Z tych zadań jednym z najważniejszych jest określenie rozmiaru magazynu w SQL Data Warehouse porównaniu z bieżącą platformą klienta. |
| [Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do usługi Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skrypt programu PowerShell, który tworzy skrypt poleceń T-SQL, aby ponownie tworzyć logowania i wybierać użytkowników bazy danych z "lokalnych" SQL Server do usługi Azure SQL PaaS. Narzędzie to umożliwia automatyczne mapowanie kont usługi Windows AD na konta usługi Azure AD lub umożliwia wyszukiwanie nazw UPN dla każdej nazwy logowania w lokalnych oknach Active Directory. Narzędzie opcjonalnie przenosi SQL Server natywnych nazw logowania. Niestandardowe role serwera i bazy danych są skryptami, a także członkostwem ról i rolą bazy danych oraz uprawnieniami użytkownika. Zawarte bazy danych nie są jeszcze obsługiwane i tylko podzbiór możliwych uprawnień SQL Server są skryptami. oznacza to, że uprawnienia udzielanie dotacji nie są obsługiwane (złożone drzewa uprawnień). Więcej szczegółów można znaleźć w dokumencie pomocy technicznej, a skrypt ma Komentarze ułatwiające zrozumienie. |

> [!NOTE]
> Powyższe zasoby zostały opracowane w ramach programu Data Migration szybko Rozpocznij pracę (DM szybko Rozpocznij pracę), który jest sponsorowany przez Zespół inżynieryjny grupy danych platformy Azure. Podstawowa karta DM szybko Rozpocznij pracę polega na odblokowaniu i przyspieszeniu złożonej modernizacji i konkurowaniu możliwości migracji platformy danych do platformy danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie DM szybko Rozpocznij pracę, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="videos"></a>Filmy wideo
- Aby zapoznać się z omówieniem przewodnika migracji usługi Azure Database i zawartych w nim informacji, zobacz wideo [jak korzystać z przewodnika migracji bazy danych](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Aby zapoznać się z krokami procesu migracji i szczegółami dotyczącymi konkretnych narzędzi i usług zalecanych do przeprowadzenia oceny i migracji, zobacz film wideo [Przegląd podróży migracji oraz narzędzia/usługi zalecane do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
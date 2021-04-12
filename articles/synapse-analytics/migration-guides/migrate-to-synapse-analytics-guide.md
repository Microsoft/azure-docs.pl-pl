---
title: 'Azure Synapse Analytics: Przewodnik migracji'
description: Postępuj zgodnie z tym przewodnikiem, aby przeprowadzić migrację baz danych do dedykowanej puli SQL usługi Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8304064e62ea3996e2ee6be6e12885cb853c9375
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278783"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrowanie magazynu danych do dedykowanej puli SQL w usłudze Azure Synapse Analytics

W poniższych sekcjach zawarto informacje na temat tego, czego dotyczy Migrowanie istniejącego rozwiązania magazynu danych do dedykowanej puli SQL usługi Azure Synapse Analytics.

## <a name="overview"></a>Omówienie

Przed rozpoczęciem migracji należy sprawdzić, czy usługa Azure Synapse Analytics jest najlepszym rozwiązaniem dla obciążenia. Azure Synapse Analytics to rozproszony system przeznaczony do przeprowadzania analiz w przypadku dużych ilości danych. Migrowanie do usługi Azure Synapse Analytics wymaga wprowadzenia pewnych zmian projektowych, które nie są trudne do zrozumienia, ale mogą one zająć trochę czasu. Jeśli Twoja firma wymaga hurtowni danych klasy korporacyjnej, korzyści są nadające się do tego nakładu pracy. Jeśli jednak nie potrzebujesz możliwości analizy usługi Azure Synapse, jest to bardziej ekonomiczne użycie [SQL Server](https://docs.microsoft.com/sql/sql-server/) lub [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

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
- Niezgodne formaty (na przykład JSON i XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Jeden z najważniejszych klientów z zaatakami jest tłumaczy obiekty bazy danych podczas migracji z jednego systemu do drugiego. [Ścieżka usługi Azure Synapse](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ułatwia uaktualnienie do nowoczesnej platformy magazynu danych przez automatyzację tłumaczenia obiektów istniejącego magazynu danych. Jest to bezpłatne, intuicyjne i łatwe w użyciu narzędzie, które automatyzuje tłumaczenie kodu, aby umożliwić szybsze Migrowanie do usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="migrate-from-sql-server"></a>[Migrowanie z programu SQL Server](#tab/migratefromSQLServer)

Aby przeprowadzić migrację SQL Server magazynu danych do usługi Azure Synapse Analytics, upewnij się, że zostały spełnione następujące wymagania wstępne:

- Wykorzystaj magazyn danych lub obciążenie analityczne.
- Pobierz najnowszą wersję [ścieżki usługi Azure Synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) , aby migrować obiekty SQL Server do obiektów Synapse platformy Azure.
- Posiadanie [dedykowanej puli SQL](../get-started-create-workspace.md) w obszarze roboczym usługi Azure Synapse.

# <a name="migrate-from-netezza"></a>[Migrowanie z Netezza](#tab/migratefromNetezza)

Aby przeprowadzić migrację magazynu danych Netezza do usługi Azure Synapse Analytics, upewnij się, że zostały spełnione następujące wymagania wstępne:

- Pobierz najnowszą wersję [ścieżki usługi Azure Synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) , aby migrować obiekty SQL Server do obiektów Synapse platformy Azure.
- Posiadanie [dedykowanej puli SQL](../get-started-create-workspace.md) w obszarze roboczym usługi Azure Synapse.

Aby uzyskać więcej informacji, zobacz [Azure Synapse Analytics Solutions and Migration for Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrowanie z płatka śniegu](#tab/migratefromSnowflake)

Aby przeprowadzić migrację magazynu danych płatnych śniegu do usługi Azure Synapse Analytics, upewnij się, że zostały spełnione następujące wymagania wstępne:

- Pobierz najnowszą wersję [ścieżki usługi Azure Synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) , aby migrować obiekty płatne z śniegu do obiektów usługi Azure Synapse.
- Posiadanie [dedykowanej puli SQL](../get-started-create-workspace.md) w obszarze roboczym usługi Azure Synapse.

# <a name="migrate-from-oracle"></a>[Migrowanie z programu Oracle](#tab/migratefromOracle)

Aby przeprowadzić migrację bazy danych Oracle do usługi Azure Synapse Analytics, upewnij się, że zostały spełnione następujące wymagania wstępne:

- Wykorzystaj magazyn danych lub obciążenie analityczne.
- Pobierz Asystent migracji do programu SQL Server dla programu Oracle w celu przekonwertowania obiektów Oracle na SQL Server. Aby uzyskać więcej informacji, zobacz [Migrowanie baz danych Oracle do SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Pobierz najnowszą wersję [ścieżki usługi Azure Synapse](https://www.microsoft.com/download/details.aspx?id=102787) , aby migrować obiekty SQL Server do obiektów Synapse platformy Azure.
- Posiadanie [dedykowanej puli SQL](../get-started-create-workspace.md) w obszarze roboczym usługi Azure Synapse.

Aby uzyskać więcej informacji, zobacz [Azure Synapse Analytics Solutions and Migration for Oracle Data Warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Przed migracją

Po podjęciu decyzji o migracji istniejącego rozwiązania do usługi Azure Synapse Analytics należy zaplanować migrację przed rozpoczęciem pracy. Głównym celem planowania jest upewnienie się, że dane, schematy tabel i kod są zgodne z usługą Azure Synapse Analytics. Istnieją pewne różnice w zakresie zgodności między bieżącym systemem i analizą usługi Azure Synapse, które należy obejść. Ponadto Migrowanie dużych ilości danych na platformę Azure trwa. Staranne planowanie przyspiesza proces pobierania danych na platformę Azure.

Innym najważniejszym celem planowania jest dostosowanie projektu, aby upewnić się, że rozwiązanie w pełni korzysta z wysokiej wydajności zapytań, którą zapewnia usługa Azure Synapse Analytics. Projektowanie magazynów danych na potrzeby skalowania wprowadza unikatowe wzorce projektowe, dlatego tradycyjne podejścia nie zawsze są najlepszym rozwiązaniem. Niektóre dostosowania projektu można wykonać po migracji, dzięki czemu zmiany wprowadzone we wcześniejszej części procesu spowodują zaoszczędzenie czasu.

## <a name="migrate"></a>Migrate

Wykonanie pomyślnej migracji wymaga przeprowadzenia migracji schematów, kodu i danych tabeli. Aby uzyskać bardziej szczegółowe wskazówki dotyczące tych tematów, zobacz następujące artykuły:

- [Rozważ zaprojektowanie tabeli](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Rozważ zmianę kodu](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrowanie danych](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Rozważ zarządzanie obciążeniami](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Więcej zasobów

Zespół ds. doradztwa klienta ma pewne wspaniałe wskazówki dotyczące usługi Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) opublikowane jako wpisy w blogu. Aby uzyskać więcej informacji na temat migracji, zobacz [Migrowanie danych do Azure SQL Data Warehouse w ramach ćwiczeń](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Zasoby migracji z rzeczywistych zaangażowania

Aby uzyskać więcej pomocy przy wykonywaniu tego scenariusza migracji, zobacz następujące zasoby. Zostały one opracowane w ramach obsługi rzeczywistego projektu migracji.

| Tytuł/link                              | Opis                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji lub bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej. |
| [Obsługa problemów z kodowaniem danych podczas ładowania danych do usługi Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ten wpis w blogu zawiera szczegółowe informacje na temat niektórych problemów z kodowaniem danych, które mogą wystąpić podczas korzystania z bazy danych w celu załadowania do SQL Data Warehouse. Ten artykuł zawiera również opcje, których można użyć do pokonania tych problemów i załadowania danych. |
| [Pobieranie rozmiarów tabel w dedykowanej puli SQL usługi Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednym z najważniejszych zadań, które musi wykonać architekt, jest uzyskanie metryk dotyczących nowego środowiska po migracji. Przykładem może być zbieranie czasów ładowania z lokalizacji lokalnej do chmury i gromadzenie podstawowych czasów ładowania. Jednym z najważniejszych zadań jest określenie rozmiaru magazynu w SQL Data Warehouse porównaniu z bieżącą platformą klienta. |
| [Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do usługi Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skrypt programu PowerShell tworzy skrypt poleceń T-SQL, aby ponownie tworzyć logowania i wybierać użytkowników bazy danych z wystąpienia lokalnego SQL Server do usługi Azure SQL platform jako usługi (PaaS). Narzędzie to umożliwia automatyczne mapowanie kont Active Directory systemu Windows Server na konta Azure Active Directory lub umożliwia wyszukiwanie nazw UPN dla każdej nazwy logowania na lokalnym Active Directory systemu Windows Server. Narzędzie opcjonalnie przenosi SQL Server natywne nazwy logowania. Niestandardowe role serwera i bazy danych są skryptami wraz z członkostwem w roli, rolą bazy danych i uprawnieniami użytkownika. Zawarte bazy danych nie są obsługiwane, a tylko podzbiór możliwych uprawnień SQL Server są skryptami. Więcej informacji można znaleźć w dokumencie pomocy technicznej, a skrypt ma Komentarze ułatwiające zrozumienie. |

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.

## <a name="videos"></a>Filmy wideo

Obejrzyj, jak [Walgreens zmigrowany system spisu sprzedaży detalicznej](https://www.youtube.com/watch?v=86dhd8N1lH4) z około 100 TB danych z Netezza do analizy Synapse Azure w czasie rejestracji.

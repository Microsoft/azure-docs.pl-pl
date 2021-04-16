---
title: 'Azure Synapse Analytics: Przewodnik migracji'
description: Postępuj zgodnie z tym przewodnikiem, aby przeprowadzić migrację baz danych do Azure Synapse Analytics dedykowanej puli SQL.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565648"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrowanie magazynu danych do dedykowanej puli SQL w Azure Synapse Analytics

Poniższe sekcje zawierają omówienie czynności związanych z migrowanie istniejącego rozwiązania magazynu danych do Azure Synapse Analytics puli SQL.

## <a name="overview"></a>Omówienie

Przed rozpoczęciem migracji należy sprawdzić, czy Azure Synapse Analytics jest najlepszym rozwiązaniem dla obciążenia. Azure Synapse Analytics to rozproszony system przeznaczony do przeprowadzania analiz dużych ilości danych. Migracja do Azure Synapse Analytics wymaga pewnych zmian projektowych, które nie są trudne do zrozumienia, ale implementacja może zająć trochę czasu. Jeśli Twoja firma wymaga magazynu danych klasy korporacyjnej, korzyści są warte wysiłku. Jeśli jednak nie potrzebujesz możliwości usługi Azure Synapse Analytics, bardziej ekonomiczne jest użycie usługi [SQL Server](https://docs.microsoft.com/sql/sql-server/) lub [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Rozważ użycie Azure Synapse Analytics podczas:

- Mieć co najmniej jeden terabajt danych.
- Planowanie uruchamiania analiz dotyczących znacznych ilości danych.
- Potrzebna jest możliwość skalowania zasobów obliczeniowych i magazynu.
- Chcesz zaoszczędzić na kosztach, ws wymagając wsadu zasobów obliczeniowych, gdy nie są potrzebne.

Zamiast Azure Synapse Analytics należy rozważyć inne opcje dla obciążeń operacyjnych (OLTP), które mają:

- Odczyty i zapis o wysokiej częstotliwości.
- Wybiera dużą liczbę pojedynczych opcji.
- Duże ilości wstawień pojedynczego wiersza.
- Przetwarzanie wierszy po wierszu wymaga przetwarzania.
- Niezgodne formaty (na przykład JSON i XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Jedną z krytycznych blokad, z którym muszą się zmierzyć klienci, jest tłumaczenie obiektów bazy danych podczas migracji z jednego systemu do innego. [Azure Synapse Ścieżka](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ułatwia uaktualnienie do nowoczesnej platformy magazynu danych przez zautomatyzowanie tłumaczenia obiektów istniejącego magazynu danych. Jest to bezpłatne, intuicyjne i łatwe w użyciu narzędzie, które automatyzuje tłumaczenie kodu, aby umożliwić szybszą migrację do Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="migrate-from-sql-server"></a>[Migrowanie z programu SQL Server](#tab/migratefromSQLServer)

Aby przeprowadzić migrację SQL Server magazynu danych do Azure Synapse Analytics, upewnij się, że zostały spełnione następujące wymagania wstępne:

- Obciążenie magazynu danych lub analizy.
- Pobierz najnowszą wersję ścieżki [Azure Synapse,](https://www.microsoft.com/en-us/download/details.aspx?id=102787) aby przeprowadzić migrację SQL Server do Azure Synapse obiektów.
- Mieć [dedykowaną pulę SQL](../get-started-create-workspace.md) w Azure Synapse roboczym.

# <a name="migrate-from-netezza"></a>[Migrowanie z netezza](#tab/migratefromNetezza)

Aby przeprowadzić migrację magazynu danych Netezza do Azure Synapse Analytics, upewnij się, że spełniliśmy następujące wymagania wstępne:

- Pobierz najnowszą wersję ścieżki [Azure Synapse,](https://www.microsoft.com/en-us/download/details.aspx?id=102787) aby przeprowadzić migrację SQL Server do Azure Synapse obiektów.
- Mieć [dedykowaną pulę SQL](../get-started-create-workspace.md) w Azure Synapse roboczym.

Aby uzyskać więcej informacji, [zobacz Azure Synapse Analytics i migracja dla netezza.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)

# <a name="migrate-from-snowflake"></a>[Migrowanie z aplikacji Snowflake](#tab/migratefromSnowflake)

Aby przeprowadzić migrację magazynu danych snowflake do Azure Synapse Analytics, upewnij się, że spełniliśmy następujące wymagania wstępne:

- Pobierz najnowszą wersję ścieżki [Azure Synapse, aby](https://www.microsoft.com/en-us/download/details.aspx?id=102787) przeprowadzić migrację obiektów Snowflake do Azure Synapse obiektów.
- Mieć [dedykowaną pulę SQL](../get-started-create-workspace.md) w Azure Synapse roboczym.

# <a name="migrate-from-oracle"></a>[Migrowanie z bazy danych Oracle](#tab/migratefromOracle)

Aby przeprowadzić migrację magazynu danych Oracle do Azure Synapse Analytics, upewnij się, że spełniliśmy następujące wymagania wstępne:

- Obciążenie magazynu danych lub analizy.
- Pobierz Asystent migracji do programu SQL Server oracle, aby przekonwertować obiekty Oracle na SQL Server. Aby uzyskać więcej informacji, zobacz [Migrowanie baz danych Oracle do SQL Server (OracleToSQL).](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)
- Pobierz najnowszą wersję ścieżki [Azure Synapse,](https://www.microsoft.com/download/details.aspx?id=102787) aby przeprowadzić migrację SQL Server do Azure Synapse obiektów.
- Mieć [dedykowaną pulę SQL](../get-started-create-workspace.md) w Azure Synapse roboczym.

Aby uzyskać więcej informacji, [zobacz Azure Synapse Analytics i migracja dla magazynu danych Oracle.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)

---

## <a name="pre-migration"></a>Przed migracją

Po podjęciu decyzji o migracji istniejącego rozwiązania do usługi Azure Synapse Analytics należy zaplanować migrację przed rozpoczęciem. Głównym celem planowania jest zapewnienie zgodności danych, schematów tabel i kodu z Azure Synapse Analytics. Istnieją pewne różnice w zgodności między bieżącym systemem i Azure Synapse Analytics, które należy pomiń. Ponadto migracja dużych ilości danych na platformę Azure wymaga czasu. Staranne planowanie przyspieszy proces pobierania danych na platformę Azure.

Innym kluczowym celem planowania jest dostosowanie projektu w celu zapewnienia, że rozwiązanie w pełni wykorzystuje wysoką wydajność zapytań, Azure Synapse Analytics została zaprojektowana w taki sposób. Projektowanie magazynów danych na skalę wprowadza unikatowe wzorce projektowe, więc tradycyjne podejścia nie zawsze są najlepsze. Chociaż niektóre korekty projektu można wprowadzić po migracji, wprowadzenie zmian wcześniej w procesie pozwoli zaoszczędzić czas później.

## <a name="migrate"></a>Migrate

Pomyślna migracja wymaga zmigrowania schematów tabel, kodu i danych. Aby uzyskać bardziej szczegółowe wskazówki dotyczące tych tematów, zobacz następujące artykuły:

- [Rozważanie projektu tabeli](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Rozważanie zmiany kodu](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrowanie danych](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Rozważ zarządzanie obciążeniami](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Więcej zasobów

Zespół doradczy klientów ma kilka świetnych wskazówek Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) opublikowanych jako wpisy w blogu. Aby uzyskać więcej informacji na temat migracji, zobacz [Migrowanie danych do Azure SQL Data Warehouse w praktyce](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migracja zasobów z rzeczywistych zaangażowania

Aby uzyskać więcej pomocy przy ukończeniu tego scenariusza migracji, zobacz następujące zasoby. Zostały one opracowane w celu wspierania rzeczywistego zaangażowania projektu migracji.

| Tytuł/link                              | Opis                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie do oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane "najlepsze dopasowanie" platform docelowych, gotowość do chmury oraz poziom korygowania aplikacji lub bazy danych dla danego obciążenia. Oferuje proste obliczenia i generowanie raportów jednym kliknięciem, które pomagają przyspieszyć ocenę dużych nieruchomości, zapewniając zautomatyzowany i jednolity proces podejmowania decyzji na platformie docelowej. |
| [Obsługa problemów z kodowaniem danych podczas ładowania danych do Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ten wpis w blogu zawiera szczegółowe informacje na temat niektórych problemów z kodowaniem danych, które mogą wystąpić podczas ładowania danych do programu SQL Data Warehouse przy użyciu technologii PolyBase. Ten artykuł zawiera również niektóre opcje, których można użyć do rozwiązania takich problemów i pomyślnego załadowania danych. |
| [Pobieranie rozmiarów tabel w Azure Synapse Analytics dedykowanej puli SQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednym z kluczowych zadań, które musi wykonać architekt, jest uzyskiwanie metryk dotyczących nowego środowiska po migracji. Przykłady obejmują zbieranie czasów ładowania ze środowisk lokalnych do chmury i zbieranie czasów ładowania programu PolyBase. Jednym z najważniejszych zadań jest określenie rozmiaru magazynu w programie SQL Data Warehouse w porównaniu z bieżącą platformą klienta. |
| [Narzędzie do przenoszenia lokalnych SQL Server logowania do Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skrypt programu PowerShell tworzy skrypt polecenia języka T-SQL w celu ponownego utworzenia identyfikatorów logowania i wybrania użytkowników bazy danych z lokalnego wystąpienia usługi SQL Server do usługi Azure SQL Platform as a Service (PaaS). Narzędzie umożliwia automatyczne mapowanie kont usługi Windows Server Active Directory do kont Azure Active Directory lub umożliwia wyszukiwania nazw UPN dla każdego logowania do kont lokalnych Windows Server Active Directory. Narzędzie opcjonalnie przenosi również SQL Server logowania natywne. Niestandardowe role serwera i bazy danych są skryptowane wraz z członkostwem w rolach, rolą bazy danych i uprawnieniami użytkownika. Zawarte bazy danych nie są obsługiwane i skrypty są SQL Server tylko podzestawu możliwych uprawnień. Więcej informacji znajduje się w dokumencie pomocy technicznej, a skrypt zawiera komentarze ułatwiające zrozumienie tego tematu. |

Zespół inżynierów ds. danych SQL opracował te zasoby. Podstawowym elementem tego zespołu jest odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych na platformę danych Microsoft Azure.

## <a name="videos"></a>Filmy wideo

Zobacz, [jak firma Walgreens](https://www.youtube.com/watch?v=86dhd8N1lH4) zmigrowała swój system magazynów detalicznych z około 100 TB danych z systemu Netezza do Azure Synapse Analytics w rekordowym czasie.

---
title: Modele zakupów
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Poznaj modele zakupów dostępne dla Azure SQL Database i wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: aef29eef7eb53c4cc4ffcc4926f9efe533374178
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319456"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Wybór między modelami zakupów rdzeń wirtualny i DTU — Azure SQL Database i wystąpienie zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL pozwalają łatwo zakupić w pełni zarządzaną infrastrukturę bazy danych platformy jako usługi (PaaS), która spełnia wymagania dotyczące wydajności i kosztów. W zależności od modelu wdrażania, który został wybrany dla Azure SQL Database, można wybrać model zakupu, który działa dla Ciebie:

- [Model zakupu oparty na wirtualnym rdzeniu (rdzeń wirtualny)](service-tiers-vcore.md) (zalecane). Ten model zakupów zapewnia wybór między zainicjowaną warstwą obliczeniową a warstwą obliczeniową bezserwerową. W przypadku alokowanej warstwy obliczeniowej należy wybrać dokładną ilość zasobów obliczeniowych, które są zawsze obsługiwane dla obciążenia. W przypadku warstwy obliczeń bezserwerowych należy określić skalowanie zasobów obliczeniowych w ramach konfigurowalnego zakresu obliczeń. Za pomocą tej warstwy obliczeniowej można również automatycznie wstrzymywać i wznawiać bazę danych w oparciu o aktywność obciążeń. Cena jednostkowa rdzeń wirtualny na jednostkę czasu jest niższa w przypadku alokowanej warstwy obliczeniowej niż w warstwie obliczeniowej bezserwerowej.
- [Model zakupu oparty na jednostkach transakcji bazy danych (DTU)](service-tiers-dtu.md). Ten model zakupów zawiera powiązane pakiety obliczeniowe i magazynowe, które są zrównoważone dla typowych obciążeń.

Istnieją dwa modele zakupów:

- [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md) jest dostępny zarówno dla [Azure SQL Database](sql-database-paas-overview.md) , jak i dla [wystąpienia zarządzanego Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md). [Warstwa usługi do skalowania](service-tier-hyperscale.md) jest dostępna dla pojedynczych baz danych korzystających z [modelu zakupu opartego na rdzeń wirtualny](service-tiers-vcore.md).
- [Model zakupu oparty na](service-tiers-dtu.md) jednostkach DTU jest dostępny dla [Azure SQL Database](single-database-manage.md).

Poniższa tabela i wykres porównują i różnią się w zależności od modelu zakupu opartego na rdzeń wirtualny i DTU:

|**Model zakupów**|**Opis**|**Optymalne zastosowanie**|
|---|---|---|
|Oparty na jednostkach DTU|Ten model jest oparty na podstawie miary zasobów obliczeniowych, magazynu i operacji we/wy. Rozmiary obliczeniowe są wyrażane w jednostkach DTU w przypadku pojedynczych baz danych, a w jednostkach transakcji elastycznej bazy danych (eDTU) w przypadku pul elastycznych. Aby uzyskać więcej informacji na temat jednostek DTU i eDTU, zobacz [Co to są jednostki DTU i eDTU?](purchasing-models.md#dtu-based-purchasing-model).|Klienci, którzy chcą mieć proste, wstępnie skonfigurowane opcje zasobów|
|Rdzeń wirtualny|Ten model umożliwia niezależne wybieranie zasobów obliczeniowych i magazynu. Model zakupu oparty na rdzeniach wirtualnych umożliwia również obniżenie kosztów dzięki [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) dla programu SQL Server.|Klienci, którzy mają elastyczność, kontrolę i przezroczystość|
||||  

![Porównanie modelu cen](./media/purchasing-models/pricing-model.png)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Koszty obliczeń

### <a name="provisioned-compute-costs"></a>Koszty związane z obliczaniem zasobów

W przypadku alokowanej warstwy obliczeniowej koszt obliczeń odzwierciedla łączną pojemność obliczeniową, która jest obsługiwana dla aplikacji.

W warstwie usług Krytyczne dla działania firmy automatycznie przypisujemy co najmniej trzy repliki. Aby odzwierciedlić dodatkową alokację zasobów obliczeniowych, Cena w modelu zakupu opartego na rdzeń wirtualny wynosi około 2,7 razy więcej w Krytyczne dla działania firmyej warstwie usług niż w warstwie usługi Ogólnego przeznaczenia. Podobnie wyższa cena magazynu na GB w warstwie usług Krytyczne dla działania firmy odzwierciedla wyższe limity we/wy i mniejsze opóźnienie magazynu SSD.

Koszt magazynu kopii zapasowych jest taki sam dla warstwy usług Krytyczne dla działania firmy i Ogólnego przeznaczenia warstwy usług, ponieważ w obu warstwach jest używany magazyn standardowy w przypadku kopii zapasowych.

### <a name="serverless-compute-costs"></a>Koszty obliczeń bezserwerowych

Aby uzyskać opis sposobu definiowania pojemności obliczeniowej i obliczania kosztów dla warstwy obliczeń bezserwerowych, zobacz [SQL Database warstwy bezserwerowej](serverless-tier-overview.md).

## <a name="storage-costs"></a>Koszty magazynowania

Różne typy magazynów są rozliczane inaczej. W przypadku magazynu danych jest naliczana opłata za magazyn z zainicjowaną obsługą opartą na wybranej maksymalnej wielkości bazy danych lub puli. Koszt nie zmienia się, o ile nie zostanie zredukowany ani zwiększony maksymalny limit. Magazyn kopii zapasowych jest skojarzony z automatycznymi kopiami zapasowymi wystąpienia i jest przydzielany dynamicznie. Zwiększenie okresu przechowywania kopii zapasowych zwiększa magazyn kopii zapasowych zużywany przez wystąpienie.

Domyślnie siedem dni automatycznego tworzenia kopii zapasowych baz danych jest kopiowane do konta magazynu geograficznie nadmiarowego do odczytu (RA-GRS) w warstwie Standardowa. Ten magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co pięć minut. Rozmiar dzienników transakcji zależy od szybkości zmiany bazy danych. Minimalna wielkość magazynu równa 100% rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. Opłata za dodatkowe użycie magazynu kopii zapasowych jest naliczana w GB miesięcznie.

Aby uzyskać więcej informacji o cenach magazynu, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

Rdzeń wirtualny (rdzeń wirtualny) reprezentuje logiczny procesor CPU i oferuje możliwość wyboru między generacjami sprzętu i fizycznymi cechami sprzętu (na przykład liczby rdzeni, pamięci i rozmiaru magazynu). Model zakupu oparty na rdzeń wirtualny zapewnia elastyczność, kontrolę, przejrzystość poszczególnych zasobów oraz prostą metodę tłumaczenia lokalnych wymagań obciążeń do chmury. Ten model umożliwia wybranie zasobów obliczeniowych, pamięci i magazynu w zależności od potrzeb związanych z obciążeniem.

W modelu zakupu opartego na rdzeń wirtualny można wybrać [ogólnego przeznaczenia](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) i [krytyczne dla działania firmy](high-availability-sla.md#premium-and-business-critical-service-tier-availability) warstwy usług dla SQL Database i wystąpienia zarządzanego SQL.  W przypadku pojedynczych baz danych można również wybrać [warstwę usługi na potrzeby skalowania](service-tier-hyperscale.md).

Model zakupu oparty na rdzeń wirtualny umożliwia niezależne wybieranie zasobów obliczeniowych i magazynowych, dopasowanie wydajności lokalnej i optymalizację cen. W modelu zakupu opartego na rdzeń wirtualny płacisz za:

- Zasoby obliczeniowe (warstwa usług + liczba rdzeni wirtualnych i ilość pamięci + generacja sprzętu).
- Typ i ilość danych oraz magazynu dzienników.
- Magazyn kopii zapasowych (RA-GRS).

> [!IMPORTANT]
> Opłaty za zasoby obliczeniowe, we/wy i magazyn danych i dzienników są naliczone za bazę danych lub pulę elastyczną. Dla każdej bazy danych jest naliczana opłata za magazyn kopii zapasowych. Aby uzyskać więcej informacji na temat opłat za wystąpienia zarządzane przez usługę SQL, zobacz [wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md).
> **Ograniczenia regionu:** Bieżącą listę obsługiwanych regionów można znaleźć w temacie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który obecnie nie jest obsługiwany, [Wyślij żądanie pomocy technicznej za pośrednictwem Azure Portal](quota-increase-request.md).

Jeśli baza danych zużywa ponad 300 DTU, przekonwertowanie na model zakupu oparty na rdzeń wirtualny może obniżyć koszty. Możesz dokonać konwersji przy użyciu dowolnie wybranego interfejsu API lub korzystając z Azure Portal bez przestojów. Jednak konwersja nie jest wymagana i nie jest wykonywana automatycznie. Jeśli model zakupu oparty na jednostkach DTU spełnia Twoje wymagania dotyczące wydajności i działalności biznesowej, należy go nadal używać.

Aby przeprowadzić konwersję z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny, zobacz [Migrowanie z jednostek DTU do rdzeń wirtualny](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>Model zakupu oparty na jednostkach DTU

Jednostka transakcji bazy danych (DTU) reprezentuje przymieszaną miarę procesora CPU, pamięci, odczytów i zapisów. Model zakupów opartych na jednostkach DTU oferuje zestaw wstępnie skonfigurowanych pakietów zasobów obliczeniowych i magazynu z uwzględnieniem różnych poziomów wydajności aplikacji. Jeśli wolisz prostoty wstępnie skonfigurowanego pakietu i Naprawiono płatności w każdym miesiącu, model oparty na jednostkach DTU może być bardziej odpowiedni dla Twoich potrzeb.

W modelu zakupu opartego na jednostkach DTU można wybrać warstwy usług podstawowa, standardowa i Premium dla Azure SQL Database. Model zakupu oparty na jednostkach DTU nie jest dostępny dla wystąpienia zarządzanego Azure SQL.

### <a name="database-transaction-units-dtus"></a>Jednostki transakcji bazy danych (DTU)

W przypadku pojedynczej bazy danych o określonym rozmiarze obliczeń w [warstwie usług](single-database-scale.md)platforma Azure gwarantuje określony poziom zasobów dla tej bazy danych (niezależnie od innej bazy danych w chmurze platformy Azure). Ta gwarancja zapewnia przewidywalny poziom wydajności. Ilość zasobów przydzielonych do bazy danych jest obliczana jako liczba DTU i jest pakietem miary zasobów obliczeniowych, magazynu i operacji we/wy.

Stosunek do tych zasobów jest pierwotnie określony przez [obciążenie porównawcze przetwarzania transakcji online (OLTP)](service-tiers-dtu.md) , zaprojektowane jako typowe dla rzeczywistych obciążeń OLTP. Gdy obciążenie przekracza ilość jednego z tych zasobów, przepustowość jest ograniczana, co powoduje wolniejsze działanie i przekroczenia limitu czasu.

Zasoby używane przez obciążenie nie wpływają na zasoby dostępne dla innych baz danych w chmurze platformy Azure. Podobnie zasoby używane przez inne obciążenia nie wpływają na zasoby dostępne dla bazy danych.

![Pole ograniczenia](./media/purchasing-models/bounding-box.png)

DTU są najbardziej przydatne do poznania zasobów względnych, które są przyłączone do baz danych w różnych rozmiarach obliczeniowych i warstwach usług. Na przykład:

- Podwajanie DTU przez zwiększenie rozmiaru obliczeń bazy danych jest równe Podwajanie zestawu zasobów dostępnych dla tej bazy danych.
- Baza danych P11 usługi w warstwie Premium z 1750 DTU zapewnia 350 razy większą moc obliczeniową jednostek DTU niż podstawowa baza danych warstwy usług z 5 DTU.  

Aby uzyskać dokładniejsze informacje o zużyciu zasobów (DTU), użyj szczegółowych informacji o [wydajności zapytań](query-performance-insight-use.md) , aby:

- Zidentyfikuj najważniejsze zapytania według liczby procesorów/czasu trwania/wykonywania, które mogą być dostrojone w celu zwiększenia wydajności. Na przykład zapytanie intensywnie korzystające z operacji we/wy może skorzystać z [technik optymalizacji w pamięci](../in-memory-oltp-overview.md) , aby lepiej wykorzystać dostępną pamięć w określonej warstwie usług i rozmiarze obliczeniowym.
- Przejdź do szczegółów zapytania, aby wyświetlić jego tekst i historię użycia zasobów.
- Uzyskaj dostęp do zaleceń dotyczących dostrajania wydajności, które pokazują akcje podejmowane przez [SQL Database Advisor](database-advisor-implement-performance-recommendations.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednostki transakcji Elastic Database (jednostek eDTU)

W przypadku baz danych, które są zawsze dostępne, zamiast zapewniać dedykowany zestaw zasobów (DTU), które mogą nie zawsze być potrzebne, można umieścić te bazy danych w [puli elastycznej](elastic-pool-overview.md). Bazy danych w puli elastycznej znajdują się na jednym serwerze i udostępniają pulę zasobów.

Zasoby udostępnione w puli elastycznej są mierzone przez jednostki transakcji Elastic Database (jednostek eDTU). Pule elastyczne zapewniają proste i ekonomiczne rozwiązanie służące do zarządzania celami wydajności dla wielu baz danych, które mają znacznie różne i nieprzewidywalne wzorce użycia. Elastyczna Pula gwarantuje, że wszystkie zasoby nie mogą być używane przez jedną bazę danych w puli, przy jednoczesnym zapewnieniu, że każda baza danych w puli zawsze ma minimalną ilość dostępnych zasobów.

Pula ma określoną liczbę jednostek eDTU dla ustawionej ceny. W puli elastycznej poszczególne bazy danych mogą być automatycznie skalowane w skonfigurowanych granicach. Baza danych w ramach większego obciążenia będzie wykorzystywać więcej jednostek edtuów do zaspokajania popytu. Bazy danych pod jaśniejszymi obciążeniami będą zużywać mniejszą liczbę jednostek eDTU. Bazy danych bez obciążenia nie będą korzystać z jednostek eDTU. Ponieważ zasoby są obsługiwane dla całej puli, a nie dla poszczególnych baz danych, pule elastyczne upraszczają zadania zarządzania i zapewniają przewidywalny budżet dla puli.

Można dodać dodatkowe jednostek eDTU do istniejącej puli bez przestoju bazy danych i bez wpływu na bazy danych w puli. Podobnie, jeśli nie potrzebujesz już dodatkowych jednostek eDTU, usuń je z istniejącej puli w dowolnej chwili. W dowolnym momencie możesz również dodawać bazy danych do baz danych lub odejmować je z puli. Aby zarezerwować jednostek eDTU dla innych baz danych, należy ograniczyć liczbę jednostek eDTU, z których może korzystać baza danych przy dużym obciążeniu. Jeśli baza danych ciągle nie korzysta z zasobów, przenieś ją z puli i skonfiguruj ją jako pojedynczą bazę danych z przewidywalną ilością wymaganych zasobów.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Określanie liczby DTU wymaganych przez obciążenie

Jeśli chcesz przeprowadzić migrację istniejącego lokalnego lub SQL Server obciążenia maszyny wirtualnej do SQL Database, użyj [kalkulatora jednostek DTU](https://dtucalculator.azurewebsites.net/) , aby przybliżyć wymaganą liczbę DTU. W przypadku istniejącego obciążenia SQL Database należy użyć [szczegółowych informacji o wydajności zapytań](query-performance-insight-use.md) , aby zrozumieć użycie zasobów bazy danych (DTU) i uzyskać dokładniejszy wgląd w optymalizację obciążeń. [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamicznym widoku zarządzania (DMV) umożliwia wyświetlenie zużycia zasobów w ciągu ostatniej godziny. Widok wykazu [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) przedstawia użycie zasobów w ciągu ostatnich 14 dni, ale przy niższej wierności średniej z pięciu minut.

### <a name="determine-dtu-utilization"></a>Określanie użycia jednostek DTU

Aby określić średnią wartość procentową wykorzystania jednostek DTU/eDTU względem limitu jednostek DTU/eDTU dla bazy danych lub puli elastycznej, należy użyć następującej formuły:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Wartości wejściowe dla tej formuły można uzyskać z [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) widoków DMV. Innymi słowy, aby określić procent użycia jednostek DTU/eDTU w kierunku liczby jednostek DTU/eDTU dla bazy danych lub puli elastycznej, należy wybrać największą wartość procentową z następujących elementów: `avg_cpu_percent` , `avg_data_io_percent` i `avg_log_write_percent` w danym momencie.

> [!NOTE]
> Limit liczby jednostek DTU bazy danych jest określany przez procesor, odczyty, zapisy i pamięć dla bazy danych. Jednak ponieważ aparat SQL Database zwykle używa całej dostępnej pamięci dla pamięci podręcznej danych w celu zwiększenia wydajności, `avg_memory_usage_percent` wartość zwykle będzie bliska 100%, niezależnie od bieżącego obciążenia bazy danych. W związku z tym nawet jeśli pamięć ma pośredni wpływ na limit jednostek DTU, nie jest używana w formule użycia jednostek DTU.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Obciążenia, które korzystają z elastycznej puli zasobów

Pule są dobrze dostosowane do baz danych o niskim poziomie wykorzystania zasobów i stosunkowo nieczęstego wzrostu użycia. Aby uzyskać więcej informacji, zobacz [kiedy należy wziąć pod uwagę SQL Database puli elastycznej?](elastic-pool-overview.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generacja sprzętowa w modelu zakupu opartego na jednostkach DTU

W modelu zakupu opartego na jednostkach DTU klienci nie mogą wybrać generowania sprzętu używanego dla swoich baz danych. Chociaż dana baza danych jest zwykle w określonym generowaniu sprzętu przez długi czas (zazwyczaj przez wiele miesięcy), istnieją pewne zdarzenia, które mogą spowodować przeniesienie bazy danych do innej generacji sprzętowej.

Na przykład baza danych może zostać przeniesiona na inną generację sprzętu w przypadku skalowania w górę lub w dół do innego celu usługi, lub jeśli Bieżąca infrastruktura w centrum danych zbliża się do limitów pojemności lub jeśli aktualnie używany sprzęt zostanie zlikwidowany z powodu jego końca życia.

Jeśli baza danych jest przenoszona na inny sprzęt, wydajność obciążeń może ulec zmianie. Model DTU gwarantuje, że przepływność i czas odpowiedzi dla obciążenia [porównawczego jednostki DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) pozostaną zasadniczo identyczne, ponieważ baza danych przejdzie do innej generacji sprzętu, o ile jej cel usługi (liczba DTU) pozostaje taki sam.

Jednak w szerokim spektrum obciążeń klientów działających w Azure SQL Database, wpływ używania innego sprzętu dla tego samego celu usługi może być bardziej widoczny. Różne obciążenia będą korzystać z różnych konfiguracji sprzętowych i funkcji. W związku z tym, w przypadku obciążeń innych niż wzorzec DTU, można zobaczyć różnice wydajności, jeśli baza danych przejdzie z jednej generacji sprzętowej na inną.

Na przykład aplikacja, która jest wrażliwa na opóźnienie sieci, może mieć lepszą wydajność na 5 rdzeń sprzęcie a obliczenia z powodu użycia przyspieszonej sieci w 5 rdzeń, ale Aplikacja korzystająca z intensywnie odczytywania we/wy może zobaczyć lepszą wydajność na obliczenia sprzętu i 5 rdzeń ze względu na większy stosunek ilości pamięci na obliczenia.

Klienci z obciążeniami, które są zależne od zmian sprzętu lub klientów, którzy chcą kontrolować wybór generacji sprzętu dla swojej bazy danych, mogą używać modelu [rdzeń wirtualny](service-tiers-vcore.md) do wybierania preferowanych generacji sprzętu podczas tworzenia i skalowania bazy danych. W modelu rdzeń wirtualny, limity zasobów każdego celu usługi na każdym generowaniu sprzętu są udokumentowane w przypadku [pojedynczych baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych](resource-limits-vcore-elastic-pools.md). Aby uzyskać więcej informacji o generacjach sprzętowych w modelu rdzeń wirtualny, zobacz [generacja sprzętu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Czy muszę przełączyć swoją aplikację w tryb offline, aby skonwertować ją z warstwy usług opartych na protokole DTU do warstwy usług opartych na rdzeń wirtualny?

Nie. Nie musisz przełączyć aplikacji do trybu offline. Nowe warstwy usług oferują prostą metodę konwersji w trybie online, która jest podobna do istniejącego procesu uaktualniania baz danych ze standardu do warstwy usługi Premium i odwrotnie. Konwersję można rozpocząć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](single-database-scale.md) i [zarządzanie pulami elastycznymi](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Czy mogę przekonwertować bazę danych z warstwy usługi w modelu zakupu opartego na rdzeń wirtualny na warstwę usługi w modelu zakupu opartym na jednostkach DTU?

Tak. bazę danych można łatwo skonwertować do dowolnego obsługiwanego celu wydajności przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](single-database-scale.md) i [zarządzanie pulami elastycznymi](elastic-pool-overview.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modelu zakupu opartego na rdzeń wirtualny, zobacz [model zakupów oparty na rdzeń wirtualny](service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat modelu zakupu opartego na jednostkach DTU, zobacz [model zakupu oparty na](service-tiers-dtu.md)jednostkach DTU.

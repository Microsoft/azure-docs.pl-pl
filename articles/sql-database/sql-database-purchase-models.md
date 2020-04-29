---
title: Modele zakupów
description: Poznaj modele zakupów dostępne dla Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255992"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Wybierz między rdzeń wirtualny i modelami zakupów jednostek DTU

Azure SQL Database pozwala łatwo zakupić aparat bazy danych w pełni zarządzanej platformy jako usługi (PaaS), który spełnia Twoje wymagania dotyczące wydajności i kosztów. W zależności od modelu wdrażania, który został wybrany dla Azure SQL Database, można wybrać model zakupu, który działa dla Ciebie:

- [Model zakupu oparty na wirtualnym rdzeniu (rdzeń wirtualny)](sql-database-service-tiers-vcore.md) (zalecane). Ten model zakupów zapewnia wybór między zainicjowaną warstwą obliczeniową a warstwą obliczeniową bezserwerową. W przypadku alokowanej warstwy obliczeniowej należy wybrać dokładną ilość zasobów obliczeniowych, które są zawsze obsługiwane dla obciążenia. W przypadku warstwy obliczeń bezserwerowych należy określić skalowanie zasobów obliczeniowych w ramach konfigurowalnego zakresu obliczeń. Za pomocą tej warstwy obliczeniowej można również automatycznie wstrzymywać i wznawiać bazę danych w oparciu o aktywność obciążeń. Cena jednostkowa rdzeń wirtualny na jednostkę czasu jest niższa w przypadku alokowanej warstwy obliczeniowej niż w warstwie obliczeniowej bezserwerowej.
- [Model zakupu oparty na jednostkach transakcji bazy danych (DTU)](sql-database-service-tiers-dtu.md). Ten model zakupów zawiera powiązane pakiety obliczeniowe i magazynowe, które są zrównoważone dla typowych obciążeń.

Różne modele zakupów są dostępne dla różnych Azure SQL Database modeli wdrażania:

- Opcje wdrażania [pojedynczej bazy danych](sql-database-single-databases-manage.md) i [elastycznej puli](sql-database-elastic-pool.md) w [Azure SQL Database](sql-database-technical-overview.md) oferują zarówno [model zakupu oparty](sql-database-service-tiers-dtu.md) na jednostkach DTU, jak i [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).
- Opcja wdrażania [wystąpienia zarządzanego](sql-database-managed-instance.md) w Azure SQL Database oferuje tylko [model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).
- [Warstwa usługi do skalowania](sql-database-service-tier-hyperscale.md) jest dostępna dla pojedynczych baz danych korzystających z [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md).

Poniższa tabela i wykres porównują i różnią się w zależności od modelu zakupu opartego na rdzeń wirtualny i DTU:

|**Model zakupu**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model jest oparty na podstawie miary zasobów obliczeniowych, magazynu i operacji we/wy. Rozmiary obliczeń są wyrażone w DTU dla pojedynczych baz danych i w jednostkach Elastic Database Transaction units (jednostek eDTU) dla pul elastycznych. Aby uzyskać więcej informacji na temat DTU i jednostek eDTU, zobacz [co to są DTU i jednostek eDTU?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Najlepsza dla klientów, którzy chcą mieć proste, wstępnie skonfigurowane opcje zasobów.|
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależne wybieranie zasobów obliczeniowych i magazynu. Model zakupu oparty na rdzeń wirtualny umożliwia również korzystanie z [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów.|Najlepsza dla klientów, którzy mają elastyczność, kontrolę i przezroczystość.|
||||  

![Porównanie modelu cen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Koszty obliczeń

### <a name="provisioned-compute-costs"></a>Koszty związane z obliczaniem zasobów

W przypadku alokowanej warstwy obliczeniowej koszt obliczeń odzwierciedla łączną pojemność obliczeniową, która jest obsługiwana dla aplikacji.

W warstwie usług Krytyczne dla działania firmy automatycznie przypisujemy co najmniej 3 repliki. Aby odzwierciedlić dodatkową alokację zasobów obliczeniowych, Cena w modelu zakupu opartego na rdzeń wirtualny wynosi około 2.7 x więcej w Krytyczne dla działania firmyej warstwie usług niż w warstwie usługi Ogólnego przeznaczenia. Podobnie wyższa cena magazynu na GB w warstwie usług Krytyczne dla działania firmy odzwierciedla wyższe limity we/wy i mniejsze opóźnienie magazynu SSD.

Koszt magazynu kopii zapasowych jest taki sam dla warstwy usług Krytyczne dla działania firmy i Ogólnego przeznaczenia warstwy usług, ponieważ w obu warstwach jest używany magazyn standardowy w przypadku kopii zapasowych.

### <a name="serverless-compute-costs"></a>Koszty obliczeń bezserwerowych

Aby uzyskać opis sposobu definiowania pojemności obliczeniowej i obliczania kosztów dla warstwy obliczeń bezserwerowych, zobacz [SQL Database bezserwerowe](sql-database-serverless.md).

## <a name="storage-costs"></a>Koszty magazynowania

Różne typy magazynów są rozliczane inaczej. W przypadku magazynu danych jest naliczana opłata za magazyn z zainicjowaną obsługą opartą na wybranej maksymalnej wielkości bazy danych lub puli. Koszt nie zmienia się, o ile nie zostanie zredukowany ani zwiększony maksymalny limit. Magazyn kopii zapasowych jest skojarzony z automatycznymi kopiami zapasowymi wystąpienia i jest przydzielany dynamicznie. Zwiększenie okresu przechowywania kopii zapasowych zwiększa magazyn kopii zapasowych zużywany przez wystąpienie.

Domyślnie 7 dni automatycznego tworzenia kopii zapasowych baz danych są kopiowane do konta magazynu geograficznie nadmiarowego do odczytu (RA-GRS) w warstwie Standardowa. Ten magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Rozmiar dzienników transakcji zależy od szybkości zmiany bazy danych. Minimalna wielkość magazynu równa 100% rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. Opłata za dodatkowe użycie magazynu kopii zapasowych jest naliczana w GB miesięcznie.

Aby uzyskać więcej informacji o cenach magazynu, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

Rdzeń wirtualny (rdzeń wirtualny) reprezentuje logiczny procesor CPU i oferuje możliwość wyboru między generacjami sprzętu i fizycznymi cechami sprzętu (na przykład liczby rdzeni, pamięci i rozmiaru magazynu). Model zakupu oparty na rdzeń wirtualny zapewnia elastyczność, kontrolę, przejrzystość poszczególnych zasobów oraz prostą metodę tłumaczenia lokalnych wymagań obciążeń do chmury. Ten model umożliwia wybranie zasobów obliczeniowych, pamięci i magazynu w oparciu o potrzeby związane z obciążeniem.

W modelu zakupu opartego na rdzeń wirtualny można wybrać warstwy usług [ogólnego przeznaczenia](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) i [krytyczne dla działania firmy](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) dla [pojedynczych baz danych](sql-database-single-database-scale.md), [pul elastycznych](sql-database-elastic-pool.md)i [wystąpień zarządzanych](sql-database-managed-instance.md). W przypadku pojedynczych baz danych można również wybrać [warstwę usługi na potrzeby skalowania](sql-database-service-tier-hyperscale.md).

Model zakupu oparty na rdzeń wirtualny umożliwia niezależne wybieranie zasobów obliczeniowych i magazynowych, dopasowanie wydajności lokalnej i optymalizację cen. W modelu zakupu opartego na rdzeń wirtualny płacisz za:

- Zasoby obliczeniowe (warstwa usług + liczba rdzeni wirtualnych i ilość pamięci + generacja sprzętu).
- Typ i ilość danych i magazynu dzienników.
- Magazyn kopii zapasowych (RA-GRS).

> [!IMPORTANT]
> Opłaty za zasoby obliczeniowe, we/wy i magazyn danych i dzienników są naliczone za bazę danych lub pulę elastyczną. Dla każdej bazy danych jest naliczana opłata za magazyn kopii zapasowych. Aby uzyskać więcej informacji na temat opłat za wystąpienia zarządzane, zobacz [wystąpienia zarządzane](sql-database-managed-instance.md).
> **Ograniczenia regionu:** Bieżącą listę obsługiwanych regionów można znaleźć w temacie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który obecnie nie jest obsługiwany, [Wyślij żądanie pomocy technicznej za pośrednictwem Azure Portal](quota-increase-request.md).

Jeśli jedna baza danych lub Pula elastyczna zużywa ponad 300 DTU, przekonwertowanie na model zakupu oparty na rdzeń wirtualny może obniżyć koszty. Możesz dokonać konwersji przy użyciu dowolnie wybranego interfejsu API lub korzystając z Azure Portal bez przestojów. Jednak konwersja nie jest wymagana i nie jest wykonywana automatycznie. Jeśli model zakupu oparty na jednostkach DTU spełnia Twoje wymagania dotyczące wydajności i działalności biznesowej, należy go nadal używać.

Aby przekonwertować model zakupu oparty na jednostkach DTU na model zakupu oparty na rdzeń wirtualny, wybierz rozmiar obliczeń przy użyciu następujących reguł przycisku przewijania:

- Co 100 DTU w warstwie Standardowa wymaga co najmniej 1 rdzeń wirtualny w warstwie usług Ogólnego przeznaczenia.
- Co 125 DTU w warstwie Premium wymaga co najmniej 1 rdzeń wirtualny w warstwie usług Krytyczne dla działania firmy.

> [!NOTE]
> Wskazówki dotyczące ustalania wielkości jednostek DTU do rdzeń wirtualny są przybliżone i są udostępniane w celu uzyskania pomocy w początkowej ocenie docelowego celu usługi bazy danych. Optymalna konfiguracja docelowej bazy danych jest zależna od obciążenia. 
> 
> Osiągnięcie optymalnego współczynnika cen/wydajności może wymagać wykorzystania elastyczności modelu rdzeń wirtualny w celu dostosowania liczby rdzeni wirtualnych, [generowania sprzętu](sql-database-service-tiers-vcore.md#hardware-generations), [usługi](sql-database-service-tiers-vcore.md#service-tiers) i warstw [obliczeniowych](sql-database-service-tiers-vcore.md#compute-tiers) , a także dostrajania innych parametrów konfiguracji bazy danych, takich jak [Maksymalny stopień równoległości](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).

## <a name="dtu-based-purchasing-model"></a>Model zakupu oparty na jednostkach DTU

Jednostka transakcji bazy danych (DTU) reprezentuje przymieszaną miarę procesora CPU, pamięci, odczytów i zapisów. Model zakupów opartych na jednostkach DTU oferuje zestaw wstępnie skonfigurowanych pakietów zasobów obliczeniowych i magazynu z uwzględnieniem różnych poziomów wydajności aplikacji. Jeśli wolisz prostoty wstępnie skonfigurowanego pakietu i Naprawiono płatności w każdym miesiącu, model oparty na jednostkach DTU może być bardziej odpowiedni dla Twoich potrzeb.

W modelu zakupu opartego na jednostkach DTU można wybrać warstwy usług podstawowa, standardowa i Premium dla [pojedynczych baz danych](sql-database-single-database-scale.md) i [pul elastycznych](sql-database-elastic-pool.md). Model zakupu oparty na jednostkach DTU nie jest dostępny dla [wystąpień zarządzanych](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednostki transakcji bazy danych (DTU)

W przypadku pojedynczej bazy danych o określonym rozmiarze obliczeń w ramach [warstwy usług](sql-database-single-database-scale.md)firma Microsoft gwarantuje określony poziom zasobów dla tej bazy danych (niezależnie od innej bazy danych w chmurze platformy Azure). Ta gwarancja zapewnia przewidywalny poziom wydajności. Ilość zasobów przydzielonych do bazy danych jest obliczana jako liczba DTU i jest pakietem miary zasobów obliczeniowych, magazynu i operacji we/wy.

Stosunek do tych zasobów jest pierwotnie określony przez [obciążenie porównawcze przetwarzania transakcji online (OLTP)](sql-database-benchmark-overview.md) , zaprojektowane jako typowe dla rzeczywistych obciążeń OLTP. Gdy obciążenie przekracza ilość jednego z tych zasobów, przepustowość jest ograniczana, co powoduje wolniejsze działanie i przekroczenia limitu czasu.

Zasoby używane przez obciążenie nie wpływają na zasoby dostępne dla innych baz danych SQL w chmurze platformy Azure. Podobnie zasoby używane przez inne obciążenia nie wpływają na zasoby dostępne dla bazy danych SQL.

![pole ograniczenia](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU są najbardziej przydatne do poznania zasobów względnych, które są przyłączone do baz danych Azure SQL w różnych rozmiarach obliczeniowych i warstwach usług. Przykład:

- Podwajanie DTU przez zwiększenie rozmiaru obliczeń bazy danych jest równe Podwajanie zestawu zasobów dostępnych dla tej bazy danych.
- Baza danych P11 usługi w warstwie Premium z 1750 DTU zapewnia większą moc obliczeniową jednostek DTU niż podstawowa baza danych warstwy usług z 5 DTU.  

Aby uzyskać dokładniejsze informacje o zużyciu zasobów (DTU), użyj szczegółowych informacji o [wydajności zapytań](sql-database-query-performance.md) , aby:

- Zidentyfikuj najważniejsze zapytania według liczby procesorów/czasu trwania/wykonywania, które mogą być dostrojone w celu zwiększenia wydajności. Na przykład zapytanie intensywnie korzystające z operacji we/wy może skorzystać z [technik optymalizacji w pamięci](sql-database-in-memory.md) , aby lepiej wykorzystać dostępną pamięć w określonej warstwie usług i rozmiarze obliczeniowym.
- Przejdź do szczegółów zapytania, aby wyświetlić jego tekst i historię użycia zasobów.
- Uzyskaj dostęp do zaleceń dotyczących dostrajania wydajności, które pokazują akcje podejmowane przez [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednostki transakcji Elastic Database (jednostek eDTU)

W przypadku baz danych SQL, które są zawsze dostępne, zamiast zapewniać dedykowany zestaw zasobów (DTU), które mogą nie zawsze być potrzebne, można umieścić te bazy danych w [puli elastycznej](sql-database-elastic-pool.md). Bazy danych w puli elastycznej znajdują się na jednym serwerze Azure SQL Database i udostępniają pulę zasobów.

Zasoby udostępnione w puli elastycznej są mierzone przez jednostki transakcji Elastic Database (jednostek eDTU). Pule elastyczne zapewniają proste i ekonomiczne rozwiązanie służące do zarządzania celami wydajności dla wielu baz danych, które mają znacznie różne i nieprzewidywalne wzorce użycia. Elastyczna Pula gwarantuje, że wszystkie zasoby nie mogą być używane przez jedną bazę danych w puli, przy jednoczesnym zapewnieniu, że każda baza danych w puli zawsze ma minimalną ilość dostępnych zasobów.

Pula ma określoną liczbę jednostek eDTU dla ustawionej ceny. W puli elastycznej poszczególne bazy danych mogą być automatycznie skalowane w skonfigurowanych granicach. Baza danych w ramach większego obciążenia będzie wykorzystywać więcej jednostek edtuów do zaspokajania popytu. Bazy danych pod jaśniejszymi obciążeniami będą zużywać mniejszą liczbę jednostek eDTU. Bazy danych bez obciążenia nie będą korzystać z jednostek eDTU. Ponieważ zasoby są obsługiwane dla całej puli, a nie dla poszczególnych baz danych, pule elastyczne upraszczają zadania zarządzania i zapewniają przewidywalny budżet dla puli.

Można dodać dodatkowe jednostek eDTU do istniejącej puli bez przestoju bazy danych i bez wpływu na bazy danych w puli. Podobnie, jeśli nie potrzebujesz już dodatkowych jednostek eDTU, usuń je z istniejącej puli w dowolnej chwili. W dowolnym momencie możesz również dodawać bazy danych do baz danych lub odejmować je z puli. Aby zarezerwować jednostek eDTU dla innych baz danych, należy ograniczyć liczbę jednostek eDTU, z których może korzystać baza danych przy dużym obciążeniu. Jeśli baza danych ciągle nie korzysta z zasobów, przenieś ją z puli i skonfiguruj ją jako pojedynczą bazę danych z przewidywalną ilością wymaganych zasobów.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Określanie liczby DTU wymaganych przez obciążenie

Jeśli chcesz przeprowadzić migrację istniejącego lokalnego lub SQL Server obciążenia maszyny wirtualnej do Azure SQL Database, użyj [kalkulatora jednostek DTU](https://dtucalculator.azurewebsites.net/) , aby przybliżyć wymaganą liczbę DTU. W przypadku istniejącego obciążenia Azure SQL Database należy użyć [szczegółowych informacji o wydajności zapytań](sql-database-query-performance.md) , aby zrozumieć użycie zasobów bazy danych (DTU) i uzyskać dokładniejszy wgląd w optymalizację obciążeń. Dynamiczny widok zarządzania [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) (DMV) umożliwia wyświetlenie zużycia zasobów w ciągu ostatniej godziny. Widok wykazu [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) przedstawia użycie zasobów w ciągu ostatnich 14 dni, ale przy niższej wierności średniej z pięciu minut.

### <a name="determine-dtu-utilization"></a>Określanie użycia jednostek DTU

Aby określić średnią wartość procentową wykorzystania jednostek DTU/eDTU względem limitu jednostek DTU/eDTU dla bazy danych lub puli elastycznej, należy użyć następującej formuły:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Wartości wejściowe dla tej formuły można uzyskać z [tabeli sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) widoków DMV. Innymi słowy, aby określić procent użycia jednostek DTU/eDTU w kierunku liczby jednostek DTU/eDTU dla bazy danych lub puli elastycznej, należy wybrać największą wartość procentową z następujących elementów: `avg_cpu_percent`, `avg_data_io_percent`i `avg_log_write_percent` w danym momencie.

> [!NOTE]
> Limit liczby jednostek DTU bazy danych jest określany przez procesor, odczyty, zapisy i pamięć dla bazy danych. Jednak ponieważ aparat bazy danych SQL Server zwykle używa całej dostępnej pamięci dla pamięci podręcznej danych w celu zwiększenia wydajności `avg_memory_usage_percent` , wartość zwykle będzie bliska 100% niezależnie od bieżącego obciążenia bazy danych. W związku z tym nawet jeśli pamięć ma pośredni wpływ na limit jednostek DTU, nie jest używana w formule użycia jednostek DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Obciążenia, które korzystają z elastycznej puli zasobów

Pule są dostosowane do baz danych z niską ilością wykorzystania zasobów i stosunkowo nieczęstego wzrostu użycia. Aby uzyskać więcej informacji, zobacz [kiedy należy wziąć pod uwagę SQL Database puli elastycznej?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generacja sprzętowa w modelu zakupu opartego na jednostkach DTU

W modelu zakupu opartego na jednostkach DTU klienci nie mogą wybrać generowania sprzętu używanego dla swoich baz danych. Chociaż dana baza danych jest zwykle w określonym generowaniu sprzętu przez długi czas (zazwyczaj przez wiele miesięcy), istnieją pewne zdarzenia, które mogą spowodować przeniesienie bazy danych do innej generacji sprzętowej.

Na przykład baza danych może zostać przeniesiona na inną generację sprzętu, jeśli jest skalowana w górę lub w dół do innego celu usługi, lub jeśli Bieżąca infrastruktura w centrum danych zbliża się do limitów pojemności lub jeśli aktualnie używany sprzęt zostanie zlikwidowany z powodu jego końca życia.

Jeśli baza danych jest przenoszona na inny sprzęt, wydajność obciążeń może ulec zmianie. Model DTU gwarantuje, że przepływność i czas odpowiedzi dla obciążenia [porównawczego jednostki DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) pozostaną zasadniczo identyczne, ponieważ baza danych przejdzie do innej generacji sprzętu, o ile jej cel usługi (liczba DTU) pozostaje taki sam. 

Jednak w szerokim spektrum obciążeń klientów działających w Azure SQL Database, wpływ używania innego sprzętu dla tego samego celu usługi może być bardziej widoczny. Różne obciążenia będą korzystać z różnych konfiguracji sprzętowych i funkcji. W związku z tym, w przypadku obciążeń innych niż test porównawczy jednostek DTU, można zobaczyć różnice wydajności, jeśli baza danych przejdzie z jednej generacji sprzętowej na inną.

Na przykład aplikacja, która jest wrażliwa na opóźnienie sieci, może mieć lepszą wydajność na 5 rdzeń sprzęcie a obliczenia ze względu na użycie przyspieszonej sieci w 5 rdzeń, ale Aplikacja korzystająca z intensywnie odczytywania we/wy może zobaczyć lepszą wydajność na obliczenia sprzęt a 5 rdzeń ze względu na wyższą proporcje pamięci na obliczenia.

Klienci korzystający z obciążeń, które są wrażliwe na zmiany sprzętu, lub Klienci, którzy chcą kontrolować wybór generacji sprzętu dla swojej bazy danych, mogą używać modelu [rdzeń wirtualny](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) do wybierania preferowanych generacji sprzętu podczas tworzenia i skalowania bazy danych. W modelu rdzeń wirtualny, limity zasobów każdego celu usługi na każdym generowaniu sprzętu są udokumentowane w przypadku [pojedynczych baz danych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) i [pul elastycznych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools). Aby uzyskać więcej informacji o generacjach sprzętowych w modelu rdzeń wirtualny, zobacz [generacja sprzętu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Czy muszę przełączyć swoją aplikację w tryb offline, aby skonwertować ją z warstwy usług opartych na protokole DTU do warstwy usług opartych na rdzeń wirtualny?

Nie. Nie musisz przełączyć aplikacji do trybu offline. Nowe warstwy usług oferują prostą metodę konwersji w trybie online, która jest podobna do istniejącego procesu uaktualniania baz danych ze standardu do warstwy usługi Premium i odwrotnie. Konwersję można rozpocząć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [zarządzanie pulami elastycznymi](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Czy mogę przekonwertować bazę danych z warstwy usługi w modelu zakupu opartego na rdzeń wirtualny na warstwę usługi w modelu zakupu opartym na jednostkach DTU?

Tak. bazę danych można łatwo skonwertować do dowolnego obsługiwanego celu wydajności przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [zarządzanie pulami elastycznymi](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modelu zakupu opartego na rdzeń wirtualny, zobacz [model zakupów oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat modelu zakupu opartego na jednostkach DTU, zobacz [model zakupu oparty na](sql-database-service-tiers-dtu.md)jednostkach DTU.

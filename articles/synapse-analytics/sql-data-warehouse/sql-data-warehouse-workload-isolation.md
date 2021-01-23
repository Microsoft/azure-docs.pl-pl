---
title: Izolacja obciążeń
description: Wskazówki dotyczące ustawiania izolacji obciążeń z grupami obciążeń w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734784"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Izolacja grupy obciążeń usługi Azure Synapse Analytics

W tym artykule wyjaśniono, jak grupy obciążeń mogą służyć do konfigurowania izolacji obciążeń, zawierają zasoby i stosować reguły środowiska uruchomieniowego na potrzeby wykonywania zapytań.

## <a name="workload-groups"></a>Grupy obciążenia

Grupy obciążeń są kontenerami dla zestawu żądań i są podstawą sposobu zarządzania obciążeniami, w tym izolacji obciążeń, w systemie.  Grupy obciążeń są tworzone przy użyciu składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Prosta konfiguracja zarządzania obciążeniami umożliwia zarządzanie obciążeniami danych i zapytaniami użytkowników.  Na przykład grupa obciążeń o nazwie `wgDataLoads` będzie definiować aspekty obciążeń dla danych ładowanych do systemu. Ponadto Grupa obciążeń o nazwie `wgUserQueries` będzie definiować aspekty obciążeń dla użytkowników uruchamiających zapytania w celu odczytu danych z systemu.

W poniższych sekcjach opisano sposób, w jaki grupy obciążeń zapewniają możliwość definiowania izolacji, zawierania, definiowania zasobów żądań i przestrzegania reguł wykonywania.

## <a name="workload-isolation"></a>Izolacja obciążeń

Izolacja obciążenia oznacza, że zasoby są zarezerwowane wyłącznie dla grupy obciążenia.  Izolacja obciążenia jest uzyskiwana przez skonfigurowanie parametru MIN_PERCENTAGE_RESOURCE do wartości większej niż zero w składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  W przypadku obciążeń ciągłego wykonywania, które muszą być zgodne z ścisłą umowy SLA, izolacja gwarantuje, że zasoby są zawsze dostępne dla grupy obciążenia.

Konfigurowanie izolacji obciążeń niejawnie definiuje gwarantowany poziom współbieżności. Na przykład grupa obciążenia z `MIN_PERCENTAGE_RESOURCE` zestawem do 30% i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ustawioną na 2% ma gwarancję 15 współbieżności.  Poziom współbieżności jest gwarantowany, ponieważ 15-2% gniazd zasobów jest zarezerwowanych w grupie obciążenia przez cały czas (bez względu na `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` to, jak jest skonfigurowany).  Jeśli `REQUEST_MAX_RESOURCE_GRANT_PERCENT` wartość jest większa niż `REQUEST_MIN_RESOURCE_GRANT_PERCENT` i `CAP_PERCENTAGE_RESOURCE` jest większa niż `MIN_PERCENTAGE_RESOURCE` Liczba dodatkowych zasobów zostanie dodana dla każdego żądania.  Jeśli `REQUEST_MAX_RESOURCE_GRANT_PERCENT` i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` jest równe i `CAP_PERCENTAGE_RESOURCE` jest większe niż `MIN_PERCENTAGE_RESOURCE` , możliwe jest dodatkowe współbieżność.  Rozważmy poniższą metodę określania gwarantowanej współbieżności:

[Gwarantowane współbieżność] = [ `MIN_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Istnieją określone minimalne wartości poziomu usługi dla min_percentage_resource.  Aby uzyskać więcej informacji, zobacz [efektywne wartości](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values) w celu uzyskania dalszych szczegółów.

W przypadku braku izolacji obciążeń żądania działają w [udostępnionej puli](#shared-pool-resources) zasobów.  Dostęp do zasobów w puli udostępnionej nie jest gwarantowany i jest przypisywany na podstawie [ważności](sql-data-warehouse-workload-importance.md) .

Konfigurowanie izolacji obciążeń powinno odbywać się z zachowaniem ostrożności, ponieważ zasoby są przypisywane do grupy obciążeń, nawet jeśli w grupie obciążenia nie ma aktywnych żądań. Zbyt konfiguracja izolacji może prowadzić do zmniejszenia całkowitego użycia systemu.

Użytkownicy powinni unikać rozwiązania do zarządzania obciążeniami, które konfigurują 100% izolacja obciążenia: 100% zostanie osiągnięty, gdy suma min_percentage_resource skonfigurowana dla wszystkich grup obciążeń równa 100%.  Ten typ konfiguracji jest nadmiernie restrykcyjny i sztywny, pozostawiając nieco wolnego miejsca dla żądań zasobów, które są przypadkowo nieprawidłowo klasyfikowane. Istnieje możliwość zezwalania na wykonywanie jednego żądania z grup obciążeń, które nie są skonfigurowane do izolacji. Zasoby przydzielone do tego żądania będą wyświetlane jako zero w systemach widoków DMV i pożyczą sobie poziom przydziału zasobów z zasobów zarezerwowanych systemowo.

> [!NOTE]
> Aby zapewnić optymalne wykorzystanie zasobów, należy wziąć pod uwagę rozwiązanie do zarządzania obciążeniami, które wykorzystuje pewną izolację, aby zapewnić, że umowy SLA są spełnione i zmieszane z udostępnionymi zasobami, które są dostępne na podstawie [ważności obciążenia](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Zawieranie obciążeń

Zawieranie obciążenia odnosi się do ograniczenia ilości zasobów, które może zużywać Grupa obciążeń.  Zawieranie obciążenia jest osiągane przez skonfigurowanie parametru CAP_PERCENTAGE_RESOURCE na wartość mniejszą niż 100 w składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  .  Rozważmy scenariusz, w którym użytkownicy muszą mieć dostęp do odczytu do systemu, aby można było uruchomić analizę analizy warunkowej za pośrednictwem zapytań ad hoc.  Te typy żądań mogą mieć negatywny wpływ na inne obciążenia działające w systemie.  Skonfigurowanie zawierania gwarantuje, że ilość zasobów jest ograniczona.

Konfigurowanie zawiera niejawnie zdefiniowanie maksymalnego poziomu współbieżności.  Mając CAP_PERCENTAGE_RESOURCE ustawioną na 60% i REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawioną na 1%, do grupy obciążeń jest dozwolony poziom współbieżności 60.  Rozważmy metodę uwzględnioną poniżej w celu określenia maksymalnej współbieżności:

[Maks. współbieżność] = [ `CAP_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Efektywna CAP_PERCENTAGE_RESOURCE grupy obciążeń nie osiągnie 100%, gdy tworzone są grupy obciążeń z MIN_PERCENTAGE_RESOURCE na poziomie większym niż zero.  Zobacz [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby uzyskać efektywne wartości środowiska uruchomieniowego.

## <a name="resources-per-request-definition"></a>Zasoby na definicję żądania

Grupy obciążeń zapewniają mechanizm definiowania minimalnej i maksymalnej ilości zasobów, które są przydzieloną na żądanie za pomocą parametrów REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT w składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Zasoby w tym przypadku to procesor CPU i pamięć.  Skonfigurowanie tych wartości wymusza, ile zasobów i jaki poziom współbieżności można osiągnąć w systemie.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT jest opcjonalnym parametrem, który domyślnie ma tę samą wartość, która jest określona dla REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Tak jak w przypadku wybrania klasy zasobów, skonfigurowanie REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawia wartość zasobów wykorzystywanych przez żądanie.  Ilość zasobów wskazywanych przez wartość zestawu jest gwarantowana do alokacji żądania przed rozpoczęciem wykonywania.  W przypadku klientów migrowania z klas zasobów do grup obciążeń należy wziąć pod uwagę [instrukcje dotyczące sposobu](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) mapowania z klas zasobów do grup obciążeń jako punktu początkowego.

Skonfigurowanie REQUEST_MAX_RESOURCE_GRANT_PERCENT do wartości większej niż REQUEST_MIN_RESOURCE_GRANT_PERCENT pozwala systemowi przydzielić więcej zasobów na żądanie.  Podczas planowania żądania system określa rzeczywistą alokację zasobów do żądania, która jest między REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT, na podstawie dostępności zasobów w puli udostępnionej i bieżącego obciążenia systemu.  Zasoby muszą znajdować się w [udostępnionej puli](#shared-pool-resources) zasobów po zaplanowaniu zapytania.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT mają obowiązujące wartości, które są zależne od obowiązujących wartości MIN_PERCENTAGE_RESOURCE i CAP_PERCENTAGE_RESOURCE.  Zobacz [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby uzyskać efektywne wartości środowiska uruchomieniowego.

## <a name="execution-rules"></a>Reguły wykonywania

W systemach raportowania ad hoc klienci mogą przypadkowo wykonywać zapytania dotyczące przemijających, które poważnie wpływają na produktywność innych.  Administratorzy systemu są zmuszeni do poświęcania czasu na zabijanie zapytań w celu zwolnienia zasobów systemowych.  Grupy obciążeń oferują możliwość skonfigurowania reguły limitu czasu wykonywania zapytania, aby anulować zapytania, które przekroczyły określoną wartość.  Reguła jest konfigurowana przez ustawienie `QUERY_EXECUTION_TIMEOUT_SEC` parametru w składni [Utwórz grupę obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="shared-pool-resources"></a>Zasoby puli udostępnionej

Zasoby puli udostępnionej są zasobami nieskonfigurowanymi do izolacji.  Grupy obciążeń z MIN_PERCENTAGE_RESOURCEm ustawionym na zero wykorzystują zasoby w udostępnionej puli do wykonywania żądań.  Grupy obciążeń o CAP_PERCENTAGE_RESOURCE większej niż MIN_PERCENTAGE_RESOURCE również używane zasoby udostępnione.  Ilość zasobów dostępnych w puli udostępnionej jest obliczana w następujący sposób.

[Pula udostępniona] = 100-[suma dla `MIN_PERCENTAGE_RESOURCE` wszystkich grup obciążeń]

Dostęp do zasobów w puli udostępnionej jest przypisywany na podstawie [ważności](sql-data-warehouse-workload-importance.md) .  Żądania o takim samym poziomie ważności będą uzyskiwać dostęp do zasobów puli udostępnionej przy pierwszej lub pierwszej kolejności.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Konfigurowanie izolacji obciążenia](quickstart-configure-workload-isolation-tsql.md)
- [UTWÓRZ GRUPĘ OBCIĄŻEŃ](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Przekonwertuj klasy zasobów na grupy obciążeń](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitorowanie Portal zarządzania obciążenia](sql-data-warehouse-workload-management-portal-monitor.md).  

---
title: Migrowanie z modelu jednostek DTU do modelu rdzeni wirtualnych
description: Przeprowadź migrację bazy danych w Azure SQL Database z modelu DTU do modelu rdzeń wirtualny. Migrowanie do rdzeń wirtualny jest podobne do uaktualniania lub obniżenia poziomu w warstwach Standardowa i Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: aa236ecaaa9c38c68e66d1813280cd98b85b9463
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790393"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrowanie Azure SQL Database z modelu opartego na jednostkach DTU do modelu opartego na rdzeń wirtualny
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano sposób migrowania bazy danych w Azure SQL Database z [modelu zakupu opartego](service-tiers-dtu.md) na jednostkach DTU do [modelu zakupu opartego na rdzeń wirtualny](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migrowanie bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobne do skalowania między celami usługi w warstwach Podstawowa, standardowa i Premium, o podobnym [czasie trwania](single-database-scale.md#latency) i [minimalnym przestoju](scale-resources.md) na końcu procesu migracji. Bazę danych poddane migracji do modelu zakupu opartego na rdzeń wirtualny można migrować z powrotem do modelu zakupu opartego na jednostkach DTU w dowolnym momencie, z wyjątkiem baz danych migrowanych do warstwy usługi w warstwie górnej [skalowania](service-tier-hyperscale.md) . 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Wybierz warstwę usług rdzeń wirtualny i cel usługi

W przypadku większości jednostek DTU rdzeń wirtualny scenariusze migracji bazy danych i pul elastycznych w warstwach usług podstawowa i Standardowa będą mapowane na [ogólnego przeznaczenia](service-tier-general-purpose.md) warstwy usług. Bazy danych i elastyczne pule w warstwie usług premium będą mapowane na [krytyczne dla działania firmy](service-tier-business-critical.md) warstwy usług. W zależności od scenariusza i wymagań aplikacji warstwa usługi do [skalowania](service-tier-hyperscale.md) może być często używana jako cel migracji dla jednej bazy danych we wszystkich warstwach usługi DTU.

Aby wybrać cel usługi lub rozmiar obliczeń dla zmigrowanej bazy danych w modelu rdzeń wirtualny, można użyć prostej, ale przybliżonej reguły przewijania: co 100 DTU w warstwach Podstawowa lub standardowa wymaga *co najmniej* 1 rdzeń wirtualny, a każdy 125 DTU w warstwie Premium wymaga *co najmniej* 1 rdzeń wirtualny. 

> [!TIP]
> Ta zasada jest przybliżona, ponieważ nie uwzględnia generowania sprzętu używanego dla bazy danych DTU lub elastycznej puli. 

W modelu DTU wszystkie dostępne [generowanie sprzętu](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) mogą być używane dla bazy danych lub puli elastycznej. Dodatkowo istnieje tylko pośrednia kontrola nad liczbą rdzeni wirtualnych (logiczne procesory CPU) przez wybranie wyższych lub mniejszych wartości jednostek DTU lub eDTU. 

Dzięki modelowi rdzeń wirtualny klienci muszą jawnie wybrać zarówno generowanie sprzętu, jak i liczbę rdzeni wirtualnych (logiczne procesory CPU). Model DTU nie oferuje takich opcji, ale generowanie sprzętu i liczba procesorów logicznych używanych dla każdej bazy danych i puli elastycznej są udostępniane za pośrednictwem dynamicznych widoków zarządzania. Dzięki temu można dokładniej określić pasujący cel usługi rdzeń wirtualny. 

Poniższe podejście używa tych informacji do określenia celu usługi rdzeń wirtualny przy użyciu podobnej alokacji zasobów, aby uzyskać podobny poziom wydajności po migracji do modelu rdzeń wirtualny.

### <a name="dtu-to-vcore-mapping"></a>Mapowanie jednostek DTU do rdzeń wirtualny

Poniższe zapytanie T-SQL, gdy jest wykonywane w kontekście bazy danych DTU do migracji, zwróci dopasowanie (prawdopodobnie ułamkowe) liczbę rdzeni wirtualnych w każdym generowaniu sprzętu w modelu rdzeń wirtualny. Przez zaokrąglenie tej liczby do najbliższej liczby rdzeni wirtualnych dostępnych dla [baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych](resource-limits-vcore-elastic-pools.md) w ramach każdej generacji sprzętu w modelu rdzeń wirtualny, klienci mogą wybrać cel usługi rdzeń wirtualny, który jest najbliższym dopasowaniem dla ich bazy danych DTU lub elastycznej puli. 

Przykładowe scenariusze migracji przy użyciu tej metody opisano w sekcji [przykładów](#dtu-to-vcore-migration-examples) .

Wykonaj to zapytanie w kontekście bazy danych, która ma zostać zmigrowana, a nie w `master` bazie danych programu. Podczas migrowania puli elastycznej wykonaj zapytanie w kontekście dowolnych baz danych w puli.

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Dodatkowe czynniki

Oprócz liczby rdzeni wirtualnych (logiczne procesory CPU) i generowania sprzętu niektóre inne czynniki mogą mieć wpływ na wybór celu usługi rdzeń wirtualny:

- Mapowanie zapytania T-SQL dopasowuje cele usługi DTU i rdzeń wirtualny w zakresie wydajności procesora CPU, dlatego wyniki będą bardziej dokładne dla obciążeń związanych z PROCESORem.
- W przypadku tej samej generacji sprzętowej i limitów zasobów rdzeni wirtualnych, liczby operacji we/wy oraz przepływności dziennika transakcji dla baz danych rdzeń wirtualny są często większe niż w przypadku baz danych DTU. W przypadku obciążeń związanych we/wy można obniżyć liczbę rdzeni wirtualnych w modelu rdzeń wirtualny, aby osiągnąć ten sam poziom wydajności. Limity zasobów dla baz danych DTU i rdzeń wirtualny w wartościach bezwzględnych są ujawniane w widoku [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . Porównanie tych wartości między bazą danych jednostek DTU do migracji a bazą danych rdzeń wirtualny przy użyciu przybliżonego celu usługi pomoże Ci dokładniej wybrać cel usługi rdzeń wirtualny.
- Zapytanie mapowania zwraca również ilość pamięci na rdzeń dla bazy danych DTU lub elastycznej puli do migracji oraz dla każdej generacji sprzętu w modelu rdzeń wirtualny. Zapewnienie podobnej lub wyższej całkowitej ilości pamięci po migracji do rdzeń wirtualny jest istotne dla obciążeń wymagających dużej ilości pamięci podręcznej danych w celu osiągnięcia wystarczającej wydajności lub obciążeń, które wymagają dużych przydziałów pamięci do przetwarzania zapytań. W przypadku takich obciążeń, w zależności od rzeczywistej wydajności, może być konieczne zwiększenie liczby rdzeni wirtualnych, aby uzyskać wystarczającą ilość pamięci.
- W przypadku wybrania celu usługi rdzeń wirtualny należy wziąć pod uwagę [historyczne wykorzystanie zasobów](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) bazy danych DTU. Bazy danych DTU ze spójnie używanymi zasobami procesora CPU mogą wymagać mniejszej liczby rdzeni wirtualnych niż zwracana przez zapytanie mapowania. W przypadku baz danych DTU, w których spójne użycie procesora CPU powoduje, że niewystarczająca wydajność obciążeń może wymagać więcej rdzeni wirtualnych niż zwracanych przez zapytanie.
- W przypadku migrowania baz danych z użyciem sporadycznych lub nieprzewidywalnych wzorców użycia należy rozważyć użycie warstwy obliczeń [Bezserwerowych](serverless-tier-overview.md) .  Należy zauważyć, że maksymalna liczba współbieżnych procesów roboczych (żądań) w bezserwerowym wyniesieniu do 75% limitu zainicjowanego obliczeń dla tej samej liczby skonfigurowanych maksymalnych rdzeni wirtualnych.  Ponadto Maksymalna ilość pamięci dostępnej w ramach serwera to 3 GB, a maksymalna liczba skonfigurowanych rdzeni wirtualnych; na przykład Maksymalna pamięć to 120 GB, gdy konfigurowane są maksymalnie 40 rdzeni wirtualnych.   
- W modelu rdzeń wirtualny obsługiwany maksymalny rozmiar bazy danych może się różnić w zależności od generacji sprzętu. W przypadku dużych baz danych sprawdź obsługiwane maksymalne rozmiary w modelu rdzeń wirtualny dla [pojedynczych baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych](resource-limits-vcore-elastic-pools.md).
- W przypadku pul elastycznych modele [jednostek DTU](resource-limits-dtu-elastic-pools.md) i [rdzeń wirtualny](resource-limits-vcore-elastic-pools.md) mają różnice w maksymalnej obsługiwanej liczbie baz danych na pulę. Należy wziąć pod uwagę podczas migrowania pul elastycznych z wieloma bazami danych.
- Niektóre generacje sprzętu mogą nie być dostępne w każdym regionie. Sprawdź dostępność w obszarze [generacja sprzętu](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> Przedstawione powyżej wskazówki dotyczące ustalania wielkości jednostek DTU są udostępniane, aby pomóc w początkowym szacowaniu docelowego celu usługi bazy danych.
>
> Optymalna konfiguracja docelowej bazy danych jest zależna od obciążenia. W związku z tym osiągnięcie optymalnego współczynnika cen/wydajności po migracji może wymagać wykorzystania elastyczności modelu rdzeń wirtualny w celu dostosowania liczby rdzeni wirtualnych, [generacji sprzętu](service-tiers-vcore.md#hardware-generations), [usług](service-tiers-vcore.md#service-tiers) i warstw [obliczeniowych](service-tiers-vcore.md#compute-tiers) , a także dostrajania innych parametrów konfiguracji bazy danych, takich jak [Maksymalny stopień równoległości](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Przykłady migracji jednostek DTU do rdzeń wirtualny

> [!NOTE]
> Wartości w poniższych przykładach są przeznaczone tylko do celów ilustracyjnych. Rzeczywiste wartości zwracane w opisanych scenariuszach mogą być różne.
> 

**Migrowanie standardowej bazy danych S9**

Zapytanie mapowania zwraca następujący wynik (niektóre kolumny nie są wyświetlane dla zwięzłości):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|5 rdzeń|5,40|16,800|7|24,000|5,05|

Widzimy, że baza danych DTU ma 24 logiczne procesory CPU (rdzeni wirtualnych) z 5,4 GB pamięci na rdzeń wirtualny i używa sprzętu 5 rdzeń. Bezpośrednim dopasowaniem do tego jest Ogólnego przeznaczenia 24 Rdzeń wirtualny baza danych na sprzęcie 5 rdzeń, tj. cel usługi **GP_Gen5_24** rdzeń wirtualny.

**Migrowanie standardowej bazy danych S0**

Zapytanie mapowania zwraca następujący wynik (niektóre kolumny nie są wyświetlane dla zwięzłości):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Obliczenia|0,42|0,250|7|0,425|5,05|

Widzimy, że baza danych DTU ma odpowiednik 0,25 logicznych procesorów CPU (rdzeni wirtualnych) z 0,42 GB pamięci na rdzeń wirtualny i używa sprzętu obliczenia. Najmniejsze cele usługi rdzeń wirtualny w generacjach sprzętowych obliczenia i 5 rdzeń, **GP_Gen4_1** i **GP_Gen5_2** zapewniają więcej zasobów obliczeniowych niż standardowa baza danych S0, dlatego nie jest możliwe bezpośrednie dopasowanie. Ponieważ sprzęt obliczenia jest [likwidowany](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/), preferowana jest opcja **GP_Gen5_2** . Ponadto, jeśli obciążenie jest odpowiednie dla warstwy obliczeń [Bezserwerowych](serverless-tier-overview.md) , **GP_S_Gen5_1** byłyby bliżej siebie.

**Migrowanie bazy danych P15 w warstwie Premium**

Zapytanie mapowania zwraca następujący wynik (niektóre kolumny nie są wyświetlane dla zwięzłości):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|5 rdzeń|4,86|29,400|7|42,000|5,05|

Widzimy, że baza danych DTU ma 42 logiczne procesory CPU (rdzeni wirtualnych) z 4,86 GB pamięci na rdzeń wirtualny i używa sprzętu 5 rdzeń. Chociaż nie istnieje cel usługi rdzeń wirtualny z 42 rdzeniami, cel usługi **BC_Gen5_40** jest bardzo blisko siebie pod względem pojemności procesora i pamięci oraz jest dobrym odpowiednikiem.

**Migrowanie puli elastycznej jednostek eDTU w warstwie Podstawowa 200**

Zapytanie mapowania zwraca następujący wynik (niektóre kolumny nie są wyświetlane dla zwięzłości):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|5 rdzeń|5,40|2,800|7|4,000|5,05|

Widzimy, że Pula elastyczna DTU ma 4 procesory logiczne (rdzeni wirtualnych) z 5,4 GB pamięci na rdzeń wirtualny i używa sprzętu 5 rdzeń. Bezpośrednie dopasowanie w modelu rdzeń wirtualny **GP_Gen5_4** jest elastyczną pulą. Jednak ten cel usługi obsługuje maksymalnie 200 baz danych na pulę, podczas 200 gdy elastyczna Pula jednostek eDTU o wartości 1 USD obsługuje do 500 baz danych. Jeśli pula elastyczna, która ma zostać zmigrowana, ma więcej niż 200 baz danych, należy **GP_Gen5_6** pasujący cel usługi rdzeń wirtualny, który obsługuje do 500 baz danych.

## <a name="migrate-geo-replicated-databases"></a>Migrowanie baz danych replikowanych geograficznie

Migracja z modelu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobna do uaktualnienia lub obniżenia poziomu relacji replikacji geograficznej między bazami danych w warstwach usług standardowa i Premium. Podczas migracji nie trzeba zatrzymać replikacji geograficznej, ale należy przestrzegać następujących reguł sekwencjonowania:

- Podczas uaktualniania należy najpierw uaktualnić pomocniczą bazę danych, a następnie uaktualnić podstawową.
- W przypadku zmiany wersji na starszą należy odwrócić kolejność: najpierw należy zmienić podstawową bazę danych, a następnie ponownie obniżyć poziom pomocniczy.

W przypadku korzystania z replikacji geograficznej między dwoma elastycznymi pulami zalecamy wyznaczanie jednej puli jako głównej, a drugiej jako pomocniczej. W takim przypadku podczas migrowania pul elastycznych należy stosować te same wskazówki dotyczące sekwencjonowania. Jeśli jednak istnieją pule elastyczne zawierające podstawowe i pomocnicze bazy danych, należy traktować pulę przy użyciu wyższego użycia jako podstawowego i odpowiednio przestrzegać reguł sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące określonych scenariuszy migracji:

|Bieżąca warstwa usługi|Docelowa warstwa usługi|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa|Zastosowania ogólne|Linię|Można migrować w dowolnej kolejności, ale muszą one zapewnić odpowiednie rozmiary rdzeń wirtualny zgodnie z powyższym opisem|
|Premium|Krytyczne dla działania firmy|Linię|Można migrować w dowolnej kolejności, ale muszą one zapewnić odpowiednie rozmiary rdzeń wirtualny zgodnie z powyższym opisem|
|Standardowa|Krytyczne dla działania firmy|Upgrade|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Standardowa|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Premium|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Zastosowania ogólne|Premium|Upgrade|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Zastosowania ogólne|Krytyczne dla działania firmy|Upgrade|Najpierw należy przeprowadzić migrację pomocniczą|
||||

## <a name="migrate-failover-groups"></a>Migrowanie grup trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga pojedynczej migracji podstawowych i pomocniczych baz danych. W tym procesie obowiązują te same zagadnienia i reguły sekwencjonowania. Po konwersji baz danych do modelu zakupu opartego na rdzeń wirtualny Grupa trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="create-a-geo-replication-secondary-database"></a>Tworzenie pomocniczej bazy danych replikacji geograficznej

Pomocniczą bazę danych replikacji geograficznej (geograficzną) można utworzyć tylko przy użyciu tej samej warstwy usług, która została użyta dla podstawowej bazy danych. W przypadku baz danych o wysokim współczynniku generowania dzienników zalecamy utworzenie pomocniczej lokalizacji geograficznej z tym samym rozmiarem obliczeniowym co podstawowy.

Jeśli tworzysz geograficzną lokację w puli elastycznej dla pojedynczej podstawowej bazy danych, upewnij się, że `maxVCore` ustawienie dla puli odpowiada rozmiarowi obliczeniowemu podstawowej bazy danych. Jeśli tworzysz geograficzną lokację główną w innej puli elastycznej, zalecamy, aby pule miały te same `maxVCore` Ustawienia.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Użyj kopii bazy danych do migrowania z jednostek DTU do rdzeń wirtualny

Możesz skopiować dowolną bazę danych z rozmiarem obliczeń opartym na jednostkach DTU do bazy danych o rozmiarze obliczeń opartym na rdzeń wirtualny bez ograniczeń lub specjalnej sekwencjonowania, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie synchronizuje danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Dla określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md).
- W przypadku określonych rozmiarów obliczeń i opcji rozmiaru magazynu dla pul elastycznych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](resource-limits-vcore-elastic-pools.md).
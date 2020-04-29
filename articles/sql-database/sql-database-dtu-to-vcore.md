---
title: Migrowanie z modelu jednostek DTU do modelu rdzeni wirtualnych
description: Migruj z modelu DTU do modelu rdzeń wirtualny. Migrowanie do rdzeń wirtualny jest podobne do uaktualniania lub obniżenia poziomu w warstwach Standardowa i Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945398"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrowanie z modelu opartego na jednostkach DTU do modelu opartego na rdzeń wirtualny

## <a name="migrate-a-database"></a>Migrowanie bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobne do uaktualnienia lub obniżenia poziomu warstwy usług standardowa i Premium w modelu zakupu opartego na jednostkach DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrowanie baz danych korzystających z replikacji geograficznej

Migracja z modelu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobna do uaktualnienia lub obniżenia poziomu relacji replikacji geograficznej między bazami danych w warstwach usług standardowa i Premium. Podczas migracji nie trzeba zatrzymać replikacji geograficznej, ale należy przestrzegać następujących reguł sekwencjonowania:

- Podczas uaktualniania należy najpierw uaktualnić pomocniczą bazę danych, a następnie uaktualnić podstawową.
- W przypadku zmiany wersji na starszą należy odwrócić kolejność: najpierw należy zmienić podstawową bazę danych, a następnie ponownie obniżyć poziom pomocniczy.

W przypadku korzystania z replikacji geograficznej między dwoma elastycznymi pulami zalecamy wyznaczanie jednej puli jako głównej, a drugiej jako pomocniczej. W takim przypadku podczas migrowania pul elastycznych należy stosować te same wskazówki dotyczące sekwencjonowania. Jeśli jednak istnieją pule elastyczne zawierające podstawowe i pomocnicze bazy danych, należy traktować pulę przy użyciu wyższego użycia jako podstawowego i odpowiednio przestrzegać reguł sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące określonych scenariuszy migracji:

|Bieżąca warstwa usługi|Docelowa warstwa usługi|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa|Zastosowania ogólne|Linię|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary rdzeń wirtualny *|
|Premium|Krytyczne dla działania firmy|Linię|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary rdzeń wirtualny *|
|Standardowa|Krytyczne dla działania firmy|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Standardowa|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Premium|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Zastosowania ogólne|Premium|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Najpierw należy zmigrować podstawowe|
|Zastosowania ogólne|Krytyczne dla działania firmy|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
||||

\*Zgodnie z zasadą kciuka każde 100 DTU w warstwie Standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 DTU w warstwie Premium wymaga co najmniej 1 rdzeń wirtualny. Aby uzyskać więcej informacji, zobacz [model zakupu oparty na rdzeń wirtualny](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrowanie grup trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga pojedynczej migracji podstawowych i pomocniczych baz danych. W tym procesie obowiązują te same zagadnienia i reguły sekwencjonowania. Po konwersji baz danych do modelu zakupu opartego na rdzeń wirtualny Grupa trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="create-a-geo-replication-secondary-database"></a>Tworzenie pomocniczej bazy danych replikacji geograficznej

Pomocniczą bazę danych replikacji geograficznej (geograficzną) można utworzyć tylko przy użyciu tej samej warstwy usług, która została użyta dla podstawowej bazy danych. W przypadku baz danych o wysokim współczynniku generowania dzienników zalecamy utworzenie pomocniczej lokalizacji geograficznej z tym samym rozmiarem obliczeniowym co podstawowy.

Jeśli tworzysz geograficzną lokację w puli elastycznej dla pojedynczej podstawowej bazy danych, upewnij się, że `maxVCore` ustawienie dla puli odpowiada rozmiarowi obliczeniowemu podstawowej bazy danych. Jeśli tworzysz geograficzną lokację główną w innej puli elastycznej, zalecamy, aby pule miały te same `maxVCore` ustawienia.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Użycie kopii bazy danych w celu przekonwertowania bazy danych opartej na jednostkach DTU na bazę danych opartą na rdzeń wirtualny

Możesz skopiować dowolną bazę danych z rozmiarem obliczeń opartym na jednostkach DTU do bazy danych o rozmiarze obliczeń opartym na rdzeń wirtualny bez ograniczeń lub specjalnej sekwencjonowania, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie synchronizuje danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Dla określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- W przypadku określonych rozmiarów obliczeń i opcji rozmiaru magazynu dla pul elastycznych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

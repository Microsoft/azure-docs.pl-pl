---
title: Ręczne inicjowanie trybu failover w wystąpieniu zarządzanym SQL
description: Dowiedz się, jak ręcznie przełączeć podstawowe i pomocnicze repliki w przypadku wystąpienia zarządzanego usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 12/16/2020
ms.openlocfilehash: 4b1c98d8621267b300a82b697bce66a6b94e82f3
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825903"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Zainicjowane przez użytkownika ręczne przejście w tryb failover w usłudze SQL Managed Instance

W tym artykule wyjaśniono, jak ręcznie przetworzyć węzeł podstawowy w warstwach usługi wystąpienia zarządzanego SQL Ogólnego przeznaczenia (GP) i Krytyczne dla działania firmy (BC) oraz jak ręcznie przełączyć pomocniczy węzeł repliki tylko do odczytu w warstwie usługi BC.

## <a name="when-to-use-manual-failover"></a>Kiedy używać ręcznej pracy awaryjnej

[Wysoka dostępność](../database/high-availability-sla.md) to podstawowa część platformy wystąpienia zarządzanego SQL, która działa w sposób przezroczysty dla aplikacji bazy danych. Przełączenie w tryb failover z węzłów głównych do pomocniczych w przypadku obniżenia poziomu lub wykrywania błędów lub w zwykłych comiesięcznych aktualizacjach oprogramowania jest oczekiwanym wystąpieniem dla wszystkich aplikacji korzystających z wystąpienia zarządzanego SQL na platformie Azure.

Rozważ wykonanie [ręcznej pracy awaryjnej](../database/high-availability-sla.md#testing-application-fault-resiliency) w wystąpieniu zarządzanym SQL z następujących powodów:
- Przetestuj aplikację pod kątem odporności trybu failover przed wdrożeniem w środowisku produkcyjnym
- Przetestowanie kompleksowych systemów pod kątem odporności na uszkodzenia na automatycznym przejściu w tryb failover
- Testowanie wpływu trybu failover na istniejące sesje bazy danych
- Sprawdź, czy zmiany w trybie failover mają na celu kompleksową wydajność ze względu na zmiany opóźnienia sieci
- W niektórych przypadkach obniżenia wydajności zapytań ręczne przełączenie w tryb failover może pomóc wyeliminować problem z wydajnością.

> [!NOTE]
> Zagwarantowanie, że aplikacje są odporne na awarie przed wdrożeniem w środowisku produkcyjnym, pomogą ograniczyć ryzyko błędów aplikacji w środowisku produkcyjnym i przyczynić się do dostępności aplikacji dla klientów.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Inicjowanie ręcznego przełączania do trybu failover w wystąpieniu zarządzanym SQL

### <a name="azure-rbac-permissions-required"></a>Wymagane są uprawnienia kontroli RBAC platformy Azure

Użytkownik inicjujący tryb failover musi mieć jedną z następujących ról platformy Azure:

- Rola właściciela subskrypcji lub
- Rola współautora wystąpienia zarządzanego lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Minimalna wersja elementu AZ. SQL musi być równa [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Rozważ użycie [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal, na którym zawsze jest dostępna Najnowsza wersja programu PowerShell. 

Jako wymaganie wstępne Użyj następującego skryptu programu PowerShell, aby zainstalować wymagane moduły platformy Azure. Ponadto wybierz subskrypcję, w której znajduje się wystąpienie zarządzane, które ma zostać przeniesiona do trybu failover.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Użyj polecenia programu PowerShell [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) z następującym przykładem, aby zainicjować tryb failover węzła podstawowego, który ma zastosowanie do warstwy usługi BC i GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Poniższe polecenie PS służy do przełączenia w tryb failover węzła pomocniczego, który ma zastosowanie tylko do warstwy usługi BC.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Korzystanie z interfejsu wiersza polecenia (CLI)

Upewnij się, że zainstalowano najnowsze skrypty interfejsu wiersza polecenia.

Użyj polecenia AZ SQL mi tryb failover CLI, w poniższym przykładzie, aby zainicjować tryb failover węzła podstawowego, który ma zastosowanie do warstwy usługi BC i GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Poniższe polecenie interfejsu wiersza polecenia służy do przełączenia w tryb failover węzła pomocniczego, który ma zastosowanie tylko do warstwy usługi BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Korzystanie z interfejsu API

Użytkownicy zaawansowani, którzy chcą potrzebować zautomatyzować pracę w trybie failover swoich wystąpień zarządzanych przez SQL w celu zaimplementowania potoku ciągłego testowania lub zautomatyzowanego łagodzenia wydajności, ta funkcja może być realizowana poprzez inicjowanie trybu failover za poorednictwem wywołania interfejsu API. Aby uzyskać szczegółowe informacje, zobacz [wystąpienia zarządzane — interfejs API REST trybu failover](/rest/api/sql/managed%20instances%20-%20failover/failover) .

Aby zainicjować tryb failover przy użyciu wywołania interfejsu API REST, najpierw Wygeneruj token uwierzytelniania przy użyciu wybranego klienta interfejsu API. Wygenerowany token uwierzytelniania jest używany jako właściwość autoryzacji w nagłówku żądania interfejsu API i jest obowiązkowy.

Poniższy kod jest przykładem identyfikatora URI interfejsu API do wywołania:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

Następujące właściwości muszą zostać przesłane w wywołaniu interfejsu API:

| **Właściwość interfejsu API** | **Parametr** |
| --- | --- |
| subscriptionId | Identyfikator subskrypcji, do której wdrożono wystąpienie zarządzane |
| resourceGroupName | Grupa zasobów zawierająca wystąpienie zarządzane |
| managedInstanceName | Nazwa wystąpienia zarządzanego |
| Replika | Obowiązkowe (Podstawowa lub ReadableSecondary). Te parametry reprezentują typ repliki do przełączenia w tryb failover: podstawowy lub możliwy do odczytu pomocniczy. Jeśli nie zostanie określony, tryb failover zostanie domyślnie zainicjowany w replice podstawowej. |
| api-version | Wartość statyczna i obecnie musi być "2019-06-01 — wersja zapoznawcza" |

Odpowiedź interfejsu API będzie jedną z następujących dwóch:

- 202 zaakceptowane
- Jeden z błędów żądania 400.

Stan operacji może być śledzony przez przeglądanie odpowiedzi interfejsu API w nagłówkach odpowiedzi. Aby uzyskać więcej informacji, zobacz [stan asynchronicznych operacji platformy Azure](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Monitorowanie trybu failover

Aby monitorować postęp ręcznej pracy awaryjnej zainicjowanej przez użytkownika, wykonaj następujące zapytanie T-SQL w ulubionym kliencie (takim jak SSMS) w wystąpieniu zarządzanym SQL. Spowoduje to odczytanie sys.dm_hadr_fabric_replica_states widoku systemowego i replik raportów dostępnych w wystąpieniu. Odśwież to samo zapytanie po zainicjowaniu ręcznego przełączania do trybu failover.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Przed zainicjowaniem trybu failover dane wyjściowe będą wskazywały bieżącą replikę podstawową w warstwie usługi BC, która zawiera jedną podstawową i trzy serwery pomocnicze w grupie dostępności AlwaysOn. Po wykonaniu przejścia w tryb failover ponowne uruchomienie tego zapytania będzie musiało wskazywać zmianę węzła podstawowego.

Nie będzie można zobaczyć tych samych danych wyjściowych z warstwą usługi GP, jak pokazano powyżej. Wynika to z faktu, że warstwa usługi GP opiera się tylko na jednym węźle. Dane wyjściowe zapytania T-SQL dla warstwy usługi GP będą wyświetlać pojedynczy węzeł tylko przed i po przejściu do trybu failover. Utrata łączności z klientem podczas pracy w trybie failover, zazwyczaj trwające na minutę, oznacza wykonanie w trybie failover.

> [!NOTE]
> Zakończenie procesu pracy w trybie failover (nie jest to rzeczywista krótka niedostępna) może potrwać kilka minut w przypadku obciążeń **o wysokiej intensywności** . Wynika to z faktu, że aparat wystąpienia poświęca wszystkie bieżące transakcje na podstawowym serwerze i przechwytuje je w węźle pomocniczym, przed przejściem do trybu failover.

> [!IMPORTANT]
> Ograniczenia funkcjonalne ręcznego przełączania trybu failover są następujące:
> - Może istnieć jeden (1) tryb failover zainicjowany w tym samym wystąpieniu zarządzanym co **15 minut**.
> - W przypadku wystąpień usługi BC musi istnieć kworum replik dla żądania trybu failover, które ma zostać zaakceptowane.
> - W przypadku wystąpień usługi BC nie można określić, która odczytana replika pomocnicza ma inicjować tryb failover.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wysokiej dostępności wystąpienia zarządzanego o [wysokiej dostępności dla wystąpienia zarządzanego Azure SQL](../database/high-availability-sla.md).
- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).

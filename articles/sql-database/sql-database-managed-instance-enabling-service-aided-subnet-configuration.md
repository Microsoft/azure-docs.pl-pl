---
title: Włączanie konfiguracji podsieci wspomaganej przez usługę dla Azure SQL Database wystąpienia zarządzanego
description: Włączanie konfiguracji podsieci wspomaganej przez usługę dla Azure SQL Database wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533270"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Włączanie konfiguracji podsieci wspomaganej przez usługę dla Azure SQL Database wystąpienia zarządzanego
Konfiguracja podsieci wspomagana przez usługę zapewnia zautomatyzowane zarządzanie konfiguracjami sieci dla podsieci obsługujących wystąpienia zarządzane. Przy użyciu przystawki Zarządzanie podsieciami z obsługą administracyjną usługa jest w stanie Pełna kontrola dostępu do danych (przepływy ruchu TDS), podczas gdy zarządzane wystąpienie jest odpowiedzialne za zapewnienie nieprzerwanego przepływu ruchu zarządzania w celu spełnienia warunków umowy SLA.

Automatycznie skonfigurowane sieciowe grupy zabezpieczeń i reguły tabeli tras są widoczne dla klienta i z adnotacjami z prefiksem _Microsoft. SQL-managedInstances_UseOnly__.

Konfiguracja wspomagana usługi jest włączana automatycznie po włączeniu [delegowania podsieci](../virtual-network/subnet-delegation-overview.md) dla `Microsoft.Sql/managedInstances` dostawcy zasobów.

> [!IMPORTANT] 
> Po włączeniu delegowania podsieci nie można go wyłączyć do momentu usunięcia ostatniego klastra wirtualnego z podsieci. Aby uzyskać więcej informacji na temat usuwania klastra wirtualnego, zobacz następujący [artykuł](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Ponieważ konfiguracja podsieci wspomagana przez usługę jest istotną funkcją do utrzymania umowy SLA, począwszy od 1 maja 2020, nie będzie możliwe wdrożenie wystąpień zarządzanych w podsieciach, które nie są delegowane do dostawcy zasobów wystąpienia zarządzanego. 1 lipca 2020 wszystkie podsieci zawierające wystąpienia zarządzane zostaną automatycznie delegowane do dostawcy zasobów wystąpienia zarządzanego. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Włączanie delegowania podsieci dla nowych wdrożeń
Aby wdrożyć wystąpienie zarządzane w programie do pustej podsieci, musisz delegować ją `Microsoft.Sql/managedInstances` do dostawcy zasobów zgodnie z opisem w [artykule](../virtual-network/manage-subnet-delegation.md). _Należy zwrócić uwagę na to, `Microsoft.DBforPostgreSQL/serversv2` że przywoływany artykuł używa dostawcy zasobów na przykład. Zamiast tego należy użyć `Microsoft.Sql/managedInstances` dostawcy zasobów._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Włączanie delegowania podsieci dla istniejących wdrożeń

Aby włączyć delegowanie podsieci dla istniejącego wdrożenia wystąpienia zarządzanego, należy sprawdzić podsieć sieci wirtualnej, w której została umieszczona. 

Aby uzyskać informacje na ten temat `Virtual network/subnet` , możesz `Overview` sprawdzić w bloku portalu wystąpienia zarządzanego.

Alternatywnie można uruchomić następujące polecenia programu PowerShell, aby dowiedzieć się, jak to zrobić. Zamień **Identyfikator subskrypcji** na identyfikator subskrypcji. Zastąp także **RG-Name nazwą** grupy zasobów dla wystąpienia zarządzanego i Zastąp wartość **mi nazwą** wystąpienia zarządzanego.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Po znalezieniu podsieci wystąpienia zarządzanego należy delegować ją do `Microsoft.Sql/managedInstances` dostawcy zasobów zgodnie z opisem w tym [artykule](../virtual-network/manage-subnet-delegation.md). _Należy zwrócić uwagę na to, `Microsoft.DBforPostgreSQL/serversv2` że przywoływany artykuł używa dostawcy zasobów na przykład. Zamiast tego należy użyć `Microsoft.Sql/managedInstances` dostawcy zasobów._


> [!IMPORTANT]
> Włączenie konfiguracji wspomaganej przez usługę nie powoduje przełączenia w tryb failover ani przerwania łączności z wystąpieniami zarządzanymi, które znajdują się już w podsieci.

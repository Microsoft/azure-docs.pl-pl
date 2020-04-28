---
title: Typy połączeń wystąpienia zarządzanego
description: Informacje o typach połączeń wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73819457"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database typy połączeń wystąpienia zarządzanego

W tym artykule wyjaśniono, jak klienci nawiązują połączenie z Azure SQL Database wystąpieniem zarządzanym w zależności od typu połączenia. Przykłady skryptów do zmiany typów połączeń są podane poniżej wraz z zagadnieniami związanymi z zmianą domyślnych ustawień łączności.

## <a name="connection-types"></a>Typy połączeń

Azure SQL Database wystąpienie zarządzane obsługuje następujące dwa typy połączeń:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych. Aby włączyć łączność przy użyciu przekierowania, należy otworzyć aplet zapory i sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zezwolenia na dostęp na portach 1433 i 11000-11999. Pakiety przejdźą bezpośrednio do bazy danych, w związku z czym istnieją ulepszenia dotyczące opóźnień i wydajności przepływności przy użyciu przekierowania przez serwer proxy.
- **Serwer proxy (domyślnie):** W tym trybie wszystkie połączenia korzystają z składnika bramy proxy. Aby włączyć łączność, należy otworzyć tylko port 1433 dla sieci prywatnych i port 3342 dla połączenia publicznego. Wybranie tego trybu może skutkować większym opóźnieniem i niższą przepływność, w zależności od rodzaju obciążenia. Zdecydowanie zalecamy użycie zasad połączenia przekierowania przez zasady połączenia serwera proxy dla najmniejszego opóźnienia i najwyższej przepływności.

## <a name="redirect-connection-type"></a>Przekieruj typ połączenia

Typ połączenia przekierowania oznacza, że po nawiązaniu sesji protokołu TCP z aparatem SQL sesja klienta uzyskuje docelowy wirtualny adres IP węzła klastra wirtualnego z modułu równoważenia obciążenia. Kolejne pakiety przepływają bezpośrednio do węzła klastra wirtualnego, pomijając bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![redirect. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Typ połączenia przekierowania obecnie działa tylko dla prywatnego punktu końcowego. Niezależnie od ustawienia typu połączenia połączenia przechodzące przez publiczny punkt końcowy byłyby za pomocą serwera proxy.

## <a name="proxy-connection-type"></a>Typ połączenia serwera proxy

Typ połączenia z serwerem proxy oznacza, że sesja TCP zostaje ustanowiona przy użyciu bramy i wszystkie kolejne pakiety przepływają przez nią. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skrypt służący do zmiany ustawień typu połączenia przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższy skrypt programu PowerShell przedstawia sposób zmiany typu połączenia dla wystąpienia zarządzanego w celu przekierowania.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Następne kroki

- [Przywracanie bazy danych do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md)
- Dowiedz się, jak [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym](sql-database-managed-instance-public-endpoint-configure.md)
- Informacje o [architekturze łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md)
---
title: Typy połączeń
titleSuffix: Azure SQL Managed Instance
description: Informacje o typach połączeń wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 6c6774fb462a21e721b19ae53d1d018d780b28ae
ms.sourcegitcommit: 581aaca8956b1717b7bc1c1d7710c782c22e6320
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517324"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Typy połączeń wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak klienci nawiązują połączenie z wystąpieniem zarządzanym usługi Azure SQL w zależności od typu połączenia. Przykłady skryptów do zmiany typów połączeń są podane poniżej wraz z zagadnieniami związanymi z zmianą domyślnych ustawień łączności.

## <a name="connection-types"></a>Typy połączeń

Wystąpienie zarządzane Azure SQL obsługuje następujące dwa typy połączeń:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych. Aby włączyć łączność przy użyciu przekierowania, należy otworzyć aplet zapory i sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zezwolenia na dostęp na portach 1433 i 11000-11999. Pakiety przejdźą bezpośrednio do bazy danych, w związku z czym istnieją ulepszenia dotyczące opóźnień i wydajności przepływności przy użyciu przekierowania przez serwer proxy.
- **Serwer proxy (domyślnie):** W tym trybie wszystkie połączenia korzystają z składnika bramy proxy. Aby włączyć łączność, należy otworzyć tylko port 1433 dla sieci prywatnych i port 3342 dla połączenia publicznego. Wybranie tego trybu może skutkować większym opóźnieniem i niższą przepływność, w zależności od rodzaju obciążenia. Zdecydowanie zalecamy użycie zasad połączenia przekierowania przez zasady połączenia serwera proxy dla najmniejszego opóźnienia i najwyższej przepływności.

## <a name="redirect-connection-type"></a>Przekieruj typ połączenia

W polu Typ połączenia przekierowania po ustanowieniu sesji protokołu TCP dla aparatu SQL sesja klienta uzyskuje docelowy wirtualny adres IP węzła klastra wirtualnego z modułu równoważenia obciążenia. Kolejne pakiety przepływają bezpośrednio do węzła klastra wirtualnego, pomijając bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Typ połączenia przekierowania aktualnie działa tylko dla prywatnego punktu końcowego. Niezależnie od ustawienia typu połączenia połączenia przechodzące przez publiczny punkt końcowy byłyby za pomocą serwera proxy.

## <a name="proxy-connection-type"></a>Typ połączenia serwera proxy

W przypadku typu połączenia serwera proxy sesja TCP zostaje ustanowiona przy użyciu bramy i wszystkie kolejne pakiety przepływają przez nią. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Zmienianie typu połączenia

- **Korzystanie z portalu:** Aby zmienić typ połączenia przy użyciu Azure Portal, Otwórz stronę Virtual Network i użyj ustawienia **Typ połączenia** , aby zmienić typ połączenia i zapisać zmiany.

- **Skrypt służący do zmiany ustawień typu połączenia przy użyciu programu PowerShell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Poniższy skrypt programu PowerShell przedstawia sposób zmiany typu połączenia dla wystąpienia zarządzanego na `Redirect` .

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

- [Przywracanie bazy danych do wystąpienia zarządzanego SQL](restore-sample-database-quickstart.md)
- Dowiedz się, jak [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym SQL](public-endpoint-configure.md)
- Informacje o [architekturze łączności wystąpienia zarządzanego SQL](connectivity-architecture-overview.md)

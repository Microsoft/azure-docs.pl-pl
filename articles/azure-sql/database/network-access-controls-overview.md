---
title: Kontrola dostępu do sieci
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Omówienie sposobu zarządzania dostępem do sieci Azure SQL Database i usługi Azure Synapse Analytics oraz kontrolowania do nich dostępu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460015"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database i kontrola dostępu do sieci w usłudze Azure Synapse Analytics

Po utworzeniu logicznego serwera SQL na podstawie [Azure Portal](single-database-create-quickstart.md) dla Azure SQL Database i usługi Azure Synapse Analytics wynik jest publicznym punktem końcowym w formacie *yourservername.Database.Windows.NET*.

Aby selektywnie zezwolić na dostęp do bazy danych za pośrednictwem publicznego punktu końcowego, można użyć następujących kontroli dostępu do sieci:

- Zezwalaj na usługi platformy Azure: po ustawieniu na włączone inne zasoby w ramach granicy platformy Azure, na przykład maszyna wirtualna platformy Azure, mogą uzyskać dostęp do SQL Database
- Reguły zapory adresów IP: Użyj tej funkcji, aby jawnie zezwolić na połączenia z określonego adresu IP, na przykład z maszyn lokalnych

Możesz również zezwolić na prywatny dostęp do bazy danych z [sieci wirtualnych](../../virtual-network/virtual-networks-overview.md) za pośrednictwem:

- Reguły zapory sieci wirtualnej: Użyj tej funkcji, aby zezwolić na ruch z określonej sieci wirtualnej w ramach granicy platformy Azure
- Link prywatny: Ta funkcja służy do tworzenia prywatnego punktu końcowego dla [logicznego programu SQL Server](logical-servers.md) w ramach określonej sieci wirtualnej

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego SQL**. Aby uzyskać więcej informacji na temat konfiguracji sieci, zobacz [nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL](../managed-instance/connect-application-instance.md) .

Zapoznaj się z poniższym wideo, aby zapoznać się z ogólnymi objaśnieniami tych kontroli dostępu i ich działaniami:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Zezwalaj na usługi platformy Azure

Domyślnie podczas tworzenia nowego serwera logicznego SQL [z Azure Portal](single-database-create-quickstart.md)to ustawienie jest **wyłączone**. To ustawienie pojawia się, gdy łączność jest dozwolona przy użyciu punktu końcowego usługi publicznej.

Możesz również zmienić to ustawienie za pośrednictwem okienka Zapora po utworzeniu logicznego programu SQL Server w następujący sposób.
  
![Zrzut ekranu przedstawiający Zarządzanie zaporą serwera][2]

Po ustawieniu na wartość on serwer zezwala **na** komunikację ze wszystkich zasobów w ramach granicy platformy Azure, która może być niedostępna w ramach subskrypcji.

W wielu przypadkach ustawienie **on** jest bardziej ograniczane niż to, czego chcą klienci. Można ustawić ustawienie na **wyłączone** i zamienić je na bardziej restrykcyjne reguły zapory IP lub reguły zapory sieci wirtualnej. 

Jednak ma to wpływ na następujące funkcje, które są uruchamiane na maszynach wirtualnych platformy Azure, które nie są częścią sieci wirtualnej, a tym samym łączą się z bazą danych za pośrednictwem adresu IP platformy Azure:

### <a name="import-export-service"></a>Importuj usługę eksportu

Usługa Import Export nie działa, gdy ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest **wyłączone**. Można jednak obejść ten problem [, ręcznie uruchamiając sqlpackage.exe z maszyny wirtualnej platformy Azure lub wykonując eksport](./database-import-export-azure-services-off.md) bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

### <a name="data-sync"></a>Synchronizacja danych

Aby **można było korzystać** z funkcji synchronizacji danych z opcją Zezwalaj na **dostęp do usług platformy Azure** , należy utworzyć poszczególne wpisy reguł zapory, aby [dodać adresy IP](firewall-create-server-level-portal-quickstart.md) ze **znacznika usługi SQL** dla regionu, w którym znajduje się baza danych **centrów** .
Dodaj te reguły zapory na poziomie serwera do serwerów, na których znajdują się bazy danych **Hub** i **elementów członkowskich** (które mogą znajdować się w różnych regionach)

Użyj poniższego skryptu programu PowerShell, aby wygenerować adresy IP odpowiadające tagowi usługi SQL dla regionu zachodnie stany USA

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag zwraca globalny zakres dla tagu usługi SQL pomimo określenia parametru lokalizacji. Pamiętaj, aby przefiltrować go do regionu, który hostuje bazę danych centrum używaną przez daną grupę synchronizacji

Zwróć uwagę, że dane wyjściowe skryptu programu PowerShell są w notacji CIDR (Classless Inter-Domain Routing). Należy ją przekonwertować na format początkowy i końcowy adresu IP, używając [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) jak to:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Możesz użyć tego dodatkowego skryptu programu PowerShell, aby przekonwertować wszystkie adresy IP z CIDR na początkowy i końcowy format adresu IP.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Teraz można je dodać jako odrębne reguły zapory, a następnie ustawić opcję **Zezwól usługom platformy Azure na dostęp do serwera**  .

## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP

Zapora oparta na protokole IP to funkcja logicznego programu SQL Server na platformie Azure, która uniemożliwia dostęp do serwera do momentu jawnego [dodawania adresów IP](firewall-create-server-level-portal-quickstart.md) komputerów klienckich.

## <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

Oprócz reguł IP Zapora serwera umożliwia definiowanie *reguł sieci wirtualnej*.  
Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i reguły dotyczące Azure SQL Database](vnet-service-endpoint-rule-overview.md) lub Obejrzyj ten film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologia dotycząca sieci platformy Azure

Należy pamiętać o następujących kwestiach dotyczących sieci platformy Azure, aby poznać reguły zapory sieci wirtualnej

**Sieć wirtualna:** Możesz mieć sieci wirtualne skojarzone z subskrypcją platformy Azure

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure, które są przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe znajdujące się poza siecią wirtualną nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurowano zabezpieczenia, aby zezwolić na dostęp.

**Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. W tym artykule jesteśmy zainteresowani nazwą typu **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera to podsieć wymieniona na liście kontroli dostępu (ACL) serwera. Aby znajdować się na liście kontroli dostępu dla bazy danych w SQL Database, podsieć musi zawierać nazwę typu **Microsoft. SQL** . Reguła sieci wirtualnej instruuje serwer, aby zaakceptował komunikację z każdego węzła znajdującego się w podsieci.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Protokół IP a reguły zapory sieci wirtualnej

Zapora Azure SQL Database umożliwia określenie zakresów adresów IP, z których ma zostać zaakceptowana komunikacja, SQL Database. To podejście jest odpowiednie dla stabilnych adresów IP, które są poza siecią prywatną platformy Azure. Jednak maszyny wirtualne w sieci prywatnej platformy Azure są skonfigurowane przy użyciu *dynamicznych* adresów IP. Dynamiczne adresy IP mogą ulec zmianie po ponownym uruchomieniu maszyny wirtualnej i w wyniku unieważnienia reguły zapory opartej na protokole IP. Folly do określenia dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

To ograniczenie można obejść, uzyskując *statyczny* adres IP dla maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu Azure Portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Jednak podejście ze statycznym adresem IP może być trudne do zarządzania i jest kosztowne, gdy jest wykonywane w odpowiedniej skali.

Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania dostępem z określonej podsieci zawierającej maszyny wirtualne.

> [!NOTE]
> Nie można jeszcze mieć SQL Database w podsieci. Jeśli serwer był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z SQL Database. W takim przypadku maszyny wirtualne mogą komunikować się z SQL Database bez konieczności używania reguł sieci wirtualnej ani reguł adresów IP.

## <a name="private-link"></a>Private Link

Link prywatny umożliwia nawiązanie połączenia z serwerem za pośrednictwem **prywatnego punktu końcowego**. Prywatny punkt końcowy to prywatny adres IP w ramach określonej [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieci.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory adresów IP na poziomie serwera, zobacz [Tworzenie bazy danych w SQL Database](single-database-create-quickstart.md).

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory sieci wirtualnej na poziomie serwera, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](vnet-service-endpoint-rule-overview.md).

- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych w programie SQL Database z aplikacji typu open source lub innych firm, zobacz [przykłady kodu dla klientów z przewodnikiem Szybki Start, aby SQL Database](/previous-versions/azure/ee336282(v=azure.100)).

- Aby uzyskać informacje na temat dodatkowych portów, które mogą być konieczne do otwarcia, zobacz sekcję **SQL Database: zewnątrz i wewnątrz** sekcji [portów powyżej 1433 for ADO.NET 4,5 i SQL Database](adonet-v12-develop-direct-route-ports.md)

- Omówienie łączności Azure SQL Database można znaleźć w temacie [Architektura łączności usługi Azure SQL](connectivity-architecture.md)

- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png

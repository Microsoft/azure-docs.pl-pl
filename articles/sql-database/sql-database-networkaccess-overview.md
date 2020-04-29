---
title: Kontrola dostępu do sieci
description: Omówienie kontroli dostępu do sieci dla Azure SQL Database i magazynu danych w celu zarządzania dostępem oraz konfigurowania jednej lub puli baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8b4ee679b21d904f997f727f5f26275c86acc9c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414413"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database i kontrola dostępu do sieci hurtowni danych

> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **Azure SQL Database wystąpienia zarządzanego**. Aby uzyskać więcej informacji na temat konfiguracji sieci, zobacz [nawiązywanie połączenia z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md) .

Po utworzeniu nowego SQL Server platformy Azure z [Azure Portal](sql-database-single-database-get-started.md), wynik będzie publicznym punktem końcowym w formacie, *yourservername.Database.Windows.NET*.

Aby selektywnie zezwolić na dostęp do SQL Database za pośrednictwem publicznego punktu końcowego, można użyć następujących kontroli dostępu do sieci:
- Zezwalaj na usługi platformy Azure: po ustawieniu na włączone inne zasoby w ramach granicy platformy Azure, na przykład maszyna wirtualna platformy Azure, mogą uzyskać dostęp do SQL Database

- Reguły zapory adresów IP: Użyj tej funkcji, aby jawnie zezwolić na połączenia z określonego adresu IP, na przykład z maszyn lokalnych

Możesz również zezwolić na prywatny dostęp do SQL Database z [sieci wirtualnych](../virtual-network/virtual-networks-overview.md) za pośrednictwem:
- Virtual Network reguł zapory: Użyj tej funkcji, aby zezwolić na ruch z określonego Virtual Network w ramach granicy platformy Azure

- Link prywatny: Ta funkcja służy do tworzenia prywatnego punktu końcowego dla usługi Azure SQL Server w ramach określonego Virtual Network



Zapoznaj się z poniższym wideo, aby zapoznać się z ogólnymi objaśnieniami tych kontroli dostępu i ich działaniami:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Zezwalaj na usługi platformy Azure 
Po utworzeniu nowego SQL Server platformy Azure [z poziomu Azure Portal](sql-database-single-database-get-started.md)to ustawienie nie jest zaznaczone.



Możesz również zmienić to ustawienie za pośrednictwem okienka Zapora po utworzeniu SQL Server platformy Azure w następujący sposób.
  
 ![Zrzut ekranu przedstawiający Zarządzanie zaporą serwera][2]

Po ustawieniu opcji **na** platformie Azure SQL Server umożliwia komunikację ze wszystkich zasobów w ramach granicy platformy Azure, która może być niedostępna w ramach subskrypcji.

W wielu przypadkach ustawienie **on** jest bardziej ograniczane niż to, czego chcą klienci. Mogą chcieć ustawić to ustawienie jako **wyłączone** i zamienić je na bardziej restrykcyjne reguły zapory IP lub Virtual Network reguły zapory. To ma wpływ na następujące funkcje, które są uruchamiane na maszynach wirtualnych platformy Azure, które nie są częścią sieci wirtualnej, a tym samym łączą się z bazą danych SQL za pośrednictwem adresu IP platformy Azure.

### <a name="import-export-service"></a>Importuj usługę eksportu
Usługa Import Export nie działa, gdy ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest **wyłączone**. Można jednak obejść ten problem [, ręcznie uruchamiając program sqlpackage. exe z maszyny wirtualnej platformy Azure lub wykonując eksport](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

### <a name="data-sync"></a>Synchronizacja danych
Aby **można było korzystać**z funkcji synchronizacji danych z opcją Zezwalaj na **dostęp do usług platformy Azure** , należy utworzyć poszczególne wpisy reguł zapory, aby [dodać adresy IP](sql-database-server-level-firewall-rule.md) ze **znacznika usługi SQL** dla regionu, w którym znajduje się baza danych **centrów** .
Dodaj te reguły zapory na poziomie serwera do serwerów logicznych, na których znajdują się bazy danych **Hub** i **elementów członkowskich** (które mogą znajdować się w różnych regionach)

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
> Funkcja Get-AzNetworkServiceTag zwraca globalny zakres dla tagu usługi SQL pomimo określenia parametru Location. Pamiętaj, aby przefiltrować go do regionu, który hostuje bazę danych centrum używaną przez daną grupę synchronizacji

Należy pamiętać, że dane wyjściowe skryptu programu PowerShell są w notacji CIDR (Classless Inter-Domain Routing) i muszą zostać przekonwertowane na format początkowy i końcowy adres IP przy użyciu [Get-IPrangeStartEnd. ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) , jak to
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Wykonaj następujące dodatkowe kroki, aby przekonwertować wszystkie adresy IP z CIDR na początkowy i końcowy format adresu IP.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Teraz można je dodać jako odrębne reguły zapory, a następnie ustawić opcję **Zezwól usługom platformy Azure na dostęp do serwera** .


## <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Zapora oparta na protokole IP to funkcja platformy Azure SQL Server, która zapobiega wszystkim dostępowi do serwera bazy danych, dopóki nie zostaną jawnie [dodane adresy IP](sql-database-server-level-firewall-rule.md) komputerów klienckich.


## <a name="virtual-network-firewall-rules"></a>Virtual Network reguły zapory

Oprócz reguł IP Zapora usługi Azure SQL Server umożliwia definiowanie *reguł sieci wirtualnej*.  
Aby dowiedzieć się więcej, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) lub Obejrzyj ten film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia dotycząca sieci platformy Azure  
Należy pamiętać o następujących kwestiach dotyczących sieci platformy Azure podczas eksplorowania Virtual Network regułami zapory

**Sieć wirtualna:** Możesz mieć sieci wirtualne skojarzone z subskrypcją platformy Azure 

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure, które są przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe znajdujące się poza siecią wirtualną nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurowano zabezpieczenia, aby zezwolić na dostęp.

**Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. W tym artykule interesuje Cię nazwę typu **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera SQL Database to podsieć wymieniona na liście kontroli dostępu (ACL) serwera SQL Database. Aby można było uzyskać listę ACL SQL Database, podsieć musi zawierać nazwę typu **Microsoft. SQL** . Reguła sieci wirtualnej instruuje serwer SQL Database, aby akceptował komunikację z każdego węzła znajdującego się w podsieci.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Reguły zapory dla protokołu IP a Virtual Network

Zapora SQL Server platformy Azure umożliwia określenie zakresów adresów IP, z których ma zostać zaakceptowana komunikacja, SQL Database. To podejście jest odpowiednie dla stabilnych adresów IP, które są poza siecią prywatną platformy Azure. Jednak maszyny wirtualne w sieci prywatnej platformy Azure są skonfigurowane przy użyciu *dynamicznych* adresów IP. Dynamiczne adresy IP mogą ulec zmianie po ponownym uruchomieniu maszyny wirtualnej i w wyniku unieważnienia reguły zapory opartej na protokole IP. Folly do określenia dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

To ograniczenie można obejść, uzyskując *statyczny* adres IP dla maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu Azure Portal](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Jednak podejście ze statycznym adresem IP może być trudne do zarządzania i jest kosztowne, gdy jest wykonywane w odpowiedniej skali. 

Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania dostępem z określonej podsieci zawierającej maszyny wirtualne.

> [!NOTE]
> Nie można jeszcze mieć SQL Database w podsieci. Jeśli serwer Azure SQL Database był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z SQL Database. W takim przypadku maszyny wirtualne mogą komunikować się z SQL Database bez konieczności używania reguł sieci wirtualnej ani reguł adresów IP.

## <a name="private-link"></a>Link prywatny 
Link prywatny umożliwia nawiązanie połączenia z usługą Azure SQL Server za pośrednictwem **prywatnego punktu końcowego**. Prywatny punkt końcowy to prywatny adres IP w ramach określonego [Virtual Network](../virtual-network/virtual-networks-overview.md) i podsieci.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory adresów IP na poziomie serwera, zobacz [Tworzenie bazy danych Azure SQL Database](sql-database-single-database-get-started.md).

- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory sieci wirtualnej na poziomie serwera, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych Azure SQL Database z aplikacji Open Source lub innych firm, zobacz [przykłady kodu dla klientów z przewodnikiem Szybki Start do SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Aby uzyskać informacje na temat dodatkowych portów, które mogą być konieczne do otwarcia, zobacz sekcję **SQL Database: zewnątrz i wewnątrz** sekcji [portów powyżej 1433 for ADO.NET 4,5 i SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Omówienie łączności Azure SQL Database można znaleźć w temacie [Architektura łączności usługi Azure SQL](sql-database-connectivity-architecture.md)

- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png


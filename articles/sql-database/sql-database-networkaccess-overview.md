---
title: Kontrolki dostępu do sieci
description: Omówienie kontroli dostępu do sieci dla usługi Azure SQL Database i hurtowni danych w celu zarządzania dostępem i konfigurowania pojedynczej lub puli bazy danych.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414413"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Kontrolki dostępu do bazy danych i magazynu danych usługi Azure SQL

> [!NOTE]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL Database**. Aby uzyskać więcej informacji na temat konfiguracji sieci, zobacz [łączenie się z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md) .

Podczas tworzenia nowego programu Azure SQL Server z [witryny Azure portal,](sql-database-single-database-get-started.md)wynik jest publiczny punkt końcowy w formacie, *yourservername.database.windows.net*.

Za pomocą następujących formantów dostępu do sieci można selektywnie zezwolić na dostęp do bazy danych SQL za pośrednictwem publicznego punktu końcowego:
- Zezwalaj na usługi platformy Azure: po ustawieniu na on inne zasoby w granicach platformy Azure, na przykład maszyna wirtualna platformy Azure, mogą uzyskiwać dostęp do bazy danych SQL

- Reguły zapory IP: Ta funkcja umożliwia jawne zezwalanie na połączenia z określonego adresu IP, na przykład z komputerów lokalnych

Można również zezwolić na prywatny dostęp do bazy danych SQL z [sieci wirtualnych](../virtual-network/virtual-networks-overview.md) za pośrednictwem:
- Reguły zapory sieci wirtualnej: użyj tej funkcji, aby zezwolić na ruch z określonej sieci wirtualnej w granicach platformy Azure

- Łącze prywatne: użyj tej funkcji do utworzenia prywatnego punktu końcowego dla programu Azure SQL Server w określonej sieci wirtualnej



Zobacz poniższy film, aby uzyskać szczegółowe wyjaśnienie tego poziomu i ich wykonania:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Zezwalaj na usługi platformy Azure 
Podczas tworzenia nowego programu Azure SQL Server [z witryny Azure portal](sql-database-single-database-get-started.md)to ustawienie pozostaje niezaznaczone.



Można również zmienić to ustawienie za pomocą okienka zapory po utworzeniu programu Azure SQL Server w następujący sposób.
  
 ![Zrzut ekranu przedstawiający zarządzanie zaporą serwera][2]

Po ustawieniu **na platformie** Azure SQL Server umożliwia komunikację ze wszystkich zasobów wewnątrz granicy platformy Azure, które mogą lub nie mogą być częścią subskrypcji.

W wielu przypadkach ustawienie **ON** jest bardziej dopuszczalne niż to, czego chce większość klientów. Mogą chcieć ustawić to ustawienie **na OFF** i zastąpić je bardziej restrykcyjnymi regułami zapory IP lub regułami zapory sieci wirtualnej. W ten sposób wpływa na następujące funkcje, które są uruchamiane na maszynach wirtualnych na platformie Azure, które nie są częścią sieci wirtualnej, a tym samym łączą się z bazą danych SQL za pośrednictwem adresu IP platformy Azure.

### <a name="import-export-service"></a>Importuj usługę eksportu
Importuj usługę eksportu nie działa, gdy **zezwalaj na dostęp do usług platformy Azure** jest **ustawiona**na OFF . Jednak można obejść ten [problem, ręcznie uruchamiając sqlpackage.exe z maszyny wirtualnej platformy Azure lub wykonywania eksportu](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

### <a name="data-sync"></a>Synchronizacja danych
Aby użyć funkcji synchronizacji danych z **zezwoleniem na dostęp do usług platformy Azure** ustawionym na **OFF,** należy utworzyć indywidualne wpisy reguł zapory, aby [dodać adresy IP](sql-database-server-level-firewall-rule.md) z **tagu usługi Sql** dla regionu obsługującego bazę danych **centrum.**
Dodaj te reguły zapory na poziomie serwera do serwerów logicznych obsługujących bazy danych **Centrum** i **Członków** (które mogą znajdować się w różnych regionach)

Użyj następującego skryptu programu PowerShell do wygenerowania adresów IP odpowiadających tagowi usługi Sql dla regionu Zachodnie stany USA
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
> Get-AzNetworkServiceTag zwraca globalny zakres tagu usługi SQL pomimo określenia parametru Lokalizacja. Pamiętaj, aby filtrować go do regionu, w którym znajduje się baza danych Centrum używana przez grupę synchronizacji

Należy zauważyć, że dane wyjściowe skryptu programu PowerShell są w notacji CIDR (Classless Inter-Domain Routing) i należy je przekonwertować na format adresu IP start i end przy użyciu [pliku Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) w ten sposób
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Wykonaj następujące dodatkowe kroki, aby przekonwertować wszystkie adresy IP z CIDR na format adresu IP początkowego i końcowego.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Teraz można dodać je jako różne reguły zapory, a następnie ustawić **zezwalaj na dostęp do serwera** usługi Azure, aby uzyskać dostęp do serwera.


## <a name="ip-firewall-rules"></a>Reguły zapory IP
Zapora oparta na adresie IP jest funkcją programu Azure SQL Server, która uniemożliwia dostęp do serwera bazy danych, dopóki jawnie nie [dodasz adresów IP](sql-database-server-level-firewall-rule.md) komputerów klienckich.


## <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

Oprócz reguł IP zapora programu Azure SQL Server umożliwia definiowanie *reguł sieci wirtualnej*.  
Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i reguły dla usługi Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) lub obejrzyj ten klip wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia dotycząca sieci platformy Azure  
Należy pamiętać o następujących terminach usługi Azure Networking podczas eksplorowania reguł zapory sieci wirtualnej

**Sieć wirtualna:** Z subskrypcją platformy Azure mogą być skojarzone sieci wirtualne 

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure (maszyny wirtualne), które zostały przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe, które znajdują się poza siecią wirtualną, nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurujesz zabezpieczenia w taki sposób, aby zezwolić na dostęp.

**Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwy typu usługi Platformy Azure. W tym artykule interesuje nas nazwa typu **Microsoft.Sql**, która odnosi się do usługi Azure o nazwie BAZA DANYCH SQL.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera bazy danych SQL jest podsiecią wymienioną na liście kontroli dostępu (ACL) serwera bazy danych SQL. Aby być na listy ACL bazy danych SQL, podsieć musi zawierać nazwę typu **Microsoft.Sql.** Reguła sieci wirtualnej informuje serwer bazy danych SQL, aby akceptował komunikację z każdego węzła, który znajduje się w podsieci.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Reguły zapory IP i sieci wirtualnej

Zapora programu Azure SQL Server umożliwia określenie zakresów adresów IP, z których komunikacja jest akceptowana do bazy danych SQL. Takie podejście jest dobre dla stabilnych adresów IP, które znajdują się poza siecią prywatną platformy Azure. Jednak maszyny wirtualne (VM) w sieci prywatnej platformy Azure są konfigurowane z *dynamicznymi* adresami IP. Dynamiczne adresy IP mogą ulec zmianie po ponownym uruchomieniu maszyny Wirtualnej i z kolei unieważnić regułę zapory opartej na protokonach IP. Byłoby głupotą określić dynamiczny adres IP w regule zapory w środowisku produkcyjnym.

Można obejść to ograniczenie, uzyskując *statyczny* adres IP dla maszyny Wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu witryny Azure portal](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Jednak statyczne podejście IP może stać się trudne do zarządzania i jest kosztowne, gdy odbywa się na dużą skalę. 

Reguły sieci wirtualnej są łatwiejszą alternatywą do ustanawiania i zarządzania dostępem z określonej podsieci zawierającej maszyny wirtualne.

> [!NOTE]
> Nie można jeszcze mieć bazy danych SQL w podsieci. Jeśli serwer bazy danych SQL platformy Azure był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z bazą danych SQL. W takim przypadku maszyny wirtualne mogą komunikować się z bazą danych SQL bez konieczności konieczności żadnych reguł sieci wirtualnej lub reguł IP.

## <a name="private-link"></a>Link prywatny 
Funkcja Private Link umożliwia łączenie się z programem Azure SQL Server za pośrednictwem **prywatnego punktu końcowego.** Prywatny punkt końcowy to prywatny adres IP w określonej [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) i podsieci.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szybki start dotyczący tworzenia reguły zapory IP na poziomie serwera, zobacz [Tworzenie bazy danych SQL platformy Azure](sql-database-single-database-get-started.md).

- Aby uzyskać szybki start dotyczący tworzenia reguły zapory sieci wirtualnej na poziomie serwera, zobacz [punkty końcowe i reguły usługi sieci wirtualnej dla bazy danych SQL azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych SQL platformy Azure z aplikacji open source lub innych firm, zobacz [Przykłady kodu szybki start klienta do bazy danych SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Aby uzyskać informacje na temat dodatkowych portów, które mogą być potrzebne do otwarcia, zobacz **baza danych SQL: Na zewnątrz vs wewnątrz** sekcji [Porty poza 1433 dla ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

- Aby zapoznać się z omówieniem łączności bazy danych SQL platformy Azure, zobacz [Architektura łączności SQL platformy Azure](sql-database-connectivity-architecture.md)

- Aby zapoznać się z omówieniem zabezpieczeń bazy danych SQL azure, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png


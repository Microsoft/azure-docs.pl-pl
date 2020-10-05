---
title: Link prywatny platformy Azure
description: Omówienie funkcji prywatnego punktu końcowego.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: b0908aee6253a3be486f71c245ea1eee2ff8b9bb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91319473"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Prywatny link do platformy Azure dla Azure SQL Database i usługi Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Link prywatny umożliwia nawiązanie połączenia z różnymi usługami PaaS na platformie Azure za pośrednictwem **prywatnego punktu końcowego**. Aby zapoznać się z listą usług PaaS Services, które obsługują funkcję link prywatny, przejdź do strony z [dokumentacją linku prywatnego](../../private-link/index.yml) . Prywatny punkt końcowy to prywatny adres IP w obrębie określonej sieci [wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieci.

> [!IMPORTANT]
> Ten artykuł dotyczy zarówno Azure SQL Database, jak i usługi Azure Synapse Analytics (dawniej SQL Data Warehouse). Dla uproszczenia termin "baza danych" odnosi się do obu baz danych w Azure SQL Database i Azure Synapse Analytics. Podobnie wszystkie odwołania do "serwer" odwołują się do [logicznego serwera SQL](logical-servers.md) , który hostuje Azure SQL Database i usługi Azure Synapse Analytics. Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Jak skonfigurować link prywatny dla Azure SQL Database 

### <a name="creation-process"></a>Proces tworzenia
Prywatne punkty końcowe można tworzyć przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure:
- [Portal](../../private-link/create-private-endpoint-portal.md)
- [Program PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Interfejs wiersza polecenia](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Proces zatwierdzania
Po utworzeniu przez administratora sieci prywatnego punktu końcowego (PE) administrator SQL może zarządzać połączeniem prywatnego punktu końcowego (PEC) do SQL Database.

1. Przejdź do zasobu serwera w Azure Portal zgodnie z krokami przedstawionymi na poniższym zrzucie ekranu

    - (1) wybierz połączenia prywatnego punktu końcowego w lewym okienku
    - (2) przedstawia listę wszystkich połączeń prywatnych punktów końcowych (PECs)
    - (3) odpowiedni zrzut ekranu, który został utworzony przez prywatny punkt końcowy (PE) ![ dla wszystkich PECs][3]

1. Wybierz z listy indywidualny element PEC, wybierając go.
![Zrzut ekranu — wybrany komputer PEC][6]

1. Administrator SQL może zdecydować się na zatwierdzenie lub odrzucenie PEC i opcjonalnie dodać odpowiedź krótką tekstu.
![Zrzut ekranu przedstawiający zatwierdzenie PEC][4]

1. Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi.
![Zrzut ekranu przedstawiający wszystkie PECs po zatwierdzeniu][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za pośrednictwem prywatnej komunikacji równorzędnej

Gdy klienci łączą się z publicznym punktem końcowym z maszyn lokalnych, ich adresy IP należy dodać do zapory opartej na protokole IP przy użyciu [reguły zapory na poziomie serwera](firewall-create-server-level-portal-quickstart.md). Chociaż ten model działa dobrze, aby umożliwić dostęp do poszczególnych maszyn na potrzeby obciążeń deweloperskich lub testowych, trudno jest zarządzać w środowisku produkcyjnym.

Za pomocą linku prywatnego klienci mogą włączyć dostęp między lokalizacjami do prywatnego punktu końcowego za pomocą [ExpressRoute](../../expressroute/expressroute-introduction.md), prywatnej komunikacji równorzędnej lub TUNELOWANIA sieci VPN. Klienci mogą następnie wyłączyć cały dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokole IP, aby zezwalać na dowolnych adresów IP.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Przypadki użycia prywatnego linku do Azure SQL Database 

Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, równorzędnej sieci wirtualnej w tym samym regionie lub za pośrednictwem sieci wirtualnej z siecią wirtualną w różnych regionach. Ponadto klienci mogą łączyć się z lokalnego przy użyciu ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

 ![Diagram opcji łączności][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Przetestuj łączność z SQL Database z maszyny wirtualnej platformy Azure w tej samej sieci wirtualnej

W tym scenariuszu przyjęto założenie, że utworzono maszynę wirtualną platformy Azure z systemem Windows Server 2016. 

1. [Uruchom sesję pulpit zdalny (RDP) i Połącz się z maszyną wirtualną](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Następnie można wykonać podstawowe testy łączności, aby upewnić się, że maszyna wirtualna nawiązuje połączenie z SQL Database za pośrednictwem prywatnego punktu końcowego przy użyciu następujących narzędzi:
    1. Protokół Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Sprawdź łączność przy użyciu protokołu Telnet

[Klient Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) to funkcja systemu Windows, która może służyć do testowania łączności. W zależności od wersji systemu operacyjnego Windows może być konieczne jawne włączenie tej funkcji. 

Otwórz okno wiersza polecenia po zainstalowaniu programu Telnet. Uruchom polecenie telnet i określ adres IP i prywatny punkt końcowy bazy danych w SQL Database.

```
>telnet 10.1.1.5 1433
```

Po pomyślnym nawiązaniu połączenia Telnet zobaczysz pusty ekran w oknie polecenia, jak na poniższym obrazie:

 ![Diagram programu Telnet][2]

### <a name="check-connectivity-using-psping"></a>Sprawdź łączność przy użyciu Psping

[Psping](/sysinternals/downloads/psping) można użyć w następujący sposób, aby sprawdzić, czy połączenie z prywatnym punktem końcowym (PEC) nasłuchuje połączeń na porcie 1433.

Uruchom psping w następujący sposób, podając nazwę FQDN dla serwera logicznego SQL i portu 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Wynik pokazuje, że Psping może wysłać polecenie ping do prywatnego adresu IP skojarzonego z PEC.

### <a name="check-connectivity-using-nmap"></a>Sprawdź łączność przy użyciu Nmap

Nmap (Maper sieci) to narzędzie bezpłatne i typu "open source" służące do przeprowadzania inspekcji sieci i zabezpieczeń. Aby uzyskać więcej informacji i link do pobierania, odwiedź stronę https://nmap.org . Za pomocą tego narzędzia można upewnić się, że prywatny punkt końcowy nasłuchuje połączeń na porcie 1433.

Uruchom Nmap w następujący sposób, podając zakres adresów podsieci, która hostuje prywatny punkt końcowy.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Wynik pokazuje, że jeden adres IP jest w użyciu. odpowiada adresowi IP dla prywatnego punktu końcowego.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Sprawdź łączność przy użyciu SQL Server Management Studio (SSMS)
> [!NOTE]
> Użyj w **pełni kwalifikowanej nazwy domeny (FQDN)** serwera w parametrach połączenia dla klientów ( `<server>.database.windows.net` ). Wszystkie próby logowania dokonane bezpośrednio na adres IP lub za pomocą prywatnej nazwy FQDN ( `<server>.privatelink.database.windows.net` ) nie powiodą się. To zachowanie jest zgodne z projektem, ponieważ prywatny punkt końcowy kieruje ruch do bramy SQL w regionie i należy określić poprawną nazwę FQDN, aby logowania zakończyły się pomyślnie.

Wykonaj kroki opisane tutaj, aby użyć programu [SSMS do nawiązania połączenia z SQL Database](connect-query-ssms.md). Po nawiązaniu połączenia z SQL Database przy użyciu programu SSMS Sprawdź, czy łączysz się z prywatnego adresu IP maszyny wirtualnej platformy Azure, uruchamiając następujące zapytanie:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Zapobieganie eksfiltracji danych)

Eksfiltracji danych w Azure SQL Database polega na tym, że autoryzowany użytkownik, taki jak administrator bazy danych, może wyodrębnić dane z jednego systemu i przenieść go do innej lokalizacji lub systemu poza organizacją. Na przykład użytkownik przenosi dane na konto magazynu należące do innej firmy.

Zapoznaj się z scenariuszem dla użytkownika z uruchomionym SQL Server Management Studio (SSMS) w ramach maszyny wirtualnej platformy Azure łączącej się z bazą danych w programie SQL Database. Ta baza danych znajduje się w centrum danych w zachodnich stanach USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp za pomocą publicznych punktów końcowych w SQL Database przy użyciu funkcji kontroli dostępu do sieci.

1. Wyłącz cały ruch usługi platformy Azure do SQL Database za pośrednictwem publicznego punktu końcowego przez ustawienie opcji Zezwalaj na **wyłączanie**usług platformy Azure. Upewnij się, że żadne adresy IP nie są dozwolone w regułach zapory na poziomie serwera i bazy danych. Aby uzyskać więcej informacji, zobacz [Azure SQL Database i kontrola dostępu do sieci w usłudze Azure Synapse Analytics](network-access-controls-overview.md).
1. Zezwalanie na ruch do bazy danych w SQL Database przy użyciu prywatnego adresu IP maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły na temat [punktów końcowych usługi](vnet-service-endpoint-rule-overview.md) i [reguł zapory sieci wirtualnej](firewall-configure.md).
1. Na maszynie wirtualnej platformy Azure Zawęź zakres połączenia wychodzącego za pomocą [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/manage-network-security-group.md) i tagów usługi w następujący sposób:
    - Określ regułę sieciowej grupy zabezpieczeń, aby zezwolić na ruch dla programu Service Tag = SQL. Zachodnie — Zezwalanie na połączenie tylko SQL Database w regionie zachodnie stany USA
    - Określ regułę sieciowej grupy zabezpieczeń (z **wyższym priorytetem**), aby odmówić ruchu dla programu Service Tag = odmowa dostępu do SQL Database we wszystkich regionach

Po zakończeniu tej instalacji maszyna wirtualna platformy Azure może łączyć się tylko z bazą danych w SQL Database w regionie zachodnie stany USA. Łączność nie jest jednak ograniczona do pojedynczej bazy danych w SQL Database. Maszyna wirtualna może nadal łączyć się z dowolną bazą danych w regionie zachodnie stany USA, włącznie z bazami danych, które nie są częścią subskrypcji. Chociaż eksfiltracji zakres danych w powyższym scenariuszu do określonego regionu, nie został on całkowicie wyeliminowany.

Za pomocą linku prywatnego klienci mogą teraz konfigurować metody kontroli dostępu do sieci, takie jak sieciowych grup zabezpieczeń, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane na określone prywatne punkty końcowe. Złośliwy tester może uzyskać dostęp tylko do zamapowanego zasobu PaaS (na przykład bazy danych w SQL Database) i bez innego zasobu. 

## <a name="limitations"></a>Ograniczenia 
Połączenia z prywatnym punktem końcowym obsługują tylko **serwer proxy** jako [zasady połączenia](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Łączenie z maszyny wirtualnej platformy Azure w Virtual Network komunikacji równorzędnej 

Skonfiguruj [komunikację równorzędną sieci wirtualnej](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) , aby nawiązać połączenie z SQL Database z maszyny wirtualnej platformy Azure w równorzędnej sieci wirtualnej.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Łączenie z maszyny wirtualnej platformy Azure w sieci wirtualnej ze środowiskiem sieci wirtualnej

Skonfiguruj [połączenie bramy sieci VPN z siecią wirtualną](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) w celu nawiązania połączenia z bazą danych w SQL Database z maszyny wirtualnej platformy Azure w innym regionie lub w ramach subskrypcji.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie ze środowiska lokalnego za pośrednictwem sieci VPN

Aby nawiązać połączenie ze środowiskiem lokalnym z bazą danych w SQL Database, wybierz i zaimplementuj jedną z opcji:
- [Połączenie punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Połączenie sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Obwód usługi ExpressRoute](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Łączenie z usługi Azure Synapse Analytics do usługi Azure Storage przy użyciu instrukcji Base i COPY

Baza i instrukcja COPY są często używane do ładowania danych do usługi Azure Synapse Analytics z kont usługi Azure Storage. Jeśli konto usługi Azure Storage, z którego dane są ładowane, ogranicza dostęp tylko do zestawu podsieci sieci wirtualnej za pośrednictwem prywatnych punktów końcowych, punktów końcowych usługi lub zapór opartych na protokole IP, połączenie z bazą danych i instrukcji COPY do konta zostanie przerwane. Aby umożliwić Importowanie i eksportowanie scenariuszy przy użyciu usługi Azure Synapse Analytics łączącej się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki podane [tutaj](vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](security-overview.md)
- Omówienie łączności Azure SQL Database można znaleźć w temacie [Architektura łączności usługi Azure SQL](connectivity-architecture.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png

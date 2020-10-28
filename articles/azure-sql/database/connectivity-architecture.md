---
title: Azure SQL Database Connectivity Architecture (Architektura łączności usługi Azure SQL Database)
description: W tym dokumencie opisano architekturę Azure SQL Database łączności dla połączeń z bazą danych z platformy Azure lub spoza platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 06/26/2020
ms.openlocfilehash: d0242ceec62db6548d91e5e58c21981a4f0246a0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672500"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL Database i architektura łączności usługi Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W tym artykule opisano architekturę różnych składników, które kierują ruch sieciowy do serwera w Azure SQL Database lub Azure Synapse Analytics. Opisano w nim również różne zasady połączeń i wpływ na klientów nawiązujących połączenie z platformy Azure i klientów nawiązujących połączenie spoza platformy Azure.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL** . Zapoznaj się z [architekturą łączności dla wystąpienia zarządzanego](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Architektura łączności

Poniższy diagram zawiera ogólne omówienie architektury łączności.

![Diagram przedstawiający ogólne omówienie architektury łączności.](./media/connectivity-architecture/connectivity-overview.png)

Poniższe kroki opisują, jak nawiązywane jest połączenie Azure SQL Database:

- Klienci łączą się z bramą, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- Brama, w zależności od obowiązujących zasad połączenia, przekierowuje lub proxy ruchu do odpowiedniego klastra bazy danych.
- Wewnątrz ruchu klastra bazy danych jest przekazywany do odpowiedniej bazy danych.

## <a name="connection-policy"></a>Zasady połączenia

Serwery w SQL Database i Azure Synapse obsługują następujące trzy opcje dla ustawienia zasad połączenia serwera:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych, co prowadzi do zredukowania opóźnień i zwiększonej przepływności. W przypadku połączeń do korzystania z tego trybu klienci muszą:
  - Zezwalaj na komunikację wychodzącą od klienta do wszystkich adresów IP usługi Azure SQL w regionie na portach z zakresu 11000 11999. Użyj tagów usługi dla SQL, aby ułatwić zarządzanie nimi.  
  - Zezwalaj na komunikację wychodzącą od klienta do Azure SQL Database adresów IP bramy na porcie 1433.

- **Serwer proxy:** W tym trybie wszystkie połączenia są nawiązywane za pośrednictwem bram Azure SQL Database, co prowadzi do zwiększonego opóźnienia i ograniczonej przepływności. W przypadku połączeń do korzystania z tego trybu klienci muszą zezwalać na komunikację wychodzącą od klienta do Azure SQL Database adresów IP bramy na porcie 1433.

- **Wartość domyślna:** Jest to zasada połączenia obowiązująca na wszystkich serwerach po utworzeniu, chyba że jawnie zmienisz zasady połączenia na `Proxy` lub `Redirect` . Zasady domyślne są `Redirect` przeznaczone dla wszystkich połączeń klientów pochodzących z platformy Azure (na przykład z maszyny wirtualnej platformy Azure) i `Proxy` dla wszystkich połączeń klientów pochodzących z zewnątrz (na przykład połączeń z lokalnej stacji roboczej).

Zdecydowanie zalecamy stosowanie `Redirect` zasad połączenia za pośrednictwem `Proxy` zasad połączenia dla najniższych opóźnień i najwyższej przepływności. Należy jednak spełnić dodatkowe wymagania dotyczące zezwalania na ruch sieciowy opisany powyżej. Jeśli klient jest maszyną wirtualną platformy Azure, możesz to zrobić za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z [tagami usług](../../virtual-network/network-security-groups-overview.md#service-tags). Jeśli klient nawiązuje połączenie z lokalnej stacji roboczej, może być konieczne skontaktowanie się z administratorem sieci w celu zezwolenia na ruch sieciowy za pośrednictwem firmowej zapory.

## <a name="connectivity-from-within-azure"></a>Łączność z poziomu platformy Azure

W przypadku łączenia się z poziomu platformy Azure połączenia mają domyślnie przypisanych zasad połączenia `Redirect` . Zasady oznacza, `Redirect` że po ustanowieniu sesji protokołu TCP do Azure SQL Database, sesja klienta zostanie przekierowana do odpowiedniego klastra bazy danych ze zmianą docelowego wirtualnego adresu IP z bramy Azure SQL Database do klastra. Następnie wszystkie kolejne pakiety przepływają bezpośrednio do klastra, pomijając Azure SQL Database bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność spoza platformy Azure

Jeśli łączysz się z spoza systemu Azure, połączenia mają domyślnie zasady połączeń `Proxy` . Zasady `Proxy` oznacza, że sesja protokołu TCP jest ustanawiana za pośrednictwem bramy Azure SQL Database i wszystkie kolejne pakiety będą przepływać przez bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Diagram przedstawiający sposób ustanawiania sesji protokołu TCP za pośrednictwem bramy Azure SQL Database i wszystkie kolejne pakiety są przesyłane przez bramę.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Dodatkowo Otwórz porty TCP 1434 i 14000-14999, aby włączyć [łączenie z programem DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Adresy IP bramy

W poniższej tabeli przedstawiono adresy IP bram według regionów. Aby nawiązać połączenie z usługą SQL Database lub Azure Synapse, musisz zezwolić na ruch sieciowy do i ze **wszystkich** bram dla regionu.

Szczegóły dotyczące sposobu migrowania ruchu do nowych bram w określonych regionach znajdują się w następującym artykule: [Azure SQL Database migracji ruchu do nowszych bram](gateway-migration.md)

| Nazwa regionu          | Adresy IP bramy |
| --- | --- |
| Australia Środkowa    | 20.36.105.0 |
| Australia Central2   | 20.36.113.0 |
| Australia Wschodnia       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australia Południowo-Wschodnia | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brazil South         | 104.41.11.5, 191.233.200.14 |
| Kanada Środkowa       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kanada Wschodnia          | 40.86.226.166, 52.242.30.154 |
| Środkowe stany USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 |
| Chiny Wschodnie           | 139.219.130.35     |
| Chiny Wschodnie 2         | 40.73.82.1         |
| Chiny Północne          | 139.219.15.17      |
| Chiny Północne 2        | 40.73.50.0         |
| Azja Wschodnia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| East US              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Wschodnie stany USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Francja Środkowa       | 40.79.137.0, 40.79.129.1 |
| Niemcy Środkowe      | 51.4.144.100       |
| Niemcy Północne wschód   | 51.5.144.179       |
| Niemcy Środkowo-Zachodnie | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Indie Środkowe        | 104.211.96.159     |
| Indie Południowe          | 104.211.224.146    |
| Indie Zachodnie           | 104.211.160.80     |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Japonia Zachodnia           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Korea Środkowa        | 52.231.32.42       |
| Korea Południowa          | 52.231.200.86      |
| Północno-środkowe stany USA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa Północna         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Norwegia Wschodnia          | 51.120.96.0        |
| Norwegia Zachodnia          | 51.120.216.0       |
| Północna Republika Południowej Afryki   | 102.133.152.0, 102.133.120.2       |
| Zachodnia Republika Południowej Afryki    | 102.133.24.0       |
| South Central US     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Azja Południowo-Wschodnia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Szwajcaria Północna    | 51.107.56.0, 51.107.57.0 |
| Szwajcaria Zachodnia     | 51.107.152.0, 51.107.153.0 |
| Środkowy Zjednoczone Emiraty Arabskie          | 20.37.72.64        |
| Północne Zjednoczone Emiraty Arabskie            | 65.52.248.0        |
| Południowe Zjednoczone Królestwo             | 51.140.184.11, 51.105.64.0 |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11        |
| Zachodnio-środkowe stany USA      | 13.78.145.25, 13.78.248.43        |
| West Europe          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| Zachodnie stany USA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Zachodnie stany USA 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat zmiany zasad połączenia Azure SQL Database dla serwera, zobacz Connection [-Policy](/cli/azure/sql/server/conn-policy).
- Aby uzyskać informacje dotyczące zachowania Azure SQL Database połączenia dla klientów korzystających z ADO.NET 4,5 lub nowszej wersji, zobacz [porty wykraczające poza 1433 dla ADO.NET 4,5](adonet-v12-develop-direct-route-ports.md).
- Ogólne informacje dotyczące opracowywania aplikacji można znaleźć w temacie [Omówienie tworzenia aplikacji SQL Database](develop-overview.md).
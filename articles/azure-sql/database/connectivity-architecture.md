---
title: Azure SQL Database architektury łączności
description: W tym dokumencie wyjaśniono Azure SQL Database łączności dla połączeń z bazą danych z platformy Azure lub spoza platformy Azure.
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
ms.date: 01/25/2021
ms.openlocfilehash: 3442e3003ef8a299beb88cd212602c8713915474
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499958"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Architektura łączności usług Azure SQL Database i Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W tym artykule wyjaśniono architekturę różnych składników, które kierują ruch sieciowy do serwera w Azure SQL Database lub Azure Synapse Analytics. Opisano w nim również różne zasady połączeń i ich wpływ na klientów łączących się z poziomu platformy Azure i klientów łączących się spoza platformy Azure.

> [!IMPORTANT]
> Ten artykuł nie *dotyczy* Azure SQL Managed Instance **.** Zapoznaj się z [tematem Connectivity architecture for a managed instance (Architektura łączności dla wystąpienia zarządzanego).](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Architektura łączności

Na poniższym diagramie przedstawiono ogólne omówienie architektury łączności.

![Diagram przedstawiający ogólny przegląd architektury łączności.](./media/connectivity-architecture/connectivity-overview.png)

W poniższych krokach opisano sposób nawiązynia połączenia w Azure SQL Database:

- Klienci łączą się z bramą, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- Brama, w zależności od efektywnych zasad połączenia, przekierowuje lub serwer proxy ruchu do odpowiedniego klastra bazy danych.
- Wewnątrz klastra bazy danych ruch jest przesyłany do odpowiedniej bazy danych.

## <a name="connection-policy"></a>Zasady połączeń

Serwery w SQL Database i Azure Synapse obsługują następujące trzy opcje ustawienia zasad połączenia serwera:

- **Przekierowanie (zalecane):** Klienci ustanawiają połączenia bezpośrednio z węzłem hostującym bazę danych, co prowadzi do zmniejszenia opóźnienia i zwiększenia przepływności. Aby połączenia używać tego trybu, klienci muszą:
  - Zezwalaj na komunikację wychodzącą od klienta do wszystkich Azure SQL IP w regionie na portach w zakresie 11000 11999. Użyj tagów usługi dla języka SQL, aby ułatwić zarządzanie nimi.  
  - Zezwalaj na komunikację wychodzącą od klienta do Azure SQL Database IP bramy na porcie 1433.

- **Serwer proxy:** W tym trybie wszystkie połączenia są proxied za pośrednictwem bram Azure SQL Database, co prowadzi do zwiększenia opóźnienia i zmniejszenia przepływności. Aby połączenia korzystać z tego trybu, klienci muszą zezwolić na komunikację wychodzącą od klienta do Azure SQL Database ip bramy na porcie 1433.

- **Ustawienie domyślne:** Są to zasady połączenia, które są obowiązywać na wszystkich serwerach po utworzeniu, chyba że jawnie zmienisz zasady połączenia na `Proxy` lub `Redirect` . Zasady domyślne są dla wszystkich połączeń klienckich pochodzących z platformy Azure (na przykład z maszyny wirtualnej platformy Azure) i dla wszystkich połączeń klienckich pochodzących spoza firmy (na przykład połączeń z lokalnej stacji `Redirect` `Proxy` roboczej).

Zdecydowanie zalecamy stosowanie zasad `Redirect` połączenia za pośrednictwem zasad `Proxy` połączenia w celu najmniejszego opóźnienia i najwyższej przepływności. Należy jednak spełnić dodatkowe wymagania dotyczące zezwalania na ruch sieciowy, jak opisano powyżej. Jeśli klient jest maszyną wirtualną platformy Azure, można to zrobić przy użyciu sieciowych grup zabezpieczeń z [tagami usługi.](../../virtual-network/network-security-groups-overview.md#service-tags) Jeśli klient łączy się ze stacji roboczej lokalnie, być może trzeba będzie współpracować z administratorem sieci, aby zezwolić na ruch sieciowy przez zaporę firmową.

## <a name="connectivity-from-within-azure"></a>Łączność z poziomu platformy Azure

Jeśli łączysz się z poziomu platformy Azure, połączenia mają domyślnie `Redirect` zasady połączenia. Zasady programu oznaczają, że po nawiąźle sesji TCP z usługą Azure SQL Database sesja klienta jest przekierowywana do odpowiedniego klastra bazy danych ze zmianą docelowego wirtualnego adresu IP z bramy Azure SQL Database do tego `Redirect` klastra. Następnie wszystkie kolejne pakiety przepływa bezpośrednio do klastra, pomijając Azure SQL Database bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![omówienie architektury](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność spoza platformy Azure

Jeśli łączysz się spoza platformy Azure, połączenia mają domyślnie `Proxy` zasady połączenia. Zasada oznacza, że sesja TCP jest ustanowiona za pośrednictwem bramy Azure SQL Database, a wszystkie kolejne pakiety `Proxy` przepływa za pośrednictwem bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Diagram przedstawiający sposób, w jaki sesja TCP jest ustanowiona za pośrednictwem Azure SQL Database i przepływu wszystkich kolejnych pakietów za pośrednictwem bramy.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Ponadto otwórz porty TCP 1434 i 14000–14999, aby włączyć [łączenie z usługą DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Adresy IP bramy

W poniższej tabeli wymieniono adresy IP bram według regionów. Aby nawiązać SQL Database lub Azure Synapse, musisz zezwolić na ruch  sieciowy do i ze wszystkich bram dla regionu.

Szczegółowe informacje na temat migracji ruchu do nowych bram w określonych regionach znajdują się w następującym artykule: Azure SQL Database migracji ruchu do [nowszej bramy](gateway-migration.md)

| Nazwa regionu          | Adresy IP bramy |
| --- | --- |
| Australia Środkowa    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Australia Środkowa 2   | 20.36.113.0, 20.36.112.6 |
| Australia Wschodnia       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australia Południowo-Wschodnia | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Brazylia Południowa         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Kanada Środkowa       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kanada Wschodnia          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Central US           | 13.67.215.62, 52.182.137.15, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Chiny Wschodnie           | 139.219.130.35     |
| Chiny Wschodnie 2         | 40.73.82.1         |
| Chiny Północne          | 139.219.15.17      |
| Chiny Północne 2        | 40.73.50.0         |
| Azja Wschodnia            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| East US              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| Wschodnie stany USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3, 40.70.144.193 |
| Francja Środkowa       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Francja Południowa         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Niemcy Środkowe      | 51.4.144.100       |
| Niemcy Północno-Wschodnie   | 51.5.144.179       |
| Niemcy Zachodnio-środkowe | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Indie Środkowe        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Indie Południowe          | 104.211.224.146    |
| Indie Zachodnie           | 104.211.160.80, 104.211.144.4 |
| Japonia Wschodnia           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Japonia Zachodnia           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Korea Środkowa        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Korea Południowa          | 52.231.200.86, 52.231.151.96 |
| Północno-środkowe stany USA     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Europa Północna         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Holandia Wschodnia          | 51.120.96.0, 51.120.96.33 |
| Holandia Zachodnia          | 51.120.216.0       |
| Północna Republika Południowej Afryki   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Zachodnia Republika Południowej Afryki    | 102.133.24.0       |
| South Central US     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Azja Południowo-Wschodnia      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Korea Północna    | 51.107.56.0, 51.107.57.0 |
| Szwajcaria Zachodnia     | 51.107.152.0, 51.107.153.0 |
| Środkowe Zjednoczone Emiraty Zjednoczone          | 20.37.72.64        |
| Północne Zjednoczone Emiraty Zjednoczone            | 65.52.248.0        |
| Południowe Zjednoczone Królestwo             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11        |
| Zachodnio-środkowe stany USA      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 |
| West Europe          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| Zachodnie stany USA              | 104.42.238.205, 13.86.216.196   |
| Zachodnie stany USA 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| Zachodnie stany USA 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat sposobu zmiany Azure SQL Database połączenia sieciowego dla serwera, zobacz [conn-policy](/cli/azure/sql/server/conn-policy).
- Informacje o zachowaniu połączenia Azure SQL Database klientów, którzy używają programu ADO.NET w wersji 4.5 lub nowszej, można znaleźć w tece [Ports beyond 1433 for ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md)(Porty inne niż 1433 dla klientów w wersji 4.5).
- Aby uzyskać ogólne informacje na temat tworzenia aplikacji, [zobacz SQL Database Application Development Overview (Omówienie tworzenia aplikacji).](develop-overview.md)

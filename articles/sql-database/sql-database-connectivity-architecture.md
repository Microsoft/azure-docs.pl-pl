---
title: Architektura łączności
description: W tym dokumencie wyjaśniono architekturę łączności SQL platformy Azure dla połączeń z bazy danych z platformy Azure lub spoza platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 2028aac9c01aedc4baa568d370c9f7d21c920647
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419267"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura łączności usługi Azure SQL
> [!NOTE]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL Database**. Zobacz [architekturę łączności dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).

W tym artykule opisano architekturę różnych składników, które kierują ruch sieciowy do bazy danych SQL Azure lub usługi SQL Data Warehouse. Wyjaśniono również różne zasady połączeń i wpływ na klientów łączących się z poziomu platformy Azure i klientów łączących się spoza platformy Azure. 

## <a name="connectivity-architecture"></a>Architektura łączności

Poniższy diagram zawiera omówienie wysokiego poziomu architektury łączności usługi Azure SQL Database.

![omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

W poniższych krokach opisano sposób nawiązywać połączenie z bazą danych SQL platformy Azure:

- Klienci łączą się z bramą, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- Brama, w zależności od zasad efektywnego połączenia, przekierowuje lub proxy ruchu do odpowiedniego klastra bazy danych.
- Wewnątrz ruchu klastra bazy danych jest przekazywał do odpowiedniej bazy danych SQL platformy Azure.

## <a name="connection-policy"></a>Zasady połączeń

Usługa Azure SQL Database obsługuje następujące trzy opcje dla ustawienia zasad połączenia serwera bazy danych SQL:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio do węzła obsługującego bazę danych, co prowadzi do zmniejszenia opóźnienia i ulepszonej przepływności. Aby połączenia korzystały z tego trybu, klienci muszą:
   - Zezwalaj na komunikację wychodzącą z klienta na wszystkie adresy IP SQL platformy Azure w regionie na portach w zakresie 11000 11999. Użyj tagów usługi dla sql, aby ułatwić zarządzanie tym.  
   - Zezwalaj na komunikację wychodzącą z klienta do adresów IP bramy usługi Azure SQL Database na porcie 1433.

- **Serwer proxy:** W tym trybie wszystkie połączenia są proxied za pośrednictwem bramy bazy danych SQL azure, co prowadzi do zwiększenia opóźnienia i zmniejszonej przepływności. Aby połączenia korzystały z tego trybu, klienci muszą zezwolić na komunikację wychodzącą z klienta do adresów IP bramy usługi Azure SQL Database na porcie 1433.

- **Wartość domyślna:** Jest to zasada połączenia obowiązująca na wszystkich serwerach po utworzeniu, `Proxy` `Redirect`chyba że jawnie zmienisz zasady połączeń na jedną lub . Domyślna zasada`Redirect` jest dla wszystkich połączeń klientów pochodzących z platformy Azure `Proxy`(na przykład z maszyny wirtualnej platformy Azure) i dla wszystkich połączeń klientów pochodzących spoza (na przykład połączenia z lokalnej stacji roboczej).

 Zdecydowanie zaleca `Redirect` się zasady `Proxy` połączeń za pośrednictwem zasad połączeń dla najniższego opóźnienia i najwyższej przepływności. Jednak należy spełnić dodatkowe wymagania dotyczące zezwalania na ruch sieciowy, jak opisano powyżej. Jeśli klient jest maszyną wirtualną platformy Azure, można to osiągnąć za pomocą sieciowych grup zabezpieczeń (NSG) za pomocą [tagów usługi](../virtual-network/security-overview.md#service-tags). Jeśli klient łączy się ze stacji roboczej lokalnie, może być konieczne połączenie z administratorem sieci, aby zezwolić na ruch sieciowy za pośrednictwem zapory firmowej.

## <a name="connectivity-from-within-azure"></a>Łączność z poziomu platformy Azure

Jeśli łączysz się z poziomu platformy Azure, `Redirect` twoje połączenia mają domyślnie zasady połączenia. Zasady `Redirect` oznacza, że po sesji TCP jest ustanawiany do bazy danych SQL platformy Azure, sesji klienta jest następnie przekierowywane do odpowiedniego klastra bazy danych ze zmianą docelowego wirtualnego adresu IP z bramy bazy danych SQL azure do klastra. Następnie wszystkie kolejne pakiety przepływają bezpośrednio do klastra, z pominięciem bramy bazy danych SQL azure. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność spoza platformy Azure

Jeśli łączysz się spoza platformy Azure, twoje `Proxy` połączenia mają domyślnie zasady połączenia. Zasady `Proxy` oznacza, że sesja TCP jest ustanawiana za pośrednictwem bramy bazy danych SQL Azure i wszystkie kolejne pakiety przepływu za pośrednictwem bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Dodatkowo otwórz porty 14000-14999, aby umożliwić [łączenie się z DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy usługi Azure SQL Database

W poniższej tabeli wymieniono adresy IP bram według regionów. Aby połączyć się z usługą Azure SQL Database, należy zezwolić na ruch sieciowy, aby & ze **wszystkich** bram dla regionu.

Szczegółowe informacje na temat sposobu migracji ruchu do nowych bram w określonych regionach znajdują się w następującym artykule: [Migracja ruchu usługi Azure SQL Database do nowszych bram](sql-database-gateway-migration.md)


| Nazwa regionu          | Adresy IP bramy |
| --- | --- |
| Australia Środkowa    | 20.36.105.0 |
| Australia Central2   | 20.36.113.0 |
| Australia Wschodnia       | 13.75.149.87, 40.79.161.1 |
| Australia Południowo-Wschodnia | 191.239.192.109, 13.73.109.251 |
| Brazylia Południowa         | 104.41.11.5, 191.233.200.14 |
| Kanada Środkowa       | 40.85.224.249      |
| Kanada Wschodnia          | 40.86.226.166      |
| Środkowe stany USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Chiny Wschodnie           | 139.219.130.35     |
| Chiny Wschodnie 2         | 40.73.82.1         |
| Chiny Północne          | 139.219.15.17      |
| Chiny Północne 2        | 40.73.50.0         |
| Azja Wschodnia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Wschodnie stany USA              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Wschodnie stany USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Francja Środkowa       | 40.79.137.0, 40.79.129.1 |
| Niemcy Środkowe      | 51.4.144.100       |
| Niemcy Północny wschód   | 51.5.144.179       |
| Indie Środkowe        | 104.211.96.159     |
| Indie Południowe          | 104.211.224.146    |
| Indie Zachodnie           | 104.211.160.80     |
| Japonia Wschodnia           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japonia Zachodnia           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Korea Środkowa        | 52.231.32.42       |
| Korea Południowa          | 52.231.200.86      |
| Północno-środkowe stany USA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa Północna         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Norwegia Wschodnia          | 51.120.96.0        |
| Norwegia Zachodnia          | 51.120.216.0       |
| Republika Południowej Afryki Północ   | 102.133.152.0      |
| Republika Południowej Afryki Zachód    | 102.133.24.0       |
| Południowo-środkowe stany USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Azja Południowo-Wschodnia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat          | 20.37.72.64        |
| Zjednoczone Emiraty Północne            | 65.52.248.0        |
| Południowe Zjednoczone Królestwo             | 51.140.184.11      |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11        |
| Zachodnio-środkowe stany USA      | 13.78.145.25       |
| Europa Zachodnia          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Zachodnie stany USA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Zachodnie stany USA 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat zmiany zasad połączenia usługi Azure SQL Database dla serwera bazy danych SQL azure, zobacz [zasady conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)
- Aby uzyskać informacje na temat zachowania połączenia usługi Azure SQL Database dla klientów korzystających ADO.NET wersji 4.5 lub nowszej, zobacz [Porty poza 1433 dla ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Aby uzyskać ogólne informacje dotyczące tworzenia aplikacji, zobacz [Omówienie tworzenia aplikacji bazy danych SQL](sql-database-develop-overview.md).

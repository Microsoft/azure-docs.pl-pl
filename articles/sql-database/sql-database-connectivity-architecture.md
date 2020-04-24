---
title: Architektura łączności
description: W tym dokumencie opisano architekturę łączności usługi Azure SQL dla połączeń z bazą danych z platformy Azure lub spoza platformy Azure.
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
ms.openlocfilehash: b4e7d827536245a22d168c7d9923c2e5b82830b0
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111797"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura łączności usługi Azure SQL
> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **Azure SQL Database wystąpienia zarządzanego**. Zapoznaj się z [architekturą łączności dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).

W tym artykule opisano architekturę różnych składników, które kierują ruch sieciowy do Azure SQL Database lub SQL Data Warehouse. Opisano w nim również różne zasady połączeń i wpływ na klientów nawiązujących połączenie z platformy Azure i klientów nawiązujących połączenie spoza platformy Azure. 

## <a name="connectivity-architecture"></a>Architektura łączności

Na poniższym diagramie przedstawiono ogólne omówienie architektury łączności Azure SQL Database.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

W poniższych krokach opisano, jak nawiązać połączenie z bazą danych Azure SQL:

- Klienci łączą się z bramą, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- Brama, w zależności od obowiązujących zasad połączenia, przekierowuje lub proxy ruchu do odpowiedniego klastra bazy danych.
- Wewnątrz ruchu klastra bazy danych jest przekazywany do odpowiedniej bazy danych Azure SQL.

## <a name="connection-policy"></a>Zasady połączenia

Azure SQL Database obsługuje następujące trzy opcje dla ustawienia zasad połączenia serwera SQL Database:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych, co prowadzi do zredukowania opóźnień i zwiększonej przepływności. W przypadku połączeń do korzystania z tego trybu klienci muszą:
   - Zezwalaj na komunikację wychodzącą od klienta do wszystkich adresów IP usługi Azure SQL w regionie na portach z zakresu 11000 11999. Użyj tagów usługi dla SQL, aby ułatwić zarządzanie nimi.  
   - Zezwalaj na komunikację wychodzącą od klienta do Azure SQL Database adresów IP bramy na porcie 1433.

- **Serwer proxy:** W tym trybie wszystkie połączenia są nawiązywane za pośrednictwem bram Azure SQL Database, co prowadzi do zwiększonego opóźnienia i ograniczonej przepływności. W przypadku połączeń do korzystania z tego trybu klienci muszą zezwalać na komunikację wychodzącą od klienta do Azure SQL Database adresów IP bramy na porcie 1433.

- **Wartość domyślna:** Jest to zasada połączenia obowiązująca na wszystkich serwerach po utworzeniu, chyba że jawnie zmienisz zasady połączenia na `Proxy` lub. `Redirect` Zasady domyślne są`Redirect` przeznaczone dla wszystkich połączeń klientów pochodzących z platformy Azure (na przykład z maszyny wirtualnej platformy Azure) i `Proxy`dla wszystkich połączeń klientów pochodzących z zewnątrz (na przykład połączeń z lokalnej stacji roboczej).

 Zdecydowanie zalecamy stosowanie zasad `Redirect` połączenia za pośrednictwem `Proxy` zasad połączenia dla najniższych opóźnień i najwyższej przepływności. Należy jednak spełnić dodatkowe wymagania dotyczące zezwalania na ruch sieciowy opisany powyżej. Jeśli klient jest maszyną wirtualną platformy Azure, możesz to zrobić za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z [tagami usług](../virtual-network/security-overview.md#service-tags). Jeśli klient nawiązuje połączenie z lokalnej stacji roboczej, może być konieczne skontaktowanie się z administratorem sieci w celu zezwolenia na ruch sieciowy za pośrednictwem firmowej zapory.

## <a name="connectivity-from-within-azure"></a>Łączność z poziomu platformy Azure

W przypadku łączenia się z poziomu platformy Azure połączenia mają domyślnie przypisanych zasad `Redirect` połączenia. Zasady `Redirect` oznacza, że po ustanowieniu sesji protokołu TCP z bazą danych Azure SQL sesja klienta zostanie przekierowana do odpowiedniego klastra bazy danych z zmianą docelowego wirtualnego adresu IP z bramy Azure SQL Database do klastra. Następnie wszystkie kolejne pakiety przepływają bezpośrednio do klastra, pomijając Azure SQL Database bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność spoza platformy Azure

Jeśli łączysz się z spoza systemu Azure, połączenia mają domyślnie zasady `Proxy` połączeń. Zasady `Proxy` oznacza, że sesja protokołu TCP jest ustanawiana za pośrednictwem bramy Azure SQL Database i wszystkie kolejne pakiety będą przepływać przez bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Dodatkowo Otwórz porty TCP 1434 i 14000-14999, aby włączyć [łączenie z programem DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy usługi Azure SQL Database

W poniższej tabeli przedstawiono adresy IP bram według regionów. Aby nawiązać połączenie z Azure SQL Database, musisz zezwolić na ruch sieciowy & ze **wszystkich** bram dla regionu.

Szczegóły dotyczące sposobu migrowania ruchu do nowych bram w określonych regionach znajdują się w następującym artykule: [Azure SQL Database migracji ruchu do nowszych bram](sql-database-gateway-migration.md)


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
| Niemcy Północne wschód   | 51.5.144.179       |
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
| Północna Republika Południowej Afryki   | 102.133.152.0      |
| Zachodnia Republika Południowej Afryki    | 102.133.24.0       |
| Południowo-środkowe stany USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Azja Południowo-Wschodnia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Środkowy Zjednoczone Emiraty Arabskie          | 20.37.72.64        |
| Północne Zjednoczone Emiraty Arabskie            | 65.52.248.0        |
| Południowe Zjednoczone Królestwo             | 51.140.184.11      |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11        |
| Zachodnio-środkowe stany USA      | 13.78.145.25       |
| Europa Zachodnia          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Zachodnie stany USA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Zachodnie stany USA 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat zmiany Azure SQL Database zasad połączenia dla serwera Azure SQL Database, zobacz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Aby uzyskać informacje dotyczące zachowania Azure SQL Database połączenia dla klientów korzystających z ADO.NET 4,5 lub nowszej wersji, zobacz [porty wykraczające poza 1433 dla ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Ogólne informacje dotyczące opracowywania aplikacji można znaleźć w temacie [Omówienie tworzenia aplikacji SQL Database](sql-database-develop-overview.md).

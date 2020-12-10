---
title: Architektura łączności — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje architekturę łączności dla Azure Database for PostgreSQL-jednego serwera.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 88d2dc456ab3dba1a21babae36712439b6231d30
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008524"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architektura łączności w Azure Database for PostgreSQL
W tym artykule opisano architekturę Azure Database for PostgreSQL łączności oraz sposób kierowania ruchu do wystąpienia bazy danych Azure Database for PostgreSQL z klientów zarówno w ramach platformy Azure, jak i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z Azure Database for PostgreSQL jest nawiązywane za pomocą bramy, która jest odpowiedzialna za kierowanie połączeń przychodzących do lokalizacji fizycznej serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Przegląd architektury łączności":::

Gdy klient nawiązuje połączenie z bazą danych, otrzymują parametry połączenia, które łączą się z bramą. Ta brama ma publiczny adres IP, który nasłuchuje na porcie 5432. Wewnątrz ruchu klastra bazy danych jest przekazywany do odpowiednich Azure Database for PostgreSQL. W związku z tym, aby nawiązać połączenie z serwerem, na przykład z sieci firmowej, należy otworzyć Zaporę po stronie klienta, aby zezwolić na ruch wychodzący z naszych bram. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy na region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Adresy IP bramy Azure Database for PostgreSQL
W poniższej tabeli wymieniono podstawowe i pomocnicze adresy IP bramy Azure Database for PostgreSQL dla wszystkich obszarów danych. Podstawowy adres IP to bieżący adres IP bramy, a drugi adres IP to adres IP trybu failover w przypadku awarii podstawowej. Jak wspomniano, klienci powinni zezwolić na ruch wychodzący zarówno do adresów IP. Drugi adres IP nie nasłuchuje w żadnej usłudze, dopóki nie zostanie aktywowany przez Azure Database for PostgreSQL w celu zaakceptowania połączeń.

| **Nazwa regionu** | **Adresy IP bramy** |
|:----------------|:-------------|
| Australia Środkowa| 20.36.105.0     |
| Australia Central2     | 20.36.113.0   |
| Australia Wschodnia | 13.75.149.87, 40.79.161.1     |
| Australia Południowo-Wschodnia |191.239.192.109, 13.73.109.251   |
| Brazil South | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Kanada Środkowa |40.85.224.249  |
| Kanada Wschodnia | 40.86.226.166    |
| Central US | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Chiny Wschodnie | 139.219.130.35    |
| Chiny Wschodnie 2 | 40.73.82.1  |
| Chiny Północne | 139.219.15.17    |
| Chiny Północne 2 | 40.73.50.0     |
| Azja Wschodnia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| East US | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113   |
| Wschodnie stany USA 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Francja Środkowa | 40.79.137.0, 40.79.129.1  |
| Francja Południowa | 40.79.177.0     |
| Niemcy Środkowe | 51.4.144.100     |
| Niemcy Północne wschód | 51.5.144.179  |
| Indie Środkowe | 104.211.96.159     |
| Indie Południowe | 104.211.224.146  |
| Indie Zachodnie | 104.211.160.80    |
| Japan East | 13.78.61.196, 191.237.240.43, 40.79.192.23 |
| Japonia Zachodnia | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Korea Środkowa | 52.231.32.42   |
| Korea Południowa | 52.231.200.86    |
| Północno-środkowe stany USA | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa Północna | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Północna Republika Południowej Afryki  | 102.133.152.0    |
| Zachodnia Republika Południowej Afryki | 102.133.24.0   |
| South Central US |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Azja Południowo-Wschodnia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Środkowy Zjednoczone Emiraty Arabskie | 20.37.72.64  |
| Północne Zjednoczone Emiraty Arabskie | 65.52.248.0    |
| Południowe Zjednoczone Królestwo | 51.140.184.11   |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11  |
| Zachodnio-środkowe stany USA | 13.78.145.25     |
| West Europe | 40.68.37.158, 191.237.232.75, 13.69.105.208, 104.40.169.187  |
| Zachodnie stany USA | 104.42.238.205, 23.99.34.75, 13.86.216.212, 13.86.217.212 |
| Zachodnie stany USA 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)

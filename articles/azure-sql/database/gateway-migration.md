---
title: Powiadomienie o migracji ruchu bramy
description: Artykuł zawiera informacje dla użytkowników dotyczące migracji adresów IP Azure SQL Database bramy
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505381"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migracji ruchu do nowszej bramy
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W miarę ulepszania infrastruktury platformy Azure firma Microsoft okresowo odświeża sprzęt, aby zapewnić najlepsze możliwe środowisko klienta. W najbliższych miesiącach planujemy dodać bramy zbudowane w oparciu o nowsze generacje sprzętu, migrować do nich ruch i ostatecznie zlikwidować bramy zbudowane na starszym sprzęcie w niektórych regionach.  

Klienci będą otrzymywać powiadomienia o kondycji usługi z dużym wyprzedzeniem o wszelkich zmianach w bramach dostępnych w poszczególnych regionach. Klienci mogą [używać Azure Portal do skonfigurowania alertów dziennika aktywności.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Najbardziej aktualne informacje będą utrzymywane w tabeli adresów IP Azure SQL Database [bramy.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Aktualizacje stanu

# <a name="in-progress"></a>[W toku](#tab/in-progress-ip)
## <a name="may-2021"></a>Maj 2021 r.
Nowe bramy SQL są dodawane do następujących regionów:
- Południowe Zjednoczone Królestwo: 51.140.144.36, 51.105.72.32  
- Zachodnio-środkowe stany USA: 13.71.193.32, 13.71.193.33 

Ta brama SQL gateway powinna zacząć akceptować ruch klientów w dniu 17 maja 2021 r.

## <a name="april-2021"></a>Kwiecień 2021 r.
Nowe bramy SQL są dodawane do następujących regionów:
- Wschodnie usa 2: 40.70.144.193

Ta brama SQL gateway powinna zacząć akceptować ruch klientów w dniu 30 kwietnia 2021 r.

Nowe bramy SQL są dodawane do następujących regionów:
- Bliski Wschód: 51.120.96.33
- Południowy Azja Wschodnia: 13.67.16.193
- Północna Republika Południowej Afryki: 102.133.152.32
- Korea Południowa: 52.231.151.96
- Północno-środkowe: US 52.162.105.9
- Australia Południowo-Wschodnia: 13.77.49.32 

Te bramy SQL Gateway powinny zacząć akceptować ruch klientów od 5 kwietnia 2021 r.

## <a name="march-2021"></a>Marzec 2021 r.
Dezaktywowanie następujących bram SQL Gateway w wielu regionach jest w trakcie dezaktywowania:
- Brazylia Południowa: 104.41.11.5
- Azja Wschodnia: 191.234.2.139
- Wschodnie usa: 191.238.6.43
- Japonia Wschodnia: 191.237.240.43
- Japonia Zachodnia: 191.238.68.11
- Europa Północna: 191.235.193.75
- Południowo-środkowe usa: 23.98.162.75
- Azja Południowo-Wschodnia: 23.100.117.95
- Europa Zachodnia: 191.237.232.75
- Zachodnie stany USA: 23.99.34.75

Nie przewiduje się żadnego wpływu na klienta, ponieważ te bramy (działające na starszym sprzęcie) nie rozsyłają żadnego ruchu klientów. Adresy IP tych bram zostaną dezaktywowane 15 marca 2021 r.

# <a name="completed"></a>[Ukończone](#tab/completed-ip)
Następujące migracje bramy zostały ukończone: 

## <a name="february-2021"></a>Luty 2021 r.
Nowe bramy SQL są dodawane do następujących regionów:

- Środkowe usa: 13.89.169.20

Te bramy SQL Gateway powinny zacząć akceptować ruch klientów w dniu 28 lutego 2021 r.

## <a name="january-2021"></a>Styczeń 2021 r.
Nowe bramy SQL są dodawane do następujących regionów:

- Australia Środkowa: 20.36.104.6 , 20.36.104.7 
- Australia Środkowa 2: 20.36.112.6 
- Brazylia Południowa: 191.234.144.16 ,191.234.152.3 
- Kanada Wschodnia: 40.69.105.9 ,40.69.105.10
- Indie Środkowe: 104.211.86.30, 104.211.86.31 
- Azja Wschodnia: 13.75.32.14 
- Francja Środkowa: 40.79.137.8, 40.79.145.12 
- Francja Południowa: 40.79.177.10 ,40.79.177.12
- Korea Środkowa: 52.231.17.22 ,52.231.17.23
- Indie Zachodnie: 104.211.144.4

Te bramy SQL Gateway powinny zacząć akceptować ruch klientów w dniu 31 stycznia 2021 r.



### <a name="october-2020"></a>Październik 2020 r.

Nowe bramy SQL są dodawane do następujących regionów:

- Niemcy Zachodnio-środkowe: 51.116.240.0, 51.116.248.0

Te bramy SQL Gateway powinny zacząć akceptować ruch klientów od 12 października 2020 r. 

### <a name="september-2020"></a>Wrzesień 2020
Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL Gateway rozpoczną akceptowanie ruchu klientów **w dniu 15 września 2020 r.:**

- Australia Południowo-Wschodnia: 13.77.48.10
- Kanada Wschodnia: 40.86.226.166, 52.242.30.154
- Południowe Zjednoczone Królestwo: 51.140.184.11, 51.105.64.0

Istniejące bramy SQL zaczną akceptować ruch w następujących regionach. Te bramy SQL Gateway rozpoczną akceptowanie ruchu klientów **od 15 września 2020 r.:**

- Australia Południowo-Wschodnia: 191.239.192.109 i 13.73.109.251
- Środkowe usa: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 i 104.208.21.1
- Azja Wschodnia: 191.234.2.139, 52.175.33.150 i 13.75.32.4
- Wschodnie usa: 40.121.158.30, 40.79.153.12, 191.238.6.43 i 40.78.225.32
- Wschodnie usa 2: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 i 104.208.150.3
- Francja Środkowa: 40.79.137.0 i 40.79.129.1
- Japonia Zachodnia: 104.214.148.156, 40.74.100.192, 191.238.68.11 i 40.74.97.10
- Północno-środkowe usa: 23.96.178.199, 23.98.55.75 i 52.162.104.33
- Azja Południowo-Wschodnia: 104.43.15.0, 23.100.117.95 i 40.78.232.3
- Zachodnie stany USA: 104.42.238.205, 23.99.34.75 i 13.86.216.196

Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL Gateway rozpoczną akceptowanie ruchu klientów **od 10 września 2020 r.:**

- Zachodnio-środkowe stany USA: 13.78.248.43 
- Północna Republika Południowej Afryki: 102.133.120.2  

Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL Gateway powinny zacząć akceptować ruch klientów **od 1 września 2020 r.:**

- Europa Północna: 13.74.104.113 
- Zachodnie stany USA 2: 40.78.248.10 
- Europa Zachodnia: 52.236.184.163 
- Południowo-środkowe usa: 20.45.121.1, 20.49.88.1 

Istniejące bramy SQL zaczną akceptować ruch w następujących regionach. Te bramy SQL Gateway powinny zacząć akceptować ruch klientów **od 1 września 2020 r.:**
- Japonia Wschodnia: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Sierpień 2020 r.

Nowe bramy SQL są dodawane do następujących regionów:

- Australia Wschodnia: 13.70.112.9
- Kanada Środkowa: 52.246.152.0, 20.38.144.1 
- Zachodnie stany USA 2: 40.78.240.8

Te bramy SQL Gateway rozpoczną akceptowanie ruchu klientów od 10 sierpnia 2020 r. 

### <a name="october-2019"></a>Październik 2019 r.
- Brazylia Południowa
- Zachodnie stany USA
- West Europe
- East US
- Central US
- Azja Południowo-Wschodnia
- South Central US
- Europa Północna
- Północno-środkowe stany USA
- Japonia Zachodnia
- Japonia Wschodnia
- Wschodnie stany USA 2
- Azja Wschodnia

---

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Migracja ruchu może zmienić publiczny adres IP rozpoznany przez usługę DNS dla bazy danych w Azure SQL Database.
Może to mieć wpływ na:

- Zakodowano adres IP dla określonej bramy w zaporze lokalnej
- Mieć wszystkie podsieci używające programu Microsoft.SQL jako punktu końcowego usługi, ale nie mogą komunikować się z adresami IP bramy
- Używanie konfiguracji [strefowo nadmiarowej dla warstwy ogólnego przeznaczenia](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Używanie konfiguracji [strefowo nadmiarowej dla warstwy Premium & krytycznej dla działalności firmy](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Nie wpłynie to na Ciebie, jeśli masz:
 
- Przekierowywanie jako zasady połączenia
- Połączenia z SQL Database z wewnątrz platformy Azure i przy użyciu tagów usługi
- Połączenia wykonane przy użyciu obsługiwanych wersji sterownika JDBC dla SQL Server nie będą miały żadnego wpływu. Aby uzyskać informacje o obsługiwanych wersjach sterownika [JDBC, zobacz Pobieranie sterownika JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)firmy Microsoft dla SQL Server .

## <a name="what-to-do-you-do-if-youre-affected"></a>Co należy zrobić, jeśli problem dotyczy

Zalecamy zezwalanie na ruch wychodzący do adresów IP dla wszystkich adresów [IP](connectivity-architecture.md#gateway-ip-addresses) bramy w regionie na porcie TCP 1433 i w zakresie portów 11000–11999. To zalecenie dotyczy klientów łączących się ze środowisk lokalnych oraz klientów łączących się za pośrednictwem punktów końcowych usługi. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [Zasady połączeń](connectivity-architecture.md#connection-policy).

Połączenia wykonane z aplikacji korzystających ze sterownika JDBC firmy Microsoft w wersji 4.0 mogą nie powieść się z weryfikacją certyfikatu. Niższe wersje programu Microsoft JDBC korzystają z nazwy pospolitej (CN) w polu Podmiot certyfikatu. Środki zaradcze mają na celu upewnienie się, że właściwość hostNameInCertificate jest ustawiona na *.database.windows.net. Aby uzyskać więcej informacji na temat sposobu ustawienia właściwości hostNameInCertificate, zobacz [Łączenie z szyfrowaniem](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe środki zaradcze nie działają, należy poprosić o pomoc techniczną SQL Database lub SQL Managed Instance przy użyciu następującego adresu URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze Azure SQL łączności](connectivity-architecture.md)
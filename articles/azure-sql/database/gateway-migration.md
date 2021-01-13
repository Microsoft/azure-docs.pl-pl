---
title: Powiadomienie o migracji ruchu bramy
description: Artykuł zawiera powiadomienie dla użytkowników o migracji adresów IP bramy Azure SQL Database
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 0542d7e0ea204d1e9279e89c9f36b9bc6c6c88fa
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165862"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrację ruchu do nowszych bram
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ze względu na to, że infrastruktura platformy Azure jest ulepszona, firma Microsoft okresowo odświeża sprzęt, aby zapewnić najlepszą obsługę klienta. W najbliższych miesiącach planuje Dodawanie bram opartych na nowszych generacjach sprzętu, migrowaniu ruchu do nich i ostatecznie likwidowanie bram opartych na starszym sprzęcie w niektórych regionach.  

Klienci będą powiadamiani za pośrednictwem poczty e-mail i w Azure Portal również z wyprzedzeniem o wszelkich zmianach bram dostępnych w poszczególnych regionach. Najbardziej aktualne informacje będą przechowywane w tabeli [adresy IP bramy Azure SQL Database](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Aktualizacje stanu

# <a name="in-progress"></a>[W toku](#tab/in-progress-ip)
## <a name="january-2021"></a>Styczeń 2021 r.
Nowe bramy SQL są dodawane do następujących regionów:

- Australia Środkowa: 20.36.104.6, 20.36.104.7 
- Australia Środkowa 2:20.36.112.6 
- Brazylia Południowa: 191.234.144.16, 191.234.152.3 
- Kanada Wschodnia: 40.69.105.9, 40.69.105.10
- Indie Środkowe: 104.211.86.30, 104.211.86.31 
- Azja Wschodnia: 13.75.32.14 
- Francja Środkowa: 40.79.137.8, 40.79.145.12 
- Francja Południowa: 40.79.177.10, 40.79.177.12
- Korea Środkowa: 52.231.17.22, 52.231.17.23
- Indie Zachodnie: 104.211.144.4

Te bramy SQL zaczynają akceptować ruch klienta na dzień 31 stycznia 2021

# <a name="completed"></a>[Ukończone](#tab/completed-ip)
Następujące migracje bramy zostały ukończone: 

### <a name="october-2020"></a>Październik 2020 r.

Nowe bramy SQL są dodawane do następujących regionów:

- Niemcy Środkowo-Zachodnie: 51.116.240.0, 51.116.248.0

Te bramy SQL zaczynają akceptować ruch klienta w dniu 12 października 2020. 

### <a name="september-2020"></a>Wrzesień 2020
Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL zaczynają akceptować ruch klienta na **15 września 2020**:

- Australia Południowo-Wschodnia: 13.77.48.10
- Kanada Wschodnia: 40.86.226.166, 52.242.30.154
- Południowe Zjednoczone Królestwo: 51.140.184.11, 51.105.64.0

Istniejące bramy SQL będą uruchamiać akceptowanie ruchu w następujących regionach. Te bramy SQL zaczynają akceptować ruch klienta na **15 września 2020** :

- Australia Południowo-Wschodnia: 191.239.192.109 i 13.73.109.251
- Środkowe stany USA: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 i 104.208.21.1
- Azja Wschodnia: 191.234.2.139, 52.175.33.150 i 13.75.32.4
- Wschodnie stany USA: 40.121.158.30, 40.79.153.12, 191.238.6.43 i 40.78.225.32
- Wschodnie stany USA 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 i 104.208.150.3
- Francja Środkowa: 40.79.137.0 i 40.79.129.1
- Japonia Zachodnia: 104.214.148.156, 40.74.100.192, 191.238.68.11 i 40.74.97.10
- Północno-środkowe stany USA: 23.96.178.199, 23.98.55.75 i 52.162.104.33
- Azja Południowo-Wschodnia: 104.43.15.0, 23.100.117.95 i 40.78.232.3
- Zachodnie stany USA: 104.42.238.205, 23.99.34.75 i 13.86.216.196

Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL zaczynają akceptować ruch klienta na **10 września 2020**:

- Zachodnio-środkowe stany USA: 13.78.248.43 
- Północna Republika Południowej Afryki: 102.133.120.2  

Nowe bramy SQL są dodawane do następujących regionów. Te bramy SQL zaczynają akceptować ruch klienta **1 września 2020**:

- Europa Północna: 13.74.104.113 
- Stany USA 2 zachodni: 40.78.248.10 
- Europa Zachodnia: 52.236.184.163 
- Południowo-środkowe stany USA: 20.45.121.1, 20.49.88.1 

Istniejące bramy SQL będą uruchamiać akceptowanie ruchu w następujących regionach. Te bramy SQL zaczynają akceptować ruch klienta **1 września 2020** :
- Japonia Wschodnia: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Sierpień 2020 r.

Nowe bramy SQL są dodawane do następujących regionów:

- Australia Wschodnia: 13.70.112.9
- Kanada Środkowa: 52.246.152.0, 20.38.144.1 
- Zachodnie stany USA 2:40.78.240.8

Te bramy SQL zaczynają akceptować ruch klienta w dniu 10 sierpnia 2020. 

### <a name="october-2019"></a>Październik 2019 r.
- Brazil South
- Zachodnie stany USA
- West Europe
- East US
- Central US
- Azja Południowo-Wschodnia
- South Central US
- Europa Północna
- Północno-środkowe stany USA
- Japonia Zachodnia
- Japan East
- Wschodnie stany USA 2
- Azja Wschodnia

---

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Migracja ruchu może zmienić publiczny adres IP rozpoznawany przez system DNS dla bazy danych w Azure SQL Database.
Może mieć to wpływ na następujące działania:

- Sztywno zakodowany adres IP dla każdej konkretnej bramy w zaporze lokalnej
- Istnieją podsieci używające programu Microsoft. SQL jako punktu końcowego usługi, ale nie mogą komunikować się z adresami IP bramy
- Użyj [konfiguracji nadmiarowej strefy dla warstwy ogólnego przeznaczenia](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Użyj [konfiguracji nadmiarowej strefy dla & warstwy krytyczne dla firm](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Nie będzie to miało wpływu na następujące warunki:
 
- Przekierowanie jako zasada połączenia
- Połączenia do SQL Database z platformy Azure i używanie tagów usługi
- Połączenia utworzone przy użyciu obsługiwanych wersji sterownika JDBC dla SQL Server nie będą miały wpływu. Aby uzyskać obsługiwane wersje JDBC, zobacz artykuł [pobieranie sterownika programu Microsoft JDBC dla SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co zrobić, jeśli chcesz to zrobić

Zalecamy Zezwalanie na ruch wychodzący do adresów IP dla wszystkich [adresów IP bramy](connectivity-architecture.md#gateway-ip-addresses) w regionie na porcie TCP 1433 i zakres portów 11000-11999. To zalecenie dotyczy klientów łączących się z lokalnymi, a także połączeń za pośrednictwem punktów końcowych usługi. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [zasady połączeń](connectivity-architecture.md#connection-policy).

Połączenia wykonane z aplikacji przy użyciu sterownika programu Microsoft JDBC w wersji 4,0 mogą kończyć się niepowodzeniem weryfikacji certyfikatu. Niższe wersje programu Microsoft JDBC korzystają z nazwy pospolitej (CN) w polu podmiotu certyfikatu. Środki zaradcze polegają na zapewnieniu, że właściwość hostNameInCertificate jest ustawiona na *. database.windows.net. Aby uzyskać więcej informacji na temat sposobu ustawiania właściwości hostNameInCertificate, zobacz [nawiązywanie połączenia z szyfrowaniem](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe środki zaradcze nie działają, należy wysłać żądanie pomocy technicznej dla SQL Database lub wystąpienia zarządzanego SQL przy użyciu następującego adresu URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze łączności usługi Azure SQL](connectivity-architecture.md)

---
title: Powiadomienie o migracji ruchu bramy
description: Artykuł zawiera powiadomienie dla użytkowników o migracji adresów IP bram Azure SQL Database Gateway
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757069"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migrację ruchu do nowszych bram

Ze względu na to, że infrastruktura platformy Azure jest ulepszona, firma Microsoft okresowo odświeża sprzęt, aby zapewnić najlepszą obsługę klienta. W najbliższych miesiącach planuje Dodawanie bram opartych na nowszych generacjach sprzętu, migrowaniu ruchu do nich i ostatecznie likwidowanie bram opartych na starszym sprzęcie w niektórych regionach.  

Klienci będą powiadamiani za pośrednictwem poczty e-mail i w Azure Portal również z wyprzedzeniem o wszelkich zmianach bram dostępnych w poszczególnych regionach. Najbardziej aktualne informacje będą przechowywane w tabeli [adresy IP bramy Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Wpływ tej zmiany

Pierwsza część migracji ruchu do nowszych bram jest zaplanowana na **14 października 2019** w następujących regionach:
- Brazylia Południowa
- Zachodnie stany USA
- Europa Zachodnia
- Wschodnie stany USA
- Środkowe stany USA
- Azja Południowo-Wschodnia
- Południowo-środkowe stany USA
- Europa Północna
- Północno-środkowe stany USA
- Japonia Zachodnia
- Japonia Wschodnia
- Wschodnie stany USA 2
- Azja Wschodnia

Migracja ruchu zmieni publiczny adres IP, który usługa DNS rozpoznaje dla SQL Database.
Będzie to miało wpływ na następujące działania:
- Sztywno zakodowany adres IP dla każdej konkretnej bramy w zaporze lokalnej
- Wszystkie podsieci używające programu Microsoft. SQL jako punktu końcowego usługi, ale nie mogą komunikować się z adresami IP bramy

Nie będzie to miało wpływu na następujące warunki:
- Przekierowanie jako zasada połączenia
- Połączenia do SQL Database z platformy Azure i używanie tagów usługi
- Połączenia utworzone przy użyciu obsługiwanych wersji sterownika JDBC dla SQL Server nie będą miały wpływu. Aby uzyskać obsługiwane wersje JDBC, zobacz artykuł [pobieranie sterownika programu Microsoft JDBC dla SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Co zrobić, jeśli chcesz to zrobić

Zalecamy Zezwalanie na ruch wychodzący do adresów IP dla wszystkich [adresów IP bramy Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) w regionie na porcie TCP 1433 i zakres portów 11000-11999. To zalecenie dotyczy klientów łączących się z lokalnymi, a także połączeń za pośrednictwem punktów końcowych usługi. Aby uzyskać więcej informacji na temat zakresów portów, zobacz [zasady połączeń](sql-database-connectivity-architecture.md#connection-policy).

Połączenia wykonane z aplikacji przy użyciu sterownika programu Microsoft JDBC w wersji 4,0 mogą kończyć się niepowodzeniem weryfikacji certyfikatu. Niższe wersje programu Microsoft JDBC korzystają z nazwy pospolitej (CN) w polu podmiotu certyfikatu. Środki zaradcze polegają na zapewnieniu, że właściwość hostNameInCertificate jest ustawiona na *. database.windows.net. Aby uzyskać więcej informacji na temat sposobu ustawiania właściwości hostNameInCertificate, zobacz [nawiązywanie połączenia z szyfrowaniem](/sql/connect/jdbc/connecting-with-ssl-encryption).

Jeśli powyższe środki zaradcze nie działają, należy wysłać żądanie pomocy technicznej dotyczące SQL Database przy użyciu następującego adresu URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze łączności usługi Azure SQL](sql-database-connectivity-architecture.md)

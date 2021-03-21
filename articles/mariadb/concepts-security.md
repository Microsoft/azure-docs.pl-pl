---
title: Zabezpieczenia — Azure Database for MariaDB
description: Omówienie funkcji zabezpieczeń w Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8a46f70e3089a8610fa8ca6362bcba6f624e2b1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664184"
---
# <a name="security-in-azure-database-for-mariadb"></a>Zabezpieczenia w usłudze Azure Database for MariaDB

Istnieje wiele warstw zabezpieczeń, które są dostępne do ochrony danych na serwerze Azure Database for MariaDB. W tym artykule opisano opcje zabezpieczeń.

## <a name="information-protection-and-encryption"></a>Ochrona informacji i szyfrowanie

### <a name="in-transit"></a>W trakcie przesyłania
Azure Database for MariaDB zabezpiecza dane przez szyfrowanie danych podczas przesyłania z Transport Layer Security. Szyfrowanie (SSL/TLS) jest domyślnie wymuszane.

### <a name="at-rest"></a>W spoczynku
Usługa Azure Database for MariaDB używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku, z wyjątkiem plików tymczasowych utworzonych podczas wykonywania zapytań. Usługa używa szyfru AES 256-bit zawartego w szyfrowaniu usługi Azure Storage, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.


## <a name="network-security"></a>Bezpieczeństwo sieci
Połączenia z serwerem Azure Database for MariaDB są najpierw kierowane przez bramę regionalną. Brama ma publicznie dostępny adres IP, podczas gdy adresy IP serwera są chronione. Więcej informacji o bramie można znaleźć w artykule dotyczącym [architektury łączności](concepts-connectivity-architecture.md).  

Nowo utworzony serwer Azure Database for MariaDB ma zaporę, która blokuje wszystkie połączenia zewnętrzne. Chociaż docierają one do bramy, nie mogą łączyć się z serwerem. 

### <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Reguły zapory adresów IP umożliwiają dostęp do serwerów na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej
Punkty końcowe usługi sieci wirtualnej zwiększają łączność sieci wirtualnej przez sieć szkieletową platformy Azure. Korzystając z reguł sieci wirtualnej, można włączyć serwer Azure Database for MariaDB, aby zezwalać na połączenia z wybranych podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie punktu końcowego usługi sieci wirtualnej](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Zarządzanie dostępem

Podczas tworzenia serwera Azure Database for MariaDB podajesz poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników MariaDB.


## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Możesz zrezygnować z [zaawansowaną ochronę przed zagrożeniami](concepts-data-access-and-security-threat-protection.md) , która wykrywa nietypowe działania wskazujące nietypową i potencjalnie szkodliwe próby dostępu do serwerów lub korzystania z nich.

[Rejestrowanie inspekcji](concepts-audit-logs.md) jest dostępne do śledzenia aktywności w bazach danych. 


## <a name="next-steps"></a>Następne kroki
- Włącz reguły zapory dla [adresów IP](concepts-firewall-rules.md) lub [sieci wirtualnych](concepts-data-access-security-vnet.md)
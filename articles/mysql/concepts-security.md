---
title: Zabezpieczenia — Azure Database for MySQL
description: Omówienie funkcji zabezpieczeń w Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5caed64beb43cbb1e884a3dde6bc0052c617f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378784"
---
# <a name="security-in-azure-database-for-mysql"></a>Zabezpieczenia w usłudze Azure Database for MySQL

Istnieje wiele warstw zabezpieczeń, które są dostępne do ochrony danych na serwerze Azure Database for MySQL. W tym artykule opisano opcje zabezpieczeń.

## <a name="information-protection-and-encryption"></a>Ochrona informacji i szyfrowanie

### <a name="in-transit"></a>W trakcie przesyłania
Azure Database for MySQL zabezpiecza dane przez szyfrowanie danych podczas przesyłania z Transport Layer Security. Szyfrowanie (SSL/TLS) jest domyślnie wymuszane.

### <a name="at-rest"></a>W spoczynku
Usługa Azure Database for MySQL używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe, są szyfrowane na dysku, łącznie z plikami tymczasowymi utworzonymi podczas wykonywania zapytań. Usługa używa szyfru AES 256-bit zawartego w szyfrowaniu usługi Azure Storage, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.


## <a name="network-security"></a>Bezpieczeństwo sieci
Połączenia z serwerem Azure Database for MySQL są najpierw kierowane przez bramę regionalną. Brama ma publicznie dostępny adres IP, podczas gdy adresy IP serwera są chronione. Więcej informacji o bramie można znaleźć w artykule dotyczącym [architektury łączności](concepts-connectivity-architecture.md).  

Nowo utworzony serwer Azure Database for MySQL ma zaporę, która blokuje wszystkie połączenia zewnętrzne. Chociaż docierają one do bramy, nie mogą łączyć się z serwerem. 

### <a name="ip-firewall-rules"></a>Reguły zapory adresów IP
Reguły zapory adresów IP umożliwiają dostęp do serwerów na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej
Punkty końcowe usługi sieci wirtualnej zwiększają łączność sieci wirtualnej przez sieć szkieletową platformy Azure. Korzystając z reguł sieci wirtualnej, można włączyć serwer Azure Database for MySQL, aby zezwalać na połączenia z wybranych podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie punktu końcowego usługi sieci wirtualnej](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Prywatny adres IP
Link prywatny umożliwia nawiązanie połączenia z Azure Database for MySQL na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo łączy usługi platformy Azure z Twoją prywatną siecią wirtualną. Dostęp do zasobów PaaS można uzyskać przy użyciu prywatnego adresu IP, podobnie jak w przypadku każdego innego zasobu w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [link prywatny — Omówienie](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>Zarządzanie dostępem

Podczas tworzenia serwera Azure Database for MySQL podajesz poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników programu MySQL.


## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Możesz zrezygnować z [zaawansowaną ochronę przed zagrożeniami](concepts-data-access-and-security-threat-protection.md) , która wykrywa nietypowe działania wskazujące nietypową i potencjalnie szkodliwe próby dostępu do serwerów lub korzystania z nich.

[Rejestrowanie inspekcji](concepts-audit-logs.md) jest dostępne do śledzenia aktywności w bazach danych. 


## <a name="next-steps"></a>Następne kroki
- Włącz reguły zapory dla [adresów IP](concepts-firewall-rules.md) lub [sieci wirtualnych](concepts-data-access-and-security-vnet.md)

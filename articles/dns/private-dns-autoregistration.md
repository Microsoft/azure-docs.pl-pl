---
title: Funkcja autorejestracji w Azure DNS strefach prywatnych
description: Omówienie funkcji autorejestrowania w Azure DNS strefach prywatnych
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 8e976199730ec74f9e76b9f4199e90e192916474
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966721"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Co to jest funkcja autorejestrowania w strefach prywatnych Azure DNS

Funkcja autorejestracji stref prywatnych Azure DNS ma problemy z zarządzaniem rekordami DNS dla maszyn wirtualnych wdrożonych w sieci wirtualnej. Po [połączeniu sieci wirtualnej](./private-dns-virtual-network-links.md) z prywatną strefą DNS i włączeniu automatycznej rejestracji dla wszystkich maszyn wirtualnych rekordy DNS dla maszyn wirtualnych wdrożonych w sieci wirtualnej są automatycznie tworzone w prywatnej strefie DNS. Oprócz przeszukiwania rekordów (rekordów), rekordy wyszukiwania wstecznego (rekordy PTR) również są tworzone automatycznie dla maszyn wirtualnych.
W przypadku dodania większej liczby maszyn wirtualnych do sieci wirtualnej rekordy DNS dla tych maszyn wirtualnych są również automatycznie tworzone w połączonej prywatnej strefie DNS.

Po usunięciu maszyny wirtualnej rekordy DNS dla tej maszyny wirtualnej są automatycznie usuwane z prywatnej strefy DNS.

Autorejestrację można włączyć, wybierając opcję "Włącz autorejestrację" podczas tworzenia linku do sieci wirtualnej.

![Włącz rejestrację autorejestrowania](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Ograniczenia

* Autorejestracja działa tylko w przypadku maszyn wirtualnych. W przypadku wszystkich innych zasobów, takich jak wewnętrzne moduły równoważenia obciążenia itp., można ręcznie utworzyć rekordy DNS w prywatnej strefie DNS połączonej z siecią wirtualną.
* Rekordy DNS są tworzone automatycznie tylko dla podstawowej karty sieciowej maszyny wirtualnej. Jeśli maszyny wirtualne mają więcej niż jedną kartę sieciową, można ręcznie utworzyć rekordy DNS dla innych interfejsów sieciowych.
* Rekordy DNS są tworzone automatycznie tylko wtedy, gdy podstawowa karta sieciowa maszyny wirtualnej używa protokołu DHCP. Jeśli skonfigurowano statyczne adresy IP (na przykład w celu użycia [wielu adresów IP na platformie Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)), autorejestracja nie tworzy rekordów dla tej maszyny wirtualnej.
* Autorejestracja w protokole IPv6 (rekordy AAAA) nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS, w tym określonych zachowań, których można oczekiwać w przypadku niektórych rodzajów operacji, zapoznaj się z tematem [prywatna strefa DNS często zadawane pytania](./dns-faq-private.md).
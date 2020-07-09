---
title: Funkcja autorejestracji w Azure DNS strefach prywatnych
description: Omówienie funkcji autorejestrowania w Azure DNS strefach prywatnych
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "71961172"
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
* Autorejestracja w protokole IPv6 (rekordy AAAA) nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS, w tym określonych zachowań, których można oczekiwać w przypadku niektórych rodzajów operacji, zapoznaj się z tematem [prywatna strefa DNS często zadawane pytania](./dns-faq-private.md).

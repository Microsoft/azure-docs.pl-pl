---
title: Co to jest strefa prywatna Azure DNS
description: Omówienie prywatnej strefy DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 0e04d7525cbd0c707ba0050f31414c2472602d1b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311417"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Co to jest prywatna strefa Azure DNS

Usługa Azure Prywatna strefa DNS zapewnia niezawodną, bezpieczną usługę DNS do zarządzania i rozpoznawania nazw domen w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Używając prywatnych stref DNS, możesz użyć własnych niestandardowych nazw domen, a nie dostępnych już dzisiaj nazw udostępnianych przez platformę Azure. 

Rekordy zawarte w prywatnej strefie DNS nie są rozpoznawane z Internetu. Rozpoznawanie nazw DNS względem prywatnej strefy DNS działa tylko z sieci wirtualnych, które są z nim połączone.

Możesz połączyć prywatną strefę DNS z jedną lub wieloma sieciami wirtualnymi, tworząc [linki sieci wirtualnej](./private-dns-virtual-network-links.md).
Możesz również włączyć funkcję [autorejestrowania](./private-dns-autoregistration.md) , aby automatycznie zarządzać cyklem życia rekordów DNS dla maszyn wirtualnych, które są wdrażane w sieci wirtualnej.

## <a name="limits"></a>Limity

Aby zrozumieć, ile prywatnych stref DNS można utworzyć w ramach subskrypcji i ile zestawów rekordów jest obsługiwanych w prywatnej strefie DNS, zobacz [limity Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Ograniczenia

* Prywatne strefy DNS z pojedynczą etykietą nie są obsługiwane. Prywatna strefa DNS musi mieć co najmniej dwie etykiety. Na przykład contoso.com ma dwie etykiety oddzielone kropką. Prywatna strefa DNS może mieć maksymalnie 34 etykiet.
* Nie można utworzyć delegowania strefy (rekordy NS) w prywatnej strefie DNS. Jeśli zamierzasz używać domeny podrzędnej, możesz bezpośrednio utworzyć domenę jako prywatną strefę DNS. Następnie można połączyć ją z siecią wirtualną bez konfigurowania delegowania serwer nazw ze strefy nadrzędnej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS można znaleźć w temacie [prywatna strefa DNS FAQ](./dns-faq-private.md).
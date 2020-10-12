---
title: Co to jest zasób linku sieci wirtualnej Azure DNS stref prywatnych
description: Przegląd zasobu podrzędnego link sieci wirtualnej a Azure DNS strefy prywatnej
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646765"
---
# <a name="what-is-a-virtual-network-link"></a>Co to jest link do sieci wirtualnej?

Po utworzeniu prywatnej strefy DNS na platformie Azure nie będzie ona natychmiast dostępna z żadnej sieci wirtualnej. Musisz połączyć ją z siecią wirtualną, zanim maszyna wirtualna hostowana w tej sieci będzie mogła uzyskać dostęp do prywatnej strefy DNS.
Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć łącze sieci wirtualnej w ramach prywatnej strefy DNS. Każda prywatna strefa DNS ma kolekcję zasobów podrzędnych linku sieci wirtualnej. Każdy z tych zasobów reprezentuje połączenie z siecią wirtualną.

Można połączyć sieć wirtualną z prywatną strefą DNS jako sieć wirtualną rejestracji lub jako sieć wirtualną rozpoznawania.

## <a name="registration-virtual-network"></a>Sieć wirtualna rejestracji

Podczas [tworzenia łącza](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) między prywatną strefą DNS i siecią wirtualną istnieje możliwość włączenia [autorejestracji](./private-dns-autoregistration.md) rekordów DNS dla maszyn wirtualnych. W przypadku wybrania tej opcji Sieć wirtualna będzie siecią wirtualną rejestracji dla prywatnej strefy DNS. Rekord DNS jest tworzony automatycznie dla maszyn wirtualnych wdrażanych w sieci. Rekordy DNS są tworzone dla maszyn wirtualnych, które zostały już wdrożone w sieci wirtualnej. Z punktu widzenia sieci wirtualnej prywatna strefa DNS zostaje strefą rejestracji dla tej sieci wirtualnej.
Jedna prywatna strefa DNS może mieć wiele sieci wirtualnych rejestracji, ale każda sieć wirtualna może mieć dokładnie jedną skojarzoną z nią strefę rejestracji.

## <a name="resolution-virtual-network"></a>Sieć wirtualna rozpoznawania

Po utworzeniu łącza sieci wirtualnej w ramach prywatnej strefy DNS i wybraniu opcji niewłączania autorejestrowania rekordów DNS Sieć wirtualna jest traktowana jako sieć wirtualna tylko do rozpoznawania. Rekordy DNS dla maszyn wirtualnych wdrożonych w tych sieciach nie zostaną automatycznie utworzone w połączonej prywatnej strefie DNS. Jednak maszyny wirtualne wdrożone w takiej sieci mogą pomyślnie wysyłać zapytania o rekordy DNS z prywatnej strefy DNS. Te rekordy mogą być tworzone ręcznie przez użytkownika lub mogą być wypełniane przez inne sieci wirtualne, które zostały połączone jako sieci rejestracji z prywatną strefą DNS.
Jedna prywatna strefa DNS może mieć wiele sieci wirtualnych rozpoznawania, a sieć wirtualna może mieć wiele skojarzonych stref rozpoznawania.

## <a name="limits"></a>Limity

Aby zrozumieć, ile sieci rejestrowania i rozpoznawania można połączyć z prywatnymi strefami DNS, zobacz [limity Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Inne zagadnienia

* Sieci wirtualne wdrożone przy użyciu klasycznego modelu wdrażania nie są obsługiwane.

* Można utworzyć tylko jeden link między prywatną strefą DNS i siecią wirtualną.

* Każde łącze sieci wirtualnej w ramach prywatnej strefy DNS musi mieć unikatową nazwę w kontekście prywatnej strefy DNS. Możesz mieć linki o tej samej nazwie w różnych prywatnych strefach DNS.

* Po utworzeniu linku sieci wirtualnej sprawdź pole "Stan łącza" zasobu linku sieci wirtualnej. W zależności od rozmiaru sieci wirtualnej może upłynąć kilka minut przed operacją linku, a stan łącza zmieni się na *zakończone*.

* Po usunięciu sieci wirtualnej wszystkie linki sieci wirtualnej i automatycznie zarejestrowane rekordy DNS skojarzone z nim w różnych prywatnych strefach DNS są automatycznie usuwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak połączyć sieć wirtualną z prywatną strefą DNS przy użyciu [Azure Portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS, w tym określonych zachowań, których można oczekiwać w przypadku niektórych rodzajów operacji, zapoznaj się z tematem [prywatna strefa DNS często zadawane pytania](./dns-faq-private.md).

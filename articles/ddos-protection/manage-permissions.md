---
title: Uprawnienia Azure DDoS Protection planu
description: Dowiedz się, jak zarządzać uprawnieniami w planie ochrony.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 08ab78171376f702aecb1f49571dda583eae5d82
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813874"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Zarządzanie planami DDoS Protection: uprawnienia i ograniczenia

Plan ochrony DDoS działa w regionach i subskrypcjach. Ten sam plan może być połączony z sieciami wirtualnymi z innych subskrypcji w różnych regionach w ramach dzierżawy. Subskrypcja skojarzona z planem polega na miesięcznym rozliczeniu za plan, a także opłat za użycie nadwyżkowe, w przypadku gdy liczba chronionych publicznych adresów IP przekracza 100. Aby uzyskać więcej informacji na temat cennika usługi DDoS, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Wymagania wstępne

- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony programu Azure DDoS Standard](manage-ddos-protection.md).

## <a name="permissions"></a>Uprawnienia

Aby można było korzystać z planów ochrony DDoS, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                            | Nazwa                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/odczyt        | Odczytaj plan ochrony DDoS              |
| Microsoft. Network/ddosProtectionPlans/Write       | Utwórz lub zaktualizuj plan ochrony DDoS  |
| Microsoft. Network/ddosProtectionPlans/Delete      | Usuń plan ochrony DDoS            |
| Microsoft. Network/ddosProtectionPlans/Join/Action | Dołącz do planu ochrony DDoS              |

Aby włączyć ochronę DDoS dla sieci wirtualnej, konto musi również mieć przypisane odpowiednie [działania dotyczące sieci wirtualnych](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Azure Policy

Tworzenie więcej niż jednego planu nie jest wymagane w przypadku większości organizacji. Nie można przenieść planu między subskrypcjami. Jeśli chcesz zmienić subskrypcję, a plan znajduje się w, musisz usunąć istniejący plan i utworzyć nowy.

W przypadku klientów, którzy mają różne subskrypcje i którzy chcą zapewnić, że jeden plan jest wdrażany w ramach swojej dzierżawy w celu kontroli kosztów, można użyć Azure Policy, aby [ograniczyć tworzenie Azure DDoS Protection planów standardowych](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Te zasady będą blokować tworzenie wszelkich planów DDoS, chyba że subskrypcja została wcześniej oznaczona jako wyjątek. W tych zasadach zostanie również wyświetlona lista wszystkich subskrypcji, które mają wdrożony plan DDoS, ale nie powinny być oznaczane jako niezgodne.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry.md)
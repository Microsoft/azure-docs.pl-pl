---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597948"
---
## <a name="attack-scenario"></a>Scenariusz ataku

Bezprawne ataki są często docelowymi portami zarządzania jako środek do uzyskania dostępu do maszyny wirtualnej. Jeśli to się powiedzie, osoba atakująca może przejąć kontrolę nad maszyną wirtualną i ustanowić przyczółka w swoim środowisku.

Jednym ze sposobów zmniejszenia narażenia na ataki z wykorzystaniem bezprawnego ataku jest ograniczenie czasu, przez który port jest otwarty. Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nastąpi połączenie z maszyną wirtualną, na przykład do wykonywania zadań zarządzania lub konserwacji. Gdy jest włączone just-in-Time, Security Center korzysta z zasad [sieciowych grup zabezpieczeń](../articles/virtual-network/security-overview.md#security-rules) (sieciowej grupy zabezpieczeń) i zapory platformy Azure, które ograniczają dostęp do portów zarządzania, więc nie mogą być objęte przez osoby atakujące.

![Scenariusz just in Time](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak działa dostęp JIT?

Gdy jest włączone just-in-Time, Security Center blokuje ruch przychodzący do maszyn wirtualnych platformy Azure przez utworzenie reguły sieciowej grupy zabezpieczeń. Wybierasz porty na maszynie wirtualnej, do której zostanie zablokowany ruch przychodzący. Te porty są kontrolowane przez rozwiązanie just-in-Time.

Gdy użytkownik zażąda dostępu do maszyny wirtualnej, Security Center sprawdza, czy użytkownik ma uprawnienia [Access Control na podstawie ról (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) dla tej maszyny wirtualnej. Jeśli żądanie zostało zatwierdzone, Security Center automatycznie konfiguruje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i zaporę platformy Azure w celu zezwolenia na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów, przez określony czas. Po upływie tego czasu program Security Center Przywraca poprzedni stan sieciowych grup zabezpieczeń. Te połączenia, które są już ustanowione, nie są jednak przerywane.

 > [!NOTE]
 > Jeśli żądanie dostępu JIT zostanie zatwierdzone dla maszyny wirtualnej za zaporą platformy Azure, Security Center automatycznie zmieni reguły zasad sieciowej grupy zabezpieczeń i zapory. Przez określony czas, reguły zezwalają na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów. Po upływie tego czasu program Security Center przywraca poprzednie Stany zapory i reguły sieciowej grupy zabezpieczeń.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Uprawnienia wymagające konfigurowania i używania JIT

| Aby umożliwić użytkownikowi: | Uprawnienia do ustawienia|
| --- | --- |
| Konfigurowanie lub edytowanie zasad JIT dla maszyny wirtualnej | *Przypisz następujące akcje do roli:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> W zakresie subskrypcji lub grupy zasobów maszyny wirtualnej: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Zażądaj dostępu JIT do maszyny wirtualnej | *Przypisz następujące akcje do użytkownika:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
---
title: Informacje o dostępie do maszyn wirtualnych just in Time w Azure Security Center
description: W tym dokumencie wyjaśniono, jak dostęp just in Time do maszyny wirtualnej w programie Azure Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93285737"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Informacje o dostępie just in time (JIT) do maszyny wirtualnej

Na tej stronie objaśniono zasady dostępu do maszyny wirtualnej w trybie just-in-Time (JIT) Azure Security Center i logika zaleceń.

Aby dowiedzieć się, jak zastosować JIT do maszyn wirtualnych przy użyciu Azure Portal (Security Center lub Azure Virtual Machines) lub programowo, zobacz [jak zabezpieczyć porty zarządzania przy użyciu JIT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Ryzyko otwarcia portów zarządzania na maszynie wirtualnej

Aktorzy zagrożeń aktywnie Wykorzystaj dostępne maszyny z otwartymi portami zarządzania, takimi jak RDP lub SSH. Wszystkie maszyny wirtualne są potencjalnymi obiektami docelowymi ataku. Po pomyślnym naruszeniu maszyny wirtualnej jest ona używana jako punkt wejścia do ataku dalszych zasobów w środowisku.



## <a name="why-jit-vm-access-is-the-solution"></a>Dlaczego dostęp do maszyny wirtualnej JIT jest rozwiązaniem 

Podobnie jak w przypadku wszystkich technik zapobiegania cyberbezpieczeństwa, celem powinien być zmniejszenie podatności na ataki. W takim przypadku oznacza to, że jest to mniej otwartych portów, szczególnie portów zarządzania.

Legalni użytkownicy korzystają również z tych portów, dlatego nie jest praktyczne, aby je zamknąć.

Aby rozwiązać ten dylematem, Azure Security Center oferuje JIT. Korzystając z JIT, można zablokować ruch przychodzący do maszyn wirtualnych, ograniczając narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Jak działa kompilator JIT z sieciowymi grupami zabezpieczeń i zaporą platformy Azure

Po włączeniu dostępu just in Time do maszyny wirtualnej można wybrać porty na maszynie wirtualnej, do której zostanie zablokowany ruch przychodzący. Security Center gwarantuje, że istnieją reguły "Odmów całego ruchu przychodzącego" dla wybranych portów w regułach [sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules) (sieciowej grupy zabezpieczeń) i [zapory platformy Azure](../firewall/rule-processing.md). Te reguły ograniczają dostęp do portów zarządzania maszyn wirtualnych platformy Azure i obrony przed atakami. 

Jeśli istnieją już inne reguły dla wybranych portów, te istniejące reguły mają pierwszeństwo przed nowym regułą "Odmów całego ruchu przychodzącego". Jeśli nie ma żadnych istniejących reguł na wybranych portach, nowe reguły mają najwyższy priorytet w sieciowej grupy zabezpieczeń i zaporze platformy Azure.

Gdy użytkownik zażąda dostępu do maszyny wirtualnej, Security Center sprawdza, czy użytkownik ma uprawnienia do [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) dla tej maszyny wirtualnej. Jeśli żądanie zostało zatwierdzone, Security Center konfiguruje Zaporę sieciowych grup zabezpieczeń i platformy Azure tak, aby zezwalała na ruch przychodzący do wybranych portów z odpowiedniego adresu IP (lub zakresu) przez określony czas. Po upływie tego czasu program Security Center Przywraca poprzedni stan sieciowych grup zabezpieczeń. Połączenia, które zostały już ustanowione, nie są przerywane.

> [!NOTE]
> Kompilator JIT nie obsługuje maszyn wirtualnych chronionych przez zapory platformy Azure kontrolowane przez [Menedżera zapory platformy Azure](../firewall-manager/overview.md).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Jak Security Center określa, które maszyny wirtualne powinny mieć zastosowany JIT

Na poniższym diagramie przedstawiono logikę, która Security Center stosowana podczas decydowania o sposobie kategoryzacji obsługiwanych maszyn wirtualnych: 

[![Przepływ logiki maszyny wirtualnej "just in Time" (JIT)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Gdy Security Center odnajdzie maszynę, która może korzystać z JIT, dodaje tę maszynę do karty **zasoby w złej kondycji** rekomendacji. 

![Zalecenie dotyczące dostępu do maszyny wirtualnej just-in-Time (JIT)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Często zadawane pytania — pytania dotyczące dostępu do maszyny wirtualnej just in Time

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Jakie uprawnienia są potrzebne do skonfigurowania i używania JIT?

Kompilator JIT wymaga włączenia [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji. 

Role **Reader** i **SecurityReader** mogą wyświetlać stan i parametry JIT.

Aby utworzyć role niestandardowe, które mogą współpracować z JIT, potrzebne są szczegóły z poniższej tabeli.

> [!TIP]
> Aby utworzyć rolę najniższych uprawnień dla użytkowników, którzy muszą zażądać dostępu JIT do maszyny wirtualnej i wykonać inne operacje JIT, użyj [skryptu Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) ze stron społeczności usługi GitHub Security Center.

| Aby umożliwić użytkownikowi: | Uprawnienia do ustawienia|
| --- | --- |
|Konfigurowanie lub edytowanie zasad JIT dla maszyny wirtualnej | *Przypisz następujące akcje do roli:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> W zakresie subskrypcji lub grupy zasobów maszyny wirtualnej: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Zażądaj dostępu JIT do maszyny wirtualnej | *Przypisz następujące akcje do użytkownika:*  <ul><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>W zakresie subskrypcji lub grupy zasobów skojarzonej z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny wirtualnej:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Odczyt zasad JIT| *Przypisz następujące akcje do użytkownika:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Następne kroki

Ta strona wyjaśnia, _dlaczego_ należy używać dostępu do maszyny wirtualnej just-in-Time (JIT). 

Przejdź do artykułu z poradnikiem, aby dowiedzieć się, jak włączyć JIT i żądać dostępu do maszyn wirtualnych z obsługą JIT:

> [!div class="nextstepaction"]
> [Jak zabezpieczyć porty zarządzania przy użyciu JIT](security-center-just-in-time.md)
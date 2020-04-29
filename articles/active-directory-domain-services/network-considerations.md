---
title: Planowanie sieci i połączenia dla Azure AD Domain Services | Microsoft Docs
description: Zapoznaj się z zagadnieniami dotyczącymi projektowania sieci wirtualnej i zasobami używanymi do łączności podczas uruchamiania Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e610bf94dfdee4e2765e4fae4259f18a9f1036b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639989"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Zagadnienia dotyczące projektowania sieci wirtualnej i opcje konfiguracji Azure AD Domain Services

Jako Azure Active Directory Domain Services (AD DS) zapewnia usługi uwierzytelniania i zarządzania dla innych aplikacji i obciążeń, połączenie sieciowe to kluczowy składnik. Bez prawidłowo skonfigurowanych zasobów sieci wirtualnej nie można komunikować się z aplikacjami i obciążeniami oraz korzystać z funkcji udostępnianych przez usługę Azure AD DS. Zaplanuj wymagania dotyczące sieci wirtualnej, aby upewnić się, że usługa Azure AD DS może obsłużyć Twoje aplikacje i obciążenia zgodnie z potrzebami.

W tym artykule przedstawiono zagadnienia dotyczące projektowania i wymagania dotyczące sieci wirtualnej platformy Azure w celu obsługi AD DS platformy Azure.

## <a name="azure-virtual-network-design"></a>Projekt sieci wirtualnej platformy Azure

Aby zapewnić łączność sieciową i zezwolić aplikacjom i usługom na uwierzytelnianie w usłudze Azure AD DS, należy użyć sieci wirtualnej platformy Azure i podsieci. W idealnym przypadku usługa Azure AD DS powinna zostać wdrożona w swojej własnej sieci wirtualnej. W tej samej sieci wirtualnej można dołączyć oddzielną podsieć aplikacji do hostowania maszyn wirtualnych zarządzania lub lekkich obciążeń aplikacji. Oddzielna Sieć wirtualna dla większych lub złożonych obciążeń aplikacji, które są połączone z siecią wirtualną platformy Azure AD DS, jest zazwyczaj najbardziej odpowiednim projektem. Inne opcje wyboru projektów są prawidłowe, pod warunkiem, że spełniasz wymagania opisane w poniższych sekcjach dla sieci wirtualnej i podsieci.

Podczas projektowania sieci wirtualnej dla usługi Azure AD DS są stosowane następujące zagadnienia:

* Usługę Azure AD DS należy wdrożyć w tym samym regionie świadczenia usługi Azure, w którym znajduje się Twoja sieć wirtualna.
    * W tej chwili można wdrożyć tylko jedną domenę zarządzaną platformy Azure AD DS dla dzierżawy usługi Azure AD. Domena zarządzana AD DS platformy Azure została wdrożona w jednym regionie. Upewnij się, że tworzysz lub wybrano sieć wirtualną w [regionie, który obsługuje usługę Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Rozważ bliskość innych regionów świadczenia usługi Azure i sieci wirtualnych, które obsługują obciążenia aplikacji.
    * Aby zminimalizować opóźnienie, należy utrzymać podstawowe aplikacje w pobliżu lub w tym samym regionie, w którym znajduje się podsieć sieci wirtualnej dla domeny zarządzanej platformy Azure AD DS. Między sieciami wirtualnymi platformy Azure można używać połączeń równorzędnych sieci wirtualnych lub wirtualnych sieci prywatnych (VPN). Te opcje połączenia zostały omówione w poniższej sekcji.
* Sieć wirtualna nie może polegać na usługach DNS innych niż udostępniane przez usługę Azure AD DS.
    * Usługa Azure AD DS udostępnia własną usługę DNS. Sieć wirtualna musi być skonfigurowana do używania tych adresów usługi DNS. Rozpoznawanie nazw dla dodatkowych przestrzeni nazw można wykonać przy użyciu usług przesyłania dalej warunkowego.
    * Nie można użyć niestandardowych ustawień serwera DNS, aby kierować zapytania z innych serwerów DNS, w tym na maszynach wirtualnych. Zasoby w sieci wirtualnej muszą korzystać z usługi DNS dostępnej w usłudze Azure AD DS.

> [!IMPORTANT]
> Po włączeniu usługi nie można przenieść AD DS platformy Azure do innej sieci wirtualnej.

Domena zarządzana AD DS platformy Azure łączy się z podsiecią w sieci wirtualnej platformy Azure. Zaprojektuj tę podsieć dla AD DS platformy Azure z następującymi kwestiami:

* Usługę Azure AD DS należy wdrożyć w jej własnej podsieci. Nie używaj istniejącej podsieci lub podsieci bramy.
* Grupa zabezpieczeń sieci jest tworzona podczas wdrażania domeny zarządzanej AD DS platformy Azure. Ta sieciowa Grupa zabezpieczeń zawiera reguły wymagane do poprawnego komunikowania się z usługą.
    * Nie twórz ani nie używaj istniejącej sieciowej grupy zabezpieczeń z własnymi regułami niestandardowymi.
* Usługa Azure AD DS wymaga adresów IP 3-5. Upewnij się, że zakres adresów IP podsieci może podawać tę liczbę adresów.
    * Ograniczanie dostępnych adresów IP może uniemożliwić Azure AD Domain Services utrzymywanie dwóch kontrolerów domeny.

Poniższy przykładowy diagram przedstawia prawidłowy projekt, w którym usługa Azure AD DS ma własną podsieć, jest podsiecią bramy dla łączności zewnętrznej, a obciążenia aplikacji znajdują się w podsieci w sieci wirtualnej:

![Zalecany projekt podsieci](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Połączenia z siecią wirtualną AD DS platformy Azure

Jak wspomniano w poprzedniej sekcji, można utworzyć tylko Azure AD Domain Services domenę zarządzaną w jednej sieci wirtualnej na platformie Azure i można utworzyć tylko jedną domenę zarządzaną dla dzierżawy usługi Azure AD. W oparciu o tę architekturę może być konieczne połączenie przynajmniej jednej sieci wirtualnej, która hostuje obciążenia aplikacji w sieci wirtualnej platformy Azure AD DS.

Możesz połączyć obciążenia aplikacji hostowane w innych sieciach wirtualnych platformy Azure, korzystając z jednej z następujących metod:

* Wirtualne sieci równorzędne
* Wirtualne sieci prywatne (VPN)

### <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna sieci wirtualnych jest mechanizmem, który łączy dwie sieci wirtualne w tym samym regionie za pomocą sieci szkieletowej platformy Azure. Globalne wirtualne sieci równorzędne mogą łączyć się z siecią wirtualną w różnych regionach platformy Azure. Po nawiązaniu połączenia równorzędnego dwie sieci wirtualne zezwalają na zasoby, takie jak maszyny wirtualne, komunikują się ze sobą bezpośrednio przy użyciu prywatnych adresów IP. Za pomocą komunikacji równorzędnej sieci wirtualnej można wdrożyć domenę zarządzaną platformy Azure AD DS przy użyciu obciążeń aplikacji wdrożonych w innych sieciach wirtualnych.

![Połączenie sieci wirtualnej przy użyciu komunikacji równorzędnej](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Aby uzyskać więcej informacji, zobacz temat [Omówienie komunikacji równorzędnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Wirtualna sieć prywatna (VPN)

Można połączyć sieć wirtualną z inną siecią wirtualną (VNet-to-VNet) w taki sam sposób, w jaki można skonfigurować sieć wirtualną do lokalizacji lokacji lokalnej. Oba połączenia wykorzystują bramę sieci VPN do tworzenia bezpiecznego tunelu przy użyciu protokołu IPsec/IKE. Ten model połączenia umożliwia wdrażanie AD DS platformy Azure w sieci wirtualnej platformy Azure, a następnie łączenie lokalizacji lokalnych lub innych chmur.

![Łączność sieci wirtualnej przy użyciu VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Aby uzyskać więcej informacji o korzystaniu z wirtualnej sieci prywatnej, przeczytaj temat [Konfigurowanie połączenia bramy sieci VPN typu sieć wirtualna-sieć wirtualna za pomocą Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Rozpoznawanie nazw podczas łączenia sieci wirtualnych

Sieci wirtualne połączone z siecią wirtualną Azure AD Domain Services zwykle mają własne ustawienia DNS. Po połączeniu z sieciami wirtualnymi nie konfiguruje się automatycznie rozpoznawania nazw dla połączenia sieci wirtualnej w celu rozpoznania usług dostarczonych przez domenę zarządzaną platformy Azure AD DS. Rozpoznawanie nazw w połączonych sieciach wirtualnych musi być skonfigurowane tak, aby umożliwić obciążom aplikacji lokalizowanie Azure AD Domain Services.

Rozpoznawanie nazw można włączyć za pomocą warunkowych usług przesyłania dalej DNS na serwerze DNS obsługującym łączenie sieci wirtualnych lub przy użyciu tych samych adresów IP DNS z sieci wirtualnej usługi Azure AD Domain Service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Zasoby sieciowe używane przez AD DS platformy Azure

W przypadku domeny zarządzanej AD DS platformy Azure tworzone są pewne zasoby sieciowe podczas wdrażania. Te zasoby są odpowiednie do pomyślnego działania i zarządzania domeną zarządzaną platformy Azure AD DS i nie powinny być konfigurowane ręcznie.

| Zasób platformy Azure                          | Opis |
|:----------------------------------------|:---|
| Karta sieciowa                  | Usługa Azure AD DS hostuje domenę zarządzaną na dwóch kontrolerach domeny (DC) działających w systemie Windows Server jako maszyn wirtualnych platformy Azure. Każda maszyna wirtualna ma interfejs sieci wirtualnej, który łączy się z podsiecią sieci wirtualnej. |
| Dynamiczny publiczny adres IP w warstwie Standardowa      | Usługa Azure AD DS komunikuje się z usługą synchronizacji i zarządzania przy użyciu publicznego adresu IP jednostki SKU. Aby uzyskać więcej informacji o publicznych adresach IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Moduł równoważenia obciążenia w warstwie Standardowa platformy Azure            | Usługa Azure AD DS korzysta ze standardowego modułu równoważenia obciążenia jednostki SKU do translacji adresów sieciowych (NAT) i równoważenia obciążenia (gdy jest używany z bezpiecznym protokołem LDAP). Aby uzyskać więcej informacji na temat modułów równoważenia obciążenia platformy Azure, zobacz [co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Reguły translatora adresów sieciowych (NAT) | Usługa Azure AD DS tworzy i używa trzech reguł translatora adresów sieciowych w ramach modułu równoważenia obciążenia — jednej reguły dla bezpiecznego ruchu HTTP i dwóch reguł bezpiecznego komunikacji zdalnej programu PowerShell. |
| Reguły modułu równoważenia obciążenia                     | W przypadku skonfigurowania domeny zarządzanej AD DS platformy Azure pod kątem bezpiecznego protokołu LDAP na porcie TCP 636, trzy reguły są tworzone i używane w module równoważenia obciążenia do dystrybucji ruchu. |

> [!WARNING]
> Nie usuwaj ani nie modyfikuj żadnego zasobu sieciowego utworzonego za pomocą usługi Azure AD DS, na przykład ręcznego konfigurowania modułu równoważenia obciążenia lub reguł. W przypadku usunięcia lub zmodyfikowania dowolnego z zasobów sieciowych może wystąpić awaria usługi Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Sieciowe grupy zabezpieczeń i wymagane porty

[Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)](../virtual-network/virtual-networks-nsg.md) zawiera listę reguł, które zezwalają na ruch sieciowy w sieci wirtualnej platformy Azure lub odmawiają go. Po wdrożeniu usługi Azure AD DS zawierającej zestaw reguł, które umożliwiają usłudze udostępnianie funkcji uwierzytelniania i zarządzania, tworzona jest sieciowa Grupa zabezpieczeń. Ta domyślna grupa zabezpieczeń sieci jest skojarzona z podsiecią sieci wirtualnej, w której wdrożono domenę zarządzaną platformy Azure AD DS.

Następujące reguły sieciowej grupy zabezpieczeń są wymagane dla usługi Azure AD DS, aby zapewnić uwierzytelnianie i usługi zarządzania. Nie Edytuj ani nie usuwaj tych reguł sieciowej grupy zabezpieczeń dla podsieci sieci wirtualnej, w której wdrożono domenę zarządzaną platformy Azure AD DS.

| Numer portu | Protocol (Protokół) | Element źródłowy                             | Element docelowy | Akcja | Wymagany | Przeznaczenie |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Dowolne         | Zezwalaj  | Tak      | Synchronizacja z dzierżawą usługi Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Dowolne         | Zezwalaj  | Tak      | Zarządzanie domeną. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Dowolne         | Zezwalaj  | Tak      | Zarządzanie domeną. |

> [!WARNING]
> Nie edytuj ręcznie tych zasobów sieciowych i konfiguracji. W przypadku kojarzenia nieskonfigurowanej grupy zabezpieczeń sieci lub tabeli tras zdefiniowanych przez użytkownika z podsiecią, w której wdrożono AD DS platformy Azure, możesz przerwać możliwość usługi i zarządzania domeną przez firmę Microsoft. Nieprzerwana synchronizacja dzierżawy usługi Azure AD i domeny zarządzanej platformy Azure AD DS.
>
> W przypadku korzystania z bezpiecznego protokołu LDAP można dodać wymaganą regułę portu 636 protokołu TCP, aby zezwolić na ruch zewnętrzny w razie potrzeby. Dodanie tej reguły nie powoduje umieszczenia w nieobsługiwanym stanie reguł sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [blokowanie bezpiecznego dostępu do protokołu LDAP za pośrednictwem Internetu](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet) .
>
> Dla sieciowej grupy zabezpieczeń istnieje również reguła domyślna dla *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*i *DenyAllOutBound* . Nie należy edytować ani usuwać tych domyślnych reguł.
>
> Umowa SLA platformy Azure nie ma zastosowania do wdrożeń, w których zastosowano nieprawidłowo skonfigurowaną tabelę zabezpieczeń sieci i/lub zdefiniowane przez użytkownika tabele tras, które blokują platformę Azure AD DS aktualizowania i zarządzania domeną.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 — synchronizacja z usługą Azure AD

* Służy do synchronizowania dzierżawy usługi Azure AD z domeną zarządzaną platformy Azure AD DS.
* Bez dostępu do tego portu domena zarządzana AD DS platformy Azure nie może zostać zsynchronizowana z dzierżawą usługi Azure AD. Użytkownicy mogą nie być w stanie zalogować się, ponieważ zmiany hasła nie będą synchronizowane z domeną zarządzaną platformy Azure AD DS.
* Dostęp przychodzący do tego portu do adresów IP jest domyślnie ograniczony przy użyciu znacznika usługi **AzureActiveDirectoryDomainServices** .
* Nie ograniczaj dostępu wychodzącego z tego portu.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 — zarządzanie przy użyciu pulpitu zdalnego

* Używany do połączeń pulpitu zdalnego z kontrolerami domeny w domenie zarządzanej platformy Azure AD DS.
* Domyślna reguła grupy zabezpieczeń sieci używa znacznika usługi *CorpNetSaw* w celu dodatkowego ograniczenia ruchu.
    * Ten tag usługi zezwala na dostęp do domeny zarządzanej za pomocą usługi Azure AD DS tylko bezpiecznym stacjom roboczym dostępu w sieci firmowej firmy Microsoft.
    * Dostęp jest dozwolony tylko w przypadku uzasadnienia biznesowego, na przykład w przypadku scenariuszy zarządzania lub rozwiązywania problemów.
* Dla tej reguły można ustawić wartość *Odmów*i ustawić wartość *Zezwalaj* tylko wtedy, gdy jest to wymagane. Większość zadań związanych z zarządzaniem i monitorowaniem odbywa się przy użyciu komunikacji zdalnej programu PowerShell. Protokół RDP jest używany tylko w rzadkich przypadkach, gdy firma Microsoft musi połączyć się zdalnie z domeną zarządzaną w celu zaawansowania rozwiązywania problemów.

> [!NOTE]
> Nie można ręcznie wybrać znacznika usługi *CorpNetSaw* w portalu, jeśli próbujesz edytować tę regułę sieciowej grupy zabezpieczeń. Musisz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby ręcznie skonfigurować regułę, która używa znacznika usługi *CorpNetSaw* .

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 — zarządzanie przy użyciu komunikacji zdalnej programu PowerShell

* Służy do wykonywania zadań zarządzania przy użyciu komunikacji zdalnej programu PowerShell w domenie zarządzanej AD DS platformy Azure.
* Bez dostępu do tego portu domeny zarządzanej AD DS platformy Azure nie można zaktualizować, skonfigurować, utworzyć kopii zapasowej ani monitorować.
* W przypadku domen zarządzanych AD DS platformy Azure, które korzystają z sieci wirtualnej opartej na Menedżer zasobów, można ograniczyć dostęp przychodzący do tego portu do znacznika usługi *AzureActiveDirectoryDomainServices* .
    * W przypadku starszych domen zarządzanych przez platformę Azure AD DS przy użyciu klasycznej sieci wirtualnej można ograniczyć dostęp przychodzący do tego portu do następujących źródłowych adresów IP: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*i *104.40.87.209*.

    > [!NOTE]
    > W 2017 Azure AD Domain Services stało się dostępne do hostowania w sieci Azure Resource Manager. Od tego czasu mogliśmy stworzyć bezpieczniejsze usługi przy użyciu nowoczesnych możliwości Azure Resource Manager. Ponieważ wdrożenia Azure Resource Manager w pełni zastępują wdrożenia klasyczne, wdrożenia usługi Azure AD DS klasycznej sieci wirtualnej zostaną wycofane 1 marca 2023.
    >
    > Aby uzyskać więcej informacji, zobacz [oficjalne powiadomienie o zaniechaniu](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Trasy definiowane przez użytkownika

Trasy zdefiniowane przez użytkownika nie są domyślnie tworzone i nie są potrzebne do poprawnego działania usługi Azure AD DS. Jeśli wymagane jest użycie tabel tras, unikaj wprowadzania jakichkolwiek zmian w marszrucie *0.0.0.0* . Zmiany w tej trasie zakłócają Azure AD Domain Services i umieszczają domenę zarządzaną w nieobsługiwanym stanie.

Należy również skierować ruch przychodzący z adresów IP uwzględnionych w odpowiednich tagach usługi platformy Azure do podsieci Azure AD Domain Services. Aby uzyskać więcej informacji na temat tagów usługi i skojarzonych z nimi adresów IP, zobacz [zakres adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Te zakresy adresów IP centrum danych platformy Azure mogą ulec zmianie bez powiadomienia. Upewnij się, że masz procesy umożliwiające zweryfikowanie, że masz najnowsze adresy IP.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat niektórych zasobów sieciowych i opcji połączenia używanych przez usługę Azure AD DS, zobacz następujące artykuły:

* [Komunikacja równorzędna sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md)
* [Bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Sieciowe grupy zabezpieczeń platformy Azure](../virtual-network/security-overview.md)

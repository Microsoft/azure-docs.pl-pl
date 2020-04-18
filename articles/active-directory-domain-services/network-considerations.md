---
title: Planowanie sieci i połączenia usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się więcej o niektórych zagadnieniach dotyczących projektowania sieci wirtualnej i zasobach używanych do łączności podczas uruchamiania Usług domenowych Active Directory platformy Azure.
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
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639989"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Zagadnienia dotyczące projektowania sieci wirtualnej i opcje konfiguracji usług domenowych usługi Azure AD

Ponieważ usługi domenowe Active Directory (AD DS) platformy Azure zapewniają usługi uwierzytelniania i zarządzania innym aplikacjom i obciążeniom, łączność sieciowa jest kluczowym składnikiem. Bez poprawnie skonfigurowanych zasobów sieci wirtualnej aplikacje i obciążenia nie mogą komunikować się z funkcjami udostępnianym przez usługę Azure AD DS i korzystać z niego. Zaplanuj wymagania dotyczące sieci wirtualnej, aby upewnić się, że usługi Azure AD DS mogą obsługiwać aplikacje i obciążenia w razie potrzeby.

W tym artykule opisano zagadnienia dotyczące projektu i wymagania dotyczące sieci wirtualnej platformy Azure do obsługi usług Azure AD DS.

## <a name="azure-virtual-network-design"></a>Projektowanie sieci wirtualnej platformy Azure

Aby zapewnić łączność sieciową i zezwolić aplikacjom i usługom na uwierzytelnianie za pomocą usług Azure AD DS, należy użyć sieci wirtualnej platformy Azure i podsieci. W idealnym przypadku usługi Azure AD DS należy wdrożyć w własnej sieci wirtualnej. W tej samej sieci wirtualnej można dołączyć oddzielną podsieć aplikacji do obsługi zarządzania maszyną wirtualną lub lekkimi obciążeniami aplikacji. Oddzielna sieć wirtualna dla większych lub złożonych obciążeń aplikacji, równorzędna z siecią wirtualną usługi Azure AD DS, jest zwykle najbardziej odpowiednim projektem. Inne opcje projektów są prawidłowe, pod warunkiem spełnienia wymagań opisanych w poniższych sekcjach dla sieci wirtualnej i podsieci.

Podczas projektowania sieci wirtualnej dla usług Azure AD DS stosuje się następujące zagadnienia:

* Usługi Azure AD DS muszą być wdrażane w tym samym regionie platformy Azure co sieć wirtualna.
    * W tej chwili można wdrożyć tylko jedną domenę zarządzana usługą Azure AD DS na dzierżawę usługi Azure AD. Domena zarządzana usługą Azure AD DS jest wdrażana w jednym regionie. Upewnij się, że tworzysz lub wybierasz sieć wirtualną w [regionie obsługującym usługi Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Należy wziąć pod uwagę bliskość innych regionów platformy Azure i sieci wirtualnych, które hostuje obciążenia aplikacji.
    * Aby zminimalizować opóźnienia, należy zachować podstawowe aplikacje blisko lub w tym samym regionie, co podsieć sieci wirtualnej domeny zarządzanej usługi Azure AD DS. Można użyć połączeń komunikacji równorzędnej sieci wirtualnej lub wirtualnej sieci prywatnej (VPN) między sieciami wirtualnymi platformy Azure. Te opcje połączenia zostały omówione w poniższej sekcji.
* Sieć wirtualna nie może polegać na usługach DNS innych niż te udostępniane przez usługi Azure AD DS.
    * Usługa Azure AD DS udostępnia własną usługę DNS. Sieć wirtualna musi być skonfigurowana do używania tych adresów usługi DNS. Rozpoznawanie nazw dla dodatkowych obszarów nazw można wykonać za pomocą usługi przesyłania dalej warunkowej.
    * Niestandardowe ustawienia serwera DNS nie są używane do kierowania zapytań z innych serwerów DNS, w tym z maszyn wirtualnych. Zasoby w sieci wirtualnej muszą korzystać z usługi DNS świadczonej przez usługi Azure AD DS.

> [!IMPORTANT]
> Po włączeniu usługi nie można przenieść usług Azure AD DS do innej sieci wirtualnej.

Domena zarządzana usługą Azure AD DS łączy się z podsiecią w sieci wirtualnej platformy Azure. Zaprojektuj tę podsieć dla usług Azure AD DS z następującymi zagadnieniami:

* Usługi Azure AD DS muszą być wdrażane we własnej podsieci. Nie używaj istniejącej podsieci ani podsieci bramy.
* Sieciowa grupa zabezpieczeń jest tworzona podczas wdrażania domeny zarządzanej usług Ad DS. Ta sieciowa grupa zabezpieczeń zawiera reguły wymagane dla poprawnej komunikacji usługi.
    * Nie należy tworzyć ani używać istniejącej sieciowej grupy zabezpieczeń z własnymi regułami niestandardowymi.
* Usługi Azure AD DS wymaga 3-5 adresów IP. Upewnij się, że zakres adresów IP podsieci może podać tę liczbę adresów.
    * Ograniczenie dostępnych adresów IP może uniemożliwić usługom domenowym usługi Azure AD utrzymywanie dwóch kontrolerów domeny.

Na poniższym przykładzie przedstawiono prawidłowy projekt, w którym usługa Azure AD DS ma własną podsieć, istnieje podsieć bramy dla łączności zewnętrznej, a obciążenia aplikacji znajdują się w podłączonej podsieci w sieci wirtualnej:

![Zalecany projekt podsieci](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Połączenia z siecią wirtualną usług Ad DS platformy Azure

Jak wspomniano w poprzedniej sekcji, można utworzyć domenę zarządzaną usług ad ad azure tylko w jednej sieci wirtualnej na platformie Azure i tylko jedna domena zarządzana może być utworzona dla dzierżawy usługi Azure AD. Na podstawie tej architektury może być konieczne połączenie jednej lub więcej sieci wirtualnych, które hostuje obciążenia aplikacji do sieci wirtualnej usług Ad DS.

Można połączyć obciążenia aplikacji hostowane w innych sieciach wirtualnych platformy Azure przy użyciu jednej z następujących metod:

* Wirtualne sieci równorzędne
* Wirtualne sieci prywatne (VPN)

### <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna sieci wirtualnej to mechanizm, który łączy dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci szkieletowej platformy Azure. Globalna komunikacja równorzędna sieci wirtualnej może łączyć sieć wirtualną w regionach platformy Azure. Po elementów równorzędnych dwie sieci wirtualne umożliwiają zasoby, takie jak maszyny wirtualne, komunikują się ze sobą bezpośrednio przy użyciu prywatnych adresów IP. Za pomocą komunikacji równorzędnej sieci wirtualnej umożliwia wdrożenie domeny zarządzanej usługi Azure AD DS z obciążeniami aplikacji wdrożonymi w innych sieciach wirtualnych.

![Łączność z siecią wirtualną przy użyciu komunikacji równorzędnej](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Aby uzyskać więcej informacji, zobacz [omówienie komunikacji równorzędnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Wirtualna sieć prywatna (VPN)

Sieć wirtualną można połączyć z inną siecią wirtualną (sieć wirtualna-sieć wirtualna) w taki sam sposób, w jaki można skonfigurować sieć wirtualną do lokalizacji lokacji lokalnej. Oba połączenia używają bramy sieci VPN do tworzenia bezpiecznego tunelu przy użyciu protokołu IPsec/IKE. Ten model połączenia umożliwia wdrażanie usług Azure AD DS w sieci wirtualnej platformy Azure, a następnie łączenie lokalizacji lokalnych lub innych chmur.

![Łączność z siecią wirtualną przy użyciu bramy sieci VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Aby uzyskać więcej informacji na temat korzystania z wirtualnej sieci prywatnej, zobacz [Konfigurowanie połączenia bramy sieci VPN z siecią wirtualną przy użyciu portalu Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Rozpoznawanie nazw podczas łączenia sieci wirtualnych

Sieci wirtualne połączone z siecią wirtualną usług domenowych usługi AD azure zazwyczaj mają własne ustawienia DNS. Po podłączeniu sieci wirtualnych nie jest automatycznie konfigurować rozpoznawanie nazw dla łączącej się sieci wirtualnej w celu rozpoznawania usług świadczonych przez domenę zarządzaną usługami Usługi Azure AD DS. Rozpoznawanie nazw w sieciach wirtualnych łączących się musi być skonfigurowane tak, aby umożliwić obciążeń aplikacji, aby zlokalizować usługi domenowe usługi Azure AD Domain Services.

Rozpoznawanie nazw można włączyć przy użyciu warunkowych usług przesyłania dalej DNS na serwerze DNS obsługującym łączące się sieci wirtualne lub przy użyciu tych samych adresów IP DNS z sieci wirtualnej usługi azure ad.

## <a name="network-resources-used-by-azure-ad-ds"></a>Zasoby sieciowe używane przez usługi Azure AD DS

Domena zarządzana usługą Azure AD DS tworzy pewne zasoby sieciowe podczas wdrażania. Te zasoby są potrzebne do pomyślnego działania i zarządzania domeną zarządzaną usługą Azure AD DS i nie powinny być konfigurowane ręcznie.

| Zasób platformy Azure                          | Opis |
|:----------------------------------------|:---|
| Karta interfejsu sieciowego                  | Usługi Azure AD DS hostują domenę zarządzaną na dwóch kontrolerach domeny (DC), które są uruchamiane w systemie Windows Server jako maszyny wirtualne platformy Azure. Każda maszyna wirtualna ma interfejs sieci wirtualnej, który łączy się z podsiecią sieci wirtualnej. |
| Dynamiczny standardowy publiczny adres IP      | Usługi Azure AD DS komunikują się z usługą synchronizacji i zarządzania przy użyciu standardowego publicznego adresu IP jednostki SKU. Aby uzyskać więcej informacji na temat publicznych adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Standardowy moduł równoważenia obciążenia platformy Azure            | Usługi Azure AD DS używa standardowego modułu równoważenia obciążenia usługi SKU do translacji adresów sieciowych (NAT) i równoważenia obciążenia (w przypadku użycia z bezpiecznym programem LDAP). Aby uzyskać więcej informacji na temat równoważenia obciążenia platformy Azure, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](../load-balancer/load-balancer-overview.md) |
| Reguły translacji adresów sieciowych (NAT) | Usługa Azure AD DS tworzy i używa trzech reguł natów na adres NAT na moduł równoważenia obciążenia — jedna reguła dla bezpiecznego ruchu HTTP i dwie reguły dla bezpiecznego zdalnego tworzenia programu PowerShell. |
| Reguły modułu równoważenia obciążenia                     | Gdy domena zarządzana usługą Azure AD DS jest skonfigurowana do bezpiecznego protokołu LDAP w porcie TCP 636, trzy reguły są tworzone i używane na modułu równoważenia obciążenia do dystrybucji ruchu. |

> [!WARNING]
> Nie usuwaj ani nie modyfikuj żadnego zasobu sieciowego utworzonego przez usługi Azure AD DS, na przykład ręcznego konfigurowania modułu równoważenia obciążenia lub reguł. Jeśli usuniesz lub zmodyfikujesz którykolwiek z zasobów sieciowych, może wystąpić awaria usługi Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Sieciowe grupy zabezpieczeń i wymagane porty

[Sieciowa grupa zabezpieczeń (NSG)](../virtual-network/virtual-networks-nsg.md) zawiera listę reguł, które zezwalają lub odmawiają ruchu sieciowego ruchowi w sieci wirtualnej platformy Azure. Sieciowa grupa zabezpieczeń jest tworzona podczas wdrażania usług Azure AD DS, która zawiera zestaw reguł, które umożliwiają usłudze świadczenie funkcji uwierzytelniania i zarządzania. Ta domyślna grupa zabezpieczeń sieci jest skojarzona z podsiecią sieci wirtualnej, w zarządzanej przez usługę Azure AD DS jest wdrażana.

Następujące reguły sieciowej grupy zabezpieczeń są wymagane dla usług Azure AD DS w celu świadczenia usług uwierzytelniania i zarządzania. Nie edytuj ani nie usuwaj tych reguł sieciowej grupy zabezpieczeń dla podsieci sieci wirtualnej, w których wdrożona jest domena zarządzana usługą Azure AD DS.

| Numer portu | Protocol (Protokół) | Element źródłowy                             | Element docelowy | Akcja | Wymagany | Przeznaczenie |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | Usługi AzureActiveDirectoryDomainServices | Dowolne         | Zezwalaj  | Tak      | Synchronizacja z dzierżawą usługi Azure AD. |
| 3389        | TCP      | Piła corpNet                         | Dowolne         | Zezwalaj  | Tak      | Zarządzanie domeną. |
| 5986        | TCP      | Usługi AzureActiveDirectoryDomainServices | Dowolne         | Zezwalaj  | Tak      | Zarządzanie domeną. |

> [!WARNING]
> Nie edytuj ręcznie tych zasobów sieciowych i konfiguracji. Po skojarzeniu nieprawidłowo skonfigurowaną grupę zabezpieczeń sieci lub tabelę tras zdefiniowaną przez użytkownika z podsiecią, w której wdrożono usługi Azure AD DS, możesz zakłócić zdolność firmy Microsoft do obsługi domeny i zarządzania nią. Synchronizacja między dzierżawą usługi Azure AD a domeną zarządzana usługą Azure AD DS również została zakłócona.
>
> Jeśli używasz bezpiecznego protokołu LDAP, możesz dodać wymaganą regułę portu TCP 636, aby w razie potrzeby zezwolić na ruch zewnętrzny. Dodanie tej reguły nie powoduje umieszczenia reguł sieciowej grupy zabezpieczeń w stanie nieobsługiwał. Aby uzyskać więcej informacji, zobacz [Blokowanie bezpiecznego dostępu LDAP przez Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Domyślne reguły *dla AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*i *DenyAllOutBound* istnieją również dla sieciowej grupy zabezpieczeń. Nie edytuj ani nie usuwaj tych reguł domyślnych.
>
> Umowa SLA platformy Azure nie ma zastosowania do wdrożeń, w których zastosowano nieprawidłowo skonfigurowaną grupę zabezpieczeń sieci i/lub tabele tras zdefiniowane przez użytkownika, które uniemożliwiają usługi Azure AD DS aktualizowanie domeny i zarządzanie nią.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 — synchronizacja z usługą Azure AD

* Służy do synchronizowania dzierżawy usługi Azure AD z domeną zarządzaną usługą Azure AD DS.
* Bez dostępu do tego portu domena zarządzana usługą Azure AD DS nie może być synchronizowana z dzierżawą usługi Azure AD. Użytkownicy mogą nie być w stanie zalogować się, ponieważ zmiany haseł nie zostaną zsynchronizowane z domeną zarządzaną usługą Azure AD DS.
* Dostęp przychodzący do tego portu do adresów IP jest domyślnie ograniczony przy użyciu tagu usługi **AzureActiveDirectoryDomainServices.**
* Nie należy ograniczać dostępu wychodzącego z tego portu.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 - zarządzanie za pomocą pulpitu zdalnego

* Używane do połączeń pulpitu zdalnego z kontrolerami domeny w domenie zarządzanej usług Azure AD DS.
* Domyślna reguła sieciowej grupy zabezpieczeń używa tagu usługi *CorpNetSaw* do dalszego ograniczania ruchu.
    * Ten tag usługi zezwala tylko na bezpieczne stacje robocze dostępu w sieci firmowej firmy Microsoft do używania pulpitu zdalnego do domeny zarządzanej usług Azure AD DS.
    * Dostęp jest dozwolony tylko z uzasadnieniem biznesowym, na przykład w scenariuszach zarządzania lub rozwiązywania problemów.
* Tę regułę można ustawić na *Odmów*i ustawić tylko *na Zezwalaj* tylko wtedy, gdy jest to wymagane. Większość zadań zarządzania i monitorowania są wykonywane przy użyciu programu PowerShell komunikacji zdalnej. RDP jest używany tylko w rzadkich przypadkach, że firma Microsoft musi połączyć się zdalnie z domeną zarządzaną w celu zaawansowanego rozwiązywania problemów.

> [!NOTE]
> Nie można ręcznie wybrać tagu usługi *CorpNetSaw* z portalu, jeśli spróbujesz edytować tę regułę sieciowej grupy zabezpieczeń. Aby ręcznie skonfigurować regułę używaną przez usługę *CorpNetSaw,* należy użyć programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 - zarządzanie za pomocą komunikacji zdalnej programu PowerShell

* Służy do wykonywania zadań zarządzania przy użyciu programu PowerShell komunikacji zdalnej w domenie zarządzanej usług Azure AD DS.
* Bez dostępu do tego portu domena zarządzana usługą Azure AD DS nie może być aktualizowana, konfigurowana, archiwizamia lub monitorowana.
* W przypadku domen zarządzanych usług Azure AD DS korzystających z sieci wirtualnej opartej na Menedżerze zasobów można ograniczyć dostęp przychodzący do tego portu do tagu usługi *AzureActiveDirectoryDomainServices.*
    * W przypadku starszych domen zarządzanych usług Azure AD DS przy użyciu sieci wirtualnej opartej na systemie klasycznym można ograniczyć dostęp przychodzący do tego portu do następujących źródłowych adresów IP: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *1 3.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*i *104.40.87.209*.

    > [!NOTE]
    > W 2017 r. usługi domenowe usługi ad usługi Azure AD stały się dostępne do hosta w sieci usługi Azure Resource Manager. Od tego czasu udało nam się zbudować bezpieczniejszą usługę przy użyciu nowoczesnych możliwości usługi Azure Resource Manager. Ponieważ wdrożenia usługi Azure Resource Manager w pełni zastępują klasyczne wdrożenia, klasyczne wdrożenia sieci wirtualnej usługi Azure AD DS zostaną wycofane 1 marca 2023 r.
    >
    > Aby uzyskać więcej informacji, zobacz [oficjalne zawiadomienie o umoriwanie](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Trasy definiowane przez użytkownika

Trasy zdefiniowane przez użytkownika nie są tworzone domyślnie i nie są potrzebne do poprawnego działania usług Azure AD DS. Jeśli wymagane jest użycie tabel tras, należy unikać wprowadzania zmian w trasie *0.0.0.0.* Zmiany w tej trasie zakłócają usługi domenowe usługi Azure AD i umieszczają domenę zarządzaną w stanie nieobsługiwać.

Należy również kierować ruch przychodzący z adresów IP uwzględnionych w odpowiednich tagach usługi platformy Azure do podsieci usług domenowych usługi azure ad. Aby uzyskać więcej informacji na temat tagów usług i skojarzonego z nimi adresu IP, zobacz [Zakresy adresów IP platformy Azure i tagi usług — Chmura publiczna](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Te zakresy adresów IP centrum danych platformy Azure można zmienić bez powiadomienia. Upewnij się, że masz procesy, aby sprawdzić, czy masz najnowsze adresy IP.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat niektórych zasobów sieciowych i opcji połączeń używanych przez usługi Azure AD DS, zobacz następujące artykuły:

* [Komunikacja równorzędna sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md)
* [Bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Sieciowe grupy zabezpieczeń platformy Azure](../virtual-network/security-overview.md)

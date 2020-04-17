---
title: Połączenie hybrydowe z aplikacją dwuwarstwową | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć urządzenia wirtualne i UDR w celu utworzenia wielowarstwowego środowiska aplikacji na platformie Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 80a9397838e90a2af504125b2dc4c4ef39251d4e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455366"
---
# <a name="virtual-appliance-scenario"></a>Scenariusz urządzenia wirtualnego
Typowym scenariuszem wśród większych klientów platformy Azure jest konieczność zapewnienia dwuwarstwowej aplikacji udostępnianej w Internecie, umożliwiając jednocześnie dostęp do warstwy tylnej z lokalnego centrum danych. W tym dokumencie zostanie przedstawiona przez scenariusz przy użyciu tras zdefiniowanych przez użytkownika (UDR), bramy sieci VPN i wirtualnych urządzeń sieciowych w celu wdrożenia środowiska dwuwarstwowego, które spełnia następujące wymagania:

* Aplikacja sieci Web musi być dostępna tylko z publicznego Internetu.
* Serwer sieci Web obsługujący aplikację musi mieć dostęp do serwera aplikacji wewnętrznej bazy danych.
* Cały ruch z Internetu do aplikacji sieci web musi przejść przez urządzenie wirtualne zapory. To urządzenie wirtualne będzie używane tylko dla ruchu internetowego.
* Cały ruch przechodzący do serwera aplikacji musi przejść przez urządzenie wirtualne zapory. To urządzenie wirtualne będzie używane do uzyskiwania dostępu do serwera zaplecza i dostępu przychodzącego z sieci lokalnej za pośrednictwem bramy sieci VPN.
* Administratorzy muszą mieć możliwość zarządzania urządzeniami wirtualnymi zapory z ich komputerów lokalnych przy użyciu trzeciego urządzenia wirtualnego zapory używanego wyłącznie do celów zarządzania.

Jest to standardowy scenariusz sieci obwodowej (znany również jako DMZ) z DMZ i siecią chroniona. Taki scenariusz można skonstruować na platformie Azure przy użyciu sieci NSG, zapory urządzeń wirtualnych lub kombinacji obu. W poniższej tabeli przedstawiono niektóre plusy i minusy między sieciami sieciowymi a urządzeniami wirtualnymi zapory.

|  | Zalety | Wady |
| --- | --- | --- |
| Sieciowa grupa zabezpieczeń |Bez kosztów. <br/>Zintegrowana z usługą Azure RBAC. <br/>Reguły można tworzyć w szablonach usługi Azure Resource Manager. |Złożoność może się różnić w większych środowiskach. |
| Zapora |Pełna kontrola nad płaszczyzną danych. <br/>Centralne zarządzanie za pomocą konsoli zapory. |Koszt urządzenia zapory. <br/>Nie jest zintegrowany z usługą Azure RBAC. |

Poniższe rozwiązanie używa urządzeń wirtualnych zapory do zaimplementowania scenariusza sieci obwodowej (DMZ)/chronionej sieci.

## <a name="considerations"></a>Zagadnienia do rozważenia
Środowisko wyjaśnione powyżej na platformie Azure można wdrożyć przy użyciu różnych funkcji dostępnych obecnie, w następujący sposób.

* **Sieć wirtualna (VNet)**. Sieć wirtualna platformy Azure działa w podobny sposób do sieci lokalnej i może być podzielona na jedną lub więcej podsieci, aby zapewnić izolację ruchu i rozdzielenie problemów.
* **Urządzenie wirtualne**. Kilku partnerów udostępnia urządzenia wirtualne w portalu Azure Marketplace, które mogą być używane dla trzech zapór opisanych powyżej. 
* **Trasy zdefiniowane przez użytkownika (UDR)**. Tabele tras mogą zawierać udry używane przez sieć Azure do kontrolowania przepływu pakietów w sieci wirtualnej. Te tabele tras mogą być stosowane do podsieci. Jedną z najnowszych funkcji na platformie Azure jest możliwość zastosowania tabeli marszruty do sieci GatewaySubnet, zapewniając możliwość przekazywania całego ruchu przychodzącego do sieci wirtualnej platformy Azure z połączenia hybrydowego do urządzenia wirtualnego.
* **Przekazywanie IP**. Domyślnie aparat sieci platformy Azure przekazuje pakiety do kart interfejsu sieci wirtualnej (NIC) tylko wtedy, gdy docelowy adres IP pakietu jest zgodny z adresem IP karty sieciowej. W związku z tym jeśli UDR definiuje, że pakiet musi być wysyłany do danego urządzenia wirtualnego, aparat sieci platformy Azure spadnie tego pakietu. Aby upewnić się, że pakiet jest dostarczany do maszyny Wirtualnej (w tym przypadku urządzenia wirtualnego), który nie jest rzeczywistym miejscem docelowym dla pakietu, należy włączyć przekazywanie adresów IP dla urządzenia wirtualnego.
* **sieciowych grup zabezpieczeń .** Poniższy przykład nie korzysta z sieciowych grup zabezpieczeń, ale można użyć grup zabezpieczeń zastosowanych do podsieci i/lub kart sieciowych w tym rozwiązaniu w celu dalszego filtrowania ruchu do i z tych podsieci i kart sieciowych.

![Łączność IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

W tym przykładzie istnieje subskrypcja, która zawiera następujące elementy:

* 2 grupy zasobów, nie pokazano na diagramie. 
  * **ONPREMRG**. Zawiera wszystkie zasoby niezbędne do symulacji sieci lokalnej.
  * **AZURERG**. Zawiera wszystkie zasoby niezbędne dla środowiska sieci wirtualnej platformy Azure. 
* Sieci wirtualnej o nazwie **onpremvnet** używane do naśladowania lokalnego centrum danych segmentacji, jak podano poniżej.
  * **onpremsn1**. Podsieć zawierająca maszynę wirtualną (VM) z uruchomionym ubuntu w celu naśladowania serwera lokalnego.
  * **onpremsn2**. Podsieć zawierająca maszynę wirtualną z systemem Ubuntu w celu naśladowania komputera lokalnego używanego przez administratora.
* Istnieje jedno urządzenie wirtualne zapory o nazwie **OPFW** na **onpremvnet** używane do utrzymania tunelu do **azurevnet**.
* Sieci wirtualnej o nazwie **azurevnet** segmented jak na liście poniżej.
  * **azsn1**. Podsieć zapory zewnętrznej używana wyłącznie dla zapory zewnętrznej. Cały ruch internetowy będzie odbywał się za pośrednictwem tej podsieci. Ta podsieć zawiera tylko kartę sieciową połączony z zaporą zewnętrzną.
  * **azsn2**. Podsieć frontona internetowa obsługujący maszynę wirtualną działającą jako serwer sieci web, która będzie dostępna z Internetu.
  * **azsn3**. Podsieć zaplecza obsługująca maszynę wirtualną z uruchomionym serwerem aplikacji wewnętrznej bazy danych, do który będzie dostępny serwer sieci web frontowego.
  * **azsn4**. Podsieć zarządzania używana wyłącznie do zapewnienia dostępu do zarządzania wszystkimi urządzeniami wirtualnymi zapory. Ta podsieć zawiera tylko kartę sieciową dla każdego urządzenia wirtualnego zapory używanego w rozwiązaniu.
  * **Portal GatewaySubnet**. Podsieć połączenia hybrydowego platformy Azure wymagana dla usługi ExpressRoute i bramy sieci VPN zapewnia łączność między sieciami wirtualnymi platformy Azure a innymi sieciami. 
* W sieci **azurevnet** znajdują się 3 urządzenia wirtualne zapory. 
  * **AZF1**. Zewnętrzna zapora dostępna w publicznym Internecie przy użyciu zasobu publicznego adresu IP na platformie Azure. Należy upewnić się, że masz szablon z marketplace lub bezpośrednio od dostawcy urządzenia, który udostępnia urządzenie wirtualne 3-NIC.
  * **AZF2**. Wewnętrzna zapora służy do kontrolowania ruchu między **azsn2** i **azsn3**. Jest to również urządzenie wirtualne 3-NIC.
  * **AZF3**. Zapora zarządzania dostępna dla administratorów z lokalnego centrum danych i połączona z podsiecią zarządzania używaną do zarządzania wszystkimi urządzeniami zapory. Szablony urządzeń wirtualnych 2-NIC można znaleźć w portalu Marketplace lub zażądać ich bezpośrednio od dostawcy urządzenia.

## <a name="user-defined-routing-udr"></a>Routing zdefiniowany przez użytkownika (UDR)
Każda podsieć na platformie Azure może być połączona z tabelą UDR używaną do definiowania sposobu kierowania ruchu inicjowanego w tej podsieci. Jeśli nie są zdefiniowane żadne UDR, platforma Azure używa tras domyślnych, aby umożliwić przepływ ruchu z jednej podsieci do drugiej. Aby lepiej zrozumieć UDR, odwiedź [stronę Jakie są trasy zdefiniowane przez użytkownika i przekierowanie IP](virtual-networks-udr-overview.md).

Aby upewnić się, że komunikacja odbywa się za pośrednictwem odpowiedniego urządzenia zapory, na podstawie ostatniego wymagania powyżej, należy utworzyć następującą tabelę tras zawierającą UDR w **azurevnet**.

### <a name="azgwudr"></a>azgwudr
W tym scenariuszu tylko ruch płynący z lokalnego do platformy Azure będzie używany do zarządzania zaporami przez połączenie z **AZF3**, a ruch musi przejść przez wewnętrzną zaporę, **AZF2**. W związku z tym tylko jedna trasa jest konieczne w **GatewaySubnet,** jak pokazano poniżej.

| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Umożliwia ruchowi lokalnemu dotarcie do zapory zarządzania **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Zezwala na ruch do podsieci wewnętrznej bazy danych hostującej serwer aplikacji za pośrednictwem **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Umożliwia kierowanie całego innego ruchu przez **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Umożliwia ruch **do azsn2** przepływać z serwera aplikacji do serwera www przez **AZF2** |

Należy również utworzyć tabele tras dla podsieci w **onpremvnet** naśladować lokalnego centrum danych.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Umożliwia ruch **napremsn2** przez **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Zezwala na ruch do podsieci kopii zapasowej na platformie Azure za pośrednictwem **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Umożliwia ruch **napremsn1** przez **OPFW** |

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
UDR i przekierowanie IP to funkcje, których można używać w połączeniu, aby umożliwić urządzenia wirtualne do kontrolowania przepływu ruchu w sieci wirtualnej platformy Azure.  Urządzenie wirtualne to po prostu maszyna wirtualna, na której działa aplikacja służąca do obsługi ruchu sieciowego w określony sposób, na przykład zapora lub urządzenie NAT.

Ta maszyna wirtualna musi mieć zdolność odbierania ruchu przychodzącego, który nie jest skierowany do niej samej. Aby umożliwić maszynie wirtualnej odbieranie ruchu kierowanego do innych miejsc docelowych, konieczne jest włączenie dla tej maszyny wirtualnej funkcji przesyłania dalej IP. Jest to ustawienie platformy Azure, a nie systemu operacyjnego gościa. Urządzenie wirtualne nadal musi uruchomić jakiś typ aplikacji, aby obsłużyć ruch przychodzący i odpowiednio go rozsyłać.

Aby dowiedzieć się więcej o przekazywaniu adresów IP, odwiedź stronę [Jakie są trasy zdefiniowane przez użytkownika i przekierowanie IP](virtual-networks-udr-overview.md).

Na przykład załóżmy, że masz następującą konfigurację w sieci wirtualnej platformy Azure:

* **Podsieć onpremsn1** zawiera maszynę wirtualną o nazwie **onpremvm1**.
* **Podsieć onpremsn2** zawiera maszynę wirtualną o nazwie **onpremvm2**.
* Urządzenie wirtualne o nazwie **OPFW** jest podłączone do **onpremsn1** i **onpremsn2**.
* Trasa zdefiniowana przez użytkownika połączona z **onpremsn1** określa, że cały ruch **do onpremsn2** musi być wysyłany do **OPFW**.

W tym momencie, jeśli **onpremvm1** próbuje ustanowić połączenie z **onpremvm2**, UDR będzie używany i ruch zostanie wysłany do **OPFW** jako następny przeskok. Należy pamiętać, że rzeczywiste miejsce docelowe pakietu nie jest zmieniane, nadal mówi **onpremvm2** jest miejscem docelowym. 

Bez przekazywania adresów IP włączone dla **OPFW**logiki sieci wirtualnej platformy Azure spowoduje upuszczenie pakietów, ponieważ umożliwia pakiety, które mają być wysyłane do maszyny Wirtualnej, jeśli adres IP maszyny Wirtualnej jest miejscem docelowym dla pakietu.

Dzięki przekazywaniu adresów IP logika sieci wirtualnej platformy Azure będzie przesyłać dalej pakiety do OPFW, bez zmiany oryginalnego adresu docelowego. **OPFW** musi obsługiwać pakiety i określić, co z nimi zrobić.

Aby powyższy scenariusz działał, należy włączyć przekazywanie adresów IP na kartach sieciowych dla **OPFW,** **AZF1,** **AZF2**i **AZF3,** które są używane do routingu (wszystkie karty sieciowe z wyjątkiem sieci połączonych z podsiecią zarządzania). 

## <a name="firewall-rules"></a>Reguły zapory
Jak opisano powyżej, przekazywanie adresów IP zapewnia tylko pakiety są wysyłane do urządzeń wirtualnych. Urządzenie musi jeszcze zdecydować, co zrobić z tymi pakietami. W powyższym scenariuszu należy utworzyć następujące reguły w urządzeniach:

### <a name="opfw"></a>OPFW (opfw)
OPFW reprezentuje urządzenie lokalne zawierające następujące reguły:

* **Trasa**: Cały ruch do 10.0.0.0/16 (**azurevnet**) musi być wysyłany przez tunel **ONPREMAZURE**.
* **Zasady:** Zezwalaj na cały ruch dwukierunkowy między **portami2** a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1 ( AZF1 )
AZF1 reprezentuje urządzenie wirtualne platformy Azure zawierające następujące reguły:

* **Zasady:** Zezwalaj na cały ruch dwukierunkowy między **portem1** a **portem2**.

### <a name="azf2"></a>AZF2 (AZF2)
AZF2 reprezentuje urządzenie wirtualne platformy Azure zawierające następujące reguły:

* **Trasa:** Cały ruch do 10.0.0.0/16 (**onpremvnet**) musi być wysłany na adres IP bramy platformy Azure (tj. 10.0.0.1) przez **port1**.
* **Zasady:** Zezwalaj na cały ruch dwukierunkowy między **portem1** a **portem2**.

## <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń
W tym scenariuszu nie są używane cele zabezpieczeń. Można jednak zastosować sieciowe sieciowe do każdej podsieci, aby ograniczyć ruch przychodzący i wychodzący. Na przykład można zastosować następujące reguły sieciowej sieciowej sieciowej do zewnętrznej podsieci FW.

**Dane**

* Zezwalaj na cały ruch TCP z Internetu na port 80 na dowolnej maszynie wirtualnej w podsieci.
* Odmów wszelkiego innego ruchu z Internetu.

**Wychodzących**

* Odmów wszelkiego ruchu do Internetu.

## <a name="high-level-steps"></a>Kroki wysokiego poziomu
Aby wdrożyć ten scenariusz, wykonaj kroki wysokiego poziomu poniżej.

1. Zaloguj się do subskrypcji platformy Azure.
2. Jeśli chcesz wdrożyć sieć wirtualną, aby naśladować sieć lokalną, aprowizować zasoby, które są częścią **ONPREMRG**.
3. Aprowizuj zasoby, które są częścią **azurerg**.
4. Aprowizuj tunel z **onpremvnet** do **azurevnet**.
5. Po udostępnieniu wszystkich zasobów zaloguj się do **onpremvm2** i pingu 10.0.3.101, aby przetestować łączność między **onpremsn2** i **azsn3**.


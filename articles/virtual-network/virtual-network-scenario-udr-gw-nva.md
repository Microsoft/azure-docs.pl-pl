---
title: Połączenie hybrydowe z aplikacją dwuwarstwową | Microsoft Docs
description: Dowiedz się, jak wdrażać urządzenia wirtualne i UDR w celu utworzenia wielowarstwowego środowiska aplikacji na platformie Azure
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
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231713"
---
# <a name="virtual-appliance-scenario"></a>Scenariusz urządzenia wirtualnego
Typowy scenariusz między większym klientem platformy Azure to konieczność zapewnienia dwuwarstwowej aplikacji uwidocznionej w Internecie, dzięki czemu można uzyskać dostęp do warstwy back z lokalnego centrum danych. Ten dokument przeprowadzi Cię przez scenariusz przy użyciu zdefiniowanych przez użytkownika tras (UDR), VPN Gateway i wirtualnych urządzeń sieciowych do wdrożenia środowiska dwuwarstwowego spełniającego następujące wymagania:

* Aplikacja sieci Web musi być dostępna tylko z publicznej sieci Internet.
* Serwer sieci Web hostujący aplikację musi mieć możliwość uzyskania dostępu do serwera aplikacji zaplecza.
* Cały ruch z Internetu do aplikacji sieci Web musi przechodzić przez urządzenie wirtualne zapory. To urządzenie wirtualne będzie używane tylko w przypadku ruchu internetowego.
* Cały ruch wysyłany do serwera aplikacji musi przechodzić przez urządzenie wirtualne zapory. To urządzenie wirtualne będzie używane do uzyskiwania dostępu do serwera końcowego zaplecza i uzyskiwania dostępu z sieci lokalnej za pośrednictwem VPN Gateway.
* Administratorzy muszą mieć możliwość zarządzania urządzeniami wirtualnymi zapory z komputerów lokalnych przy użyciu trzeciego urządzenia wirtualnego zapory używanego wyłącznie na potrzeby zarządzania.

Jest to standardowy scenariusz sieci obwodowej (nazywany także strefą DMZ) ze strefą DMZ i chronioną siecią. Taki scenariusz można utworzyć na platformie Azure przy użyciu sieciowych grup zabezpieczeń, urządzeń wirtualnych zapory lub kombinacji obu tych elementów. W poniższej tabeli przedstawiono niektóre z zalet i wad między urządzeniami wirtualnymi sieciowych grup zabezpieczeń i zaporą.

|  | Zalety | Wady |
| --- | --- | --- |
| **Sieciowa grupa zabezpieczeń** |Bez kosztów. <br/>Integracja z usługą Azure RBAC. <br/>Reguły można tworzyć w szablonach Azure Resource Manager. |Złożoność może się różnić w większych środowiskach. |
| **Zapora** |Pełna kontrola nad płaszczyzną danych. <br/>Centralne zarządzanie przy użyciu konsoli zapory. |Koszt urządzenia zapory. <br/>Niezintegrowane z funkcją RBAC platformy Azure. |

Poniższe rozwiązanie używa urządzeń wirtualnych zapory do implementowania scenariusza sieci/Protected sieci obwodowej (DMZ).

## <a name="considerations"></a>Zagadnienia do rozważenia
Środowisko opisane powyżej na platformie Azure można wdrożyć przy użyciu różnych funkcji dostępnych dzisiaj w następujący sposób.

* **Sieć wirtualna (VNet)** . Sieć wirtualna platformy Azure działa podobnie jak w przypadku sieci lokalnej i może zostać ujęta w jedną lub kilka podsieci w celu zapewnienia izolacji ruchu i rozdzielenia problemów.
* **Urządzenie wirtualne**. Kilku partnerów udostępnia urządzenia wirtualne w portalu Azure Marketplace, które mogą być używane przez trzy opisane powyżej zapory. 
* **Trasy zdefiniowane przez użytkownika (UDR)**. Tabele tras mogą zawierać UDR używane przez sieć platformy Azure do sterowania przepływem pakietów w sieci wirtualnej. Te tabele tras można stosować do podsieci. Jedną z najnowszych funkcji platformy Azure jest możliwość zastosowania tabeli tras do GatewaySubnet, co zapewnia możliwość przekazywania całego ruchu kierowanego do sieci wirtualnej platformy Azure z połączenia hybrydowego z urządzeniem wirtualnym.
* **Przekazywanie adresów IP**. Domyślnie aparat sieci platformy Azure przekazuje pakiety do kart interfejsów sieci wirtualnej (nic) tylko wtedy, gdy docelowy adres IP pakietu jest zgodny z adresem IP karty sieciowej. W związku z tym, jeśli UDR określa, że pakiet musi zostać wysłany do danego urządzenia wirtualnego, aparat sieci platformy Azure porzuci ten pakiet. Aby zapewnić dostarczenie pakietu do maszyny wirtualnej (w tym przypadku urządzenia wirtualnego), które nie jest rzeczywistym miejscem docelowym pakietu, należy włączyć przekazywanie IP dla urządzenia wirtualnego.
* **Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)**. Poniższy przykład nie korzysta z sieciowych grup zabezpieczeń, ale można użyć sieciowych grup zabezpieczeń zastosowanych do podsieci i/lub kart sieciowych w tym rozwiązaniu, aby dodatkowo odfiltrować ruch do i z tych podsieci oraz kart sieciowych.

![Łączność IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

W tym przykładzie istnieje subskrypcja, która zawiera następujące elementy:

* 2 grupy zasobów, które nie są wyświetlane na diagramie. 
  * **ONPREMRG**. Zawiera wszystkie zasoby niezbędne do symulowania sieci lokalnej.
  * **AZURERG**. Zawiera wszystkie zasoby niezbędne dla środowiska sieci wirtualnej platformy Azure. 
* Sieć wirtualna o nazwie **onpremvnet** użyta do naśladowania lokalnego centrum danych segmentów, jak wymieniono poniżej.
  * **onpremsn1**. Podsieć zawierająca maszynę wirtualną z systemem Ubuntu do naśladowania serwera lokalnego.
  * **onpremsn2**. Podsieć zawierająca maszynę wirtualną z systemem Ubuntu do naśladowania komputera lokalnego używanego przez administratora.
* Istnieje jedno urządzenie wirtualne zapory o nazwie **OPFW** w **onpremvnet** używane do obsługi tunelu do **azurevnet**.
* Sieć wirtualna o nazwie **azurevnet** segmenty wymienioną poniżej.
  * **azsn1**. Zewnętrzna podsieć zapory używana wyłącznie dla zapory zewnętrznej. Cały ruch internetowy zostanie przechodzący przez tę podsieć. Ta podsieć zawiera tylko kartę sieciową połączoną z zaporą zewnętrzną.
  * **azsn2**. Podsieć frontonu obsługująca maszynę wirtualną działającą jako serwer sieci Web, do której będzie uzyskiwany dostęp z Internetu.
  * **azsn3**. Podsieć zaplecza obsługująca maszynę wirtualną z serwerem aplikacji zaplecza dostępną na serwerze frontonu sieci Web.
  * **azsn4**. Podsieć zarządzania używana wyłącznie w celu zapewnienia dostępu do zarządzania wszystkimi urządzeniami wirtualnymi zapory. Ta podsieć zawiera kartę sieciową dla każdego urządzenia wirtualnego zapory użytego w rozwiązaniu.
  * **GatewaySubnet**. Podsieć połączenia hybrydowego platformy Azure wymagana dla ExpressRoute i VPN Gateway w celu zapewnienia łączności między usługą Azure sieci wirtualnych i innymi sieciami. 
* W sieci **azurevnet** znajdują się 3 urządzenia wirtualne zapory. 
  * **AZF1**. Zewnętrzna Zapora udostępniona publicznie w Internecie przy użyciu zasobu publicznego adresu IP na platformie Azure. Musisz mieć pewność, że masz szablon z witryny Marketplace lub bezpośrednio z poziomu dostawcy urządzenia, który udostępnia urządzenie wirtualne z 3 KARTAmi.
  * **AZF2**. Wewnętrzna Zapora służąca do kontrolowania ruchu między **azsn2** i **azsn3**. Jest to również urządzenie wirtualne 3-karta sieciowa.
  * **AZF3**. Zapora zarządzania dostępna dla administratorów z lokalnego centrum danych i połączona z podsiecią zarządzania służącą do zarządzania wszystkimi urządzeniami zapory. Szablony urządzeń wirtualnych 2-kart sieciowych można znaleźć w witrynie Marketplace lub zażądać ich bezpośrednio od dostawcy urządzenia.

## <a name="user-defined-routing-udr"></a>Routing zdefiniowany przez użytkownika (UDR)
Każda podsieć na platformie Azure może być połączona z tabelą UDR służącą do definiowania sposobu, w jaki ruch zainicjowany w tej podsieci jest kierowany. Jeśli nie zdefiniowano żadnych UDR, platforma Azure korzysta z domyślnych tras, aby umożliwić przepływ ruchu z jednej podsieci do innej. Aby lepiej zrozumieć UDR, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](virtual-networks-udr-overview.md).

Aby zapewnić, że komunikacja odbywa się przez odpowiednie urządzenie zapory, na podstawie poprzedniego wymagania powyżej należy utworzyć następującą tabelę tras zawierającą UDR w **azurevnet**.

### <a name="azgwudr"></a>azgwudr
W tym scenariuszu jedyny ruch przepływający z lokalnego na platformę Azure będzie używany do zarządzania zaporami przez połączenie z usługą **AZF3**, a ruch musi przechodzić przez zaporę wewnętrzną, **AZF2**. W związku z tym w **GatewaySubnet** jest konieczna tylko jedna trasa, jak pokazano poniżej.

| Element docelowy | Narzędzie Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Zezwala na ruch lokalny do **AZF3** zapory zarządzania |

### <a name="azsn2udr"></a>azsn2udr
| Element docelowy | Narzędzie Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Zezwala na ruch do podsieci zaplecza hostującym serwer aplikacji za pomocą **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Zezwala na kierowanie całego ruchu przez **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Element docelowy | Narzędzie Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Zezwala na **azsn2** ruchu z serwera aplikacji do serwera WebServer za pośrednictwem usługi **AZF2** |

Należy również utworzyć tabele tras dla podsieci w **onpremvnet** , aby naśladować lokalny centrum danych.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Element docelowy | Narzędzie Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Zezwala na ruch do **onpremsn2** przez **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Element docelowy | Narzędzie Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Zezwala na ruch do podsieci z kopią zapasową na platformie Azure za pomocą **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Zezwala na ruch do **onpremsn1** przez **OPFW** |

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
UDR i przekazywanie adresów IP to funkcje, których można używać w połączeniu z urządzeniami wirtualnymi do kontrolowania przepływu ruchu w sieci wirtualnej platformy Azure.  Urządzenie wirtualne to po prostu maszyna wirtualna, na której działa aplikacja służąca do obsługi ruchu sieciowego w określony sposób, na przykład zapora lub urządzenie NAT.

Ta maszyna wirtualna musi mieć zdolność odbierania ruchu przychodzącego, który nie jest skierowany do niej samej. Aby umożliwić maszynie wirtualnej odbieranie ruchu kierowanego do innych miejsc docelowych, konieczne jest włączenie dla tej maszyny wirtualnej funkcji przesyłania dalej IP. Jest to ustawienie platformy Azure, a nie systemu operacyjnego gościa. Urządzenie wirtualne nadal musi uruchamiać jakiś typ aplikacji, aby obsługiwać ruch przychodzący i odpowiednio kierować.

Aby dowiedzieć się więcej na temat przekazywania adresów IP, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](virtual-networks-udr-overview.md).

Załóżmy na przykład, że masz następujące czynności konfiguracyjne w sieci wirtualnej platformy Azure:

* **Onpremsn1** podsieci zawiera maszynę wirtualną o nazwie **onpremvm1**.
* **Onpremsn2** podsieci zawiera maszynę wirtualną o nazwie **onpremvm2**.
* Urządzenie wirtualne o nazwie **OPFW** jest połączone z **onpremsn1** i **onpremsn2**.
* Trasa zdefiniowana przez użytkownika połączona z **onpremsn1** określa, że cały ruch do **onpremsn2** musi być wysyłany do **OPFW**.

W tym momencie, jeśli **onpremvm1** próbuje nawiązać połączenie z **onpremvm2**, zostanie użyty UDR, a ruch zostanie wysłany do **OPFW** jako następnym przeskoku. Pamiętaj, że rzeczywista lokalizacja docelowa pakietu nie jest zmieniana, nadal mówi, że **onpremvm2** jest miejscem docelowym. 

Bez włączonego przekazywania adresów IP dla **OPFW**, logika sieci wirtualnej platformy Azure spowoduje porzucenie pakietów, ponieważ zezwala na wysyłanie tylko pakietów na maszynę wirtualną, jeśli adres IP maszyny wirtualnej jest miejscem docelowym pakietu.

Dzięki funkcji przekazywania adresów IP logika sieci wirtualnej platformy Azure będzie przekazywać pakiety do OPFW bez konieczności zmiany oryginalnego adresu docelowego. **OPFW** musi obsługiwać pakiety i określać, co należy zrobić z nimi.

Aby zapoznać się z powyższym scenariuszem, należy włączyć przekazywanie adresów IP na kartach sieciowych dla **OPFW**, **AZF1**, **AZF2** i **AZF3** , które są używane do routingu (wszystkie karty sieciowe z wyjątkiem tych połączonych z podsiecią zarządzania). 

## <a name="firewall-rules"></a>Reguły zapory
Zgodnie z powyższym opisem, przekazywanie IP tylko zapewnia, że pakiety są wysyłane do urządzeń wirtualnych. Urządzenie nadal musi zdecydować, co należy zrobić z tymi pakietami. W powyższym scenariuszu konieczne będzie utworzenie następujących reguł w urządzeniach:

### <a name="opfw"></a>OPFW
OPFW reprezentuje urządzenie lokalne zawierające następujące reguły:

* **Trasa**: cały ruch do 10.0.0.0/16 (**azurevnet**) musi być wysyłany za poorednictwem tunelu **ONPREMAZURE**.
* **Zasady**: Zezwalaj na cały ruch dwukierunkowy między **PORT2** i **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 reprezentuje urządzenie wirtualne platformy Azure zawierające następujące reguły:

* **Zasady**: Zezwalaj na cały ruch dwukierunkowy między **PORT1** i **PORT2**.

### <a name="azf2"></a>AZF2
AZF2 reprezentuje urządzenie wirtualne platformy Azure zawierające następujące reguły:

* **Trasa**: cały ruch do 10.0.0.0/16 (**onpremvnet**) musi być wysyłany do adresu IP bramy platformy Azure (np. 10.0.0.1) do **PORT1**.
* **Zasady**: Zezwalaj na cały ruch dwukierunkowy między **PORT1** i **PORT2**.

## <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)
W tym scenariuszu sieciowych grup zabezpieczeń nie są używane. Można jednak zastosować sieciowych grup zabezpieczeń do każdej podsieci, aby ograniczyć ruch przychodzący i wychodzący. Na przykład można zastosować następujące reguły sieciowej grupy zabezpieczeń do zewnętrznej podsieci zapory.

**Dane**

* Zezwalaj na ruch TCP z Internetu do portu 80 na dowolnej maszynie wirtualnej w podsieci.
* Odrzuć cały ruch z Internetu.

**Przeznaczony**

* Odrzuć cały ruch do Internetu.

## <a name="high-level-steps"></a>Kroki wysokiego poziomu
Aby wdrożyć ten scenariusz, postępuj zgodnie z poniższymi krokami poniżej.

1. Zaloguj się do subskrypcji platformy Azure.
2. Jeśli chcesz wdrożyć sieć wirtualną w celu naśladowania sieci lokalnej, zainicjuj zasoby, które są częścią **ONPREMRG**.
3. Zainicjuj obsługę zasobów, które są częścią **AZURERG**.
4. Zainicjuj obsługę tunelu z **onpremvnet** do **azurevnet**.
5. Po udostępnieniu wszystkich zasobów Zaloguj się do **onpremvm2** i polecenie ping 10.0.3.101, aby przetestować łączność między **onpremsn2** i **azsn3**.


---
title: Omówienie grup zabezpieczeń sieci platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o sieciowych grupach zabezpieczeń. Sieciowe grupy zabezpieczeń ułatwiają Filtrowanie ruchu sieciowego między zasobami platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 60c350b10fb3db82af47551591d95e87cacd63a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065011"
---
# <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
<a name="network-security-groups"></a>

Za pomocą grupy zabezpieczeń sieci platformy Azure można filtrować ruch sieciowy do i z zasobów platformy Azure w sieci wirtualnej platformy Azure. Grupa zabezpieczeń sieci zawiera [reguły zabezpieczeń](#security-rules), które zezwalają na lub blokują przychodzący ruch sieciowy lub wychodzący ruch sieciowy dla kilku typów zasobów platformy Azure. Dla każdej reguły można określić źródło i obiekt docelowy, port i protokół.

W tym artykule opisano właściwości reguły sieciowej grupy zabezpieczeń, stosowane [domyślne reguły zabezpieczeń](#default-security-rules) oraz właściwości reguły, które można modyfikować w celu utworzenia [rozszerzonej reguły zabezpieczeń](#augmented-security-rules).

## <a name="security-rules"></a><a name="security-rules"></a>Reguły zabezpieczeń

Grupa zabezpieczeń sieci nie zawiera żadnych reguł lub dowolną liczbę reguł zgodnie z potrzebami, w ramach [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) subskrypcji platformy Azure. Każda reguła określa następujące właściwości:

|Właściwość  |Wyjaśnienie  |
|---------|---------|
|Nazwa|Unikatowa nazwa w obrębie sieciowej grupy zabezpieczeń.|
|Priorytet | Liczba z zakresu od 100 do 4096. Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.|
|Obiekt źródłowy lub docelowy| Dowolny lub indywidualny adres IP, blok CIDR (na przykład 10.0.0.0/24), tag usługi lub grupa zabezpieczeń aplikacji. W przypadku określenia adresu dla zasobu platformy Azure należy określić prywatny adres IP przypisany do zasobu. W przypadku ruchu przychodzącego grupy zabezpieczeń sieci są przetwarzane po tym, jak platforma Azure przetłumaczy publiczny adres IP na prywatny adres IP, a w przypadku ruchu wychodzącego — zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP. . Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Możliwość określenia wielu poszczególnych adresów IP i zakresów (nie można określić wielu tagów usługi ani grup aplikacji) w regule nosi nazwę [rozszerzonych reguł zabezpieczeń](#augmented-security-rules). Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu adresów IP i zakresów adresów IP w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania.|
|Protokół     | TCP, UDP, ICMP lub dowolny.|
|Kierunek| Określa, czy reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego.|
|Zakres portów     |Można określić pojedynczy port lub zakres portów. Na przykład można określić port 80 lub 10000–10005. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu portów lub zakresów portów w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania.   |
|Akcja     | Zezwolenie lub zablokowanie        |

Reguły zabezpieczeń grupy zabezpieczeń sieci są oceniane według priorytetu na podstawie krotki składającej się z pięciu informacji (źródło, port źródłowy, obiekt docelowy, port docelowy i protokół) w celu zezwolenia na ruch lub zablokowania go. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu. Dzięki rekordowi przepływu grupa zabezpieczeń sieci jest stanowa. Jeśli zostanie określona reguła zabezpieczeń dla ruchu wychodzącego do dowolnego adresu za pośrednictwem (na przykład) portu 80, nie trzeba określać żadnej reguły zabezpieczeń ruchu przychodzącego dla odpowiedzi na ruch wychodzący. Należy tylko określić regułę zabezpieczeń dla ruchu przychodzącego w przypadku, jeśli komunikacja jest inicjowana zewnętrznie. Jest to również prawdziwe w odwrotnym przypadku. Jeśli ruch przychodzący jest dozwolony przez port, nie trzeba określać reguły zabezpieczeń dla ruchu wychodzącego, aby odpowiadać na ruch przychodzący przez port.

Istniejące połączenia mogą nie zostać przerwane po usunięciu reguły zabezpieczeń, która zezwoliła na przepływ. Przepływy ruchu są przerywane po zakończeniu połączenia, gdy przez co najmniej kilka minut nie ma ruchu z żadnej strony.

Istnieją ograniczenia dotyczące liczby reguł zabezpieczeń, które można utworzyć w grupie zabezpieczeń sieci. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a>Domyślne reguły zabezpieczeń

Platforma Azure tworzy następujące reguły domyślne w każdej tworzonej grupie zabezpieczeń sieci:

#### <a name="inbound"></a>Inbound

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Dowolne|Zezwalaj|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Dowolne|Zezwalaj|

##### <a name="denyallinbound"></a>DenyAllInbound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Dowolne|Zablokuj|

#### <a name="outbound"></a>Wychodzący

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Dowolne | Zezwalaj |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Dowolne | Zezwalaj |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Dowolne | Zablokuj |

W kolumnach **Źródło** i **Obiekt docelowy** elementy *VirtualNetwork*, *AzureLoadBalancer* i *Internet* są [tagami usługi](service-tags-overview.md), a nie adresami IP. W kolumnie Protokół **wszystkie** obejmują protokoły TCP, UDP i ICMP. Podczas tworzenia reguły można określić protokół TCP, UDP, ICMP lub dowolny. Wartość *0.0.0.0/0* w kolumnach **Źródło** i **Obiekt docelowy** reprezentuje wszystkie adresy. Klienci, takie jak Azure Portal, interfejs wiersza polecenia platformy Azure lub program PowerShell, mogą używać dla tego wyrażenia znaku * lub dowolnego z nich.
 
Nie można usunąć reguł domyślnych, ale można je przesłonić, tworząc reguły o wyższych priorytetach.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a>Rozszerzone reguły zabezpieczeń

Rozszerzone reguły zabezpieczeń upraszczają definicję zabezpieczeń dla sieci wirtualnych, umożliwiając definiowanie zasad zabezpieczeń większych i złożonych sieci przy użyciu mniejszej liczby reguł. Można połączyć wiele portów, wiele jawnych adresów IP i zakresów w jedną, łatwo zrozumiałą regułę zabezpieczeń. Rozszerzone reguły stosuje się w polach źródła, obiektu docelowego i portów reguły. Aby uprościć zarządzanie definicją reguły zabezpieczeń, połącz rozszerzone reguły zabezpieczeń z [tagami usług](service-tags-overview.md) lub [grupami zabezpieczeń aplikacji](#application-security-groups). Istnieją limity liczby adresów, zakresów i portów, które można określić w regule. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Tagi usługi

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Pomaga zminimalizować złożoność częstych aktualizacji w regułach zabezpieczeń sieci.

Aby uzyskać więcej informacji, zobacz [Tagi usług platformy Azure](service-tags-overview.md). Przykład użycia znacznika usługi magazynu w celu ograniczenia dostępu do sieci można znaleźć w temacie [ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Grupy zabezpieczeń aplikacji

Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup. Możesz ponownie używać zasad zabezpieczeń na dużą skalę bez ręcznej obsługi jawnych adresów IP. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji](application-security-groups.md).

## <a name="how-traffic-is-evaluated"></a>Sposób oceniania ruchu

W sieci wirtualnej platformy Azure można wdrożyć zasoby z kilku usług platformy Azure. Aby uzyskać pełną listę, zobacz [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Usługi, które można wdrożyć w sieci wirtualnej). Z każdą siecią [podsiecią](virtual-network-manage-subnet.md#change-subnet-settings) i [interfejsem sieciowym](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) sieci wirtualnej na maszynie wirtualnej możesz skojarzyć zero lub jedną grupę zabezpieczeń sieci. Tę samą grupę zabezpieczeń sieci można zastosować do dowolnej liczby interfejsów sieciowych i podsieci.

Poniższa ilustracja przedstawia różne scenariusze dotyczące sposobu wdrażania grup zabezpieczeń sieci w celu zezwolenia na ruch sieciowy do i z Internetu przez port TCP 80:

![Sieciowe grupy zabezpieczeń — przetwarzanie](./media/security-groups/nsg-interaction.png)

Odwołaj się do poprzedniej ilustracji i następującego tekstu, aby zrozumieć, w jaki sposób platforma Azure przetwarza reguły ruchu przychodzącego i wychodzącego dla grup zabezpieczeń sieci:

### <a name="inbound-traffic"></a>Ruch przychodzący

W przypadku ruchu przychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje.

- **VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG1*, ponieważ jest ona skojarzona z podsiecią *Subnet1*, a maszyna wirtualna *VM1* znajduje się w podsieci *Subnet1*. O ile nie utworzono reguły zezwalającej na port 80 dla ruchu przychodzącego, ruch jest blokowany przez domyślną regułę zabezpieczeń [DenyAllInbound](#denyallinbound) i nigdy nie jest oceniany przez grupę *NSG2*, ponieważ grupa *NSG2* jest skojarzona z interfejsem sieciowym. Jeśli grupa *NSG1* zawiera regułę zabezpieczeń, która zezwala na port 80, ruch jest następnie przetwarzany przez grupę *NSG2*. Aby zezwolić na ruch przychodzący na porcie 80 do maszyny wirtualnej, obie grupy *NSG1* i *NSG2* muszą zawierać regułę zezwalającą ruch przychodzący na porcie 80 z Internetu.
- **VM2**: przetwarzane są reguły w grupie *NSG1*, ponieważ maszyna wirtualna *VM2* znajduje się również w podsieci *Subnet1*. Ponieważ maszyna wirtualna *VM2* nie ma grupy zabezpieczeń sieci skojarzonej z jej interfejsem sieciowym, odbiera cały ruch dozwolony przez grupę *NSG1* lub nie odbiera całego ruchu blokowanego przez grupę *NSG1*. Ruch jest dozwolony albo blokowany dla wszystkich zasobów w tej samej podsieci, gdy grupa zabezpieczeń sieci jest skojarzona z podsiecią.
- **VM3**: ponieważ ma żadnej sieciowej grupy zabezpieczeń skojarzonej z podsiecią *Subnet2*, ruch jest dozwolony do tej podsieci i przetwarzany przez grupę *NSG2*, ponieważ grupa *NSG2* jest skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej *VM3*.
- **VM4**: ruch jest dozwolony do maszyny wirtualnej *VM4*, ponieważ grupa zabezpieczeń sieci nie jest skojarzona z podsiecią *Subnet3* ani interfejsem sieciowym w tej maszynie wirtualnej. Cały ruch sieciowy przez podsieć i interfejs sieciowy jest dozwolony, jeśli nie mają one skojarzonej żadnej sieciowej grupy zabezpieczeń.

### <a name="outbound-traffic"></a>Ruch wychodzący

W przypadku ruchu wychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje.

- **Maszyna VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG2*. O ile nie utworzono reguły zabezpieczeń, która blokuje ruch wychodzący do Internetu na porcie 80, ruch jest dozwolony przez domyślną regułą zabezpieczeń [AllowInternetOutbound](#allowinternetoutbound) w obu grupach *NSG1* i *NSG2*. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która nie zezwala na port 80, ruch jest blokowany i nigdy nie jest oceniany przez grupę *NSG1*. Aby zablokować port 80 na maszynie wirtualnej, jedna lub obie grupy zabezpieczeń sieci muszą zawierać regułę, która blokuje ruch do Internetu na porcie 80.
- **Maszyna VM2**: cały ruch jest wysyłany przez interfejs sieciowy do podsieci, ponieważ interfejs sieciowy dołączony do maszyny wirtualnej *VM2* nie ma skojarzonej grupy zabezpieczeń sieci. Reguły w grupie *NSG1* są przetwarzane.
- **VM3**: jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która blokuje port 80, ruch jest blokowany. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która zezwala na użycie portu 80, ruch wychodzący do Internetu przez port 80 jest dozwolony, ponieważ grupa zabezpieczeń sieci nie jest skojarzona z podsiecią *Subnet2*.
- **VM4**: cały ruch sieciowy z maszyny wirtualnej *VM4* jest dozwolony, ponieważ żadna grupa zabezpieczeń sieci nie jest skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej ani z podsiecią *Subnet3*.


### <a name="intra-subnet-traffic"></a>Ruch wewnątrz podsieci

Należy pamiętać, że reguły zabezpieczeń w sieciowej grupy zabezpieczeń skojarzonej z podsiecią mogą mieć wpływ na łączność między MASZYNami wirtualnymi. Na przykład, jeśli reguła zostanie dodana do *NSG1* , która odmówi cały ruch przychodzący i wychodzący, *VM1* i *VM2* nie będą już mogły komunikować się ze sobą. Aby to umożliwić, należy dodać inną regułę. 

Reguły agregowane stosowane do interfejsu sieciowego można łatwo wyświetlić, wyświetlając [obowiązujące reguły zabezpieczeń](virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Możesz również skorzystać z możliwości [weryfikowania przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w celu ustalenia, czy komunikacja z lub do interfejsu sieciowego jest dozwolona. Weryfikacja przepływu IP informuje o tym, czy komunikacja jest dozwolona, czy odmowa, oraz która reguła zabezpieczeń sieci zezwala na ruch lub go odmawia.

> [!NOTE]
> Sieciowe grupy zabezpieczeń są skojarzone z podsieciami lub do maszyn wirtualnych i usług w chmurze wdrożonych w klasycznym modelu wdrażania, a także do podsieci lub interfejsów sieciowych w Menedżer zasobów model wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).

> [!TIP]
> O ile nie masz konkretnej przyczyny, zalecamy skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym, ale nie obu jednocześnie. Ponieważ reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią mogą powodować konflikt z regułami w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, możesz napotkać nieoczekiwane problemy z komunikacją, co będzie wymagać rozwiązywania problemów.

## <a name="azure-platform-considerations"></a>Zagadnienia dotyczące platformy Azure

- **Wirtualny adres IP węzła hosta**: podstawowe usługi infrastruktury, takie jak DHCP, DNS, IMDS i monitorowanie kondycji, są udostępniane za pomocą zwirtualizowanych adresów IP hosta 168.63.129.16 i 169.254.169.254. Te adresy IP należą do firmy Microsoft i są jedynymi zwirtualizowanymi adresami IP używanymi do tego celu we wszystkich regionach. Obowiązujące reguły zabezpieczeń i obowiązujące trasy nie będą obejmować tych reguł platformy. Aby zastąpić tę podstawową komunikację z infrastrukturą, można utworzyć regułę zabezpieczeń, aby odmówić ruchu przy użyciu następujących [tagów usługi](service-tags-overview.md) w regułach sieciowej grupy zabezpieczeń: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Dowiedz się, jak [zdiagnozować Filtrowanie ruchu sieciowego](diagnose-network-traffic-filter-problem.md) i [zdiagnozować Routing sieciowy](diagnose-network-routing-problem.md).
- **Licencjonowanie (usługa zarządzania kluczami):** obrazy systemu Windows uruchomione na maszynach wirtualnych muszą być licencjonowane. W celu zapewnienia licencjonowania do serwerów hosta usługi zarządzania kluczami zostaje wysłane żądanie licencjonowania, które takie żądania obsługują. Żądanie jest wysyłane za pomocą portu 1688. W przypadku wdrożeń korzystających z konfiguracji [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) ta reguła platformy zostanie wyłączona.
- **Maszyny wirtualne w pulach ze zrównoważonym obciążeniem**: port źródłowy i zakres adresów stosowane są z komputera źródłowego, nie modułu równoważenia obciążenia. Port docelowy i zakres adresów dotyczą komputera docelowego, a nie modułu równoważenia obciążenia.
- **Wystąpienia usług platformy Azure**: wystąpienia kilku usług platformy Azure, takich jak usługa HDInsight, środowiska usług aplikacji i zestawy skalowania maszyn wirtualnych, są wdrażane w podsieciach sieci wirtualnej. Aby uzyskać pełną listę usług, które można wdrażać w sieciach wirtualnych, zobacz [Virtual network for Azure services (Sieć wirtualna dla usług platformy Azure)](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Upewnij się, że należy zapoznać się z wymaganiami dotyczącymi portów dla każdej usługi przed zastosowaniem grupy zabezpieczeń sieci do podsieci, w której zasób został wdrożony. Jeśli porty wymagane przez usługę zostaną zablokowane, usługa nie będzie działać prawidłowo.
- **Wysyłanie wychodzących wiadomości e-mail**: Firma Microsoft zaleca korzystanie z usług uwierzytelnionego przekazywania SMTP (zwykle połączonych za pośrednictwem portu 587 protokołu TCP, ale często również innych) do wysyłania wiadomości E-mail z usługi Azure Virtual Machines. Usługi przekazywania SMTP specjalizują się w obsłudze reputacji nadawcy, aby ograniczyć możliwość odrzucenia wiadomości e-mail przez zewnętrznych dostawców poczty e-mail. Takie usługi przekazywania SMTP obejmują między innymi usługi Exchange Online Protection i SendGrid. Korzystanie z usług przekazywania SMTP nie jest w żaden sposób ograniczone na platformie Azure, niezależnie od typu subskrypcji. 

  Jeśli subskrypcja platformy Azure została utworzona przed 15 listopada 2017 r., oprócz używania usług przekazywania SMTP można wysłać wiadomości e-mail bezpośrednio za pośrednictwem portu 25 protokołu TCP. Jeśli subskrypcja została utworzona po 15 listopada 2017 r., wysyłanie wiadomości e-mail bezpośrednio przez port 25 może okazać się niemożliwe. Zachowanie komunikacji wychodzącej za pośrednictwem portu 25 zależy od typu Twojej subskrypcji w następujący sposób:

     - **Umowa Enterprise Agreement**: komunikacja wychodząca przez port 25 jest dozwolona. Możesz wysyłać wychodzące wiadomości e-mail bezpośrednio z maszyn wirtualnych do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń platformy Azure. 
     - **Płatność zgodnie z rzeczywistym użyciem:** komunikacja wychodząca przez port 25 jest zablokowana dla wszystkich zasobów. Jeśli musisz wysyłać wiadomości e-mail z maszyny wirtualnej bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz zgłosić wniosek o usunięcie ograniczenia. Wnioski są przeglądane i zatwierdzane według uznania firmy Microsoft, a odpowiednie prawa są przyznawane dopiero po pomyślnym zakończeniu kontroli mającej na celu zapobieganie oszustwom. Aby przesłać wniosek, otwórz zgłoszenie do pomocy technicznej z typem problemu *Techniczny*, *Łączność sieciowa*, *Nie można wysłać wiadomości e-mail (SMTP/port 25)*. W tym zgłoszeniu do pomocy technicznej szczegółowo opisz, dlaczego w ramach subskrypcji musisz wysyłać wiadomości e-mail bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekazywania protokołu SMTP. Jeśli subskrypcja zostanie uznana za wyjątek, tylko maszyny wirtualne utworzone po dacie uznania będą mogły obsługiwać komunikację wychodzącą przez port 25.
     - **MSDN, Azure — dostęp próbny, Azure w ramach programu licencjonowania Open, Education, BizSpark i bezpłatna wersja próbna**: komunikacja wychodząca przez port 25 jest zablokowana dla wszystkich zasobów. Nie można wysyłać żadnych wniosków o usunięcie ograniczenia, ponieważ takie prawa nie są przyznawane. Aby wysyłać wiadomości e-mail z maszyny wirtualnej, musisz skorzystać z usługi przekazywania SMTP.
     - **Dostawca usług w chmurze**: klienci korzystający z zasobów platformy Azure za pośrednictwem dostawcy usług w chmurze mogą utworzyć zgłoszenie do pomocy technicznej za pomocą swojego dostawcy usług w chmurze i zażądać, aby dostawca utworzył przypadek odblokowania w ich imieniu, jeśli nie można użyć bezpiecznego przekazywania protokołu SMTP.

  Jeśli platforma Azure zezwoli Ci na wysyłanie wiadomości e-mail za pośrednictwem portu 25, firma Microsoft nie gwarantuje, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail z maszyny wirtualnej. Jeśli określony dostawca odrzuca pocztę z maszyny wirtualnej, musisz w bezpośredniej współpracy z dostawcą rozwiązać wszelkie problemy z dostarczaniem wiadomości lub filtrowaniem spamu albo użyć usługi uwierzytelnionego przekazywania SMTP.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, które zasoby platformy Azure można wdrożyć w sieci wirtualnej i które z nimi są skojarzone sieciowe grupy zabezpieczeń, zobacz [integracja z siecią wirtualną dla usług platformy Azure](virtual-network-for-azure-services.md)
* Jeśli nie masz doświadczenia w tworzeniu grup zabezpieczeń sieci, możesz ukończyć szybki [samouczek](tutorial-filter-network-traffic.md), aby zyskać pewne doświadczenie w tym zakresie. 
* Jeśli znasz grupy zabezpieczeń sieci i chcesz nimi zarządzać, zobacz [Manage a network security group](manage-network-security-group.md) (Zarządzanie sieciową grupą zabezpieczeń). 
* Jeśli występują problemy z komunikacją i musisz rozwiązać problemy z grupami zabezpieczeń sieci, zobacz [Diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md). 
* Dowiedz się, jak włączyć [dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby analizować ruch sieciowy do i z zasobów, które mają skojarzoną grupę zabezpieczeń sieci.

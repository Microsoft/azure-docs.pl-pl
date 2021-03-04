---
title: Projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych
titleSuffix: Azure Virtual Network NAT
description: Dowiedz się, jak projektować sieci wirtualne przy użyciu zasobów bramy translatora adresów sieciowych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 5c70c575464d82f714022291406418cdd1ca0f8d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102094970"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych

Zasoby bramy translatora adresów sieciowych są częścią [Virtual Network NAT](nat-overview.md) i zapewniają wychodzącą łączność z Internetem dla jednej lub kilku podsieci sieci wirtualnej. Podsieć stanów sieci wirtualnej, która będzie używana przez bramę translatora adresów sieciowych. Translator adresów sieciowych udostępnia translację adresów sieci (Resources) dla podsieci.  Zasoby bramy translatora adresów sieciowych określają, które statyczne adresy IP będą używane przez maszyny wirtualne podczas tworzenia przepływów wychodzących. Statyczne adresy IP pochodzą z zasobów publicznego adresu IP (PIP), zasobów prefiksu publicznego adresu IP lub obu. Jeśli jest używany zasób prefiksu publicznego adresu IP, wszystkie adresy IP całego zasobu prefiksu publicznego adresu IP są używane przez zasób bramy translatora adresów sieciowych. Zasób bramy NAT może korzystać z maksymalnie 16 statycznych adresów IP z obu.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*

## <a name="how-to-deploy-nat"></a>Jak wdrożyć translator adresów sieciowych

Konfigurowanie i używanie bramy translatora adresów sieciowych jest celowo proste:  

Zasób bramy translatora adresów sieciowych:
- Utwórz regionalną lub strefowo (izolowaną od strefy) zasób bramy translatora adresów sieciowych,
- Przypisywanie adresów IP,
- W razie potrzeby zmodyfikuj limit czasu bezczynności protokołu TCP (opcjonalnie).  Przejrzyj [czasomierze](#timers) <ins>przed</ins> zmianą domyślnego.

Sieć wirtualna:
- Skonfiguruj podsieć sieci wirtualnej tak, aby korzystała z bramy translatora adresów sieciowych.

Trasy zdefiniowane przez użytkownika nie są wymagane.

## <a name="resource"></a>Zasób

Zasób został zaprojektowany tak, aby można było go zobaczyć w następującym Azure Resource Manager przykładzie w formacie przypominającym szablon.  Ten format podobny do szablonu jest przedstawiony tutaj, aby zilustrować koncepcje i strukturę.  Zmodyfikuj przykład swoich potrzeb.  Ten dokument nie jest przeznaczony dla samouczka.

Na poniższym diagramie przedstawiono zapisywalne odwołania między różnymi zasobami Azure Resource Manager.  Strzałka wskazuje kierunek odniesienia pochodzący z lokalizacji, w której jest zapisywalny. Przegląd 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Model obiektów Virtual Network NAT">
</p>

*Rysunek: Virtual Network model obiektów NAT*

Translacja adresów sieciowych jest zalecana w przypadku większości obciążeń, chyba że jest to zależne od [puli Load Balancer łączności wychodzącej](../load-balancer/load-balancer-outbound-connections.md).  

Można migrować ze standardowych scenariuszy równoważenia obciążenia, w tym [reguł wychodzących](../load-balancer/load-balancer-outbound-connections.md#outboundrules), do bramy translatora adresów sieciowych. Aby przeprowadzić migrację, Przenieś publiczny adres IP i zasoby prefiksu publicznego adresu IP z frontonów modułu równoważenia obciążenia do bramy translatora adresów sieciowych. Nowe adresy IP dla bramy translatora adresów sieciowych nie są wymagane. Standardowe zasoby publicznego adresu IP i zasób prefiksu publicznego adresu IP mogą być ponownie używane, o ile łączna wartość nie przekracza 16 adresów IP. Zaplanuj migrację z przerwaniem działania usługi na uwadze podczas przejścia.  Możesz zminimalizować przerwy, automatyzując proces. Najpierw Przetestuj migrację w środowisku przejściowym.  W trakcie przejścia nie ma to wpływu na przepływy pochodzące przychodzące.


Poniższy przykład jest fragmentem kodu z szablonu Azure Resource Manager.  Ten szablon wdraża kilka zasobów, w tym bramy translatora adresów sieciowych.  W tym przykładzie szablon zawiera następujące parametry:

- **natgatewayname** — nazwa bramy translatora adresów sieciowych.
- **Lokalizacja** — region platformy Azure, w którym znajduje się zasób.
- **publicipname** — nazwa publicznego adresu IP skojarzonego z bramą translatora adresów sieciowych.
- **vnetname** — nazwa sieci wirtualnej.
- **subnetname** -Name podsieci skojarzonej z bramą translatora adresów sieciowych.

Łączna liczba adresów IP dostarczonych przez wszystkie zasoby adresów IP i prefiksów nie może przekraczać 16 adresów IP. Dozwolona jest dowolna liczba adresów IP z zakresu od 1 do 16.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Po utworzeniu zasobu bramy NAT można go użyć w jednej lub kilku podsieciach sieci wirtualnej. Określ, które podsieci używają tego zasobu bramy NAT. Brama translatora adresów sieciowych nie może obejmować więcej niż jednej sieci wirtualnej. Nie jest wymagane przypisanie tej samej bramy NAT do wszystkich podsieci sieci wirtualnej. Poszczególne podsieci można skonfigurować przy użyciu różnych zasobów bramy translatora adresów sieciowych.

Scenariusze, które nie korzystają ze stref dostępności, będą regionalne (nie określono strefy). Jeśli używasz stref dostępności, możesz określić strefę, aby odizolować translator adresów sieciowych do określonej strefy. Nadmiarowość stref nie jest obsługiwana. Przejrzyj [strefy dostępności](#availability-zones)translatora adresów sieciowych.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

Bramy translatora adresów sieciowych są definiowane za pomocą właściwości w podsieci w sieci wirtualnej. Przepływy utworzone przez maszyny wirtualne w **podsieci** **vnetname** sieci wirtualnej będą używały bramy translatora adresów sieciowych. Wszystkie połączenia wychodzące będą używać adresów IP skojarzonych z **natgatewayname** jako źródłowego adresu IP.

Aby uzyskać więcej informacji na temat szablonu Azure Resource Manager użytego w tym przykładzie, zobacz:

- [Szybki Start: Tworzenie Menedżer zasobów szablonu bramy translatora adresów sieciowych](quickstart-create-nat-gateway-template.md)
- [Virtual Network translator adresów sieciowych](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Wskazówki dotyczące projektowania

Przejrzyj tę sekcję, aby zapoznać się z zagadnieniami dotyczącymi projektowania sieci wirtualnych przy użyciu translatora adresów sieciowych.  

1. [Optymalizacja kosztów](#cost-optimization)
1. [Współistnienie ruchu przychodzącego i wychodzącego](#coexistence-of-inbound-and-outbound)
2. [Zarządzanie zasobami podstawowymi](#managing-basic-resources)
3. [Strefy dostępności](#availability-zones)

### <a name="cost-optimization"></a>Optymalizacja kosztów

[Punkty końcowe usługi](virtual-network-service-endpoints-overview.md) i [link prywatny](../private-link/private-link-overview.md) są opcjami, które należy wziąć pod uwagę w przypadku optymalizacji kosztów. Translator adresów sieciowych nie jest wymagany w przypadku tych usług. Ruch kierowany do punktów końcowych usługi lub link prywatny nie jest przetwarzany przez translator adresów sieciowych sieci wirtualnej.  

Punkty końcowe usługi wiążą zasoby usługi platformy Azure z siecią wirtualną i kontrolują dostęp do zasobów usługi platformy Azure. Na przykład podczas uzyskiwania dostępu do usługi Azure Storage należy użyć punktu końcowego dla magazynu, aby uniknąć naliczania opłat za dane dotyczące translatora adresów sieciowych. Punkty końcowe usługi są bezpłatne.

Link prywatny udostępnia usługę Azure PaaS Service (lub inne usługi hostowane za pomocą linku prywatnego) jako prywatny punkt końcowy wewnątrz sieci wirtualnej.  Połączenie prywatne jest rozliczane na podstawie czasu trwania i przetworzonych danych.

Oceń, czy jedno lub oba te podejścia są dobrym dopasowaniem do scenariusza i użycia w razie potrzeby.

### <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie ruchu przychodzącego i wychodzącego

Brama translatora adresów sieciowych jest zgodna z:

 - Moduł równoważenia obciążenia w warstwie Standardowa
 - Standardowy publiczny adres IP
 - Standardowy prefiks publicznego adresu IP

Podczas tworzenia nowego wdrożenia Zacznij od standardowych jednostek SKU.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*

Internetowy scenariusz tylko dla ruchu wychodzącego udostępniony przez bramę translatora adresów sieciowych można rozszerzyć przy użyciu ruchu przychodzącego z funkcji internetowych. Każdy zasób jest świadomy kierunku, w którym pochodzi przepływ. W podsieci z bramą NAT wszystkie scenariusze połączeń wychodzących do Internetu są zastępowane przez bramę translatora adresów sieciowych. Przychodzące ze scenariuszy internetowych są udostępniane przez odpowiednie zasoby.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>Translator adresów sieciowych i maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia">
</p>

*Ilustracja: Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia |
| Wychodzący | Brama translatora adresów sieciowych |

Maszyna wirtualna będzie używać bramy translatora adresów sieciowych dla ruchu wychodzącego.  Nie ma to żadnego oddziaływania na przychodzące.

#### <a name="nat-and-vm-with-public-load-balancer"></a>Translator adresów sieciowych i maszyna wirtualna z Load Balancer publiczny

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network translatora adresów sieciowych i maszyn wirtualnych z Load Balancerami publicznymi">
</p>

*Rysunek: Virtual Network translatora adresów sieciowych i maszyn wirtualnych z Load Balancerami publicznymi*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Load Balancer publiczny |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę translatora adresów sieciowych.  Nie ma to żadnego oddziaływania na przychodzące.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT i VM z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network translatora adresów sieciowych i maszyn wirtualnych z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer">
</p>

*Ilustracja: Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę translatora adresów sieciowych.  Maszyna wirtualna będzie również używać bramy translatora adresów sieciowych dla ruchu wychodzącego.  Nie ma to żadnego oddziaływania na przychodzące.

### <a name="managing-basic-resources"></a>Zarządzanie zasobami podstawowymi

Standardowy moduł równoważenia obciążenia, publiczny adres IP i publiczny prefiks adresu IP są zgodne z bramą translatora adresów sieciowych. Bramy translatora adresów sieciowych działają w zakresie podsieci. Podstawowa jednostka SKU tych usług należy wdrożyć w podsieci bez bramy translatora adresów sieciowych. Ta separacja umożliwia współistnienie wariantów jednostki SKU w tej samej sieci wirtualnej.

Bramy translatora adresów sieciowych mają wyższy priorytet niż scenariusze wychodzące podsieci. Nie można dostosować modułu równoważenia obciążenia podstawowego lub publicznego adresu IP (i wszystkich utworzonych przez nie usług zarządzanych) przy użyciu prawidłowych tłumaczeń. Brama translatora adresów sieciowych przejmuje kontrolę nad ruchem internetowym w podsieci. Ruch przychodzący do podstawowego modułu równoważenia obciążenia i publiczny adres IP jest niedostępny. Ruch przychodzący do podstawowego modułu równoważenia obciążenia i lub publiczny adres IP skonfigurowany na maszynie wirtualnej nie będzie dostępny.

### <a name="availability-zones"></a>Strefy dostępności

#### <a name="zone-isolation-with-zonal-stacks"></a>Izolacja strefy z stosami stref

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Virtual Network translator adresów sieciowych z izolacją strefy, tworzenie wielu "zonal stacks"">
</p>

*Rysunek: Virtual Network translator adresów sieciowych z izolacją strefy, tworzenie wielu "stosów strefowych"*

Nawet bez stref dostępności translator adresów sieciowych jest odporny na awarie i może przetrwać wiele awarii składników infrastruktury.  Strefy dostępności kompilują się w tej odporności z scenariuszami izolacji strefy dla translatora adresów sieciowych.

Sieci wirtualne i ich podsieci są konstrukcjami regionalnymi.  Podsieci nie są ograniczone do strefy.

Istnieje strefa obietnicy dla izolacji strefy, gdy wystąpienie maszyny wirtualnej używające zasobu bramy NAT znajduje się w tej samej strefie co zasób bramy NAT i jej publiczne adresy IP. Wzorzec, który ma być używany na potrzeby izolacji strefy, tworzy "Stos strefowy" dla strefy dostępności.  Ten "stos stref" składa się z wystąpień maszyn wirtualnych, zasobów bramy translatora adresów sieciowych, publicznego adresu IP i/lub prefiksów zasobów w podsieci przyjętych do obsługi tylko tej samej strefy.   Operacje płaszczyzny kontroli i płaszczyzny danych są następnie wyrównane z i są ograniczone do określonej strefy. 

Niepowodzenie w strefie innej niż lokalizacja, w której istnieje scenariusz, nie ma wpływu na translatora adresów sieciowych. Ruch wychodzący z maszyn wirtualnych znajdujących się w tej samej strefie nie powiedzie się z powodu izolacji strefy.  

#### <a name="integrating-inbound-endpoints"></a>Integrowanie przychodzących punktów końcowych

Jeśli w scenariuszu są wymagane przychodzące punkty końcowe, dostępne są dwie opcje:

| Opcja | Wzorce | Przykład | Pro | Con |
|---|---|---|---|---|
| jedno | **Wyrównaj** punkty końcowe przychodzące z odpowiednimi **stosami stref** , które tworzysz dla ruchu wychodzącego. | Tworzenie standardowego modułu równoważenia obciążenia za pomocą stref frontonu. | Ten sam model kondycji i tryb niepowodzeń dla ruchu przychodzącego i wychodzącego. Prostsze działanie. | Poszczególne adresy IP na strefę mogą wymagać zamaskowania przez wspólną nazwę DNS. |
| (2) | **Nałóż** stosy stref przy użyciu przychodzącego punktu końcowego **między strefami** . | Utwórz standardową usługę równoważenia obciążenia za pomocą frontonu Strefowo nadmiarowego. | Pojedynczy adres IP dla punktu końcowego ruchu przychodzącego. | Model kondycji i tryby niepowodzeń dla ruchu przychodzącego i wychodzącego.  Bardziej skomplikowane do działania. |

>[!NOTE]
> Izolowana od strefy Brama NAT wymaga, aby adresy IP odpowiadały strefie bramy translatora adresów sieciowych. Zasoby bramy translatora adresów sieciowych z adresami IP z innej strefy lub bez strefy są niedozwolone.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Scenariusze wychodzące między strefami nie są obsługiwane

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Virtual Network translator adresów sieciowych jest niezgodny z podsiecią obejmującą strefy">
</p>

*Rysunek: Virtual Network translator adresów sieciowych jest niezgodny z podsiecią obejmującą strefy*

W przypadku wdrażania wystąpień maszyn wirtualnych w wielu strefach w tej samej podsieci nie można osiągnąć obietnicy strefy przy użyciu zasobów bramy translatora adresów sieciowych.   Nawet jeśli z podsiecią dołączono wiele bram translatora adresów sieciowych, wystąpienie maszyny wirtualnej nie wie, który zasób bramy NAT ma zostać wybrany.

Strefa Promise does't istnieje, gdy nie są wyrównane strefy wystąpienie maszyny wirtualnej i strefa bramy translatora adresów sieciowych, lub b) zasób bramy NAT jest używany z wystąpieniami maszyn wirtualnych stref.

Gdy scenariusz będzie działał, jego model kondycji i tryb błędu nie są zdefiniowane z punktu strefy dostępności. Rozważ przechodzenie przez stosy stref lub wszystkie regionalne.

>[!NOTE]
>Właściwość Zones zasobu bramy NAT nie jest modyfikowalna.  Wdróż ponownie zasób bramy translatora adresów sieciowych z zamierzonym preferencjami regionalnymi lub strefy.

>[!NOTE] 
>Adresy IP same przez siebie nie są strefowo nadmiarowe, jeśli nie określono żadnej strefy.  Fronton [Usługa Load Balancer w warstwie Standardowa jest strefowo nadmiarowy](../load-balancer/load-balancer-standard-availability-zones.md) , jeśli adres IP nie został utworzony w określonej strefie.  Nie dotyczy to translatora adresów sieciowych.  Obsługiwane jest tylko izolacja regionalna lub strefy.

## <a name="performance"></a>Wydajność

Każdy zasób bramy NAT może zapewnić do 50 GB/s przepustowości. Wdrożenia można podzielić na wiele podsieci i przypisać do skalowania każdą podsieć lub grupę podsieci.

Każda Brama NAT może obsługiwać 64 000 przepływy dla protokołów TCP i UDP odpowiednio dla przypisanego wychodzącego adresu IP.  Zapoznaj się z poniższą sekcją dotyczącą translacji adresów sieciowych (NAT), aby uzyskać szczegółowe informacje, a także artykuł dotyczący [rozwiązywania problemów](./troubleshoot-nat.md) z konkretną wskazówką

## <a name="source-network-address-translation"></a>Translacja adresów sieci źródłowej

Translator adresów sieciowych (Resources Address Translation) ponownie zapisuje źródło przepływu, aby pochodził z innego adresu IP.  Zasoby bramy translatora adresów sieciowych wykorzystują odmianę przywoływaną zwykle do translacji adresów portu (źródła). Źródło ponownie zapisuje adres źródłowy i port źródłowy. Za pomocą elementu SubPrivate nie istnieje stała relacja między liczbą prywatnych adresów i ich przetłumaczonymi adresami publicznymi.  

### <a name="fundamentals"></a>Podstawy

Spójrzmy na przykład cztery przepływy, aby wyjaśnić podstawową koncepcję.  Brama translatora adresów sieciowych korzysta z 65.52.1.1 zasobów publicznego adresu IP, a maszyna wirtualna nawiązywa połączenia z 65.52.0.1.

| Przepływ | Krotka źródłowa | Kolekcja docelowa |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Te przepływy mogą wyglądać następująco po przejściu z lokalizacji:

| Przepływ | Krotka źródłowa | Spójna kolekcja Source SNAT'ed | Kolekcja docelowa | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

Lokalizacja docelowa będzie widziała źródło przepływu jako 65.52.0.1 (krotka źródłowa źródła danych) ze pokazywanym przypisanym portem.  Dane o pokazanej w powyższej tabeli nazywa się również przystawcy reportcy adresów sieciowych.  Wiele źródeł prywatnych jest zamaskowanych za adresem IP i portem.  

#### <a name="source-snat-port-reuse"></a>ponowne użycie portu źródła (Resources)

Bramy translatora adresów sieciowych odpowiednio Uzgodnij ponownie używać portów źródła (Resources).  Poniższy przykład ilustruje tę koncepcję jako dodatkowy przepływ dla poprzedzającego zestawu przepływów.  Maszyna wirtualna w przykładzie jest przepływem do 65.52.0.2.

| Przepływ | Krotka źródłowa | Kolekcja docelowa |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Brama NAT prawdopodobnie przetłumaczy przepływ 4 na port, który może być używany również dla innych miejsc docelowych.  Zobacz [skalowanie](#scaling) w celu uzyskania dodatkowej dyskusji na temat prawidłowej zmiany wielkości APROWIZACJI adresów IP.

| Przepływ | Krotka źródłowa | Spójna kolekcja Source SNAT'ed | Kolekcja docelowa | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

W powyższym przykładzie nie należy przypisywać określonych portów źródłowych.  Powyższym jest ilustracja wyłącznie koncepcji podstawowej.

Przystawka NAT określona przez translatora adresów sieciowych różni się od [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) w kilku aspektach.

### <a name="on-demand"></a>Na żądanie

Translacja adresów sieciowych na żądanie umożliwia obsługę nowych przepływów ruchu wychodzącego. Wszystkie dostępne porty protokołu Subnet w spisie są używane przez dowolną maszynę wirtualną w podsieciach skonfigurowanych przy użyciu translatora adresów sieciowych. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego na żądanie">
</p>

*Rysunek: Virtual Network translator adresów sieciowych na żądanie*

Dowolna konfiguracja adresu IP maszyny wirtualnej może tworzyć przepływy wychodzące na żądanie w razie potrzeby.  Alokacja wstępna, na wystąpienie w przypadku wystąpienia na najgorszy przypadek, nie jest wymagana.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Różnice w scenariuszach wyczerpania">
</p>

*Ilustracja: różnice w scenariuszach wyczerpania*

Po wydaniach portów dla tego elementu jest on dostępny do użycia przez dowolną maszynę wirtualną w podsieciach skonfigurowanych przy użyciu translatora adresów sieciowych.  Alokacja na żądanie umożliwia dynamiczne i niezależne obciążenia w podsieciach, w których są wymagane.  O ile jest dostępny spis portów współistnienia, przepływy adresów sieciowych będą się kończyć pomyślnie. Aktywne punkty dostępu portów przyłączania () mogą korzystać z większej ilości zasobów. Porty z przydziałami nie są pozostawiane niepotrzebnym maszynom wirtualnym.

### <a name="scaling"></a>Skalowanie

Skalowanie translatora adresów sieciowych jest głównie funkcją zarządzania udostępnionym spisem portów przydziałów adresów sieciowych. Translator adresów sieciowych wymaga wystarczającego spisu portów strumienia adresów sieciowych dla oczekiwanych szczytowych przepływów ruchu wychodzącego dla wszystkich podsieci podłączonych do zasobu bramy NAT.  Do utworzenia spisu portów przytranslatorów adresów sieciowych można używać zasobów publicznych adresów IP, zasobów prefiksu publicznego adresu IP lub obu.  

>[!NOTE]
>Jeśli przypiszesz publiczny zasób prefiksu adresu IP, zostanie użyty cały publiczny prefiks adresu IP.  Nie można przypisać zasobu prefiksu publicznego adresu IP, a następnie rozdzielić pojedynczych adresów IP, aby przypisać je do innych zasobów.  Jeśli chcesz przypisać poszczególne adresy IP z publicznego prefiksu IP do wielu zasobów, musisz utworzyć poszczególne publiczne adresy IP z zasobu prefiksu publicznego adresu IP i przypisać je w razie potrzeby zamiast samego zasobu prefiksu publicznego adresu IP.

Reporty adresów sieciowych mapują adresy prywatne na co najmniej jeden publiczny adres IP, zastępując adres źródłowy i port źródłowy w procesach. Zasób bramy NAT będzie używać portów 64 000 (porty protokołu IP) na skonfigurowany publiczny adres IP dla tego tłumaczenia. Zasoby bramy translatora adresów sieciowych można skalować do maksymalnie 16 adresów IP i 1 – 1 portów. Jeśli zostanie podany zasób prefiksu publicznego adresu IP, każdy adres IP w ramach tego prefiksu udostępnia spis portów. Dodanie większej liczby publicznych adresów IP spowoduje zwiększenie dostępnych portów źródłowego translatora adresów sieciowych. Protokoły TCP i UDP są oddzielnymi spisami portów i niezwiązanych z nimi.

Zasoby bramy translatora adresów sieciowych odpowiednio Uzgodnij ponownie Użyj portów źródła (Resources). Jako wskazówki dotyczące projektowania na potrzeby skalowania, należy założyć, że każdy przepływ wymaga nowego portu i przeskaluje łączną liczbę dostępnych adresów IP dla ruchu wychodzącego.  Należy uważnie zastanowić się nad skalą, w której projektujesz adresy IP i zapewnić odpowiednie udostępnianie.

Porty przyłączone do różnych miejsc docelowych najprawdopodobniej będą ponownie używane, gdy jest to możliwe. I jako podejścia do wyczerpania portów w ramach adresów sieciowych, przepływy mogą się nie powieść.  

Zobacz [podstawowe informacje o Przytranslatorze adresów sieciowych](#source-network-address-translation) .


### <a name="protocols"></a>Protokoły

Zasoby bramy translatora adresów sieciowych współpracują z nagłówkami transportu IP i IP przepływów UDP i TCP oraz są niezależny od do ładunków warstwy aplikacji.  Inne protokoły IP nie są obsługiwane.

### <a name="timers"></a>Czasomierze

>[!IMPORTANT]
>Czasomierz długotrwały bezczynny może niepotrzebnie zwiększyć prawdopodobieństwo wyczerpania. Im dłuższy limit czasomierza, tym dłużej translator adresów sieciowych będzie przechowywany w portach przyłączeń do momentu, w którym ostatecznie przestanie bezczynny. Jeśli przepływy są bezczynne, nadal będą kończyć się niepowodzeniem, a niekoniecznie zużywają spis portów.  W przypadku przepływów zakończonych niepowodzeniem o 2 godzinach zakończyło się niepowodzeniem. Zwiększenie limitu czasu bezczynności to ostatnia opcja, która powinna być używana oszczędnie. Jeśli przepływ nigdy nie przejdzie do trybu bezczynności, nie będzie to miało wpływu na czasomierz bezczynności.

Limit czasu bezczynności protokołu TCP można dostosować z 4 minut (domyślnie) do 120 minut (2 godziny) dla wszystkich przepływów.  Dodatkowo można zresetować czasomierz bezczynności z ruchem w przepływie.  Zalecany wzorzec odświeżania długich połączeń bezczynnych i wykrywania aktywności punktu końcowego to ruch TCP.  Utrzymywanie aktywności protokołu TCP jest wyświetlane jako zduplikowane potwierdzenia do punktów końcowych, są niskie obciążenie i niewidoczne dla warstwy aplikacji.

Następujące czasomierze są używane dla wydania portu reportów adresów sieciowych:

| Czasomierz | Wartość |
|---|---|
| PAKIET TCP FIN | 60 sekund |
| PARAMETR RST PROTOKOŁU TCP | 10 sekund |
| Otwarta połowa TCP | 30 sekund |

Port dla tego elementu jest dostępny do ponownego użycia w tym samym docelowym adresie IP i porcie docelowym po 5 sekundach.

>[!NOTE] 
>Te ustawienia czasomierza mogą ulec zmianie. Te wartości są dostarczane w celu ułatwienia rozwiązywania problemów i nie należy w tym momencie podejmować zależności od określonych czasomierzy.

## <a name="limitations"></a>Ograniczenia

- Translator adresów sieciowych jest zgodny z publicznym adresem IP jednostki SKU, publicznym prefiksem adresu IP i zasobami modułu równoważenia obciążenia.   Zasoby podstawowe (na przykład podstawowa usługa równoważenia obciążenia) i wszelkie produkty pochodzące z nich nie są zgodne z translatorem adresów sieciowych.  Zasoby podstawowe muszą być umieszczone w podsieci, która nie jest skonfigurowana przy użyciu translatora adresów sieciowych.
- Rodzina adresów IPv4 jest obsługiwana.  Translator adresów sieciowych nie współdziała z rodziną adresów IPv6.  Nie można wdrożyć translatora adresów sieciowych w podsieci z prefiksem IPv6.
- Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.
- Fragmentacja IP nie jest obsługiwana.

## <a name="suggestions"></a>Sugestie

Chcemy wiedzieć, jak możemy ulepszyć usługę. Brak możliwości? Zapoznaj się z informacjami o tym, co będziemy tworzyć dalej w usłudze [UserVoice dla translatora adresów sieciowych](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [translatorze adresów sieciowych sieci wirtualnych](nat-overview.md).
* Informacje o [metrykach i alertach dotyczących zasobów bramy translatora adresów sieciowych](nat-metrics.md).
* Informacje o [rozwiązywaniu problemów z zasobami bramy translatora adresów sieciowych](troubleshoot-nat.md).

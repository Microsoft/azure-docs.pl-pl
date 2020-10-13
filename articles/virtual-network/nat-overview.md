---
title: Co to jest Azure Virtual Network translator adresów sieciowych?
titlesuffix: Azure Virtual Network
description: Omówienie funkcji Virtual Network translatora adresów sieciowych, zasobów, architektury i implementacji. Dowiedz się, jak działa usługa NAT Virtual Network i jak używać zasobów bramy translatora adresów sieciowych w chmurze.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 75a2bb187b2ed7a234e99d8cd293cb30148bcb1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667201"
---
# <a name="what-is-virtual-network-nat"></a>Co to jest Virtual Network translator adresów sieciowych?

Virtual Network NAT (translator adresów sieciowych) upraszcza połączenia z Internetem tylko w ruchu wychodzącym dla sieci wirtualnych. W przypadku skonfigurowania w podsieci wszystkie połączenia wychodzące korzystają z określonych statycznych publicznych adresów IP.  Łączność wychodząca jest możliwa bez usługi równoważenia obciążenia lub publicznych adresów IP podłączonych bezpośrednio do maszyn wirtualnych. Translator adresów sieciowych jest w pełni zarządzany i wysoce odporny.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Virtual Network translator adresów sieciowych">
</p>



*Rysunek: Virtual Network translator adresów sieciowych*

## <a name="static-ip-addresses-for-outbound-only"></a>Statyczne adresy IP tylko dla ruchu wychodzącego

Łączność wychodząca może być zdefiniowana dla każdej podsieci z translatorem adresów sieciowych.  Wiele podsieci w tej samej sieci wirtualnej może mieć różne NAT. Podsieć jest konfigurowana przez określenie, który zasób bramy NAT ma być używany. Wszystkie przepływy wychodzące UDP i TCP z dowolnego wystąpienia maszyny wirtualnej będą korzystać z translatora adresów sieciowych. 

Translator adresów sieciowych jest zgodny z zasobami publicznych adresów IP jednostki SKU lub publicznymi zasobami prefiksu adresu IP lub połączeniem obu tych elementów.  Publicznego prefiksu adresu IP można używać bezpośrednio lub rozpowszechniać publiczne adresy IP prefiksu w wielu zasobach bramy translatora adresów sieciowych. Translator adresów sieciowych będzie oczyszczał cały ruch do zakresu adresów IP prefiksu.  Wszystkie filtrowanie adresów IP wdrożeń jest teraz łatwe.

Cały ruch wychodzący dla podsieci jest przetwarzany automatycznie przy użyciu translatora adresów sieciowych bez żadnej konfiguracji klienta.  Trasy zdefiniowane przez użytkownika nie są wymagane. Translator adresów sieciowych ma wyższy priorytet niż inne scenariusze wychodzące i zastępuje domyślne miejsce docelowe w podsieci.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Protokół integracyjny na żądanie z wieloma adresami IP na potrzeby skalowania

Translator adresów sieciowych korzysta z "translacji adresów sieci (PNAT lub") i jest zalecany w przypadku większości obciążeń. Dynamiczne lub rozbieżne obciążenia mogą być łatwo dostosowane do alokacji przepływów wychodzących na żądanie. Unika się rozbudowanego wstępnego planowania, wstępnego przydzielania i ostatecznego udostępniania zasobów wychodzących. Zasoby portów protokołu przesyłania adresów sieciowych są udostępniane i dostępne we wszystkich podsieciach przy użyciu określonego zasobu bramy NAT i są udostępniane w razie konieczności.

Publiczny adres IP dołączony do translatora adresów sieciowych zapewnia do 64 000 współbieżne przepływy odpowiednio dla protokołów UDP i TCP. Można zacząć od pojedynczego adresu IP i skalować do 16 adresów IP przy użyciu publicznych adresów IP lub publicznych prefiksów IP.  Zasób bramy NAT będzie używać wszystkich adresów IP skojarzonych z zasobem dla połączeń wychodzących ze wszystkich podsieci skonfigurowanych przy użyciu tego samego zasobu bramy NAT.

Translator adresów sieciowych umożliwia tworzenie przepływów z sieci wirtualnej do Internetu. Ruch powrotny z Internetu jest dozwolony tylko w odpowiedzi na aktywny przepływ.

W przeciwieństwie do wychodzącego ruchu przychodzącego modułu równoważenia obciążenia translator adresów sieciowych nie ma żadnych ograniczeń dotyczących tego, który prywatny adres IP wystąpienia maszyny wirtualnej może nakonywać połączeń wychodzących.  Podstawowa i pomocnicza Konfiguracja adresu IP można utworzyć wychodzące połączenie internetowe z translatorem adresów sieciowych.

## <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie ruchu przychodzącego i wychodzącego

Translator adresów sieciowych jest zgodny z następującymi zasobami standardowej jednostki SKU:

- Moduł równoważenia obciążenia
- Publiczny adres IP
- Prefiks publicznego adresu IP

Te zasoby są używane razem z translatorem adresów sieciowych w celu zapewnienia przychodzącej łączności z Internetem w podsieciach. Translator adresów sieciowych udostępnia wszystkie wychodzące połączenia z Internetem z podsieci.

Funkcja NAT i zgodne standardowe funkcje jednostki SKU są świadome kierunku, w którym przepływ został uruchomiony. Scenariusze przychodzące i wychodzące mogą współistnieć. Te scenariusze będą otrzymywać poprawne tłumaczenia adresów sieciowych, ponieważ te funkcje są świadome kierunku przepływu. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Kierunek przepływu Virtual Network NAT">
</p>

*Rysunek: Virtual Network kierunek przepływu NAT*

## <a name="fully-managed-highly-resilient"></a>W pełni zarządzana, wysoce odporna

Translacja adresów sieciowych jest w pełni skalowana od początku. Nie jest wymagana żadna operacja skalowania w górę lub w poziomie.  Platforma Azure zarządza operacją translatora adresów sieciowych.  Translator adresów sieciowych zawsze ma wiele domen błędów i może utrzymywać wiele awarii bez przestoju usługi.

## <a name="tcp-reset-for-unrecognized-flows"></a>Resetowanie protokołu TCP dla nierozpoznanych przepływów

Po stronie prywatnej translatora adresów sieciowych wysyła pakiety resetowania protokołu TCP, aby próbować komunikować się z nieistniejącym połączeniem TCP. Przykładem są połączenia, które osiągnęły limit czasu bezczynności. Następny odebrany pakiet zwróci Reset TCP do prywatnego adresu IP, aby sygnalizować i wymusić zamknięcie połączenia.

Publiczna strona translatora adresów sieciowych nie generuje pakietów resetowania protokołu TCP ani żadnego innego ruchu.  Emitowany jest tylko ruch generowany przez sieć wirtualną klienta.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurowalny limit czasu bezczynności TCP

Używany jest domyślny limit czasu bezczynności TCP wynoszący 4 minuty i można go zwiększyć do 120 minut. Wszystkie działania w przepływie mogą również zresetować czasomierz bezczynności, w tym utrzymywanie aktywności protokołu TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Izolacja regionalna lub strefa z strefami dostępności

Domyślnie jest to ustawienie regionalne. Podczas tworzenia scenariuszy [strefy dostępności](../availability-zones/az-overview.md) translator adresów sieciowych może być odizolowany w określonej strefie (wdrożenie strefowe).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="Virtual Network translator adresów sieciowych ze strefami dostępności">
</p>

*Rysunek: Virtual Network translator adresów sieciowych ze strefami dostępności*

## <a name="multi-dimensional-metrics-for-observability"></a>Wielowymiarowe metryki do zaobserwowania

Można monitorować operacje NAT za pomocą wielowymiarowych metryk uwidocznionych w Azure Monitor. Te metryki mogą służyć do obserwowania użycia i rozwiązywania problemów.  Zasoby bramy translatora adresów sieciowych uwidaczniają następujące metryki:
- Bajty
- SPI
- Pakiety opuszczone
- Łączna liczba połączeń z translatorem adresów sieciowych
- Przejścia stanu połączenia z przyłączaniem adresów sieciowych na interwał.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Umowa SLA

Przy ogólnej dostępności ścieżka danych NAT jest dostępna co najmniej 99,9%.

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Dostępność

Virtual Network translator adresów sieciowych i zasób bramy NAT są dostępne we wszystkich regionach wszystkich [regionów](https://azure.microsoft.com/global-infrastructure/regions/)chmur platformy Azure.

## <a name="suggestions"></a>Sugestie

Chcemy wiedzieć, jak możemy ulepszyć usługę. Zaproponuj i zagłosuj na to, co będziemy kompilować dalej w usłudze [UserVoice dla translatora adresów sieciowych](https://aka.ms/natuservoice).

## <a name="limitations"></a>Ograniczenia

* Translator adresów sieciowych jest zgodny z publicznym adresem IP jednostki SKU, publicznym prefiksem adresu IP i zasobami modułu równoważenia obciążenia. Zasoby podstawowe, takie jak podstawowa usługa równoważenia obciążenia, i wszelkie produkty pochodzące z nich nie są zgodne z translatorem adresów sieciowych.  Zasoby podstawowe muszą być umieszczone w podsieci, która nie jest skonfigurowana przy użyciu translatora adresów sieciowych.
* Rodzina adresów IPv4 jest obsługiwana.  Translator adresów sieciowych nie współdziała z rodziną adresów IPv6.  Nie można wdrożyć translatora adresów sieciowych w podsieci z prefiksem IPv6.
* Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
* [Powiedz nam, co należy utworzyć obok Virtual Network translatora adresów sieciowych w usłudze UserVoice](https://aka.ms/natuservoice).

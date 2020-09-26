---
title: 'Azure VPN Gateway: przegląd — konfiguracje bramy o wysokiej dostępności'
description: Ten artykuł zawiera omówienie opcji konfiguracji o wysokiej dostępności przy użyciu bram Azure VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 48756b43e64576a5dd38467bb1dd97e91c168a06
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360858"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Połączenia obejmujące wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej dostępności
Ten artykuł zawiera omówienie opcji konfiguracji połączeń obejmujących wiele lokalizacji i połączeń między sieciami wirtualnymi o wysokiej dostępności przy użyciu bram Azure VPN Gateway.

## <a name="about-azure-vpn-gateway-redundancy"></a><a name = "activestandby"></a>Informacje o nadmiarowości bramy Azure VPN Gateway
Każda brama Azure VPN Gateway składa się z dwóch wystąpień działających w konfiguracji aktywne-w gotowości. W przypadku każdej planowanej konserwacji lub nieplanowanych zakłóceń działania aktywnego wystąpienia wystąpienie w trybie gotowości automatycznie przejmie zadanie (tryb failover) i wznowi połączenia typu lokacja-lokacja (S2S) przez sieć VPN lub między sieciami wirtualnymi. Przełączenie spowoduje krótką przerwę w działaniu. W przypadku planowanej konserwacji łączność powinna zostać przywrócona w ciągu od 10 do 15 sekund. W przypadku nieplanowanych awarii odzyskiwanie połączenia potrwa dłużej — od ok. 1 do 1,5 minuty w najgorszym przypadku. W przypadku połączeń sieci VPN typu punkt-lokacja (P2S) między klientami i bramą połączenia P2S zostaną rozłączone, a użytkownicy będą musieli ponownie nawiązać połączenia z poziomu maszyn klienckich.

![Diagram przedstawia lokację lokalną z prywatnymi podsieciami i P i lokalnymi punktami V P N połączonymi z aktywną bramą Azure V P N do łączenia się z podsieciami hostowanymi na platformie Azure z dostępną bramą gotowości.](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Połączenia o wysokiej dostępności obejmujące wiele lokalizacji
Dostępnych jest kilka opcji umożliwiających zapewnienie wyższej dostępności połączeń obejmujących wiele lokacji:

* Wiele lokalnych urządzeń sieci VPN
* Brama Azure VPN Gateway w konfiguracji aktywne-aktywne
* Połączenie obu tych opcji

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>Wiele lokalnych urządzeń sieci VPN
Można nawiązywać połączenie z bramą Azure VPN Gateway z sieci lokalnej przy użyciu wielu urządzeń, jak przedstawiono na poniższym diagramie:

![Wiele lokalnych połączeń sieci VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Ta konfiguracja zapewnia wiele aktywnych tuneli z tej samej bramy Azure VPN Gateway do urządzeń lokalnych w tej samej lokalizacji. Istnieją pewne wymagania i ograniczenia:

1. Należy utworzyć wiele połączeń typu lokacja-lokacja sieci VPN z urządzeń sieci VPN do platformy Azure. W przypadku łączenia wielu urządzeń sieci VPN z tej samej sieci lokalnej z platformą Azure należy utworzyć jedną bramę sieć lokalną dla każdego urządzenia sieci VPN i jedno połączenie z bramy sieci VPN platformy Azure z każdą bramą sieci lokalnej.
2. Bramy sieci lokalnej odpowiadające urządzeniom sieci VPN muszą mieć unikatowe publiczne adresy IP we właściwości „GatewayIpAddress”.
3. Ta konfiguracja wymaga użycia protokołu BGP. Każda brama sieci lokalnej reprezentującą urządzenie sieci VPN musi mieć unikatowy adres IP elementu równorzędnego protokołu BGP określony we właściwości „BgpPeerIpAddress”.
4. Wartości w polu właściwości AddressPrefix w poszczególnych bramach sieci lokalnej nie mogą się nakładać. Należy określić wartość właściwości „BgpPeerIpAddress” w formacie /32 CIDR w polu AddressPrefix, na przykład 10.200.200.254/32.
5. Należy użyć protokołu BGP, aby anonsować te same prefiksy co prefiksy sieci lokalnej dla bramy Azure VPN Gateway, a cały ruch będzie jednocześnie przekazywany przy użyciu tych tuneli.
6. Należy użyć równorzędnego routingu z obsługą wielu ścieżek (ECMP).
7. Każde połączenie jest wliczane do maksymalnej liczby tuneli bramy Azure VPN Gateway wynoszącej 10 dla podstawowych i standardowych jednostek SKUs oraz 30 dla jednostek SKU wysokiej wydajności. 

W tej konfiguracji brama Azure VPN Gateway jest nadal w trybie aktywny-w gotowości, dlatego będzie mieć miejsce to samo zachowanie i krótka przerwa jak w opisie [powyżej](#activestandby). Ta konfiguracja chroni przed awariami lub przerwami w działaniu sieci lokalnej i urządzeń sieci VPN.

### <a name="active-active-azure-vpn-gateway"></a>Brama Azure VPN Gateway w konfiguracji aktywne-aktywne
Teraz można utworzyć bramę Azure VPN Gateway w konfiguracji aktywne-aktywne, gdzie oba wystąpienia maszyny wirtualnej bramy ustanowią tunele połączenia lokacja-lokacja sieci VPN do lokalnych urządzeń sieci VPN, jak przedstawiono na poniższym diagramie:

![Diagram przedstawia lokację lokalną z prywatnymi podsieciami i P i lokalnymi punktami V P N połączonymi z dwoma aktywnymi bramami usługi Azure V P N w celu łączenia się z podsieciami hostowanymi na platformie Azure.](./media/vpn-gateway-highlyavailable/active-active.png)

W tej konfiguracji każde wystąpienie bramy platformy Azure będzie mieć unikatowy publiczny adres IP i każde ustanowi tunel typu lokacja-lokacja sieci VPN przy użyciu protokołu IPsec/IKE do lokalnego urządzenia sieci VPN określonego w bramie sieci lokalnej i połączeniu. Należy pamiętać, że oba tunele sieci VPN są w rzeczywistości częścią tego samego połączenia. Nadal należy skonfigurować lokalne urządzenie sieci VPN, aby akceptowało lub ustanowiło dwa tunele typu lokacja-lokacja sieci VPN do tych dwóch publicznych adresów IP bramy Azure VPN Gateway.

Ponieważ wystąpienia bramy platformy Azure są w konfiguracji aktywne-aktywne, ruch z sieci wirtualnej platformy Azure do sieci lokalnej będzie kierowany przez dwa tunele jednocześnie, nawet jeśli lokalne urządzenie sieci VPN może preferować z tuneli. Należy jednak pamiętać, że ten sam przepływ przy użyciu protokołu TCP lub UDP będzie zawsze odbywać się za pomocą tego samego tunelu lub ścieżki, chyba że na jednym z wystąpień będzie mieć miejsce zdarzenie konserwacji.

W przypadku planowanej konserwacji lub nieplanowanego zdarzenia dotyczącego jednego wystąpienia bramy tunel IPsec z tego wystąpienia do lokalnego urządzenia sieci VPN zostanie rozłączony. Odpowiednie trasy na urządzeniach sieci VPN powinny zostać automatycznie usunięte lub wycofane, aby umożliwić przełączenie ruchu do innego aktywnego tunelu IPsec. Po stronie platformy Azure przełączenie z wystąpienia, którego dotyczy problem, do aktywnego wystąpienia nastąpi automatycznie.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Podwójna nadmiarowość: bramy sieci VPN w konfiguracji aktywne-aktywne dla platformy Azure i sieci lokalnych
Najbardziej niezawodną opcją jest połączenie bram w konfiguracji aktywne-aktywne w sieci lokalnej i na platformie Azure, jak przedstawiono na poniższym diagramie.

![Podwójna nadmiarowość](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

W tym przypadku należy utworzyć i skonfigurować bramę Azure VPN Gateway w konfiguracji aktywne-aktywne i utworzyć dwie bramy sieci lokalnej oraz dwa połączenia dla lokalnych urządzeń sieci VPN zgodnie z powyższym opisem. Wynikiem jest pełna siatka łączności składająca się z 4 tuneli IPsec między siecią wirtualną Azure i siecią lokalną.

Wszystkie bramy i tunele po stronie platformy Azure są aktywne, dlatego ruch zostanie rozłożony jednocześnie na 4 tunele, choć każdy przepływ przy użyciu protokołu TCP lub UDP będzie odbywać się za pomocą tego samego tunelu lub ścieżki po stronie platformy Azure. Pomimo rozłożenia ruchu przepływność przy użyciu tuneli IPsec może być nieznacznie wyższa, jednak głównym celem tej konfiguracji jest wysoka dostępność. Ze względu na statystyczny charakter rozłożenia trudno jest zmierzyć, w jakim stopniu ruch poszczególnych aplikacji będzie wpływać na łączną przepływność.

Ta topologia wymaga dwóch bram sieci lokalnej i dwóch połączeń w celu obsługi pary lokalnych urządzeń sieci VPN, a protokół BGP jest wymagany w celu umożliwienia dwóch połączeń z tą samą siecią lokalną. Te wymagania są takie same jak [powyżej](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Połączenia o wysokiej dostępności między sieciami wirtualnymi za pośrednictwem bram Azure VPN Gateway
Tę samą konfigurację typu aktywne-aktywne można również zastosować w przypadku połączeń między sieciami wirtualnymi platformy Azure. Można utworzyć bramy VPN Gateway typu aktywne-aktywne dla obu sieci wirtualnych i połączyć je ze sobą w celu utworzenia takiej samej pełnej siatki łączności złożonej z 4 tuneli między dwiema sieciami wirtualnymi, jak przedstawiono na poniższym diagramie:

![Diagram przedstawia dwa regiony platformy Azure obsługujące prywatne I P podsieci oraz dwie bramy Azure V P N, za pomocą których łączą się dwie lokacje wirtualne.](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

W tej konfiguracji zawsze istnieje para tuneli między dwiema sieciami wirtualnymi w przypadku planowanych zdarzeń konserwacji, co zapewnia jeszcze lepszą dostępność. Mimo że ta sama topologia dla łączności obejmującej wiele lokalizacji wymaga dwóch połączeń, topologia łączności między sieciami wirtualnymi będzie wymagać tylko jednego połączenia dla każdej bramy. Ponadto użycie protokołu BGP jest opcjonalne, o ile routing tranzytowy za pośrednictwem połączenia między sieciami wirtualnymi nie jest wymagany.

## <a name="next-steps"></a>Następne kroki
Zobacz [Konfigurowanie bram VPN Gateway w konfiguracji typu aktywne-aktywne dla połączeń obejmujących wiele lokalizacji i połączeń między sieciami wirtualnymi](vpn-gateway-activeactive-rm-powershell.md) dla procedury konfigurowania połączeń obejmujących wiele lokalizacji i połączeń między sieciami wirtualnymi typu aktywne-aktywne.


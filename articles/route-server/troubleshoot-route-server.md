---
title: Rozwiązywanie problemów z serwerem usługi Azure Route
description: Dowiedz się, jak rozwiązywać problemy dotyczące serwera usługi Azure Route.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 83f1e83653c5674988cadcb5b54d3c675ae0b8b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489444"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Rozwiązywanie problemów z serwerem usługi Azure Route

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Problemy z łącznością

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Dlaczego moja urządzenie WUS traci łączność z Internetem po anonsowania trasy domyślnej (0.0.0.0/0) na serwerze usługi Azure Route Server?
Gdy urządzenie WUS anonsuje trasę domyślną, usługa Azure Route Server programuje je dla wszystkich maszyn wirtualnych w sieci wirtualnej, w tym samego urządzenie WUS. Ta trasa domyślna ustawia urządzenie WUS jako następny przeskok dla całego ruchu powiązanego z Internetem. Jeśli urządzenie WUS wymaga łączności z Internetem, należy skonfigurować [trasę zdefiniowaną przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) w celu zastąpienia tej trasy domyślnej z urządzenie WUS i dołączenia UDR do podsieci, w której jest hostowany urządzenie WUS (zobacz Poniższy przykład). W przeciwnym razie maszyna hosta urządzenie WUS będzie nadal wysyłać ruch związany z Internetem, w tym dane wysyłane przez urządzenie WUS z powrotem do samego urządzenie WUS.

| Trasa | Następny przeskok |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>Dlaczego mogę wysłać polecenie ping z urządzenie WUS do adresu IP elementu równorzędnego protokołu BGP na serwerze usługi Azure Route, ale po skonfigurowaniu komunikacji równorzędnej BGP nie można już wysyłać ping do tego samego adresu IP? Dlaczego Komunikacja równorzędna BGP jest wyłączona?

W niektórych urządzenie WUS należy dodać trasę statyczną dla podsieci serwera usługi Azure Route. Na przykład jeśli usługa Azure Route Server znajduje się w 10.0.255.0/27, a urządzenie WUS jest w 10.0.1.0/24, należy dodać następującą trasę do tabeli routingu w urządzenie WUS:

| Trasa | Następny przeskok |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 to domyślny adres IP bramy w podsieci, w której znajduje się urządzenie WUS (lub dokładniej, jedna z kart sieciowych).

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Dlaczego mam łączność z siecią lokalną za pośrednictwem usługi ExpressRoute i/lub sieci VPN platformy Azure, gdy wdrażam serwer usługi Azure Route w sieci wirtualnej, która ma już bramę ExpressRoute i/lub bramę sieci VPN platformy Azure?
W przypadku wdrażania serwera usługi Azure Route w sieci wirtualnej należy zaktualizować płaszczyznę kontroli między bramami a siecią wirtualną. W trakcie tej aktualizacji istnieje okres czasu, w którym maszyny wirtualne w sieci wirtualnej utracą łączność z siecią lokalną. Zdecydowanie zalecamy zaplanowanie konserwacji w celu wdrożenia serwera usługi Azure Route w środowisku produkcyjnym.  

## <a name="control-plane-issues"></a>Problemy z płaszczyzną kontroli

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Dlaczego Komunikacja równorzędna BGP między moją urządzenie WUS a serwerem tras platformy Azure odbywa się w górę i w dół ("niestabilny")?

Przyczyną niestabilny może być spowodowane ustawieniem czasomierza BGP. Domyślnie czasomierz Keep-Alive na serwerze usługi Azure Route jest ustawiony na 60 sekund, a czasomierz wstrzymania wynosi 180 sekund.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Dlaczego moje urządzenie WUS nie odbierają tras z serwera usługi Azure Route, mimo że Komunikacja równorzędna BGP jest aktualna?

Numerem ASN wykorzystywanym przez serwer usługi Azure Route jest 65515. Upewnij się, że skonfigurowano inny numer ASN dla urządzenie WUS, tak aby można było nawiązać sesję "eBGP" między programem urządzenie WUS i serwerem usługi Azure Route, dzięki czemu Propagacja trasy może nastąpić automatycznie. Upewnij się, że w konfiguracji protokołu BGP włączono opcję "wiele przeskoków", ponieważ urządzenie WUS i serwer usługi Azure Route znajdują się w różnych podsieciach w sieci wirtualnej.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>Komunikacja równorzędna BGP między moją urządzenie WUS i serwerem usługi Azure Route jest w pełni. Mogę zobaczyć, że trasy są poprawnie wymieniane między nimi. Dlaczego trasy urządzenie WUS nie są używane w efektywnej tabeli routingu mojej maszyny wirtualnej? 

* Jeśli maszyna wirtualna znajduje się w tej samej sieci wirtualnej co urządzenie WUS i serwer tras platformy Azure:

     Serwer usługi Azure Route udostępnia dwa adresy IP elementów równorzędnych protokołu BGP, które są hostowane na dwóch maszynach wirtualnych, które współdzielą przesyłanie tras do wszystkich innych maszyn wirtualnych działających w sieci wirtualnej. Każdy z urządzenie WUS musi skonfigurować dwie identyczne sesje protokołu BGP (na przykład użyć takiej samej wartości jak liczba, taka sama jak ścieżka i anonsowanie tego samego zestawu tras) dla dwóch maszyn wirtualnych, dzięki czemu maszyny wirtualne w sieci wirtualnej mogą uzyskać spójne informacje routingu z serwera usługi Azure Route. Zapoznaj się z poniższym diagramem.

    ![Diagram przedstawiający wirtualne urządzenie sieciowe z serwerem tras.](./media/faq/network-virtual-appliances.png)

    Jeśli masz co najmniej dwa wystąpienia urządzenie WUS, *możesz anonsować* różne jako ścieżki dla tej samej trasy z różnych wystąpień urządzenie WUS, jeśli chcesz wyznaczyć jedno wystąpienie urządzenie WUS jako aktywne i inne pasywne.

* Jeśli maszyna wirtualna znajduje się w innej sieci wirtualnej niż ta, na której znajduje się urządzenie WUS i serwer tras platformy Azure. Sprawdź, czy jest włączona Komunikacja równorzędna sieci wirtualnej między tymi dwoma sieci wirtualnych *i* czy w sieci wirtualnej maszyny wirtualne jest włączony serwer tras zdalnych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować serwer usługi Azure Route](quickstart-configure-route-server-powershell.md)

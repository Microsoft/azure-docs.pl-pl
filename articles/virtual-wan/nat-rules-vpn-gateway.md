---
title: Konfigurowanie reguł NAT sieci VPN dla bramy
titleSuffix: Azure Virtual WAN
description: Dowiedz się, jak skonfigurować reguły NAT dla bramy sieci VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431220"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Konfigurowanie reguł NAT dla bramy sieci VPN wirtualnej sieci WAN — wersja zapoznawcza

> [!IMPORTANT]
> Reguły NAT są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wirtualną bramę sieci VPN w sieci WAN można skonfigurować przy użyciu statycznych reguł NAT jeden do jednego. Reguła NAT zapewnia mechanizm konfigurowania translacji jeden-do-jednego adresów IP. Translatora adresów sieciowych może służyć do łączenia dwóch sieci IP, które mają niezgodne lub nakładające się adresy IP. Typowy scenariusz to gałęzie z nakładanymi adresami IP, które chcą uzyskać dostęp do zasobów sieci wirtualnej platformy Azure.

Ta konfiguracja używa tabeli przepływu do kierowania ruchu z zewnętrznego (hosta) do wewnętrznego adresu IP skojarzonego z punktem końcowym w sieci wirtualnej (maszyna wirtualna, komputer, kontener itp.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram przedstawiający architekturę.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Konfigurowanie reguł translatora adresów sieciowych

W każdej chwili można skonfigurować i wyświetlić reguły NAT w ustawieniach bramy sieci VPN.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Zrzut ekranu przedstawiający edytowanie reguł.":::

1. Przejdź do koncentratora wirtualnego.
1. Wybierz pozycję **VPN (lokacja-lokacja)**.
1. Wybierz pozycję **reguły NAT (Edytuj)**.
1. Na stronie **Edytowanie reguły NAT** można **dodać/edytować/usunąć** regułę NAT przy użyciu następujących wartości:

   * **Nazwa:** Unikatowa nazwa reguły NAT.
   * **Typ:** Ruchom. Statyczny NAT jeden do jednego ustanawia relację jeden do jednego między adresem wewnętrznym i adresem zewnętrznym.
   * **Tryb:** IngressSnat lub EgressSnat.  
      * Tryb IngressSnat (znany również jako źródłowy translator adresów sieciowych) ma zastosowanie do ruchu w ramach bramy sieci VPN typu lokacja-lokacja centrum platformy Azure.
      * Tryb EgressSnat (znany także jako źródłowy translator adresów sieciowych) ma zastosowanie do ruchu wychodzącego z bramy sieci VPN typu lokacja-lokacja centrum platformy Azure.
   * **InternalMapping:** Zakres prefiksu adresu źródłowych adresów IP w sieci firmowej, który zostanie zmapowany na zestaw zewnętrznych adresów IP. Innymi słowy, zakres prefiksu adresów przed translatorem adresów sieciowych.
   * **ExternalMapping:** Zakres prefiksu adresu docelowych adresów IP w sieci zewnętrznej, do której będą mapowane adresy IP. Innymi słowy, zakres prefiksu adresu po translatorze adresów sieciowych.
   * **Połącz połączenie:** Zasób połączenia, który wirtualnie nawiązuje połączenie z witryną sieci VPN z bramą sieci VPN typu lokacja-lokacja centrum platformy Azure.

### <a name="configuration-considerations"></a><a name="considerations"></a>Zagadnienia dotyczące konfiguracji

* Rozmiar podsieci dla mapowania wewnętrznego i zewnętrznego musi być taki sam dla statycznego translatora adresów sieciowych jeden-do-jednego.
* Aby dodać prefiksy **ExternalMapping** w polu "prywatna przestrzeń adresowa", należy edytować witrynę sieci VPN w Azure Portal. Obecnie lokacje z włączonym protokołem BGP muszą mieć pewność, że lokalny anons protokołu BGP (Ustawienia protokołu BGP urządzenia) zawiera wpis dla prefiksów mapowania zewnętrznego.

## <a name="examples-and-verification"></a><a name="examples"></a>Przykłady i weryfikacja

### <a name="ingress-mode-nat"></a>Tryb transferu danych przychodzących

Reguły NAT trybu wejściowego są stosowane w pakietach, które wprowadzają platformę Azure za pośrednictwem bramy sieci VPN typu lokacja-lokacja wirtualnej sieci WAN. W tym scenariuszu chcesz połączyć dwie gałęzie sieci VPN typu lokacja-lokacja z platformą Azure. Lokacja sieci VPN 1 nawiązuje połączenie za pośrednictwem link1, a lokacja 2 łączy się za pośrednictwem linku 2. Każda lokacja ma przestrzeń adresową 192.169.1.0/24.

Na poniższym diagramie przedstawiono przewidywany wynik końcowy:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagram przedstawiający tryb danych wejściowych NAT.":::

1. Określ regułę NAT.

   Określ regułę NAT, aby zapewnić, że Brama sieci VPN typu lokacja-lokacja może rozróżnić dwie gałęzie z nakładającymi się obszarami adresów (na przykład 192.168.1.0/24). W tym przykładzie koncentrujemy się na witrynie link1 dla sieci VPN 1.

   Można skonfigurować następującą regułę NAT i skojarzyć ją z linkiem 1 jednej z gałęzi. Ponieważ jest to statyczna reguła NAT, przestrzenie adresowe InternalMapping i ExternalMapping zawierają tę samą liczbę adresów IP.

   * **Nazwa:** IngressRule01
   * **Typ:** Ruchom
   * **Tryb:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Połącz połączenie:** Link 1

1. Anonsuj poprawną ExternalMapping.

   W tym kroku upewnij się, że Brama sieci VPN typu lokacja-lokacja anonsuje poprawną przestrzeń adresową ExternalMapping do reszty zasobów platformy Azure. Istnieją różne instrukcje, w zależności od tego, czy protokół BGP jest włączony, czy nie.

   **Przykład 1: protokół BGP jest włączony**

   * Upewnij się, że lokalny głośnik BGP znajdujący się w witrynie sieci VPN 1 jest skonfigurowany tak, aby anonsować przestrzeń adresową 10.1.1.0/24.
   * W tej wersji zapoznawczej lokacje z włączonym protokołem BGP muszą mieć pewność, że lokalny anons protokołu BGP (Ustawienia protokołu BGP urządzenia) zawiera wpis dla prefiksów mapowania zewnętrznego.

   **Przykład 2: protokół BGP nie jest włączony**

   * Przejdź do zasobu koncentratora wirtualnego, który zawiera bramę sieci VPN typu lokacja-lokacja. Na stronie koncentrator wirtualny w obszarze **łączność** wybierz pozycję **Sieć VPN (lokacja-lokacja)**.
   * Wybierz lokację sieci VPN, która jest połączona z wirtualnym koncentratorem sieci WAN za pośrednictwem linku 1. Wybierz pozycję **Edytuj lokację** i wprowadź 10.1.1.0/24 jako prywatną przestrzeń adresową dla witryny sieci VPN.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie witryny sieci VPN.":::

### <a name="packet-flow"></a>Przepływ pakietów

W tym przykładzie urządzenie lokalne chce dotrzeć do sieci wirtualnej szprychy. Przepływ pakietów jest następujący, a Translacja NAT jest pogrubienie.

1. Inicjowany jest ruch z lokalizacji lokalnej.
   * Źródłowy adres IP: **192.168.1.1**
   * Docelowy adres IP: 30.0.0.1
1. Ruch przechodzi do bramy lokacja-lokacja i jest tłumaczony przy użyciu reguły NAT, a następnie wysyłany do szprychy.
   * Źródłowy adres IP: **10.1.1.1**
   * Docelowy adres IP: 30.0.0.1
1. Odpowiedź od szprychy została zainicjowana.
   * Źródłowy adres IP: 30.0.0.1
   * Docelowy adres IP: **10.1.1.1**
1. Ruch przechodzi do bramy sieci VPN typu lokacja-lokacja, a tłumaczenie jest wycofywane i wysyłane do lokalnego.
   * Źródłowy adres IP: 30.0.0.1
   * Docelowy adres IP: **192.168.1.1**

### <a name="verification-checks"></a>Kontrole weryfikacji

W tej sekcji przedstawiono testy, aby sprawdzić, czy konfiguracja została prawidłowo skonfigurowana.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Weryfikowanie DefaultRouteTable, reguł i tras

Gałęzie w wirtualnej sieci WAN kojarzą się z **DefaultRouteTable**, co oznacza, że wszystkie połączenia gałęzi dowiedzą trasy, które są wypełnione w DefaultRouteTable. Reguła NAT zostanie wyświetlona z prefiksem mapowania zewnętrznego w skutecznych trasach DefaultRouteTable.

Przykład:

* **Prefiks:** 10.1.1.0/24  
* **Typ następnego przeskoku:** VPN_S2S_Gateway
* **Następny przeskok:** Zasób VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Weryfikuj prefiksy adresów

Ten przykład dotyczy zasobów w sieciach wirtualnych, które są skojarzone z DefaultRouteTable.

**Efektywne trasy** na kartach sieciowych każdej maszyny wirtualnej znajdującej się w sieci wirtualnej szprych połączonej z koncentratorem wirtualnej sieci WAN powinny również zawierać prefiksy adresów dla reguł translatora adresów sieciowych **ExternalMapping**.

#### <a name="validate-bgp-advertisements"></a>Weryfikowanie anonsów BGP

Jeśli skonfigurowano protokół BGP dla połączenia z witryną sieci VPN, sprawdź, czy w lokalnym głośniku BGP znajduje się wpis, aby upewnić się, że anonsuje wpisu dla prefiksów mapowania zewnętrznego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfiguracji typu lokacja-lokacja, zobacz [Konfigurowanie wirtualnej sieci WAN połączenie lokacja-lokacja](virtual-wan-site-to-site-portal.md).

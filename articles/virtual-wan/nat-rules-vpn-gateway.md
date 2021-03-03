---
title: Konfigurowanie reguł NAT sieci VPN dla bramy
titleSuffix: Azure Virtual WAN
description: Dowiedz się, jak skonfigurować reguły NAT dla bramy sieci VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746930"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Konfigurowanie reguł NAT dla bramy sieci VPN wirtualnej sieci WAN — wersja zapoznawcza

> [!IMPORTANT]
> Reguły NAT są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wirtualną bramę sieci VPN w sieci WAN można skonfigurować przy użyciu statycznych reguł NAT jeden do jednego. Reguła NAT zapewnia mechanizm konfigurowania translacji jeden-do-jednego adresów IP. Translatora adresów sieciowych może służyć do łączenia dwóch sieci IP, które mają niezgodne lub nakładające się adresy IP. Typowy scenariusz to gałęzie z nakładanymi adresami IP, które chcą uzyskać dostęp do zasobów sieci wirtualnej platformy Azure.

Ta konfiguracja używa tabeli przepływu do kierowania ruchu z zewnętrznego (hosta) do wewnętrznego adresu IP skojarzonego z punktem końcowym w sieci wirtualnej (maszyna wirtualna, komputer, kontener itp.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram przedstawiający architekturę.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Konfigurowanie i wyświetlanie reguł

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

### <a name="configuration-considerations"></a>Zagadnienia dotyczące konfiguracji

* Rozmiar podsieci dla mapowania wewnętrznego i zewnętrznego musi być taki sam dla statycznego translatora adresów sieciowych jeden-do-jednego.
* Aby dodać prefiksy **ExternalMapping** w polu "prywatna przestrzeń adresowa", należy edytować witrynę sieci VPN w Azure Portal. Obecnie lokacje z włączonym protokołem BGP muszą mieć pewność, że lokalny anons protokołu BGP (Ustawienia protokołu BGP urządzenia) zawiera wpis dla prefiksów mapowania zewnętrznego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfiguracji typu lokacja-lokacja, zobacz [Konfigurowanie wirtualnej sieci WAN połączenie lokacja-lokacja](virtual-wan-site-to-site-portal.md).

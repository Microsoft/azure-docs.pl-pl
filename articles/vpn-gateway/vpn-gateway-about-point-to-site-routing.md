---
title: 'Azure VPN Gateway: informacje o routingu P2S'
description: Informacje na temat routingu sieci VPN typu punkt-lokacja w różnych systemach operacyjnych, protokołach dostępu zdalnego i konfiguracjach sieci wirtualnych.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91827226"
---
# <a name="about-point-to-site-vpn-routing"></a>Routing sieci VPN typu punkt-lokacja — informacje

Ten artykuł pomaga zrozumieć, jak działa routing sieci VPN typu punkt-lokacja. Zachowanie routingu sieci VPN P2S jest zależne od systemu operacyjnego klienta, protokołu używanego do połączenia sieci VPN oraz sposobu, w jaki sieci wirtualne (sieci wirtualnych) są połączone ze sobą.

Platforma Azure obsługuje obecnie dwa protokoły dla dostępu zdalnego, IKEv2 i SSTP. Protokół IKEv2 jest obsługiwany w wielu systemach operacyjnych klienta, takich jak Windows, Linux, macOS, Android i iOS. Protokół SSTP jest obsługiwany tylko w systemie Windows. Jeśli wprowadzisz zmiany w topologii sieci i masz klientów sieci VPN z systemem Windows, pakiet klienta VPN dla klientów systemu Windows musi zostać pobrany i zainstalowany ponownie, aby zmiany zostały zastosowane do klienta programu.

> [!NOTE]
> Ten artykuł ma zastosowanie tylko do protokołu IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Informacje o diagramach

W tym artykule istnieje wiele różnych diagramów. Każda sekcja zawiera inną topologię lub konfigurację. Na potrzeby tego artykułu połączenia typu lokacja-lokacja (S2S) i połączeń między sieciami wirtualnymi działają tak samo, jak w przypadku tuneli IPsec. Wszystkie bramy sieci VPN w tym artykule są oparte na trasach.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Jedna izolowana Sieć wirtualna

Połączenie bramy sieci VPN typu punkt-lokacja w tym przykładzie dotyczy sieci wirtualnej, która nie jest podłączona ani nie jest równorzędna z żadną inną siecią wirtualną (VNet1). W tym przykładzie klienci mogą uzyskać dostęp do VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Izolowany routing sieci wirtualnej" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci z systemem Windows mogą uzyskać dostęp do VNet1

* Klienci spoza systemu Windows mogą uzyskać dostęp do VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Wiele równorzędnych sieci wirtualnych

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 jest połączona z VNet2. Sieć wirtualna 2 jest połączona z usługą sieci vnet3. VNet1 jest połączona z sieci vnet4. Nie ma bezpośredniej komunikacji równorzędnej między VNet1 i sieci vnet3. VNet1 ma włączoną opcję "Zezwalaj na tranzyt bramy", a VNet2 i sieci vnet4 to "Użyj bram zdalnych".

Klienci korzystający z systemu Windows mogą uzyskać dostęp bezpośrednio do sieci wirtualnych komunikacji równorzędnej, ale klient VPN musi zostać pobrany ponownie, jeśli wprowadzono jakiekolwiek zmiany w komunikacji równorzędnej sieci wirtualnej lub topologii sieciowej. Klienci spoza systemu Windows mogą uzyskać dostęp do bezpośrednio równorzędnej sieci wirtualnych. Dostęp nie jest przechodni i jest ograniczony tylko do bezpośredniej komunikacji równorzędnej sieci wirtualnych.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Wiele równorzędnych sieci wirtualnych" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Sieci vnet3:10.3.0.0/16

* Sieci vnet4:10.4.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci z systemem Windows mogą uzyskać dostęp do VNet1, VNet2 i sieci vnet4, ale należy ponownie pobrać klienta sieci VPN, aby zmiany topologii zaczęły obowiązywać.

* Klienci spoza systemu Windows mogą uzyskać dostęp do VNet1, VNet2 i sieci vnet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Wiele sieci wirtualnych połączonych przy użyciu sieci VPN S2S

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do sieci vnet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej ani połączenia sieci VPN typu lokacja-lokacja między VNet1 i sieci vnet3. Dla wszystkich połączeń lokacja-lokacja nie jest uruchomiony protokół BGP dla routingu.

Klienci korzystający z systemu Windows lub innego obsługiwanego systemu operacyjnego mogą uzyskać dostęp tylko do VNet1. Aby można było uzyskać dostęp do dodatkowych sieci wirtualnych, należy użyć protokołu BGP.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Wiele sieci wirtualnych i S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Sieci vnet3:10.3.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci systemu Windows mogą uzyskać dostęp tylko do VNet1

* Klienci spoza systemu Windows mogą uzyskać dostęp tylko do VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Wiele sieci wirtualnych połączonych przy użyciu sieci VPN S2S (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do sieci vnet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej ani połączenia sieci VPN typu lokacja-lokacja między VNet1 i sieci vnet3. Wszystkie połączenia lokacja-lokacja używają protokołu BGP dla routingu.

Klienci korzystający z systemu Windows lub innego obsługiwanego systemu operacyjnego mogą uzyskać dostęp do wszystkich sieci wirtualnych, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, ale trasy do połączonych sieci wirtualnych muszą być dodawane ręcznie do klientów z systemem Windows.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Wiele sieci wirtualnych i S2S (BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Sieci vnet3:10.3.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów z systemem Windows: 10.1.0.0/16

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci systemu Windows mogą uzyskać dostęp do VNet1, VNet2 i sieci vnet3, ale trasy do VNet2 i sieci vnet3 należy dodać ręcznie.

* Klienci spoza systemu Windows mogą uzyskać dostęp do VNet1, VNet2 i sieci vnet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Jedna sieć wirtualna i oddział

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 nie jest połączona/równorzędna z żadną inną siecią wirtualną, ale jest połączona z lokacją lokalną za pośrednictwem połączenia sieci VPN typu lokacja-lokacja, które nie korzysta z protokołu BGP.

Komputery z systemem Windows i klientami spoza systemu Windows mogą uzyskać dostęp tylko do VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="Routing z siecią wirtualną i biurem oddziału" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci systemu Windows mogą uzyskać dostęp tylko do VNet1

* Klienci spoza systemu Windows mogą uzyskać dostęp tylko do VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Jedna sieć wirtualna i Biuro oddziału (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 nie jest połączony ani równorzędny z żadną inną siecią wirtualną, ale jest połączony z lokacją lokalną (Site1) za pośrednictwem połączenia sieci VPN typu lokacja-lokacja z włączonym protokołem BGP.

Klienci z systemem Windows mogą uzyskać dostęp do sieci wirtualnej i oddziału (Site1), ale trasy do Site1 muszą być dodawane ręcznie do klienta. Klienci spoza systemu Windows mogą uzyskać dostęp do sieci wirtualnej oraz lokalnego oddziału.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="Routing z siecią wirtualną i biurem oddziału — BGP" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci z systemem Windows mogą uzyskać dostęp do VNet1 i Site1, ale trasy do Site1 będą musiały być dodawane ręcznie.

* Klienci spoza systemu Windows mogą uzyskać dostęp do VNet1 i Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Wiele sieci wirtualnych połączonych przy użyciu S2S i biurze oddziału

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do sieci vnet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Między sieciami VNet1 i sieci vnet3 nie ma bezpośredniego tunelu komunikacji równorzędnej ani sieci VPN typu lokacja-lokacja. Sieci vnet3 jest podłączony do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN nie korzystają z protokołu BGP.

Wszyscy klienci mogą uzyskiwać dostęp tylko do VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Diagram przedstawiający wiele sieci S2S i oddział firmy Microsoft" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Sieci vnet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci systemu Windows mogą uzyskać dostęp tylko do VNet1

* Klienci spoza systemu Windows mogą uzyskać dostęp tylko do VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Wiele sieci wirtualnych połączonych przy użyciu połączenia S2S i biura oddziału (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do sieci vnet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Między sieciami VNet1 i sieci vnet3 nie ma bezpośredniego tunelu komunikacji równorzędnej ani sieci VPN typu lokacja-lokacja. Sieci vnet3 jest podłączony do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN korzystają z protokołu BGP.

Klienci korzystający z systemu Windows mogą uzyskać dostęp do sieci wirtualnych i lokacji połączonych przy użyciu połączenia sieci VPN typu lokacja-lokacja, ale trasy do VNet2, sieci vnet3 i Site1 muszą być dodawane ręcznie do klienta. Klienci spoza systemu Windows mogą uzyskać dostęp do sieci wirtualnych i witryn, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja bez żadnej interwencji ręcznej. Dostęp jest przechodni, a klienci mogą uzyskać dostęp do zasobów we wszystkich połączonych sieci wirtualnych i lokacjach (lokalnie).

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="połączenie S2S i oddział z wielosieciową siecią wirtualną" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Sieci vnet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Dodane trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów spoza systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienci systemu Windows mogą uzyskać dostęp do VNet1, VNet2, sieci vnet3 i Site1, ale trasy do VNet2, sieci vnet3 i Site1 należy dodać ręcznie do klienta.

* Klienci spoza systemu Windows mogą uzyskać dostęp do VNet1, Vnet2, sieci vnet3 i Site1.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć tworzenie sieci VPN P2S [, zobacz Tworzenie sieci VPN P2S przy użyciu Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

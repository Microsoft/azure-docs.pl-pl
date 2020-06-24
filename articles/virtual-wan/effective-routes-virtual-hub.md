---
title: 'Wyświetlanie efektywnych tras koncentratora wirtualnego: Azure Virtual WAN | Microsoft Docs'
description: Wyświetl efektywne trasy dla koncentratora wirtualnego w wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/02/2020
ms.author: cherylmc
ms.openlocfilehash: c7d436f2aecb021a7848ef0455a3f1c834cc38c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750544"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Wyświetlanie efektywnych tras koncentratora wirtualnego

W Azure Portal można wyświetlić wszystkie trasy wirtualnego koncentratora sieci WAN. Aby wyświetlić trasy, przejdź do koncentratora wirtualnego, a następnie wybierz pozycję **Routing-> Wyświetl efektywne trasy**.

## <a name="understanding-routes"></a><a name="understand"></a>Informacje o trasach

Poniższy przykład może pomóc lepiej zrozumieć sposób wyświetlania routingu wirtualnego sieci WAN.

W tym przykładzie mamy wirtualną sieć WAN z trzema centrami. Pierwsze centrum znajduje się w regionie Wschodnie stany USA, drugie centrum znajduje się w regionie Europa Zachodnia, a trzeci centrum znajduje się w regionie zachodnie stany USA. W wirtualnej sieci WAN wszystkie centra są połączone. W tym przykładzie przyjęto założenie, że centra Wschodnie stany USA i Europa Zachodnia mają połączenia z gałęzi lokalnych (szprychy) i sieci wirtualnych platformy Azure.

Połączenie sieci wirtualnej platformy Azure (10.4.0.0/16) z sieciowym urządzeniem wirtualnym (10.4.0.6) jest w dalszej komunikacji równorzędnej z siecią wirtualną (10.5.0.0/16). Więcej [informacji na](#abouthubroute) temat tabeli tras centrum znajduje się w dalszej części tego artykułu.

W tym przykładzie założono również, że gałąź Europa Zachodnia 1 jest połączona z centrum Wschodnie stany USA, a także do centrum Europa Zachodnia. Obwód ExpressRoute w regionie Wschodnie stany USA nawiązuje połączenie z gałęzią 2 do centrum Wschodnie stany USA.

![diagram](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Wyświetl efektywne trasy

Po wybraniu opcji "Wyświetl efektywne trasy" w portalu dane wyjściowe są wyświetlane w [tabeli tras centrum](#routetable) dla centrum Wschodnie stany USA.

Aby to zrobić w perspektywie, pierwszy wiersz oznacza, że centrum Wschodnie stany USA wyuczył trasę 10.20.1.0/24 (gałąź 1) z powodu połączenia *typu następnego przeskoku* VPN ("następny przeskok" VPN Gateway Instance0 IP 10.1.0.6, wystąpienia1 IP 10.1.0.7). *Kierowanie punktów początkowych* do identyfikatora zasobu. *Ścieżka as* wskazuje ścieżkę as dla gałęzi 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabela tras centrum

Użyj paska przewijania u dołu tabeli, aby wyświetlić ścieżkę "AS".

| **Prefiks** |  **Typ następnego skoku** | **Następny przeskok** |  **Źródło trasy** |**Ścieżka AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|Sieć VPN |10.1.0.6, 10.1.0.7| /subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-GW|21000|
|10.23.1.0/24| Sieć VPN |10.1.0.6, 10.1.0.7|/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW|23000|
|10.4.0.0/16|Połączenie Virtual Network| Na linku |  |  |
|10.5.0.0/16| Adres IP| 10.4.0.6|/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| Adres IP| `<Azure Firewall IP>` |/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| Koncentrator zdalny|10.8.0.6, 10.8.0.7|/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| Koncentrator zdalny|  Na linku |/subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_1| |

>[!NOTE]
> Jeśli środkowe stany USA i Europa Zachodnia nie komunikują się ze sobą w przykładowej topologii, nie istnieje trasa poznania (10.9.0.0/16). Koncentratory anonsują tylko te sieci, które są bezpośrednio połączone z nimi.
>

## <a name="additional-information"></a><a name="additional"></a>Dodatkowe informacje

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Informacje o tabeli tras centrum

Można utworzyć trasę koncentratora wirtualnego i zastosować trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego. Pozwala to na ustawienie trasy dla docelowej sieci wirtualnej za pośrednictwem adresu IP (zazwyczaj jest to sieciowe urządzenie wirtualne (urządzenie WUS). Aby uzyskać więcej informacji na temat urządzeń WUS, zobacz [kierowanie ruchu z koncentratora wirtualnego do urządzenie WUS](virtual-wan-route-table-portal.md). Należy pamiętać, że te trasy nie będą wyświetlane w tabeli obowiązujących tras. Obowiązująca tabela tras zawiera tylko prefiksy dla lokalnych i zdalnych centrów oraz połączone Virtual Network przestrzeń adresową i trasy uzyskane za pośrednictwem protokołu BGP.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Informacje o trasy domyślnej (0.0.0.0/0)

Koncentrator wirtualny ma możliwość propagowania zaznanej trasy domyślnej do sieci wirtualnej, sieci VPN typu lokacja-lokacja oraz połączenia ExpressRoute, jeśli flaga jest włączona w połączeniu. Ta flaga jest widoczna podczas edytowania połączenia sieci wirtualnej, połączenia sieci VPN lub połączenia ExpressRoute. Element "EnableInternetSecurity" jest zawsze domyślnie fałszywy w przypadku połączeń między sieciami wirtualnymi, ExpressRoute i VPN.

Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN. Trasa domyślna jest propagowana, jeśli jest już wykorzystana przez koncentrator wirtualnych sieci WAN w wyniku wdrożenia zapory w koncentratorze lub jeśli w innej połączonej lokacji włączono tunelowanie wymuszone.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

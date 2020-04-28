---
title: Nawiązywanie połączenia z siecią równorzędną w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak połączyć sieć laboratorium z inną siecią jako równorzędną. Na przykład Połącz lokalną sieć szkolną/Uniwersytet z siecią wirtualną laboratorium na platformie Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188008"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Połącz sieć laboratorium z równorzędną siecią wirtualną w Azure Lab Services

Ten artykuł zawiera informacje o komunikacji równorzędnej w sieci z inną siecią.

## <a name="overview"></a>Omówienie

Komunikacja równorzędna sieci wirtualnych umożliwia bezproblemowe łączenie sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pomocą prywatnych adresów IP. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).

Może być konieczne połączenie sieci laboratorium z równorzędną siecią wirtualną w niektórych scenariuszach, takich jak następujące:

- Maszyny wirtualne w laboratorium mają oprogramowanie łączące się z lokalnymi serwerami licencji w celu uzyskania licencji.
- Maszyny wirtualne w laboratorium muszą mieć dostęp do zestawów danych (lub innych plików) w udziałach sieciowych University.

Niektóre sieci lokalne są połączone z platformą Azure Virtual Network przy użyciu bramy [ExpressRoute](../../expressroute/expressroute-introduction.md) lub [Virtual Network](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Te usługi należy skonfigurować poza Azure Lab Services. Aby dowiedzieć się więcej na temat łączenia sieci lokalnej z platformą Azure przy użyciu usługi ExpressRoute, zobacz [Omówienie usługi ExpressRoute](../../expressroute/expressroute-introduction.md). W przypadku połączenia lokalnego przy użyciu bramy Virtual Network brama, określona Sieć wirtualna i konto laboratorium muszą znajdować się w tym samym regionie.

> [!NOTE]
> Podczas tworzenia Virtual Network platformy Azure, która będzie połączona z kontem laboratorium, ważne jest, aby zrozumieć, w jaki sposób region sieci wirtualnej ma wpływ na to, gdzie są tworzone laboratorium klasowe.  Aby uzyskać więcej informacji, zobacz sekcję Podręcznika administratora w witrynie [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfiguruj podczas tworzenia konta laboratorium

Podczas tworzenia nowego [konta laboratorium](tutorial-setup-lab-account.md)można wybrać istniejącą sieć wirtualną, która zostanie wyświetlona na liście rozwijanej **równorzędnej sieci wirtualnej** na karcie **Zaawansowane** .  Na liście zostaną wyświetlone tylko sieci wirtualne w tym samym regionie, w którym znajduje się konto laboratorium. Wybrana Sieć wirtualna jest połączona (równorzędna) do laboratoriów utworzonych w ramach konta laboratorium.  Wszystkie maszyny wirtualne w laboratoriach, które są tworzone po wprowadzeniu tej zmiany, będą miały dostęp do zasobów w sieci wirtualnej połączonej za pomocą komunikacji równorzędnej.

![Wybierz sieć wirtualną do elementu równorzędnego](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Zakres adresów

Istnieje również możliwość zapewnienia **zakresu adresów** dla maszyn wirtualnych dla laboratoriów.  Właściwość **zakres adresów** stosuje się tylko wtedy, gdy dla laboratorium jest włączona **równorzędna Sieć wirtualna** .  W przypadku podanego zakresu adresów wszystkie maszyny wirtualne w laboratoriach w ramach konta laboratorium zostaną utworzone w tym zakresie adresów. Zakres adresów powinien być w notacji CIDR (np. 10.20.0.0/20) i nie pokrywa się z żadnym istniejącymi zakresami adresów.  W przypadku podawania zakresu adresów należy wziąć pod uwagę liczbę *laboratoriów* , które zostaną utworzone, i podać zakres adresów do tego celu. Usługi laboratoryjne zakładają maksymalnie 512 maszyn wirtualnych na laboratorium.  Na przykład zakres adresów IP z "/23" może utworzyć tylko jedno laboratorium.  Zakres z "/21" umożliwi tworzenie czterech laboratoriów.

Jeśli **zakres adresów** nie zostanie określony, usługi Lab Services będą używać domyślnego zakresu adresów nadanego przez platformę Azure podczas tworzenia sieci wirtualnej w celu komunikacji równorzędnej z siecią wirtualną.  Zakres jest często podobny do 10. x. 0.0/16.  Może to prowadzić do nakładania się zakresów adresów IP, dlatego upewnij się, że w ustawieniach laboratorium określono zakres adresów, lub sprawdź, czy zakres adresów sieci wirtualnej jest w trybie komunikacji równorzędnej.

## <a name="configure-after-the-lab-is-created"></a>Konfiguruj po utworzeniu laboratorium

Tę samą Właściwość można włączyć na karcie **Konfiguracja laboratoriów** na stronie **konto laboratorium** , jeśli nie skonfigurowano sieci równorzędnej w momencie tworzenia konta laboratorium. Zmiana wprowadzona dla tego ustawienia ma zastosowanie tylko do laboratoriów, które są tworzone po zmianie. Jak widać w obrazie, możesz włączyć lub wyłączyć **równorzędną sieć wirtualną** dla laboratoriów na koncie laboratorium.

![Włącz lub Wyłącz komunikację równorzędną sieci wirtualnej po utworzeniu laboratorium](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

W przypadku wybrania sieci wirtualnej dla pola **równorzędna Sieć wirtualna** opcja **Zezwól na wybór laboratorium do wyboru lokalizacji laboratorium** jest wyłączona. Wynika to z faktu, że laboratoria w ramach konta laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby można było połączyć się z zasobami w równorzędnej sieci wirtualnej.

> [!IMPORTANT]
> Ustawienie równorzędnej sieci wirtualnej ma zastosowanie tylko do laboratoriów tworzonych po wprowadzeniu zmiany, a nie do istniejących laboratoriów.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Dołączanie galerii obrazów udostępnionych do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Skonfiguruj inne ustawienia laboratorium](how-to-configure-lab-accounts.md)

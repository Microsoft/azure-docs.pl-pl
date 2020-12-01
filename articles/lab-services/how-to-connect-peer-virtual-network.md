---
title: Nawiązywanie połączenia z siecią równorzędną w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak połączyć sieć laboratorium z inną siecią jako równorzędną. Na przykład Połącz lokalną sieć organizacji/Uniwersytet z siecią wirtualną laboratorium na platformie Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434247"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Połącz sieć laboratorium z równorzędną siecią wirtualną w Azure Lab Services

Ten artykuł zawiera informacje o komunikacji równorzędnej w sieci z inną siecią.

## <a name="overview"></a>Omówienie

Komunikacja równorzędna sieci wirtualnych umożliwia bezproblemowe łączenie sieci wirtualnych platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są traktowane jako jedna sieć. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pomocą prywatnych adresów IP. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md).

Może być konieczne połączenie sieci laboratorium z równorzędną siecią wirtualną w niektórych scenariuszach, takich jak następujące:

- Maszyny wirtualne w laboratorium mają oprogramowanie łączące się z lokalnymi serwerami licencji w celu uzyskania licencji.
- Maszyny wirtualne w laboratorium muszą mieć dostęp do zestawów danych (lub innych plików) w udziałach sieciowych University.

Niektóre sieci lokalne są połączone z platformą Azure Virtual Network przy użyciu bramy [ExpressRoute](../expressroute/expressroute-introduction.md) lub [Virtual Network](../vpn-gateway/vpn-gateway-about-vpngateways.md). Te usługi należy skonfigurować poza Azure Lab Services. Aby dowiedzieć się więcej na temat łączenia sieci lokalnej z platformą Azure przy użyciu usługi ExpressRoute, zobacz [Omówienie usługi ExpressRoute](../expressroute/expressroute-introduction.md). W przypadku połączenia lokalnego przy użyciu bramy Virtual Network brama, określona Sieć wirtualna i konto laboratorium muszą znajdować się w tym samym regionie.

> [!NOTE]
> Podczas tworzenia Virtual Network platformy Azure, która będzie połączona z kontem laboratorium, ważne jest, aby zrozumieć, w jaki sposób region sieci wirtualnej ma wpływ na miejsce tworzenia laboratoriów.  Aby uzyskać więcej informacji, zobacz sekcję Podręcznika administratora w witrynie [regions\locations](./administrator-guide.md#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfiguruj podczas tworzenia konta laboratorium

Podczas tworzenia nowego [konta laboratorium](tutorial-setup-lab-account.md)można wybrać istniejącą sieć wirtualną, która zostanie wyświetlona na liście rozwijanej **równorzędnej sieci wirtualnej** na karcie **Zaawansowane** .  Na liście zostaną wyświetlone tylko sieci wirtualne w tym samym regionie, w którym znajduje się konto laboratorium. Wybrana Sieć wirtualna jest połączona (równorzędna) do laboratoriów utworzonych w ramach konta laboratorium.  Wszystkie maszyny wirtualne w laboratoriach, które są tworzone po wprowadzeniu tej zmiany, będą miały dostęp do zasobów w sieci wirtualnej połączonej za pomocą komunikacji równorzędnej.

![Wybierz sieć wirtualną do elementu równorzędnego](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Zakres adresów

Istnieje również możliwość zapewnienia **zakresu adresów** dla maszyn wirtualnych dla laboratoriów.  Właściwość **zakres adresów** ma zastosowanie tylko wtedy, gdy dla laboratorium jest włączona **równorzędna Sieć wirtualna** . W przypadku podanego zakresu adresów wszystkie maszyny wirtualne w laboratoriach w ramach konta laboratorium zostaną utworzone w tym zakresie adresów. Zakres adresów powinien być w notacji CIDR (na przykład 10.20.0.0/20) i nie pokrywa się z żadnym istniejącymi zakresami adresów.  W przypadku podawania zakresu adresów należy wziąć pod uwagę liczbę *laboratoriów* , które zostaną utworzone, i podać zakres adresów do tego celu. Usługi laboratoryjne zakładają maksymalnie 512 maszyn wirtualnych na laboratorium.  Na przykład zakres adresów IP z "/23" może utworzyć tylko jedno laboratorium.  Zakres z "/21" umożliwi tworzenie czterech laboratoriów.

Jeśli **zakres adresów** nie zostanie określony, usługi Lab Services będą używać domyślnego zakresu adresów nadanego przez platformę Azure podczas tworzenia sieci wirtualnej w celu komunikacji równorzędnej z siecią wirtualną.  Zakres jest często podobny do 10. x. 0.0/16.  Może to prowadzić do nakładania się zakresów adresów IP, dlatego należy określić zakres adresów w ustawieniach laboratorium lub sprawdzić zakres adresów sieci wirtualnej, która jest wysyłana za pomocą komunikacji równorzędnej.

> [!NOTE]
> Tworzenie laboratorium może zakończyć się niepowodzeniem, jeśli konto laboratorium jest połączone z siecią wirtualną, ale ma zbyt wąski zakres adresów IP. Możesz zalogować się z zakresu adresów, jeśli na koncie laboratorium jest zbyt wiele laboratoriów (każde laboratorium używa 512 adresów). 
> 
> Jeśli Tworzenie laboratorium zakończy się niepowodzeniem, skontaktuj się z właścicielem/administratorem konta laboratorium i poproś o zwiększenie zakresu adresów. Administrator może zwiększyć zakres adresów przy użyciu kroków opisanych w sekcji [Określanie zakresu adresów dla maszyn wirtualnych w ramach konta laboratorium](#specify-an-address-range-for-vms-in-the-lab-account) . 

## <a name="configure-after-the-lab-account-is-created"></a>Konfiguruj po utworzeniu konta laboratorium

Tę samą Właściwość można włączyć na karcie **Konfiguracja laboratoriów** na stronie **konto laboratorium** , jeśli nie skonfigurowano sieci równorzędnej w momencie tworzenia konta laboratorium. Zmiana wprowadzona dla tego ustawienia ma zastosowanie tylko do laboratoriów, które są tworzone po zmianie. Jak widać w obrazie, możesz włączyć lub wyłączyć **równorzędną sieć wirtualną** dla laboratoriów na koncie laboratorium.

![Włącz lub Wyłącz komunikację równorzędną sieci wirtualnej po utworzeniu laboratorium](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

W przypadku wybrania sieci wirtualnej dla pola **równorzędna Sieć wirtualna** opcja **Zezwól na wybór laboratorium do wyboru lokalizacji laboratorium** jest wyłączona. Wynika to z faktu, że laboratoria w ramach konta laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby można było połączyć się z zasobami w równorzędnej sieci wirtualnej.

> [!IMPORTANT]
> Ustawienie równorzędnej sieci wirtualnej ma zastosowanie tylko do laboratoriów tworzonych po wprowadzeniu zmiany, a nie do istniejących laboratoriów.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Określ zakres adresów dla maszyn wirtualnych w ramach konta laboratorium
Poniższa procedura zawiera kroki umożliwiające określenie zakresu adresów dla maszyn wirtualnych w laboratorium. W przypadku aktualizacji zakresu, który został wcześniej określony, zmodyfikowany zakres adresów ma zastosowanie tylko do maszyn wirtualnych, które zostały utworzone po wprowadzeniu zmiany. 

Poniżej przedstawiono niektóre ograniczenia dotyczące określania zakresu adresów, który należy zachować na uwadze. 

- Prefiks musi być mniejszy lub równy 23. 
- Jeśli sieć wirtualna jest połączona z kontem laboratorium, podany zakres adresów nie może pokrywać się z zakresem adresów z równorzędnej sieci wirtualnej.

1. Na stronie **konto laboratorium** wybierz pozycję **Ustawienia laboratoria** w menu po lewej stronie.
2. Dla pola **zakres adresów** Określ zakres adresów dla maszyn wirtualnych, które zostaną utworzone w laboratorium. Zakres adresów powinien znajdować się w notacji Classless Inter-Domain Routing (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Konfiguruj zakres adresów](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Dołączanie galerii obrazów udostępnionych do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Skonfiguruj inne ustawienia laboratorium](how-to-configure-lab-accounts.md)
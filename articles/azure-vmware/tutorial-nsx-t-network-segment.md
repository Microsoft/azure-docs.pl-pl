---
title: Samouczek — Tworzenie segmentu sieci NSX-T w rozwiązaniu Azure VMware
description: Dowiedz się, jak utworzyć segmenty sieci NSX-T, które są używane na potrzeby maszyn wirtualnych w programie vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 780cac15efc043b9ae44b77af1234adca3fec5a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254537"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Samouczek: Tworzenie segmentu sieci NSX-T w rozwiązaniu Azure VMware

Segmenty sieci utworzone w Menedżerze NSX-T są używane jako sieci maszyn wirtualnych w programie vCenter. Maszyny wirtualne utworzone w programie vCenter są umieszczane na segmentach sieci utworzonych w NSX-T i są widoczne w programie vCenter.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawigowanie w Menedżerze NSX-T w celu dodania segmentów sieci
> * Dodaj nowy segment sieci
> * Obserwuj nowy segment sieci w programie vCenter

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka wymagane jest chmurę prywatną rozwiązania VMware platformy Azure z dostępem do interfejsów zarządzania Menedżera vCenter i NSX-T. Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Konfigurowanie sieci dla swojej chmury prywatnej VMware w systemie Azure](tutorial-configure-networking.md) .

## <a name="provision-a-network-segment-in-nsx-t"></a>Inicjowanie obsługi administracyjnej segmentu sieci w NSX-T

1. W programie vCenter dla chmury prywatnej wybierz pozycję **SDDC-Datacenter > Networks** i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. W Menedżerze NSX-T dla chmury prywatnej wybierz pozycję **Sieć**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Wybierz **segmenty**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Na stronie Przegląd segmentów NSX-T wybierz pozycję **Dodaj segment**. Trzy segmenty są tworzone w ramach aprowizacji chmury prywatnej i nie mogą być używane w przypadku maszyn wirtualnych.  W tym celu musisz dodać nowy segment sieci.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Nazwij segment, wybierz wstępnie skonfigurowaną bramę pomoc (TNTxx-T1) jako **połączoną bramę**, pozostaw **Typ** jako elastyczny, wybierz wstępnie skonfigurowaną **strefę transportu** nakładki (TNTxx-nakładki-lt), a następnie wybierz pozycję Ustaw podsieci. Wszystkie inne ustawienia w tej sekcji oraz profile **portów** i **segmentów** mogą pozostać w domyślnym formacie, zgodnie z konfiguracją.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Ustaw adres IP bramy dla nowego segmentu, a następnie wybierz pozycję **Dodaj**. Używany adres IP musi znajdować się w nienakładających się blokach adresów RFC1918, co zapewnia możliwość nawiązywania połączenia z maszynami wirtualnymi w nowym segmencie.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Zastosuj nowy segment sieci, wybierając pozycję **Zastosuj** , a następnie Zapisz konfigurację przy użyciu polecenia **Zapisz**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Nowy segment sieci został utworzony i odrzucisz opcję, aby kontynuować konfigurowanie segmentu, wybierając pozycję **nie**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Upewnij się, że nowy segment sieci jest obecny w NSX-T, wybierając pozycję **sieć > segmenty** i zobaczysz nowy segment na liście (w tym przypadku "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

1. Potwierdź, że nowy segment sieci jest obecny w programie vCenter, wybierając pozycję **sieć > SDDC-Datacenter** i obserwowanie nowego segmentu znajduje się na liście (w tym przypadku "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="W programie vCenter dla chmury prywatnej wybierz pozycję SDDC-Datacenter > sieci i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono segmenty sieci NSX-T, które są używane dla maszyn wirtualnych w programie vCenter. Teraz możesz [utworzyć bibliotekę zawartości, aby wdrożyć maszyny wirtualne w rozwiązaniu VMware platformy Azure](deploy-vm-content-library.md) i zainicjować obsługę administracyjną maszyny wirtualnej w sieci utworzonej w tym samouczku.

W przeciwnym razie przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć ExpressRoute Global Reach komunikację równorzędną z chmurą prywatną w rozwiązaniu VMware platformy Azure.

> [!div class="nextstepaction"]
> [Równorzędne środowiska lokalne z chmurą prywatną](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->

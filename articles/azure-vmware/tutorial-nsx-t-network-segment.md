---
title: 'Samouczek: Tworzenie segmentu sieci NSX-T w rozwiązaniu Azure VMware (Automatyczna synchronizacja)'
description: W tym samouczku utworzono segmenty sieci NSX-T, które są używane dla maszyn wirtualnych w programie vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 5654fbb6a063d4dfeb541c20407f9a09dff1509f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101671"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution-avs"></a>Samouczek: Tworzenie segmentu sieci NSX-T w rozwiązaniu Azure VMware (Automatyczna synchronizacja)

Segmenty sieci utworzone w Menedżerze NSX-T są używane jako sieci maszyn wirtualnych w programie vCenter. Maszyny wirtualne utworzone w programie vCenter są umieszczane na segmentach sieci utworzonych w NSX-T i są widoczne w programie vCenter.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawigowanie w Menedżerze NSX-T w celu dodania segmentów sieci
> * Dodaj nowy segment sieci
> * Obserwuj nowy segment sieci w programie vCenter

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są wymagane następujące chmury prywatne w wersji załączonej z dostępem do interfejsów zarządzania Menedżera vCenter i NSX-T. Zobacz [Samouczek: Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Inicjowanie obsługi administracyjnej segmentu sieci w NSX-T

1. W programie vCenter dla chmury prywatnej wybierz pozycję **SDDC-Datacenter > Networks** i zwróć uwagę na to, że nie ma jeszcze żadnych sieci.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Wybierz SDDC — sieci > centrum danych":::

1. W Menedżerze NSX-T dla chmury prywatnej wybierz pozycję **Sieć**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Wybierz pozycję Sieć, aby przejść do widoku omówienia sieci Menedżera NSX-T.":::

1. Wybierz **segmenty**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Wybierz segmenty na stronie Przegląd sieci.":::

1. Na stronie Przegląd segmentów NSX-T wybierz pozycję **Dodaj segment**. Trzy segmenty są tworzone w ramach aprowizacji chmury prywatnej i nie mogą być używane w przypadku maszyn wirtualnych.  W tym celu musisz dodać nowy segment sieci.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Na stronie Omówienie opcji Dodaj segment z segmentu sieci.":::

1. Nazwij segment, wybierz wstępnie skonfigurowaną bramę pomoc (TNTxx-T1) jako **połączoną bramę**, pozostaw **Typ** jako elastyczny, wybierz wstępnie skonfigurowaną **strefę transportu** nakładki (TNTxx-nakładki-lt), a następnie wybierz pozycję Ustaw podsieci. Wszystkie inne ustawienia w tej sekcji oraz profile **portów** i **segmentów** mogą pozostać w domyślnym formacie, zgodnie z konfiguracją.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Ustaw nazwę segmentu, połączoną bramę i typ oraz strefę transportu, a następnie wybierz pozycję "Ustaw podsieci".":::

1. Ustaw adres IP bramy dla nowego segmentu, a następnie wybierz pozycję **Dodaj**. Używany adres IP musi znajdować się w nienakładających się blokach adresów RFC1918, co zapewnia możliwość nawiązywania połączenia z maszynami wirtualnymi w nowym segmencie.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Określ adres IP bramy segmentu w notacji CIDR i wybierz pozycję Dodaj.":::

1. Zastosuj nowy segment sieci, wybierając pozycję **Zastosuj** , a następnie Zapisz konfigurację przy użyciu polecenia **Zapisz**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Zastosuj nowy segment sieci do konfiguracji NSX-T z ZASTOSOWANIEm.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Zapisz nowy segment sieci w konfiguracji NSX-T z opcją Zapisz.":::

1. Nowy segment sieci został utworzony i odrzucisz opcję, aby kontynuować konfigurowanie segmentu, wybierając pozycję **nie**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Odrzuć, aby skonfigurować nowo utworzony segment sieci, wybierając pozycję nie.":::

1. Upewnij się, że nowy segment sieci jest obecny w NSX-T, wybierając pozycję **sieć > segmenty** i zobaczysz nowy segment na liście (w tym przypadku "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Upewnij się, że nowy segment sieci jest obecny w NSX-T.":::

1. Potwierdź, że nowy segment sieci jest obecny w programie vCenter, wybierając pozycję **sieć > SDDC-Datacenter** i obserwowanie nowego segmentu znajduje się na liście (w tym przypadku "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Upewnij się, że nowy segment sieci jest obecny w programie vCenter.":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono segmenty sieci NSX-T, które są używane dla maszyn wirtualnych w programie vCenter. Teraz możesz skorzystać z [samouczka: Tworzenie biblioteki zawartości w celu wdrażania maszyn wirtualnych w usłudze Azure VMware Solution (Automatyczna synchronizacja)](tutorial-deploy-vm-content-library.md) w celu utworzenia biblioteki zawartości w programie vCenter oraz udostępnienia maszyny wirtualnej w sieci utworzonej w tym samouczku.

<!-- LINKS - external-->

<!-- LINKS - internal -->

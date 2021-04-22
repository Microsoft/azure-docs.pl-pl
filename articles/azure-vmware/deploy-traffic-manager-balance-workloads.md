---
title: Wdrażanie Traffic Manager równoważenia Azure VMware Solution obciążeń
description: Dowiedz się, jak zintegrować Traffic Manager z Azure VMware Solution, aby równoważyć obciążenia aplikacji w wielu punktach końcowych w różnych regionach.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876926"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Wdrażanie Traffic Manager równoważenia Azure VMware Solution obciążeń

W tym artykule opisano kroki integracji Azure Traffic Manager [z](../traffic-manager/traffic-manager-overview.md) Azure VMware Solution. Integracja równoważy obciążenia aplikacji w wielu punktach końcowych. W tym artykule opisano również kroki konfigurowania usługi Traffic Manager [](../application-gateway/overview.md) do kierowania ruchu między trzema Azure Application Gateway obejmującymi kilka Azure VMware Solution regionów. 

Bramy mają skonfigurowane Azure VMware Solution wirtualne jako elementy członkowskie puli zaplecza w celu równoważenia obciążenia przychodzących żądań warstwy 7. Aby uzyskać więcej informacji, zobacz [Używanie Azure Application Gateway do ochrony aplikacji internetowych w Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)

Na diagramie pokazano, Traffic Manager równoważenie obciążenia dla aplikacji na poziomie systemu DNS między regionalnymi punktami końcowymi. Bramy mają składowe puli zaplecza skonfigurowane jako serwery usług IIS i są przywołyne Azure VMware Solution zewnętrznych punktów końcowych. Połączenie za pośrednictwem sieci wirtualnej między dwoma regionami chmury prywatnej używa bramy usługi ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagram architektury integracji Traffic Manager z Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Przed rozpoczęciem zapoznaj się z tematem [Wymagania wstępne,](#prerequisites) a następnie zapoznaj się z procedurami, aby:

> [!div class="checklist"]
> * Weryfikowanie konfiguracji bram aplikacji i segmentu NSX-T
> * Tworzenie profilu Traffic Manager aplikacji
> * Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager końcowego

## <a name="prerequisites"></a>Wymagania wstępne

- Trzy maszyny wirtualne skonfigurowane jako serwery microsoft IIS działające w różnych Azure VMware Solution regionach: 
   - Zachodnie stany USA
   - West Europe
   - Wschodnie usa (lokalne) 

- Brama aplikacji z zewnętrznymi punktami końcowymi w regionach Azure VMware Solution wymienionych powyżej.

- Hostuj z łącznością z Internetem na celu weryfikacji. 

- Segment [sieci NSX-T utworzony w Azure VMware Solution](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Weryfikowanie konfiguracji bram aplikacji

Poniższe kroki weryfikują konfigurację bram aplikacji.

1. W Azure Portal wybierz pozycję **Bramy aplikacji,** aby wyświetlić listę bieżących bram aplikacji:

   - AVS-GW-WUS
   - AVS-GW-EUS (lokalnie)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Zrzut ekranu przedstawiający stronę bramy aplikacji z listą skonfigurowanych bram aplikacji." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Wybierz jedną z wcześniej wdrożonych bram aplikacji. 

   Zostanie otwarte okno z różnymi informacjami o bramie aplikacji. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Zrzut ekranu przedstawiający stronę bramy aplikacji ze szczegółami wybranej bramy aplikacji." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Wybierz **pozycję Pule zaplecza,** aby zweryfikować konfigurację jednej z pul zaplecza. Zobaczysz jeden element członkowski puli zaplecza maszyny wirtualnej skonfigurowany jako serwer internetowy o adresie IP 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie puli zaplecza z wyróżnionym docelowym adresem IP.":::

1. Sprawdź konfigurację innych bram aplikacji i elementów członkowskich puli zaplecza. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Weryfikowanie konfiguracji segmentu NSX-T

Poniższe kroki weryfikują konfigurację segmentu NSX-T w Azure VMware Solution środowisku.

1. Wybierz **pozycję Segmenty,** aby wyświetlić skonfigurowane segmenty.  Zostanie wyświetlony ekran Contoso-segment1 połączony z bramą Contoso-T01, elastycznym routerem warstwy 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Zrzut ekranu przedstawiający profile segmentów w NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Wybierz **pozycję Bramy warstwy 1,** aby wyświetlić listę bram warstwy 1 z liczbą połączonych segmentów. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Zrzut ekranu przedstawiający adres bramy wybranego segmentu.":::    

1. Wybierz segment połączony z contoso-T01. Zostanie otwarte okno z interfejsem logicznym skonfigurowanym na routerze warstwy 01. Służy jako brama maszyny wirtualnej członka puli zaplecza podłączonej do segmentu.

1. W kliencie vSphere wybierz maszynę wirtualną, aby wyświetlić jej szczegóły. 

   >[!NOTE]
   >Jej adres IP pasuje do członka puli zaplecza maszyny wirtualnej skonfigurowanego jako serwer internetowy z poprzedniej sekcji: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły maszyny wirtualnej w kliencie vSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Wybierz maszynę wirtualną, a następnie wybierz pozycję ACTIONS > Edit Settings (Edytuj **ustawienia),** aby zweryfikować połączenie z segmentem NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://rc.portal.azure.com/#home). W **obszarze Azure Services > Networking** wybierz pozycję Traffic Manager **profilów.**

2. Wybierz **pozycję + Dodaj,** aby utworzyć nowy Traffic Manager profilu.
 
3. Podaj następujące informacje, a następnie wybierz pozycję **Utwórz:**

   - Nazwa profilu
   - Metoda routingu (użyj [ważonej)](../traffic-manager/traffic-manager-routing-methods.md)
   - Subskrypcja
   - Grupa zasobów

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager końcowego

1. Wybierz profil Traffic Manager w okienku wyników wyszukiwania, wybierz pozycję **Punkty końcowe,** a następnie **pozycję + Dodaj**.

1. Dla każdego z zewnętrznych punktów końcowych w różnych regionach wprowadź wymagane szczegóły, a następnie wybierz pozycję **Dodaj**: 
   - Typ
   - Nazwa
   - W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP
   - Waga (przypisz wagę 1 do każdego punktu końcowego). 

   Po utworzeniu wszystkie trzy są Traffic Manager profilu. Stan monitora wszystkich trzech musi mieć stan **Online.**

3. Wybierz **pozycję Przegląd** i skopiuj adres URL w obszarze Nazwa **DNS.**

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Zrzut ekranu przedstawiający przegląd Traffic Manager punktu końcowego z wyróżniona nazwą DNS." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Wklej adres URL nazwy DNS w przeglądarce. Zrzut ekranu przedstawia ruch skierowany do regionu Europa Zachodnia.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Zrzut ekranu przedstawiający okno przeglądarki z ruchem kierowanego do Europy Zachodniej."::: 

5. Odśwież przeglądarkę. Zrzut ekranu przedstawia ruch skierowany do innego zestawu elementów członkowskich puli zaplecza w regionie Zachodnie stany USA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Zrzut ekranu przedstawiający okno przeglądarki z ruchem kierowanego do zachodnie stany USA."::: 

6. Odśwież przeglądarkę ponownie. Zrzut ekranu przedstawia ruch skierowany do końcowego zestawu lokalnych elementów członkowskich puli zaplecza.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Zrzut ekranu przedstawiający okno przeglądarki z ruchem kierowanego do sieci lokalnej.":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zintegrować Azure Traffic Manager z Azure VMware Solution, możesz dowiedzieć się więcej na temat:

- [Korzystanie Azure Application Gateway na Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)
- [Metody routingu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)
- [Łączenie usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Mierzenie Traffic Manager wydajności](../traffic-manager/traffic-manager-performance-considerations.md)

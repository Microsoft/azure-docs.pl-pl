---
title: Wdróż Traffic Manager, aby zrównoważyć obciążenia rozwiązań VMware platformy Azure
description: Dowiedz się, jak zintegrować Traffic Manager z rozwiązaniem VMware platformy Azure, aby zrównoważyć obciążenia aplikacji w wielu punktach końcowych w różnych regionach.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988597"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Wdróż Traffic Manager, aby zrównoważyć obciążenia rozwiązań VMware platformy Azure

W tym artykule omówiono procedurę integrowania [usługi azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) z rozwiązaniem VMware platformy Azure. Integracja równoważy obciążenia aplikacji w wielu punktach końcowych. W tym artykule przedstawiono również procedurę konfigurowania Traffic Manager w celu kierowania ruchu między trzema [Application Gateway platformy Azure](../application-gateway/overview.md) , obejmujących kilka regionów rozwiązań VMware platformy Azure. 

Bramy mają maszyny wirtualne rozwiązania Azure VMware, które zostały skonfigurowane jako elementy członkowskie puli zaplecza do równoważenia obciążenia przychodzących żądań warstwy 7. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi azure Application Gateway do ochrony aplikacji sieci Web na platformie Azure VMware](protect-azure-vmware-solution-with-application-gateway.md)

Na diagramie przedstawiono sposób, w jaki Traffic Manager zapewnia Równoważenie obciążenia dla aplikacji na poziomie systemu DNS między regionalnymi punktami końcowymi. Bramy mają członków puli zaplecza skonfigurowanych jako serwery IIS i przywoływanych jako zewnętrzne punkty końcowe rozwiązania Azure VMware. Połączenie przez sieć wirtualną między dwoma regionami chmury prywatnej używa bramy ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagram architektury Traffic Manager integracji z rozwiązaniem VMware platformy Azure" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Przed rozpoczęciem należy najpierw przejrzeć [wymagania wstępne](#prerequisites) , a następnie zapoznać się z procedurami, aby:

> [!div class="checklist"]
> * Weryfikowanie konfiguracji bram aplikacji i segmentu NSX-T
> * Tworzenie profilu Traffic Manager
> * Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager

## <a name="prerequisites"></a>Wymagania wstępne

- Trzy maszyny wirtualne skonfigurowane jako serwery usług IIS firmy Microsoft działające w różnych regionach rozwiązań VMware platformy Azure: 
   - Zachodnie stany USA
   - West Europe
   - Wschodnie stany USA (lokalnie) 

- Brama aplikacji z zewnętrznymi punktami końcowymi w regionach rozwiązań platformy Azure VMware wymienionych powyżej.

- Host z łącznością internetową w celu weryfikacji. 

- [Segment sieci NSX-T utworzony w rozwiązaniu VMware platformy Azure](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Weryfikowanie konfiguracji bram aplikacji

Poniższe kroki weryfikują konfigurację bram aplikacji.

1. W Azure Portal wybierz pozycję **bramy aplikacji** , aby wyświetlić listę bieżących bram aplikacji:

   - AUTOMATYCZNA SYNCHRONIZACJA — GW-WUS
   - Automatyczna synchronizacja — GW-EUS (lokalna)
   - AUTOMATYCZNA SYNCHRONIZACJA — GW-UZE

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Zrzut ekranu przedstawiający stronę bramy Application Gateway z listą skonfigurowanych bram aplikacji." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Wybierz jedną z wcześniej wdrożonych bram aplikacji. 

   Zostanie otwarte okno zawierające różne informacje o bramie aplikacji. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Zrzut ekranu strony bramy Application Gateway przedstawiający szczegóły wybranej bramy aplikacji." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Wybierz pozycję **Pule zaplecza** , aby zweryfikować konfigurację jednej z pul zaplecza. Zostanie wyświetlony jeden element członkowski puli zaplecza maszyny wirtualnej skonfigurowany jako serwer sieci Web z adresem IP 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Zrzut ekranu strony edytowanie puli zaplecza z wyróżnionym docelowym adresem IP.":::

1. Sprawdź konfigurację innych bram aplikacji i członków puli zaplecza. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Weryfikowanie konfiguracji segmentu NSX-T

Poniższe kroki weryfikują konfigurację segmentu NSX-T w środowisku rozwiązań VMware platformy Azure.

1. Wybierz **segmenty** , aby wyświetlić skonfigurowane segmenty.  Zobaczysz, że contoso-SEGMENT1 połączono z bramą contoso-T01, elastycznym routerem warstwy 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Zrzut ekranu przedstawiający profile segmentów w Menedżerze NSX-T." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Wybierz **bramy warstwy 1** , aby wyświetlić listę bram warstwy 1 z liczbą połączonych segmentów. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Zrzut ekranu przedstawiający adres bramy wybranego segmentu.":::    

1. Wybierz segment połączony z Contoso-T01. Zostanie otwarte okno z interfejsem logicznym skonfigurowanym na routerze warstwy-01. Służy jako brama do maszyny wirtualnej elementu członkowskiego puli zaplecza połączonej z segmentem.

1. W kliencie vSphere wybierz maszynę wirtualną, aby wyświetlić jej szczegóły. 

   >[!NOTE]
   >Jego adres IP jest zgodny z elementem członkowskim puli zaplecza maszyny wirtualnej skonfigurowanym jako serwer sieci Web w poprzedniej sekcji: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły maszyny wirtualnej w kliencie vSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Wybierz maszynę wirtualną, a następnie wybierz pozycję **akcje > Edytuj ustawienia** , aby zweryfikować połączenie z segmentem NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

1. Zaloguj się w witrynie [Azure Portal](https://rc.portal.azure.com/#home). W obszarze **usługi platformy Azure > sieć** wybierz pozycję **Traffic Manager profile**.

2. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy profil Traffic Manager.
 
3. Podaj następujące informacje, a następnie wybierz pozycję **Utwórz**:

   - Nazwa profilu
   - Routing — Metoda (użycie [ważone](../traffic-manager/traffic-manager-routing-methods.md)
   - Subskrypcja
   - Grupa zasobów

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager

1. Wybierz profil Traffic Manager z okienka wyników wyszukiwania, wybierz **punkty końcowe**, a następnie **+ Dodaj**.

1. Dla każdego z zewnętrznych punktów końcowych w różnych regionach wprowadź wymagane szczegóły, a następnie wybierz pozycję **Dodaj**: 
   - Typ
   - Nazwa
   - W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP
   - Waga (Przypisz wagę 1 do każdego punktu końcowego). 

   Po utworzeniu wszystkie trzy są wyświetlane w profilu Traffic Manager. Stan monitora wszystkich trzech musi być w **trybie online**.

3. Wybierz pozycję **Przegląd** i skopiuj adres URL pod **nazwą DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Zrzut ekranu przedstawiający przegląd Traffic Manager punktu końcowego z wyróżnioną nazwą DNS." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Wklej adres URL nazwy DNS w przeglądarce. Zrzut ekranu przedstawia ruch kierowany do regionu Europa Zachodnia.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do Europa Zachodnia."::: 

5. Odśwież przeglądarkę. Zrzut ekranu przedstawia ruch kierowany do innego zestawu elementów członkowskich puli zaplecza w regionie zachodnie stany USA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do regionu zachodnie stany USA."::: 

6. Odśwież przeglądarkę ponownie. Zrzut ekranu przedstawia ruch kierowany do końcowego zestawu członków puli zaplecza lokalnie.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do lokalnego.":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy omówiono integrację usługi Azure Traffic Manager z rozwiązaniem VMware platformy Azure, warto zapoznać się z tematem:

- [Korzystanie z usługi azure Application Gateway w rozwiązaniu VMware platformy Azure](protect-azure-vmware-solution-with-application-gateway.md).
- [Traffic Manager metod routingu](../traffic-manager/traffic-manager-routing-methods.md).
- [Łączenie usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Mierzenie wydajności Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md).

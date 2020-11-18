---
title: Wdróż Traffic Manager, aby zrównoważyć obciążenia Azure VMware Solution (Automatyczna synchronizacja)
description: Dowiedz się, jak zintegrować Traffic Manager z rozwiązaniem VMware platformy Azure (Automatyczna synchronizacja), aby zrównoważyć obciążenia aplikacji w wielu punktach końcowych w różnych regionach.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874310"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Wdróż Traffic Manager, aby zrównoważyć obciążenia Azure VMware Solution (Automatyczna synchronizacja)

W tym artykule omówiono integrację Traffic Manager z rozwiązaniem VMware platformy Azure (Automatyczna synchronizacja) w celu zrównoważenia obciążeń aplikacji w wielu punktach końcowych. Zobaczymy scenariusz, w którym Traffic Manager będzie kierować ruchem między trzema bramami aplikacji obejmującymi kilka regionów automatycznej synchronizacji: zachodnie stany USA, Europa Zachodnia i lokalne w regionie Wschodnie stany USA. 

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia, który umożliwia optymalne dystrybuowanie ruchu do usług w globalnych regionach platformy Azure. Równoważenie obciążenia ruchu aplikacji między uruchomionymi obciążeniami platformy Azure i zewnętrznymi publicznymi punktami końcowymi. Aby uzyskać więcej informacji na temat Traffic Manager, zobacz [co to jest Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

Najpierw zapoznaj się z [wymaganiami wstępnymi](#prerequisites) . następnie przeprowadzimy procedurę, aby:

> [!div class="checklist"]
> * Weryfikowanie konfiguracji bram aplikacji
> * Sprawdź konfigurację segmentu NSX-T
> * Tworzenie profilu Traffic Manager
> * Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager

## <a name="topology"></a>Topologia

Jak pokazano na poniższej ilustracji, usługa Azure Traffic Manager zapewnia Równoważenie obciążenia dla aplikacji na poziomie systemu DNS między regionalnymi punktami końcowymi. Bramy aplikacji mają członków puli zaplecza skonfigurowanych jako serwery IIS i są przywoływane jako zewnętrzne punkty końcowe synchronizacji.

Połączenie przez sieć wirtualną między dwoma regionami chmury prywatnej, zachodnim stanem USA i Europa Zachodnia oraz serwerem lokalnym w regionie Wschodnie stany USA używa bramy ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagram architektury Traffic Manager integracji z rozwiązaniem VMware platformy Azure" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>Wymagania wstępne

- Trzy maszyny wirtualne skonfigurowane jako serwery Microsoft IIS działające w różnych regionach automatycznej synchronizacji: zachodnie stany USA, Europa Zachodnia i lokalne. 

- Brama aplikacji z zewnętrznymi punktami końcowymi w regionie zachodnie stany USA, Europa Zachodnia i lokalnie.

- Host z łącznością internetową w celu weryfikacji. 

## <a name="verify-configuration-of-your-application-gateways"></a>Weryfikowanie konfiguracji bram aplikacji

[Usługa Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) to moduł równoważenia obciążenia sieci Web warstwy 7, który umożliwia zarządzanie ruchem do aplikacji sieci Web. Aby uzyskać więcej informacji na temat Application Gateway, zobacz [co to jest usługa Azure Application Gateway?](../application-gateway/overview.md) 

W tym scenariuszu trzy wystąpienia bramy aplikacji są skonfigurowane jako zewnętrzne punkty końcowe automatycznej synchronizacji. Bramy aplikacji mają maszyny wirtualne o automatycznej synchronizacji, które są skonfigurowane jako elementy członkowskie puli zaplecza do równoważenia obciążenia przychodzących żądań warstwy 7. (Aby dowiedzieć się, jak skonfigurować Application Gateway z maszynami wirtualnymi o automatycznej synchronizacji jako pule zaplecza, zobacz [Korzystanie z usługi azure Application Gateway do ochrony aplikacji sieci Web na platformie Azure VMware](protect-azure-vmware-solution-with-application-gateway.md).)  

Poniższe kroki sprawdzają poprawność konfiguracji bram aplikacji.

1. Otwórz Azure Portal a następnie wybierz pozycję **bramy aplikacji** , aby wyświetlić listę bieżących bram aplikacji. 

    W tym scenariuszu skonfigurowano trzy bramy aplikacji:
    - AUTOMATYCZNA SYNCHRONIZACJA — GW-WUS
    - Automatyczna synchronizacja — GW-EUS (lokalnie)
    - AUTOMATYCZNA SYNCHRONIZACJA — GW-UZE

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Zrzut ekranu przedstawiający stronę bramy Application Gateway z listą skonfigurowanych bram aplikacji." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Wybierz jedną z wcześniej wdrożonych bram aplikacji. Zostanie otwarte okno zawierające różne informacje o bramie aplikacji. Wybierz pozycję **Pule zaplecza** , aby zweryfikować konfigurację jednej z pul zaplecza.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Zrzut ekranu strony bramy Application Gateway przedstawiający szczegóły wybranej bramy aplikacji." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. W takim przypadku zobaczysz jeden element członkowski puli zaplecza maszyny wirtualnej skonfigurowany jako serwer sieci Web z adresem IP 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Zrzut ekranu strony edytowanie puli zaplecza z wyróżnionym docelowym adresem IP.":::

    Można w podobny sposób sprawdzić konfigurację innych bram aplikacji i członków puli zaplecza. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Sprawdź konfigurację segmentu NSX-T

Segmenty sieci utworzone w Menedżerze NSX-T są używane jako sieci maszyn wirtualnych w programie vCenter. Aby uzyskać więcej informacji, zobacz samouczek [: Tworzenie segmentu sieci NSX-T w rozwiązaniu programu Azure VMware (Automatyczna synchronizacja)](tutorial-nsx-t-network-segment.md).

W naszym scenariuszu segment NSX-T jest konfigurowany w środowisku automatycznej synchronizacji, w którym jest dołączona maszyna wirtualna elementu członkowskiego puli zaplecza.

1. Wybierz **segmenty** , aby wyświetlić skonfigurowane segmenty. W takim przypadku widzimy, że firma Contoso-SEGMENT1 jest połączona z bramą contoso-T01, elastycznym routerem warstwy 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Zrzut ekranu przedstawiający profile segmentów w Menedżerze NSX-T." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. Wybierz pozycję **bramy warstwy 1** , aby wyświetlić listę bram warstwy 1 z liczbą połączonych segmentów. Wybierz segment połączony z Contoso-T01. Zostanie otwarte okno z interfejsem logicznym skonfigurowanym na routerze warstwy-01. Służy on jako brama do maszyny wirtualnej należącej do puli zaplecza połączonej z segmentem.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Zrzut ekranu przedstawiający adres bramy wybranego segmentu.":::    

3. W kliencie vSphere maszyny wirtualnej wybierz maszynę wirtualną, aby wyświetlić jej szczegóły. Zwróć uwagę, że adres IP jest zgodny z informacjami w kroku 3 poprzedniej sekcji: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły maszyny wirtualnej w kliencie VSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Wybierz maszynę wirtualną, a następnie kliknij pozycję **akcje > Edytuj ustawienia** , aby zweryfikować połączenie z segmentem NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

1. Zaloguj się do [Azure Portal](https://rc.portal.azure.com/#home). W obszarze **usługi platformy Azure > sieć** wybierz pozycję **Traffic Manager profile**.

2. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy profil Traffic Manager.
 
3. Podaj nazwę profilu, metodę routingu (zostanie użyta waga w tym scenariuszu), zobacz [Traffic Manager metod routingu](../traffic-manager/traffic-manager-routing-methods.md)), subskrypcja i Grupa zasobów, a następnie wybierz pozycję **Utwórz**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Dodawanie zewnętrznych punktów końcowych do profilu Traffic Manager

1. Wybierz profil Traffic Manager z okienka wyników wyszukiwania, wybierz **punkty końcowe** , a następnie **+ Dodaj**.

2. Wprowadź wymagane szczegóły: typ, nazwa, w pełni kwalifikowana nazwa domeny (FQDN) lub adres IP oraz waga (w tym scenariuszu przypiszemy wagę 1 do każdego punktu końcowego). Wybierz pozycję **Dodaj**. Spowoduje to utworzenie zewnętrznego punktu końcowego. Stan monitora musi być w **trybie online**. Powtórz te same czynności, aby utworzyć dwa dodatkowe zewnętrzne punkty końcowe, jeden w innym regionie i w innym miejscu. Po utworzeniu wszystkie trzy będą wyświetlane w profilu Traffic Manager, a stan wszystkich trzech powinien być w **trybie online**.

3. Wybierz pozycję **Omówienie**. Skopiuj adres URL pod **nazwą DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Zrzut ekranu przedstawiający przegląd Traffic Manager punktu końcowego z wyróżnioną nazwą DNS." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Wklej adres URL nazwy DNS w przeglądarce. Poniższy zrzut ekranu przedstawia ruch kierowany do regionu Europa Zachodnia.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do Europa Zachodnia."::: 

5. Odśwież przeglądarkę. Poniższy zrzut ekranu przedstawia ruch kierowany do innego zestawu elementów członkowskich puli zaplecza w regionie zachodnie stany USA.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do regionu zachodnie stany USA."::: 

6. Odśwież przeglądarkę ponownie. Poniższy zrzut ekranu przedstawia ruch kierowany do ostatecznego zestawu elementów członkowskich puli zaplecza lokalnie.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Zrzut ekranu okna przeglądarki przedstawiający ruch kierowany do lokalnego.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej:

- [Korzystanie z usługi Azure Application Gateway na platformie Azure VMware (Automatyczna synchronizacja)](protect-azure-vmware-solution-with-application-gateway.md)
- [Metody routingu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)
- [Łączenie usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Mierzenie wydajności Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md)

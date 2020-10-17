---
title: Wdrażanie i Konfigurowanie rozwiązania VMware platformy Azure
description: Dowiedz się, jak korzystać z informacji zebranych w fazie planowania, aby wdrożyć chmurę prywatną rozwiązania Azure VMware.
ms.topic: tutorial
ms.author: tredavis
ms.date: 10/02/2020
ms.openlocfilehash: 0839048c2d0ad5944566a48f54cca07a4daeb754
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152032"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Wdrażanie i Konfigurowanie rozwiązania VMware platformy Azure

W tym artykule przedstawiono informacje z [sekcji Planowanie](production-ready-deployment-steps.md) w celu wdrożenia rozwiązania VMware dla platformy Azure. Jeśli informacje nie zostały zdefiniowane, Wróć do [sekcji Planowanie](production-ready-deployment-steps.md) przed kontynuowaniem.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Wdrażanie usługi Azure VMware Solution

Skorzystaj z informacji zebranych w artykule [Planowanie rozwiązania Azure VMware Solution Deployment](production-ready-deployment-steps.md) :

>[!NOTE]
>Aby wdrożyć rozwiązanie VMware dla platformy Azure, musisz mieć co najmniej poziom współautor w subskrypcji.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz temat [rozwiązanie Azure VMware:](https://www.youtube.com/embed/1JLB3L2WDWI) film wideo dotyczący wdrażania. 

## <a name="create-the-jump-box"></a>Tworzenie pola skoku

>[!IMPORTANT]
>Jeśli pozostawiłeś opcję **Virtual Network** pustej w kroku wstępnego inicjowania obsługi administracyjnej na ekranie **Tworzenie chmury prywatnej** , **przed** przejściem do tej sekcji Ukończ samouczek [Konfigurowanie sieci dla chmury prywatnej programu VMware](tutorial-configure-networking.md) .  

Po wdrożeniu rozwiązania Azure VMware można utworzyć pole skoku sieci wirtualnej łączące się z programem vCenter i NSX. Po skonfigurowaniu obwodów usługi ExpressRoute i ExpressRoute Global Reach pole skoku nie jest potrzebne.  Ale warto uzyskać dostęp do programu vCenter i NSX w rozwiązaniu VMware platformy Azure.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Utwórz pole skoku rozwiązania Azure VMware" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Aby utworzyć maszynę wirtualną w sieci wirtualnej, która została [zidentyfikowana lub utworzona w ramach procesu wdrażania](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution), wykonaj następujące instrukcje: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Nawiązywanie połączenia z siecią wirtualną za pomocą ExpressRoute

Jeśli nie zdefiniowano sieci wirtualnej w kroku wdrożenia, a zamierzasz połączyć usługę Azure VMware ExpressRoute z istniejącą bramą ExpressRoute, wykonaj poniższe kroki.

Jeśli sieć wirtualna została już zdefiniowana na ekranie wdrożenia na platformie Azure, przejdź do następnej sekcji.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Weryfikowanie tras sieciowych anonsowanych

Pole skoku znajduje się w sieci wirtualnej, w której rozwiązanie Azure VMware nawiązuje połączenie za pośrednictwem obwodu usługi ExpressRoute.  Na platformie Azure przejdź do interfejsu sieciowego pola skoku i [Sprawdź obowiązujące trasy](../virtual-network/manage-route-table.md#view-effective-routes).

Na liście efektywne trasy powinny zostać wyświetlone sieci utworzone w ramach wdrożenia rozwiązania Azure VMware. Zobaczysz wiele sieci, które pochodzą z [ `/22` sieci zdefiniowanej](production-ready-deployment-steps.md#ip-address-segment) w [kroku wdrożenia](#deploy-azure-vmware-solution) wcześniej w tym artykule.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Utwórz pole skoku rozwiązania Azure VMware" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

W tym przykładzie sieć 10.74.72.0/22 została wprowadzona podczas wdrażania w sieci/24.  Jeśli zobaczysz coś podobnego, możesz połączyć się z programem vCenter w rozwiązaniu VMware platformy Azure.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Nawiązywanie połączenia i logowanie do programu vCenter i NSX-T

Zaloguj się do pola skoku utworzonego w poprzednim kroku. Po zalogowaniu Otwórz przeglądarkę internetową i przejdź do i zaloguj się do konsoli administracyjnej programu vCenter i NSX-T.  

W Azure Portal można zidentyfikować adresy IP i poświadczenia konsoli administracyjnej programu vCenter oraz NSX-T.  Wybierz chmurę prywatną, a następnie w widoku **Przegląd** wybierz pozycję **tożsamość > domyślna**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Tworzenie segmentu sieci w rozwiązaniu VMware platformy Azure

NSX-T służy do tworzenia nowych segmentów sieci w środowisku rozwiązań VMware platformy Azure.  Zdefiniowano sieci, które chcesz utworzyć w [sekcji Planowanie](production-ready-deployment-steps.md).  Jeśli nie zostały one zdefiniowane, Wróć do [sekcji Planowanie](production-ready-deployment-steps.md) przed kontynuowaniem.

>[!IMPORTANT]
>Upewnij się, że zdefiniowany blok adresów sieciowych CIDR nie nakłada się na wszystkie elementy w środowiskach platformy Azure i lokalnych.  

Postępuj zgodnie z samouczkiem [Tworzenie segmentu sieci NSX-t w rozwiązaniu Azure VMware](tutorial-nsx-t-network-segment.md) , aby utworzyć segment sieci NSX-t w rozwiązaniu VMware platformy Azure.

## <a name="verify-advertised-nsx-t-segment"></a>Weryfikuj anonsowany segment NSX-T

Wróć do kroku [Weryfikuj trasy sieciowe anonsowane](#verify-network-routes-advertised) . Zostaną wyświetlone dodatkowe trasy na liście reprezentującej segmenty sieci utworzone w poprzednim kroku.  

W przypadku maszyn wirtualnych należy przypisać segmenty, które zostały utworzone w kroku [Tworzenie segmentu sieci na platformie Azure VMware](#create-a-network-segment-on-azure-vmware-solution) .  

Ponieważ jest wymagany system DNS, Zidentyfikuj serwer DNS, którego chcesz użyć.  

- Jeśli masz ExpressRoute Global Reach skonfigurowany, użyj dowolnego serwera DNS, który będzie używany lokalnie.  
- Jeśli masz serwer DNS na platformie Azure, użyj tego programu.  
- Jeśli nie masz żadnego z nich, użyj dowolnego serwera DNS używanego przez to pole skoku.

>[!NOTE]
>Ten krok polega na zidentyfikowaniu serwera DNS, a w tym kroku nie są wykonywane żadne konfiguracje.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Obowiązkowe Podaj usługi DHCP dla segmentu sieci NSX-T

Jeśli planujesz używać protokołu DHCP w segmentach NSX-T, przejdź do tej sekcji. W przeciwnym razie przejdź do sekcji [Dodawanie maszyny wirtualnej w segmencie sieci NSX-T](#add-a-vm-on-the-nsx-t-network-segment) .  

Po utworzeniu segmentu sieci NSX-T można wykonać jedną z następujących czynności:

* Użyj NSX-T jako serwera DHCP dla utworzonych segmentów. W przypadku tej opcji należy [utworzyć serwer DHCP w NSX-T](manage-dhcp.md#create-dhcp-server) i [przekazać go do tego serwera](manage-dhcp.md#create-dhcp-relay-service).
* Przekazuj żądania DHCP z segmentów NSX-T do serwera DHCP w innym miejscu w danym środowisku. W przypadku tej opcji [tylko Konfiguracja przekaźnika](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Dodawanie maszyny wirtualnej w segmencie sieci NSX-T

W programie Azure VMware Solution vCenter Wdróż maszynę wirtualną i użyj jej do sprawdzenia łączności z sieci rozwiązań VMware platformy Azure do:

- Internet
- Sieci wirtualne platformy Azure
- Lokalnie.  

Wdróż maszynę wirtualną w taki sam sposób, jak w dowolnym środowisku vSphere.  Dołącz maszynę wirtualną do jednego z segmentów sieci utworzonych wcześniej w NSX-T.  

>[!NOTE]
>W przypadku skonfigurowania serwera DHCP należy uzyskać konfigurację sieci dla maszyny wirtualnej (nie zapomnij skonfigurować zakresu).  Jeśli zamierzasz skonfigurować statycznie, skonfiguruj ją w zwykły sposób.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testowanie łączności segmentu NSX-T

Zaloguj się do maszyny wirtualnej utworzonej w poprzednim kroku i sprawdź łączność;

1. Wyślij polecenie ping do adresu IP w Internecie.
2. Przejdź do witryny internetowej za pośrednictwem przeglądarki sieci Web.
3. Wyślij polecenie ping do pola skoku znajdującego się na Virtual Network platformy Azure.

>[!IMPORTANT]
>Na tym etapie rozwiązanie Azure VMware działa i zostało pomyślnie ustanowione połączenie z usługą i z usługi Azure Virtual Network oraz z Internetu.

## <a name="next-steps"></a>Następne kroki

W następnej sekcji nastąpi połączenie rozwiązania Azure VMware z siecią lokalną za pośrednictwem usługi ExpressRoute.
> [!div class="nextstepaction"]
> [Łączenie rozwiązania VMware z platformą Azure z środowiskiem lokalnym](azure-vmware-solution-on-premises.md)

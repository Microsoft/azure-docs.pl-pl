---
title: Wdrażanie i Konfigurowanie rozwiązania VMware platformy Azure
description: Dowiedz się, jak korzystać z informacji zebranych w fazie planowania, aby wdrożyć i skonfigurować chmurę prywatną rozwiązania Azure VMware.
ms.topic: tutorial
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 6f1e587894d51eae2af4e9f76114f53f27ffefe1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703866"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Wdrażanie i Konfigurowanie rozwiązania VMware platformy Azure

W tym artykule opisano sposób wdrażania i konfigurowania rozwiązania Azure VMware przy użyciu informacji z [sekcji Planowanie](production-ready-deployment-steps.md) . 

>[!IMPORTANT]
>Jeśli nie zdefiniowano jeszcze informacji, Wróć do [sekcji Planowanie](production-ready-deployment-steps.md) przed kontynuowaniem.


## <a name="create-an-azure-vmware-solution-private-cloud"></a>Utwórz chmurę prywatną rozwiązania Azure VMware

Postępuj zgodnie z wymaganiami wstępnymi i krokami opisanymi w samouczku [Tworzenie chmury prywatnej rozwiązania Azure VMware](tutorial-create-private-cloud.md) . Chmurę prywatną rozwiązania Azure VMware można utworzyć za pomocą [Azure Portal](tutorial-create-private-cloud.md#azure-portal) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-create-private-cloud.md#azure-cli).  

>[!NOTE]
>Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz temat [rozwiązanie Azure VMware:](https://www.youtube.com/embed/gng7JjxgayI) film wideo dotyczący wdrażania.

## <a name="create-the-jump-box"></a>Tworzenie pola skoku

>[!IMPORTANT]
>Jeśli pozostawiłeś opcję **Virtual Network** pustej w kroku wstępnego inicjowania obsługi administracyjnej na ekranie **Tworzenie chmury prywatnej** , **przed** przejściem do tej sekcji Ukończ samouczek [Konfigurowanie sieci dla chmury prywatnej programu VMware](tutorial-configure-networking.md) .  

Po wdrożeniu rozwiązania VMware dla platformy Azure utworzysz pole skoku sieci wirtualnej łączące się z programem vCenter i NSX. Po skonfigurowaniu obwodów usługi ExpressRoute i ExpressRoute Global Reach pole skoku nie jest potrzebne.  Ale warto uzyskać dostęp do programu vCenter i NSX w rozwiązaniu VMware platformy Azure.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Utwórz pole skoku rozwiązania Azure VMware" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Aby utworzyć maszynę wirtualną w sieci wirtualnej, która została [zidentyfikowana lub utworzona w ramach procesu wdrażania](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution), wykonaj następujące instrukcje: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Nawiązywanie połączenia z siecią wirtualną za pomocą ExpressRoute

>[!IMPORTANT]
>Jeśli sieć wirtualna została już zdefiniowana na ekranie wdrożenia na platformie Azure, przejdź do następnej sekcji.

Jeśli nie zdefiniowano sieci wirtualnej w kroku wdrożenia, a zamierzasz połączyć usługę Azure VMware ExpressRoute z istniejącą bramą ExpressRoute, wykonaj następujące kroki.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Weryfikowanie tras sieciowych anonsowanych

Pole skoku znajduje się w sieci wirtualnej, w której rozwiązanie Azure VMware nawiązuje połączenie za pomocą obwodu usługi ExpressRoute.  Na platformie Azure przejdź do interfejsu sieciowego pola skoku i [Sprawdź obowiązujące trasy](../virtual-network/manage-route-table.md#view-effective-routes).

Na liście efektywne trasy powinny zostać wyświetlone sieci utworzone w ramach wdrożenia rozwiązania Azure VMware. Zobaczysz wiele sieci, które pochodzą z [ `/22` sieci zdefiniowanej](production-ready-deployment-steps.md#ip-address-segment) podczas [tworzenia chmury prywatnej](#create-an-azure-vmware-solution-private-cloud).  

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Sprawdź trasy sieciowe anonsowane z rozwiązania Azure VMware do platformy Azure Virtual Network" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

W tym przykładzie sieć 10.74.72.0/22 została wprowadzona podczas wdrażania w sieci/24.  Jeśli zobaczysz coś podobnego, możesz połączyć się z programem vCenter w rozwiązaniu VMware platformy Azure.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Nawiązywanie połączenia i logowanie do programu vCenter i NSX-T

Zaloguj się do pola skoku utworzonego w poprzednim kroku. Po zalogowaniu Otwórz przeglądarkę internetową i przejdź do i zaloguj się do Menedżera vCenter i NSX-T.  

W Azure Portal można zidentyfikować adresy IP i poświadczenia konsoli Menedżera vCenter oraz NSX-T.  Wybierz chmurę prywatną, a następnie **Zarządzaj**  >  **tożsamością**.

>[!TIP]
>Wybierz pozycję **Generuj nowe hasło** , aby wygenerować nowe hasła VCENTER i NSX-T.

:::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::



## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Tworzenie segmentu sieci w rozwiązaniu VMware platformy Azure

Menedżer NSX-T służy do tworzenia nowych segmentów sieci w środowisku rozwiązań VMware platformy Azure.  W [sekcji Planowanie](production-ready-deployment-steps.md)zostały zdefiniowane sieci, które chcesz utworzyć.  Jeśli nie zostały one zdefiniowane, Wróć do [sekcji Planowanie](production-ready-deployment-steps.md) przed kontynuowaniem.

>[!IMPORTANT]
>Upewnij się, że zdefiniowany blok adresów sieciowych CIDR nie nakłada się na wszystkie elementy w środowiskach platformy Azure i lokalnych.  

Postępuj zgodnie z samouczkiem [Tworzenie segmentu sieci NSX-t w rozwiązaniu Azure VMware](tutorial-nsx-t-network-segment.md) , aby utworzyć segment sieci NSX-t w rozwiązaniu VMware platformy Azure.

## <a name="verify-advertised-nsx-t-segment"></a>Weryfikuj anonsowany segment NSX-T

Wróć do kroku [Weryfikuj trasy sieciowe anonsowane](#verify-network-routes-advertised) . Na liście będą widoczne inne trasy reprezentujące segmenty sieci utworzone w poprzednim kroku.  

W przypadku maszyn wirtualnych należy przypisać segmenty utworzone w kroku [Tworzenie segmentu sieci na platformie Azure VMware](#create-a-network-segment-on-azure-vmware-solution) .  

Ponieważ jest wymagany system DNS, Zidentyfikuj serwer DNS, którego chcesz użyć.  

- Jeśli masz ExpressRoute Global Reach skonfigurowany, użyj dowolnego serwera DNS, który będzie używany lokalnie.  
- Jeśli masz serwer DNS na platformie Azure, użyj tego programu.  
- Jeśli nie masz żadnego z nich, użyj dowolnego serwera DNS używanego przez to pole skoku.

>[!NOTE]
>Ten krok polega na zidentyfikowaniu serwera DNS, a w tym kroku nie są wykonywane żadne konfiguracje.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Obowiązkowe Podaj usługi DHCP dla segmentu sieci NSX-T

Jeśli planujesz używać protokołu DHCP w segmentach NSX-T, przejdź do tej sekcji. W przeciwnym razie przejdź do sekcji [Dodawanie maszyny wirtualnej w segmencie sieci NSX-T](#add-a-vm-on-the-nsx-t-network-segment) .  

Po utworzeniu segmentu sieci NSX-T można utworzyć rozwiązanie DHCP i zarządzać nim na platformie Azure VMware na dwa sposoby:

* Jeśli używasz NSX-T do hostowania serwera DHCP, musisz [utworzyć serwer DHCP](manage-dhcp.md#create-a-dhcp-server) i [przekazać go do tego serwera](manage-dhcp.md#create-dhcp-relay-service). 
* Jeśli używasz zewnętrznego serwera DHCP innej firmy w sieci, musisz [utworzyć usługę przekaźnika DHCP](manage-dhcp.md#create-dhcp-relay-service).  W przypadku tej opcji [tylko Konfiguracja przekaźnika](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Dodawanie maszyny wirtualnej w segmencie sieci NSX-T

W programie Azure VMware Solution vCenter Wdróż maszynę wirtualną i użyj jej do sprawdzenia łączności z sieci rozwiązań VMware platformy Azure, aby:

- Internet
- Sieci wirtualne platformy Azure
- Lokalnie.  

Wdróż maszynę wirtualną w taki sam sposób, jak w dowolnym środowisku vSphere.  Dołącz maszynę wirtualną do jednego z segmentów sieci utworzonych wcześniej w NSX-T.  

>[!NOTE]
>W przypadku skonfigurowania serwera DHCP należy uzyskać konfigurację sieci dla maszyny wirtualnej (nie zapomnij skonfigurować zakresu).  Jeśli zamierzasz skonfigurować statycznie, skonfiguruj ją w zwykły sposób.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testowanie łączności segmentu NSX-T

Zaloguj się do maszyny wirtualnej utworzonej w poprzednim kroku i sprawdź łączność;

1. Wyślij polecenie ping do adresu IP w Internecie.
2. W przeglądarce sieci Web przejdź do witryny internetowej.
3. Wyślij polecenie ping do pola skoku znajdującego się na Virtual Network platformy Azure.

Rozwiązanie VMware platformy Azure jest teraz w trakcie pracy i zostało pomyślnie ustanowione połączenie z usługą i z usługi Azure Virtual Network oraz z Internetu.

## <a name="next-steps"></a>Następne kroki

W następnej sekcji zostanie nawiązane połączenie programu Azure VMware z siecią lokalną za pomocą ExpressRoute.
> [!div class="nextstepaction"]
> [Łączenie rozwiązania VMware z platformą Azure z środowiskiem lokalnym](azure-vmware-solution-on-premises.md)

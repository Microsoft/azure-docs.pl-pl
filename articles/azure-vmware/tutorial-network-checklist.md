---
title: Samouczek — Lista kontrolna planowania sieci
description: Więcej informacji na temat wymagań wstępnych dotyczących sieci i szczegółów dotyczących łączności sieciowej i portów sieciowych dla rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5538f9c5d6543ca312835f4ef6437e413dea231b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576681"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Lista kontrolna planowania sieci dla rozwiązań VMware platformy Azure 

Rozwiązanie VMware dla platformy Azure oferuje środowisko chmury prywatnej VMware, które jest dostępne dla użytkowników i aplikacji z lokalnych środowisk lub zasobów opartych na platformie Azure. Łączność jest dostarczana za pośrednictwem usług sieciowych, takich jak Azure ExpressRoute i połączenia sieci VPN, i wymaga określonych zakresów adresów sieciowych i portów zapory na potrzeby włączania usług. Ten artykuł zawiera informacje potrzebne do poprawnego skonfigurowania sieci do pracy z rozwiązaniem VMware platformy Azure.

Niniejszy samouczek zawiera informacje na temat następujących zagadnień:

> [!div class="checklist"]
> * Zagadnienia dotyczące obwodów sieci wirtualnych i ExpressRoute
> * Wymagania dotyczące routingu i podsieci
> * Wymagane porty sieciowe dla programu w celu komunikowania się z usługami
> * Zagadnienia dotyczące protokołu DHCP i usługi DNS w rozwiązaniu VMware platformy Azure

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Zagadnienia dotyczące obwodów sieci wirtualnych i ExpressRoute
Po utworzeniu połączenia sieci wirtualnej w ramach subskrypcji obwód usługi ExpressRoute jest ustanawiany za pośrednictwem komunikacji równorzędnej, korzysta z klucza autoryzacji i identyfikatora komunikacji równorzędnej, który jest żądany w Azure Portal. Komunikacja równorzędna to połączenie prywatne, jedno-do-jednego między chmurą prywatną i siecią wirtualną.

> [!NOTE] 
> Obwód usługi ExpressRoute nie jest częścią wdrożenia chmury prywatnej. Lokalny obwód ExpressRoute wykracza poza zakres tego dokumentu. Jeśli potrzebujesz łączności lokalnej z chmurą prywatną, możesz użyć jednego z istniejących obwodów usługi ExpressRoute lub kupić jeden z nich w Azure Portal.

Podczas wdrażania chmury prywatnej otrzymujesz adresy IP dla Menedżera vCenter i NSX-T. Aby uzyskać dostęp do tych interfejsów zarządzania, należy utworzyć dodatkowe zasoby w sieci wirtualnej subskrypcji. Procedury tworzenia tych zasobów i ustanawiania [prywatnej komunikacji równorzędnej ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) można znaleźć w samouczkach.

Sieci logiczne w chmurze prywatnej są dostarczane ze wstępnie zainicjowaną NSX-T. Bramy warstwy 0 i bramy warstwy 1 są wstępnie udostępniane. Można utworzyć segment i dołączyć go do istniejącej bramy warstwy 1 lub dołączyć do nowej zdefiniowanej bramy warstwy 1. Składniki sieci logicznej NSX-T zapewniają East-West łączności między obciążeniami i zapewniają North-South łączność z Internetem i usługami platformy Azure.

## <a name="routing-and-subnet-considerations"></a>Zagadnienia dotyczące routingu i podsieci
Chmura prywatna automatycznej synchronizacji jest połączona z siecią wirtualną platformy Azure przy użyciu połączenia usługi Azure ExpressRoute. Ta wysoka przepustowość, połączenie o małym opóźnieniu pozwala uzyskiwać dostęp do usług uruchomionych w ramach subskrypcji platformy Azure w środowisku chmury prywatnej. Routing jest oparty na Border Gateway Protocol (BGP), automatycznie inicjowany i domyślnie włączony dla każdego wdrożenia chmury prywatnej. 

Automatyczna synchronizacja chmur prywatnych wymaga co najmniej `/22` bloku adresów sieciowych CIDR dla podsieci, jak pokazano poniżej. Ta sieć uzupełnia sieci lokalne. Blok adresów nie powinien nakładać się na bloki adresów używane w innych sieciach wirtualnych w ramach subskrypcji i sieci lokalnych. W ramach tego bloku adresów, zarządzania, aprowizacji i sieci vMotion są automatycznie obsługiwane.

Przykładowy `/22` blok adresów sieciowych CIDR:  `10.10.0.0/22`

Podsieci:

| Użycie sieci             | Podsieć | Przykład          |
| ------------------------- | ------ | ---------------- |
| Zarządzanie chmurą prywatną  | `/26`  | `10.10.0.0/26`   |
| Sieć vMotion           | `/25`  | `10.10.1.128/25` |
| Obciążenia maszyn wirtualnych              | `/24`  | `10.10.2.0/24`   |
| Komunikacja równorzędna ExpressRoute      | `/29`  | `10.10.3.8/29`   |


## <a name="required-network-ports"></a>Wymagane porty sieciowe

| Element źródłowy | Element docelowy | Protokół | Port | Opis  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Serwer DNS w chmurze prywatnej | Lokalny serwer DNS | UDP | 53 | Klient DNS — żądania przesyłane dalej z komputera vCenter do wszelkich lokalnych zapytań DNS (Sprawdź poniższą sekcję DNS) |  
| Lokalny serwer DNS   | Serwer DNS w chmurze prywatnej | UDP | 53 | Klient DNS — żądania przesyłane dalej z usług lokalnych do serwerów DNS w chmurze prywatnej (Sprawdź poniższą sekcję DNS) |  
| Sieć lokalna  | Prywatny serwer vCenter w chmurze  | TCP (HTTP)  | 80 | vCenter Server wymaga portu 80 dla bezpośrednich połączeń HTTP.Port 80 przekierowuje żądania do portu HTTPS 443. To przekierowanie jest pomocne, jeśli używasz  `http://server`   zamiast  `https://server` .  <br><br>WS-Management (wymagane jest również, aby port 443 był otwarty) <br><br>Jeśli używasz niestandardowej bazy danych Microsoft SQL, a nie bazy danych SQL Server 2008 na vCenter Server, port 80 jest używany przez usługi SQL Reporting. Po zainstalowaniu vCenter Server Instalator będzie monitował o zmianę portu HTTP dla vCenter Server. Aby zapewnić pomyślną instalację, zmień port HTTP vCenter Server na wartość niestandardową.Program Microsoft Internet Information Services (IIS) również używa portu 80. Zobacz konflikt między vCenter Server i IIS dla portu 80. |  
| Sieć zarządzania chmurą prywatną | Lokalna usługa Active Directory  | TCP  | 389 | Ten port musi być otwarty na lokalnym i wszystkich zdalnych wystąpieniach vCenter Server. Ten port jest numerem portu LDAP dla usług katalogowych dla grupy vCenter Server. System vCenter Server należy powiązać z portem 389, nawet jeśli to wystąpienie vCenter Server nie jest przyłączane do grupy trybu połączonego. Jeśli na tym porcie jest uruchomiona inna usługa, może być zalecane jej usunięcie lub zmianę portu na inny port. Usługę LDAP można uruchomić na dowolnym porcie z prze1025 do 65535.Jeśli to wystąpienie pełni rolę Active Directory Microsoft Windows, Zmień numer portu z 389 na dostępny port z 1025 do 65535. Ten port jest opcjonalny — w celu skonfigurowania lokalnej usługi AD jako źródła tożsamości w programie vCenter w chmurze prywatnej. |  
| Sieć lokalna  | Prywatny serwer vCenter w chmurze  | TCP (HTTPS)  | 443 | Ten port umożliwia dostęp do programu vCenter z sieci lokalnej.Domyślny port wykorzystywany przez system vCenter Server do nasłuchiwania połączeń z klienta vSphere. Aby umożliwić systemowi vCenter Server odbierania danych z klienta vSphere, otwórz port 443 w zaporze. System vCenter Server używa również portu 443 do monitorowania transferu danych z klientów SDK.Ten port jest również używany dla następujących usług: WS-Management (wymaga to również, aby port 80 był otwarty). vSphere dostęp klienta do programu vSphere Update Manager. Połączenia klienta zarządzania siecią innej firmy z usługą vCenter Server. Klienci zarządzania siecią innych firm uzyskują dostęp do hostów. |  
| Przeglądarka internetowa  | Hybrydowy Menedżer chmury  | TCP (HTTPS) | 9443 | Interfejs zarządzania urządzeniami wirtualnymi w chmurze hybrydowej dla konfiguracji systemu hybrydowego programu Cloud Manager. |
| Sieć administracyjna  | Hybrydowy Menedżer chmury | Protokół SSH | 22 | Dostęp SSH do administratora do hybrydowego Menedżera chmurowego. |
| HCM | Brama chmury | TCP (HTTPS) | 8123 | Wyślij instrukcje usługi replikacji opartej na hoście do bramy chmury hybrydowej. |
| HCM | Brama chmury | HTTP TCP (HTTPS) | 9443 | Wyślij instrukcje dotyczące zarządzania do lokalnej bramy chmury hybrydowej przy użyciu interfejsu API REST. |
| Brama chmury | L2C | TCP (HTTPS) | 443 | Wyślij instrukcje dotyczące zarządzania z bramy Cloud Gateway do L2C, gdy L2C używa tej samej ścieżki co Brama chmury hybrydowej. |
| Brama chmury | Hosty ESXi | TCP | 80 902 | Zarządzanie i wdrażanie OVF. |
| Brama w chmurze (lokalna)| Brama chmury (zdalna) | UDP | 4500 | Wymagane w przypadku protokołu IPSEC<br>   Internet Key Exchange (IKEv2) do hermetyzacji obciążeń dla tunelu dwukierunkowego. Obsługiwane są również adresy sieciowe Translation-Traversal (NAT-T). |
| Brama w chmurze (lokalna) | Brama chmury (zdalna)  | UDP | 500 | Wymagane w przypadku protokołu IPSEC<br> Internet Key Exchange (ISAKMP) dla tunelu dwukierunkowego. |
| Lokalna sieć vCenter | Sieć zarządzania chmurą prywatną | TCP | 8000 |  vMotion maszyn wirtualnych z lokalnego programu vCenter do chmury prywatnej — vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Zagadnienia dotyczące rozpoznawania nazw DHCP i DNS
Aplikacje i obciążenia działające w środowisku chmury prywatnej wymagają rozpoznawania nazw i usług DHCP w celu wyszukania i przypisywania adresów IP. Aby zapewnić te usługi, wymagane są odpowiednie infrastruktury DHCP i DNS. Można skonfigurować maszynę wirtualną, aby udostępnić te usługi w środowisku chmury prywatnej.  

Użyj wbudowanej usługi DHCP do NSX lub Użyj lokalnego serwera DHCP w chmurze prywatnej zamiast routingu ruch DHCP z powrotem w sieci WAN do lokalnego.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia i wymagania dotyczące wdrażania chmury prywatnej rozwiązania Azure VMware. 


Po umieszczeniu odpowiedniej sieci na miejscu przejdź do następnego samouczka, aby utworzyć chmurę prywatną rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Utwórz chmurę prywatną rozwiązania Azure VMware](tutorial-create-private-cloud.md)

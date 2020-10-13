---
title: Koncepcja — Integruj wdrożenie rozwiązania Azure VMware z architekturą Hub i szprychy
description: Zapoznaj się z zaleceniami dotyczącymi integrowania wdrożenia rozwiązania Azure VMware w istniejącej lub nowej architekturze Hub i szprych na platformie Azure.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: bfd0da4f03eedaf215ddb55facffc2296a9d0b85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580282"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrowanie rozwiązań VMware platformy Azure z architekturą Hub i szprychy

W tym artykule udostępniamy zalecenia dotyczące integrowania wdrożenia rozwiązania Azure VMware w istniejącej lub nowej [architekturze Hub i szprych](/azure/architecture/reference-architectures/hybrid-networking/shared-services) na platformie Azure. 

Scenariusz Hub i szprych zakłada środowisko chmury hybrydowej z obciążeniami w:

* Natywne platformy Azure korzystające z usług IaaS lub PaaS
* Rozwiązanie Azure VMware 
* vSphere lokalnie

## <a name="architecture"></a>Architektura

*Centrum* to Virtual Network platformy Azure, które działa jako centralne punkty łączności z chmurą prywatną i rozwiązaniem VMware platformy Azure. *Szprychy* są sieciami wirtualnymi równorzędnymi z koncentratorem, aby umożliwić komunikację między sieciami wirtualnymi.

Ruch między lokalnym centrum danych i chmurą prywatną rozwiązania VMware platformy Azure oraz centrum odbywa się za pomocą połączeń usługi Azure ExpressRoute. Sieci wirtualne szprych zazwyczaj zawierają obciążenia oparte na IaaS, ale mogą mieć usługi PaaS, takie jak [App Service Environment](../app-service/environment/intro.md), które mają bezpośrednią integrację z usługą Virtual Network lub innymi usługami PaaS z włączonym [łączem prywatnym platformy Azure](../private-link/index.yml) .

Na diagramie przedstawiono przykład wdrożenia centrum i szprych na platformie Azure połączonych z lokalnym i rozwiązaniem VMware platformy Azure za pomocą ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false":::

Architektura ma następujące główne składniki:

-   **Lokacja lokalna:** Lokalne centra danych klienta połączone z platformą Azure za pomocą połączenia ExpressRoute.

-   **Chmura prywatna rozwiązania Azure VMware:** Rozwiązanie VMware platformy Azure SDDC utworzone przez co najmniej jeden klaster vSphere, każdy z nich z maksymalnie 16 węzłami.

-   **ExpressRoute Gateway:** Umożliwia komunikację między chmurą prywatną rozwiązań VMware platformy Azure, usługami udostępnionymi w sieci wirtualnej Hub i obciążeniami działającymi w sieciach wirtualnych szprych.

-   **ExpressRoute Global REACH:** Umożliwia łączność między środowiskiem lokalnym i chmurą prywatną rozwiązania VMware platformy Azure.


  > [!NOTE]
  > **Zagadnienia dotyczące sieci VPN S2S:** W przypadku wdrożeń produkcyjnych rozwiązania VMware platformy Azure usługa Azure S2S VPN nie jest obsługiwana z powodu wymagań sieci dla programu VMware HCX. Można go jednak użyć do wdrożenia koncepcji ZK.


-   **Sieć wirtualna centrum:** Działa jako centralny punkt łączności z siecią lokalną i chmurą prywatną rozwiązania VMware platformy Azure.

-   **Sieć wirtualna szprych**

    -   **IaaS szprych:** IaaS szpryche obsługuje obciążenia oparte na usłudze Azure IaaS, w tym zestawy dostępności maszyn wirtualnych i zestawy skalowania maszyn sieciowych oraz odpowiednie składniki sieciowe.

    -   **PaaS szprych:** PaaS szprychy udostępnia usługi Azure PaaS Services przy użyciu adresów prywatnych, łącząc je z [prywatnym punktem końcowym](../private-link/private-endpoint-overview.md) i [prywatnym](../private-link/private-link-overview.md).

-   **Zapora platformy Azure:** Działa jako centralny element do segmentacji ruchu między szprychami i rozwiązaniem VMware platformy Azure.

-   **Application Gateway:** Udostępnia i chroni aplikacje sieci Web, które są uruchamiane na maszynach wirtualnych Azure IaaS/PaaS lub Azure VMware Solution Machines. Integruje się z innymi usługami, takimi jak API Management.

## <a name="network-and-security-considerations"></a>Zagadnienia dotyczące sieci i zabezpieczeń

Połączenia ExpressRoute umożliwiają przepływ ruchu między środowiskiem lokalnym, rozwiązaniem VMware platformy Azure i siecią szkieletową platformy Azure. W celu zaimplementowania tej łączności rozwiązanie VMware platformy Azure używa [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) .

Ponieważ Brama ExpressRoute nie zapewnia przechodniej routingu między podłączonymi do nich obwodami, łączność lokalna musi również używać ExpressRoute Global Reach do komunikacji między środowiskiem lokalnym vSphere i rozwiązaniem VMware platformy Azure. 

* **Przepływ ruchu rozwiązań w środowisku lokalnym do platformy Azure VMware**

  :::image type="content" source="media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false":::


* **Rozwiązanie VMware platformy Azure do centralnego przepływu ruchu w sieci wirtualnej**

  :::image type="content" source="media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false":::


Więcej informacji na temat rozwiązań dotyczących sieci i łączności VMware firmy Azure można znaleźć w [dokumentacji produktu rozwiązania VMware platformy Azure](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentacja ruchu

[Zapora systemu Azure](../firewall/index.yml) jest centralną częścią topologii gwiazdy, wdrożoną w centralnej sieci wirtualnej. Użyj zapory platformy Azure lub innego sieciowego urządzenia wirtualnego platformy Azure do ustanowienia reguł ruchu i segmentacji komunikacji między różnymi szprychami i obciążeniami rozwiązań VMware platformy Azure.

Utwórz tabele tras, aby skierować ruch do zapory platformy Azure.  W przypadku sieci wirtualnych szprych Utwórz trasę, która ustawia domyślną trasę do wewnętrznego interfejsu zapory platformy Azure, w ten sposób, gdy obciążenie w Virtual Network musi dotrzeć do przestrzeni adresowej rozwiązania VMware platformy Azure, a Zapora może ją oszacować i zastosować odpowiednią regułę ruchu, aby zezwolić na dostęp lub go zabronić.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Trasa z prefiksem adresu 0.0.0.0/0 w ustawieniu **GatewaySubnet** nie jest obsługiwana.

Ustaw trasy dla określonych sieci w odpowiedniej tabeli tras. Na przykład trasy docierające do usługi Azure VMware Solution Management i obciążenia prefiksy adresów IP z obciążeń szprych i na odwrót.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Drugi poziom segmentacji ruchu przy użyciu sieciowych grup zabezpieczeń w szprychach i w centrum do tworzenia bardziej szczegółowych zasad ruchu sieciowego.

> [!NOTE]
> **Ruch z rozwiązań lokalnych do platformy Azure VMware:** Ruch między obciążeniami lokalnymi, vSpheremi lub innymi, są włączane przez Global Reach, ale ruch nie przechodzi przez zaporę platformy Azure w centrum. W tym scenariuszu należy zaimplementować mechanizmy segmentacji ruchu lokalnego lub w rozwiązaniu VMware platformy Azure.

### <a name="application-gateway"></a>Application Gateway

Platformy Azure Application Gateway V1 i v2 zostały przetestowane przy użyciu aplikacji sieci Web, które działają na maszynach wirtualnych rozwiązań VMware platformy Azure jako Pula zaplecza. Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web działających na maszynach wirtualnych rozwiązań VMware platformy Azure do Internetu. Może również bezpiecznie udostępnić aplikacje użytkownikom wewnętrznym.

Zapoznaj się z artykułem dotyczącym rozwiązań VMware platformy Azure w [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) , aby uzyskać szczegółowe informacje i wymagania.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Serwera przesiadkowego i Azure bastionu

Dostęp do środowiska rozwiązań VMware platformy Azure z programem serwera przesiadkowego, który jest maszyną wirtualną z systemem Windows 10 lub Windows Server wdrożoną w podsieci usługi udostępnionej w sieci wirtualnej centrum.

Najlepszym rozwiązaniem w zakresie zabezpieczeń jest wdrożenie [Microsoft Azure bastionu](../bastion/index.yml) w ramach sieci wirtualnej centrum. Usługa Azure bastionu zapewnia bezproblemowe dostęp protokołu RDP i SSH do maszyn wirtualnych wdrożonych na platformie Azure bez konieczności aprowizacji publicznych adresów IP do tych zasobów. Po zainicjowaniu obsługi administracyjnej usługi Azure bastionu można uzyskać dostęp do wybranej maszyny wirtualnej z Azure Portal. Po nawiązaniu połączenia zostanie otwarta nowa karta, na której jest wyświetlany pulpit serwera przesiadkowego oraz z poziomu tego pulpitu, można uzyskać dostęp do płaszczyzny zarządzania chmurą prywatną rozwiązania VMware platformy Azure.

> [!IMPORTANT]
> Nie należy przydzielić publicznego adresu IP do maszyny wirtualnej serwera przesiadkowego lub uwidocznić Port 3389/TCP w publicznym Internecie. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Zagadnienia dotyczące rozwiązywania problemów Azure DNS

Aby uzyskać Azure DNS rozwiązanie, dostępne są dwie opcje:

-   Użyj kontrolerów domeny Azure Active Directory (Azure AD) wdrożonych w centrum (opisanego w temacie [uwagi dotyczące tożsamości](#identity-considerations)) jako serwerów nazw.

-   Wdróż i Skonfiguruj strefę prywatną Azure DNS.

Najlepszym podejściem jest łączenie obu rozwiązań w celu zapewnienia niezawodnego rozpoznawania nazw na potrzeby rozwiązania VMware dla platformy Azure, lokalnego i platformy Azure.

Zgodnie z ogólnym zaleceniem projektowym należy używać istniejącej infrastruktury Azure DNS (w tym przypadku Active Directory zintegrowanej DNS) wdrożonej na co najmniej dwóch maszynach wirtualnych platformy Azure wdrożonych w sieci wirtualnej Hub i skonfigurowanych w sieciach wirtualnych szprych do używania tych serwerów Azure DNS w ustawieniach DNS.

Nadal można używać platformy Azure Prywatna strefa DNS, w której strefa Prywatna strefa DNS platformy Azure jest połączona z sieciami wirtualnymi, a serwery DNS są używane jako resolvery hybrydowe z warunkowym przekazywaniem do rozwiązania do lokalnego/platformy Azure VMware działającego w systemie DNS i infrastruktury Prywatna strefa DNS klienta platformy Azure.

Istnieje kilka zagadnień, które należy wziąć pod uwagę w przypadku Azure DNS stref prywatnych:

* Należy włączyć autorejestrację, aby Azure DNS automatycznie zarządzać cyklem życia rekordów DNS dla maszyn wirtualnych wdrożonych w sieciach wirtualnych szprych.
* Maksymalna liczba prywatnych stref DNS, z którymi może być połączona sieć wirtualna, jest tylko jedna.
* Maksymalna liczba prywatnych stref DNS, z którymi można połączyć sieć wirtualną, to 1000 bez włączonej autorejestracji.

Serwery rozwiązań lokalnych i platformy Azure VMware można skonfigurować za pomocą usług przesyłania dalej warunkowego do maszyn wirtualnych programu rozpoznawania nazw na platformie Azure dla strefy Prywatna strefa DNS platformy Azure.

## <a name="identity-considerations"></a>Zagadnienia dotyczące tożsamości

Najlepszym rozwiązaniem jest wdrożenie co najmniej jednego kontrolera domeny usługi Active Directory w centrum przy użyciu podsieci usługi udostępnionej, idealnie do dwóch z nich w sposób dystrybuowany w ramach stref lub zestawu dostępności maszyn wirtualnych. Zobacz [centrum architektury platformy Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) , aby rozszerzyć lokalną domenę usługi AD na platformę Azure.

Ponadto Wdróż inny kontroler domeny na stronie rozwiązania VMware platformy Azure, aby działać jako tożsamość i źródło DNS w środowisku vSphere.

Najlepszym rozwiązaniem jest zintegrowanie [domeny usługi AD z Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->

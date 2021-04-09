---
title: Koncepcja — Integruj wdrożenie rozwiązania Azure VMware z architekturą Hub i szprychy
description: Dowiedz się więcej na temat integrowania wdrożenia rozwiązania Azure VMware w architekturze gwiazdy i szprych na platformie Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99062749"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrowanie rozwiązań VMware platformy Azure z architekturą Hub i szprychy

Ten artykuł zawiera zalecenia dotyczące integrowania wdrożenia rozwiązania Azure VMware w istniejącej lub nowej [architekturze Hub i szprych](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) na platformie Azure. 


Scenariusz Hub i szprych zakłada środowisko chmury hybrydowej z obciążeniami w:

* Natywne platformy Azure korzystające z usług IaaS lub PaaS
* Rozwiązanie Azure VMware 
* vSphere lokalnie

## <a name="architecture"></a>Architektura

*Centrum* to Virtual Network platformy Azure, które działa jako centralne punkty łączności z chmurą prywatną i rozwiązaniem VMware platformy Azure. *Szprychy* są sieciami wirtualnymi równorzędnymi z koncentratorem, aby umożliwić komunikację między sieciami wirtualnymi.

Ruch między lokalnym centrum danych i chmurą prywatną rozwiązania VMware platformy Azure oraz centrum odbywa się za pomocą połączeń usługi Azure ExpressRoute. Sieci wirtualne szprych zazwyczaj zawierają obciążenia oparte na IaaS, ale mogą mieć usługi PaaS, takie jak [App Service Environment](../app-service/environment/intro.md), które mają bezpośrednią integrację z usługą Virtual Network lub innymi usługami PaaS z włączonym [łączem prywatnym platformy Azure](../private-link/index.yml) .

>[!IMPORTANT]
>Możesz użyć istniejącej bramy ExpressRoute, aby nawiązać połączenie z rozwiązaniem VMware platformy Azure, o ile nie przekracza limit czterech obwodów ExpressRoute na sieć wirtualną.  Jednak w celu uzyskania dostępu do rozwiązania VMware platformy Azure ze środowisk lokalnych za pośrednictwem usługi ExpressRoute należy mieć ExpressRoute Global Reach, ponieważ Brama ExpressRoute nie zapewnia routingu przechodniego między podłączonymi do niego obwodami.

Na diagramie przedstawiono przykład wdrożenia centrum i szprych na platformie Azure połączonych z lokalnym i rozwiązaniem VMware platformy Azure za pomocą ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Wdrożenie integracji usługi Hub i gwiazdy platformy Azure VMware" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

Architektura ma następujące główne składniki:

- **Lokacja lokalna:** Lokalne centra danych klienta połączone z platformą Azure za pomocą połączenia ExpressRoute.

- **Chmura prywatna rozwiązania Azure VMware:** Rozwiązanie VMware platformy Azure SDDC utworzone przez co najmniej jeden klaster vSphere, każdy z nich z maksymalnie 16 hostami.

- **ExpressRoute Gateway:** Umożliwia komunikację między chmurą prywatną rozwiązań VMware platformy Azure, usługami udostępnionymi w sieci wirtualnej Hub i obciążeniami działającymi w sieciach wirtualnych szprych.

- **ExpressRoute Global REACH:** Umożliwia łączność między środowiskiem lokalnym i chmurą prywatną rozwiązania VMware platformy Azure. Łączność między rozwiązaniem VMware platformy Azure i siecią szkieletową Azure odbywa się wyłącznie za pośrednictwem ExpressRoute Global Reach. Nie można wybrać żadnej opcji poza ExpressRoute szybka ścieżka.  ExpressRoute Direct nie jest obsługiwana.


- **Zagadnienia dotyczące sieci VPN S2S:** W przypadku wdrożeń produkcyjnych rozwiązania VMware platformy Azure usługa Azure S2S VPN nie jest obsługiwana z powodu wymagań sieci dla programu VMware HCX. Można go jednak użyć do wdrożenia koncepcji ZK.


- **Sieć wirtualna centrum:** Działa jako centralny punkt łączności z siecią lokalną i chmurą prywatną rozwiązania VMware platformy Azure.

- **Sieć wirtualna szprych**

    - **IaaS szprych:** IaaS szpryche obsługuje obciążenia oparte na usłudze Azure IaaS, w tym zestawy dostępności maszyn wirtualnych i zestawy skalowania maszyn sieciowych oraz odpowiednie składniki sieciowe.

    - **PaaS szprych:** PaaS szprychy udostępnia usługi Azure PaaS Services przy użyciu adresów prywatnych, łącząc je z [prywatnym punktem końcowym](../private-link/private-endpoint-overview.md) i [prywatnym](../private-link/private-link-overview.md).

- **Zapora platformy Azure:** Działa jako centralny element do segmentacji ruchu między szprychami i rozwiązaniem VMware platformy Azure.

- **Application Gateway:** Udostępnia i chroni aplikacje sieci Web, które są uruchamiane na maszynach wirtualnych Azure IaaS/PaaS lub Azure VMware Solution Machines. Integruje się z innymi usługami, takimi jak API Management.

## <a name="network-and-security-considerations"></a>Zagadnienia dotyczące sieci i zabezpieczeń

Połączenia ExpressRoute umożliwiają przepływ ruchu między środowiskiem lokalnym, rozwiązaniem VMware platformy Azure i siecią szkieletową platformy Azure. W celu zaimplementowania tej łączności rozwiązanie VMware platformy Azure używa [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) .

Ponieważ Brama ExpressRoute nie zapewnia przechodniej routingu między podłączonymi do nich obwodami, łączność lokalna musi również używać ExpressRoute Global Reach do komunikacji między środowiskiem lokalnym vSphere i rozwiązaniem VMware platformy Azure. 

* **Przepływ ruchu rozwiązań w środowisku lokalnym do platformy Azure VMware**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Przepływ ruchu rozwiązań w środowisku lokalnym do platformy Azure VMware" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Rozwiązanie VMware platformy Azure do centralnego przepływu ruchu w sieci wirtualnej**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware — rozwiązanie do przepływu ruchu w sieci wirtualnej Hub" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Aby uzyskać więcej informacji na temat rozwiązań dotyczących sieci i łączności VMware firmy Azure, zapoznaj się z [dokumentacją produktu Solution VMware](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentacja ruchu

[Zapora systemu Azure](../firewall/index.yml) to centralny element topologii piasty i szprych wdrożony w sieci wirtualnej centrum. Użyj zapory platformy Azure lub innego sieciowego urządzenia wirtualnego platformy Azure do ustanowienia reguł ruchu i segmentacji komunikacji między różnymi szprychami i obciążeniami rozwiązań VMware platformy Azure.

Utwórz tabele tras, aby skierować ruch do zapory platformy Azure.  W przypadku sieci wirtualnych szprych Utwórz trasę, która ustawia domyślną trasę do wewnętrznego interfejsu zapory platformy Azure. W ten sposób, gdy obciążenie w Virtual Network musi dotrzeć do przestrzeni adresowej rozwiązań VMware platformy Azure, Zapora może ją oszacować i zastosować odpowiednią regułę ruchu w celu zezwolenia lub odmowy.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Tworzenie tabel tras w celu kierowania ruchu do zapory platformy Azure" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Trasa z prefiksem adresu 0.0.0.0/0 w ustawieniu **GatewaySubnet** nie jest obsługiwana.

Ustaw trasy dla określonych sieci w odpowiedniej tabeli tras. Na przykład trasy docierające do usługi Azure VMware Solution Management i obciążenia prefiksy adresów IP z obciążeń szprych i inne sposoby.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Ustawianie tras dla określonych sieci w odpowiedniej tabeli tras" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Drugi poziom segmentacji ruchu przy użyciu sieciowych grup zabezpieczeń w szprychach i w centrum do tworzenia bardziej szczegółowych zasad ruchu sieciowego.

> [!NOTE]
> **Ruch z rozwiązań lokalnych do platformy Azure VMware:** Ruch między obciążeniami lokalnymi, vSpheremi lub innymi, są włączane przez Global Reach, ale ruch nie przechodzi przez zaporę platformy Azure w centrum. W tym scenariuszu należy zaimplementować mechanizmy segmentacji ruchu lokalnego lub w rozwiązaniu VMware platformy Azure.

### <a name="application-gateway"></a>Application Gateway

Platformy Azure Application Gateway V1 i v2 zostały przetestowane przy użyciu aplikacji sieci Web, które działają na maszynach wirtualnych rozwiązań VMware platformy Azure jako Pula zaplecza. Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web działających na maszynach wirtualnych rozwiązań VMware platformy Azure do Internetu. Może również bezpiecznie udostępnić aplikacje użytkownikom wewnętrznym.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący rozwiązań VMware platformy Azure w [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md).

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Drugi poziom segmentacji ruchu przy użyciu sieciowych grup zabezpieczeń" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Pole skoku i usługa Azure bastionu

Dostęp do środowiska rozwiązań VMware platformy Azure za pomocą pola skoku, które jest maszyną wirtualną z systemem Windows 10 lub Windows Server wdrożoną w podsieci usługi udostępnionej w sieci wirtualnej centrum.

>[!IMPORTANT]
>Azure bastionu to usługa, która jest zalecana do łączenia się z polem skoku, aby uniemożliwić udostępnianie rozwiązania Azure VMware do Internetu. Nie można używać usługi Azure bastionu do nawiązywania połączeń z maszynami wirtualnymi rozwiązań VMware platformy Azure, ponieważ nie są one obiektami usługi Azure IaaS.  

Najlepszym rozwiązaniem w zakresie zabezpieczeń jest wdrożenie [Microsoft Azure bastionu](../bastion/index.yml) w ramach sieci wirtualnej centrum. Usługa Azure bastionu zapewnia bezproblemowe dostęp protokołu RDP i SSH do maszyn wirtualnych wdrożonych na platformie Azure bez konieczności aprowizacji publicznych adresów IP do tych zasobów. Po zainicjowaniu obsługi administracyjnej usługi Azure bastionu można uzyskać dostęp do wybranej maszyny wirtualnej z Azure Portal. Po nawiązaniu połączenia zostanie otwarta nowa karta, w której jest wyświetlany pulpit pola skoku i z poziomu tego pulpitu, można uzyskać dostęp do płaszczyzny zarządzania chmurą prywatną rozwiązania Azure VMware.

> [!IMPORTANT]
> Nie należy przydzielić publicznego adresu IP do maszyny wirtualnej z polem skoku ani udostępniać portu 3389/TCP do publicznego Internetu. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Sieć wirtualna usługi Azure bastionu Hub" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Zagadnienia dotyczące rozwiązywania problemów Azure DNS

W przypadku Azure DNS rozpoznawania dostępne są dwie opcje:

-   Użyj kontrolerów domeny wdrożonych w centrum (opisanego w temacie [uwagi dotyczące tożsamości](#identity-considerations)) jako serwerów nazw.

-   Wdróż i Skonfiguruj strefę prywatną Azure DNS.

Najlepszym podejściem jest łączenie obu rozwiązań w celu zapewnienia niezawodnego rozpoznawania nazw na potrzeby rozwiązania VMware dla platformy Azure, lokalnego i platformy Azure.

Zgodnie z ogólnym zaleceniem projektowym należy używać istniejącej infrastruktury Azure DNS (w tym przypadku Active Directory zintegrowanej DNS) wdrożonej na co najmniej dwóch maszynach wirtualnych platformy Azure wdrożonych w sieci wirtualnej Hub i skonfigurowanych w sieciach wirtualnych szprych do używania tych serwerów Azure DNS w ustawieniach DNS.

Możesz użyć usługi Azure Prywatna strefa DNS, w której strefa Prywatna strefa DNS platformy Azure łączy się z siecią wirtualną.  Serwery DNS są używane jako resolvery hybrydowe z warunkowym przekazywaniem do lokalnego lub rozwiązania VMware platformy Azure, w którym działa system DNS, przy użyciu infrastruktury Prywatna strefa DNS klienta platformy Azure. 

Aby automatycznie zarządzać cyklem życia rekordów DNS dla maszyn wirtualnych wdrożonych w sieciach wirtualnych szprych, Włącz autorejestrację. Gdy ta funkcja jest włączona, Maksymalna liczba prywatnych stref DNS jest tylko jeden. W przypadku wyłączenia wartość maksymalna to 1000.

Serwery rozwiązań lokalnych i platformy Azure VMware można skonfigurować za pomocą usług przesyłania dalej warunkowego do maszyn wirtualnych programu rozpoznawania nazw na platformie Azure dla strefy Prywatna strefa DNS platformy Azure.

## <a name="identity-considerations"></a>Zagadnienia dotyczące tożsamości

Najlepszym rozwiązaniem jest wdrożenie co najmniej jednego kontrolera domeny w centrum. Użyj dwóch podsieci usługi udostępnionej w ramach rozproszonej strefy lub zestawu dostępności maszyny wirtualnej. Aby uzyskać więcej informacji na temat rozszerzania lokalnej domeny Active Directory (AD) na platformę Azure, zobacz [centrum architektury platformy Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

Ponadto Wdróż inny kontroler domeny na stronie rozwiązania VMware platformy Azure, aby działać jako tożsamość i źródło DNS w środowisku vSphere.

Najlepszym rozwiązaniem jest zintegrowanie [domeny usługi AD z Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->

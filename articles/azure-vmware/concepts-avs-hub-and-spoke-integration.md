---
title: Koncepcja — integruje wdrożenie rozwiązania Azure VMware (Automatyczna synchronizacja) w architekturze gwiazdy i szprych
description: Zapoznaj się z zaleceniami dotyczącymi integracji wdrożenia programu Azure VMware Solution (Automatyczna synchronizacja) w istniejącej lub nowej architekturze Hub i szprych na platformie Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85375048"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Integrowanie rozwiązania Azure VMware (Automatyczna synchronizacja) w architekturze gwiazdy i szprych

W tym artykule udostępniamy zalecenia dotyczące integrowania wdrożenia programu Azure VMware Solution (Automatyczna synchronizacja) w istniejącej lub nowej [architekturze Hub i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) na platformie Azure. 

Scenariusz Hub i szprych zakłada środowisko chmury hybrydowej z obciążeniami w:

* Natywne platformy Azure korzystające z usług IaaS lub PaaS
* AUTOMATYCZNEJ 
* vSphere lokalnie

## <a name="architecture"></a>Architektura

*Centrum* to Virtual Network platformy Azure, które działa jako centralny punkt łączności z chmurą prywatną i funkcją automatycznej synchronizacji. *Szprychy* są sieciami wirtualnymi równorzędnymi z koncentratorem, aby umożliwić komunikację między sieciami wirtualnymi.

Ruch między lokalnym centrum danych a chmurą prywatną w chmurze, a centrum odbywa się za pomocą połączeń ExpressRoute. Sieci wirtualne szprych zazwyczaj zawierają obciążenia oparte na IaaS, ale mogą mieć usługi PaaS, takie jak [App Service Environment](../app-service/environment/intro.md), które mają bezpośrednią integrację z usługą Virtual Network lub innymi usługami PaaS z włączonym [łączem prywatnym platformy Azure](https://docs.microsoft.com/azure/private-link/) . 

Na diagramie przedstawiono przykład wdrożenia centrum i szprych na platformie Azure połączonych z lokalnymi i automatyczna synchronizacja za pomocą ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Wdrożenie narzędzia do integracji centrów i gwiazdy":::




Architektura ma następujące główne składniki:

-   **Lokacja lokalna:** Lokalne centra danych klienta połączone z platformą Azure za pomocą połączenia usługi Express Route.

-   **Automatyczna synchronizacja chmury prywatnej:** Automatyczna synchronizacja SDDC utworzona przez co najmniej jeden klaster vSphere, każdy z nich z maksymalnie 16 węzłami.

-   **ExpressRoute Gateway:** Umożliwia komunikację między chmurą prywatną, siecią lokalną, usługami udostępnionymi w sieci wirtualnej centrum oraz obciążeniami działającymi w sieciach wirtualnych szprych.

    > [!NOTE]
    > **Zagadnienia dotyczące sieci VPN S2S:** W przypadku wdrożeń produkcyjnych na potrzeby automatycznej synchronizacji usługa Azure S2S nie jest obsługiwana ze względu na wymagania dotyczące sieci dla usługi HCX. Jednakże w przypadku wdrożenia w ramach koncepcji lub nieprodukcyjnej, które nie wymaga HCX, można go użyć.

-   **Sieć wirtualna centrum:** Działa jako centralny punkt łączności z siecią lokalną i automatyczna synchronizacja chmury prywatnej.

-   **Sieć wirtualna szprych**

    -   **IaaS szprych:** IaaS szprych będzie hostować obciążenia oparte na usłudze Azure IaaS, w tym zestawy dostępności maszyn wirtualnych i zestawy skalowania maszyny wirtualnej oraz odpowiednie składniki sieciowe.

    -   **PaaS szprych:** PaaS szprychy udostępnia usługi Azure PaaS Services przy użyciu adresów prywatnych, łącząc je z [prywatnym punktem końcowym](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) i [prywatnym](https://docs.microsoft.com/azure/private-link/private-link-overview).

-   **Zapora platformy Azure:** Działa jako centralny element do segmentacji ruchu między szprychami, Premium i automatyczna synchronizacja.

-   **Application Gateway:** Udostępnia i chroni aplikacje sieci Web, które są uruchamiane na maszynach wirtualnych Azure IaaS/PaaS lub automatycznej synchronizacji. Integruje się z innymi usługami, takimi jak API Management.

## <a name="network-and-security-considerations"></a>Zagadnienia dotyczące sieci i zabezpieczeń

Połączenia ExpressRoute umożliwiają przepływ ruchu między środowiskiem lokalnym, automatyczna synchronizacja i siecią szkieletową Azure. W celu zaimplementowania tej łączności funkcja automatycznej synchronizacji używa [Global REACH ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) .

Łączność lokalna może również korzystać z ExpressRoute Global Reach, ale nie jest to wymagane.

* **Ruch przychodzący w środowisku lokalnym do automatycznej synchronizacji**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Ruch przychodzący w środowisku lokalnym do automatycznej synchronizacji":::


* **Automatyczna synchronizacja z przepływem ruchu w sieci wirtualnej**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Automatyczna synchronizacja z przepływem ruchu w sieci wirtualnej centrum":::


Więcej szczegółowych informacji na temat automatycznej synchronizacji i koncepcji związanych z łącznością w programie można znaleźć w [dokumentacji produktu](https://docs.microsoft.com/azure/azure-vmware/concepts-networking).

### <a name="traffic-segmentation"></a>Segmentacja ruchu

[Zapora systemu Azure](https://docs.microsoft.com/azure/firewall/) jest centralną częścią topologii gwiazdy, wdrożoną w centralnej sieci wirtualnej. Użyj zapory platformy Azure lub innego obsługiwanego sieciowego urządzenia wirtualnego platformy Azure do ustanowienia reguł ruchu i segmentacji komunikacji między różnymi szprychami, lokalnymi i AUTOMATYCZNAmi obciążeniami.

Utwórz tabele tras, aby skierować ruch do zapory platformy Azure.  W przypadku sieci wirtualnych szprych Utwórz trasę, która ustawia domyślną trasę do wewnętrznego interfejsu zapory platformy Azure, w ten sposób, gdy obciążenie w Virtual Network musi dotrzeć do przestrzeni adresowej automatycznej synchronizacji, którą może oszacować Zapora, i zastosuj odpowiednią regułę ruchu, aby zezwolić na dostęp lub go zabronić.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Tworzenie tabel tras w celu kierowania ruchu do zapory platformy Azure":::


> [!IMPORTANT]
> Trasa z prefiksem adresu 0.0.0.0/0 w ustawieniu **GatewaySubnet** nie jest obsługiwana.

Ustaw trasy dla określonych sieci w odpowiedniej tabeli tras. Na przykład trasy umożliwiające zarządzanie AUTOMATYCZną i obciążeniami prefiksami adresów IP w środowisku lokalnym i na odwrót, a następnie kierowanie całego ruchu z lokalnego do automatycznej synchronizacji chmur prywatnych za pośrednictwem zapory platformy Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Ustawianie tras dla określonych sieci w odpowiedniej tabeli tras":::

Drugi poziom segmentacji ruchu przy użyciu sieciowych grup zabezpieczeń w szprychach i w centrum do tworzenia bardziej szczegółowych zasad ruchu sieciowego. 


### <a name="application-gateway"></a>Application Gateway

Platformy Azure Application Gateway V1 i v2 zostały przetestowane przy użyciu aplikacji sieci Web, które działają na maszynach wirtualnych automatycznej synchronizacji jako Pula zaplecza. Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web uruchamianych na maszynach wirtualnych z funkcją automatycznej synchronizacji do Internetu. Może również bezpiecznie udostępnić aplikacje użytkownikom wewnętrznym.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Drugi poziom segmentacji ruchu przy użyciu sieciowych grup zabezpieczeń":::


### <a name="jumpbox-and-azure-bastion"></a>Serwera przesiadkowego i Azure bastionu

Dostęp do środowiska automatycznej synchronizacji z programem serwera przesiadkowego, który jest maszyną wirtualną z systemem Windows 10 lub Windows Server wdrożoną w podsieci usługi udostępnionej w sieci wirtualnej centrum.

Najlepszym rozwiązaniem w zakresie zabezpieczeń jest wdrożenie [Microsoft Azure bastionu](https://docs.microsoft.com/azure/bastion/) w ramach sieci wirtualnej centrum. Usługa Azure bastionu zapewnia bezproblemowe dostęp protokołu RDP i SSH do maszyn wirtualnych wdrożonych na platformie Azure bez konieczności aprowizacji publicznych adresów IP do tych zasobów. Po zainicjowaniu obsługi administracyjnej usługi Azure bastionu można uzyskać dostęp do wybranej maszyny wirtualnej z Azure Portal. Po nawiązaniu połączenia zostanie otwarta nowa karta, na której jest wyświetlany pulpit serwera przesiadkowego oraz z poziomu tego pulpitu, można uzyskać dostęp do płaszczyzny zarządzania chmurą prywatną w chmurze.

> [!IMPORTANT]
> Nie należy przydzielić publicznego adresu IP do maszyny wirtualnej serwera przesiadkowego lub uwidocznić Port 3389/TCP w publicznym Internecie. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Sieć wirtualna usługi Azure bastionu Hub":::


## <a name="azure-dns-resolution-considerations"></a>Zagadnienia dotyczące rozwiązywania problemów Azure DNS

Aby uzyskać Azure DNS rozwiązanie, dostępne są dwie opcje:

-   Użyj kontrolerów domeny Azure Active Directory (Azure AD) wdrożonych w centrum (opisanego w temacie [uwagi dotyczące tożsamości](#identity-considerations)) jako serwerów nazw.

-   Wdróż i Skonfiguruj strefę prywatną Azure DNS.

Najlepszym rozwiązaniem jest łączenie obu rozwiązań w celu zapewnienia niezawodnego rozpoznawania nazw na potrzeby automatycznej synchronizacji, lokalnego i platformy Azure.

Zgodnie z ogólnym zaleceniem projektowym należy używać istniejącej infrastruktury Azure DNS (w tym przypadku Active Directory zintegrowanej DNS) wdrożonej na co najmniej dwóch maszynach wirtualnych platformy Azure wdrożonych w sieci wirtualnej Hub i skonfigurowanych w sieciach wirtualnych szprych do używania tych serwerów Azure DNS w ustawieniach DNS.

Nadal można używać platformy Azure Prywatna strefa DNS, w której strefa Prywatna strefa DNS platformy Azure jest połączona z sieciami wirtualnymi, a serwery DNS są używane jako resolvery hybrydowe z warunkowym przekazywaniem do lokalnego/automatycznej synchronizacji z nazwami DNS, które korzystają z infrastruktury Prywatna strefa DNS platformy Azure dla klientów.

Istnieje kilka zagadnień, które należy wziąć pod uwagę w przypadku Azure DNS stref prywatnych:

* Należy włączyć autorejestrację, aby Azure DNS automatycznie zarządzać cyklem życia rekordów DNS dla maszyn wirtualnych wdrożonych w sieciach wirtualnych szprych.
* Maksymalna liczba prywatnych stref DNS, z którymi może być połączona sieć wirtualna, jest tylko jedna.
* Maksymalna liczba prywatnych stref DNS, z którymi można połączyć sieć wirtualną, to 1000 bez włączonej autorejestracji.

Serwery lokalne i automatyczna synchronizacja można skonfigurować za pomocą usług przesyłania dalej warunkowego do maszyn wirtualnych programu rozpoznawania nazw na platformie Azure dla strefy Prywatna strefa DNS platformy Azure.

## <a name="identity-considerations"></a>Zagadnienia dotyczące tożsamości

Najlepszym rozwiązaniem jest wdrożenie co najmniej jednego kontrolera domeny usługi Active Directory w centrum przy użyciu podsieci usługi udostępnionej, idealnie do dwóch z nich w sposób dystrybuowany w ramach stref lub zestawu dostępności maszyn wirtualnych. Zobacz [centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) , aby rozszerzyć lokalną domenę usługi AD na platformę Azure.

Ponadto Wdróż inny kontroler domeny na stronie automatycznej synchronizacji, aby działać jako tożsamość i źródło DNS w środowisku vSphere.

W przypadku programu vCenter i logowania jednokrotnego ustaw źródło tożsamości w Azure Portal na stronie **Zarządzanie \> \> źródłami tożsamości tożsamości**.

Najlepszym rozwiązaniem jest zintegrowanie [domeny usługi AD z Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->



---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 963f0698b921caa413c61059ad69284c41b4f265
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999453"
---
Użycie integracji regionalnej sieci wirtualnej umożliwia aplikacji dostęp do:

* Zasoby w sieci wirtualnej w tym samym regionie, w którym znajduje się aplikacja.
* Zasoby w usłudze sieci wirtualnych połączone z siecią wirtualną, z którą jest zintegrowana aplikacja.
* Zabezpieczone usługi punktów końcowych usługi.
* Zasoby w ramach połączeń usługi Azure ExpressRoute.
* Zasoby w sieci wirtualnej, z którą nawiązano integrację.
* Zasoby między połączeniami równorzędnymi, które obejmują połączenia usługi Azure ExpressRoute.
* Prywatne punkty końcowe 

W przypadku korzystania z integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie można korzystać z następujących funkcji sieciowych platformy Azure:

* **Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)**: można zablokować ruch wychodzący z sieciowej grupy zabezpieczeń, który znajduje się w podsieci integracji. Reguły ruchu przychodzącego nie mają zastosowania, ponieważ nie można używać integracji sieci wirtualnej w celu zapewnienia dostępu do aplikacji w ramach ruchu przychodzącego.
* **Tabele tras (UDR)**: można umieścić tabelę tras w podsieci integracji w celu wysłania ruchu wychodzącego w dowolnym miejscu.

Domyślnie aplikacja kieruje tylko ruch RFC1918 do sieci wirtualnej. Jeśli chcesz skierować cały ruch wychodzący do sieci wirtualnej, Zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika **konfiguracji** w portalu aplikacji. Wybierz pozycję **Nowe ustawienie aplikacji**.
1. Wprowadź **WEBSITE_VNET_ROUTE_ALL** w polu **Nazwa** , a następnie wprowadź **1** w polu **wartość** .

   ![Podaj ustawienie aplikacji][4]

1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Jeśli cały ruch wychodzący jest kierowany do sieci wirtualnej, podlega sieciowych grup zabezpieczeń i UDR, które są stosowane do podsieci integracji. W przypadku kierowania całego ruchu wychodzącego do sieci wirtualnej adresy wychodzące nadal są adresami wychodzącymi wymienionymi we właściwościach aplikacji, chyba że zostaną podane trasy do wysyłania ruchu w innym miejscu.

Istnieją pewne ograniczenia dotyczące używania integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie:

* Nie można uzyskać dostępu do zasobów w ramach globalnych połączeń komunikacji równorzędnej.
* Ta funkcja jest dostępna ze wszystkich App Service jednostek skalowania w wersji Premium v2 i Premium v3. Jest ona również dostępna w wersji Standard, ale tylko z nowszych App Service jednostek skalowania. Jeśli jesteś w starszej jednostce skalowania, możesz użyć tej funkcji tylko z planu App Service Premium w wersji 2. Jeśli chcesz mieć możliwość użycia funkcji w planie standardowej App Service, Utwórz aplikację w planie App Service Premium v3. Plany te są obsługiwane tylko przez nasze najnowsze jednostki skalowania. Możesz skalować w dół, jeśli wolisz.  
* Podsieć integracji może być używana tylko przez jeden plan App Service.
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w App Service Environment.
* Ta funkcja wymaga nieużywanej podsieci, która jest/28 lub większa w sieci wirtualnej Azure Resource Manager.
* Aplikacja i Sieć wirtualna muszą znajdować się w tym samym regionie.
* Nie można usunąć sieci wirtualnej przy użyciu zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej.
* Integrację z usługą sieci wirtualnych można zintegrować tylko w ramach tej samej subskrypcji, w której znajduje się aplikacja.
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na App Service plan. Wiele aplikacji w tym samym planie App Service może korzystać z tej samej sieci wirtualnej.
* Nie możesz zmienić subskrypcji aplikacji ani planu, gdy istnieje aplikacja, która korzysta z integracji regionalnej sieci wirtualnej.
* Twoja aplikacja nie może rozpoznać adresów w Azure DNS Private Zones bez zmian konfiguracji

Integracja z siecią wirtualną zależy od użycia dedykowanej podsieci.  Po udostępnieniu podsieci, podsieć platformy Azure traci 5 adresów IP od początku. Jeden z adresów jest używany z podsieci integracji dla każdego wystąpienia planu. W przypadku skalowania aplikacji do czterech wystąpień używane są cztery adresy. Debet 5 adresów od rozmiaru podsieci oznacza, że maksymalna liczba dostępnych adresów na blok CIDR to:

- /28 ma 11 adresów
- /27 ma 27 adresów
- /26 ma 59 adresów

W przypadku skalowania w górę lub w dół w miarę potrzeb będzie potrzebny krótki okres. Limity rozmiaru oznaczają rzeczywiste dostępne obsługiwane wystąpienia na rozmiar podsieci, jeśli podsieć jest:

- /28 maksymalna skalowanie w poziomie to 5 wystąpień
- /27 maksymalna skala pozioma to 13 wystąpień
- /26 maksymalna skalowanie w poziomie to 29 wystąpień

Limity zanotowane na maksymalnej skali poziomej założono, że konieczne będzie skalowanie w górę lub w dół w dowolnym momencie lub w poziomie jednostki SKU. 

Ponieważ nie można zmienić rozmiaru podsieci po przypisaniu, użyj podsieci, która jest wystarczająco duża, aby można było dowolnie skalować aplikację. Aby uniknąć problemów z pojemnością podsieci, zalecanym rozmiarem jest/26 z 64 adresów.  

Jeśli chcesz, aby aplikacje w innym planie miały dostęp do sieci wirtualnej, która jest już połączona przez aplikacje w innym planie, wybierz inną podsieć niż używana przez istniejącą integrację z siecią wirtualną.

Ta funkcja jest w pełni obsługiwana zarówno w przypadku aplikacji systemu Windows, jak i Linux, w tym [kontenerów niestandardowych](../articles/app-service/quickstart-custom-container.md). Wszystkie zachowania działają tak samo w aplikacjach systemu Windows i Linux.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Integracja z regionalną siecią wirtualną umożliwia korzystanie z punktów końcowych usługi. Aby korzystać z punktów końcowych usługi w aplikacji, należy użyć regionalnej integracji sieci wirtualnej w celu nawiązania połączenia z wybraną siecią wirtualną, a następnie skonfigurowania punktów końcowych usługi przy użyciu usługi docelowej w podsieci użytej do integracji. Jeśli chcesz uzyskać dostęp do usługi za pośrednictwem punktów końcowych usługi:

1. Konfigurowanie integracji regionalnej sieci wirtualnej z aplikacją internetową
1. Przejdź do usługi docelowej i skonfiguruj punkty końcowe usługi dla podsieci używanej do integracji

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Za pomocą sieciowych grup zabezpieczeń można blokować ruch przychodzący i wychodzący do zasobów w sieci wirtualnej. Aplikacja, która korzysta z integracji regionalnej sieci wirtualnej, może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch do adresów publicznych, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawione na 1. Reguły ruchu przychodzącego w sieciowej grupy zabezpieczeń nie mają zastosowania do Twojej aplikacji, ponieważ Integracja sieci wirtualnej ma wpływ tylko na ruch wychodzący z aplikacji.

Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji ograniczenia dostępu. SIECIOWEJ grupy zabezpieczeń zastosowana do podsieci integracji obowiązuje niezależnie od trasy zastosowanej do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, a nie masz żadnych tras, które wpływają na publiczny ruch sieciowy w podsieci integracji, cały ruch wychodzący nadal podlega sieciowych grup zabezpieczeń przypisanej do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL nie jest ustawiona, sieciowych grup zabezpieczeń są stosowane tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Za pomocą tabel tras można kierować ruch wychodzący z aplikacji do dowolnego miejsca. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918. Jeśli ustawisz wartość WEBSITE_VNET_ROUTE_ALL na 1, wpłynie to na wszystkie wywołania wychodzące. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na żądania aplikacji przychodzących. Wspólne miejsca docelowe mogą obejmować urządzenia zapory lub bramy.

Jeśli chcesz skierować cały ruch wychodzący lokalnie, możesz użyć tabeli tras do wysłania całego ruchu wychodzącego do bramy ExpressRoute. W przypadku kierowania ruchu do bramy należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy Border Gateway Protocol (BGP) wpływają również na ruch aplikacji. Jeśli masz trasy BGP ze względu na bramę ExpressRoute, wpłynie to na ruch wychodzący aplikacji. Domyślnie trasy BGP mają wpływ tylko na ruch docelowy RFC1918. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, trasy protokołu BGP mogą mieć wpływ na cały ruch wychodzący.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Gdy aplikacja zostanie zintegrowana z siecią wirtualną, używa tego samego serwera DNS, z którym jest skonfigurowana Sieć wirtualna. To zachowanie można zastąpić w aplikacji przez skonfigurowanie ustawienia aplikacji WEBSITE_DNS_SERVER z adresem żądanego serwera DNS. Jeśli skonfigurowano niestandardowy serwer DNS z siecią wirtualną, ale chciałeś korzystać z aplikacji Azure DNS strefach prywatnych, należy ustawić WEBSITE_DNS_SERVER za pomocą wartości 168.63.129.16. 

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Jeśli chcesz wykonać wywołania do [prywatnych punktów końcowych][privateendpoints], musisz się upewnić, że wyszukiwania DNS będą rozpoznawane jako prywatny punkt końcowy. Aby upewnić się, że wyszukiwania DNS z aplikacji wskazują Twoje prywatne punkty końcowe, możesz:

* Integracja z usługą Azure DNS Private Zones. Jeśli sieć wirtualna nie ma niestandardowego serwera DNS, będzie to automatyczne
* Zarządzanie prywatnym punktem końcowym na serwerze DNS używanym przez aplikację. Aby to zrobić, musisz znać prywatny adres punktu końcowego, a następnie wskazać punkt końcowy, który próbujesz połączyć z rekordem.
* Skonfiguruj własny serwer DNS do przesyłania dalej do Azure DNS stref prywatnych

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
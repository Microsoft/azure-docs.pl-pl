---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609541"
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

Domyślnie aplikacja kieruje tylko ruch RFC1918 do sieci wirtualnej. Jeśli chcesz skierować cały ruch wychodzący do sieci wirtualnej, wykonaj następujące kroki, aby dodać `WEBSITE_VNET_ROUTE_ALL` ustawienie do aplikacji: 

1. Przejdź do interfejsu użytkownika **konfiguracji** w portalu aplikacji. Wybierz pozycję **Nowe ustawienie aplikacji**.
1. Wprowadź `WEBSITE_VNET_ROUTE_ALL` w polu **Nazwa** , a następnie wprowadź `1` w polu **wartość** .

   ![Podaj ustawienie aplikacji][4]

1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Gdy cały ruch wychodzący jest kierowany do sieci wirtualnej, podlega sieciowych grup zabezpieczeń i UDR, które są stosowane do podsieci integracji. Gdy `WEBSITE_VNET_ROUTE_ALL` jest ustawiona na `1` , ruch wychodzący jest nadal wysyłany z adresów wymienionych we właściwościach aplikacji, o ile nie zostaną podane trasy kierujące ruchem w innym miejscu.
> 
> W ramach regionalnej integracji sieci wirtualnej nie można użyć portu 25.

Istnieją pewne ograniczenia dotyczące używania integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie:

* Nie można uzyskać dostępu do zasobów w ramach globalnych połączeń komunikacji równorzędnej.
* Ta funkcja jest dostępna ze wszystkich App Service jednostek skalowania w wersji Premium v2 i Premium v3. Jest ona również dostępna w wersji Standard, ale tylko z nowszych App Service jednostek skalowania. Jeśli jesteś w starszej jednostce skalowania, możesz użyć tylko tej funkcji z planu App Service Premium w wersji 2. Jeśli chcesz mieć pewność, że możesz użyć funkcji w planie standardowej App Service, Utwórz aplikację w planie App Service Premium v3. Plany te są obsługiwane tylko przez nasze najnowsze jednostki skalowania. Możesz skalować w dół, jeśli wolisz.  
* Podsieć integracji może być używana tylko przez jeden plan App Service.
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w App Service Environment.
* Ta funkcja wymaga nieużywanej podsieci, która jest/28 lub większa w sieci wirtualnej Azure Resource Manager.
* Aplikacja i Sieć wirtualna muszą znajdować się w tym samym regionie.
* Nie można usunąć sieci wirtualnej przy użyciu zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej.
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na App Service plan. Wiele aplikacji w tym samym planie App Service może korzystać z tej samej sieci wirtualnej.
* Nie możesz zmienić subskrypcji aplikacji ani planu, gdy istnieje aplikacja, która korzysta z integracji regionalnej sieci wirtualnej.
* Twoja aplikacja nie może rozpoznać adresów w Azure DNS Private Zones bez zmian konfiguracji.

Integracja z siecią wirtualną zależy od dedykowanej podsieci. Po udostępnieniu podsieci, podsieć platformy Azure traci pięć adresów IP od początku. Jeden z adresów jest używany z podsieci integracji dla każdego wystąpienia planu. W przypadku skalowania aplikacji do czterech wystąpień używane są cztery adresy. 

W przypadku skalowania w górę lub w dół Wymagana przestrzeń adresowa jest podwójna przez krótki czas. Ma to wpływ na prawdziwe, dostępne obsługiwane wystąpienia dla danego rozmiaru podsieci. W poniższej tabeli przedstawiono zarówno maksymalną liczbę dostępnych adresów na blok CIDR, jak i wpływ na skalowanie w poziomie:

| Rozmiar bloku CIDR | Maksymalna liczba dostępnych adresów | Maksymalna Skala pozioma (wystąpienia)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Przyjęto założenie, że w pewnym momencie trzeba będzie skalować w górę lub w dół w dowolnym rozmiarze lub w jednostce SKU. 

Ponieważ nie można zmienić rozmiaru podsieci po przypisaniu, użyj podsieci, która jest wystarczająco duża, aby można było dowolnie skalować aplikację. Aby uniknąć problemów z pojemnością podsieci, należy używać adresów/26 z 64.  

Jeśli chcesz, aby aplikacje w innym planie miały dostęp do sieci wirtualnej, która jest już połączona przez aplikacje w innym planie, wybierz inną podsieć niż ta, która jest używana przez istniejącą integrację z siecią wirtualną.

Ta funkcja jest w pełni obsługiwana zarówno w przypadku aplikacji systemu Windows, jak i Linux, w tym [kontenerów niestandardowych](../articles/app-service/quickstart-custom-container.md). Wszystkie zachowania działają tak samo w aplikacjach systemu Windows i Linux.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Integracja z regionalną siecią wirtualną umożliwia dostęp do usług platformy Azure, które są zabezpieczone za pomocą punktów końcowych usługi. Aby uzyskać dostęp do usługi zabezpieczonej przez punkt końcowy usługi, należy wykonać następujące czynności:

1. Skonfiguruj regionalną integrację sieci wirtualnej z aplikacją internetową, aby połączyć się z określoną podsiecią w celu integracji.
1. Przejdź do usługi docelowej i skonfiguruj punkty końcowe usługi w podsieci integracji.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Za pomocą sieciowych grup zabezpieczeń można blokować ruch przychodzący i wychodzący do zasobów w sieci wirtualnej. Aplikacja, która korzysta z integracji regionalnej sieci wirtualnej, może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch do adresów publicznych, musisz mieć ustawienie aplikacji `WEBSITE_VNET_ROUTE_ALL` ustawione na `1` . Reguły ruchu przychodzącego w sieciowej grupy zabezpieczeń nie mają zastosowania do Twojej aplikacji, ponieważ Integracja sieci wirtualnej ma wpływ tylko na ruch wychodzący z aplikacji.

Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji ograniczenia dostępu. SIECIOWEJ grupy zabezpieczeń zastosowana do podsieci integracji obowiązuje niezależnie od trasy zastosowanej do podsieci integracji. Jeśli `WEBSITE_VNET_ROUTE_ALL` jest ustawiona na `1` i nie masz żadnych tras mających wpływ na ruch publiczny w podsieci integracji, cały ruch wychodzący nadal podlega sieciowych grup zabezpieczeń przypisanej do podsieci integracji. Gdy `WEBSITE_VNET_ROUTE_ALL` nie jest ustawiona, sieciowych grup zabezpieczeń są stosowane tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Za pomocą tabel tras można kierować ruch wychodzący z aplikacji do dowolnego miejsca. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918. Po ustawieniu `WEBSITE_VNET_ROUTE_ALL` na `1` wszystkie Twoje wywołania wychodzące zostaną uwzględnione. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na żądania aplikacji przychodzących. Wspólne miejsca docelowe mogą obejmować urządzenia zapory lub bramy.

Jeśli chcesz skierować cały ruch wychodzący lokalnie, możesz użyć tabeli tras do wysłania całego ruchu wychodzącego do bramy ExpressRoute. W przypadku kierowania ruchu do bramy należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy Border Gateway Protocol (BGP) wpływają również na ruch aplikacji. Jeśli masz trasy BGP ze względu na bramę ExpressRoute, dotyczy to ruchu wychodzącego aplikacji. Domyślnie trasy BGP mają wpływ tylko na ruch docelowy RFC1918. Gdy `WEBSITE_VNET_ROUTE_ALL` jest ustawiona na `1` , wszystkie ruch wychodzący mogą mieć wpływ na trasy protokołu BGP.

### <a name="azure-dns-private-zones"></a>Azure DNS stref prywatnych 

Gdy aplikacja zostanie zintegrowana z siecią wirtualną, używa tego samego serwera DNS, z którym jest skonfigurowana Sieć wirtualna. Domyślnie aplikacja nie będzie współpracować z Azure DNS strefami prywatnymi. Aby można było korzystać z stref prywatnych Azure DNS, należy dodać następujące ustawienia aplikacji:

1. `WEBSITE_DNS_SERVER` z wartością `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` z wartością `1`

Te ustawienia wysyłają wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej i umożliwiają aplikacji dostęp do Azure DNS strefy prywatnej. Dzięki tym ustawieniom aplikacja może używać Azure DNS, badając strefę prywatną DNS na poziomie procesu roboczego.  

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Aby wykonać wywołania do [prywatnych punktów końcowych][privateendpoints], należy się upewnić, że wyszukiwanie DNS jest rozpoznawane jako prywatny punkt końcowy. To zachowanie można wymusić w jeden z następujących sposobów: 

* Integracja z Azure DNS strefami prywatnymi. Jeśli sieć wirtualna nie ma niestandardowego serwera DNS, jest to wykonywane automatycznie.
* Zarządzanie prywatnym punktem końcowym na serwerze DNS używanym przez aplikację. W tym celu należy znać prywatny adres punktu końcowego, a następnie wskazać punkt końcowy, do którego próbujesz uzyskać dostęp przy użyciu rekordu A.
* Skonfiguruj własny serwer DNS do przesyłania dalej do Azure DNS stref prywatnych.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md

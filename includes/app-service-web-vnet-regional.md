---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604883"
---
Korzystanie z regionalnej integracji sieci wirtualnej umożliwia dostęp aplikacji:

* Zasoby w sieci wirtualnej w tym samym regionie co aplikacja.
* Zasoby w sieciach wirtualnych równorzędne z siecią wirtualną, z pomocą sieci wirtualnej, z pomocą jest zintegrowana.
* Usługi zabezpieczone punktu końcowego usługi.
* Zasoby w połączeniach usługi Azure ExpressRoute.
* Zasoby w sieci wirtualnej, z którą jesteś zintegrowany.
* Zasoby między połączeniami równorzędnym, które obejmują połączenia Usługi Azure ExpressRoute.
* Prywatne punkty końcowe 

Korzystając z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie, można użyć następujących funkcji sieci platformy Azure:

* **Sieciowe grupy zabezpieczeń (NSG)**: Można zablokować ruch wychodzący za pomocą sieciowej grupy zabezpieczeń umieszczonej w podsieci integracji. Reguły ruchu przychodzącego nie mają zastosowania, ponieważ nie można użyć integracji sieci wirtualnej, aby zapewnić dostęp przychodzący do aplikacji.
* **Tabele tras (UDR)**: Tabelę tras można umieścić w podsieci integracji, aby wysyłać ruch wychodzący w odpowiedni sposób.

Domyślnie aplikacja kieruje tylko ruch RFC1918 do sieci wirtualnej. Jeśli chcesz rozsyłać cały ruch wychodzący do sieci wirtualnej, zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika **konfiguracji** w portalu aplikacji. Wybierz **pozycję Nowe ustawienie aplikacji**.
1. Wprowadź **WEBSITE_VNET_ROUTE_ALL** w polu **Nazwa** i wprowadź **1** w polu **Wartość.**

   ![Podaj ustawienie aplikacji][4]

1. Kliknij przycisk **OK**.
1. Wybierz pozycję **Zapisz**.

Jeśli trasa całego ruchu wychodzącego do sieci wirtualnej, podlega nsgs i UDRs, które są stosowane do podsieci integracji. Podczas kierowania całego ruchu wychodzącego do sieci wirtualnej, adresy wychodzące są nadal adresy wychodzące, które są wymienione we właściwościach aplikacji, chyba że udostępniasz trasy do wysyłania ruchu w innym miejscu.

Istnieją pewne ograniczenia dotyczące korzystania z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie:

* Nie można dotrzeć do zasobów w globalnych połączeniach komunikacji równorzędnej.
* Ta funkcja jest dostępna tylko z nowszych jednostek skalowania usługi Azure App Service, które obsługują plany usługi aplikacji PremiumV2.
* Podsieć integracji może być używana tylko przez jeden plan usługi app service.
* Tej funkcji nie można używać przez aplikacje izolowanego planu, które znajdują się w środowisku usługi aplikacji.
* Ta funkcja wymaga nieużywane podsieci, która jest /27 z 32 adresów lub większe w sieci wirtualnej usługi Azure Resource Manager.
* Aplikacja i sieci wirtualnej musi znajdować się w tym samym regionie.
* Nie można usunąć sieci wirtualnej za pomocą zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej.
* Można zintegrować tylko z sieciami wirtualnymi w tej samej subskrypcji co aplikacja.
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na plan usługi app service. Wiele aplikacji w tym samym planie usługi app service można użyć tej samej sieci wirtualnej.
* Nie można zmienić subskrypcji aplikacji lub planu, gdy istnieje aplikacja, która korzysta z regionalnej integracji sieci wirtualnej.
* Aplikacja nie może rozpoznać adresów w strefach prywatnych dns platformy Azure bez zmian konfiguracji

Jeden adres jest używany dla każdego wystąpienia planu. Jeśli skalujesz aplikację do pięciu wystąpień, zostanie użytych pięć adresów. Ponieważ rozmiaru podsieci nie można zmienić po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby pomieścić dowolną skalę, do której może dotrzeć aplikacja. A /26 z 64 adresami jest zalecanym rozmiarem. /26 z 64 adresami obsługuje plan Premium z 30 wystąpieniami. Podczas skalowania planu w górę lub w dół, trzeba dwa razy więcej adresów na krótki okres czasu.

Jeśli chcesz, aby aplikacje w innym planie osiągnęły sieci wirtualnej, która jest już połączona z aplikacjami w innym planie, wybierz inną podsieć niż ta używana przez istniejącą integrację sieci wirtualnej.

Funkcja jest w wersji zapoznawczej dla Linuksa. Forma linuksa tej funkcji obsługuje tylko wykonywanie połączeń na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Aplikacja sieci Web lub funkcja dla kontenerów

Jeśli hostujesz aplikację w systemie Linux z wbudowanymi obrazami, regionalna integracja sieci wirtualnej działa bez dodatkowych zmian. Jeśli używasz aplikacji sieci Web lub funkcji dla kontenerów, należy zmodyfikować obraz platformy docker, aby użyć integracji sieci wirtualnej. W obrazie platformy docker użyj zmiennej środowiskowej PORT jako portu nasłuchiwania głównego serwera sieci Web, zamiast numeru portu zakodowanego. Zmienna środowiskowa PORT jest automatycznie ustawiana przez platformę w czasie uruchamiania kontenera. Jeśli używasz SSH, demon SSH musi być skonfigurowany do nasłuchiwać na numer portu określony przez zmienną środowiskową SSH_PORT podczas korzystania z regionalnej integracji sieci wirtualnej. Nie ma obsługi integracji sieci wirtualnej wymaganej przez bramę w systemie Linux.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Regionalna integracja sieci wirtualnej umożliwia korzystanie z punktów końcowych usługi. Aby użyć punktów końcowych usługi z aplikacją, użyj regionalnej integracji sieci wirtualnej, aby połączyć się z wybraną siecią wirtualną, a następnie skonfiguruj punkty końcowe usługi z usługą docelową w podsieci używanej do integracji. Jeśli następnie chcesz uzyskać dostęp do usługi za połacie punktów końcowych usługi:

1. konfigurowanie regionalnej integracji sieci wirtualnej z aplikacją internetową
1. przejdź do usługi docelowej i skonfiguruj punkty końcowe usługi względem podsieci używanej do integracji

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowych grup zabezpieczeń można używać do blokowania ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej. Aplikacja korzystająca z regionalnej integracji sieci wirtualnej może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch na adresy publiczne, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawiona na 1. Reguły ruchu przychodzącego w sieciowej sieciowej nie mają zastosowania do aplikacji, ponieważ integracja sieci wirtualnej wpływa tylko na ruch wychodzący z aplikacji.

Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji Ograniczenia dostępu. Sieciowej sieciowej, która jest stosowana do podsieci integracji jest w życie niezależnie od tras stosowanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1 i nie masz żadnych tras, które wpływają na ruch adresu publicznego w podsieci integracji, cały ruch wychodzący jest nadal przedmiotem sieciowej grup zabezpieczeń przypisanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL nie jest ustawiona, nsgs są stosowane tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Tabele tras umożliwiają kierowanie ruchu wychodzącego z aplikacji do dowolnego miejsca. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918. Jeśli ustawisz WEBSITE_VNET_ROUTE_ALL na 1, dotyczy to wszystkich połączeń wychodzących. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na przychodzące żądania aplikacji. Typowe miejsca docelowe mogą obejmować urządzenia zapory lub bramy.

Jeśli chcesz rozsyłać cały ruch wychodzący lokalnie, możesz użyć tabeli tras, aby wysłać cały ruch wychodzący do bramy usługi ExpressRoute. Jeśli kierujesz ruch do bramy, należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy protokołu BGP (Border Gateway Protocol) również wpływają na ruch w aplikacji. Jeśli masz trasy BGP z czegoś takiego jak brama usługi ExpressRoute, ruch wychodzący aplikacji będzie miał wpływ. Domyślnie trasy BGP mają wpływ tylko na ruch docelowy RFC1918. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, wszystkie trasy BGP mogą mieć wpływ na cały ruch wychodzący.

### <a name="azure-dns-private-zones"></a>Strefy prywatne usługi Azure DNS 

Po zintegrowaniu aplikacji z siecią wirtualną używa tego samego serwera DNS, z którego jest skonfigurowana witryna wirtualna. Domyślnie aplikacja nie będzie działać z usługą Azure DNS Private Zones. Aby pracować z strefami prywatnymi usługi Azure DNS, należy dodać następujące ustawienia aplikacji:

1. WEBSITE_DNS_SERVER o wartości 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL o wartości 1

Te ustawienia będą wysyłać wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej, oprócz włączania aplikacji do korzystania ze stref prywatnych usługi Azure DNS.

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Jeśli chcesz nawiązywać połączenia z [prywatnymi punktami końcowymi,][privateendpoints]musisz zintegrować się z prywatnymi strefami DNS platformy Azure lub zarządzać prywatnym punktem końcowym na serwerze DNS używanym przez aplikację. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint

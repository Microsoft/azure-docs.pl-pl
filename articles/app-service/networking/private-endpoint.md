---
title: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
description: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 06/02/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 15b3f2e48b78036c02ef86446f2ab920f22f7c76
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295443"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Używanie prywatnych punktów końcowych dla usługi Azure Web App (wersja zapoznawcza)

> [!Note]
> Po odświeżeniu wersji zapoznawczej wydano funkcję ochrony danych eksfiltracji.
>
> Wersja zapoznawcza jest dostępna we wszystkich regionach publicznych dla PremiumV2 Windows i Linux Web Apps i elastycznych funkcji Premium. 

Możesz użyć prywatnego punktu końcowego dla aplikacji sieci Web platformy Azure, aby umożliwić klientom znajdującym się w sieci prywatnej bezpieczny dostęp do aplikacji za pośrednictwem prywatnego linku. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ruch sieciowy między klientem w sieci prywatnej a aplikacją sieci Web przechodzi przez sieć wirtualną oraz prywatny link w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Korzystanie z prywatnego punktu końcowego dla aplikacji sieci Web umożliwia:

- Zabezpiecz swoją aplikację sieci Web, konfigurując prywatny punkt końcowy, eliminując ujawnianie opinii publicznej.
- Bezpiecznie łącz się z aplikacją internetową z sieci lokalnych, które łączą się z siecią wirtualną za pomocą sieci VPN lub prywatnej komunikacji równorzędnej ExpressRoute.
- Unikaj eksfiltracji danych z sieci wirtualnej. 

Jeśli potrzebujesz bezpiecznego połączenia między siecią wirtualną a aplikacją sieci Web, punkt końcowy usługi jest najprostszym rozwiązaniem. Jeśli trzeba również skontaktować się z aplikacją internetową ze strony lokalnej za pośrednictwem bramy platformy Azure, sieci równorzędnej z regionalną lub wirtualną siecią równorzędną, jest to rozwiązanie.  

Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi][serviceendpoint].

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy to specjalny interfejs sieciowy (NIC) dla aplikacji sieci Web platformy Azure w podsieci w Virtual Network (VNet).
Utworzenie prywatnego punktu końcowego dla aplikacji sieci Web zapewnia bezpieczną łączność między klientami w sieci prywatnej a aplikacją sieci Web. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej.
Połączenie między prywatnym punktem końcowym a aplikacją sieci Web używa bezpiecznego [linku prywatnego][privatelink]. Prywatny punkt końcowy jest używany tylko w przypadku przepływów przychodzących do aplikacji sieci Web. Przepływy wychodzące nie będą używać tego prywatnego punktu końcowego, ale można wstrzyknąć przepływy wychodzące do sieci w innej podsieci za pomocą [funkcji integracji sieci wirtualnej][vnetintegrationfeature].

Podsieć, w której jest podłączony prywatny punkt końcowy, może zawierać inne zasoby, nie jest potrzebna dedykowana pusta podsieć.
Możesz również wdrożyć prywatny punkt końcowy w innym regionie niż aplikacja internetowa. 

> [!Note]
>Funkcja integracji sieci wirtualnej nie może używać tej samej podsieci niż prywatny punkt końcowy. jest to ograniczenie funkcji integracji sieci wirtualnej.

Z perspektywy zabezpieczeń:

- Po włączeniu prywatnych punktów końcowych w aplikacji sieci Web należy wyłączyć wszystkie prawa dostępu publicznego.
- Można włączyć wiele prywatnych punktów końcowych w innych sieci wirtualnych i podsieciach, w tym sieci wirtualnych w innych regionach.
- Adres IP karty sieciowej prywatnego punktu końcowego musi być dynamiczny, ale pozostanie taki sam do momentu usunięcia prywatnego punktu końcowego.
- Karta sieciowa prywatnego punktu końcowego nie może mieć skojarzonej sieciowej grupy zabezpieczeń.
- W podsieci, w której znajduje się prywatny punkt końcowy, może być skojarzona sieciowej grupy zabezpieczeń, ale należy wyłączyć wymuszanie zasad sieciowych dla prywatnego punktu końcowego: zobacz [wyłączanie zasad sieciowych dla prywatnych punktów końcowych][disablesecuritype]. W związku z tym nie można filtrować według sieciowej grupy zabezpieczeń dostępu do prywatnego punktu końcowego.
- Po włączeniu prywatnego punktu końcowego w aplikacji sieci Web konfiguracja [ograniczeń dostępu][accessrestrictions] aplikacji sieci Web nie jest szacowana.
- Możesz wyeliminować ryzyko związane z eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły sieciowej grupy zabezpieczeń, w których miejsce docelowe jest znacznikiem internetowym lub usług platformy Azure. Po wdrożeniu prywatnego punktu końcowego dla aplikacji sieci Web można uzyskać dostęp tylko do tej konkretnej aplikacji sieci Web za pomocą prywatnego punktu końcowego. Jeśli masz inną aplikację sieci Web, musisz wdrożyć inny dedykowany prywatny punkt końcowy dla tej innej aplikacji sieci Web.

W dziennikach HTTP sieci Web aplikacji sieci Web znajduje się adres IP źródła klienta. Jest to implementowane przy użyciu protokołu serwera proxy TCP, przekazując Właściwość IP klienta do aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie informacji o połączeniu przy użyciu protokołu TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globalny punkt końcowy punktu końcowego aplikacji sieci Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, nie zmieniamy wpisu DNS w trakcie okresu zapoznawczego. Musisz samodzielnie zarządzać wpisem DNS na prywatnym serwerze DNS lub Azure DNS strefie prywatnej.
Jeśli musisz użyć niestandardowej nazwy DNS, musisz dodać nazwę niestandardową w aplikacji sieci Web. W trakcie korzystania z wersji zapoznawczej Nazwa niestandardowa musi być zweryfikowana, podobnie jak nazwa niestandardowa, przy użyciu publicznego rozpoznawania DNS. Aby uzyskać więcej informacji, zobacz [niestandardowe sprawdzanie poprawności nazw DNS][dnsvalidation].

Jeśli musisz na przykład użyć konsoli programu kudu lub interfejsu API REST (wdrożenie z obsługą platformy Azure DevOps), musisz utworzyć dwa rekordy w strefie prywatnej Azure DNS lub na niestandardowym serwerze DNS. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link][pricing].

## <a name="limitations"></a>Ograniczenia

Gdy korzystasz z funkcji platformy Azure w elastycznym planie Premium z prywatnym punktem końcowym, aby uruchomić lub wykonać funkcję w portalu internetowym platformy Azure, musisz mieć bezpośredni dostęp do sieci lub otrzymać błąd HTTP 403. Innymi słowy, przeglądarka musi mieć dostęp do prywatnego punktu końcowego, aby wykonać funkcję z portalu sieci Web systemu Azure. 

W trakcie okresu zapoznawczego tylko miejsce produkcyjne jest widoczne za prywatnym punktem końcowym, a inne gniazda muszą być osiągalne przez publiczny punkt końcowy.

Regularnie udoskonalamy funkcję prywatnego linku i prywatnego punktu końcowego. Zapoznaj się z [tym artykułem][pllimitations] , aby uzyskać aktualne informacje o ograniczeniach.

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web za pomocą portalu, zobacz [jak połączyć się prywatnie z aplikacją sieci Web][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal

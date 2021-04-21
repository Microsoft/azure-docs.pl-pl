---
title: Adresy IP dla ruchu przychodzącego/wychodzącego
description: Dowiedz się, jak przychodzące i wychodzące adresy IP są używane w Azure App Service, kiedy się zmieniają i jak znaleźć adresy dla aplikacji.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1dda487d23c9f955aea8e35d16e5a560a890a173
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834484"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzące i wychodzące adresy IP w Azure App Service

[Azure App Service](overview.md) jest usługą wielodostępną, z wyjątkiem [App Service środowiskach.](environment/intro.md) Aplikacje, które nie znajdują się w App Service (nie w warstwie [Izolowana),](https://azure.microsoft.com/pricing/details/app-service/)współdzielą infrastrukturę sieciową z innymi aplikacjami. W związku z tym adresy IP dla ruchu przychodzącego i wychodzącego aplikacji mogą być różne, a nawet mogą ulec zmianie w niektórych sytuacjach.

[App Service używają](environment/intro.md) dedykowanych infrastruktur sieci, dzięki czemu aplikacje działające w środowisku App Service uzyskają statyczne, dedykowane adresy IP dla połączeń przychodzących i wychodzących.

## <a name="how-ip-addresses-work-in-app-service"></a>Jak działają adresy IP w App Service

Aplikacja App Service działa w ramach planu App Service, a plany App Service są wdrażane w jednej z jednostek wdrażania w infrastrukturze platformy Azure (nazywanej wewnętrznie przestrzenią internetową). Każda jednostka wdrażania ma przypisany zestaw wirtualnych adresów IP, w tym jeden publiczny adres IP dla ruchu przychodzącego i zestaw [wychodzących adresów IP.](#find-outbound-ips) Wszystkie App Service w tej samej jednostce wdrożenia i wystąpieniach aplikacji, które są w nich uruchamiane, współużytkują ten sam zestaw wirtualnych adresów IP. W przypadku App Service Environment (plan usługi App Service w [](https://azure.microsoft.com/pricing/details/app-service/)warstwie Izolowana) plan App Service jest samą jednostką wdrożenia, więc w związku z tym wirtualne adresy IP są do niego dedykowane.

Ze względu na to, że nie można przenosić planu App Service między jednostkami wdrażania, wirtualne adresy IP przypisane do aplikacji zwykle pozostają takie same, ale istnieją wyjątki.

## <a name="when-inbound-ip-changes"></a>Po zmianie adresu IP dla ruchu przychodzącego

Niezależnie od liczby wystąpień skalowanych w zewnątrz każda aplikacja ma jeden przychodzący adres IP. Adres IP dla ruchu przychodzącego może ulec zmianie w przypadku wykonania jednej z następujących akcji:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów (jednostka wdrażania może ulec zmianie).
- Usuń ostatnią aplikację w kombinacji grupy zasobów i _regionu_ i utwórz ją ponownie (jednostka wdrożenia może ulec zmianie).
- Usuń istniejące powiązanie protokołu TLS/SSL oparte na adresie IP, na przykład podczas odnawiania certyfikatu (zobacz [Odnawianie certyfikatu).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Znajdowanie przychodzącego adresu IP

Po prostu uruchom następujące polecenie w terminalu lokalnym:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Uzyskiwanie statycznego adresu IP dla ruchu przychodzącego

Czasami możesz chcieć mieć dedykowany, statyczny adres IP dla aplikacji. Aby uzyskać statyczny adres IP dla ruchu przychodzącego, należy [zabezpieczyć domenę niestandardową](configure-ssl-bindings.md#secure-a-custom-domain). Jeśli do zabezpieczenia aplikacji nie potrzebujesz funkcji TLS, możesz nawet przekazać certyfikat z podpisem własnym dla tego powiązania. W przypadku powiązania protokołu TLS opartego na adresie IP certyfikat jest powiązany z samym adresem IP, więc App Service statyczny adres IP w celu jego rozwiązania. 

## <a name="when-outbound-ips-change"></a>Kiedy zmieniają się wychodzące ip

Niezależnie od liczby wystąpień skalowanych w poziomie każda aplikacja ma ustawioną liczbę wychodzących adresów IP w danym momencie. Każde połączenie wychodzące z aplikacji App Service, na przykład z bazą danych serwera, używa jednego z wychodzących adresów IP jako adresu IP źródła. Adres IP do użycia jest wybierany losowo w czasie wykonywania, więc usługa back-end musi otworzyć zaporę dla wszystkich wychodzących adresów IP aplikacji.

Zestaw wychodzących adresów IP aplikacji zmienia się w przypadku wykonania jednej z następujących akcji:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów (jednostka wdrażania może ulec zmianie).
- Usuń ostatnią aplikację w kombinacji grupy zasobów i _regionu_ i utwórz ją ponownie (jednostka wdrożenia może ulec zmianie).
- Skaluj aplikację między niższymi warstwami **(Podstawowa,** **Standardowa** i **Premium)** i **Premium V2** (adresy IP mogą być dodawane do zestawu lub odejmowane od zestawu).

Zestaw wszystkich możliwych wychodzących adresów IP, których aplikacja może używać, niezależnie od warstw cenowych, można znaleźć, szukając właściwości lub w polu Dodatkowe wychodzące adresy IP w bloku Właściwości w `possibleOutboundIpAddresses` Azure Portal.   Zobacz [Znajdowanie wychodzących ips](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdowanie wychodzących ip

Aby znaleźć adresy IP ruchu wychodzącego aktualnie używane przez  aplikację w Azure Portal, kliknij pozycję Właściwości w okienku nawigacji po lewej stronie aplikacji. Są one wyświetlane w polu **Wychodzące adresy IP.**

Te same informacje można znaleźć, uruchamiając następujące polecenie w Cloud Shell [.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Aby znaleźć _wszystkie_ możliwe wychodzące adresy IP dla aplikacji,  niezależnie od warstw cenowych, kliknij pozycję Właściwości w okienku nawigacji po lewej stronie aplikacji. Są one wymienione w **polu Dodatkowe adresy IP ruchu wychodzącego.**

Te same informacje można znaleźć, uruchamiając następujące polecenie w Cloud Shell [.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak ograniczyć ruch przychodzący według źródłowych adresów IP.

> [!div class="nextstepaction"]
> [Ograniczenia statycznych adresów IP](app-service-ip-restrictions.md)

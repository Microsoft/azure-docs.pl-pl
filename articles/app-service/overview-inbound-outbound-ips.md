---
title: Przychodzące/wychodzące adresy IP
description: Dowiedz się, w jaki sposób przychodzący i wychodzący adres IP są używane w Azure App Service, gdy zmieniają się i jak znaleźć adresy dla aplikacji.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591371"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzące i wychodzące adresy IP w Azure App Service

[Azure App Service](overview.md) to usługa z wieloma dzierżawami, z wyjątkiem [środowisk App Service](environment/intro.md). Aplikacje, które nie znajdują się w środowisku App Service (nie w [warstwie izolowanej](https://azure.microsoft.com/pricing/details/app-service/)), współużytkują infrastrukturę sieci z innymi aplikacjami. W związku z tym przychodzące i wychodzące adresy IP aplikacji mogą być różne i nawet w pewnych sytuacjach mogą się zmieniać.

[Środowiska App Service](environment/intro.md) używają dedykowanych infrastruktur sieciowych, więc aplikacje działające w środowisku App Service otrzymują statyczne, dedykowane adresy IP zarówno dla połączeń przychodzących, jak i wychodzących.

## <a name="how-ip-addresses-work-in-app-service"></a>Jak działają adresy IP w App Service

Aplikacja App Service uruchamiana w planie App Service, a plany App Service są wdrażane w jednej z jednostek wdrażania w infrastrukturze platformy Azure (wewnętrznie nazywanej przestrzenią sieci Web). Każda jednostka wdrożenia ma przypisany zestaw wirtualnych adresów IP, w tym jeden publiczny adres IP ruchu przychodzącego i zbiór [wychodzących adresów IP](#find-outbound-ips). Wszystkie plany App Service w tej samej jednostce wdrożenia i wystąpieniach aplikacji, które w nich działają, współużytkują ten sam zestaw wirtualnych adresów IP. W przypadku App Service Environment (planu App Service w [warstwie izolowanej](https://azure.microsoft.com/pricing/details/app-service/)) plan App Service jest samą jednostką wdrożenia, dzięki czemu wirtualne adresy IP są w efekcie dedykowane.

Ponieważ nie masz uprawnień do przenoszenia planu App Service między jednostkami wdrażania, wirtualne adresy IP przypisane do aplikacji zwykle pozostają takie same, ale istnieją wyjątki.

## <a name="when-inbound-ip-changes"></a>Gdy przychodzące zmiany adresów IP

Niezależnie od liczby wystąpień skalowanych w poziomie, każda aplikacja ma jeden adres IP ruchu przychodzącego. Adres IP ruchu przychodzącego może ulec zmianie podczas wykonywania jednej z następujących czynności:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów (może się zmienić jednostka wdrożenia).
- Usuń ostatnią aplikację w kombinacji grupy zasobów _i_ regionu i utwórz ją ponownie (jednostka wdrożenia może ulec zmianie).
- Usuń istniejące powiązanie protokołu TLS/SSL opartego na protokole IP, takie jak podczas odnawiania certyfikatu (zobacz [odnów certyfikat](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Znajdowanie przychodzącego adresu IP

Po prostu uruchom następujące polecenie w lokalnym terminalu:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Pobierz statyczny adres IP dla ruchu przychodzącego

Czasami może być potrzebny dedykowany, statyczny adres IP dla aplikacji. Aby uzyskać statyczny adres IP dla ruchu przychodzącego, należy [zabezpieczyć domenę niestandardową](configure-ssl-bindings.md#secure-a-custom-domain). Jeśli w rzeczywistości nie potrzebujesz funkcji TLS do zabezpieczenia aplikacji, możesz nawet przekazać certyfikat z podpisem własnym dla tego powiązania. W przypadku powiązań TLS opartych na protokole IP certyfikat jest powiązany z samym adresem IP, dlatego App Service udostępnia statyczny adres IP, aby go nawiązać. 

## <a name="when-outbound-ips-change"></a>Po zmianie wychodzących adresów IP

Niezależnie od liczby wystąpień skalowanych w poziomie, każda aplikacja ma określoną liczbę wychodzących adresów IP w danym momencie. Każde połączenie wychodzące z aplikacji App Service, na przykład do bazy danych zaplecza, używa jednego z wychodzących adresów IP jako źródłowego adresu IP. Adres IP do użycia jest wybierany losowo w czasie wykonywania, więc usługa zaplecza musi otworzyć Zaporę do wszystkich wychodzących adresów IP dla aplikacji.

Zestaw wychodzących adresów IP dla aplikacji zmienia się podczas wykonywania jednej z następujących czynności:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów (może się zmienić jednostka wdrożenia).
- Usuń ostatnią aplikację w kombinacji grupy zasobów _i_ regionu i utwórz ją ponownie (jednostka wdrożenia może ulec zmianie).
- Skaluj aplikację między niższą warstwą (**podstawowa**, **standardowa** i **Premium**) i warstwą **Premium v2** (adresy IP mogą być dodawane lub odejmowane z zestawu).

Można znaleźć zestaw wszystkich możliwych wychodzących adresów IP, które mogą być używane przez aplikację, niezależnie od warstw cenowych, szukając `possibleOutboundIpAddresses` właściwości lub w polu **dodatkowe wychodzące adresy IP** w bloku **Właściwości** w Azure Portal. Zobacz [Znajdź wychodzące adresy IP](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdź wychodzące adresy IP

Aby znaleźć wychodzące adresy IP aktualnie używane przez aplikację w Azure Portal, kliknij przycisk **Właściwości** w nawigacji po lewej stronie aplikacji. Są one wymienione w polu **wychodzące adresy IP** .

Te same informacje można znaleźć, uruchamiając następujące polecenie w [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Aby znaleźć _wszystkie_ możliwe wychodzące adresy IP dla aplikacji, bez względu na warstwy cenowe, kliknij pozycję **Właściwości** w obszarze nawigacji po lewej stronie aplikacji. Są one wymienione w polu **dodatkowe wychodzące adresy IP** .

Te same informacje można znaleźć, uruchamiając następujące polecenie w [Cloud Shell](../cloud-shell/quickstart.md).

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

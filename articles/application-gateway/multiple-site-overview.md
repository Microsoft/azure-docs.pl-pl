---
title: Hostowanie wielu witryn w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie obsługi wielolokacjowej platformy Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80257414"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostowanie wielu witryn usługi Application Gateway

Obsługa wielu witryn umożliwia skonfigurowanie więcej niż jednej aplikacji sieci Web na tym samym porcie bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. W poniższym przykładzie Brama Application Gateway obsługuje ruch dla `contoso.com` i `fabrikam.com` z dwóch pul serwera zaplecza o nazwie puli contososerverpool i puli fabrikamserverpool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Reguły są przetwarzane w kolejności, w której są wyświetlane w portalu dla jednostki SKU w wersji 1. W przypadku jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Zdecydowanie zaleca się skonfigurowanie odbiorników obejmujących wiele lokacji przed skonfigurowaniem podstawowego odbiornika.  Zapewni to skierowanie ruchu do odpowiedniego zaplecza. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Analogicznie, można hostować wiele poddomen w tej samej domenie nadrzędnej w ramach tego samego wdrożenia bramy aplikacji. Można na przykład hostować i `http://blog.contoso.com` `http://app.contoso.com` obsługiwać pojedyncze wdrożenie bramy aplikacji.

## <a name="host-headers-and-server-name-indication-sni"></a>Nagłówki hosta i oznaczanie nazwy serwera (SNI, Server Name Indication)

Istnieją trzy popularne mechanizmy włączania hostingu wielu witryn w tej samej infrastrukturze.

1. Hostowanie wielu aplikacji internetowych — każda z nich na unikatowym adresie IP.
2. Użycie nazwy hosta do hostowania wielu aplikacji internetowych na tym samym adresie IP.
3. Użycie różnych portów do hostowania wielu aplikacji internetowych na tym samym adresie IP.

Obecnie Application Gateway obsługuje jeden publiczny adres IP, na którym nasłuchuje ruch. W związku z tym wiele aplikacji, z których każdy ma własny adres IP, nie jest obecnie obsługiwane. 

Application Gateway obsługuje wiele aplikacji, które nasłuchują na różnych portach, ale ten scenariusz wymaga, aby aplikacje akceptowały ruch na portach niestandardowych. Często nie jest to konfiguracja, której chcesz użyć.

Usługa Application Gateway bazuje na nagłówkach hosta HTTP 1.1 w celu hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie. Lokacje hostowane w usłudze Application Gateway mogą również obsługiwać odciążanie protokołu TLS przy użyciu rozszerzenia TLS Oznaczanie nazwy serwera (SNI). Ten scenariusz oznacza, że przeglądarka i farma sieci Web zaplecza klienta muszą obsługiwać protokół HTTP/1.1 i rozszerzenie TLS zgodnie ze standardem RFC 6066.

## <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejące elementy konfiguracji odbiornika HttpListener są rozszerzane do obsługi elementów wskazujących nazwę hosta i nazwę serwera. Jest on używany przez Application Gateway do kierowania ruchu do odpowiedniej puli zaplecza. 

Poniższy przykład kodu jest fragmentem elementu HttpListeners z pliku szablonu:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Możesz odwiedzić stronę [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Szablon usługi Resource Manager z zastosowaniem hostowania wielu witryn), aby zapoznać się z kompleksowym wdrożeniem opartym na szablonie.

## <a name="routing-rule"></a>Reguła routingu

W regule routingu nie jest wymagana żadna zmiana. Nadal należy wybierać podstawową regułę routingu „Basic” w celu powiązania odpowiedniego odbiornika witryny z właściwą pulą adresów zaplecza.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z informacjami o hostowaniu wielu witryn przejdź do [tworzenia bramy aplikacji przy użyciu hostowania wielu witryn](tutorial-multiple-sites-powershell.md), aby utworzyć bramę aplikacji z możliwością obsługi więcej niż jednej aplikacji internetowej.


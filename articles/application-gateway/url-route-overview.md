---
title: Omówienie routingu zawartości opartego na adresach URL w usłudze Azure Application Gateway
description: Ten artykuł zawiera Omówienie routingu zawartości opartego na adresach URL usługi Azure Application Gateway, konfiguracji UrlPathMap i reguły PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a9b2e8148586ec58ea6a7a033099e726920857b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84987927"
---
# <a name="url-path-based-routing-overview"></a>Routing oparty na ścieżkach URL — omówienie

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. 

Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól serwerów zaplecza.

W poniższym przykładzie usługa Application Gateway obsługuje ruch dla domeny contoso.com z trzech pul serwerów zaplecza, na przykład: VideoServerPool, ImageServerPool i DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Żądania HTTP \: //contoso.com/video/* są kierowane do puli videoserverpool, a protokół http \: //contoso.com/images/* są kierowane do puli imageserverpool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

> [!IMPORTANT]
> W przypadku jednostki SKU V1 reguły są przetwarzane w kolejności, w której są wyświetlane w portalu. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik. W przypadku jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Jednak zdecydowanie zaleca się skonfigurowanie odbiorników z obsługą kilku lokacji najpierw przed skonfigurowaniem odbiornika podstawowego. Zapewnia to skierowanie ruchu do odpowiedniego zaplecza.

## <a name="urlpathmap-configuration-element"></a>Element konfiguracji UrlPathMap

Element urlPathMap jest używany do określania wzorców ścieżki na potrzeby mapowań pul serwerów zaplecza. Poniższy przykład kodu jest fragmentem elementu urlPathMap z pliku szablonu.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>Właściwości

Właściwości to lista wzorców ścieżek do dopasowania. Każdy wzorzec musi rozpoczynać się od znaku „/”, a znak gwiazdki „*” jest dozwolony jedynie na końcu po znaku „/”. Ciąg podawany do dopasowania ścieżki nie zawiera żadnego tekstu po pierwszym? lub #, a te znaki nie są dozwolone w tym miejscu. W przeciwnym razie wszystkie znaki dozwolone w adresie URL są dozwolone w właściwości.

Obsługiwane wzorce zależą od tego, czy wdrożono Application Gateway v1 lub v2:

#### <a name="v1"></a>wersjach

W regułach ścieżki jest rozróżniana wielkość liter.

|Wersja 1 — wzorzec ścieżki  |Czy jest obsługiwana?  |
|---------|---------|
|`/images/*`     |tak|
|`/images*`     |tak|
|`/images/*.jpg`     |nie|
|`/*.jpg`     |nie|
|`/Repos/*/Comments/*`     |nie|
|`/CurrentUser/Comments/*`     |tak|

#### <a name="v2"></a>v2

W regułach ścieżki jest rozróżniana wielkość liter.

|2 — wzorzec ścieżki  |Czy jest obsługiwana?  |
|---------|---------|
|`/images/*`     |tak|
|`/images*`     |tak|
|`/images/*.jpg`     |nie|
|`/*.jpg`     |nie|
|`/Repos/*/Comments/*`     |nie|
|`/CurrentUser/Comments/*`     |tak|

Aby uzyskać więcej informacji, zobacz [Resource Manager template using URL-based routing](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) (Szablon usługi Resource Manager korzystający z routingu opartego na adresach URL).

## <a name="pathbasedrouting-rule"></a>Reguła PathBasedRouting

Reguła RequestRoutingRule typu PathBasedRouting jest używana do powiązania odbiornika z elementem urlPathMap. Wszystkie żądania otrzymane dla tego odbiornika są kierowane zgodnie z zasadami określonymi w elemencie urlPathMap.
Fragment reguły PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z informacjami na temat routingu zawartości opartego na adresach URL skorzystaj z informacji dotyczących [tworzenia bramy aplikacji przy użyciu routingu opartego na adresach URL](create-url-route-portal.md), aby utworzyć bramę aplikacji za pomocą reguł routingu adresów URL.

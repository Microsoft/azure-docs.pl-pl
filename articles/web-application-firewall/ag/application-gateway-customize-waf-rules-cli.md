---
title: Dostosowywanie reguł przy użyciu interfejsu wiersza polecenia — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące sposobu dostosowywania reguł zapory aplikacji sieci Web w Application Gateway przy użyciu interfejsu wiersza polecenia platformy Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74048531"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure

Zapora aplikacji sieci Web Application Gateway Azure (WAF) zapewnia ochronę aplikacji sieci Web. Te zabezpieczenia są udostępniane przez zestaw reguł programu Open Web Application Security (OWASP) Core (KSR). Niektóre reguły mogą spowodować fałszywie dodatnie i blokować rzeczywisty ruch. Z tego powodu Application Gateway oferuje możliwość dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup reguł i reguł, zobacz [Lista zasad i reguł reguł KSR aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie zasad i grup reguł

Poniższy przykład kodu pokazuje, jak wyświetlać reguły i grupy reguł, które można konfigurować.

### <a name="view-rule-groups"></a>Wyświetl grupy reguł

Poniższy przykład pokazuje, jak wyświetlić grupy reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Następujące dane wyjściowe to obcięta odpowiedź z powyższego przykładu:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Wyświetlanie reguł w grupie reguł

Poniższy przykład pokazuje, jak wyświetlić reguły w określonej grupie reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Następujące dane wyjściowe to obcięta odpowiedź z powyższego przykładu:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Wyłączanie reguł

Poniższy przykład wyłącza reguły `910018` i `910017` bramę aplikacji:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Reguły obowiązkowe

Poniższa lista zawiera warunki, które powodują, że WAF zablokują żądanie w trybie zapobiegania (w trybie wykrywania są rejestrowane jako wyjątki). Nie można ich skonfigurować ani wyłączyć:

* Nie można przeanalizować treści żądania w wyniku zablokowania żądania, chyba że inspekcja treści jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści żądania (bez plików) jest większa niż skonfigurowany limit
* Treść żądania (w tym pliki) jest większa niż limit
* Wystąpił błąd wewnętrzny w aparacie WAF

Specyficzne dla KSR 3. x:

* Próg limitu przychodzącego wyniku anomalii

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wyłączonych reguł można dowiedzieć się, jak wyświetlić dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

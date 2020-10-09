---
title: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure
description: Dowiedz się, jak dodawać niestandardowe akcje do interfejsu API REST platformy Azure. W tym artykule omówiono wymagania i najlepsze rozwiązania dla punktów końcowych, które chcą zaimplementować akcje niestandardowe.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650398"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure

W tym artykule opisano wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych niestandardowego dostawcy zasobów platformy Azure, które implementują akcje niestandardowe. Jeśli nie znasz niestandardowych dostawców zasobów platformy Azure, zapoznaj [się z tematem Omówienie niestandardowych dostawców zasobów](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Jak zdefiniować punkt końcowy akcji

**Punkt końcowy** jest adresem URL, który wskazuje na usługę, która implementuje podstawowy kontrakt między działem IT i platformą Azure. Punkt końcowy jest zdefiniowany w dostawcy zasobów niestandardowych i może być dowolnym publicznie dostępnym adresem URL. Poniższy przykład zawiera **akcję** o nazwie `myCustomAction` zaimplementowane przez `endpointURL` .

Przykład **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Tworzenie punktu końcowego akcji

**Punkt końcowy** , który implementuje **akcję** , musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. Po utworzeniu niestandardowego dostawcy zasobów z **akcją** zostanie wygenerowany nowy zestaw interfejsów API na platformie Azure. W takim przypadku akcja spowoduje wygenerowanie nowego interfejsu API akcji platformy Azure dla `POST` wywołań:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

To żądanie zostanie następnie przekazane do **punktu końcowego** w postaci:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywana ponownie do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone w obiekcie najwyższego poziomu.
- `Content-Type`Nagłówek powinien być ustawiony na wartość "Application/JSON; charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Odpowiedź niestandardowego dostawcy zasobów platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Wywoływanie akcji niestandardowej

Istnieją dwa główne sposoby wywołania niestandardowej akcji poza niestandardowego dostawcę zasobów:

- Interfejs wiersza polecenia platformy Azure
- Szablony usługi Azure Resource Manager

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parametr | Wymagane | Opis
---|---|---
akcja | *opcję* | Nazwa akcji zdefiniowanej w **ResourceProvider**.
identyfikatory | *opcję* | Identyfikator zasobu **ResourceProvider**.
treść żądania | *znaleziono* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Akcje mają ograniczoną obsługę w szablonach Azure Resource Manager. Aby akcja była wywoływana wewnątrz szablonu, musi zawierać [`list`](../templates/template-functions-resource.md#list) prefiks w nazwie.

Przykład **ResourceProvider** z akcją listy:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Przykładowy szablon Azure Resource Manager:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parametr | Wymagane | Opis
---|---|---
resourceIdentifier | *opcję* | Identyfikator zasobu **ResourceProvider**.
apiVersion | *opcję* | Wersja interfejsu API środowiska uruchomieniowego zasobu. Powinno to być zawsze "2018-09-01-Preview".
functionValues | *znaleziono* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

## <a name="next-steps"></a>Następne kroki

- [Omówienie niestandardowych dostawców zasobów platformy Azure](overview.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)

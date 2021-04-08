---
title: Dokumentacja pamięci podręcznej zasobu niestandardowego
description: Niestandardowa dokumentacja pamięci podręcznej zasobów dla dostawców zasobów niestandardowych platformy Azure. W tym artykule przedstawiono wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe pamięci podręcznej.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650385"
---
# <a name="custom-resource-cache-reference"></a>Niestandardowe odwołanie do pamięci podręcznej zasobów

W tym artykule przedstawiono wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe pamięci podręcznej. Jeśli nie znasz niestandardowych dostawców zasobów platformy Azure, zapoznaj [się z tematem Omówienie niestandardowych dostawców zasobów](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu pamięci podręcznej

Zasób serwera proxy można utworzyć, określając element **routingtype** na wartość "proxy" w pamięci podręcznej.

Przykładowy dostawca zasobów niestandardowych:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

## <a name="building-proxy-resource-endpoint"></a>Tworzenie punktu końcowego zasobu serwera proxy

**Punkt końcowy** , który implementuje **punkt końcowy** zasobu "proxy" w pamięci podręcznej, musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. W takim przypadku obiekt **ResourceType** wygeneruje nowy interfejs API zasobów platformy Azure dla `PUT` , `GET` i, `DELETE` Aby wykonać CRUD na jednym zasobie, a także `GET` pobrać wszystkie istniejące zasoby:

> [!NOTE]
> Interfejs API platformy Azure będzie generować metody żądań `PUT` , `GET` , i `DELETE` , ale **punkt końcowy** pamięci podręcznej musi obsługiwać `PUT` i `DELETE` .
> Zalecamy, aby **punkt końcowy** został również zaimplementowany `GET` .

### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

To żądanie zostanie następnie przekazane do **punktu końcowego** w postaci:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywana ponownie do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone w obiekcie najwyższego poziomu.
- `Content-Type`Nagłówek powinien być ustawiony na wartość "Application/JSON; charset = utf-8 ".
- Dostawca zasobów niestandardowych spowoduje zastąpienie `name` `type` pól,, i `id` dla żądania.
- Dostawca zasobów niestandardowych zwróci tylko pola pod `properties` obiektem dla punktu końcowego pamięci podręcznej.

**Punkt końcowy** Reakcji

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`Pola, `id` i `type` zostaną automatycznie wygenerowane dla zasobu niestandardowego przez niestandardowego dostawcę zasobów.

Odpowiedź niestandardowego dostawcy zasobów platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Usuwanie zasobu niestandardowego

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

To żądanie zostanie następnie przekazane do **punktu końcowego** w postaci:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywana ponownie do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone w obiekcie najwyższego poziomu.
- `Content-Type`Nagłówek powinien być ustawiony na wartość "Application/JSON; charset = utf-8 ".
- Dostawca zasobów niestandardowych platformy Azure usunie element z jego pamięci podręcznej, jeśli zostanie zwrócona odpowiedź na poziomie 200. Nawet jeśli zasób nie istnieje, **punkt końcowy** powinien zwrócić 204.

**Punkt końcowy** Reakcji

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpowiedź niestandardowego dostawcy zasobów platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Żądanie **nie** zostanie przekazane do **punktu końcowego**.

Odpowiedź niestandardowego dostawcy zasobów platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Wylicz wszystkie zasoby niestandardowe

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

To żądanie **nie** zostanie przekazane do **punktu końcowego**.

Odpowiedź niestandardowego dostawcy zasobów platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie niestandardowych dostawców zasobów platformy Azure](overview.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: niestandardowe informacje o serwerze proxy zasobów](proxy-resource-endpoint-reference.md)

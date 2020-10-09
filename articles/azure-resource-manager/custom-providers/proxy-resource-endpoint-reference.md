---
title: Dokumentacja serwera proxy zasobu niestandardowego
description: Niestandardowy adres serwera proxy zasobów dla dostawców zasobów niestandardowych platformy Azure. W tym artykule opisano wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe serwera proxy.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650463"
---
# <a name="custom-resource-proxy-reference"></a>Odwołanie do niestandardowego serwera proxy zasobów

W tym artykule opisano wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe serwera proxy. Jeśli nie znasz niestandardowych dostawców zasobów platformy Azure, zapoznaj [się z tematem Omówienie niestandardowych dostawców zasobów](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu serwera proxy

Zasób serwera proxy można utworzyć, określając element **routingtype** jako "proxy".

Przykładowy dostawca zasobów niestandardowych:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Tworzenie punktu końcowego zasobu serwera proxy

**Punkt końcowy** , który implementuje **punkt końcowy** zasobu "proxy", musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. W takim przypadku obiekt **ResourceType** wygeneruje nowy interfejs API zasobów platformy Azure dla `PUT` , `GET` i, `DELETE` Aby wykonać CRUD na jednym zasobie, a także `GET` pobrać wszystkie istniejące zasoby.

> [!NOTE]
> `id`Pola, `name` i `type` nie są wymagane, ale są niezbędne do integracji zasobu niestandardowego z istniejącym ekosystemem platformy Azure.

Przykładowy zasób:

``` JSON
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

Odwołanie do parametru:

Właściwość | Sample | Opis
---|---|---
name | '{myCustomResourceName}' | Nazwa niestandardowego zasobu.
typ | "Microsoft. CustomProviders/resourceProviders/{resourceTypeName}" | Przestrzeń nazw typu zasobu.
identyfikator | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Identyfikator zasobu.

### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

Przychodzące żądanie interfejsu API platformy Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Punkt końcowy** Reakcji

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

To żądanie zostanie następnie przekazane do **punktu końcowego** w postaci:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywana ponownie do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone w obiekcie najwyższego poziomu.
- `Content-Type`Nagłówek powinien być ustawiony na wartość "Application/JSON; charset = utf-8 ".

**Punkt końcowy** Reakcji

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

To żądanie zostanie następnie przekazane do **punktu końcowego** w postaci:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywana ponownie do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone w obiekcie najwyższego poziomu.
- `Content-Type`Nagłówek powinien być ustawiony na wartość "Application/JSON; charset = utf-8 ".
- Listę zasobów należy umieścić pod właściwości najwyższego poziomu `value` .

**Punkt końcowy** Reakcji

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
- [Odwołanie: niestandardowe odwołanie do pamięci podręcznej zasobów](proxy-cache-resource-endpoint-reference.md)

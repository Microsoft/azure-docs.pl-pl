---
title: Dodawanie niestandardowych zasobów do interfejsu API REST platformy Azure
description: Dowiedz się, jak dodać zasoby niestandardowe do interfejsu API REST platformy Azure. W tym artykule omówiono wymagania i najlepsze rozwiązania dla punktów końcowych, które chcą zaimplementować zasoby niestandardowe.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650528"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Dodawanie niestandardowych zasobów do interfejsu API REST platformy Azure

W tym artykule opisano wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych niestandardowego dostawcy zasobów platformy Azure, które implementują zasoby niestandardowe. Jeśli nie znasz niestandardowych dostawców zasobów platformy Azure, zapoznaj [się z tematem Omówienie niestandardowych dostawców zasobów](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu

**Punkt końcowy** jest adresem URL, który wskazuje na usługę, która implementuje podstawowy kontrakt między działem IT i platformą Azure. Punkt końcowy jest zdefiniowany w dostawcy zasobów niestandardowych i może być dowolnym publicznie dostępnym adresem URL. Poniższy przykład ma element **ResourceType** o nazwie `myCustomResource` zaimplementowane przez `endpointURL` .

Przykład **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Kompilowanie punktu końcowego zasobu

**Punkt końcowy** , który implementuje element **ResourceType** , musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. Po utworzeniu niestandardowego dostawcy zasobów z obiektem **resourceer** zostanie wygenerowany nowy zestaw interfejsów API na platformie Azure. W takim przypadku obiekt **ResourceType** wygeneruje nowy interfejs API zasobów platformy Azure dla `PUT` , `GET` i, `DELETE` Aby wykonać CRUD na jednym zasobie, a także `GET` pobrać wszystkie istniejące zasoby:

Manipulowanie pojedynczym zasobem ( `PUT` , `GET` i `DELETE` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Pobierz wszystkie zasoby ( `GET` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

W przypadku zasobów niestandardowych dostawcy zasobów niestandardowych oferują dwa typy **routingTypes**: " `Proxy` " i " `Proxy, Cache` ".

### <a name="proxy-routing-type"></a>Typ routingu serwera proxy

Metody " `Proxy` " **routingtype** "wszystkie żądania metod do **punktu końcowego** określonego w dostawcy zasobów niestandardowych. Kiedy używać " `Proxy` ":

- Wymagana jest pełna kontrola nad odpowiedzią.
- Integrowanie systemów z istniejącymi zasobami.

Aby dowiedzieć się więcej o `Proxy` zasobach, zobacz informacje [na temat niestandardowego serwera proxy zasobów](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Typ routingu pamięci podręcznej serwera proxy

`Proxy, Cache`Tylko serwery proxy " **routingtype** " `PUT` i `DELETE` metody żądania do **punktu końcowego** określonego w dostawcy zasobów niestandardowych. Dostawca zasobów niestandardowych automatycznie zwróci `GET` żądania na podstawie tego, co zostało zapisane w pamięci podręcznej. Jeśli zasób niestandardowy jest oznaczony za pomocą pamięci podręcznej, dostawca zasobów niestandardowych doda również/Zastąp pola w odpowiedzi, aby udostępnić interfejsy API platformy Azure. Kiedy używać " `Proxy, Cache` ":

- Tworzenie nowego systemu, który nie ma istniejących zasobów.
- Pracuj z istniejącym ekosystemem platformy Azure.

Aby dowiedzieć się więcej o `Proxy, Cache` zasobach, zobacz [odwołanie do pamięci podręcznej zasobów niestandardowych](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Tworzenie zasobu niestandardowego

Istnieją dwa główne sposoby tworzenia zasobów niestandardowych poza niestandardowym dostawcą zasobów:

- Interfejs wiersza polecenia platformy Azure
- Szablony usługi Azure Resource Manager

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Utwórz zasób niestandardowy:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parametr | Wymagane | Opis
---|---|---
is-full-Object | *opcję* | Wskazuje, że obiekt właściwości zawiera inne opcje, takie jak lokalizacja, Tagi, jednostka SKU i/lub plan.
identyfikator | *opcję* | Identyfikator zasobu niestandardowego. Ta wartość powinna być wyłączona z **ResourceProvider**
properties | *opcję* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

Usuń zasób niestandardowy platformy Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagane | Opis
---|---|---
identyfikator | *opcję* | Identyfikator zasobu niestandardowego. Ta wartość powinna się znajdować poza **ResourceProvider**.

Pobierz zasób niestandardowy platformy Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagane | Opis
---|---|---
identyfikator | *opcję* | Identyfikator zasobu niestandardowego. Ta wartość powinna być wyłączona z **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Zasoby wymagają, aby odpowiedź zawierała odpowiednie `id` , `name` i `type` z **punktu końcowego**.

Szablony Azure Resource Manager wymagają `id` , że `name` i `type` są zwracane prawidłowo z punktu końcowego podrzędnego. Zwrócona odpowiedź zasobu powinna mieć postać:

Przykładowa odpowiedź **punktu końcowego** :

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Przykładowy szablon Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parametr | Wymagane | Opis
---|---|---
resourceTypeName | *opcję* | **Nazwa** typu **zasobów** zdefiniowanego w dostawcy niestandardowym.
resourceProviderName | *opcję* | Nazwa niestandardowego wystąpienia dostawcy zasobów.
customResourceName | *opcję* | Niestandardowa nazwa zasobu.

## <a name="next-steps"></a>Następne kroki

- [Omówienie niestandardowych dostawców zasobów platformy Azure](overview.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: niestandardowe informacje o serwerze proxy zasobów](proxy-resource-endpoint-reference.md)
- [Odwołanie: niestandardowe odwołanie do pamięci podręcznej zasobów](proxy-cache-resource-endpoint-reference.md)

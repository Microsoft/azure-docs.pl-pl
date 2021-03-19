---
title: Tworzenie i używanie dostawcy niestandardowego
description: W tym samouczku przedstawiono sposób tworzenia i używania niestandardowego dostawcy platformy Azure. Użyj dostawców niestandardowych, aby zmienić przepływy pracy na platformie Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503756"
---
# <a name="create-and-use-a-custom-provider"></a>Tworzenie i używanie dostawcy niestandardowego

Dostawca niestandardowy to kontrakt między platformą Azure i punktem końcowym. Dostawcy niestandardowi mogą zmieniać przepływy pracy na platformie Azure. W tym samouczku przedstawiono proces tworzenia niestandardowego dostawcy. Jeśli nie znasz dostawców niestandardowych platformy Azure, zobacz [Omówienie niestandardowych dostawców zasobów platformy Azure](overview.md).

## <a name="create-a-custom-provider"></a>Tworzenie niestandardowego dostawcy

> [!NOTE]
> Ten samouczek nie pokazuje, jak utworzyć punkt końcowy. Jeśli nie masz punktu końcowego RESTFUL, postępuj zgodnie z [samouczkiem dotyczącym tworzenia punktów końcowych RESTful](./tutorial-custom-providers-function-authoring.md), który stanowi podstawę bieżącego samouczka.

Po utworzeniu punktu końcowego można utworzyć niestandardowego dostawcę w celu wygenerowania kontraktu między dostawcą i punktem końcowym. Za pomocą niestandardowego dostawcy można określić listę definicji punktów końcowych:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Właściwość | Wymagane | Opis
---|---|---
**Nazwij** | Tak | Nazwa definicji punktu końcowego. Platforma Azure udostępnia tę nazwę za pomocą interfejsu API w obszarze/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingtype** | Nie | Typ kontraktu punktu końcowego. Jeśli wartość nie jest określona, zostanie ona domyślnie ustawiona na "proxy".
**punktu końcowego** | Tak | Punkt końcowy, do którego będą kierowane żądania. Ten punkt końcowy obsługuje odpowiedź i wszystkie efekty uboczne żądania.

Wartość **punktu końcowego** jest WYZWALANYM adresem URL aplikacji funkcji platformy Azure. `<yourapp>` `<funcname>` `<functionkey>` Symbole zastępcze, i muszą zostać zastąpione wartościami dla utworzonej aplikacji funkcji.

## <a name="define-custom-actions-and-resources"></a>Definiowanie niestandardowych akcji i zasobów

Dostawca niestandardowy zawiera listę definicji punktów końcowych modelowanych w ramach **akcji** i właściwości **resourceTypes** . Właściwość **Actions** mapuje na akcje niestandardowe udostępniane przez niestandardowego dostawcę, a właściwość **resourceTypes** to zasoby niestandardowe. W tym samouczku Dostawca niestandardowy ma właściwość **Actions** o nazwie `myCustomAction` i Właściwość **resourceTypes** o nazwie `myCustomResources` .

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Wdróż dostawcę niestandardowego

> [!NOTE]
> Należy zastąpić wartości **punktu końcowego** identyfikatorem URL wyzwalacza z aplikacji funkcji utworzonej w poprzednim samouczku.

Poprzedni Dostawca niestandardowy można wdrożyć przy użyciu szablonu Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Korzystanie z niestandardowych akcji i zasobów

Po utworzeniu niestandardowego dostawcy można użyć nowych interfejsów API platformy Azure. Na poniższych kartach wyjaśniono, jak wywoływać dostawcę niestandardowego i korzystać z niego.

### <a name="custom-actions"></a>Akcje niestandardowe

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> Należy zastąpić `{subscriptionId}` `{resourceGroupName}` symbole zastępcze i grupę zasobów, w której został wdrożony niestandardowy dostawca.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametr | Wymagane | Opis
---|---|---
*transakcji* | Tak | Nazwa akcji zdefiniowanej w dostawcy niestandardowym
*identyfikatory* | Tak | Identyfikator zasobu dostawcy niestandardowego
*treść żądania* | Nie | Treść żądania, która zostanie wysłana do punktu końcowego

# <a name="template"></a>[Szablon](#tab/template)

Brak.

---

### <a name="custom-resources"></a>Zasoby niestandardowe

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> Należy zastąpić `{subscriptionId}` `{resourceGroupName}` symbole zastępcze i grupę zasobów, w której został wdrożony niestandardowy dostawca.

#### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parametr | Wymagane | Opis
---|---|---
*is-full-Object* | Tak | Wskazuje, czy obiekt właściwości zawiera inne opcje, takie jak lokalizacja, Tagi, jednostka SKU lub plan.
*id* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem niestandardowego identyfikatora zasobu dostawcy.
*aœciwoœci* | Tak | Treść żądania, która zostanie wysłana do punktu końcowego.

#### <a name="delete-a-custom-resource"></a>Usuwanie zasobu niestandardowego

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagane | Opis
---|---|---
*id* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem niestandardowego identyfikatora zasobu dostawcy.

#### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagane | Opis
---|---|---
*id* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem niestandardowego identyfikatora zasobu dostawcy.

# <a name="template"></a>[Szablon](#tab/template)

Przykładowy szablon Menedżer zasobów:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parametr | Wymagane | Opis
---|---|---
*resourceTypeName* | Tak | `name`Wartość właściwości **resourceTypes** zdefiniowanej w dostawcy niestandardowym.
*resourceProviderName* | Tak | Nazwa wystąpienia dostawcy niestandardowego.
*customResourceName* | Tak | Niestandardowa nazwa zasobu.

---

> [!NOTE]
> Po zakończeniu wdrażania i korzystania z dostawcy niestandardowego Pamiętaj o oczyszczeniu wszystkich utworzonych zasobów, w tym aplikacji funkcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o dostawcach niestandardowych. Aby uzyskać więcej informacji, zobacz:

- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)

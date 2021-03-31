---
title: Przegląd niestandardowych definicji ról
description: Opisuje koncepcję tworzenia niestandardowych definicji ról dla zarządzanych aplikacji.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391825"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definicji roli niestandardowej w Azure Managed Applications

Definicja roli niestandardowej to opcjonalny artefakt w aplikacjach zarządzanych. Służy do określania, jakie uprawnienia musi wykonać aplikacja zarządzana.

Ten artykuł zawiera omówienie artefaktu definicji roli niestandardowej i jego możliwości.

## <a name="custom-role-definition-artifact"></a>Artefakt definicji roli niestandardowej

Należy nazwać customRoleDefinition.jsartefaktu definicji roli niestandardowej. Umieść go na tym samym poziomie, co createUiDefinition.json i mainTemplate.jsw pakiecie. zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicję aplikacji zarządzanej, zobacz [Publikowanie definicji aplikacji zarządzanej.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Schemat definicji roli niestandardowej

customRoleDefinition.jsw pliku ma właściwość najwyższego poziomu `roles` , która jest tablicą ról. Role te są uprawnieniami, które muszą działać aplikacja zarządzana. Obecnie dozwolone są tylko wbudowane role, ale można określić wiele ról. Do roli może odwoływać się identyfikator definicji roli lub nazwa roli.

Przykładowy kod JSON dla definicji roli niestandardowej:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Role

Rola składa się z `$.properties.roleName` lub `id` :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Możesz użyć albo `id` `roleName` pola. Wymagany jest tylko jeden. Te pola są używane do wyszukiwania definicji roli, która powinna zostać zastosowana. W przypadku podania obu tych wartości `id` pole zostanie użyte.

|Właściwość|Wymagane?|Opis|
|---------|---------|---------|
|identyfikator|Tak|Identyfikator wbudowanej roli. Możesz użyć pełnego identyfikatora lub tylko identyfikatora GUID.|
|Role|Tak|Nazwa wbudowanej roli.|
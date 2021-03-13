---
title: Porównanie składni Azure Resource Manager szablonów w formacie JSON i Bicep
description: Porównuje Azure Resource Manager szablony opracowane za pomocą notacji JSON i Bicep oraz pokazują, jak konwertować między językami.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418049"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Porównanie JSON i Bicep dla szablonów

W tym artykule porównano składnię Bicep z składnią JSON dla szablonów Azure Resource Manager (szablony ARM). W większości przypadków Bicep zawiera składnię, która jest mniej pełna niż odpowiednik w formacie JSON.

## <a name="syntax-equivalents"></a>Odpowiedniki składni

Jeśli wiesz już, jak tworzyć szablony ARM przy użyciu formatu JSON, Skorzystaj z poniższej tabeli, aby dowiedzieć się więcej o równoważnej składni Bicep.

| Scenariusz | Bicep | JSON |
| -------- | ------------ | ----- |
| Tworzenie wyrażenia | `func()` | `"[func()]"` |
| Pobierz wartość parametru | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Pobierz wartość zmiennej | `exampleVar` | `[variables('exampleVar'))]` |
| Łączenie ciągów | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Ustaw właściwość zasobu | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Zwróć wartość logiczną i | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Pobierz identyfikator zasobu zasobu w szablonie | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Pobierz właściwość z zasobu w szablonie | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Warunkowo ustaw wartość | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Oddziel rozwiązanie do wielu plików | Korzystanie z modułów | Używanie połączonych szablonów |
| Ustawianie docelowego zakresu wdrożenia | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Ustaw zależność | Polegaj na automatycznym wykryciu zależności lub ręcznie ustaw zależność z `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Deklaracja zasobu | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Zalecenia

* Jeśli to możliwe, Unikaj używania funkcji [Reference](template-functions-resource.md#reference) i [ResourceID](template-functions-resource.md#resourceid) w pliku Bicep. W przypadku odwoływania się do zasobu w tym samym wdrożeniu Bicep zamiast tego użyj identyfikatora zasobu. Na przykład jeśli w pliku Bicep został wdrożony zasób z `stg` identyfikatorem zasobu, użyj składni podobnej do lub, `stg.id` `stg.properties.primaryEndpoints.blob` Aby uzyskać wartości właściwości. Korzystając z identyfikatora zasobu, można utworzyć niejawną zależność między zasobami. Nie musisz jawnie ustawiać zależności przy użyciu właściwości dependsOn.
* Użyj spójnej wielkości liter dla identyfikatorów. Jeśli nie masz pewności, jakiego typu użyta jest wielkość liter, wypróbuj notacji CamelCase wielkości liter. Na przykład `param myCamelCasedParameter string`.
* Dodaj opis do parametru tylko wtedy, gdy opis zawiera podstawowe informacje dla użytkowników. `//`Aby uzyskać pewne informacje, można użyć komentarzy.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat Bicep, zobacz [samouczek Bicep](./bicep-tutorial-create-first-bicep.md).
* Aby dowiedzieć się więcej o konwertowaniu szablonów między językami, zobacz [konwertowanie szablonów ARM między elementami JSON i Bicep](bicep-decompile.md).

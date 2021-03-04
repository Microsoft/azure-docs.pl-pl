---
title: Porównanie składni Azure Resource Manager szablonów w formacie JSON i Bicep
description: Porównuje Azure Resource Manager szablony opracowane za pomocą notacji JSON i Bicep oraz pokazują, jak konwertować między językami.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036218"
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

## <a name="decompile-json-to-bicep"></a>Dekompilowanie pliku JSON do Bicep

Interfejs wiersza polecenia Bicep udostępnia polecenie do dekompilowania istniejącego szablonu ARM do pliku Bicep. Aby dekompilować plik JSON, użyj: `bicep decompile "path/to/file.json"`

To polecenie udostępnia punkt początkowy dla tworzenia Bicep, ale polecenie nie działa dla wszystkich szablonów. Polecenie może się nie powieść lub może być konieczne naprawienie problemów po zakończeniu dekompilacji. Obecnie zagnieżdżone szablony mogą być dekompilowane tylko wtedy, gdy używają zakresu oceny wyrażenia "Inner".

Możesz wyeksportować szablon dla grupy zasobów, a następnie przekazać go bezpośrednio do polecenia dekompilowania Bicep. Poniższy przykład pokazuje, jak dekompilować wyeksportowany szablon.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Wyeksportuj szablon](export-template-portal.md) przy użyciu portalu. Użyj `bicep decompile <filename>` pobranego pliku.

---

## <a name="build-json-from-bicep"></a>Kompiluj kod JSON z Bicep

Interfejs wiersza polecenia Bicep udostępnia również polecenie konwersji Bicep na format JSON. Aby skompilować plik JSON, użyj: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Widok obok siebie

[Bicep plac zabaw](https://aka.ms/bicepdemo) umożliwia wyświetlanie odpowiedników plików JSON i Bicep obok siebie. Możesz wybrać przykładowy szablon, aby wyświetlić obie wersje. Można też wybrać opcję `Decompile` przekazania własnego szablonu JSON i wyświetlić odpowiedni plik Bicep.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat Bicep, zobacz [samouczek Bicep](./bicep-tutorial-create-first-bicep.md).

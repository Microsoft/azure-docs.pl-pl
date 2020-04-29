---
title: Błędy nieznalezienia zasobu
description: Opisuje sposób rozwiązywania błędów, gdy nie można znaleźć zasobu podczas wdrażania przy użyciu szablonu Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773258"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Nie znaleziono błędów dla zasobów platformy Azure

W tym artykule opisano błędy, które mogą pojawić się, gdy nie można znaleźć zasobu podczas wdrażania.

## <a name="symptom"></a>Objaw

Jeśli szablon zawiera nazwę zasobu, którego nie można rozpoznać, zostanie wyświetlony komunikat o błędzie podobny do:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Jeśli używasz funkcji [Reference](template-functions-resource.md#reference) lub [listKeys](template-functions-resource.md#listkeys) z zasobem, którego nie można rozpoznać, zostanie wyświetlony następujący błąd:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Przyczyna

Menedżer zasobów musi pobrać właściwości dla zasobu, ale nie może zidentyfikować zasobu w subskrypcji.

## <a name="solution-1---set-dependencies"></a>Rozwiązanie 1 — Ustawianie zależności

Jeśli próbujesz wdrożyć brakujący zasób w szablonie, sprawdź, czy musisz dodać zależność. Menedżer zasobów optymalizuje wdrożenie przez tworzenie zasobów równolegle, gdy jest to możliwe. Jeśli jeden zasób musi zostać wdrożony po innym zasobie, należy użyć elementu **dependsOn** w szablonie. Na przykład podczas wdrażania aplikacji sieci Web plan App Service musi istnieć. Jeśli nie określono, że aplikacja sieci Web zależy od planu App Service, Menedżer zasobów tworzy jednocześnie oba zasoby. Zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można odnaleźć zasobu planu App Service, ponieważ nie istnieje on jeszcze podczas próby ustawienia właściwości aplikacji sieci Web. Ten błąd można uniknąć, ustawiając zależność w aplikacji sieci Web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Jednak chcesz uniknąć ustawiania zależności, które nie są potrzebne. W przypadku niepotrzebnych zależności można przedłużyć czas trwania wdrożenia, zapobiegając niezależnym zasobom, które nie są od siebie wzajemnie wdrażane. Ponadto można utworzyć zależności cykliczne, które blokują wdrożenie. Funkcja [Reference](template-functions-resource.md#reference) i [list *](template-functions-resource.md#list) Functions tworzy niejawną zależność od zasobu, do którego istnieje odwołanie, gdy ten zasób jest wdrażany w tym samym szablonie i jest przywoływany przez jego nazwę (nie identyfikator zasobu). W związku z tym może być większa zależność od zależności określonych we właściwości **dependsOn** . Funkcja [ResourceID](template-functions-resource.md#resourceid) nie tworzy zależności niejawnej lub sprawdza, czy zasób istnieje. Funkcja [Reference](template-functions-resource.md#reference) i [Lista *](template-functions-resource.md#list) Functions nie tworzą niejawnej zależności, gdy zasób jest określany przez jego identyfikator zasobu. Aby utworzyć zależność niejawną, należy przekazać nazwę zasobu wdrożonego w tym samym szablonie.

Gdy widzisz problemy z zależnościami, musisz uzyskać wgląd w kolejność wdrażania zasobów. Aby wyświetlić kolejność operacji wdrażania:

1. Wybierz historię wdrożenia dla grupy zasobów.

   ![Wybierz historię wdrożenia](./media/error-not-found/select-deployment.png)

2. Wybierz wdrożenie z historii, a następnie wybierz pozycję **zdarzenia**.

   ![Wybieranie zdarzeń wdrożenia](./media/error-not-found/select-deployment-events.png)

3. Sprawdzanie sekwencji zdarzeń dla każdego zasobu. Zwróć uwagę na stan każdej operacji. Na przykład na poniższej ilustracji przedstawiono trzy konta magazynu, które zostały wdrożone równolegle. Należy zauważyć, że trzy konta magazynu są uruchamiane w tym samym czasie.

   ![wdrożenie równoległe](./media/error-not-found/deployment-events-parallel.png)

   Następny obraz przedstawia trzy konta magazynu, które nie są wdrażane równolegle. Drugie konto magazynu zależy od pierwszego konta magazynu, a trzecie konto magazynu zależy od drugiego konta magazynu. Pierwsze konto magazynu zostało uruchomione, zaakceptowane i ukończone przed rozpoczęciem następnego.

   ![wdrożenie sekwencyjne](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Rozwiązanie 2 — Pobieranie zasobu z innej grupy zasobów

Gdy zasób istnieje w innej grupie zasobów niż ta, w której jest wdrażana, użyj [funkcji ResourceID](template-functions-resource.md#resourceid) , aby uzyskać w pełni kwalifikowaną nazwę zasobu.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Rozwiązanie 3 — sprawdzanie funkcji referencyjnej

Wyszukaj wyrażenie zawierające funkcję [Reference](template-functions-resource.md#reference) . Wartości, które można określić, różnią się w zależności od tego, czy zasób znajduje się w tym samym szablonie, grupie zasobów i subskrypcji. Sprawdź dokładnie, czy podajesz wymagane wartości parametrów dla danego scenariusza. Jeśli zasób znajduje się w innej grupie zasobów, podaj pełny identyfikator zasobu. Na przykład, aby odwołać się do konta magazynu w innej grupie zasobów, należy użyć:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Rozwiązanie 4 — uzyskiwanie zarządzanej tożsamości z zasobu

W przypadku wdrażania zasobu, który niejawnie tworzy [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md), przed pobraniem wartości w zarządzanej tożsamości należy poczekać, aż ten zasób zostanie wdrożony. Jeśli nazwa tożsamości zarządzanej zostanie przekazana do funkcji [referencyjnej](template-functions-resource.md#reference) , Menedżer zasobów próbuje rozpoznać odwołanie przed wdrożeniem zasobu i tożsamości. Zamiast tego należy przekazać nazwę zasobu, do którego jest stosowana tożsamość. Takie podejście gwarantuje, że zasób i zarządzana tożsamość zostaną wdrożone przed Menedżer zasobów rozpozna funkcję Reference.

W funkcji Reference Użyj `Full` , aby uzyskać wszystkie właściwości, w tym zarządzaną tożsamość.

Na przykład aby uzyskać identyfikator dzierżawy dla tożsamości zarządzanej, która została zastosowana do zestawu skalowania maszyn wirtualnych, użyj:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```
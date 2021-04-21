---
title: Błędy nie znaleziono zasobu
description: Opisuje sposób rozwiązywania błędów, gdy nie można odnaleźć zasobu. Ten błąd może wystąpić podczas wdrażania szablonu Azure Resource Manager lub podczas podejmowania akcji zarządzania.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5e3a72eaad99721cec9500956179a3ae9d9cf8d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762141"
---
# <a name="resolve-resource-not-found-errors"></a>Rozwiązywanie błędów nie znaleziono zasobu

W tym artykule opisano błąd, który występuje, gdy nie można odnaleźć zasobu podczas operacji. Zazwyczaj ten błąd jest wyświetlany podczas wdrażania zasobów. Ten błąd jest również wyświetlany podczas wykonywania zadań zarządzania Azure Resource Manager nie można znaleźć wymaganego zasobu. Jeśli na przykład spróbujesz dodać tagi do zasobu, który nie istnieje, zostanie wyświetlony ten błąd.

## <a name="symptom"></a>Objaw

Istnieją dwa kody błędów, które wskazują, że nie można odnaleźć zasobu. Błąd **NotFound** zwraca wynik podobny do:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Błąd **ResourceNotFound** zwraca wynik podobny do:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Przyczyna

Resource Manager musi pobrać właściwości zasobu, ale nie może znaleźć zasobu w twoich subskrypcjach.

## <a name="solution-1---check-resource-properties"></a>Rozwiązanie 1 — sprawdzanie właściwości zasobów

Po otrzymaniu tego błędu podczas wykonywania zadania zarządzania sprawdź wartości podane dla zasobu. Trzy wartości do sprawdzenia to:

* Nazwa zasobu
* Nazwa grupy zasobów
* Subskrypcja

Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, sprawdź, czy uruchamiasz polecenie w subskrypcji zawierającej zasób. Możesz zmienić subskrypcję za pomocą [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) lub [az account set](/cli/azure/account#az_account_set). Wiele poleceń zapewnia również parametr subskrypcji, który umożliwia określenie innej subskrypcji niż bieżący kontekst.

Jeśli masz problemy ze sprawdzeniem właściwości, zaloguj się do [portalu](https://portal.azure.com). Znajdź zasób, który chcesz użyć, i sprawdź nazwę zasobu, grupę zasobów i subskrypcję.

## <a name="solution-2---set-dependencies"></a>Rozwiązanie 2 — ustawianie zależności

Jeśli ten błąd wystąpi podczas wdrażania szablonu, może być konieczne dodanie zależności. Resource Manager optymalizuje wdrożenie, tworząc zasoby równolegle, jeśli jest to możliwe. Jeśli jeden zasób musi zostać wdrożony po innym zasobie, musisz użyć elementu **dependsOn** w szablonie. Na przykład podczas wdrażania aplikacji internetowej musi istnieć App Service planu. Jeśli nie określono, że aplikacja internetowa zależy od planu App Service, Resource Manager tworzy oba zasoby w tym samym czasie. Pojawia się komunikat o błędzie informujący, że nie można odnaleźć zasobu planu App Service, ponieważ jeszcze nie istnieje podczas próby ustawienia właściwości w aplikacji internetowej. Ten błąd można zapobiec, ustawiając zależność w aplikacji internetowej.

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

Jednak chcesz uniknąć ustawiania zależności, które nie są potrzebne. Jeśli masz niepotrzebne zależności, wydłużasz czas trwania wdrożenia, uniemożliwiając równoległe wdrażanie zasobów, które nie są od siebie zależne. Ponadto można utworzyć zależności cykliczne, które blokują wdrożenie. Funkcja [referencyjna](template-functions-resource.md#reference) i funkcje [list*](template-functions-resource.md#list) tworzą niejawną zależność od zasobu, do których istnieje odwołanie, gdy ten zasób jest wdrażany w tym samym szablonie i jest przywołyny przez jego nazwę (a nie identyfikator zasobu). W związku z tym może mieć więcej zależności niż zależności określone we właściwości **dependsOn.** Funkcja [resourceId](template-functions-resource.md#resourceid) nie tworzy niejawnej zależności ani nie sprawdza, czy zasób istnieje. Funkcja [referencyjna](template-functions-resource.md#reference) i [funkcje list*](template-functions-resource.md#list) nie tworzą niejawnej zależności, gdy zasób jest określany za pomocą identyfikatora zasobu. Aby utworzyć niejawną zależność, przekaż nazwę zasobu wdrożonego w tym samym szablonie.

Gdy zobaczysz problemy z zależnościami, musisz uzyskać wgląd w kolejność wdrażania zasobów. Aby wyświetlić kolejność operacji wdrażania:

1. Wybierz historię wdrażania dla grupy zasobów.

   ![wybieranie historii wdrażania](./media/error-not-found/select-deployment.png)

2. Wybierz wdrożenie z historii, a następnie wybierz pozycję **Zdarzenia**.

   ![wybieranie zdarzeń wdrażania](./media/error-not-found/select-deployment-events.png)

3. Sprawdź sekwencję zdarzeń dla każdego zasobu. Zwróć uwagę na stan każdej operacji. Na przykład na poniższej ilustracji przedstawiono trzy konta magazynu wdrożone równolegle. Zwróć uwagę, że trzy konta magazynu są uruchomione w tym samym czasie.

   ![wdrożenie równoległe](./media/error-not-found/deployment-events-parallel.png)

   Następny obraz przedstawia trzy konta magazynu, które nie są wdrażane równolegle. Drugie konto magazynu zależy od pierwszego konta magazynu, a trzecie od drugiego konta magazynu. Pierwsze konto magazynu zostanie uruchomione, zaakceptowane i ukończone przed rozpoczęciem następnego.

   ![wdrożenie sekwencyjne](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Rozwiązanie 3 — uzyskiwanie zasobu zewnętrznego

W przypadku wdrażania szablonu i konieczności uzyskania zasobu, który istnieje w innej subskrypcji lub grupie zasobów, użyj [funkcji resourceId](template-functions-resource.md#resourceid). Ta funkcja zwraca wartość , aby uzyskać w pełni kwalifikowaną nazwę zasobu.

Parametry subskrypcji i grupy zasobów w funkcji resourceId są opcjonalne. Jeśli ich nie po udostępnisz, będą one domyślnie korzystać z bieżącej subskrypcji i grupy zasobów. Podczas pracy z zasobem w innej grupie zasobów lub subskrypcji upewnij się, że te wartości są podane.

Poniższy przykład pobiera identyfikator zasobu, który istnieje w innej grupie zasobów.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Rozwiązanie 4 — uzyskiwanie tożsamości zarządzanej z zasobu

Jeśli wdrażasz zasób, który niejawnie [](../../active-directory/managed-identities-azure-resources/overview.md)tworzy tożsamość zarządzaną, musisz poczekać na wdrożenie tego zasobu przed pobieraniem wartości w tożsamości zarządzanej. Jeśli przekażemy nazwę tożsamości [](template-functions-resource.md#reference) zarządzanej do funkcji referencyjnej, Resource Manager rozwiąże odwołanie przed wdrożeniem zasobu i tożsamości. Zamiast tego należy przekazać nazwę zasobu, do których jest stosowana tożsamość. Takie podejście zapewnia, że zasób i tożsamość zarządzana są wdrażane przed Resource Manager rozpoznawania funkcji referencyjnej.

W funkcji referencyjnej użyj funkcji , `Full` aby pobrać wszystkie właściwości, w tym tożsamość zarządzaną.

Wzorzec jest:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Nie używaj wzorca:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Szablon nie powiedzie się.

Aby na przykład uzyskać identyfikator podmiotu zabezpieczeń dla tożsamości zarządzanej, która jest stosowana do maszyny wirtualnej, użyj:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Aby uzyskać identyfikator dzierżawy tożsamości zarządzanej, która jest stosowana do zestawu skalowania maszyn wirtualnych, użyj:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Rozwiązanie 5 — sprawdzanie funkcji

Podczas wdrażania szablonu poszukaj wyrażeń, które używają funkcji [reference](template-functions-resource.md#reference) lub [listKeys.](template-functions-resource.md#listkeys) Podane wartości różnią się w zależności od tego, czy zasób znajduje się w tym samym szablonie, grupie zasobów i subskrypcji. Sprawdź, czy udostępniasz wymagane wartości parametrów dla swojego scenariusza. Jeśli zasób znajduje się w innej grupie zasobów, podaj pełny identyfikator zasobu. Aby na przykład odwołać się do konta magazynu w innej grupie zasobów, użyj:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Rozwiązanie 6 — po usunięciu zasobu

Po usunięciu zasobu może wystąpić krótki czas, gdy zasób nadal pojawia się w portalu, ale nie jest faktycznie dostępny. Jeśli wybierzesz zasób, zostanie wyświetlany komunikat o błędzie z komunikatem o tym, że zasób nie został znaleziony. Odśwież portal, aby uzyskać najnowszy widok.

Jeśli problem będzie się kontynuować po krótkim czasie oczekiwania, skontaktuj [się z pomocą techniczną](https://azure.microsoft.com/support/options/).

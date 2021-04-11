---
title: Błędy nieznalezienia zasobu
description: Opisuje sposób rozwiązywania błędów, gdy nie można znaleźć zasobu. Ten błąd może wystąpić podczas wdrażania szablonu Azure Resource Manager lub podczas wykonywania akcji związanych z zarządzaniem.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950879"
---
# <a name="resolve-resource-not-found-errors"></a>Rozpoznaj błędy nieznalezionego zasobu

W tym artykule opisano błąd, który pojawia się, gdy nie można znaleźć zasobu podczas operacji. Zazwyczaj ten błąd jest wyświetlany podczas wdrażania zasobów. Ten błąd jest również wyświetlany podczas wykonywania zadań zarządzania i Azure Resource Manager nie może znaleźć wymaganego zasobu. Na przykład, jeśli spróbujesz dodać tagi do zasobu, który nie istnieje, zostanie wyświetlony ten błąd.

## <a name="symptom"></a>Objaw

Istnieją dwa kody błędów wskazujące, że nie można znaleźć zasobu. Błąd **NOTFOUND** zwraca wynik podobny do:

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

Menedżer zasobów musi pobrać właściwości zasobu, ale nie może znaleźć zasobu w Twoich subskrypcjach.

## <a name="solution-1---check-resource-properties"></a>Rozwiązanie 1 — Sprawdzanie właściwości zasobów

Po otrzymaniu tego błędu podczas wykonywania zadania zarządzania Sprawdź wartości podane dla zasobu. Oto trzy wartości do sprawdzenia:

* Nazwa zasobu
* Nazwa grupy zasobów
* Subskrypcja

Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, sprawdź, czy jest uruchomione polecenie w subskrypcji zawierającej zasób. Subskrypcję można zmienić za pomocą opcji [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) lub [AZ Account Set](/cli/azure/account#az-account-set). Wiele poleceń udostępnia również parametr subskrypcji, który pozwala określić inną subskrypcję niż bieżący kontekst.

Jeśli masz problemy z weryfikacją właściwości, zaloguj się do [portalu](https://portal.azure.com). Znajdź zasób, którego próbujesz użyć, i Sprawdź nazwę zasobu, grupę zasobów i subskrypcję.

## <a name="solution-2---set-dependencies"></a>Rozwiązanie 2 — Ustawianie zależności

Jeśli ten błąd wystąpi podczas wdrażania szablonu, może być konieczne dodanie zależności. Menedżer zasobów optymalizuje wdrożenie przez tworzenie zasobów równolegle, gdy jest to możliwe. Jeśli jeden zasób musi zostać wdrożony po innym zasobie, należy użyć elementu **dependsOn** w szablonie. Na przykład podczas wdrażania aplikacji sieci Web plan App Service musi istnieć. Jeśli nie określono, że aplikacja sieci Web zależy od planu App Service, Menedżer zasobów tworzy jednocześnie oba zasoby. Zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można odnaleźć zasobu planu App Service, ponieważ nie istnieje on jeszcze podczas próby ustawienia właściwości aplikacji sieci Web. Ten błąd można uniknąć, ustawiając zależność w aplikacji sieci Web.

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

## <a name="solution-3---get-external-resource"></a>Rozwiązanie 3 — Pobieranie zasobu zewnętrznego

Podczas wdrażania szablonu i konieczności pobrania zasobu, który istnieje w innej subskrypcji lub grupie zasobów, należy użyć [funkcji ResourceID](template-functions-resource.md#resourceid). Ta funkcja zwraca do pobrania w pełni kwalifikowanej nazwy zasobu.

Parametry subskrypcji i grupy zasobów w funkcji resourceId są opcjonalne. Jeśli ich nie podano, domyślnie są to bieżąca subskrypcja i Grupa zasobów. Podczas pracy z zasobem w innej grupie zasobów lub subskrypcji upewnij się, że podane zostały te wartości.

Poniższy przykład pobiera identyfikator zasobu dla zasobu, który istnieje w innej grupie zasobów.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Rozwiązanie 4 — uzyskiwanie zarządzanej tożsamości z zasobu

W przypadku wdrażania zasobu, który niejawnie tworzy [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md), przed pobraniem wartości w zarządzanej tożsamości należy poczekać, aż ten zasób zostanie wdrożony. Jeśli nazwa tożsamości zarządzanej zostanie przekazana do funkcji [referencyjnej](template-functions-resource.md#reference) , Menedżer zasobów próbuje rozpoznać odwołanie przed wdrożeniem zasobu i tożsamości. Zamiast tego należy przekazać nazwę zasobu, do którego jest stosowana tożsamość. Takie podejście gwarantuje, że zasób i zarządzana tożsamość zostaną wdrożone przed Menedżer zasobów rozpozna funkcję Reference.

W funkcji Reference Użyj, `Full` Aby uzyskać wszystkie właściwości, w tym zarządzaną tożsamość.

Wzorzec to:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Nie używaj wzorca:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Szablon zakończy się niepowodzeniem.

Aby na przykład uzyskać identyfikator podmiotu zabezpieczeń dla tożsamości zarządzanej, która jest zastosowana do maszyny wirtualnej, należy użyć:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Lub, aby uzyskać identyfikator dzierżawy dla tożsamości zarządzanej, która została zastosowana do zestawu skalowania maszyn wirtualnych, użyj:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Rozwiązanie 5 — sprawdzanie funkcji

Podczas wdrażania szablonu należy poszukać wyrażeń, które używają funkcji [Reference](template-functions-resource.md#reference) lub [listKeys](template-functions-resource.md#listkeys) . Wartości, które można określić, różnią się w zależności od tego, czy zasób znajduje się w tym samym szablonie, grupie zasobów i subskrypcji. Sprawdź, czy podajesz wymagane wartości parametrów dla danego scenariusza. Jeśli zasób znajduje się w innej grupie zasobów, podaj pełny identyfikator zasobu. Na przykład, aby odwołać się do konta magazynu w innej grupie zasobów, należy użyć:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Rozwiązanie 6 — po usunięciu zasobu

Po usunięciu zasobu może wystąpić krótki czas, po którym zasób nadal pojawia się w portalu, ale nie jest w rzeczywistości dostępny. W przypadku wybrania zasobu zostanie wyświetlony komunikat o błędzie z informacją, że nie można odnaleźć zasobu. Odśwież Portal, aby uzyskać najnowszy widok.

Jeśli problem będzie się powtarzać po krótkim czasie oczekiwania, [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/).

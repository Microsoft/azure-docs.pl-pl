---
title: 'Szybki Start: Tworzenie zapytania udostępnionego z szablonami'
description: W tym przewodniku szybki start użyjesz szablonu Menedżer zasobów, aby utworzyć zapytanie udostępnione grafu zasobów, które zlicza maszyny wirtualne według systemu operacyjnego.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234332"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Szybki Start: Tworzenie zapytania udostępnionego przy użyciu szablonu Menedżer zasobów

Zapytania dotyczące wykresów zasobów można zapisać jako _zapytanie prywatne_ lub _udostępnione zapytanie_. Zapytanie prywatne jest zapisywane w profilu portalu użytkowników i nie jest widoczne dla innych użytkowników. Udostępnione zapytanie jest obiektem Menedżer zasobów, który może być współużytkowany przez uprawnienia i dostęp oparty na rolach. Udostępnione zapytanie zapewnia wspólne i spójne wykonywanie odnajdywania zasobów. Ten przewodnik Szybki Start używa szablonu Menedżer zasobów do utworzenia zapytania udostępnionego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-shared-query"></a>Utwórz zapytanie udostępnione

W tym przewodniku szybki start utworzysz zapytanie udostępnione o nazwie _Liczba maszyn wirtualnych według systemu operacyjnego_. Aby wypróbować to zapytanie w zestawie SDK lub w portalu z Eksploratorem grafów zasobów, zobacz [przykłady — liczba maszyn wirtualnych według typu systemu operacyjnego](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

Zasób zdefiniowany w szablonie to:

- [Microsoft. ResourceGraph/zapytania](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

> [!NOTE]
> Usługa Azure Resource Graph jest bezpłatna. Aby uzyskać więcej informacji, zobacz [Omówienie grafu zasobów platformy Azure](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do witryny Azure Portal i otworzyć szablon:

   [![Wdrażanie szablonu zasad na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, określ nazwę, a następnie wybierz przycisk **OK**. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa zapytania | Pozostaw wartość domyślną **Liczba maszyn wirtualnych według systemu operacyjnego**. |
   | Kod zapytania | Pozostaw wartość domyślną`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Opis zapytania | Pozostaw wartość domyślną **Ta kwerenda udostępniona zlicza wszystkie zasoby maszyn wirtualnych i podsumowuje je według typu systemu operacyjnego.** |
   | Wyrażam zgodę na powyższe warunki i postanowienia | Zaznaczenia |

1. Wybierz pozycję **Kup**.

Dodatkowe zasoby:

- Aby znaleźć więcej przykładów szablonów, zobacz [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołanie do szablonu, przejdź do pozycji [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Aby uzyskać informacje na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby uruchomić nowe udostępnione zapytanie, wykonaj następujące kroki:

1. Na pasku wyszukiwania portalu Wyszukaj **zapytanie dotyczące wykresów zasobów** i wybierz je.

1. Wybierz zapytanie udostępnione o nazwie **Liczba maszyn wirtualnych według systemu operacyjnego**, a następnie wybierz kartę **wyniki** na stronie **Przegląd** .

Alternatywnie można otworzyć udostępnione zapytanie z Eksploratora grafu zasobów:

1. Na pasku wyszukiwania portalu Wyszukaj pozycję **Eksplorator wykresu zasobów** i wybierz go.

1. Wybierz przycisk **Otwórz zapytanie** .

1. Zmień **Typ** na _zapytania udostępnione_. Jeśli na liście nie ma widocznej **liczby maszyn wirtualnych według systemu operacyjnego** , użyj pola filtru, aby ograniczyć wyniki. Gdy jest widoczne zapytanie udostępnione **Liczba maszyn wirtualnych według systemu operacyjnego** , wybierz jego nazwę.

1. Po załadowaniu zapytania wybierz przycisk **Uruchom zapytanie** . Wyniki są wyświetlane na karcie **wyniki** poniżej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone zapytanie udostępnione, wykonaj następujące kroki:

1. Na pasku wyszukiwania portalu Wyszukaj **zapytanie dotyczące wykresów zasobów** i wybierz je.

1. Ustaw pole wyboru obok zapytania udostępnionego o nazwie **Liczba maszyn wirtualnych według systemu operacyjnego**.

1. Wybierz przycisk **Usuń** w górnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zapytanie udostępnione grafu zasobów.

Aby dowiedzieć się więcej o udostępnionych zapytaniach, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Zarządzanie zapytaniami w Azure Portal](./tutorials/create-share-query.md)
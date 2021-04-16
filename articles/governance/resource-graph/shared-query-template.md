---
title: 'Szybki start: tworzenie zapytania udostępnionego za pomocą szablonów'
description: W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager arm do utworzenia Resource Graph udostępnionego, które zlicza maszyny wirtualne według systemu operacyjnego.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: ac736d4371531bb38a8cd2cf095acbdfbc7c08a1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535792"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Szybki start: tworzenie udostępnionego zapytania przy użyciu szablonu usługi ARM

Resource Graph zapytania można zapisać jako zapytanie _prywatne_ lub _udostępnione._ Prywatne zapytanie jest zapisywane w profilu portalu dla użytkowników indywidualnych i nie jest widoczne dla innych osób. Zapytanie udostępnione jest obiektem Resource Manager, który można udostępniać innym osobom za pośrednictwem uprawnień i dostępu opartego na rolach. Udostępnione zapytanie zapewnia typowe i spójne wykonywanie odnajdywania zasobów. W tym przewodniku Szybki start Azure Resource Manager szablonu usługi Arm do utworzenia zapytania udostępnionego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie szablonu usługi ARM w celu utworzenia zapytania udostępnionego na platformie Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

W tym przewodniku Szybki start utworzysz udostępnione zapytanie o nazwie _Count VMs by OS (Liczba maszyn wirtualnych według systemu operacyjnego)._ Aby wypróbować to zapytanie w zestawie SDK lub w portalu za pomocą programu Resource Graph Explorer, zobacz [Przykłady — zliczanie](./samples/starter.md#count-os)maszyn wirtualnych według typu systemu operacyjnego.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

Zasób zdefiniowany w szablonie to:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

> [!NOTE]
> Azure Resource Graph jest bezpłatna. Aby uzyskać więcej informacji, zobacz [Omówienie Azure Resource Graph](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do witryny Azure Portal i otworzyć szablon:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie szablonu usługi ARM w celu utworzenia zapytania udostępnionego na platformie Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz **pozycję Utwórz nową,** określ nazwę, a następnie wybierz przycisk **OK.** |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa zapytania | Pozostaw wartość domyślną: **Zlicz maszyny wirtualne według systemu operacyjnego.** |
   | Wykonywanie zapytania o kod | Pozostaw wartość domyślną: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Opis zapytania | Pozostaw wartość domyślną: to udostępnione zapytanie zlicza wszystkie zasoby maszyny wirtualnej i **podsumowuje według typu systemu operacyjnego.** |
   | Wyrażam zgodę na powyższe warunki i postanowienia | (Wybierz) |

1. Wybierz pozycję **Kup**.

Niektóre dodatkowe zasoby:

- Aby znaleźć więcej przykładowych szablonów, zobacz [Szablon szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)
- Aby wyświetlić odwołanie do szablonu, przejdź do tematu [Informacje o szablonach platformy Azure.](/azure/templates/microsoft.resourcegraph/allversions)
- Aby dowiedzieć się, jak opracowywać szablony arm, [zobacz Azure Resource Manager dokumentacji](../../azure-resource-manager/management/overview.md).
- Aby dowiedzieć się więcej na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji.](../../azure-resource-manager/templates/deploy-to-subscription.md)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby uruchomić nowe zapytanie udostępnione, wykonaj następujące kroki:

1. Na pasku wyszukiwania portalu wyszukaj odpowiednie **Resource Graph i** wybierz je.

1. Wybierz udostępnione zapytanie o nazwie **Count VMs by OS**(Liczba maszyn wirtualnych według systemu operacyjnego), a następnie wybierz **kartę Results (Wyniki)** na **stronie** Przegląd.

Udostępnione zapytanie można również otworzyć z Resource Graph Explorer:

1. Na pasku wyszukiwania portalu wyszukaj pozycję **Resource Graph Explorer i** wybierz ją.

1. Wybierz przycisk **Otwórz** zapytanie.

1. Zmień **typ na** Udostępnione _zapytania._ Jeśli nie widzisz listy **Liczba** maszyn wirtualnych według systemu operacyjnego, użyj pola filtru, aby ograniczyć wyniki. Gdy udostępnione **zapytanie Liczba maszyn wirtualnych według systemu** operacyjnego będzie widoczne, wybierz jego nazwę.

1. Po załadowaniu zapytania wybierz przycisk **Uruchom** zapytanie. Wyniki są wyświetlane na **karcie** Wyniki.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone zapytanie udostępnione, wykonaj następujące kroki:

1. Na pasku wyszukiwania portalu wyszukaj odpowiednie **Resource Graph i** wybierz je.

1. Ustaw pole wyboru obok udostępnionego zapytania o nazwie **Zlicz maszyny wirtualne według systemu operacyjnego.**

1. Wybierz przycisk **Usuń** u góry strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Resource Graph udostępnione.

Aby dowiedzieć się więcej o zapytaniach udostępnionych, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Zarządzanie zapytaniami w Azure Portal](./tutorials/create-share-query.md)

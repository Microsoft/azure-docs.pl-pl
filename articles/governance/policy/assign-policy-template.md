---
title: 'Szybki Start: nowe przypisanie zasad z szablonami'
description: W tym przewodniku szybki start utworzysz przypisanie zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu Azure Resource Manager (szablon ARM).
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: b61198fecf0c3a6ba9aad19abc60bc042b68c29c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096618"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu ARM

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych, przy użyciu szablonu Azure Resource Manager (szablon ARM). Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk umożliwiający wdrożenie szablonu ARM na potrzeby przypisywania Azure Policy do platformy Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz wbudowaną definicję zasad o nazwie _Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych_. Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](./samples/index.md).

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

Zasób zdefiniowany w szablonie to:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

> [!NOTE]
> Usługa Azure Policy jest bezpłatna. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do witryny Azure Portal i otworzyć szablon:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk umożliwiający wdrożenie szablonu ARM na potrzeby przypisywania Azure Policy do platformy Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, określ nazwę, a następnie wybierz przycisk **OK**. Na zrzucie ekranu nazwa grupy zasobów to _mypolicyquickstart \<Date in MMDD\> RG_. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa przypisania zasad | Określ nazwę przydziału zasad. Jeśli chcesz, możesz użyć wyświetlania definicji zasad. Na przykład _Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych_. |
   | Nazwa RG | Określ nazwę grupy zasobów, do której chcesz przypisać zasady. W tym przewodniku szybki start Użyj wartości domyślnej **[resourceName (). Name]**. **[resourceing ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** to funkcja szablonu, która pobiera grupę zasobów. |
   | Identyfikator definicji zasad | Określ **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-B460-a2d36003525a**. |
   | Wyrażam zgodę na powyższe warunki i postanowienia | Zaznaczenia |

1. Wybierz pozycję **Kup**.

Dodatkowe zasoby:

- Aby znaleźć więcej przykładów szablonów, zobacz [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołanie do szablonu, przejdź do pozycji [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.authorization/allversions).
- Aby dowiedzieć się, jak opracowywać szablony ARM, zobacz [dokumentację Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Aby uzyskać informacje na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Zrzut ekranu przedstawiający szczegóły zgodności na stronie zgodność zasad." border="false":::

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Aby uzyskać więcej informacji, zobacz [jak działa zgodność](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Kliknij prawym przyciskiem myszy _maszyny wirtualne inspekcji, które nie używają przypisania zasad dysków zarządzanych_ , i wybierz pozycję **Usuń przypisanie**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Zrzut ekranu przedstawiający korzystanie z menu kontekstowego, aby usunąć przypisanie ze strony zgodność." border="false":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przypisano wbudowaną definicję zasad do zakresu i ocenił swój raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
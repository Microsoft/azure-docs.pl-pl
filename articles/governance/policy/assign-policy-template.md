---
title: 'Szybki start: nowe przypisanie zasad z szablonami'
description: W tym przewodniku Szybki start można użyć szablonu Menedżera zasobów do utworzenia przypisania zasad w celu identyfikowania niezgodnych zasobów.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: ae6b2d151baaa904215639276c7fb74766810c6a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606602"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Szybki start: tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów przy użyciu szablonu Menedżera zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki start utworzysz przypisanie zasad i przypisać wbudowaną definicję zasad o nazwie _Maszyny wirtualne inspekcji, które nie używają dysków zarządzanych_. Aby uzyskać częściową listę dostępnych wbudowanych zasad, zobacz [przykłady zasad platformy Azure](./samples/index.md).

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

Zasób zdefiniowany w szablonie to:

- [Microsoft.Authorization/policyAssignments Microsoft.Authorization/policyAssignments Microsoft.Authorization/policyAssignments Microsoft.](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

> [!NOTE]
> Usługa Azure Policy jest bezpłatna. Aby uzyskać więcej informacji, zobacz [Omówienie zasad platformy Azure](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do witryny Azure Portal i otworzyć szablon:

   [![Wdrażanie szablonu zasad na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz **pozycję Utwórz nowy**, określ nazwę, a następnie wybierz przycisk **OK**. Na zrzucie ekranu nazwa grupy zasobów to _mypolicyquickstart\<Date in\>MMDD rg_. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa przypisania zasad | Określ nazwę przypisania zasad. W razie potrzeby można użyć wyświetlania definicji zasad. Na przykład **maszyny wirtualne inspekcji, które nie używają dysków zarządzanych**. |
   | Nazwa Rg | Określ nazwę grupy zasobów, do której chcesz przypisać zasadę. W tym przewodniku Szybki start użyj wartości domyślnej **[resourceGroup().name]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** to funkcja szablonu pobierająca grupę zasobów. |
   | Identyfikator definicji zasad | Określ **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Wyrażam zgodę na warunki określone powyżej | (Wybierz) |

1. Wybierz pozycję **Kup**.

Niektóre dodatkowe zasoby:

- Aby znaleźć więcej przykładowych szablonów, zobacz [Szablon szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołanie do szablonu, przejdź do [odwołania do szablonu platformy Azure](/azure/templates/microsoft.authorization/allversions).
- Aby dowiedzieć się, jak tworzyć szablony Usługi Resource Manager, zobacz [dokumentację usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Aby dowiedzieć się, jak rozwijać subskrypcję, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

![Strona omówienie zgodności zasad](./media/assign-policy-template/policy-compliance.png)

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Aby uzyskać więcej informacji, zobacz [Jak działa zgodność](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Kliknij prawym przyciskiem myszy **polecenie Inspekcja maszyn wirtualnych, które nie używają** przypisania zasad dysków zarządzanych i wybierz polecenie Usuń **przypisanie**.

   ![Usuwanie przypisania ze strony omówienie zgodności](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przypisano wbudowaną definicję zasad do zakresu i oceniono jego raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
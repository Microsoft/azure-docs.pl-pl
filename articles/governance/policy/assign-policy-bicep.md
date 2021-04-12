---
title: 'Szybki Start: nowe przypisanie zasad z plikiem Bicep (wersja zapoznawcza)'
description: W tym przewodniku szybki start utworzysz przypisanie zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu pliku Bicep (wersja zapoznawcza).
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223980"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu pliku Bicep

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych, przy użyciu pliku [Bicep (wersja zapoznawcza)](https://github.com/Azure/bicep) skompilowanego do szablonu Azure Resource Manager. Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w Azure Portal.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Przycisk umożliwiający wdrożenie szablonu ARM na potrzeby przypisywania Azure Policy do platformy Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Bicep wersja `0.3` lub nowsza. Jeśli nie masz jeszcze interfejsu wiersza polecenia Bicep lub potrzebujesz aktualizacji, zobacz [Install Bicep (wersja zapoznawcza)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Przejrzyj plik Bicep

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz wbudowaną definicję zasad o nazwie _Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](./samples/index.md).

Utwórz następujący plik Bicep jako `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

Zasób zdefiniowany w pliku to:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

> [!NOTE]
> Usługa Azure Policy jest bezpłatna. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](./overview.md).

Po zainstalowaniu interfejsu wiersza polecenia Bicep i utworzeniu pliku można wdrożyć plik Bicep z:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

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

1. Usuń `assignment.bicep` plik.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przypisano wbudowaną definicję zasad do zakresu i ocenił swój raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
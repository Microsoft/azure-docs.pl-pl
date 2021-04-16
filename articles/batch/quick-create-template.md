---
title: Przewodnik Szybki start platformy Azure — tworzenie konta usługi Batch — Azure Resource Manager szablonu
description: W tym przewodniku Szybki start pokazano, jak utworzyć konto usługi Batch przy użyciu szablonu usługi ARM.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: e3405e98e2753a308b6b34b392aa364a67f7fe5b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535580"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Szybki start: tworzenie konta usługi Batch przy użyciu szablonu usługi ARM

Konto usługi Batch jest potrzebne do tworzenia zasobów obliczeniowych (pul węzłów obliczeniowych) i zadań usługi Batch. Konto usługi Azure Storage można połączyć z kontem usługi Batch, co jest przydatne do wdrażania aplikacji oraz przechowywania danych wejściowych i wyjściowych dla większości rzeczywistych obciążeń. W tym przewodniku Szybki start pokazano, jak utworzyć konto usługi Batch, w tym magazyn, Azure Resource Manager szablonu usługi Arm. Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia związane z usługą Batch, co pozwoli na wypróbowanie tej usługi z bardziej realistycznymi obciążeniami na większą skalę.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć aktywną subskrypcję platformy Azure.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

- [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)tworzy konto magazynu.
- [Microsoft.Batch/batchAccounts:](/azure/templates/microsoft.batch/batchaccounts)tworzy konto usługi Batch.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Batch magazynu.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

   ![Resource Manager szablonu, tworzenie konta usługi Batch, wdrażanie portalu](media/quick-create-template/batch-template.png)

   - **Subskrypcja**: wybierz subskrypcję platformy Azure.
   - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK.**
   - **Lokalizacja**: wybierz lokalizację. Na przykład **Środkowe stany USA**.
   - **Nazwa konta usługi Batch:** pozostaw wartość domyślną.
   - **Konta magazynuku:** wybierz typ konta magazynu. Na przykład **Standard_LRS**.
   - **Lokalizacja:** pozostaw wartość domyślną, aby zasoby będą w tej samej lokalizacji co grupa zasobów.
   - Wyrażam zgodę na powyższe warunki i postanowienia: **Wybierz pozycję**.

1. Wybierz pozycję **Kup**.

Po kilku minutach powinno zostać wyświetlony komunikat z powiadomieniem, że konto usługi Batch zostało pomyślnie utworzone.

W tym przykładzie Azure Portal jest używany do wdrożenia szablonu. Oprócz interfejsu Azure Portal można również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Możesz zweryfikować wdrożenie w Azure Portal, przechodząc do utworzonej grupy zasobów. Na **ekranie Przegląd** upewnij się, że konto usługi Batch i konto magazynu są obecne.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi [samouczkami,](./tutorial-parallel-dotnet.md)możesz pozostawić te zasoby na miejscu. Jeśli nie są już potrzebne, możesz usunąć grupę zasobów [,](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)co spowoduje również usunięcie utworzonego konta usługi Batch i utworzonego konta magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Batch i konto magazynu. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)

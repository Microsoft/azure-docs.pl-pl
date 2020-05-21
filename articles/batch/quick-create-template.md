---
title: Przewodnik Szybki Start platformy Azure — Tworzenie konta usługi Batch — szablon Azure Resource Manager
description: Szybko naucz się uruchamiać zadanie usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure. Tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów.
ms.topic: quickstart
ms.date: 05/19/2020
ms.custom: subject-armqs
ms.openlocfilehash: 535c8c34ea7af8e6bc56c3ecfe564de4c1b2bc54
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694229"
---
# <a name="quickstart-create-a-batch-account-by-using-azure-resource-manager-template"></a>Szybki Start: Tworzenie konta usługi Batch przy użyciu szablonu Azure Resource Manager

Do tworzenia zasobów obliczeniowych (pul węzłów obliczeniowych) i zadań wsadowych jest potrzebne konto w usłudze Batch. Konto magazynu platformy Azure można połączyć z kontem usługi Batch, co jest przydatne do wdrażania aplikacji i przechowywania danych wejściowych i wyjściowych dla większości obciążeń rzeczywistych.

Ten przewodnik Szybki Start przedstawia sposób tworzenia konta w usłudze Batch, w tym magazynu, przy użyciu szablonu Azure Resource Manager. Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia związane z usługą Batch, co pozwoli na wypróbowanie tej usługi z bardziej realistycznymi obciążeniami na większą skalę.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć aktywną subskrypcję platformy Azure.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-batchaccount-with-storage).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

- [Microsoft. Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): tworzy konto magazynu.
- [Microsoft. Batch/batchAccounts](https://docs.microsoft.com/azure/templates/microsoft.batch/batchaccounts): tworzy konto w usłudze Batch.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Batch i konto magazynu.

   [![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

   ![Szablon Menedżer zasobów, tworzenie konta w usłudze Batch, Portal wdrożenia](media/quick-create-template/batch-template.png)

   - **Subskrypcja**: wybierz subskrypcję platformy Azure.
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
   - **Lokalizacja**: wybierz lokalizację. Na przykład **Środkowe stany USA**.
   - **Nazwa konta**w usłudze Batch: pozostaw wartość domyślną.
   - **Accountsku magazynu**: Wybierz typ konta magazynu. Na przykład **Standard_LRS**.
   - **Lokalizacja**: pozostaw wartość domyślną, aby zasoby były w tej samej lokalizacji co grupa zasobów.
   - Wyrażam zgodę na powyższe warunki i postanowienia: **SELECT**.

1. Wybierz pozycję **Kup**.

Po kilku minutach zobaczysz powiadomienie o pomyślnym utworzeniu konta w usłudze Batch.

W tym przykładzie Azure Portal jest używany do wdrożenia szablonu. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Możesz sprawdzić poprawność wdrożenia w Azure Portal, przechodząc do utworzonej grupy zasobów. Na ekranie **Przegląd** Sprawdź, czy konto usługi Batch i konto magazynu są obecne.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi [samouczkami](./tutorial-parallel-dotnet.md), możesz pozostawić te zasoby na miejscu. Lub, jeśli nie są już potrzebne, można [usunąć grupę zasobów](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), która spowoduje również usunięcie utworzonego konta programu Batch i konta magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto magazynu wsadowego i konto magazyn. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)

---
title: Eksportowanie szablonu Azure Resource Manager zadania Azure Stream Analytics
description: W tym artykule opisano sposób eksportowania szablonu Azure Resource Manager dla zadania Azure Stream Analytics.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015423"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Eksportowanie szablonu Azure Resource Manager zadania Azure Stream Analytics

[Szablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) umożliwiają wdrożenie infrastruktury jako kodu. Szablon jest plikiem JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację dla zasobów. Należy określić zasoby do wdrożenia i właściwości dla tych zasobów.

Możesz ponownie wdrożyć zadanie Azure Stream Analytics, eksportując szablon Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Otwórz zadanie w VS Code

Przed wyeksportowaniem szablonu należy najpierw otworzyć istniejące zadanie Stream Analytics w Visual Studio Code. 

Aby wyeksportować zadanie do projektu lokalnego, zlokalizuj zadanie, które chcesz wyeksportować w **eksploratorze Stream Analytics** w Azure Portal. Na stronie **zapytanie** wybierz pozycję **Otwórz w programie Visual Studio**. Następnie wybierz pozycję **Visual Studio Code**.

![Otwórz zadanie Stream Analytics w Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Aby uzyskać więcej informacji na temat korzystania z Visual Studio Code do zarządzania zadaniami Stream Analytics, zobacz [Przewodnik Szybki Start](quick-create-visual-studio-code.md)dotyczący usługi Visual Studio Code.

## <a name="compile-the-script"></a>Kompilowanie skryptu 

Następnym krokiem jest skompilowanie skryptu zadania do szablonu Azure Resource Manager. Przed skompilowaniem skryptu upewnij się, że dla zadania wprowadzono co najmniej jedno wejście i jedno wyjście. Jeśli nie skonfigurowano danych wejściowych lub wyjściowych, musisz najpierw skonfigurować dane wejściowe i wyjściowe.

1. W Visual Studio Code przejdź do pliku *transformacji. asaql* zadania.

   ![Plik transformacji. asaql w Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Kliknij prawym przyciskiem myszy plik *transformacji. asaql* i wybierz pozycję **ASA: Kompiluj skrypt** z menu.

1. Zwróć uwagę, że folder **Deploy** pojawia się w obszarze roboczym zadania Stream Analytics.

1. Zapoznaj się *JobTemplate.js* pliku, który jest szablonem usługi Azure Resource Management używanym do wdrożenia programu.

## <a name="complete-the-parameters-file"></a>Ukończ plik parametrów

Następnie uzupełnij plik parametrów szablonu zarządzania zasobami platformy Azure.

1. Otwórz *JobTemplate.parameters.jsw* pliku znajdującym się w folderze **Deploy** w obszarze roboczym zadania Stream Analytics w Visual Studio Code.

1. Zauważ, że klucze wejściowe i wyjściowe mają wartość null. Zamień wartości null na rzeczywiste klucze dostępu dla zasobów wejściowych i wyjściowych.

1. Zapisz plik parametrów.

## <a name="deploy-using-templates"></a>Wdrażanie przy użyciu szablonów

Możesz przystąpić do wdrażania zadania Azure Stream Analytics przy użyciu szablonów Azure Resource Manager wygenerowanych w poprzedniej sekcji.

W oknie programu PowerShell uruchom następujące polecenie. Pamiętaj, aby reaplce *ResourceGroupName*, *TemplateFile* i *TemplateParameterFile* z rzeczywistą nazwą grupy zasobów i pełną ścieżkę pliku do *JobTemplate.js* i *JobTemplate.parameters.jsna* plikach w **folderze Deploy** w obszarze roboczym zadania.

Jeśli nie masz skonfigurowanego Azure PowerShell, wykonaj kroki opisane w temacie [Install Azure PowerShell module](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Następne kroki

* [Przetestuj Azure Stream Analytics zadania lokalnie z danymi wejściowymi na żywo przy użyciu Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Eksplorowanie Azure Stream Analytics zadań z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)
---
title: Samouczek — Tworzenie obszaru roboczego usługi Azure ML — szablon Menedżer zasobów
description: W tym samouczku użyjesz szablonu Azure Resource Manager, aby szybko wdrożyć obszar roboczy platformy Azure na potrzeby uczenia maszynowego
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 098d82e6521a4a355ac31809937b589f984816f2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027175"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Samouczek: wdrażanie obszaru roboczego usługi Azure Machine Learning przy użyciu szablonu ARM

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku przedstawiono sposób tworzenia obszaru roboczego usługi Azure Machine Learning przy użyciu szablonu Azure Resource Manager (szablon ARM). Obszary robocze usługi Azure Machine Learning organizują wszystkie zasoby usługi Machine Learning z bazowych zestawów danych do wdrożonych modeli. Obszary robocze to pojedyncze miejsce do współpracy z współpracownikami nad tworzeniem, uruchamianiem i przeglądaniem eksperymentów, zarządzaniem szkoleniami i inferencing zasobów obliczeniowych oraz monitorowaniem i wdrożonymi wersjami modeli.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów usługi ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/services/machine-learning/).

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft. MachineLearningServices/obszary robocze](/azure/templates/microsoft.machinelearningservices/workspaces): Tworzenie obszaru roboczego usługi Azure ml. W tym szablonie lokalizacja i nazwa są parametrami, które użytkownik może przekazać lub interaktywnie wprowadzić.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby użyć szablonu w interfejsie wiersza polecenia platformy Azure, zaloguj się i wybierz swoją subskrypcję (zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)). Następnie uruchom polecenie:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location &&
echo "Press [ENTER] to continue ..." &&
read
```

Po uruchomieniu powyższego polecenia wpisz:

1. Nazwa projektu, która będzie stanowić podstawę nazw utworzonej grupy zasobów i obszaru roboczego usługi Azure ML.
1. Lokalizacja platformy Azure, w której ma zostać wdrożone wdrożenie.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Aby wyświetlić obszar roboczy usługi Azure ML:

1. Przejdź do witryny https://portal.azure.com.
1. Zaloguj się.
1. Wybierz właśnie utworzony obszar roboczy.

Zobaczysz stronę główną Azure Machine Learning:

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Zrzut ekranu przedstawiający obszar roboczy usługi Azure ML":::

Aby wyświetlić wszystkie zasoby skojarzone z wdrożeniem, kliknij link w lewym górnym rogu z nazwą obszaru roboczego (na zrzucie ekranu `my_templated_ws` ). Ten link spowoduje przejście do grupy zasobów w Azure Portal. Nazwa grupy zasobów to `{projectName}rg` i obszar roboczy o nazwie `{projectName}ws` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz używać tego obszaru roboczego, usuń go. Ponieważ obszar roboczy jest skojarzony z innymi zasobami, takimi jak konto magazynu, prawdopodobnie chcesz usunąć całą utworzoną grupę zasobów. Aby usunąć grupę zasobów za pomocą portalu, kliknij przycisk **Usuń** i potwierdź. Można też usunąć grupę zasobów z interfejsu wiersza polecenia przy użyciu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz obszar roboczy Azure Machine Learning z szablonu ARM. Jeśli chcesz eksplorować Azure Machine Learning, Kontynuuj pracę z samouczkiem.

> [!div class="nextstepaction"]
> [Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu z użyciem zestawu SDK języka Python](tutorial-1st-experiment-sdk-setup.md)

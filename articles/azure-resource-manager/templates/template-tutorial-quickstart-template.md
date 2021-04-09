---
title: Samouczek — korzystanie z szablonów szybkiego startu
description: Dowiedz się, jak korzystać z szablonów szybkiego startu platformy Azure, aby ukończyć tworzenie szablonu.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106907"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Samouczek: korzystanie z szablonów szybkiego startu platformy Azure

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium szablonów utworzonych przez społeczność. Możesz użyć przykładowych szablonów w opracowywaniu szablonu. W tym samouczku znajdziesz definicję zasobu witryny sieci Web i dodasz ją do własnego szablonu. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego wyeksportowanych szablonów](template-tutorial-export-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Ten szablon działa w przypadku wdrażania kont magazynu i planów usługi App Service, ale może być konieczne dodanie do niej witryny sieci Web. Możesz użyć wstępnie skompilowanych szablonów, aby szybko wykryć kod JSON wymagany do wdrożenia zasobu.

## <a name="find-template"></a>Znajdź szablon

1. Otwórz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)
1. W obszarze **wyszukiwania** wprowadź polecenie _Wdróż aplikację sieci Web systemu Linux_.
1. Wybierz kafelek z tytułem **Wdróż podstawową aplikację sieci Web systemu Linux**. Jeśli masz problemy z znalezieniem go, Oto [link bezpośredni](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Wybierz pozycję **Przeglądaj w witrynie GitHub**.
1. Wybierz _azuredeploy.js_.
1. Zapoznaj się z szablonem. W szczególności poszukaj `Microsoft.Web/sites` zasobu.

    ![Witryna sieci Web szybkiego startu szablonu Menedżer zasobów](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Popraw istniejący szablon

Scal szablon szybkiego startu z istniejącym szablonem:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Nazwa aplikacji sieci Web musi być unikatowa na platformie Azure. Aby zapobiec duplikowaniu nazw, `webAppPortalName` zmienna została zaktualizowana z `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` do `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Dodaj przecinek na końcu `Microsoft.Web/serverfarms` definicji, aby oddzielić definicję zasobu z `Microsoft.Web/sites` definicji.

Istnieje kilka ważnych funkcji, które należy wziąć pod uwagę w tym nowym zasobie.

Zobaczysz, że zawiera on element o nazwie `dependsOn` , który jest ustawiony na plan usługi App Service. To ustawienie jest wymagane, ponieważ musi istnieć plan usługi App Service przed utworzeniem aplikacji sieci Web. `dependsOn`Element informuje Menedżer zasobów sposobu porządkowania zasobów do wdrożenia.

`serverFarmId`Właściwość używa funkcji [ResourceID](template-functions-resource.md#resourceid) . Ta funkcja pobiera unikatowy identyfikator zasobu. W takim przypadku pobiera unikatowy identyfikator planu usługi App Service. Aplikacja sieci Web jest skojarzona z jednym określonym planem usługi App Service.

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać szablonu szybkiego startu do tworzenia szablonu. W następnym samouczku dodasz Tagi do zasobów.

> [!div class="nextstepaction"]
> [Dodawanie tagów](template-tutorial-add-tags.md)

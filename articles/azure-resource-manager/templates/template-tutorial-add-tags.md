---
title: Samouczek — Dodawanie tagów do zasobów w szablonie
description: Dodawanie tagów do zasobów wdrażanych w szablonie Azure Resource Manager (szablon ARM). Tagi pozwalają logicznie organizować zasoby.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107009"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Samouczek: Dodawanie tagów w szablonie ARM

W tym samouczku dowiesz się, jak dodać tagi do zasobów w szablonie Azure Resource Manager (szablon ARM). [Tagi](../management/tag-resources.md) ułatwiają logiczne organizowanie zasobów. Wartości tagów są wyświetlane w raportach kosztów. Ten samouczek trwa **8 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego szablonów szybkiego startu](template-tutorial-quickstart-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Poprzedni szablon został wdrożony, konto magazynu, plan App Service i aplikacja internetowa.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Po wdrożeniu tych zasobów może być konieczne śledzenie kosztów i znajdowanie zasobów należących do kategorii. Możesz dodać tagi, aby pomóc w rozwiązaniu tych problemów.

## <a name="add-tags"></a>Dodawanie tagów

Zasoby taguje się, aby dodać wartości, które ułatwiają identyfikację ich zastosowania. Na przykład można dodać tagi, które wyświetlają środowisko i projekt. Można dodać tagi, które identyfikują centrum kosztów lub zespół, który jest właścicielem zasobu. Dodaj dowolne wartości, które są istotne dla Twojej organizacji.

Poniższy przykład wyróżnia zmiany w szablonie. Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Czas na wdrożenie szablonu i sprawdzenie wyników.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono `templateFile` zmienną na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Wybierz jeden z zasobów, na przykład zasób konta magazynu. Zobaczysz, że ma teraz Tagi.

   ![Pokaż Tagi](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano znaczniki do zasobów. W następnym samouczku dowiesz się, jak używać plików parametrów, aby uprościć przekazywanie wartości do szablonu.

> [!div class="nextstepaction"]
> [Użyj pliku parametrów](template-tutorial-use-parameter-file.md)

---
title: Samouczek — Dodawanie parametrów do szablonu
description: Dodaj parametry do szablonu Azure Resource Manager (szablon ARM), aby umożliwić jego wielokrotne użycie.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107066"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Samouczek: Dodawanie parametrów do szablonu ARM

W [poprzednim samouczku](template-tutorial-add-resource.md)przedstawiono sposób dodawania konta magazynu do szablonu i wdrażania go. W tym samouczku dowiesz się, jak ulepszyć szablon Azure Resource Manager (szablon ARM) przez dodanie parametrów. Ukończenie tego samouczka zajmuje około **14 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego zasobów](template-tutorial-add-resource.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Być może zauważono, że wystąpił problem z tym szablonem. Nazwa konta magazynu jest zapisana w kodzie na stałe. Każdorazowe użycie tego szablonu spowoduje wdrożenie tego samego konta magazynu. Aby wdrożyć konto magazynu o innej nazwie, należy utworzyć nowy szablon, który oczywiście nie jest praktycznym sposobem automatyzacji wdrożeń.

## <a name="make-template-reusable"></a>Tworzenie szablonu do ponownego użycia

Aby można było ponownie używać szablonu, dodajmy parametr, który służy do przekazywania nazwy konta magazynu. Wyróżniony kod JSON w poniższym przykładzie pokazuje, co zmieniło się w szablonie. `storageName`Parametr jest identyfikowany jako ciąg. Maksymalna długość jest równa 24 znaków, aby zapobiec nazwom, które są zbyt długie.

Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Wdróżmy szablon. Poniższy przykład wdraża szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Zwróć uwagę, że podajesz nazwę konta magazynu jako jedną z wartości w poleceniu wdrożenia. Podaj nazwę konta magazynu o tej samej nazwie, która została użyta w poprzednim samouczku.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono `templateFile` zmienną na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Omówienie aktualizacji zasobów

W poprzedniej sekcji zostało wdrożone konto magazynu o tej samej nazwie, która została wcześniej utworzona. Możesz zastanawiać się, w jaki sposób zasób ma wpływ na ponowne wdrożenie.

Jeśli zasób już istnieje i żadna zmiana nie zostanie wykryta we właściwościach, nie jest podejmowana żadna akcja. Jeśli zasób już istnieje, ale dokonano zmiany właściwości, zasób zostanie zaktualizowany. Jeśli zasób nie istnieje, zostanie utworzony.

Ten sposób obsługi aktualizacji oznacza, że szablon może obejmować wszystkie zasoby potrzebne do rozwiązania platformy Azure. Możesz bezpiecznie ponownie wdrożyć szablon i wiedzieć, że zasoby są zmieniane lub tworzone tylko w razie konieczności. Na przykład jeśli dodano pliki do konta magazynu, można ponownie wdrożyć konto magazynu bez utraty tych plików.

## <a name="customize-by-environment"></a>Dostosuj według środowiska

Parametry umożliwiają dostosowanie wdrożenia poprzez podanie wartości dopasowanych do danego środowiska. Na przykład można przekazać różne wartości w zależności od tego, czy wdrażasz do środowiska na potrzeby opracowywania, testowania i produkcji.

Poprzedni szablon zawsze wdrożono konto magazynu **Standard_LRS** . W zależności od środowiska może być konieczna elastyczność wdrażania różnych jednostek SKU. Poniższy przykład pokazuje zmiany w celu dodania parametru dla jednostki SKU. Skopiuj cały plik i wklej go nad szablonem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

`storageSKU`Parametr ma wartość domyślną. Ta wartość jest używana, gdy wartość nie jest określona podczas wdrażania. Zawiera również listę dozwolonych wartości. Te wartości są zgodne z wartościami, które są konieczne do utworzenia konta magazynu. Nie chcesz, aby użytkownicy szablonu mogli przekazać jednostki SKU, które nie działają.

## <a name="redeploy-template"></a>Ponowne wdrażanie szablonu

Wszystko jest gotowe do ponownego wdrożenia. Ponieważ domyślna jednostka SKU jest ustawiona na **Standard_LRS**, nie trzeba podawać wartości dla tego parametru.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

Aby sprawdzić elastyczność szablonu, wdróż ponownie. Tym razem ustaw parametr SKU na **Standard_GRS**. Możesz przekazać nową nazwę, aby utworzyć inne konto magazynu, lub użyć tej samej nazwy do zaktualizowania istniejącego konta magazynu. Obie opcje działają.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Na koniec Uruchommy jeszcze jeden test i zobacz, co się dzieje po przejściu do jednostki SKU, która nie jest jedną z dozwolonych wartości. W takim przypadku testuje scenariusz, w którym użytkownik szablonu traktuje **podstawową** , jest jedną z jednostek SKU.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Polecenie kończy się niepowodzeniem z komunikatem o błędzie z informacją, które wartości są dozwolone. Menedżer zasobów identyfikuje błąd przed rozpoczęciem wdrażania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Ulepszono szablon utworzony w [pierwszym samouczku](template-tutorial-create-first-template.md) przez dodanie parametrów. W następnym samouczku znajdziesz informacje na temat funkcji szablonu.

> [!div class="nextstepaction"]
> [Dodawanie funkcji szablonu](template-tutorial-add-functions.md)

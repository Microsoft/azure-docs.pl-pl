---
title: Samouczek — Dodawanie parametrów do pliku Azure Resource Manager Bicep
description: Dodaj parametry do pliku Bicep, aby umożliwić jego wielokrotne użycie.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632785"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Samouczek: Dodawanie parametrów do pliku Azure Resource Manager Bicep

W [poprzednim samouczku](bicep-tutorial-create-first-bicep.md)przedstawiono sposób wdrażania konta magazynu. W tym samouczku dowiesz się, jak ulepszyć plik Bicep przez dodanie parametrów. Ukończenie tego samouczka zajmuje około **14 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy zakończenie [tworzenia pierwszego pliku Bicep](bicep-tutorial-create-first-bicep.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka Bicep wygląda następująco:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Być może zauważono, że wystąpił problem z tym plikiem Bicep. Nazwa konta magazynu jest zapisana w kodzie na stałe. Tego pliku Bicep można użyć tylko do wdrożenia tego samego konta magazynu za każdym razem. Aby wdrożyć konto magazynu o innej nazwie, należy utworzyć nowy plik Bicep, który oczywiście nie jest praktycznym sposobem automatyzacji wdrożeń.

## <a name="make-bicep-file-reusable"></a>Utwórz plik Bicep do ponownego wykorzystania

Aby umożliwić wielokrotne użycie pliku Bicep, dodajmy parametr, którego można użyć do przekazania nazwy konta magazynu. Następujący plik Bicep pokazuje, co zmieniło się w pliku. `storageName`Parametr jest identyfikowany jako ciąg. Maksymalna długość jest równa 24 znaków, aby zapobiec nazwom, które są zbyt długie.

Skopiuj cały plik i zastąp go poniższą zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Zwróć uwagę, że parametry można przywoływać bezpośrednio przy użyciu ich nazw w Bicep, w porównaniu do wymagania [Parameters ("storagename")] w szablonie JSON usługi ARM.

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Wdróżmy plik Bicep. Poniższy przykład wdraża plik Bicep za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Zwróć uwagę, że podajesz nazwę konta magazynu jako jedną z wartości w poleceniu wdrożenia. Podaj nazwę konta magazynu o tej samej nazwie, która została użyta w poprzednim samouczku.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Omówienie aktualizacji zasobów

W poprzedniej sekcji zostało wdrożone konto magazynu o tej samej nazwie, która została wcześniej utworzona. Możesz zastanawiać się, w jaki sposób zasób ma wpływ na ponowne wdrożenie.

Jeśli zasób już istnieje i żadna zmiana nie zostanie wykryta we właściwościach, nie jest podejmowana żadna akcja. Jeśli zasób już istnieje, ale dokonano zmiany właściwości, zasób zostanie zaktualizowany. Jeśli zasób nie istnieje, zostanie utworzony.

Ten sposób obsługi aktualizacji oznacza, że plik Bicep może obejmować wszystkie zasoby potrzebne do rozwiązania platformy Azure. Można bezpiecznie ponownie wdrożyć plik Bicep i wiedzieć, że zasoby są zmieniane lub tworzone tylko w razie konieczności. Na przykład jeśli dodano pliki do konta magazynu, można ponownie wdrożyć konto magazynu bez utraty tych plików.

## <a name="customize-by-environment"></a>Dostosuj według środowiska

Parametry umożliwiają dostosowanie wdrożenia poprzez podanie wartości dopasowanych do danego środowiska. Na przykład można przekazać różne wartości w zależności od tego, czy wdrażasz do środowiska na potrzeby opracowywania, testowania i produkcji.

Poprzedni plik Bicep zawsze wdrożono konto magazynu **Standard_LRS** . W zależności od środowiska może być konieczna elastyczność wdrażania różnych jednostek SKU. Poniższy przykład pokazuje zmiany w celu dodania parametru dla jednostki SKU. Skopiuj cały plik i wklej go za pomocą pliku Bicep.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU`Parametr ma wartość domyślną. Ta wartość jest używana, gdy wartość nie jest określona podczas wdrażania. Zawiera również listę dozwolonych wartości. Te wartości są zgodne z wartościami, które są konieczne do utworzenia konta magazynu. Nie chcesz, aby użytkownicy tego pliku Bicep zachodziły z jednostek SKU, które nie działają.

## <a name="redeploy-bicep-file"></a>Wdróż ponownie plik Bicep

Wszystko jest gotowe do ponownego wdrożenia. Ponieważ domyślna jednostka SKU jest ustawiona na **Standard_LRS**, nie trzeba podawać wartości dla tego parametru.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

Aby zobaczyć elastyczność pliku Bicep, wdróż ponownie. Tym razem ustaw parametr SKU na **Standard_GRS**. Możesz przekazać nową nazwę, aby utworzyć inne konto magazynu, lub użyć tej samej nazwy do zaktualizowania istniejącego konta magazynu. Obie opcje działają.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Na koniec Uruchommy jeszcze jeden test i zobacz, co się dzieje po przejściu do jednostki SKU, która nie jest jedną z dozwolonych wartości. W takim przypadku będziemy testować scenariusz, w którym użytkownik pliku Bicep traktuje jako **podstawową** jedną z jednostek SKU.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Ulepszono plik Bicep utworzony w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md) przez dodanie parametrów. W następnym samouczku dowiesz się więcej na temat funkcji Bicep.

> [!div class="nextstepaction"]
> [Dodawanie funkcji](bicep-tutorial-add-functions.md)

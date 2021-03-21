---
title: Samouczek — Eksportowanie szablonu JSON z Azure Portal na potrzeby tworzenia Bicep
description: Dowiedz się, jak za pomocą wyeksportowanego szablonu JSON ukończyć programowanie Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632553"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Samouczek: korzystanie z wyeksportowanego szablonu JSON z Azure Portal

W tej serii samouczków utworzono plik Bicep w celu wdrożenia konta usługi Azure Storage. W następnych dwóch samouczkach dodasz *plan App Service* i *witrynę sieci Web*. Zamiast tworzyć szablony od podstaw, dowiesz się, jak eksportować szablony JSON z Azure Portal oraz jak używać przykładowych szablonów z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/). Te szablony można dostosować do własnych potrzeb. Ten samouczek koncentruje się na eksportowaniu szablonów i dostosowywaniu wyników dla pliku Bicep. Ukończenie może potrwać około **14 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego danych wyjściowych](bicep-tutorial-add-outputs.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Ten plik Bicep dobrze sprawdza się w przypadku wdrażania kont magazynu, ale warto dodać do niego więcej zasobów. Możesz wyeksportować szablon JSON z istniejącego zasobu, aby szybko uzyskać kod JSON dla tego zasobu. Następnie przekonwertuj kod JSON na Bicep, zanim będzie można go dodać do pliku Bicep.

## <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace** wprowadź **App Service plan**, a następnie wybierz pozycję **App Service plan**.  Nie wybieraj **planu App Service (klasyczny)**
1. Wybierz przycisk **Utwórz**.
1. Wprowadź:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** , a następnie określ nazwę. Podaj inną nazwę grupy zasobów niż ta, która była używana w tej serii samouczków.
    - **Nazwa**: Wprowadź nazwę planu usługi App Service.
    - **System operacyjny**: wybierz pozycję **Linux**.
    - **Region**: Wybierz lokalizację platformy Azure. Na przykład **Środkowe stany USA**.
    - **Warstwa cenowa**: aby zaoszczędzić koszty, Zmień jednostkę SKU na **podstawową B1** (w obszarze Tworzenie/testowanie).

    ![Portal szablonu Menedżer zasobów szablonu eksportu](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Wybierz pozycję **Przejrzyj i Utwórz**.
1. Wybierz przycisk **Utwórz**. Utworzenie zasobu trwa kilka chwil.

## <a name="export-template"></a>Eksportowanie szablonu

Obecnie Azure Portal obsługuje tylko eksportowanie szablonów JSON. Istnieją narzędzia, których można użyć do dekompilowania szablonów JSON do plików Bicep.

1. Wybierz pozycję **Przejdź do zasobu**.

    ![Przechodzenie do zasobu](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Wybierz pozycję **Eksportuj szablon**.

    ![Szablon eksportu Menedżer zasobów szablonu](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   Funkcja eksportowania szablonu Pobiera bieżący stan zasobu i generuje szablon w celu jego wdrożenia. Eksportowanie szablonu może być przydatnym sposobem na szybkie pobranie kodu JSON potrzebnego do wdrożenia zasobu.

1. `Microsoft.Web/serverfarms`Definicje i definicje parametrów są potrzebnymi elementami.

    ![Szablon wyeksportowanego szablonu Menedżer zasobów szablonu eksportu](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Zwykle wyeksportowany szablon jest bardziej szczegółowy niż podczas tworzenia szablonu. Na przykład obiekt SKU w wyeksportowanym szablonie ma pięć właściwości. Ten szablon działa, ale można tylko użyć `name` właściwości. Możesz rozpocząć od wyeksportowanego szablonu, a następnie zmodyfikować go w taki sposób, aby odpowiadał Twoim wymaganiom.

1. Kliknij pozycję **Pobierz**.  Pobrany plik zip zawiera **template.jsna** i **parameters.js**. Rozpakuj pliki.
1. Przejdź do **https://bicepdemo.z22.web.core.windows.net/** opcji Anuluj **kompilację**, a następnie otwórz **template.js**. Szablon jest pobierany w Bicep.

## <a name="revise-existing-bicep-file"></a>Popraw istniejący plik Bicep

Niezgodny szablon wyeksportowany daje większość potrzebnych Bicep, ale należy go dostosować do pliku Bicep. Należy zwrócić szczególną uwagę na różnice między parametrami i zmiennymi między plikiem Bicep a wyeksportowanym plikiem Bicep. Oczywiście proces eksportowania nie wie o parametry i zmienne, które zostały już zdefiniowane w pliku Bicep.

Poniższy przykład pokazuje Dodatki do pliku Bicep. Zawiera eksportowany kod i pewne zmiany. Najpierw zmienia nazwę parametru zgodnie z konwencją nazewnictwa. Następnie używa parametru Location dla lokalizacji planu usługi App Service. Po trzecie usuwa niektóre właściwości, w których wartość domyślna jest dobra.

Skopiuj cały plik i Zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć plik Bicep.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Grupa zasobów zawiera konto magazynu i plan App Service.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak wyeksportować szablon JSON z Azure Portal, jak przekonwertować szablon JSON na plik Bicep oraz jak używać wyeksportowanego szablonu do tworzenia Bicep. Możesz również użyć szablonów szybkiego startu platformy Azure, aby uprościć programowanie Bicep.

> [!div class="nextstepaction"]
> [Korzystanie z szablonów szybkiego startu platformy Azure](bicep-tutorial-quickstart-template.md)

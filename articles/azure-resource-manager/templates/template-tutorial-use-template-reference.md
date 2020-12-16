---
title: Używanie dokumentacji szablonu
description: Użyj odniesienia szablonu Azure Resource Manager (szablon ARM), aby utworzyć szablon.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584140"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Samouczek: korzystanie z odwołania do szablonu ARM

Dowiedz się, jak znaleźć informacje o schemacie szablonu, a następnie użyć tych informacji do tworzenia szablonów Azure Resource Manager (szablony ARM).

W tym samouczku użyjesz szablonu podstawowego z szablonów szybkiego startu platformy Azure. Korzystając z dokumentacji dotyczącej szablonów, można dostosować szablon.

![Odwołanie do szablonu Menedżer zasobów wdrożenie konta magazynu](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Informacje o szablonie
> * Znajdowanie dokumentacji szablonu
> * Edytowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to repozytorium szablonów usługi ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W obszarze Visual Studio Code wybierz pozycję **plik**  >  **Otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
1. Wybierz pozycję **plik**  >  **Zapisz jako,** aby zapisać plik jako _azuredeploy.jsna_ komputerze lokalnym.

## <a name="understand-the-schema"></a>Informacje o schemacie

1. W Visual Studio Code Zwiń szablon do poziomu głównego. Masz najprostszą strukturę z następującymi elementami:

    ![Najprostsza struktura szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: Określ lokalizację pliku schematu JSON opisującego wersję języka szablonów.
    * `contentVersion`: określ dowolną wartość dla tego elementu, aby udokumentować istotne zmiany w szablonie.
    * `parameters`: Określ wartości, które są dostarczane, gdy wdrożenie jest wykonywane w celu dostosowania wdrożenia zasobów.
    * `variables`: Określ wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonów.
    * `resources`: Określ typy zasobów wdrożone lub zaktualizowane w grupie zasobów.
    * `outputs`: Określ wartości, które są zwracane po wdrożeniu.

1. Rozwiń węzeł `resources` . `Microsoft.Storage/storageAccounts`Zdefiniowano zasób. Nazwa jednostki SKU używa wartości parametru. Parametr jest wywoływany `storageAccountType` .

    ![Definicja konta magazynu szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Rozwiń `parameters` , aby zobaczyć, jak `storageAccountType` jest zdefiniowany. Parametr ma cztery dozwolone wartości. Znajdziesz inne dozwolone wartości, a następnie Popraw definicję parametru.

    ![Jednostki SKU zasobów konta magazynu Menedżer zasobów szablonu](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Znajdowanie dokumentacji szablonu

1. Przejdź do [dokumentacji szablonu platformy Azure](/azure/templates/).
1. W polu **Filtruj według tytułu** wprowadź **konta magazynu** i wybierz pierwsze **konta magazynu** w obszarze **odwołanie > magazyn**.

    ![Konto magazynu odwołania do szablonu usługi Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Dostawca zasobów ma zwykle kilka wersji interfejsu API:

    ![Wersje kont magazynu odwołania do szablonu Menedżer zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. W okienku po lewej stronie wybierz pozycję **wszystkie zasoby** w obszarze **Magazyn** . Ta strona zawiera listę typów zasobów i wersje dostawcy zasobów magazynu. Zalecane jest korzystanie z najnowszych wersji interfejsu API dla typów zasobów zdefiniowanych w szablonie.

    ![Wersje typów kont magazynu odwołania do szablonu Menedżer zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Wybierz najnowszą wersję `storageAccount` typu zasobu. Najnowsza wersja to **2019-06-01** , gdy ten artykuł zostanie zapisany. Upewnij się, że ta wersja jest zgodna z wersją użytą dla zasobu konta magazynu w szablonie. W przypadku aktualizacji wersji interfejsu API Sprawdź, czy definicja zasobu jest zgodna z odwołaniem do szablonu.

1. Ta strona zawiera szczegółowe informacje o typie zasobu storageAccount. Na przykład wyświetla listę dozwolonych wartości dla **obiektu jednostki SKU**. Istnieje więcej jednostek SKU niż wymieniono w otwartym wcześniej szablonie szybkiego startu. Szablon szybkiego startu można dostosować w taki sposób, aby obejmował wszystkie dostępne typy magazynów.

    ![Jednostki SKU konta magazynu odwołania do szablonu Menedżer zasobów](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Edytowanie szablonu

W obszarze Visual Studio Code Dodaj dodatkowe typy kont magazynu, jak pokazano na poniższym zrzucie ekranu:

![Menedżer zasobów szablonu zasobów konta magazynu](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Zaloguj się do [Azure Cloud Shell](https://shell.azure.com)

1. Wybierz preferowane środowisko, wybierając opcję **PowerShell** lub **bash** (dla interfejsu wiersza polecenia) w lewym górnym rogu.  Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    ![Azure Portal Cloud Shell przekazywania pliku](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**. Zobacz poprzedni zrzut ekranu. Wybierz plik, który został zapisany w poprzedniej sekcji. Po `ls` przekazaniu pliku możesz użyć polecenia i `cat` polecenia, aby sprawdzić, czy plik został pomyślnie przekazany.

1. W Cloud Shell Uruchom następujące polecenia. Wybierz kartę, aby wyświetlić kod programu PowerShell lub kod interfejsu wiersza polecenia.

   Podczas wdrażania szablonu należy określić `storageAccountType` parametr z nowo dodaną wartością, na przykład **Standard_RAGRS**. Wdrożenie zakończy się niepowodzeniem, jeśli użyto oryginalnego szablonu szybkiego startu, ponieważ **Standard_RAGRS** nie jest dozwoloną wartością.

    # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
1. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
1. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia dokumentacji szablonu w celu dostosowania istniejącego szablonu. Aby dowiedzieć się, jak utworzyć wiele wystąpień konta magazynu, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie wielu wystąpień](./template-tutorial-create-multiple-instances.md)

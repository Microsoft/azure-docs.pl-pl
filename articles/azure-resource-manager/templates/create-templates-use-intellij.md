---
title: Wdróż szablon — pomysł IntelliJ
description: Dowiedz się, jak utworzyć pierwszy szablon Azure Resource Manager (szablon ARM), korzystając z POMYSŁu IntelliJ oraz jak go wdrożyć.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905965"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Tworzenie i wdrażanie szablonów ARM przy użyciu POMYSŁu IntelliJ

Dowiedz się, jak wdrożyć szablon Azure Resource Manager (szablon ARM) na platformie Azure przy użyciu POMYSŁu IntelliJ oraz proces edytowania i aktualizowania szablonu bezpośrednio z poziomu środowiska IDE. Szablony ARM to pliki JSON definiujące zasoby, które należy wdrożyć dla rozwiązania. Aby zrozumieć koncepcje związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Omówienie wdrażania szablonów](overview.md).

![Diagram portalu Menedżer zasobów Template](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Zainstalowane środowisko [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) w wersji Ultimate Edition lub Community Edition
* Zainstalowany zestaw narzędzi [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053). Aby uzyskać więcej informacji, zobacz [Przewodnik po zarządzaniu wtyczkami platformy IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html)
* Należy [być zalogowanym](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) do konta platformy Azure dla zestawu narzędzi Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Wdróż szablon szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi ARM. Szablon używany w tym artykule nazywa się [Tworzenie konta magazynu w warstwie Standardowa](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Definiuje zasób konta usługi Azure Storage.

1. Kliknij prawym przyciskiem myszy i Zapisz [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) i [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) na komputerze lokalnym.

1. Jeśli zestaw narzędzi platformy Azure został poprawnie zainstalowany i zalogowany, powinien zostać wyświetlony Eksplorator Azure na pasku bocznym POMYSŁu IntelliJ. Kliknij prawym przyciskiem myszy pozycję **Zarządzanie zasobami** i wybierz pozycję **Utwórz wdrożenie**.

    ![Szablon Menedżer zasobów kliknij prawym przyciskiem myszy, aby utworzyć wdrożenie](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Skonfiguruj **nazwę wdrożenia**, **subskrypcję**, **grupę zasobów** i **region**. Tutaj wdrażamy szablon w nowej grupie zasobów `testRG` . Następnie wybierz pozycję ścieżka dla **szablonu zasobu** jak `azuredeploy.json` i **parametry zasobu** podczas `azuredeploy.parameters.json` pobierania.

    ![Szablon Menedżer zasobów wybierz pliki do utworzenia wdrożenia](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po kliknięciu przycisku OK wdrożenie zostanie rozpoczęte. Do momentu ukończenia wdrożenia można znaleźć postęp z **paska stanu** pomysłu IntelliJ na dole.

    ![Stan wdrożenia szablonu Menedżer zasobów](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Przeglądanie istniejącego wdrożenia

1. Po zakończeniu wdrożenia zostanie wyświetlona nowa grupa zasobów `testRG` i nowe wdrożenie. Kliknij prawym przyciskiem myszy wdrożenie i zobaczysz listę możliwych akcji. Teraz wybierz pozycję **Pokaż właściwości**.

    ![Wdrożenie Menedżer zasobówego przeglądania szablonów](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Widok karty zostanie otwarty, aby wyświetlić przydatne właściwości, takie jak stan wdrożenia i Struktura szablonu.

    ![Szablon Menedżer zasobów Pokaż właściwości wdrożenia](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Edytowanie i Aktualizowanie istniejącego wdrożenia

1. Wybierz pozycję **Edytuj wdrożenie** z menu rozwijanego prawym przyciskiem myszy lub widoku Pokaż właściwości przed. Zostanie otwarty inny widok karty, który pokazuje szablon i pliki parametrów wdrożenia na platformie Azure. Aby zapisać te pliki do lokalnego, można kliknąć pozycję **Eksportuj plik szablonu**  lub **pliki parametrów eksportu**.

    ![Edytowanie wdrożenia szablonu Menedżer zasobów](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Można edytować te dwa pliki na tej stronie i wdrażać zmiany na platformie Azure. W tym miejscu zmodyfikujemy wartość **storageAccountType** w plikach parametrów, od `Standard_LRS` do `Standard_GRS` . Następnie kliknij pozycję **Aktualizuj wdrożenie** u dołu i Potwierdź aktualizację.

    ![Zrzut ekranu przedstawia szablon Menedżer zasobów z wyświetlonym monitem wdrożenia aktualizacji.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po zakończeniu wdrażania aktualizacji można sprawdzić, czy w portalu została zmieniona wartość utworzone konto magazynu `Standard_GRS` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Można to zrobić za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. W Eksploratorze Azure z POMYSŁu IntelliJ kliknij prawym przyciskiem myszy utworzoną **grupę zasobów** i wybierz pozycję Usuń.

    ![Usuwanie grupy zasobów w Eksploratorze platformy Azure z POMYSŁu IntelliJ](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Należy zauważyć, że usunięcie wdrożenia nie spowoduje usunięcia zasobów utworzonych przez wdrożenie. Jeśli nie są już potrzebne, Usuń odpowiednią grupę zasobów lub określone zasoby.

## <a name="next-steps"></a>Następne kroki

Głównym celem tego artykułu jest użycie IntelliJ pomysł do wdrożenia istniejącego szablonu z szablonów szybkiego startu platformy Azure. Wiesz również, jak wyświetlać i aktualizować istniejące wdrożenie na platformie Azure. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Odwiedź stronę Java w centrum deweloperów Azure](/azure/java)

---
title: Tworzenie pulpitu nawigacyjnego Azure Portal przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak utworzyć pulpit nawigacyjny Azure Portal przy użyciu szablonu Azure Resource Manager.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 06/15/2020
ms.openlocfilehash: fd2d7006e0c13f7e652b4edd973a36f94f2e2265
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85560504"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Szybki Start: tworzenie pulpitu nawigacyjnego w Azure Portal przy użyciu szablonu ARM

Pulpit nawigacyjny w Azure Portal to skoncentrowany i zorganizowany widok zasobów w chmurze. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia pulpitu nawigacyjnego. Pulpit nawigacyjny pokazuje wydajność maszyny wirtualnej, a także pewne informacje i linki statyczne.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Istniejąca maszyna wirtualna.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Pulpit nawigacyjny tworzony w następnej części tego przewodnika Szybki Start wymaga istniejącej maszyny wirtualnej. Utwórz maszynę wirtualną, wykonując następujące kroki.

1. W Azure Portal wybierz pozycję Cloud Shell.

    ![Wybieranie usługi Cloud Shell ze wstążki Azure Portal](media/quick-create-template/cloud-shell.png)

1. Skopiuj poniższe polecenie i wprowadź je w wierszu polecenia, aby utworzyć grupę zasobów.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Kopiuj polecenie do wiersza polecenia](media/quick-create-template/command-prompt.png)

1. Skopiuj poniższe polecenie i wprowadź je w wierszu polecenia, aby utworzyć maszynę wirtualną w grupie zasobów.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. Wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej. To jest nowa nazwa użytkownika i hasło. nie jest to na przykład konto używane do logowania się na platformie Azure. Aby uzyskać więcej informacji, zobacz Wymagania dotyczące [nazwy użytkownika](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) i [hasła](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Wdrożenie maszyny wirtualnej rozpocznie się i zwykle trwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/). Szablon tego artykułu jest zbyt długi, aby można go było wyświetlić w tym miejscu. Aby wyświetlić szablon, zobacz [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json). Jeden zasób platformy Azure jest zdefiniowany w szablonie [Microsoft. Portal/pulpity nawigacyjne](/azure/templates/microsoft.portal/dashboards) — Utwórz pulpit nawigacyjny w Azure Portal.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości, a następnie wybierz pozycję **Przegląd + Utwórz**.

    ![Szablon ARM, tworzenie pulpitu nawigacyjnego, wdrażanie portalu](media/quick-create-template/create-dashboard-using-template-portal.png)

    O ile nie zostanie on określony, użyj wartości domyślnych, aby utworzyć pulpit nawigacyjny.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **SimpleWinVmResourceGroup**.
    * **Lokalizacja**: wybierz pozycję **Wschodnie stany USA**.
    * **Nazwa maszyny wirtualnej**: wprowadź **SimpleWinVm**.
    * **Grupa zasobów maszyny wirtualnej**: wprowadź **SimpleWinVmResourceGroup**.

1. Wybierz pozycję **Utwórz** lub **Kup**. Po pomyślnym wdrożeniu pulpitu nawigacyjnego otrzymujesz powiadomienie:

    ![Szablon ARM, tworzenie pulpitu nawigacyjnego, powiadomienie dotyczące wdrażania portalu](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal został użyty do wdrożenia szablonu. Oprócz witryny Azure Portal do wdrażania szablonów można również użyć programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure oraz interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Sprawdź, czy pulpit nawigacyjny został utworzony pomyślnie i czy można zobaczyć dane z maszyny wirtualnej.

1. W witrynie Azure Portal wybierz pozycję **Pulpit nawigacyjny**.

    ![Azure Portal nawigację do pulpitu nawigacyjnego](media/quick-create-template/navigate-to-dashboards.png)

1. Na stronie Pulpit nawigacyjny wybierz pozycję **prosty pulpit nawigacyjny maszyny wirtualnej**.

    ![Przejdź do prostego pulpitu nawigacyjnego maszyny wirtualnej](media/quick-create-template/select-simple-vm-dashboard.png)

1. Przejrzyj pulpit nawigacyjny utworzony przez szablon ARM. Można zobaczyć, że część zawartości jest statyczna, ale istnieją także wykresy pokazujące wydajność utworzonej maszyny wirtualnej na początku.

    ![Przejrzyj prosty pulpit nawigacyjny maszyny wirtualnej](media/quick-create-template/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć maszynę wirtualną i skojarzony z nią pulpit nawigacyjny, Usuń grupę zasobów, która ją zawiera.

1. W Azure Portal Wyszukaj pozycję **SimpleWinVmResourceGroup**, a następnie wybierz ją w wynikach wyszukiwania.

1. Na stronie **SimpleWinVmResourceGroup** wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, która ma zostać poprawna, a następnie wybierz pozycję **Usuń**.

    ![Usuwanie grupy zasobów](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych w Azure Portal, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure Portal](azure-portal-dashboards.md)
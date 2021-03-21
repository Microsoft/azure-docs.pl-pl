---
title: Tworzenie laboratorium przy użyciu Azure DevTest Labs i szablonu Azure Resource Manager
description: W tym przewodniku szybki start utworzysz laboratorium w Azure DevTest Labs przy użyciu szablonu Azure Resource Manager (szablon ARM). Administrator laboratorium konfiguruje laboratorium, tworzy maszyny wirtualne w laboratorium i konfiguruje zasady.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91461193"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Szybki Start: Konfigurowanie laboratorium przy użyciu szablonu Azure DevTest Labs ARM
W tym przewodniku szybki start utworzysz laboratorium z maszyną wirtualną z systemem Windows Server 2019 Datacenter przy użyciu szablonu Azure Resource Manager (szablon ARM). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

W tym przewodniku szybki start wykonasz następujące czynności:

> [!div class="checklist"]
> * Przegląd szablonu 
> * Wdrożenie szablonu
> * Weryfikowanie szablonu
> * Oczyszczanie zasobów

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft. wspólny/laboratoria**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft. wspólny Labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft. wspólny Labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Wdrożenie szablonu
Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk. 

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Utwórz **nową grupę zasobów** , aby była łatwa do oczyszczenia później.
1. Wybierz **lokalizację** grupy zasobów. 
1. Wprowadź **nazwę laboratorium**. 
1. Wprowadź **nazwę maszyny wirtualnej**. 
1. Wprowadź **nazwę użytkownika** , który ma dostęp do maszyny wirtualnej. 
1. Wprowadź **hasło** dla użytkownika. 
1. Wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**. 
1. Następnie wybierz pozycję **Kup**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Strona wdrażania szablonu":::

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia
1. Wybierz pozycję **powiadomienia** u góry, aby wyświetlić stan wdrożenia, a następnie kliknij link **wdrażanie w toku** .

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Powiadomienie dotyczące wdrożenia":::
2. Na stronie **wdrażanie — przegląd** poczekaj na zakończenie wdrożenia. Wykonanie tej operacji (szczególnie w przypadku tworzenia maszyny wirtualnej) zajmuje trochę czasu. Następnie wybierz pozycję **Przejdź do grupy zasobów** lub **nazwę grupy zasobów** , jak pokazano na poniższej ilustracji: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Przejdź do grupy zasobów":::
3. Na stronie **Grupa zasobów** zostanie wyświetlona lista zasobów w grupie zasobów. Upewnij się, że w obszarze zasoby widzisz laboratorium typu: `DevTest Lab` . Widoczne są również zasoby zależne, takie jak sieć wirtualna i maszyna wirtualna w grupie zasobów. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Strona główna grupy zasobów":::
4. Wybierz swoje laboratorium z listy zasobów, aby wyświetlić stronę główną dla laboratorium. Upewnij się, że maszyna wirtualna z systemem Windows Server 2019 Datacenter znajduje się na liście **moje maszyny wirtualne** . Na poniższej ilustracji sekcja **podstawowe** informacje jest zminimalizowana. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Strona główna laboratorium":::

    > [!IMPORTANT] 
    > Pozostaw Tę stronę otwartą i postępuj zgodnie z instrukcjami w następnej sekcji, aby wyczyścić zasoby, aby uniknąć ponoszenia kosztów związanych z uruchamianiem laboratorium i maszyną wirtualną na platformie Azure. Jeśli chcesz przejść do następnego samouczka, aby przetestować dostęp do maszyny wirtualnej w laboratorium, Oczyść zasoby po przejściu przez ten samouczek. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Najpierw usuń laboratorium, aby można było usunąć grupę zasobów. Nie będzie można usunąć grupy zasobów z laboratorium. Aby usunąć laboratorium, wybierz pozycję **Usuń** na pasku narzędzi. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Usuń przycisk laboratorium":::
 2. Na stronie Potwierdzenie wpisz **nazwę laboratorium** i wybierz pozycję **Usuń**. 
 3. Poczekaj na usunięcie laboratorium. Wybierz ikonę **dzwonka** , aby wyświetlić powiadomienie z operacji usuwania. Ten proces zajmuje trochę czasu. Potwierdź usunięcie laboratorium, a następnie wybierz **grupę zasobów** w menu stron nadrzędnych. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Potwierdź usunięcie maszyny wirtualnej w powiadomieniach":::
 1. Na stronie **Grupa zasobów** wybierz pozycję **Usuń grupę zasobów** z paska narzędzi. Na stronie Potwierdzenie wpisz **nazwę grupy zasobów**, a następnie wybierz pozycję **Usuń**. Sprawdź powiadomienia, aby upewnić się, że grupa zasobów została usunięta.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Przycisk usuwania grupy zasobów":::

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start utworzono laboratorium z maszyną wirtualną. Aby dowiedzieć się, jak uzyskać dostęp do laboratorium, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: uzyskiwanie dostępu do laboratorium](tutorial-use-custom-lab.md)

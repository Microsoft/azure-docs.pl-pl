---
title: Szablon z zasobami zależnymi
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager z wieloma zasobami, a także jak wdrożyć go przy użyciu witryny Azure Portal
author: mumian
ms.date: 04/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e589fa8ae5627746ec1f04e2098a7b592f00dc24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684940"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Samouczek: Tworzenie szablonów ARM z zasobami zależnymi

Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager (ARM) w celu wdrożenia wielu zasobów i skonfigurowania kolejności wdrażania. Po utworzeniu szablonu można wdrożyć szablon przy użyciu powłoki chmury z witryny Azure portal.

Instrukcje w tym samouczku pozwalają utworzyć konto magazynu, maszynę wirtualną, sieć wirtualną oraz niektóre inne zasoby zależne. Niektórych zasobów nie można wdrożyć, dopóki nie istnieje inny zasób. Przykładowo nie można utworzyć maszyny wirtualnej, jeżeli nie istnieje konto magazynu i interfejs sieciowy. Relację tę definiuje się, ustawiając jeden zasób jako zależny od innych zasobów. Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach ARM](./define-resource-dependency.md).

![Diagram kolejności wdrażania zasobów zależnych od szablonu menedżera zasobów Menedżera zasobów](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Eksplorowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybki start platformy Azure to repozytorium szablonów ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik**>**otwórz plik**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz **opcję Zapisz plik,**>**Save As** aby zapisać kopię pliku na komputerze lokalnym o nazwie **azuredeploy.json**.

## <a name="explore-the-template"></a>Eksplorowanie szablonu

Podczas eksplorowania szablonu w tej sekcji spróbuj odpowiedzieć na następujące pytania:

* Jak wiele zasobów platformy Azure zostało zdefiniowanych w tym szablonie?
* Jednym z tych zasobów jest konto usługi Azure Storage.  Czy definicja przypomina tę użytą w poprzednim samouczku?
* Czy możesz znaleźć dokumentację szablonów dla zasobów zdefiniowanych w tym szablonie?
* Czy możesz znaleźć zależności zasobów?

1. W programie Visual Studio Code zwiń elementy, aby wyświetlić tylko elementy pierwszego poziomu oraz elementy drugiego poziomu wewnątrz **zasobów**:

    ![Szablony usługi Azure Resource Manager w programie Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Szablon definiuje sześć zasobów:

   * [**Microsoft.Storage/storageKonta .**](/azure/templates/Microsoft.Storage/storageAccounts)
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Warto przejrzeć odwołanie do szablonu przed dostosowaniem szablonu.

1. Rozwiń pierwszy zasób. Jest to konto magazynu. Porównaj definicję zasobu z [odwołaniem do szablonu](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definicja konta magazynu w szablonach usługi Resource Manager w programie Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Rozwiń drugi zasób. Typ zasobu to `Microsoft.Network/publicIPAddresses`. Porównaj definicję zasobu z [odwołaniem do szablonu](/azure/templates/microsoft.network/publicipaddresses).

    ![Definicja publicznego adresu IP w szablonach usługi Resource Manager w programie Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Rozwiń trzeci zasób. Typ zasobu to `Microsoft.Network/networkSecurityGroups`. Porównaj definicję zasobu z [odwołaniem do szablonu](/azure/templates/microsoft.network/networksecuritygroups).

    ![Definicja sieciowej grupy zabezpieczeń szablonów szablonów usługi Azure Resource Manager w programie Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Rozwiń czwarty zasób. Typ zasobu to `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code Usługi Azure Resource Manager szablony zależyOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Element dependsOn umożliwia zdefiniowanie jednego zasobu jako zasobu zależnego od jednego lub większej liczby zasobów. Ten zasób zależy od innego zasobu:

    * `Microsoft.Network/networkSecurityGroups`

1. Rozwiń piąty zasób. Typ zasobu to `Microsoft.Network/networkInterfaces`. Zasób zależy od dwóch innych zasobów:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Rozwiń szósty zasób. Ten zasób to maszyna wirtualna. Zależy on od dwóch innych zasobów:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Na poniższym diagramie przedstawiono zasoby i informacje o zależności dla tego szablonu:

![Diagram zależności szablonu usługi Azure Resource Manager w programie Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Poprzez określenie zależności usługa Resource Manager efektywnie wdraża rozwiązanie. Usługa wdraża równolegle konto magazynu, publiczny adres IP i sieć wirtualną, ponieważ nie mają zależności. Po wdrożeniu adresu IP i sieci wirtualnej zostanie utworzony interfejs sieciowy. Po wdrożeniu wszystkich innych zasobów usługa Resource Manager wdroży maszynę wirtualną.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Postępuj zgodnie z instrukcjami w [Deploy szablon,](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) aby otworzyć powłokę chmury i przekazać poprawiony szablon.

1. Uruchom następujący skrypt programu PowerShell, aby wdrożyć szablon.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzoną maszynę wirtualną:

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $resourceGroupName = "${projectName}rg"
    $vmName = "SimpleWinVM"

    Get-AzVM -Name $vmName -ResourceGroupName $resourceGroupName
    
    Write-Host "Press [ENTER] to continue ..."
    ```

    Nazwa maszyny wirtualnej jest zakodowana jako **SimpleWinVM** wewnątrz szablonu.

1. Nawiąż połączenie RDP z maszyną wirtualną, aby sprawdzić, czy została pomyślnie utworzona.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów. Zobaczysz w sumie sześć zasobów w grupie zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i wdrożono szablon, aby utworzyć maszynę wirtualną, sieć wirtualną i zasoby zależne. Aby dowiedzieć się, jak używać skryptów wdrażania do wykonywania operacji przed/po wdrożeniu, zobacz:

> [!div class="nextstepaction"]
> [Używanie skryptu wdrażania](./template-tutorial-deployment-script.md)

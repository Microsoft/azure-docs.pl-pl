---
title: Użycie warunku w szablonach
description: Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków. Pokazuje, jak wdrożyć nowy zasób lub użyć istniejącego zasobu.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696027"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Samouczek: Używanie warunku w szablonach usługi ARM

Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków w szablonie Azure Resource Manager (szablon ARM).

W samouczku [Ustawianie kolejności wdrażania zasobów](./template-tutorial-create-templates-with-dependent-resources.md) tworzysz maszynę wirtualną, sieć wirtualną i kilka innych zasobów zależnych, w tym konto magazynu. Zamiast za każdym razem tworzyć nowe konto magazynu, zezwalasz użytkownikom na utworzenie nowego konta magazynu lub użycie istniejącego. Aby osiągnąć ten cel, definiujesz dodatkowy parametr. Jeśli wartość parametru jest **Nowa**, zostanie utworzone nowe konto magazynu. W przeciwnym razie używane jest istniejące konto magazynu o podanej nazwie.

![Diagram warunków używania szablonu Menedżer zasobów](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Modyfikowanie szablonu
> * Wdrożenie szablonu
> * Czyszczenie zasobów

Ten samouczek obejmuje tylko podstawowy scenariusz użycia warunków. Aby uzyskać więcej informacji, zobacz:

* [Struktura pliku szablonu: warunek](conditional-resource-deployment.md).
* [Warunkowego wdrażania zasobu w szablonie ARM](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Funkcja szablonu: Jeśli](./template-functions-logical.md#if).
* [Funkcje porównania dla szablonów ARM](./template-functions-comparison.md)

Aby uzyskać Microsoft Learn moduł, który obejmuje warunki, zobacz [zarządzanie złożonymi wdrożeniami w chmurze za pomocą zaawansowanych funkcji szablonów usługi ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w wdrożeniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W obszarze Visual Studio Code wybierz pozycję **plik**  >  **Otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
1. Istnieje sześć zasobów zdefiniowanych przez szablon:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Warto zapoznać się z dokumentacją szablonu przed przystąpieniem do dostosowywania szablonu.

1. Wybierz pozycję **plik**  >  **Zapisz jako,** aby zapisać kopię pliku na komputerze lokalnym o nazwie _azuredeploy.jsna_.

## <a name="modify-the-template"></a>Modyfikowanie szablonu

Wprowadź dwie zmiany do istniejącego szablonu:

* Dodaj parametr nazwy konta magazynu. Użytkownicy mogą określić nazwę nowego lub istniejącego konta magazynu.
* Dodaj nowy parametr o nazwie `newOrExisting` . Wdrożenie używa tego parametru, aby określić, czy należy utworzyć nowe konto magazynu, czy użyć istniejącego konta magazynu.

Poniżej przedstawiono procedurę wprowadzania zmian:

1. Otwórz plik _azuredeploy.json_ w programie Visual Studio Code.
1. Zastąp trzy `variables('storageAccountName')` przy użyciu `parameters('storageAccountName')` w całym szablonie.
1. Usuń następującą definicję zmiennej:

    ![Zrzut ekranu, który wyróżnia definicje zmiennych, które należy usunąć.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Dodaj następujące dwa parametry na początku sekcji Parameters:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Naciśnij klawisze Alt + Shift + F, aby sformatować szablon w Visual Studio Code.

    Zaktualizowana definicja parametrów wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Dodaj następujący wiersz na początku definicji konta magazynu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Warunek sprawdza wartość parametru `newOrExisting` . Jeśli wartość parametru to **new**, przy wdrażaniu tworzone jest konto magazynu.

    Zaktualizowana definicja konta magazynu wygląda następująco:

    ![Zrzut ekranu przedstawiający zaktualizowaną definicję konta magazynu.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Zaktualizuj `storageUri` Właściwość definicji zasobu maszyny wirtualnej za pomocą następującej wartości:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ta zmiana jest niezbędna, jeśli używasz istniejącego konta magazynu w innej grupie zasobów.

1. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Zaloguj się do [Azure Cloud Shell](https://shell.azure.com)

1. Wybierz preferowane środowisko, wybierając opcję **PowerShell** lub **bash** (dla interfejsu wiersza polecenia) w lewym górnym rogu. Po przełączeniu wymagane jest ponowne uruchomienie powłoki.

    ![Azure Portal Cloud Shell przekazywania pliku](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**. Zobacz poprzedni zrzut ekranu. Wybierz plik, który został zapisany w poprzedniej sekcji. Po `ls` przekazaniu pliku możesz użyć polecenia i `cat` polecenia, aby sprawdzić, czy plik został pomyślnie przekazany.

1. Uruchom następujący skrypt programu PowerShell, aby wdrożyć szablon.

    > [!IMPORTANT]
    > Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Nazwa może zawierać tylko małe litery lub cyfry. Nie może być dłuższa niż 24 znaki. Nazwa konta magazynu jest nazwą projektu z dołączonym **magazynem** . Upewnij się, że nazwa projektu i wygenerowana nazwa konta magazynu spełniają wymagania dotyczące nazw kont magazynu.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Wdrożenie nie powiedzie się `newOrExisting` , jeśli jest **nowe**, ale konto magazynu o podanej nazwie konta magazynu już istnieje.

Spróbuj wprowadzić inne wdrożenie z `newOrExisting` ustawionym na **istniejące** i określ istniejące konto magazynu. Aby wcześniej utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Aby usunąć grupę zasobów, wybierz pozycję **Wypróbuj** , aby otworzyć Cloud Shell. Aby wkleić skrypt programu PowerShell, kliknij prawym przyciskiem myszy okienko powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono szablon, który pozwala użytkownikom wybrać między utworzeniem nowego konta magazynu a użyciem istniejącego konta magazynu. Aby dowiedzieć się, jak pobierać wpisy tajne z usługi Azure Key Vault i używać ich jako haseł przy wdrożeniach na podstawie szablonu, zobacz:

> [!div class="nextstepaction"]
> [Integrowanie usługi Key Vault z wdrożeniem z użyciem szablonu](./template-tutorial-use-key-vault.md)

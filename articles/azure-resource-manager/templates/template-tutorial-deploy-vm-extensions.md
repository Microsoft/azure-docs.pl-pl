---
title: Wdróż rozszerzenia maszyn wirtualnych z szablonem
description: Dowiedz się, jak wdrożyć rozszerzenia maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f82e0eb45f4bc7c3260554b1b1120025029336bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073646"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych przy użyciu szablonów ARM

Dowiedz się, jak używać [rozszerzeń maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/features-windows.md) do wykonywania konfiguracji po wdrożeniu oraz zadań automatyzacji na maszynach wirtualnych platformy Azure. Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. W tym samouczku wdrożono niestandardowe rozszerzenie skryptu z szablonu Azure Resource Manager (ARM) w celu uruchomienia skryptu programu PowerShell na maszynie wirtualnej z systemem Windows.  Skrypt instaluje serwer internetowy na maszynie wirtualnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie skryptu programu PowerShell
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrażanie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Szybki Start: tworzenie Azure Resource Manager szablonów z Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w wdrożeniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-powershell-script"></a>Przygotowywanie skryptu programu PowerShell

Można użyć wbudowanego skryptu programu PowerShell lub pliku skryptu.  W tym samouczku pokazano, jak używać pliku skryptu. Skrypt programu PowerShell z następującą zawartością jest udostępniany w witrynie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

W przypadku wybrania opcji opublikowania pliku we własnej lokalizacji należy zaktualizować `fileUri` element w szablonie w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W Visual Studio Code wybierz pozycję **plik**  >  **Otwórz plik**.
1. W polu **Nazwa pliku** wklej następujący adres URL: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wybierz pozycję **Otwórz**, aby otworzyć plik.
    Szablon definiuje pięć zasobów:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.

1. Aby zapisać kopię pliku o nazwie *azuredeploy.json* na komputerze lokalnym, wybierz pozycję **Plik** > **Zapisz jako**.

## <a name="edit-the-template"></a>Edytowanie szablonu

Dodaj zasób rozszerzenia maszyny wirtualnej do istniejącego szablonu o następującej zawartości:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Zobacz [informacje szczegółowe o rozszerzeniu](/azure/templates/microsoft.compute/virtualmachines/extensions), jeśli potrzebujesz więcej informacji na temat tej definicji zasobu. Poniżej przedstawiono niektóre ważne elementy:

* **name**: ponieważ zasób rozszerzenia jest zasobem podrzędnym obiektu maszyny wirtualnej, nazwa musi mieć prefiks nazwy maszyny wirtualnej. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md).
* **dependsOn**: Utwórz zasób rozszerzenia po utworzeniu maszyny wirtualnej.
* **fileUris**: lokalizacje, w których są przechowywane pliki skryptów. Jeśli nie chcesz używać podanej lokalizacji, musisz zaktualizować wartości.
* **sekcji commandtoexecute**: to polecenie wywołuje skrypt.

Aby użyć skryptu wbudowanego, Usuń **fileUris**i zaktualizuj **sekcji commandtoexecute** do:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Ten skrypt wbudowany aktualizuje również zawartość iisstart.html.

Należy również otworzyć port HTTP, aby umożliwić dostęp do serwera sieci Web.

1. Znajdź **securityRules** w szablonie.
1. Dodaj następującą regułę obok pozycji **default-Allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Procedurę wdrażania można znaleźć w sekcji "Wdrażanie szablonu" w [samouczku: Tworzenie szablonów ARM z zasobami zależnymi](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Zalecamy użycie wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz sekcję [Wymagania wstępne](#prerequisites) tego artykułu.

W Cloud Shell Uruchom następujące polecenie, aby pobrać publiczny adres IP maszyny wirtualnej:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Wklej adres IP do przeglądarki sieci Web. Zostanie otwarta domyślna strona powitalna usług Internet Information Services (IIS):

![Strona powitalna usług Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów platformy Azure wdrożonych przez Ciebie, wyczyść je, usuwając grupę zasobów.

1. W witrynie Azure Portal w okienku po lewej wybierz pozycję **Grupa zasobów**.
2. W polu **Filtruj według nazwy** podaj nazwę grupy zasobów.
3. Wybierz nazwę grupy zasobów.
    W grupie zasobów jest wyświetlanych sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** w górnym menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono maszynę wirtualną i rozszerzenie maszyny wirtualnej. Rozszerzenie zainstalowało serwer internetowy usług IIS na maszynie wirtualnej. Aby dowiedzieć się, jak zaimportować plik BACPAC za pomocą rozszerzenia usługi Azure SQL Database, zobacz:

> [!div class="nextstepaction"]
> [Wdrażanie rozszerzeń SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)

---
title: Używanie usługi Azure Key Vault w szablonach
description: Dowiedz się, jak używać usługi Azure Key Vault do bezpiecznego przekazywania wartości parametrów podczas wdrażania szablonu usługi Resource Manager
author: mumian
ms.date: 04/16/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: c33ad17927dae701e4201e76b7a75690c59dc374
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536707"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Samouczek: Integracja usługi Azure Key Vault we wdrożeniu szablonu ARM

Dowiedz się, jak pobrać wpisy tajne z magazynu kluczy platformy Azure i przekazać wpisy tajne jako parametry podczas wdrażania szablonu usługi Azure Resource Manager (ARM). Wartość parametru nigdy nie jest narażona, ponieważ odwołujesz się tylko do jego identyfikatora magazynu kluczy. Klucz tajny magazynu kluczy można odwoływać się przy użyciu identyfikatora statycznego lub identyfikatora dynamicznego. W tym samouczku użyto identyfikatora statycznego. W podejściu identyfikatora statycznego odwołujesz się do przechowalni kluczy w pliku parametrów szablonu, a nie do pliku szablonu. Aby uzyskać więcej informacji na temat obu podejść, zobacz [Korzystanie z usługi Azure Key Vault w celu przekazania bezpiecznej wartości parametru podczas wdrażania](./key-vault-parameter.md).

W samouczku [Kolejność wdrażania zasobu Ustawianie](./template-tutorial-create-templates-with-dependent-resources.md) maszyny wirtualnej (VM). Należy podać nazwę użytkownika i hasło administratora maszyny Wirtualnej. Zamiast podawania hasła, można wstępnie przechowywać hasło w magazynie kluczy platformy Azure, a następnie dostosować szablon, aby pobrać hasło z magazynu kluczy podczas wdrażania.

![Diagram przedstawiający integrację szablonu Menedżera zasobów z magazynem kluczy](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie magazynu kluczy
> * Otwieranie szablonu szybkiego startu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny Wirtualnej. Oto przykład do generowania hasła:

    ```console
    openssl rand -base64 32
    ```
    Sprawdź, czy wygenerowane hasło spełnia wymagania dotyczące hasła maszyny Wirtualnej. Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Aby zapoznać się z wymaganiami dotyczącymi haseł maszyny [Wirtualnej, zobacz Jakie są wymagania dotyczące hasła podczas tworzenia maszyny Wirtualnej?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Przygotowanie magazynu kluczy

W tej sekcji utworzysz magazyn kluczy i dodasz do niego klucz tajny, dzięki czemu można pobrać klucz tajny podczas wdrażania szablonu. Istnieje wiele sposobów tworzenia magazynu kluczy. W tym samouczku można użyć programu Azure PowerShell do wdrożenia [szablonu ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ten szablon zawiera dwie czynności:

* Tworzy magazyn kluczy `enabledForTemplateDeployment` z włączoną właściwością. Ta właściwość musi być *true* przed procesem wdrażania szablonu można uzyskać dostęp do wpisów tajnych, które są zdefiniowane w magazynie kluczy.
* Dodaje klucz tajny do magazynu kluczy. Klucz tajny przechowuje hasło administratora maszyny Wirtualnej.

> [!NOTE]
> Jako użytkownik wdrażający szablon maszyny wirtualnej, jeśli nie jesteś właścicielem lub współautorem magazynu kluczy, właściciel lub współautor musi udzielić ci dostępu do uprawnienia *Microsoft.KeyVault/vaults/deploy/action* dla magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Key Vault w celu przekazania bezpiecznej wartości parametru podczas wdrażania](./key-vault-parameter.md).

Aby uruchomić następujący skrypt programu Azure PowerShell, wybierz pozycję **Spróbuj otworzyć** usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy okienko powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Nazwa grupy zasobów jest nazwą projektu, ale z **rg** dołączone do niego. Aby ułatwić czyszczenie [zasobów utworzonych w tym samouczku,](#clean-up-resources)użyj tej samej nazwy projektu i nazwy grupy zasobów podczas [wdrażania następnego szablonu](#deploy-the-template).
> * Domyślną nazwą klucza tajnego jest **vmAdminPassword**. Jest hardcoded w szablonie.
> * Aby włączyć szablon do pobierania klucza tajnego, należy włączyć zasady dostępu o nazwie **Włącz dostęp do usługi Azure Resource Manager do wdrożenia szablonu** dla magazynu kluczy. Ta zasada jest włączona w szablonie. Aby uzyskać więcej informacji na temat zasad dostępu, zobacz [Wdrażanie magazynów kluczy i wpisów tajnych](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Szablon ma jedną wartość wyjściową, o nazwie *keyVaultId*. Użyjesz tego identyfikatora wraz z tajną nazwą, aby pobrać wartość tajnego w dalszej części samouczka. Format identyfikatora zasobu to:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Podczas kopiowania i wklejania identyfikatora może on zostać podzielony na wiele wierszy. Scalanie linii i przycinanie dodatkowych spacji.

Aby sprawdzić poprawność wdrożenia, uruchom następujące polecenie programu PowerShell w tym samym okienku powłoki, aby pobrać klucz tajny w postaci zwykłego tekstu. Polecenie działa tylko w tej samej sesji powłoki, ponieważ używa zmiennej *$keyVaultName*, która jest zdefiniowana w poprzednim skrypcie programu PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teraz przygotowaliśmy skarbiec kluczy i tajemnicę. W poniższych sekcjach pokazano, jak dostosować istniejący szablon, aby pobrać klucz tajny podczas wdrażania.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybki start platformy Azure to repozytorium szablonów ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon, który jest używany w tym samouczku nazywa [się Wdrażanie prostej maszyny Wirtualnej systemu Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **otwórz plik**.

1. W polu **Nazwa pliku** wklej następujący adres URL: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik. Scenariusz jest taki sam jak ten, który jest używany w [samouczku: Tworzenie szablonów ARM z zasobami zależnymi](./template-tutorial-create-templates-with-dependent-resources.md).
   Szablon definiuje sześć zasobów:

   * [**Microsoft.Storage/storageKonta .**](/azure/templates/Microsoft.Storage/storageAccounts)
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Warto mieć podstawową wiedzę na temat szablonu przed jego dostosowaniem.

1. Wybierz **pozycję Zapisz jako plik,** > **Save As**a następnie zapisz kopię pliku na komputerze lokalnym o nazwie *azuredeploy.json*.

1. Powtórz kroki od 1 do 3, aby otworzyć następujący adres URL, a następnie zapisz plik jako *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edytowanie pliku parametrów

Za pomocą metody identyfikatora statycznego, nie trzeba wprowadzać żadnych zmian w pliku szablonu. Pobieranie wartości tajnej odbywa się przez skonfigurowanie pliku parametru szablonu.

1. W programie Visual Studio Code otwórz *azuredeploy.parameters.json,* jeśli nie jest jeszcze otwarty.
1. Zaktualizuj `adminPassword` parametr do:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Zastąp wartość **identyfikatora** identyfikatorem zasobu magazynu kluczy utworzonego w poprzedniej procedurze. SecretName jest zakodowany jako **vmAdminPassword**.  Zobacz [Przygotowywanie magazynu kluczy](#prepare-a-key-vault).

    ![Integrowanie pliku parametrów wdrażania magazynu kluczy i szablonu usługi Resource Manager](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Zaktualizuj następujące wartości:

    * **adminUsername**: Nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: Nazwa wartości dnsLabelPrefix.

    Przykłady nazw można znaleźć na poprzednim obrazie.

1. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami [w deploy the template](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Przekaż do usługi Cloud Shell zarówno *azuredeploy.json,* jak i *azuredeploy.parameters.json,* a następnie użyj następującego skryptu programu PowerShell do wdrożenia szablonu:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

Write-Host "Press [ENTER] to continue ..."
```

Podczas wdrażania szablonu należy użyć tej samej grupy zasobów, która została użyta w magazynie kluczy. Takie podejście ułatwia czyszczenie zasobów, ponieważ należy usunąć tylko jedną grupę zasobów zamiast dwóch.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej przetestuj poświadczenia logowania przy użyciu hasła przechowywanego w magazynie kluczy.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. Wybierz **grupy** > **\<zasobów*YourResourceGroupName*>** > **simpleWinVM**.
1. Wybierz **opcję Połącz** u góry.
1. Wybierz **pozycję Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na maszynie wirtualnej przy użyciu hasła przechowywanego w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów platformy Azure, wyczyść zasoby, które zostały wdrożone przez usunięcie grupy zasobów.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku pobrano klucz tajny z magazynu kluczy platformy Azure. Następnie użyto klucza tajnego we wdrożeniu szablonu. Aby dowiedzieć się, jak wykonać zadania po wdrożeniu przy użyciu rozszerzenia maszyny wirtualnej, zobacz:

> [!div class="nextstepaction"]
> [Wdrażanie rozszerzeń maszyny wirtualnej](./template-tutorial-deploy-vm-extensions.md)

---
title: Przewodnik Szybki Start platformy Azure — tworzenie magazynu kluczy platformy Azure i wpisu tajnego przy użyciu szablonu Azure Resource Manager | Microsoft Docs
description: Przewodnik Szybki Start przedstawiający sposób tworzenia magazynów kluczy Azure i dodawania wpisów tajnych do magazynów przy użyciu szablonu Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 68c10ba5581ffe404e8e3c1ac8ce92832cfcd6c4
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896433"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu szablonu ARM

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia magazynu kluczy i wpisu tajnego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące Azure PowerShell lub polecenie interfejsu wiersza polecenia platformy Azure, wybierając pozycję **Wypróbuj** , a następnie wklej skrypt do okienka powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej** .

        # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Zanotuj identyfikator obiektu. Będzie ona potrzebna w następnej sekcji tego przewodnika Szybki Start.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Key — magazyn/magazyny**](/azure/templates/microsoft.keyvault/vaults): Utwórz magazyn kluczy platformy Azure.
* [**Microsoft. Key/magazyny/wpisy tajne**](/azure/templates/microsoft.keyvault/vaults/secrets): Utwórz klucz tajny magazynu kluczy.

Więcej przykładów szablonów Azure Key Vault można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    ![Szablon ARM, integracja Key Vault, wdrażanie portalu](../media/quick-create-template/create-key-vault-using-template-portal.png)

    O ile nie zostanie on określony, użyj wartości domyślnej, aby utworzyć magazyn kluczy i wpis tajny.

    * **Subskrypcja** : wybierz subskrypcję platformy Azure.
    * **Grupa zasobów** : wybierz pozycję **Utwórz nową** , wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK** .
    * **Lokalizacja** : wybierz lokalizację. Na przykład **Środkowe stany USA** .
    * **Nazwa Key Vault** : Wprowadź nazwę magazynu kluczy, która musi być globalnie unikatowa w przestrzeni nazw. Vault.Azure.NET. Podczas sprawdzania poprawności wdrożenia potrzebna jest nazwa w następnej sekcji.
    * **Identyfikator dzierżawy** : Funkcja szablonu automatycznie pobiera swój identyfikator dzierżawy. Nie zmieniaj wartości domyślnej.
    * **Identyfikator użytkownika usługi AD** : Wprowadź identyfikator obiektu użytkownika usługi Azure AD, który został pobrany z [wymagań wstępnych](#prerequisites).
    * **Nazwa wpisu tajnego** : Wprowadź nazwę wpisu tajnego, który jest przechowywany w magazynie kluczy. Na przykład klucz **AdminPassword** .
    * **Wartość wpisu tajnego** : wprowadź wartość klucza tajnego. Jeśli przechowujesz hasło, zalecamy użycie wygenerowanego hasła utworzonego w sekcji wymagania wstępne.
    * **Wyrażam zgodę na powyższe warunki i postanowienia** : Zaznacz.
3. Wybierz pozycję **Kup** . Po pomyślnym wdrożeniu magazynu kluczy otrzymujesz powiadomienie:

    ![Szablon ARM, integracja Key Vault, wdrażanie powiadomienia portalu](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal, aby sprawdzić Magazyn kluczy i klucz tajny, lub użyć następującego interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić utworzony wpis tajny.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Dane wyjściowe wyglądają podobnie do:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

![Zrzut ekranu pokazujący dane wyjściowe weryfikacji portalu wdrażania w interfejsie wiersza polecenia.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Szablon ARM, integracja Key Vault, wdrażanie danych wyjściowych weryfikacji portalu](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy i klucz tajny przy użyciu szablonu usługi ARM oraz zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Key Vault i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań

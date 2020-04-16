---
title: Szybki start platformy Azure — tworzenie magazynu kluczy platformy Azure i klucza tajnego przy użyciu szablonu usługi Azure Resource Manager | Dokumenty firmy Microsoft
description: Przewodnik Szybki start przedstawiający sposób tworzenia magazynów kluczy platformy Azure i dodawania wpisów tajnych do magazynów przy użyciu szablonu usługi Azure Resource Manager.
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
ms.openlocfilehash: 8190c241631e0d90e6ae1fbd82f3d3a05e161aab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424972"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Szybki start: ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu szablonu Usługi Resource Manager

[Usługa Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu Menedżera zasobów w celu utworzenia magazynu kluczy i klucza tajnego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące polecenie programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, wybierając pozycję **Wypróbuj,** a następnie wklej skrypt do okienka powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**.

        # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Zanotuj identyfikator obiektu. Jest potrzebny w następnej sekcji tego przewodnika Szybki start.

## <a name="create-a-vault-and-a-secret"></a>Tworzenie przechowalni i klucza tajnego

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): tworzenie magazynu kluczy platformy Azure.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): utworzyć klucz tajny magazynu kluczy.

Więcej przykładów szablonów usługi Azure Key Vault można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../media/quick-create-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    ![Szablon Menedżera zasobów, integracja z magazynem kluczy, wdrażanie portalu](../media/quick-create-template/create-key-vault-using-template-portal.png)

    O ile nie zostanie określona, użyj wartości domyślnej, aby utworzyć magazyn kluczy i klucz tajny.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz **pozycję Utwórz nowy**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa magazynu kluczy:** wprowadź nazwę magazynu kluczy, która musi być globalnie unikatowa w obszarze nazw .vault.azure.net. Nazwa jest potrzebna w następnej sekcji podczas sprawdzania poprawności wdrożenia.
    * **Identyfikator dzierżawy:** funkcja szablonu automatycznie pobiera identyfikator dzierżawy.  Nie zmieniaj wartości domyślnej.
    * **Nazwa użytkownika reklamy:** wprowadź identyfikator obiektu użytkownika usługi Azure AD, który został pobrany z [wymagań wstępnych](#prerequisites).
    * **Nazwa tajna**: wprowadź nazwę klucza tajnego, który przechowujesz w magazynie kluczy.  Na przykład **adminpassword**.
    * **Wartość tajna:** wprowadź wartość tajną.  Jeśli przechowujesz hasło, zaleca się użycie wygenerowanego hasła utworzonego w wymaganiach wstępnych.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: zaznacz.
3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu magazynu kluczy otrzymasz powiadomienie:

    ![Szablon Menedżera zasobów, integracja z magazynem kluczy, wdrażanie powiadomień portalu](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Witryna Azure portal służy do wdrażania szablonu. Oprócz witryny Azure portal można również użyć interfejsu API programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Za pomocą witryny Azure Portal można sprawdzić magazyn kluczy i klucz tajny, lub użyć następującego skryptu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby wyświetlić listę utworzonego klucza tajnego.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Dane wyjściowe wyglądają podobnie do:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

![Szablon Menedżera zasobów, integracja z magazynem kluczy, wdrażanie danych wyjściowych sprawdzania poprawności portalu](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Szablon Menedżera zasobów, integracja z magazynem kluczy, wdrażanie danych wyjściowych sprawdzania poprawności portalu](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy i klucz tajny przy użyciu szablonu usługi Azure Resource Manager i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o usłudze Key Vault i usłudze Azure Resource Manager, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o [usłudze Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)

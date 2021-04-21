---
title: Przewodnik Szybki start platformy Azure — tworzenie magazynu kluczy platformy Azure i klucza tajnego przy użyciu Azure Resource Manager szablonu | Microsoft Docs
description: Przewodnik Szybki start przedstawiający sposób tworzenia magazynów kluczy platformy Azure i dodawania wpisów tajnych do magazynów przy użyciu Azure Resource Manager szablonu.
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
ms.openlocfilehash: 214c86eb7272c87e067b1d5f6df0b09ce9e7095c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814140"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Szybki start: konfigurowanie i pobieranie informacji tajnych z Azure Key Vault przy użyciu szablonu usługi ARM

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu usługi Azure Resource Manager (szablonu ARM) w celu utworzenia magazynu kluczy i klucza tajnego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące polecenie Azure PowerShell interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj**, a następnie wklej skrypt w okienku powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz pozycję **Wklej**.

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

    2. Zanotuj identyfikator obiektu. Będzie on potrzebny w następnej sekcji tego przewodnika Szybki start.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)utwórz magazyn kluczy platformy Azure.
* [**Microsoft.KeyVault/vaults/secrets:**](/azure/templates/microsoft.keyvault/vaults/secrets)utwórz wpis tajny magazynu kluczy.

Więcej Azure Key Vault przykładów szablonów można znaleźć w [szablonach szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    ![Szablon usługi ARM, Key Vault integracji, wdrażanie portalu](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Jeśli nie zostanie określony, użyj wartości domyślnej, aby utworzyć magazyn kluczy i klucz tajny.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK.**
    * **Lokalizacja**: wybierz lokalizację. Na przykład **Środkowe stany USA**.
    * **Key Vault Nazwa:** wprowadź nazwę magazynu kluczy, która musi być globalnie unikatowa w przestrzeni nazw vault.azure.net . Nazwa będzie potrzebna w następnej sekcji podczas weryfikowania wdrożenia.
    * **Identyfikator dzierżawy:** funkcja szablonu automatycznie pobiera identyfikator dzierżawy. Nie zmieniaj wartości domyślnej.
    * **Identyfikator użytkownika usługi AD:** wprowadź identyfikator obiektu użytkownika usługi Azure AD pobrany z witryny [Prerequisites](#prerequisites).
    * **Nazwa klucza tajnego:** wprowadź nazwę klucza tajnego przechowywanego w magazynie kluczy. Na przykład **adminpassword**.
    * **Wartość wpisów** tajnych: wprowadź wartość wpisów tajnych. Jeśli przechowujesz hasło, zaleca się użycie wygenerowanego hasła utworzonego w wymaganiach wstępnych.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.
3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu magazynu kluczy otrzymasz powiadomienie:

    ![Szablon usługi ARM, Key Vault integracji, wdrażanie powiadomienia portalu](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz interfejsu Azure Portal można również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Możesz użyć klucza Azure Portal do sprawdzenia magazynu kluczy i klucza tajnego albo użyć następującego interfejsu wiersza polecenia platformy Azure lub Azure PowerShell skryptu, aby wyświetlić listę utworzonych kluczy tajnych.

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

![Zrzut ekranu przedstawiający dane wyjściowe weryfikacji portalu wdrażania w interfejsie wiersza polecenia.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

![Szablon usługi ARM, Key Vault integracji, wdrażanie danych wyjściowych weryfikacji portalu](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

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

W tym przewodniku Szybki start utworzono magazyn kluczy i klucz tajny przy użyciu szablonu usługi ARM oraz zweryfikowano wdrożenie. Aby dowiedzieć się więcej Key Vault i Azure Resource Manager, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)

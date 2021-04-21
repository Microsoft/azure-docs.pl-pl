---
title: Szybki start — tworzenie zasobu Azure Analysis Services serwera przy użyciu Azure Resource Manager szablonu
description: Przewodnik Szybki start pokazujący, jak Azure Analysis Services zasób serwera przy użyciu Azure Resource Manager szablonu.
author: minewiskan
ms.author: owend
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - subject-armqs - references_regions - mode-arm
ms.openlocfilehash: e7203f4b5890ab81cbf337c5f3201d85a3aef0c0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769373"
---
# <a name="quickstart-create-a-server---arm-template"></a>Szybki start: tworzenie serwera — szablon usługi ARM

W tym przewodniku Szybki start opisano, jak utworzyć zasób serwera Analysis Services subskrypcji platformy Azure przy użyciu szablonu Azure Resource Manager (szablonu usługi ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** odwiedź stronę [Bezpłatna wersja próbna platformy Azure,](https://azure.microsoft.com/offers/ms-azr-0044p/) aby utworzyć konto.
* **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory. Ponadto musisz zalogować się na platformie Azure przy użyciu konta należącego do tej dzierżawy usługi Azure Active Directory. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Pojedynczy [zasób Microsoft.AnalysisServices/servers](/azure/templates/microsoft.analysisservices/servers) z regułą zapory jest zdefiniowany w szablonie.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz następujący link Deploy to Azure (Wd wdrażaj na platformie Azure), aby zalogować się do platformy Azure i otworzyć szablon. Szablon służy do tworzenia zasobu serwera Analysis Services i określania właściwości wymaganych i opcjonalnych.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    Jeśli nie określono inaczej, użyj wartości domyślnych.

    * **Subskrypcja:** wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** kliknij **pozycję Utwórz nową,** a następnie wprowadź unikatową nazwę nowej grupy zasobów.
    * **Lokalizacja:** wybierz domyślną lokalizację dla zasobów utworzonych w grupie zasobów.
    * **Nazwa serwera:** wprowadź nazwę zasobu serwera. 
    * **Lokalizacja:** Ignoruj dla Analysis Services. Lokalizacja jest określona w lokalizacji serwera.
    * **Lokalizacja serwera:** wprowadź lokalizację Analysis Services serwera. Jest to często ten sam region co domyślna lokalizacja określona dla grupy zasobów, ale nie jest to wymagane. Na przykład **Północno-środkowe usa**. Aby uzyskać informacje o obsługiwanych [regionach, zobacz Analysis Services dostępności według regionów.](analysis-services-overview.md#availability-by-region)
    * **Nazwa SKU:** wprowadź nazwę sku dla serwera Analysis Services do utworzenia. Do wyboru: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Dostępność SKU zależy od regionu. Do oceny i testowania zaleca się użycie S0 lub D1.
    * **Pojemność:** wprowadź łączną liczbę wystąpień skalowania repliki zapytania w celu skalowania w zewnątrz. Skalowanie w zewnątrz więcej niż jednego wystąpienia jest obsługiwane tylko w wybranych regionach.
    * **Ustawienia zapory:** wprowadź reguły zapory dla ruchu przychodzącego do zdefiniowania dla serwera. Jeśli nie zostanie określony, zapora zostanie wyłączona.
    * **Backup Blob Container URI (Tworzenie** kopii zapasowej kontenera obiektów blob): wprowadź wartość URI sygnatury dostępu współdzielonego Azure Blob Storage kontenera z uprawnieniami do odczytu, zapisu i listy. Wymagane tylko wtedy, gdy zamierzasz używać kopii [zapasowej/przywracania.](analysis-services-backup.md)
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu serwera otrzymasz powiadomienie:

   ![Szablon usługi ARM, wdrażanie powiadomienia portalu](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Użyj Azure Portal lub Azure PowerShell, aby sprawdzić, czy grupa zasobów i zasób serwera zostały utworzone.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i zasób serwera nie Azure Portal już potrzebne, Azure PowerShell interfejsu wiersza polecenia platformy Azure lub serwera.

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

W tym przewodniku Szybki start za pomocą szablonu usługi ARM utworzyliśmy nową grupę zasobów i zasób Azure Analysis Services zasobów serwera. Po utworzeniu zasobu serwera przy użyciu szablonu należy wziąć pod uwagę następujące kwestie:

> [!div class="nextstepaction"]
> [Szybki start: Konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)   

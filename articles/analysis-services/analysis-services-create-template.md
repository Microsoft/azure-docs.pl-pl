---
title: Szybki Start — tworzenie zasobu serwera Azure Analysis Services przy użyciu szablonu Azure Resource Manager
description: Przewodnik Szybki Start przedstawiający sposób tworzenia zasobu serwera Azure Analysis Services przy użyciu szablonu Azure Resource Manager.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230793"
---
# <a name="quickstart-create-a-server---arm-template"></a>Szybki Start: Tworzenie szablonu serwera ARM

W tym przewodniku szybki start opisano sposób tworzenia zasobu serwera Analysis Services w ramach subskrypcji platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) , aby utworzyć konto.
* **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory. Ponadto musisz zalogować się na platformie Azure przy użyciu konta należącego do tej dzierżawy usługi Azure Active Directory. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

Pojedynczy zasób [Microsoft. AnalysisServices/Server](/azure/templates/microsoft.analysisservices/servers) z regułą zapory jest zdefiniowany w szablonie.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz następujące polecenie Wdróż w usłudze Azure link, aby zalogować się do platformy Azure i otworzyć szablon. Szablon służy do tworzenia zasobu serwera Analysis Services i określania wymaganych i opcjonalnych właściwości.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    O ile nie określono inaczej, użyj wartości domyślnych.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: kliknij pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów.
    * **Lokalizacja**: Wybierz domyślną lokalizację dla zasobów utworzonych w grupie zasobów.
    * **Nazwa serwera**: Wprowadź nazwę zasobu serwera. 
    * **Lokalizacja**: zignoruj dla Analysis Services. Lokalizacja jest określona w lokalizacji serwera.
    * **Lokalizacja serwera**: wprowadź lokalizację serwera Analysis Services. Jest to często ten sam region, co domyślna lokalizacja określona dla grupy zasobów, ale nie jest wymagana. Na przykład **Północno-środkowe stany USA**. W przypadku obsługiwanych regionów zobacz [Analysis Services dostępność według regionów](analysis-services-overview.md#availability-by-region).
    * **Nazwa jednostki SKU**: Wprowadź nazwę jednostki SKU dla serwera Analysis Services, który ma zostać utworzony. Wybierz spośród: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Dostępność jednostki SKU zależy od regionu. S0 lub D1 są zalecane do oceny i testowania.
    * **Pojemność**: wprowadź łączną liczbę wystąpień w poziomie repliki zapytań. Skalowanie w poziomie więcej niż jednego wystąpienia jest obsługiwane tylko w wybranych regionach.
    * **Ustawienia zapory**: wprowadź reguły zapory dla ruchu przychodzącego, które mają zostać zdefiniowane dla serwera. Jeśli nie zostanie określony, Zapora jest wyłączona.
    * **Identyfikator URI kontenera obiektów BLOB kopii zapasowej**: Wprowadź identyfikator URI sygnatury dostępu współdzielonego do prywatnego kontenera BLOB Storage platformy Azure z uprawnieniami odczyt, zapis i lista. Wymagane tylko wtedy, gdy zamierzasz używać funkcji [tworzenia kopii zapasowej/przywracania](analysis-services-backup.md).
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu serwera otrzymujesz powiadomienie:

   ![Szablon ARM, powiadomienie portalu wdrożenia](./media/analysis-services-create-template/notification.png)

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

Gdy grupa zasobów i zasób serwera nie będą już potrzebne, użyj Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

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

W tym przewodniku szybki start użyto szablonu ARM do utworzenia nowej grupy zasobów i zasobu serwera Azure Analysis Services. Po utworzeniu zasobu serwera przy użyciu szablonu należy wziąć pod uwagę następujące kwestie:

> [!div class="nextstepaction"]
> [Szybki start: Konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)   

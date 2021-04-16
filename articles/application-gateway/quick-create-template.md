---
title: 'Szybki start: bezpośredni ruch internetowy przy użyciu Resource Manager szablonu'
titleSuffix: Azure Application Gateway
description: W tym przewodniku Szybki start dowiesz się, jak za pomocą szablonu usługi Resource Manager utworzyć maszynę wirtualną, Azure Application Gateway który kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: dd100361ba5d4ff175e340ced782999e52c720c4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538444"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Szybki start: bezpośredni ruch internetowy za pomocą Azure Application Gateway — szablon usługi ARM

W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm) do utworzenia Azure Application Gateway. Następnie przetestuj bramę aplikacji, aby upewnić się, że działa prawidłowo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Możesz również ukończyć ten przewodnik Szybki start przy użyciu interfejsu [wiersza Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub interfejsu wiersza polecenia [platformy Azure.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Dla uproszczenia ten szablon tworzy prostą konfigurację z publicznym adresem IP frontonia, podstawowym odbiornikiem do hostowania pojedynczej lokacji w bramie aplikacji, podstawową regułą routingu żądań i dwiema maszynami wirtualnymi w puli zaplecza.

Szablon używany w tym przewodniku Szybki start pochodzi z szablonów [szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) jeden dla bramy aplikacji i dwa dla maszyn wirtualnych.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualmachines) dwie maszyny wirtualne
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkinterfaces) dwie dla maszyn wirtualnych
- [**Microsoft.Compute/virtualMachine/extensions:**](/azure/templates/microsoft.compute/virtualmachines/extensions) do konfigurowania usług IIS i stron sieci Web

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu usługi ARM na platformie Azure:

1. Wybierz **pozycję Deploy to Azure (Wd wdrażaj** na platformie Azure), aby zalogować się do platformy Azure i otworzyć szablon. Szablon tworzy bramę aplikacji, infrastrukturę sieci i dwie maszyny wirtualne w puli zaplecza z uruchomionymi usługami IIS.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Wybierz lub utwórz grupę zasobów, wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
3. Wybierz **pozycję Przeglądanie + tworzenie,** a następnie wybierz pozycję **Utwórz.**

   Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Chociaż usługi IIS nie są wymagane do utworzenia bramy aplikacji, są zainstalowane w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na stronie **Przegląd.** ![ Nagraj publiczny adres IP bramy aplikacji. Możesz też wybrać pozycję ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) **Wszystkie** zasoby, wprowadzić wartość *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybrać ją w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zaplecza.

   ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Odśwież przeglądarkę wiele razy. Powinny zostać wyświetlonych połączenia zarówno z myVM1, jak i myVM2.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Spowoduje to usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

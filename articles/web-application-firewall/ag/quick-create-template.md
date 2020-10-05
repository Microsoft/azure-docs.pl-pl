---
title: 'Szybki Start: Tworzenie usługi Azure WAF V2 na Application Gateway — Azure Resource Manager szablon'
titleSuffix: Azure Application Gateway
description: Dowiedz się, w jaki sposób używać szablonu szybkiego startu Azure Resource Manager (szablon ARM) do tworzenia zapory aplikacji sieci Web V2 na platformie Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 036503f95a7dc1aaa5690bacc01d07034e6d162f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91266959"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Szybki Start: Tworzenie usługi Azure WAF V2 na Application Gateway przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć zaporę aplikacji sieci Web platformy Azure w wersji 2 na Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon tworzy prostą zaporę aplikacji sieci Web V2 na platformie Azure Application Gateway. Obejmuje on publiczny adres IP frontonu IP, ustawienia protokołu HTTP, regułę z odbiornikiem podstawowym na porcie 80 i pulę zaplecza. Zostanie utworzona zasada WAF z regułą niestandardową, która blokuje ruch do puli zaplecza na podstawie typu dopasowania adresu IP.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses) : jeden dla bramy Application Gateway i dwie maszyny wirtualne.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dwie maszyny wirtualne
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dwie dla maszyn wirtualnych
- [**Microsoft. COMPUTE/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : Aby skonfigurować usługi IIS i strony sieci Web

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon ARM na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon umożliwia utworzenie bramy aplikacji, infrastruktury sieciowej i dwóch maszyn wirtualnych w puli zaplecza z uruchomionymi usługami IIS.

   [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów.
3. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Chociaż usługi IIS nie są wymagane do utworzenia bramy aplikacji, są zainstalowane na serwerach wewnętrznej bazy danych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła WAF V2 na bramie aplikacji.

Użyj usług do przetestowania bramy aplikacji:

1. Na stronie **przeglądowej** Znajdź publiczny adres IP bramy aplikacji. ![ Zarejestruj publiczny adres IP bramy aplikacji ](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) lub, wybierz pozycję **wszystkie zasoby**, wpisz *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybierz go w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Odpowiedź z **Niedozwolonym 403** sprawdza, czy WAF został pomyślnie utworzony i blokuje połączenia z pulą zaplecza.
4. Zmień regułę niestandardową, aby **zezwalać na ruch**.
  Uruchom następujący skrypt Azure PowerShell, zastępując nazwę grupy zasobów:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Wielokrotnie Odświeżaj przeglądarkę i powinny być widoczne połączenia zarówno myVM1, jak i myVM2.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu bramy aplikacji, Usuń grupę zasobów. Spowoduje to usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie bramy aplikacji za pomocą zapory aplikacji sieci Web przy użyciu Azure Portal](application-gateway-web-application-firewall-portal.md)
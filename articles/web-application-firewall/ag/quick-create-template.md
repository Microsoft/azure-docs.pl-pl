---
title: 'Szybki start: tworzenie szablonu usługi Azure WAF w wersji 2 w bramie aplikacji — szablon Menedżera zasobów'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć zaporę aplikacji sieci Web w wersji 2 w usłudze Azure Application Gateway za pomocą szablonu Menedżera zasobów.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617976"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Szybki start: tworzenie szablonu usługi Azure WAF w wersji 2 w bramie aplikacji — szablon Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu Menedżera zasobów do utworzenia zapory aplikacji sieci Web platformy Azure w wersji 2 na bramie aplikacji.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-web-application-firewall"></a>Tworzenie Zapory aplikacji sieci Web

Ten szablon tworzy prostą zaporę aplikacji sieci Web w wersji 2 w usłudze Azure Application Gateway. Obejmuje to publiczny adres IP frontend ip adres IP, ustawienia HTTP, reguła z odbiornikiem podstawowym na porcie 80 i puli wewnętrznej bazy danych. Zasady WAF z regułą niestandardową jest tworzony do blokowania ruchu do puli wewnętrznej bazy danych na podstawie typu dopasowania adresu IP.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Wiele zasobów platformy Azure są zdefiniowane w szablonie:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) jeden dla bramy aplikacji i dwa dla maszyn wirtualnych.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dwie maszyny wirtualne
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dwa dla maszyn wirtualnych
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : skonfigurowanie usług IIS i stron internetowych

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu Usługi Resource Manager na platformie Azure:

1. Wybierz **pozycję Wdrażanie na platformie Azure,** aby zalogować się na platformę Azure i otworzyć szablon. Szablon tworzy bramę aplikacji, infrastrukturę sieciową i dwie maszyny wirtualne w puli wewnętrznej bazy danych z uruchomionymi usługami IIS.

   [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Wybierz lub utwórz grupę zasobów.
3. Wybierz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz opcję **Kup.** Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Mimo że usługi IIS nie są wymagane do utworzenia bramy aplikacji, jest ona zainstalowana na serwerach wewnętrznej bazy danych w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła waf w wersji 2 na bramie aplikacji. 

Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na jej stronie **Przegląd.** ![Zarejestruj publiczny adres](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP bramy aplikacji Lub możesz wybrać wszystkie **zasoby**, wprowadź *myAGPublicIPAddress* w polu wyszukiwania, a następnie zaznacz go w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. A **403 Zakazana** odpowiedź sprawdza, czy WAF został pomyślnie utworzony i blokuje połączenia z puli wewnętrznej bazy danych.
4. Zmień regułę niestandardową, aby **zezwalać na ruch**.
  Uruchom następujący skrypt programu Azure PowerShell, zastępując nazwę grupy zasobów:
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

   Odśwież przeglądarkę wiele razy i powinieneś zobaczyć połączenia zarówno z myVM1, jak i myVM2.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne zasoby utworzone za pomocą bramy aplikacji, usuń grupę zasobów. Spowoduje to usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołanie polecenia `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie bramy aplikacji z zaporą aplikacji sieci Web przy użyciu portalu Azure](application-gateway-web-application-firewall-portal.md)
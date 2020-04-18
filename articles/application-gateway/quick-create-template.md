---
title: 'Szybki start: bezpośredni ruch internetowy przy użyciu szablonu Menedżera zasobów'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak użyć szablonu Menedżera zasobów do utworzenia bramy aplikacji platformy Azure, która kieruje ruch sieci web do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cce3ef20a93c6d7a24bfa312501d2f8cc8ed9273
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604903"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Szybki start: bezpośredni ruch internetowy za pomocą bramy aplikacji platformy Azure — szablon Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu Menedżera zasobów do utworzenia bramy aplikacji platformy Azure. Następnie należy przetestować bramę aplikacji, aby upewnić się, że działa poprawnie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki start można również ukończyć za pomocą [portalu Azure,](quick-create-portal.md) [usługi Azure PowerShell](quick-create-powershell.md)lub [interfejsu wiersza polecenia platformy Azure.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Dla uproszczenia ten szablon tworzy prostą konfigurację z publicznym adresem IP front-end, podstawowym odbiornikiem do hostowania pojedynczej lokacji w bramie aplikacji, podstawową regułą routingu żądań i dwiema maszynami wirtualnymi w puli wewnętrznej bazy danych.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Wiele zasobów platformy Azure są zdefiniowane w szablonie:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) jeden dla bramy aplikacji i dwa dla maszyn wirtualnych.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dwie maszyny wirtualne
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dwa dla maszyn wirtualnych
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : skonfigurowanie usług IIS i stron internetowych


### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu Usługi Resource Manager na platformie Azure:

1. Wybierz **pozycję Wdrażanie na platformie Azure,** aby zalogować się na platformę Azure i otworzyć szablon. Szablon tworzy bramę aplikacji, infrastrukturę sieciową i dwie maszyny wirtualne w puli wewnętrznej bazy danych z uruchomionymi usługami IIS.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Wybierz lub utwórz grupę zasobów, wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
3. Wybierz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz opcję **Kup.** Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Mimo że usługi IIS nie są wymagane do utworzenia bramy aplikacji, jest ona instalowana w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na jej stronie **Przegląd.** ![Zarejestruj publiczny adres](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP bramy aplikacji Lub możesz wybrać wszystkie **zasoby**, wprowadź *myAGPublicIPAddress* w polu wyszukiwania, a następnie zaznacz go w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie połączyć się z zapleczem.

   ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Odśwież przeglądarkę wiele razy i powinieneś zobaczyć połączenia zarówno z myVM1, jak i myVM2.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne zasoby utworzone za pomocą bramy aplikacji, usuń grupę zasobów. Spowoduje to usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołanie polecenia `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

---
title: 'Szybki Start: bezpośredni ruch internetowy przy użyciu szablonu Menedżer zasobów'
titleSuffix: Azure Application Gateway
description: W tym przewodniku szybki start dowiesz się, jak za pomocą szablonu Menedżer zasobów utworzyć Application Gateway platformy Azure, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 3ea5c891a0e3709c1ce469be2e9101a0825acfcb
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093875"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Szybki Start: bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway — szablon ARM

W tym przewodniku szybki start utworzysz Application Gateway platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM). Następnie przetestuj bramę aplikacji, aby upewnić się, że działa prawidłowo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

W celu uproszczenia ten szablon tworzy prostą konfigurację z publicznym adresem IP frontonu, podstawowym odbiornikiem obsługującym jedną lokację w bramie aplikacji, podstawową regułę routingu żądań i dwie maszyny wirtualne w puli zaplecza.

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses) : jeden dla bramy Application Gateway i dwie maszyny wirtualne.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : dwie maszyny wirtualne
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dwie dla maszyn wirtualnych
- [**Microsoft. COMPUTE/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : Aby skonfigurować usługi IIS i strony sieci Web

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Wdróż szablon ARM na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon umożliwia utworzenie bramy aplikacji, infrastruktury sieciowej i dwóch maszyn wirtualnych w puli zaplecza z uruchomionymi usługami IIS.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów, wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
3. Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz**.

   Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Mimo że Usługa IIS nie jest wymagana do utworzenia bramy aplikacji, jest zainstalowana w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Na stronie **przeglądowej** Znajdź publiczny adres IP bramy aplikacji. ![ Zarejestruj publiczny adres IP bramy aplikacji ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) lub, wybierz pozycję **wszystkie zasoby**, wpisz *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybierz go w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Prawidłowa odpowiedź weryfikuje, czy Brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zapleczem.

   ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Wielokrotnie Odświeżaj przeglądarkę i powinny być widoczne połączenia zarówno myVM1, jak i myVM2.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu bramy aplikacji, Usuń grupę zasobów. Spowoduje to usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

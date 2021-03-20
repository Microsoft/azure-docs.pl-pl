---
title: 'Szybki Start: Tworzenie zapory platformy Azure z wieloma publicznymi adresami IP — szablon Menedżer zasobów'
description: W tym przewodniku szybki start dowiesz się, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia zapory platformy Azure z wieloma publicznymi adresami IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 33d110f327a381241fd9dbf55996a6e3f9fd3a43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92057981"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>Szybki Start: Tworzenie zapory platformy Azure z wieloma publicznymi adresami IP — szablon ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM) do wdrożenia zapory platformy Azure z wieloma publicznymi adresami IP z prefiksu publicznego adresu IP. Wdrożona Zapora ma reguły dotyczące kolekcji reguł NAT, które zezwalają na połączenia RDP z dwiema maszynami wirtualnymi z systemem Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać więcej informacji na temat zapory platformy Azure z wieloma publicznymi adresami IP, zobacz [wdrażanie zapory platformy Azure z wieloma publicznymi adresami IP przy użyciu Azure PowerShell](deploy-multi-public-ip-powershell.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon służy do tworzenia zapory platformy Azure z dwoma publicznymi adresami IP wraz z zasobami niezbędnymi do obsługi zapory platformy Azure.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/publicIPPrefix**](/azure/templates/microsoft.network/publicipprefixes)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon ARM na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon umożliwia utworzenie zapory platformy Azure, infrastruktury sieciowej i dwóch maszyn wirtualnych.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie zapory platformy Azure z wieloma adresami publicznymi IP** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji 
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nową**, a następnie wybierz **przycisk OK**.
   - Lokalizacja: Wybierz lokalizację
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika dla konta użytkownika administratora 
   - Hasło administratora: wpisz hasło administratora lub klucz

3. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

W Azure Portal Przejrzyj wdrożone zasoby. Zanotuj publiczne adresy IP zapory.  

Użyj Podłączanie pulpitu zdalnego, aby nawiązać połączenie z publicznymi adresami IP zapory. Pomyślne połączenia przedstawiają reguły NAT zapory, które zezwalają na połączenie z serwerami zaplecza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą zapory, Usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md)

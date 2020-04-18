---
title: 'Szybki start: tworzenie zapory platformy Azure z wieloma publicznymi adresami IP — szablon Menedżera zasobów'
description: Dowiedz się, jak użyć szablonu Menedżera zasobów do utworzenia Zapory platformy Azure z wieloma publicznymi adresami IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605208"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Szybki start: tworzenie zapory platformy Azure z wieloma publicznymi adresami IP — szablon Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu Menedżera zasobów do wdrożenia zapory platformy Azure z wieloma publicznymi adresami IP.

Wdrożona zapora ma reguły zbierania reguł NAT, które zezwalają na połączenia RDP z dwoma maszynami wirtualnymi systemu Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać więcej informacji na temat Zapory platformy Azure z wieloma publicznymi adresami IP, zobacz [Wdrażanie Zapory platformy Azure z wieloma publicznymi adresami IP przy użyciu programu Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Tworzenie zapory platformy Azure

Ten szablon tworzy zaporę platformy Azure z dwoma publicznymi adresami IP wraz z zasobami niezbędnymi do obsługi zapory platformy Azure.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Wiele zasobów platformy Azure są zdefiniowane w szablonie:

- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageKondyje**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu Usługi Resource Manager na platformie Azure:

1. Wybierz **pozycję Wdrażanie na platformie Azure,** aby zalogować się na platformę Azure i otworzyć szablon. Szablon tworzy zaporę platformy Azure, infrastrukturę sieciową i dwie maszyny wirtualne.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie zapory platformy Azure z wieloma adresami publicznymi IP** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji 
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nowe**i wybierz **przycisk OK**.
   - Lokalizacja: Wybierz lokalizację
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika konta administratora 
   - Hasło administratora: wpisz hasło lub klucz administratora

3. Wybierz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz opcję **Kup.** Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

W witrynie Azure portal przejrzyj wdrożone zasoby. Zanotuj publiczne adresy IP zapory.  

Użyj funkcji Podłączanie pulpitu zdalnego do łączenia się z publicznymi adresami IP zapory. Pomyślne połączenia pokazują reguły NAT zapory, które umożliwiają połączenie z serwerami wewnętrznej bazy danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zasoby utworzone za pomocą zapory nie są już potrzebne, usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołanie polecenia `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu witryny Azure portal](tutorial-hybrid-portal.md)
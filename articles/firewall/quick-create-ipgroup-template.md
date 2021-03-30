---
title: 'Szybki Start: Tworzenie zapory platformy Azure i grup adresów IP — szablon Menedżer zasobów'
description: W tym przewodniku szybki start dowiesz się, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia zapory platformy Azure i grup adresów IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 7966cbe44fc8984cf3c3d9e2d08a11bf599ff158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079136"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Szybki Start: Tworzenie zapory platformy Azure i grup adresów IP — szablon ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM) do wdrożenia zapory platformy Azure z przykładowymi grupami adresów IP używanymi w regule sieci i aplikacji. Grupa adresów IP to zasób najwyższego poziomu, który umożliwia definiowanie i grupowanie adresów IP, zakresów i podsieci w pojedynczym obiekcie. Jest to przydatne w przypadku zarządzania adresami IP w regułach zapory platformy Azure. Możesz ręcznie wprowadzić adresy IP lub zaimportować je z pliku.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon służy do tworzenia zapory platformy Azure i grup adresów IP oraz niezbędnych zasobów do obsługi zapory platformy Azure.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon ARM na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon umożliwia utworzenie zapory platformy Azure, infrastruktury sieciowej i dwóch maszyn wirtualnych.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie zapory platformy Azure ze IpGroups** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji 
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nową**, a następnie wybierz **przycisk OK**.
   - Lokalizacja: Wybierz lokalizację
   - Nazwa Virtual Network: wpisz nazwę nowej sieci wirtualnej (VNet) 
   - Nazwa grupy IP 1: nazwa typu dla grupy adresów IP 1 
   - Nazwa grupy IP 2: nazwa typu dla grupy IP 2 
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika dla konta użytkownika administratora 
   - Uwierzytelnianie: Wybierz sshPublicKey lub hasło 
   - Hasło administratora: wpisz hasło administratora lub klucz

3. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

W Azure Portal Przejrzyj wdrożone zasoby, zwłaszcza reguły zapory, które korzystają z grup adresów IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Grupy adresów IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Reguły sieciowe.":::

Aby dowiedzieć się więcej o składni i właściwościach JSON zapory w szablonie, zobacz artykuł [Microsoft. Network azureFirewalls Template Reference](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą zapory, Usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure Portal](tutorial-hybrid-portal.md)
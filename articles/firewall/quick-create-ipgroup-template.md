---
title: 'Szybki start: tworzenie zapory platformy Azure i grup IP — szablon Menedżera zasobów'
description: Dowiedz się, jak utworzyć zaporę i grupy ADRESÓW IP za pomocą szablonu Menedżera zasobów.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605244"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Szybki start: tworzenie zapory platformy Azure i grup IP — szablon Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu Menedżera zasobów do wdrożenia zapory platformy Azure z przykładowymi grupami ADRESÓW IP używanymi w regule sieci i regułie aplikacji.

Grupa IP to zasób najwyższego poziomu, który umożliwia definiowanie i grupowanie adresów IP, zakresów i podsieci w jeden obiekt. Jest to przydatne do zarządzania adresami IP w regułach zapory platformy Azure. Można ręcznie wprowadzić adresy IP lub zaimportować je z pliku.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Tworzenie zapory platformy Azure i grup adresów IP

Ten szablon tworzy zaporę platformy Azure i grupy ADRESÓW IP wraz z zasobami niezbędnymi do obsługi Zapory platformy Azure.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Wiele zasobów platformy Azure są zdefiniowane w szablonie:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie zapory platformy Azure z grupami ip** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji 
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nowe**i wybierz **przycisk OK**.
   - Lokalizacja: Wybierz lokalizację
   - Nazwa sieci wirtualnej: wpisz nazwę nowej sieci wirtualnej 
   - Nazwa grupy IP 1: Nazwa typu grupy IP 1 
   - Nazwa grupy IP 2: Nazwa typu grupy IP 2 
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika konta administratora 
   - Uwierzytelnianie: wybierz sshPublicKey lub hasło 
   - Hasło administratora: wpisz hasło lub klucz administratora

3. Wybierz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz opcję **Kup.** Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

W witrynie Azure portal przejrzyj wdrożone zasoby, zwłaszcza reguły zapory korzystające z grup IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Grupy IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Reguły sieci.":::

Aby dowiedzieć się więcej o składni JSON i właściwościach zapory w szablonie, zobacz [Odwołanie do szablonu microsoft.network azureFirewalls](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zasoby utworzone za pomocą zapory nie są już potrzebne, usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołanie polecenia `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu witryny Azure portal](tutorial-hybrid-portal.md)
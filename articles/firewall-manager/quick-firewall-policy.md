---
title: 'Szybki start: tworzenie Azure Firewall i zasad zapory — Resource Manager szablonu'
description: W tym przewodniku Szybki start wdrożysz zasady Azure Firewall zapory.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529878"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Szybki start: tworzenie zasad Azure Firewall zapory — szablon usługi ARM

W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm), aby utworzyć Azure Firewall i zasady zapory. Zasady zapory mają regułę aplikacji, która zezwala na połączenia z usługą , oraz regułę, która zezwala na połączenia Windows Update użyciu tagu `www.microsoft.com` **FQDN WindowsUpdate.** Reguła sieci zezwala na połączenia UDP z serwerem czasu pod 13.86.101.172.

Ponadto grupy adresów IP są używane w zasadach do definiowania **źródłowych adresów** IP.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać informacje o Azure Firewall Manager, [zobacz Co to jest Azure Firewall Manager?](overview.md).

Aby uzyskać informacje o Azure Firewall, [zobacz Co to jest Azure Firewall?](../firewall/overview.md).

Aby uzyskać informacje o grupach adresów IP, zobacz [Grupy adresów IP w Azure Firewall](../firewall/ip-groups.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon tworzy sieć wirtualną piasty wraz z zasobami niezbędnymi do obsługi scenariusza.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu usługi ARM na platformie Azure:

1. Wybierz **pozycję Deploy to Azure (Wd** wdrażaj na platformie Azure), aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy sieć Azure Firewall wirtualnej sieci WAN i koncentratora wirtualnego, infrastrukturę sieci i dwie maszyny wirtualne.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. W portalu na stronie **Create a Firewall and FirewallPolicy with Rules and Ipgroups** (Tworzenie zapory i zasad zapory za pomocą reguł i grup adresów IP) wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz jedną z istniejących subskrypcji.
   - Grupa zasobów: wybierz jedną z istniejących grup zasobów lub wybierz **pozycję Utwórz nową** i wybierz przycisk **OK.**
   - Region: wybierz region.
   - Nazwa zapory: wpisz nazwę zapory.

3. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po zakończeniu wdrażania zobaczysz następujące podobne zasoby.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Wdrożone zasoby":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą zapory, usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Azure Firewall Manager omówienie zasad](policy-overview.md)

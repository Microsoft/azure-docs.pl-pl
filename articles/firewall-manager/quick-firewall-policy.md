---
title: 'Szybki Start: Tworzenie zapory platformy Azure i zasad zapory — Menedżer zasobów szablonu'
description: W tym przewodniku szybki start wdrożono zaporę platformy Azure i zasady zapory.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: 9cc263d311bd550a92a0c8f14ab5ce86d72e9ee3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633635"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Szybki Start: Tworzenie zapory platformy Azure i zasad zapory — szablon ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM) do utworzenia zapory platformy Azure i zasad zapory. Zasady zapory mają regułę aplikacji, która umożliwia nawiązywanie połączeń z `www.microsoft.com` regułą i umożliwia nawiązywanie połączeń Windows Update przy użyciu znacznika FQDN usługi **windowsupdate** . Reguła sieci umożliwia nawiązywanie połączeń UDP z serwerem czasu w 13.86.101.172.

Ponadto grupy adresów IP są używane w regułach do definiowania **źródłowych** adresów IP.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać informacje na temat Menedżera zapory platformy Azure, zobacz [co to jest Menedżer zapory platformy Azure?](overview.md).

Aby uzyskać informacje na temat zapory platformy Azure, zobacz [co to jest Zapora platformy Azure?](../firewall/overview.md).

Aby uzyskać informacje na temat grup adresów IP, zobacz [grupy adresów IP w zaporze platformy Azure](../firewall/ip-groups.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon umożliwia utworzenie centralnej sieci wirtualnej wraz z zasobami niezbędnymi do obsługi tego scenariusza.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon ARM na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon umożliwia utworzenie zapory platformy Azure, wirtualnej sieci WAN i wirtualnego centrum, infrastruktury sieciowej i dwóch maszyn wirtualnych.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie zapory i firewallpolicy z regułami i Ipgroups** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji.
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nową**, a następnie wybierz **przycisk OK**.
   - Region: Wybierz region.
   - Nazwa zapory: wpisz nazwę zapory.

3. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrażania zostaną wyświetlone następujące podobne zasoby.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Wdrożone zasoby":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą zapory, Usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Omówienie zasad Menedżera zapory platformy Azure](policy-overview.md)

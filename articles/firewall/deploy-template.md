---
title: 'Szybki Start: Tworzenie zapory platformy Azure przy użyciu szablonu Strefy dostępności-Menedżer zasobów'
description: Wdróż zaporę platformy Azure przy użyciu szablonu. Sieć wirtualna ma jedną sieć wirtualną z trzema podsieciami. Wdrożono dwie maszyny wirtualne z systemem Windows Server; pole skoku i serwer.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 9b9b7926caa717c1a02988ac7a927bd9bd39d52a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683709"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Szybki Start: wdrażanie zapory platformy Azure przy użyciu szablonu Strefy dostępności-Menedżer zasobów

W tym przewodniku szybki start użyjesz szablonu Menedżer zasobów do wdrożenia zapory platformy Azure w trzech Strefy dostępności. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Szablon tworzy testowe środowisko sieciowe za pomocą zapory. Sieć ma jedną sieć wirtualną (VNet) z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*i *JumpboxSubnet*. Każda podsieć *ServersSubnet* i *JumpboxSubnet* ma jedną, dwurdzeniową maszynę wirtualną z systemem Windows Server.

Zapora znajduje się w podsieci *AzureFirewallSubnet* i ma kolekcję reguł aplikacji z pojedynczą regułą, która umożliwia dostęp do programu `www.microsoft.com` .

Zdefiniowany przez użytkownika ruch sieciowy z podsieci *ServersSubnet* przez zaporę, w której są stosowane reguły zapory.

Aby uzyskać więcej informacji na temat zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Tworzenie zapory platformy Azure za pomocą Strefy dostępności

Ten szablon służy do tworzenia zapory platformy Azure z Strefy dostępności wraz z niezbędnymi zasobami do obsługi zapory platformy Azure.

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon umożliwia utworzenie zapory platformy Azure, infrastruktury sieciowej i dwóch maszyn wirtualnych.

   [![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. W portalu na stronie **Tworzenie konfiguracji piaskownicy zapory platformy Azure ze strefami** wpisz lub wybierz następujące wartości:
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wpisz nazwę grupy zasobów, a następnie wybierz **przycisk OK**. 
   - **Nazwa Virtual Network**: wpisz nazwę nowej sieci wirtualnej. 
   - **Nazwa użytkownika administratora**: wpisz nazwę użytkownika konta administratora.
   - **Hasło administratora**: wpisz hasło administratora. 

3. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Poznaj zasoby, które zostały utworzone za pomocą zapory.

Aby dowiedzieć się więcej o składni i właściwościach JSON zapory w szablonie, zobacz [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, możesz usunąć grupę zasobów, zaporę i wszystkie powiązane zasoby, uruchamiając `Remove-AzResourceGroup` polecenie programu PowerShell. Aby usunąć grupę zasobów o nazwie Moja *zasobów*, uruchom polecenie: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Nie usuwaj grupy zasobów i zapory, jeśli planujesz kontynuować pracę z samouczkiem dotyczącym monitorowania zapory. 

## <a name="next-steps"></a>Następne kroki

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](tutorial-diagnostics.md)
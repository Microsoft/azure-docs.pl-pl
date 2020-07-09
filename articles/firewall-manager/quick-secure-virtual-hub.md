---
title: 'Szybki Start: Zabezpieczanie wirtualnego koncentratora przy użyciu Menedżera zapory platformy Azure — Menedżer zasobów szablonu'
description: Dowiedz się, jak zabezpieczyć koncentrator wirtualny przy użyciu Menedżera zapory platformy Azure.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 6641cc29025d39ddff33e706dd9b1b0da517b884
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563700"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>Szybki Start: Zabezpieczanie koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure — Menedżer zasobów szablonu

W tym przewodniku szybki start użyjesz szablonu Menedżer zasobów, aby zabezpieczyć centrum wirtualne przy użyciu Menedżera zapory platformy Azure. Wdrożona Zapora ma regułę aplikacji, która umożliwia nawiązywanie połączeń z usługą `www.microsoft.com` . Dwie maszyny wirtualne z systemem Windows Server 2019 są wdrażane w celu przetestowania zapory. Jeden serwer skoku jest używany do nawiązywania połączenia z serwerem obciążeń. Z serwera obciążenia można łączyć się tylko z usługą `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać więcej informacji na temat Menedżera zapory platformy Azure, zobacz [co to jest Menedżer zapory platformy Azure?](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-secured-virtual-hub"></a>Tworzenie bezpiecznego centrum wirtualnego

Ten szablon umożliwia utworzenie bezpiecznego koncentratora wirtualnego przy użyciu Menedżera zapory platformy Azure wraz z zasobami wymaganymi do obsługi tego scenariusza.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon umożliwia utworzenie zapory platformy Azure, wirtualnej sieci WAN i wirtualnego centrum, infrastruktury sieciowej i dwóch maszyn wirtualnych.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. W portalu na stronie **zabezpieczone centra wirtualne** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz z istniejących subskrypcji 
   - Grupa zasobów: wybierz z istniejących grup zasobów lub wybierz pozycję **Utwórz nową**, a następnie wybierz **przycisk OK**.
   - Lokalizacja: Wybierz lokalizację
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika dla konta użytkownika administratora 
   - Hasło administratora: wpisz hasło administratora lub klucz

3. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Teraz Przetestuj reguły zapory, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. W Azure Portal Sprawdź ustawienia sieci dla maszyny wirtualnej **obciążenia** i zanotuj prywatny adres IP.
2. Połącz pulpit zdalny z maszyną **wirtualną i zaloguj się.** W tym miejscu Otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **SRV** .

3. Otwórz program Internet Explorer i przejdź do `www.microsoft.com`.
4. Wybierz pozycję **OK**  >  **Zamknij** na stronie Alerty zabezpieczeń programu Internet Explorer.

   Powinna zostać wyświetlona strona główna firmy Microsoft.

5. Przejdź do `www.google.com`.

   Dostęp powinien zostać zablokowany przez zaporę.

Teraz sprawdzono, że reguły zapory działają:

* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą zapory, Usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o dostawcach partnerów zabezpieczeń](trusted-security-partners.md)

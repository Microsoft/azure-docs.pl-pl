---
title: 'Szybki start: zabezpieczanie koncentratora wirtualnego przy użyciu Azure Firewall Manager — Resource Manager szablonu'
description: Z tego przewodnika Szybki start dowiesz się, jak zabezpieczyć koncentrator wirtualny przy użyciu Azure Firewall Manager.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 88b961f269c89a1e19c0fd4419ffdc33706423de
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529645"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Szybki start: zabezpieczanie koncentratora wirtualnego przy użyciu Azure Firewall Manager — szablon usługi ARM

W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm) do zabezpieczenia koncentratora wirtualnego przy użyciu Azure Firewall Manager. Wdrożona zapora ma regułę aplikacji, która zezwala na połączenia z usługą `www.microsoft.com` . Do testowania zapory są wdrażane dwie maszyny wirtualne z systemem Windows Server 2019. Jeden serwer przeskoku jest używany do nawiązywania połączenia z serwerem obciążenia. Z serwera obciążenia można łączyć się tylko z usługą `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby uzyskać więcej informacji na Azure Firewall Manager, [zobacz Co to jest Azure Firewall Manager?](overview.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon tworzy bezpieczny koncentrator wirtualny przy Azure Firewall Manager, wraz z zasobami niezbędnymi do obsługi scenariusza.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu usługi ARM na platformie Azure:

1. Wybierz **pozycję Deploy to Azure (Wd** wdrażaj na platformie Azure), aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy sieć Azure Firewall wirtualnej sieci WAN i koncentratora wirtualnego, infrastrukturę sieci i dwie maszyny wirtualne.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. W portalu na stronie **Zabezpieczone koncentratory wirtualne** wpisz lub wybierz następujące wartości:
   - Subskrypcja: wybierz jedną z istniejących subskrypcji 
   - Grupa zasobów: wybierz jedną z istniejących grup zasobów lub wybierz **pozycję Utwórz nową** i wybierz przycisk **OK.**
   - Lokalizacja: wybierz lokalizację
   - Nazwa użytkownika administratora: wpisz nazwę użytkownika konta użytkownika administratora 
   - Hasło administratora: wpisz hasło lub klucz administratora

3. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**. Wdrożenie może potrwać 10 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Teraz przetestuj reguły zapory, aby potwierdzić, że działa zgodnie z oczekiwaniami.

1. Na stronie Azure Portal ustawienia sieci dla maszyny wirtualnej **Workload-Srv** i zanotuj prywatny adres IP.
2. Połącz pulpit zdalny z **maszyną wirtualną Jump-Srv** i zaloguj się. W tym miejscu otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **Workload-Srv.**

3. Otwórz program Internet Explorer i przejdź do `www.microsoft.com`.
4. Wybierz **przycisk OK**  >  **Zamknij** na Internet Explorer alerty zabezpieczeń.

   Powinna zostać wyświetlony strona główna firmy Microsoft.

5. Przejdź na stronę `www.google.com`.

   Dostęp powinien zostać zablokowany przez zaporę.

Po sprawdzeniu, czy reguły zapory działają:

- Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą zapory, usuń grupę zasobów. Spowoduje to usunięcie zapory i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o dostawcach partnerów zabezpieczeń](trusted-security-partners.md)

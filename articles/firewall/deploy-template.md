---
title: Wdrażanie usługi Azure Firewall przy użyciu szablonu
description: Wdróż zaporę platformy Azure przy użyciu szablonu. Utworzona sieć ma jedną sieć wirtualną z trzema podsieciami. Wdrożone są dwa podstawowe maszyny wirtualne z systemem Windows Server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74169195"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Wdrażanie usługi Azure Firewall przy użyciu szablonu

[Szablon Konfiguracja piaskownicy tworzenia AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) tworzy środowisko sieci testowej z zaporą. Sieć ma jedną sieć wirtualną (VNet) z trzema podsieciami: *AzureFirewallSubnet*, *ServersSubnet*i *JumpboxSubnet*. Każda podsieć *ServersSubnet* i *JumpboxSubnet* ma jedną, dwurdzeniową maszynę wirtualną z systemem Windows Server.

Zapora znajduje się w podsieci *AzureFirewallSubnet* i ma kolekcję reguł aplikacji z pojedynczą regułą, która umożliwia dostęp do `www.microsoft.com`programu.

Zdefiniowany przez użytkownika ruch sieciowy z podsieci *ServersSubnet* przez zaporę, w której są stosowane reguły zapory.

Aby uzyskać więcej informacji na temat zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Użycie szablonu do wdrożenia zapory platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

**Aby zainstalować i wdrożyć zaporę platformy Azure przy użyciu szablonu:**

1. Uzyskaj dostęp do szablonu [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)pod adresem.
   
1. Przeczytaj wprowadzenie i, gdy wszystko jest gotowe do wdrożenia, wybierz pozycję **Wdróż na platformie Azure**.
   
1. W razie potrzeby zaloguj się do witryny Azure Portal. 

1. W portalu na stronie **Tworzenie konfiguracji piaskownicy AzureFirewall** wpisz lub wybierz następujące wartości:
   
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wpisz nazwę grupy zasobów, a następnie wybierz **przycisk OK**. 
   - **Nazwa Virtual Network**: wpisz nazwę nowej sieci wirtualnej. 
   - **Nazwa użytkownika administratora**: wpisz nazwę użytkownika konta administratora.
   - **Hasło administratora**: wpisz hasło administratora. 
   
1. Przeczytaj warunki i postanowienia, a następnie wybierz **Akceptuję warunki i postanowienia podane powyżej**.
   
1. Wybierz pozycję **Kup**.
   
   Utworzenie zasobów potrwa kilka minut. 
   
1. Poznaj zasoby, które zostały utworzone za pomocą zapory. 

Aby dowiedzieć się więcej o składni i właściwościach JSON zapory w szablonie, zobacz [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, możesz usunąć grupę zasobów, zaporę i wszystkie powiązane zasoby, uruchamiając polecenie programu PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Aby usunąć grupę zasobów o nazwie Moja *zasobów*, uruchom polecenie: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Nie usuwaj jeszcze grupy zasobów i zapory, jeśli planujesz kontynuować pracę z samouczkiem dotyczącym monitorowania zapory. 

## <a name="next-steps"></a>Następne kroki

Następnie można monitorować Dzienniki zapory platformy Azure:

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)

---
title: Szablon ARM usługi link prywatny Azure
description: Szablon ARM usługi link prywatny
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237129"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>Tworzenie usługi link prywatny — szablon Menedżer zasobów

W tym przewodniku szybki start utworzysz usługę linku prywatnego przy użyciu szablonu Menedżer zasobów.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego

Ten szablon służy do tworzenia usługi linku prywatnego.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : usługa link prywatny umożliwiająca udostępnianie prywatnie usługi
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : prywatny punkt końcowy w celu uzyskania dostępu do prywatnej usługi
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : moduł równoważenia obciążenia, który udostępnia maszyny wirtualne, które obsługują usługę
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 2 maszyny wirtualne, takie, które obsługują usługę i jeden do testowania połączenia z prywatnym punktem końcowym
- [**Microsoft. COMPUTE/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : rozszerzenie instalujące serwer sieci Web
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : jeden dla każdej maszyny wirtualnej
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIpAddresses) : 2 publiczny adres IP, po jednym dla każdej maszyny wirtualnej
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 interfejsy sieciowe, jeden dla każdej maszyny wirtualnej

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon umożliwia utworzenie maszyny wirtualnej, standardowego modułu równoważenia obciążenia, usługi linku prywatnego, prywatnego punktu końcowego, sieci i maszyny wirtualnej do zweryfikowania.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów,
3. Wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
4. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

> [!NOTE]
> szablon ARM generuje unikatową nazwę zasobu maszyny wirtualnej myConsumerVm<b>{unikatowy}</b> . Zastąp <b>{unikatowy identyfikator}</b> wygenerowanej wartości.

### <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną _myConsumerVm {unikatowym}_ z Internetu w następujący sposób:

1.  Na pasku wyszukiwania portalu wprowadź _myConsumerVm {unikatowy}_.

2.  Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

3.  Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (_rdp_), który zostanie pobrany na komputer.

4.  Otwórz pobrany \* plik RDP.

    a. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    b. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.
    
    > [!NOTE]
    > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

5.  Wybierz przycisk **OK**.

6.  Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

7.  Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

### <a name="access-the-http-service-privately-from-the-vm"></a>Prywatna dostęp do usługi http z maszyny wirtualnej

W tej sekcji połączysz się z usługą http z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

1.  W Pulpit zdalny _myConsumerVm {unikatowy}_
2.  Otwórz przeglądarkę i wprowadź adres prywatnego punktu końcowegohttp://10.0.0.5/
3.  Zostanie wyświetlona domyślna strona usług IIS

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą usługi link prywatny, Usuń grupę zasobów. Spowoduje to usunięcie usługi link prywatny i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)

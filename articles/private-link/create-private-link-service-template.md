---
title: Tworzenie prywatnej usługi linkowej w ramach prywatnego linku platformy Azure
description: W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby utworzyć usługę łącza prywatnego.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817616"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>Szybki Start: Tworzenie usługi linku prywatnego przy użyciu szablonu Azure Resource Manager

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby utworzyć usługę łącza prywatnego.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Możesz również ukończyć ten przewodnik Szybki Start przy użyciu [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](create-private-link-service-cli.md).

## <a name="prerequisite"></a>Wymaganie wstępne

Potrzebujesz konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego

Ten szablon służy do tworzenia usługi linku prywatnego.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): istnieje jedna sieć wirtualna dla każdej maszyny wirtualnej.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): moduł równoważenia obciążenia, który udostępnia maszyny wirtualne, które obsługują usługę.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): Istnieją dwa interfejsy sieciowe — jeden dla każdej maszyny wirtualnej.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): Istnieją dwie maszyny wirtualne, na których jest obsługiwana usługa, i jeden, który testuje połączenie z prywatnym punktem końcowym.
- [**Microsoft. COMPUTE/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): rozszerzenie instalujące serwer sieci Web.
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): usługa link prywatny do udostępniania usługi.
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIpAddresses): Istnieją dwa publiczne adresy IP, po jednym dla każdej maszyny wirtualnej.
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): prywatny punkt końcowy do uzyskiwania dostępu do usługi.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Poniżej przedstawiono sposób wdrażania szablonu Azure Resource Manager na platformie Azure:

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz pozycję **Wdróż na platformie Azure**. Ten szablon umożliwia utworzenie maszyny wirtualnej, standardowego modułu równoważenia obciążenia, usługi linku prywatnego, prywatnego punktu końcowego, sieci i maszyny wirtualnej do zweryfikowania.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów.
3. Wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
4. Zapoznaj się z instrukcjami dotyczącymi warunków i postanowień. Jeśli zgadzasz się, wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**  >  **Purchase**.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

> [!NOTE]
> Szablon Azure Resource Manager generuje unikatową nazwę zasobu maszyny wirtualnej myConsumerVm<b>{unikatowy}</b> . Zastąp wygenerowaną wartość **{unikatowy}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną _myConsumerVm {unikatowym}_ z Internetu w następujący sposób:

1.  Na pasku wyszukiwania portalu wprowadź _myConsumerVm {unikatowy}_.

2.  Wybierz pozycję **Połącz**. Zostanie otwarte okno **łączenie z maszyną wirtualną** .

3.  Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (_rdp_), który zostanie pobrany na komputer.

4.  Otwórz pobrany plik RDP.

    a. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    b. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.
    
    > [!NOTE]
    > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

5.  Wybierz przycisk **OK**.

6.  Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

7.  Po wyświetleniu pulpitu maszyny wirtualnej Zminimalizuj go, aby wrócić do pulpitu lokalnego.

### <a name="access-the-http-service-privately-from-the-vm"></a>Prywatna dostęp do usługi http z maszyny wirtualnej

Poniżej przedstawiono sposób nawiązywania połączenia z usługą http z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

1.  Przejdź do Pulpit zdalny _myConsumerVm {unikatowy}_.
2.  Otwórz przeglądarkę i wprowadź adres prywatnego punktu końcowego: http://10.0.0.5/ .
3.  Zostanie wyświetlona domyślna strona usług IIS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą usługi link prywatny, Usuń grupę zasobów. Spowoduje to usunięcie usługi link prywatny i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łączu prywatnym platformy Azure](private-link-overview.md).

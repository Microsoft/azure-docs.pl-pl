---
title: 'Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia — szablon platformy Azure'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia modułu równoważenia obciążenia przy użyciu szablonu Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 378ab88f4dee0c725e89f77cc6b2ffe049ff877a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008439"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu ARM

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. 

W tym przewodniku szybki start przedstawiono sposób wdrażania standardowego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych.

Użycie szablonu ARM powoduje przeliczenie mniejszej liczby kroków do innych metod wdrażania.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/).

Jednostki SKU modułu równoważenia obciążenia i publicznego adresu IP muszą być zgodne. Podczas tworzenia standardowego modułu równoważenia obciążenia należy również utworzyć nowy, publiczny adres IP, który jest skonfigurowany jako fronton dla usługi równoważenia obciążenia w warstwie Standardowa. Jeśli chcesz utworzyć podstawowy moduł równoważenia obciążenia, użyj [tego szablonu](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Firma Microsoft zaleca używanie standardowej jednostki SKU dla obciążeń produkcyjnych.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses): w przypadku usługi równoważenia obciążenia, hosta bastionu i dla każdej z trzech maszyn wirtualnych.
- [**Microsoft. Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft. COMPUTE/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): służy do konfigurowania programu Internet Information Server (IIS) i stron sieci Web.

Aby znaleźć więcej szablonów związanych z Azure Load Balancer, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

   Wdrożenie szablonu tworzy trzy strefy dostępności. Strefy dostępności są obsługiwane tylko w [określonych regionach](../availability-zones/az-overview.md). Użyj jednego z obsługiwanych regionów. Jeśli nie masz pewności, wprowadź **centralną**.

   Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** . Wymagana jest nazwa grupy zasobów w następnej sekcji.

Wdrożenie szablonu trwa około 10 minut. Po zakończeniu dane wyjściowe są podobne do:

![Szablon platformy Azure usługa Load Balancer w warstwie Standardowa Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell służy do wdrożenia szablonu. Można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Wybierz moduł równoważenia obciążenia. Nazwa domyślna to nazwa projektu z dołączonym **-lb** .

1. Skopiuj tylko część adresu IP z publicznego adresu IP, a następnie wklej ją na pasku adresu przeglądarki.

   ![Publiczny adres IP szablonu usługi równoważenia obciążenia w warstwie Standardowa Azure Menedżer zasobów](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    W przeglądarce zostanie wyświetlona domyślna strona serwera sieci Web Internet Information Services (IIS).

   ![Internetowy serwer usług IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można wymusić odświeżenie przeglądarki sieci Web od komputera klienckiego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń: 

* Grupa zasobów
* Moduł równoważenia obciążenia
* Powiązane zasoby

Przejdź do Azure Portal, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Utworzono sieć wirtualną modułu równoważenia obciążenia i maszyn wirtualnych.
* Utworzono hosta usługi Azure bastionu na potrzeby zarządzania.
* Utworzono standardowy moduł równoważenia obciążenia i dołączono do niego maszyny wirtualne.
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej, przejdź do samouczków dotyczących Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

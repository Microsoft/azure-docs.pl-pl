---
title: 'Szybki Start: Tworzenie strefy Azure DNS i rekordu Azure Resource Manager szablonu (szablon ARM)'
titleSuffix: Azure DNS
description: Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. Ten przewodnik Szybki Start umożliwia tworzenie i zarządzanie pierwszą strefą i rekordem DNS przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 986258631d47989e5be5e738da86f844283ce706
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093824"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Szybki Start: Tworzenie strefy Azure DNS i rekordu przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia strefy DNS zawierającej rekord A.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

W tym przewodniku szybki start utworzysz unikatową strefę DNS z sufiksem * <span>azurequickstart.</span> Organizacja*. Rekord *A* wskazujący na dwa adresy IP również zostanie umieszczony w strefie.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Dwa zasoby platformy Azure zostały zdefiniowane w szablonie:

* [**Microsoft. Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
* [**Microsoft. Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A) (używany do tworzenia rekordu A w strefie)

Aby znaleźć więcej szablonów związanych z usługą Azure Traffic Manager, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy strefę z jednym rekordem A wskazującą dwa adresy IP. Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** .

    Wdrożenie szablonu trwa kilka sekund. Po zakończeniu dane wyjściowe są podobne do:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Szablon Menedżer zasobów strefy Azure DNS dane wyjściowe wdrożenia programu PowerShell":::

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Grupa zasobów powinna zawierać następujące zasoby:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Szablon Menedżer zasobów strefy Azure DNS dane wyjściowe wdrożenia programu PowerShell":::

1. Wybierz strefę DNS z sufiksem ** <span>azurequickstart.</span> Organizacja** , aby sprawdzić, czy strefa jest prawidłowo tworzona z rekordem **a** , odwołujący się do wartości **1.2.3.4** i **1.2.3.5**.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Szablon Menedżer zasobów strefy Azure DNS dane wyjściowe wdrożenia programu PowerShell":::

1. Skopiuj jedną z nazw serwerów nazw z poprzedniego kroku.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.<dns zone name> <name server name>
   ```

   Na przykład:

   ```
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Powinien wyglądać podobnie do poniższego zrzutu ekranu:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Szablon Menedżer zasobów strefy Azure DNS dane wyjściowe wdrożenia programu PowerShell":::

Nazwa hosta **www<span>. 2lwynbseszpam. azurequickstart.</span> Organizacja** jest rozpoznawana jako **1.2.3.4** i **1.2.3.5**, tak jak została skonfigurowana. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu strefy DNS, Usuń grupę zasobów. Spowoduje to usunięcie strefy DNS i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Strefa DNS
* Rekord A

Po utworzeniu pierwszej strefy i rekordu DNS przy użyciu szablonu Azure Resource Manager można utworzyć rekordy dla aplikacji sieci Web w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

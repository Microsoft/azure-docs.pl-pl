---
title: 'Szybki start: tworzenie strefy Azure DNS rekordu — szablon Azure Resource Manager (szablon usługi ARM)'
titleSuffix: Azure DNS
description: Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. W tym przewodniku Szybki start krok po kroku po raz pierwszy utworzysz strefę i rekord DNS przy użyciu Azure Resource Manager (szablonu usługi ARM).
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539025"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Szybki start: tworzenie strefy Azure DNS rekordu przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak utworzyć strefę DNS z rekordem za pomocą szablonu usługi Azure Resource Manager `A` (arm).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

W tym przewodniku Szybki start utworzysz unikatową strefę DNS z sufiksem `azurequickstart.org` . Rekord, `A` który wskaże dwa adresy IP, również zostanie umieszczony w strefie.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A:**](/azure/templates/microsoft.network/dnsZones/A)służy do tworzenia `A` rekordu w strefie.

Aby znaleźć więcej szablonów powiązanych z Azure Traffic Manager, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj w** poniższym bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Zaczekaj, aż zostanie wyświetlony monit z konsoli.

1. Wybierz **pozycję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy strefę z jednym `A` rekordem, który wskaże dwa adresy IP. Nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

    Wdrożenie szablonu może potrwać kilka sekund. Po zakończeniu dane wyjściowe będą podobne do:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS danych wyjściowych Resource Manager wdrożenia szablonu programu PowerShell":::

Azure PowerShell służy do wdrażania szablonu. Oprócz interfejsu Azure PowerShell można również użyć interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

1. Grupa zasobów powinna zawierać następujące zasoby widoczne tutaj:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Grupa zasobów wdrożenia strefy DNS":::

1. Wybierz strefę DNS z sufiksem , aby sprawdzić, czy strefa została utworzona prawidłowo z rekordem odwołującym `azurequickstart.org` się do wartości i `A` `1.2.3.4` `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Wdrażanie strefy DNS":::

1. Skopiuj jedną z nazw serwerów nazw z poprzedniego kroku.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Na przykład:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Powinien zostać wyświetlony ekran podobny do poniższego:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Nslookup strefy DNS":::

Nazwa hosta `www.2lwynbseszpam.azurequickstart.org` jest rozpoznaowana jako `1.2.3.4` i , tak jak ją `1.2.3.5` skonfigurowano. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą strefy DNS, usuń grupę zasobów. Spowoduje to usunięcie strefy DNS i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

- Strefa DNS
- `A` Rekord

Teraz, po utworzeniu pierwszej strefy i rekordu DNS przy użyciu szablonu usługi ARM, możesz utworzyć rekordy dla aplikacji internetowej w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

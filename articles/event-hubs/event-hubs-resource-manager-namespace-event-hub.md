---
title: 'Szybki Start: tworzenie centrum zdarzeń z grupą konsumentów — Azure Event Hubs'
description: 'Szybki Start: tworzenie przestrzeni nazw Event Hubs z centrum zdarzeń i grupą odbiorców przy użyciu szablonów Azure Resource Manager'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8a1ad9a2b2ce7a8e9e5911cca18b297378a966bd
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315657"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Szybki Start: tworzenie centrum zdarzeń przy użyciu szablonu Azure Resource Manager

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md). W tym przewodniku szybki start utworzysz centrum zdarzeń przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/management/overview.md). Wdrażasz szablon Azure Resource Manager, aby utworzyć przestrzeń nazw typu [Event Hubs](event-hubs-what-is-event-hubs.md)z jednym centrum zdarzeń.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/).

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft. EventHub/przestrzenie nazw**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft. EventHub/przestrzenie nazw/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby wdrożyć szablon:

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Utworzenie centrum zdarzeń trwa kilka chwil.

1. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
1. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby zweryfikować wdrożenie, możesz otworzyć grupę zasobów z poziomu [Azure Portal](https://portal.azure.com)lub użyć następującego skryptu Azure PowerShell.  Jeśli Cloud Shell nadal jest otwarty, nie musisz kopiować/uruchamiać pierwszego wiersza (odczyt-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Jeśli Cloud Shell nadal jest otwarty, nie musisz kopiować/uruchamiać pierwszego wiersza (odczyt-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie Event Hubs przestrzeni nazw oraz centrum zdarzeń w przestrzeni nazw. Aby uzyskać instrukcje krok po kroku dotyczące wysyłania zdarzeń do (lub) odbierania zdarzeń z centrum zdarzeń, zobacz samouczki **wysyłania i odbierania zdarzeń** :

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Przejdź](event-hubs-go-get-started-send.md)
- [C (tylko wysyłanie)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko odbieranie)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

---
title: Tworzenie przestrzeni nazw Azure Service Bus przy użyciu szablonu
description: Tworzenie przestrzeni nazw komunikatów Service Bus przy użyciu szablonu Azure Resource Manager
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067396"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw Service Bus przy użyciu szablonu Azure Resource Manager

Dowiedz się, jak wdrożyć szablon Azure Resource Manager, aby utworzyć Service Bus przestrzeni nazw. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [dokumentację Azure Resource Manager](../azure-resource-manager/index.yml).

Następujące szablony są również dostępne do tworzenia Service Bus przestrzenie nazw:

* [Tworzenie przestrzeni nazw Service Bus przy użyciu kolejki](./service-bus-resource-manager-namespace-queue.md)
* [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](./service-bus-resource-manager-namespace-topic.md)
* [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](./service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

W tym przewodniku szybki start użyjesz [istniejącego szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Aby utworzyć przestrzeń nazw usługi Service Bus, wdrażając szablon:

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do usługi Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Nazwa grupy zasobów to nazwa przestrzeni nazw usługi Service Bus z dołączoną **RG** .

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

Utworzenie centrum zdarzeń trwa kilka chwil.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby wyświetlić wdrożoną przestrzeń nazw usługi Service Bus, można otworzyć grupę zasobów z poziomu Azure Portal lub użyć następującego skryptu Azure PowerShell. Jeśli usługa Cloud Shell jest nadal otwarta, nie musisz kopiować/uruchamiać pierwszego i drugiego wiersza następującego skryptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell służy do wdrażania szablonu w tym samouczku. Aby poznać inne metody wdrażania szablonów, zobacz:

* Za [pomocą Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Za [pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).
* Za [pomocą interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Jeśli usługa Cloud Shell jest nadal otwarta, nie musisz kopiować/uruchamiać pierwszego i drugiego wiersza następującego skryptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie przestrzeni nazw Service Bus. Zobacz inne Przewodniki Szybki Start, aby dowiedzieć się, jak tworzyć kolejki, tematy/subskrypcje i korzystać z nich:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Wprowadzenie do tematów usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
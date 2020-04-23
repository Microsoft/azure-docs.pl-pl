---
title: Wdrażanie szablonów aplikacji logiki
description: Dowiedz się, jak wdrożyć szablony usługi Azure Resource Manager utworzone dla aplikacji logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086440"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Wdrażanie szablonów usługi Azure Resource Manager dla aplikacji logiki azure

Po utworzeniu szablonu usługi Azure Resource Manager dla aplikacji logiki można wdrożyć szablon w następujących sposób:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Interfejs wiersza polecenia platformy Azure](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Wdrażanie za pośrednictwem witryny Azure portal

Aby automatycznie wdrożyć szablon aplikacji logiki na platformie Azure, można wybrać następujący przycisk **Wdrażanie na platformie Azure,** który loguje się do witryny Azure portal i monituje o informacje na temat aplikacji logiki. Następnie można wprowadzić wszelkie niezbędne zmiany w szablonie aplikacji logiki lub parametrów.

[![Wdrażanie na platformie Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Na przykład po zalogowaniu się do witryny Azure portal zostanie wyświetlony monit o podanie następujących informacji:

* Nazwa subskrypcji platformy Azure
* Grupa zasobów, której chcesz użyć
* Lokalizacja aplikacji logiki
* Nazwa aplikacji logiki
* A test URI
* Akceptacja określonych warunków

Więcej informacji można znaleźć w następujących tematach:

* [Omówienie: automatyzacja wdrażania aplikacji logiki za pomocą szablonów usługi Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager i witryny Azure portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

Aby wdrożyć szablon aplikacji logiki z projektu grupy zasobów platformy Azure, który został utworzony przy użyciu programu Visual Studio, wykonaj następujące [kroki, aby ręcznie wdrożyć aplikację logiki](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) na platformie Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Aby wdrożyć w określonej *grupie zasobów platformy Azure,* należy użyć następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Więcej informacji można znaleźć w następujących tematach:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć w określonej *grupie zasobów platformy Azure,* należy użyć następującego polecenia:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Więcej informacji można znaleźć w następujących tematach:

* [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Wdrażanie za pomocą usługi Azure DevOps

Aby wdrożyć szablony aplikacji logiki i zarządzać środowiskami, zespoły często używają narzędzia, takiego jak [Potoki platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) w [usłudze Azure DevOps.](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) Usługa Azure Pipelines udostępnia [zadanie wdrażania grupy zasobów platformy Azure,](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) które można dodać do dowolnego potoku kompilacji lub wydania. Aby autoryzacja do wdrażania i generowania potoku wydania, trzeba również [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD) . Dowiedz się więcej o [używaniu podmiotów korzystających z usług Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)

Aby uzyskać więcej informacji na temat ciągłej integracji i ciągłego wdrażania (CI/CD) dla szablonów usługi Azure Resource Manager za pomocą potoków platformy Azure, zobacz następujące tematy i przykłady:

* [Integruj szablony usługi Resource Manager za pomocą potoków platformy Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Samouczek: Ciągła integracja szablonów usługi Azure Resource Manager z potokami platformy Azure](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Przykład: Łączenie się z kolejkami usługi Azure Service Bus z aplikacji logiki azure i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Łączenie się z kontami usługi Azure Storage z usługi Azure Logic Apps i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Konfigurowanie akcji aplikacji funkcji dla usługi Azure Logic Apps i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Połącz się z kontem integracji z usługi Azure Logic Apps i wdrażaj za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Przykład: organizowanie potoków platformy Azure przy użyciu aplikacji logiki azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Oto ogólne kroki wysokiego poziomu dotyczące korzystania z usługi Azure Pipelines:

1. W usłudze Azure Pipelines utwórz pusty potok.

1. Wybierz zasoby potrzebne do potoku, takie jak szablon aplikacji logiki i pliki parametrów szablonu, które są generowane ręcznie lub jako część procesu kompilacji.

1. W przypadku zadania agenta znajdź i dodaj zadanie **wdrażania grupy zasobów platformy Azure.**

   ![Dodawanie zadania "Wdrażanie grupy zasobów platformy Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Skonfiguruj za pomocą [jednostki usługi](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Dodaj odwołania do plików szablonów aplikacji logiki i parametrów szablonu.

1. Kontynuuj tworzenie kroków w procesie wydawania dla dowolnego innego środowiska, automatycznego testu lub osób zatwierdzających w razie potrzeby.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autoryzowanie połączeń OAuth

Po wdrożeniu aplikacja logiki działa end-to-end z prawidłowymi parametrami. Jednak nadal należy autoryzować wszystkie połączenia OAuth do generowania prawidłowych tokenów dostępu do [uwierzytelniania poświadczeń.](../active-directory/develop/authentication-scenarios.md) Oto sposoby autoryzowania połączeń OAuth:

* W przypadku wdrożeń automatycznych można użyć skryptu, który zapewnia zgodę dla każdego połączenia OAuth. Oto przykładowy skrypt w usłudze GitHub w projekcie [LogicAppConnectionAuth.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Aby ręcznie autoryzować połączenia OAuth, otwórz aplikację logiki w logice App Designer, w witrynie Azure portal lub w programie Visual Studio. W projektancie autoryzuj wszystkie wymagane połączenia.

Jeśli zamiast tego używasz [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) do autoryzowania połączeń, dowiedz się, jak [określić parametry jednostki usługi w szablonie aplikacji logiki.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)

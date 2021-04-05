---
title: Wdrażanie szablonów aplikacji logiki
description: Dowiedz się, jak wdrażać szablony Azure Resource Manager utworzone dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705340"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Wdrażanie szablonów usługi Azure Resource Manager dla usługi Azure Logic Apps

Po utworzeniu szablonu Azure Resource Manager dla aplikacji logiki można wdrożyć szablon w następujący sposób:

* [Witryna Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Interfejs wiersza polecenia platformy Azure](#cli)
* [Interfejs API REST usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Wdrażanie za Azure Portal

Aby automatycznie wdrożyć szablon aplikacji logiki na platformie Azure, możesz wybrać następujący przycisk **Wdróż na platformie Azure** , który zaloguje się do Azure Portal i zostanie wyświetlony komunikat z prośbą o informacje o aplikacji logiki. Następnie można wprowadzić wszelkie niezbędne zmiany szablonu lub parametrów aplikacji logiki.

[![Wdrażanie na platformie Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Na przykład po zalogowaniu się do Azure Portal zostanie wyświetlony monit o podanie następujących informacji:

* Nazwa subskrypcji platformy Azure
* Grupa zasobów, której chcesz użyć
* Lokalizacja aplikacji logiki
* Nazwa aplikacji logiki
* Identyfikator URI testu
* Akceptacja określonych warunków i postanowień

Więcej informacji można znaleźć w następujących tematach:

* [Przegląd: Automatyzowanie wdrażania aplikacji logiki za pomocą szablonów Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Wdrażanie zasobów za pomocą szablonów Azure Resource Manager i Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

Aby wdrożyć szablon aplikacji logiki z projektu grupy zasobów platformy Azure utworzonego przy użyciu programu Visual Studio, wykonaj następujące kroki, [Aby ręcznie wdrożyć aplikację logiki](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) na platformie Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Aby wdrożyć w określonej *grupie zasobów platformy Azure*, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Więcej informacji można znaleźć w następujących tematach:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć w określonej *grupie zasobów platformy Azure*, użyj następującego polecenia:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Więcej informacji można znaleźć w następujących tematach:

* [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Wdrażanie za pomocą usługi Azure DevOps

W celu wdrożenia szablonów aplikacji logiki i zarządzania środowiskami zespoły często używają narzędzia, takiego jak [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) w [usłudze Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines udostępnia [zadanie wdrażania grupy zasobów platformy Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , które można dodać do dowolnego potoku kompilacji lub wydania. Aby można było przeprowadzić autoryzację w celu wdrożenia i wygenerowania potoku wydania, potrzebna jest również nazwa [główna usługi](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Dowiedz się więcej o [używaniu jednostek usługi z Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Aby uzyskać więcej informacji na temat ciągłej integracji i ciągłego wdrażania (CI/CD) dla Azure Resource Manager szablonów z Azure Pipelines, zobacz następujące tematy i przykłady:

* [Integrowanie szablonów Menedżer zasobów z Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Samouczek: Ciągła integracja szablonów Azure Resource Manager z Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Przykład: łączenie z kolejkami Azure Service Bus z Azure Logic Apps i wdrażania przy użyciu Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: łączenie z kontami usługi Azure Storage z Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Konfigurowanie akcji aplikacji funkcji dla Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Nawiązywanie połączenia z kontem integracji z Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Przykład: organizowanie Azure Pipelines przy użyciu Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Poniżej przedstawiono ogólne kroki wysokiego poziomu dotyczące korzystania z Azure Pipelines:

1. W Azure Pipelines Utwórz pusty potok.

1. Wybieranie zasobów potrzebnych dla potoku, takich jak szablon aplikacji logiki i pliki parametrów szablonu, które są generowane ręcznie lub jako część procesu kompilacji.

1. W przypadku zadania agenta Znajdź i Dodaj zadanie **wdrażania grupy zasobów platformy Azure** .

   ![Dodawanie zadania "wdrożenie grupy zasobów platformy Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Skonfiguruj przy użyciu [nazwy głównej usługi](/azure/devops/pipelines/library/connect-to-azure).

1. Dodaj odwołania do szablonów aplikacji logiki i plików parametrów szablonu.

1. Kontynuuj Kompilowanie etapów procesu wydania dla dowolnego innego środowiska, test automatyczny lub osoby zatwierdzające zgodnie z wymaganiami.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autoryzuj połączenia OAuth

Po wdrożeniu aplikacja logiki działa od końca do końca z prawidłowymi parametrami, ale w celu wygenerowania prawidłowych tokenów dostępu do [uwierzytelniania poświadczeń](../active-directory/develop/authentication-vs-authorization.md)nadal trzeba autoryzować lub używać wstępnie utworzonych połączeń protokołu OAuth. Należy jednak tylko raz wdrażać i uwierzytelniać zasoby połączenia interfejsu API, co oznacza, że nie trzeba uwzględniać tych zasobów połączenia w kolejnych wdrożeniach, chyba że trzeba zaktualizować informacje o połączeniu. W przypadku korzystania z potoku ciągłej integracji i ciągłego wdrażania należy wdrożyć tylko zaktualizowane Logic Apps zasoby i nie trzeba ponownie autoryzować połączeń za każdym razem.

Poniżej przedstawiono kilka sugestii obsługi autoryzacji połączeń:

* Autoryzuj i udostępniaj zasoby połączeń interfejsu API w aplikacjach logiki, które znajdują się w tym samym regionie. Połączenia interfejsu API są dostępne jako zasoby platformy Azure niezależnie od aplikacji logiki. Chociaż Aplikacje logiki mają zależności od zasobów połączenia interfejsu API, zasoby połączenia interfejsu API nie mają zależności od aplikacji logiki i pozostają po usunięciu zależnych aplikacji logiki. Ponadto aplikacje logiki mogą korzystać z połączeń interfejsu API, które istnieją w innych grupach zasobów. Jednak Projektant aplikacji logiki obsługuje tworzenie połączeń interfejsu API tylko w tej samej grupie zasobów co Aplikacje logiki.

  > [!NOTE]
  > Jeśli rozważasz Udostępnianie połączeń interfejsu API, upewnij się, że rozwiązanie może [obsługiwać potencjalne problemy związane z ograniczaniem](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). Ograniczanie przepustowości odbywa się na poziomie połączenia, dlatego ponowne użycie tego samego połączenia przez wiele aplikacji logiki może zwiększyć prawdopodobieństwo wystąpienia problemów.

* Jeśli scenariusz nie obejmuje usług i systemów, które wymagają uwierzytelniania wieloskładnikowego, można użyć skryptu programu PowerShell, aby wyrazić zgodę na każde połączenie OAuth przez uruchomienie ciągłego procesu roboczego integracji jako zwykłego konta użytkownika na maszynie wirtualnej, która ma aktywne sesje przeglądarki z autoryzacjami i już podaną zgodą. Na przykład można przeznaczenie przykładowego skryptu dostarczonego przez [projekt LogicAppConnectionAuth w repozytorium GitHub Logic Apps](https://github.com/logicappsio/LogicAppConnectionAuth).

* Ręcznie Autoryzuj połączenia protokołu OAuth, otwierając aplikację logiki w Projektancie aplikacji logiki, w Azure Portal lub w programie Visual Studio.

* Jeśli zamiast tego autoryzujesz połączenia przy użyciu jednostki [usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), Dowiedz się, jak [określić parametry jednostki usługi w szablonie aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)

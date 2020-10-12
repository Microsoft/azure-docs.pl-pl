---
title: Tworzenie szablonów aplikacji logiki na potrzeby wdrożenia
description: Dowiedz się, jak utworzyć szablony Azure Resource Manager do automatyzowania wdrażania w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 4535e6bf11f8c2abf20b1b323925c3fc3299d362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971785"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Tworzenie szablonów usługi Azure Resource Manager w celu zautomatyzowania wdrożenia usługi Azure Logic Apps

Aby ułatwić automatyzację tworzenia i wdrażania aplikacji logiki, w tym artykule opisano sposoby tworzenia [szablonu Azure Resource Manager](../azure-resource-manager/management/overview.md) dla aplikacji logiki. Aby zapoznać się z omówieniem struktury i składni szablonu zawierającego definicję przepływu pracy i inne zasoby niezbędne do wdrożenia, zobacz [Omówienie: Automatyzowanie wdrażania dla aplikacji logiki za pomocą szablonów Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps udostępnia [wstępnie utworzony szablon Azure Resource Manager aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) , którego można użyć ponownie, nie tylko do tworzenia aplikacji logiki, ale także do definiowania zasobów i parametrów do użycia w ramach wdrożenia. Możesz użyć tego szablonu do własnych scenariuszy firmy lub dostosować szablon w celu spełnienia wymagań.

> [!IMPORTANT]
> Upewnij się, że połączenia w szablonie używają tej samej grupy zasobów platformy Azure i lokalizacji jako aplikacji logiki.

Aby uzyskać więcej informacji na temat szablonów Azure Resource Manager, zobacz następujące tematy:

* [Struktura i składnia szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Opracowywanie szablonów usługi Azure Resource Manager pozwalających zachować spójność w chmurze](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Tworzenie szablonów przy użyciu programu Visual Studio

Aby Najprostszym sposobem utworzyć prawidłowe, sparametryzowane szablony aplikacji logiki, które są głównie gotowe do wdrożenia, użyj programu Visual Studio (wersja bezpłatna lub nowsza) oraz narzędzi Azure Logic Apps Tools for Visual Studio. Następnie możesz [utworzyć aplikację logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) lub [znaleźć i pobrać istniejącą aplikację logiki z Azure Portal do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Pobierając aplikację logiki, uzyskasz szablon zawierający definicje aplikacji logiki oraz inne zasoby, takie jak połączenia. Szablon *parameterizes*również lub definiuje parametry dla, wartości używane do wdrażania aplikacji logiki i innych zasobów. Można podać wartości tych parametrów w osobnym pliku parametrów. Dzięki temu można łatwiej zmienić te wartości w zależności od potrzeb wdrożenia. Więcej informacji można znaleźć w następujących tematach:

* [Tworzenie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Tworzenie szablonów przy użyciu Azure PowerShell

Szablony Menedżer zasobów można tworzyć za pomocą Azure PowerShell z [modułem LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Ten moduł open source najpierw szacuje aplikację logiki i wszystkie połączenia używane przez aplikację logiki. Następnie moduł generuje zasoby szablonu z wymaganymi parametrami dla wdrożenia.

Załóżmy na przykład, że masz aplikację logiki, która odbiera komunikat z kolejki Azure Service Bus i przekazuje dane do Azure SQL Database. Moduł zachowuje wszystkie logiky aranżacji i parameterizes parametry połączenia SQL i Service Bus, aby można było podać i zmienić te wartości w zależności od potrzeb wdrożenia.

W poniższych przykładach pokazano, jak tworzyć i wdrażać aplikacje logiki przy użyciu szablonów Azure Resource Manager, Azure Pipelines w usłudze Azure DevOps i Azure PowerShell:

* [Przykład: łączenie z kolejkami Azure Service Bus z Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: łączenie z kontami usługi Azure Storage z Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: skonfiguruj akcję aplikacji funkcji dla Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Nawiązywanie połączenia z kontem integracji z poziomu Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Zainstaluj moduły programu PowerShell

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

1. Aby najprostszy sposób zainstalować moduł LogicAppTemplate z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate), uruchom następujące polecenie:

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   Aby zaktualizować do najnowszej wersji, uruchom następujące polecenie:

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

Lub, aby zainstalować je ręcznie, wykonaj kroki opisane w temacie usługi GitHub dla [twórcy szablonu aplikacji logiki](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Zainstaluj klienta Azure Resource Manager

Aby moduł LogicAppTemplate działał z dowolnym tokenem dostępu dzierżawy i subskrypcji platformy Azure, zainstaluj [narzędzie Azure Resource Manager Client](https://github.com/projectkudu/ARMClient), czyli proste narzędzie wiersza polecenia, które wywołuje interfejs API Azure Resource Manager.

Po uruchomieniu `Get-LogicAppTemplate` polecenia za pomocą tego narzędzia, polecenie najpierw pobiera token dostępu za pomocą narzędzia ARMClient, przekazuje token do skryptu programu PowerShell i tworzy szablon jako plik JSON. Aby uzyskać więcej informacji na temat narzędzia, zobacz ten [artykuł dotyczący narzędzia Azure Resource Manager Client](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generowanie szablonu przy użyciu programu PowerShell

Aby wygenerować szablon po zainstalowaniu modułu LogicAppTemplate i [interfejsu wiersza polecenia platformy Azure](/cli/azure/), Uruchom to polecenie programu PowerShell:

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Aby postępować zgodnie z zaleceniami dotyczącymi instalacji rurowej w tokenie przy użyciu [narzędzia klienta Azure Resource Manager](https://github.com/projectkudu/ARMClient), należy uruchomić to polecenie zamiast `$SubscriptionId` identyfikatora subskrypcji platformy Azure:

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Po wyodrębnieniu można utworzyć plik parametrów na podstawie szablonu, uruchamiając następujące polecenie:

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

W celu wyodrębnienia z odwołaniami Azure Key Vault (tylko statyczny) Uruchom następujące polecenie:

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametry | Wymagane | Opis |
|------------|----------|-------------|
| TemplateFile | Tak | Ścieżka pliku do pliku szablonu |
| KeyVault | Nie | Wyliczenie opisujące sposób obsługi możliwych wartości magazynu kluczy. Wartość domyślna to `None`. |
||||

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie szablonów aplikacji logiki](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

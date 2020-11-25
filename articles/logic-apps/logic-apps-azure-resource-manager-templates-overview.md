---
title: Przegląd — Automatyzowanie wdrożenia dla Azure Logic Apps
description: Dowiedz się więcej na temat Azure Resource Manager szablonów do automatyzowania wdrażania Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: 4070f373175f3497156ced011a57e2ed7bd6e770
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009776"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Przegląd: Automatyzowanie wdrażania Azure Logic Apps przy użyciu szablonów Azure Resource Manager

Gdy wszystko jest gotowe do automatyzowania tworzenia i wdrażania aplikacji logiki, można rozszerzyć podstawową definicję przepływu pracy aplikacji logiki do [szablonu Azure Resource Manager](../azure-resource-manager/management/overview.md). Ten szablon definiuje infrastrukturę, zasoby, parametry i inne informacje o aprowizacji i wdrażaniu aplikacji logiki. Definiując parametry dla wartości, które różnią się w zależności od wdrożenia, znanego również jako *parametryzacja*, można wielokrotnie i spójnie wdrażać aplikacje logiki na podstawie różnych potrzeb związanych z wdrażaniem.

Na przykład w przypadku wdrażania w środowiskach na potrzeby programowania, testowania i produkcji można używać różnych parametrów połączenia dla każdego środowiska. Można zadeklarować parametry szablonu, które przyjmują różne parametry połączenia, a następnie przechowywać te ciągi w osobnym [pliku parametrów](../azure-resource-manager/templates/parameter-files.md). Dzięki temu można zmienić te wartości bez konieczności aktualizacji i ponownego wdrożenia szablonu. W scenariuszach, w których znajdują się wartości parametrów, które są poufne lub muszą być zabezpieczone, takie jak hasła i wpisy tajne, można przechowywać te wartości w [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) i mieć pliki parametrów pobierają te wartości. Jednak w tych scenariuszach wdrożenie zostanie wdrożone ponownie w celu pobrania bieżących wartości.

W tym omówieniu opisano atrybuty w Menedżer zasobów szablon, który zawiera definicję przepływu pracy aplikacji logiki. Zarówno szablon, jak i definicja przepływu pracy używają składni JSON, ale istnieją pewne różnice, ponieważ definicja przepływu pracy jest również zgodna ze [schematem języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). Na przykład wyrażenia szablonów i wyrażenia definicji przepływu pracy różnią się w [odniesieniu do parametrów](#parameter-references) i wartości, które mogą być akceptowane.

> [!TIP]
> Aby najłatwiej uzyskać prawidłowy sparametryzowany szablon aplikacji logiki, który jest głównie gotowy do wdrożenia, użyj programu Visual Studio (bezpłatna wersja Community lub nowsza) oraz narzędzi Azure Logic Apps Tools for Visual Studio. Następnie możesz [utworzyć aplikację logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) lub [znaleźć i pobrać istniejącą aplikację logiki z platformy Azure do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Można też tworzyć szablony aplikacji logiki przy użyciu [Azure PowerShell z modułem LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

Przykładowa aplikacja logiki w tym temacie używa [wyzwalacza programu Outlook pakietu Office 365](/connectors/office365/) , który jest uruchamiany po nadejściu nowej wiadomości e-mail i [akcji BLOB Storage platformy Azure](/connectors/azureblob/) , która tworzy obiekt BLOB treści wiadomości e-mail i przekazuje ten obiekt BLOB do kontenera usługi Azure Storage. Przykłady pokazują również, jak Sparametryzuj wartości, które różnią się w zależności od wdrożenia.

Aby uzyskać więcej informacji na temat szablonów Menedżer zasobów, zobacz następujące tematy:

* [Struktura i składnia szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Najlepsze rozwiązania dotyczące szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Opracowywanie szablonów usługi Azure Resource Manager pozwalających zachować spójność w chmurze](../azure-resource-manager/templates/templates-cloud-consistency.md)

Aby zapoznać się z informacjami o zasobach, które są specyficzne dla aplikacji logiki, kont integracji, artefaktów konta integracji i środowisk usługi integracji, zobacz [Microsoft. Logic — typy zasobów](/azure/templates/microsoft.logic/allversions).

Przykładowe szablony aplikacji logiki można znaleźć w następujących przykładach:

* [Pełny szablon](#full-example-template) , który jest używany w przykładach tego tematu
* [Przykładowy szablon aplikacji logiki szybkiego startu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) w usłudze GitHub

Aby uzyskać informacje na temat interfejsu API REST Logic Apps, należy rozpocząć od usługi [Azure Logic Apps API REST](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Struktura szablonu

Na najwyższym poziomie szablon Menedżer zasobów jest zgodny z tą strukturą, która jest w pełni opisana w temacie [Azure Resource Manager Struktura szablonu i składnia](../azure-resource-manager/templates/template-syntax.md) :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

W przypadku szablonu aplikacji logiki należy korzystać głównie z tych obiektów szablonu:

| Atrybut | Opis |
|-----------|-------------|
| `parameters` | Deklaruje [Parametry szablonu](../azure-resource-manager/templates/template-syntax.md#parameters) do akceptowania wartości, które mają być używane podczas tworzenia i dostosowywania zasobów do wdrożenia na platformie Azure. Na przykład te parametry akceptują wartości w polu Nazwa i lokalizacja aplikacji logiki, połączenia i inne zasoby niezbędne do wdrożenia. Te wartości parametrów można przechowywać w [pliku parametrów](#template-parameter-files), który jest opisany w dalszej części tego tematu. Aby uzyskać ogólne informacje, zobacz [Parametry — struktura i składnia szablonu Menedżer zasobów](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definiuje [zasoby](../azure-resource-manager/templates/template-syntax.md#resources) , które mają zostać utworzone lub zaktualizowane i wdrożone w grupie zasobów platformy Azure, takie jak aplikacja logiki, połączenia, konta usługi Azure Storage itd. Ogólne informacje znajdują się w temacie [resources-Menedżer zasobów Template Structure i Syntax](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Szablon aplikacji logiki używa tego formatu nazwy pliku:

**<*logika-App-Name* # C0.jsna**

> [!IMPORTANT]
> W składni szablonu jest rozróżniana wielkość liter, dlatego należy użyć spójnej wielkości liter. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametry szablonu

Szablon aplikacji logiki ma wiele `parameters` obiektów, które znajdują się na różnych poziomach i wykonują różne funkcje. Na przykład na najwyższego poziomu można zadeklarować [Parametry szablonu](../azure-resource-manager/templates/template-syntax.md#parameters) dla wartości, aby akceptować i używać podczas wdrażania podczas tworzenia i wdrażania zasobów na platformie Azure, na przykład:

* Aplikacja logiki
* Połączenia używane przez logikę do uzyskiwania dostępu do innych usług i systemów za pomocą [łączników zarządzanych](../connectors/apis-list.md)
* Inne zasoby wymagane przez aplikację logiki do wdrożenia

  Na przykład jeśli aplikacja logiki używa [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dla scenariuszy biznes-to-Business (B2B), obiekt najwyższego poziomu szablonu `parameters` deklaruje parametr, który akceptuje identyfikator zasobu dla tego konta integracji.

Poniżej przedstawiono ogólną strukturę i składnię definicji parametru, która jest w pełni opisana przez [Parametry-Menedżer zasobów strukturę i składnię szablonu](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

W tym przykładzie przedstawiono tylko parametry szablonu dla wartości używanych do tworzenia i wdrażania tych zasobów na platformie Azure:

* Nazwa i lokalizacja aplikacji logiki
* Identyfikator do użycia dla konta integracji połączonego z aplikacją logiki

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Oprócz parametrów, które obsługują wartości, które są wrażliwe lub muszą być zabezpieczone, takich jak nazwy użytkowników, hasła i wpisy tajne, wszystkie te parametry obejmują `defaultValue` atrybuty, chociaż w niektórych przypadkach wartości domyślne są pustymi wartościami. Wartości wdrożenia, które mają być używane dla tych parametrów szablonu, są dostarczane przez przykładowy [plik parametrów](#template-parameter-files) opisany w dalszej części tego tematu.

Aby uzyskać więcej informacji na temat zabezpieczania parametrów szablonu, zobacz następujące tematy:

* [Zalecenia dotyczące zabezpieczeń dla parametrów szablonu](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Poprawa zabezpieczeń parametrów szablonu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Przekaż zabezpieczone wartości parametrów za pomocą Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Inne obiekty szablonu często odwołują się do parametrów szablonu, dzięki czemu mogą korzystać z wartości, które przechodzą przez parametry szablonu, na przykład:

* [Obiekt zasobów szablonu](#template-resources), opisany w dalszej części tego tematu, definiuje każdy zasób na platformie Azure, który ma zostać utworzony i wdrożony, na przykład w [definicji zasobu aplikacji logiki](#logic-app-resource-definition). Te zasoby często używają wartości parametrów szablonu, takich jak nazwa i lokalizacja aplikacji logiki oraz informacje o połączeniu.

* Na poziomie bardziej szczegółowym w definicji zasobu aplikacji logiki [obiekt parametrów definicji przepływu pracy](#workflow-definition-parameters) deklaruje parametry dla wartości, które mają być używane w czasie wykonywania aplikacji logiki. Na przykład można zadeklarować parametry definicji przepływu pracy dla nazwy użytkownika i hasła, których wyzwalacz HTTP używa do uwierzytelniania. Aby określić wartości parametrów definicji przepływu pracy, należy użyć `parameters` obiektu, który znajduje się *poza* definicją przepływu pracy, ale nadal *w* definicji zasobu aplikacji logiki. W tym obiekcie zewnętrznym `parameters` można odwołać się do wcześniej zadeklarowanych parametrów szablonu, które mogą akceptować wartości podczas wdrażania z pliku parametrów.

Podczas odwoływania się do parametrów, wyrażenia szablonu i funkcje używają różnej składni i zachowują się inaczej od wyrażeń definicji przepływu pracy i funkcji. Aby uzyskać więcej informacji na temat tych różnic, zobacz [odwołania do parametrów](#parameter-references) w dalszej części tego tematu.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Najlepsze praktyki — parametry szablonu

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące definiowania parametrów:

* Zadeklaruj parametry tylko dla wartości, które różnią się w zależności od potrzeb wdrożenia. Nie deklaruj parametrów dla wartości, które pozostają takie same w różnych wymaganiach dotyczących wdrażania.

* Dołącz `defaultValue` atrybut, który może określać puste wartości dla wszystkich parametrów, z wyjątkiem wartości, które są poufne lub muszą być zabezpieczone. Zawsze używaj zabezpieczonych parametrów dla nazw użytkowników, haseł i wpisów tajnych. Aby ukryć lub chronić poufne wartości parametrów, postępuj zgodnie ze wskazówkami w tych tematach:

  * [Zalecenia dotyczące zabezpieczeń dla parametrów szablonu](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Poprawa zabezpieczeń parametrów szablonu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Przekaż zabezpieczone wartości parametrów za pomocą Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Aby rozróżnić nazwy parametrów szablonu od nazw parametrów definicji przepływu pracy, można użyć opisowych nazw parametrów szablonu, na przykład: `TemplateFabrikamPassword`

Aby uzyskać więcej najlepszych rozwiązań dotyczących szablonów, zobacz [najlepsze rozwiązania dotyczące parametrów szablonów](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Plik parametrów szablonu

Aby podać wartości parametrów szablonu, Zapisz te wartości w [pliku parametrów](../azure-resource-manager/templates/parameter-files.md). W ten sposób można użyć różnych plików parametrów w zależności od potrzeb wdrożenia. Oto format nazwy pliku do użycia:

* Nazwa pliku szablonu aplikacji logiki: **< *Logic-App-Name* # C0.json**
* Nazwa pliku parametrów: **< *Logic-App-Name* # C0.parameters.json**

Poniżej znajduje się struktura wewnątrz pliku parametrów, która zawiera odwołanie do magazynu kluczy do [przekazywania zabezpieczonej wartości parametru z Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Ten przykładowy plik parametrów określa wartości parametrów szablonu zadeklarowanych wcześniej w tym temacie:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Zasoby szablonu

Szablon zawiera `resources` obiekt, który jest tablicą zawierającą definicje dla każdego zasobu do utworzenia i wdrożenia na platformie Azure, na przykład [definicję zasobu aplikacji logiki](#logic-app-resource-definition), [definicje zasobów połączenia](#connection-resource-definitions)i inne zasoby, które wymagają wdrożenia przez aplikację logiki.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Szablony mogą obejmować definicje zasobów dla wielu aplikacji logiki, dlatego należy upewnić się, że wszystkie zasoby aplikacji logiki określają tę samą grupę zasobów platformy Azure. Po wdrożeniu szablonu w grupie zasobów platformy Azure przy użyciu programu Visual Studio zostanie wyświetlony monit o wybranie aplikacji logiki, która ma zostać otwarta. Ponadto projekt grupy zasobów platformy Azure może zawierać więcej niż jeden szablon, dlatego upewnij się, że po wyświetleniu monitu zostanie wybrany prawidłowy plik parametrów.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Wyświetl definicje zasobów

Aby przejrzeć definicje zasobów dla wszystkich zasobów w grupie zasobów platformy Azure, [Pobierz aplikację logiki z platformy Azure do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), która jest najprostszym sposobem utworzenia prawidłowego szablonu sparametryzowanej aplikacji logiki, który jest głównie gotowy do wdrożenia.

Aby uzyskać ogólne informacje na temat zasobów szablonów i ich atrybutów, zobacz następujące tematy:

* [Zasoby — struktura i składnia szablonu Menedżer zasobów](../azure-resource-manager/templates/template-syntax.md#resources)
* [Najlepsze rozwiązania dotyczące zasobów szablonów](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definicja zasobu aplikacji logiki

[Definicja zasobu przepływu pracy aplikacji logiki w szablonie](/azure/templates/microsoft.logic/workflows) rozpoczyna się od `properties` obiektu, który zawiera następujące informacje:

* Stan aplikacji logiki przy wdrożeniu
* Identyfikator dowolnego konta integracji używanego przez aplikację logiki
* Definicja przepływu pracy aplikacji logiki
* `parameters`Obiekt, który ustawia wartości do użycia w czasie wykonywania
* Inne informacje o zasobach aplikacji logiki, takie jak nazwa, typ, lokalizacja, wszystkie ustawienia konfiguracji środowiska uruchomieniowego itd.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Poniżej znajdują się atrybuty specyficzne dla definicji zasobu aplikacji logiki:

| Atrybut | Wymagany | Typ | Opis |
|-----------|----------|------|-------------|
| `state` | Tak | Ciąg | Stan aplikacji logiki przy wdrożeniu, gdzie `Enabled` oznacza, że aplikacja logiki działa i `Disabled` oznacza, że aplikacja logiki jest nieaktywna. Jeśli na przykład nie masz gotowości do działania aplikacji logiki, ale chcesz wdrożyć wersję roboczą, możesz użyć `Disabled` opcji. |
| `integrationAccount` | Nie | Obiekt | Jeśli aplikacja logiki korzysta z konta integracji, które przechowuje artefakty dla scenariuszy biznes-to-Business (B2B), ten obiekt zawiera `id` atrybut, który określa identyfikator konta integracji. |
| `definition` | Tak | Obiekt | Podstawowa definicja przepływu pracy aplikacji logiki, która jest tym samym obiektem, który pojawia się w widoku kodu i jest w pełni opisana w [dokumentacji schematu dotyczącej języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md) . W tej definicji przepływu pracy `parameters` obiekt deklaruje parametry dla wartości, które mają być używane w środowisku uruchomieniowym aplikacji logiki. Aby uzyskać więcej informacji, zobacz [definicji i parametrów przepływu pracy](#workflow-definition-parameters). <p><p>Aby wyświetlić atrybuty w definicji przepływu pracy aplikacji logiki, przejdź od "Widok projektu" do "Widok kodu" w Azure Portal lub Visual Studio lub za pomocą narzędzia, takiego jak [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Nie | Obiekt | [Wartości parametrów definicji przepływu pracy](#workflow-definition-parameters) do użycia w środowisku uruchomieniowym aplikacji logiki. Definicje parametrów dla tych wartości są wyświetlane w [obiekcie parametrów definicji przepływu pracy](#workflow-definition-parameters). Ponadto, jeśli aplikacja logiki korzysta z [łączników zarządzanych](../connectors/apis-list.md) do uzyskiwania dostępu do innych usług i systemów, ten obiekt zawiera `$connections` obiekt, który ustawia wartości połączenia do użycia w czasie wykonywania. |
| `accessControl` | Nie | Obiekt | Do określania atrybutów zabezpieczeń aplikacji logiki, takich jak ograniczanie dostępu do adresów IP do wyzwalaczy żądań lub uruchamianie historii danych wejściowych i wyjściowych. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do usługi Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md). |
| `runtimeConfiguration` | Nie | Obiekt | Do określania `operationOptions` właściwości, które kontrolują sposób, w jaki aplikacja logiki działa w czasie wykonywania. Można na przykład uruchomić aplikację logiki w [trybie wysokiej przepustowości](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode). |
|||||

Aby uzyskać więcej informacji na temat definicji zasobów dla tych Logic Apps obiektów, zobacz [Microsoft. Logic — typy zasobów](/azure/templates/microsoft.logic/allversions):

* [Definicja zasobu przepływu pracy](/azure/templates/microsoft.logic/workflows)
* [Definicja zasobu środowiska usługi integracji](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Definicja zasobu zarządzanego interfejsu API środowiska usługi integracji](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Definicja zasobu konta integracji](/azure/templates/microsoft.logic/integrationaccounts)

* Artefakty konta integracji:

  * [Definicja zasobu umowy](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Definicja zasobu zestawu](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Definicja zasobu konfiguracji partii](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Definicja zasobu certyfikatu](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Definicja zasobu mapy](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Definicja zasobu partnera](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Definicja zasobu schematu](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Definicja zasobu sesji](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definicja i parametry przepływu pracy

Definicja przepływu pracy aplikacji logiki pojawia się w `definition` obiekcie, który pojawia się w `properties` obiekcie w definicji zasobu aplikacji logiki. Ten `definition` obiekt jest tym samym obiektem, który pojawia się w widoku kodu i jest w pełni opisany w temacie [Informacje o schemacie dla języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md) . Definicja przepływu pracy zawiera wewnętrzny `parameters` obiekt deklaracji, w którym można definiować nowe lub edytowane istniejące parametry wartości, które są używane przez definicję przepływu pracy w czasie wykonywania. Następnie można odwoływać się do tych parametrów w wyzwalaczu lub akcjach w przepływie pracy. Domyślnie ten `parameters` obiekt jest pusty, chyba że aplikacja logiki tworzy połączenia z innymi usługami i systemami za pomocą [łączników zarządzanych](../connectors/apis-list.md).

Aby ustawić wartości parametrów definicji przepływu pracy, należy użyć `parameters` obiektu, który znajduje się *poza* definicją przepływu pracy, ale nadal *w* definicji zasobu aplikacji logiki. W tym obiekcie zewnętrznym `parameters` można odwoływać się do poprzednio zadeklarowanych parametrów szablonu, które mogą akceptować wartości podczas wdrażania z pliku parametrów.

> [!TIP]
>
> Najlepszym rozwiązaniem jest nie odwołuje się bezpośrednio do parametrów szablonu, które są oceniane we wdrożeniu, z wewnątrz definicji przepływu pracy. Zamiast tego należy zadeklarować parametr definicji przepływu pracy, który można następnie ustawić w `parameters` obiekcie, który znajduje się *poza* definicją przepływu pracy, ale nadal *w* definicji zasobu aplikacji logiki. Aby uzyskać więcej informacji, zobacz [odwołania do parametrów](#parameter-references).

Ta składnia pokazuje, gdzie można zadeklarować parametry na poziomach definicji szablonu i przepływu pracy oraz gdzie można ustawić te wartości parametrów, odwołując się do parametrów definicji szablonu i przepływu pracy:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parametry definicji bezpiecznego przepływu pracy

Dla parametru definicji przepływu pracy, który obsługuje informacje poufne, hasła, klucze dostępu lub wpisy tajne w środowisku uruchomieniowym, należy zadeklarować lub edytować parametr, aby użyć `securestring` `secureobject` typu parametru lub. Można odwołać się do tego parametru w obrębie i w ramach definicji przepływu pracy. Na najwyższego poziomu szablonu Zadeklaruj parametr, który ma ten sam typ, aby obsłużyć te informacje podczas wdrażania.

Aby ustawić wartość parametru definicji przepływu pracy, należy użyć `parameters` obiektu, który znajduje się *poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobu aplikacji logiki, aby odwołać się do parametru szablonu. Na koniec, aby przekazać wartość do parametru szablonu podczas wdrażania, należy przechowywać tę wartość w [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) i odwołać się do tego magazynu kluczy w [pliku parametrów](#template-parameter-files) , który jest używany przez szablon podczas wdrażania.

Ten przykładowy szablon pokazuje, jak można wykonać te zadania, definiując zabezpieczone parametry w razie potrzeby, aby można było przechowywać ich wartości w Azure Key Vault:

* Zadeklaruj zabezpieczone parametry dla wartości używanych do uwierzytelniania dostępu.
* Użyj tych wartości zarówno na poziomach szablonu, jak i w definicji przepływu pracy.
* Podaj te wartości przy użyciu pliku parametrów.

**Szablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Plik parametrów**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Najlepsze praktyki — parametry definicji przepływu pracy

Aby upewnić się, że projektant aplikacji logiki może poprawnie pokazać parametry definicji przepływu pracy, postępuj zgodnie z następującymi najlepszymi rozwiązaniami:

* Dołącz `defaultValue` atrybut, który może określać puste wartości dla wszystkich parametrów, z wyjątkiem wartości, które są poufne lub muszą być zabezpieczone.

* Zawsze używaj zabezpieczonych parametrów dla nazw użytkowników, haseł i wpisów tajnych. Aby ukryć lub chronić poufne wartości parametrów, postępuj zgodnie ze wskazówkami w tych tematach:

  * [Zalecenia dotyczące zabezpieczeń dotyczące parametrów akcji](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Zalecenia dotyczące zabezpieczeń parametrów w definicjach przepływu pracy](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Przekazywanie bezpiecznych wartości parametrów za pomocą Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Aby uzyskać więcej informacji na temat parametrów definicji przepływu pracy, zobacz [Parameters-Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definicje zasobów połączenia

Gdy aplikacja logiki tworzy i używa połączeń z innymi usługami i systemem przy użyciu [łączników zarządzanych](../connectors/apis-list.md), `resources` obiekt szablonu zawiera definicje zasobów dla tych połączeń. Mimo że tworzysz połączenia z poziomu aplikacji logiki, połączenia są oddzielane zasobami platformy Azure z ich własnymi definicjami zasobów. Aby przejrzeć te definicje zasobów połączeń, [Pobierz aplikację logiki z platformy Azure do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), która jest najprostszym sposobem utworzenia prawidłowego szablonu sparametryzowanej aplikacji logiki, który jest głównie gotowy do wdrożenia.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Definicje zasobów połączenia odwołują się do parametrów najwyższego poziomu szablonu dla ich wartości, co oznacza, że można podać te wartości podczas wdrażania przy użyciu pliku parametrów. Upewnij się, że połączenia używają tej samej grupy zasobów platformy Azure i lokalizacji jako aplikacji logiki.

Poniżej przedstawiono przykładową definicję zasobu dla połączenia z pakietem Office 365 Outlook oraz odpowiednie parametry szablonu:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Definicja zasobu aplikacji logiki działa również z definicjami zasobów połączenia w następujący sposób:

* Wewnątrz definicji przepływu pracy `parameters` obiekt deklaruje `$connections` parametr wartości połączenia do użycia w środowisku uruchomieniowym aplikacji logiki. Ponadto wyzwalacz lub Akcja, która tworzy połączenie, używa odpowiednich wartości, które przechodzą przez ten `$connections` parametr.

* *Poza* definicją przepływu pracy, ale nadal *w* definicji zasobu aplikacji logiki, inny `parameters` obiekt ustawia wartości do użycia w czasie wykonywania dla `$connections` parametru przez odwołanie do odpowiednich parametrów szablonu. Te wartości używają wyrażeń szablonu do odwoływania się do zasobów, które bezpiecznie przechowują metadane dla połączeń w aplikacji logiki.

  Na przykład metadane mogą zawierać parametry połączenia i tokeny dostępu, które można przechowywać w [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Aby przekazać te wartości do parametrów szablonu, należy odwołać się do tego magazynu kluczy w [pliku parametrów](#template-parameter-files) , który jest używany przez szablon podczas wdrażania. Aby uzyskać więcej informacji na temat różnic w parametrach odwołań, zobacz [odwołania do parametrów](#parameter-references) w dalszej części tego tematu.

  Po otwarciu definicji przepływu pracy aplikacji logiki w widoku kodu za pomocą Azure Portal lub Visual Studio, `$connections` obiekt pojawia się poza definicją przepływu pracy, ale na tym samym poziomie. Takie porządkowanie w widoku kodu sprawia, że te parametry są łatwiejsze do odwołania podczas ręcznego aktualizowania definicji przepływu pracy:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Definicja zasobu aplikacji logiki zawiera `dependsOn` obiekt, który określa zależności od połączeń używanych przez aplikację logiki.

Każde utworzone połączenie ma unikatową nazwę na platformie Azure. W przypadku tworzenia wielu połączeń z tą samą usługą lub systemem każda nazwa połączenia jest dołączana z liczbą, która zwiększa się wraz z każdym nowym połączeniem utworzonym, na przykład,, `office365` `office365-1` i tak dalej.

W tym przykładzie przedstawiono interakcje między definicją zasobu aplikacji logiki a definicją zasobu połączenia dla pakietu Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parametry bezpiecznego połączenia

Dla parametru połączenia, który obsługuje informacje poufne, hasła, klucze dostępu lub wpisy tajne, definicja zasobu połączenia zawiera `parameterValues` obiekt, który określa te wartości w formacie pary nazwa-wartość. Aby ukryć te informacje, można zadeklarować lub edytować parametry szablonu dla tych wartości przy użyciu `securestring` `secureobject` typów parametrów lub. Następnie można przechowywać te informacje w [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Aby przekazać te wartości do parametrów szablonu, należy odwołać się do tego magazynu kluczy w [pliku parametrów](#template-parameter-files) , który jest używany przez szablon podczas wdrażania.

Oto przykład, który zawiera nazwę konta i klucz dostępu dla połączenia usługi Azure Blob Storage:

**Plik parametrów**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Szablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Uwierzytelnianie połączeń

Po wdrożeniu aplikacja logiki działa na całym końcu z prawidłowymi parametrami. Należy jednak nadal autoryzować wszystkie połączenia OAuth w celu wygenerowania prawidłowych tokenów dostępu do [uwierzytelniania poświadczeń](../active-directory/develop/authentication-vs-authorization.md). Aby uzyskać więcej informacji, zobacz [Autoryzuj połączenia OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Niektóre połączenia obsługują użycie jednostki [usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) do autoryzacji połączeń dla aplikacji logiki, która jest [zarejestrowana w usłudze Azure AD](../active-directory/develop/quickstart-register-app.md). Na przykład ta definicja zasobu połączenia Azure Data Lake pokazuje, jak odwoływać się do parametrów szablonu, które obsługują informacje o jednostce usługi i jak szablon deklaruje następujące parametry:

**Definicja zasobu połączenia**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Atrybut | Opis |
|-----------|-------------|
| `token:clientId` | Identyfikator aplikacji lub klienta skojarzony z jednostką usługi |
| `token:clientSecret` | Wartość klucza skojarzona z jednostką usługi |
| `token:TenantId` | Identyfikator katalogu dla dzierżawy usługi Azure AD |
| `token:grantType` | Żądany typ dotacji, który musi mieć wartość `client_credentials` . Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform oraz przepływ poświadczeń klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definicje parametrów szablonu**

Obiekt najwyższego poziomu szablonu `parameters` deklaruje następujące parametry dla przykładowego połączenia:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Aby uzyskać więcej informacji na temat pracy z jednostkami usługi, zobacz następujące tematy:

* [Tworzenie jednostki usługi przy użyciu Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Tworzenie jednostki usługi platformy Azure przy użyciu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Tworzenie jednostki usługi z certyfikatem przy użyciu Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Odwołania do parametrów

Aby odwołać się do parametrów szablonu, można użyć wyrażeń szablonu z [funkcjami szablonu](../azure-resource-manager/templates/template-functions.md), które są oceniane podczas wdrażania. Wyrażenia szablonu używają nawiasów kwadratowych (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Aby odwołać się do parametrów definicji przepływu pracy, należy użyć [wyrażeń i funkcji języka definicji przepływu pracy](../logic-apps/workflow-definition-language-functions-reference.md), które są oceniane w czasie wykonywania. Można zauważyć, że niektóre funkcje szablonu i funkcje definicji przepływu pracy mają taką samą nazwę. Wyrażenia definicji przepływu pracy zaczynają się od symbolu "at" ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Można przekazać wartości parametrów szablonu do definicji przepływu pracy dla aplikacji logiki do użycia w czasie wykonywania. Należy jednak unikać używania parametrów szablonu, wyrażeń i składni w definicji przepływu pracy, ponieważ projektant aplikacji logiki nie obsługuje elementów szablonu. Ponadto składnia i wyrażenia szablonu mogą komplikuje kod z powodu różnic w obliczaniu wyrażeń.

Zamiast tego wykonaj następujące ogólne kroki, aby zadeklarować i odwołać się do parametrów definicji przepływu pracy w czasie wykonywania, zadeklarować i odwołać się do parametrów szablonu do użycia we wdrożeniu, a następnie określić wartości, które zostaną przekazane podczas wdrażania przy użyciu pliku parametrów. Aby uzyskać szczegółowe informacje, zobacz sekcję [definicji i parametrów przepływu pracy](#workflow-definition-parameters) we wcześniejszej części tego tematu.

1. Utwórz szablon i zadeklaruj parametry szablonu dla wartości, które mają być akceptowane i używane podczas wdrażania.

1. W definicji przepływu pracy zadeklaruj parametry wartości, które mają być akceptowane i używane w czasie wykonywania. Następnie można odwoływać się do tych wartości w ramach definicji przepływu pracy.

1. W `parameters` obiekcie, który znajduje się *poza* definicją przepływu pracy, ale nadal *w* definicji zasobu aplikacji logiki, ustaw wartości parametrów definicji przepływu pracy, odwołując się do odpowiednich parametrów szablonu. Dzięki temu można przekazać wartości parametrów szablonu do parametrów definicji przepływu pracy.

1. W pliku Parameters Określ wartości dla szablonu do użycia we wdrożeniu.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Pełny przykładowy szablon

Poniżej przedstawiono przykładowy szablon sparametryzowany, który jest używany przez przykłady tego tematu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie szablonów aplikacji logiki](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)

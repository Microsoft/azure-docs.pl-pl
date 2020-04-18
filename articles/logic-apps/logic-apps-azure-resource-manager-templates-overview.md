---
title: Omówienie — automatyzacja wdrażania aplikacji logiki azure
description: Dowiedz się więcej o szablonach usługi Azure Resource Manager w celu zautomatyzowania wdrażania aplikacji logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641827"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Omówienie: automatyzacja wdrażania aplikacji logiki platformy Azure przy użyciu szablonów usługi Azure Resource Manager

Gdy będziesz gotowy do automatyzacji tworzenia i wdrażania aplikacji logiki, możesz rozszerzyć podstawową definicję przepływu pracy aplikacji logiki na [szablon usługi Azure Resource Manager.](../azure-resource-manager/management/overview.md) Ten szablon definiuje infrastrukturę, zasoby, parametry i inne informacje do inicjowania obsługi administracyjnej i wdrażania aplikacji logiki. Definiując parametry dla wartości, które różnią się w przypadku wdrożenia, znany również jako *parametryzacji*, można wielokrotnie i konsekwentnie wdrażać aplikacje logiki w oparciu o różne potrzeby wdrażania.

Na przykład jeśli wdrożysz w środowiskach dla rozwoju, testowania i produkcji, prawdopodobnie używasz różnych ciągów połączeń dla każdego środowiska. Można zadeklarować parametry szablonu, które akceptują różne parametry połączenia, a następnie przechowywać te ciągi w oddzielnym [pliku parametrów](../azure-resource-manager/templates/parameter-files.md). W ten sposób można zmienić te wartości bez konieczności aktualizowania i ponownego rozmieszczenia szablonu. W scenariuszach, w których masz wartości parametrów, które są poufne lub muszą być zabezpieczone, takie jak hasła i wpisy tajne, można przechowywać te wartości w [usłudze Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) i pobrać te wartości przez plik parametrów. Jednak w tych scenariuszach należy ponownie wdrożyć, aby pobrać bieżące wartości.

W tym przeglądzie opisano atrybuty w szablonie Menedżera zasobów, który zawiera definicję przepływu pracy aplikacji logiki. Zarówno szablon, jak i definicja przepływu pracy używają składni JSON, ale istnieją pewne różnice, ponieważ definicja przepływu pracy jest również zgodna ze [schematem Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). Na przykład wyrażenia szablonu i wyrażenia definicji przepływu pracy różnią się w jaki sposób [odwołują się do parametrów](#parameter-references) i wartości, które mogą zaakceptować.

> [!TIP]
> Aby uzyskać najprostszy sposób, aby uzyskać prawidłowy szablon aplikacji logiki sparametryzowane, który jest w większości gotowy do wdrożenia, użyj programu Visual Studio (bezpłatna wersja społeczności lub większa) i narzędzia Azure Logic Apps Tools for Visual Studio. Następnie można [utworzyć aplikację logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) lub znaleźć i pobrać [istniejącą aplikację logiki z platformy Azure do programu Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> Można też tworzyć szablony aplikacji logiki przy użyciu [programu Azure PowerShell z modułem LogicAppTemplate.](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)

Przykładowa aplikacja logiki w tym temacie używa [wyzwalacza programu Office 365 Outlook,](/connectors/office365/) który jest uruchamiany po nadejściu nowej wiadomości e-mail i [akcji usługi Azure Blob Storage,](/connectors/azureblob/) która tworzy obiekt obiektu blob dla treści poczty e-mail i przekazuje ten obiekt blob do kontenera magazynu platformy Azure. Przykłady pokazują również, jak parametryzować wartości, które różnią się w czasie wdrażania.

Aby uzyskać więcej informacji na temat szablonów Menedżera zasobów, zobacz następujące tematy:

* [Struktura i składnia szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Najlepsze rozwiązania dotyczące szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Opracowywanie szablonów usługi Azure Resource Manager pozwalających zachować spójność w chmurze](../azure-resource-manager/templates/templates-cloud-consistency.md)

Aby uzyskać przykładowe szablony aplikacji logiki, zobacz następujące przykłady:

* [Pełny szablon,](#full-example-template) który jest używany dla przykładów tego tematu
* [Przykładowy szablon aplikacji logiki szybki start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) w usłudze GitHub

Aby uzyskać informacje o zasobach szablonów specyficzne dla aplikacji logiki, kont integracji i artefaktów kont integracji, zobacz [Typy zasobów firmy Microsoft.Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Struktura szablonu

Na najwyższym poziomie szablon Menedżera zasobów jest zgodny z tą strukturą, która jest w pełni opisana w strukturze szablonu usługi Azure Resource Manager i temacie [składni:](../azure-resource-manager/templates/template-syntax.md)

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

W przypadku szablonu aplikacji logiki pracujesz przede wszystkim z tymi obiektami szablonu:

| Atrybut | Opis |
|-----------|-------------|
| `parameters` | Deklaruje [parametry szablonu](../azure-resource-manager/templates/template-syntax.md#parameters) do akceptowania wartości używanych podczas tworzenia i dostosowywania zasobów do wdrożenia na platformie Azure. Na przykład te parametry akceptują wartości nazwy i lokalizacji aplikacji logiki, połączeń i innych zasobów niezbędnych do wdrożenia. Te wartości parametrów można zapisać w [pliku parametrów,](#template-parameter-files)który jest opisany w dalszej części tego tematu. Aby uzyskać ogólne informacje, zobacz [Parametry — Struktura i składnia szablonu Menedżera zasobów](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definiuje [zasoby](../azure-resource-manager/templates/template-syntax.md#resources) do tworzenia lub aktualizowania i wdrażania w grupie zasobów platformy Azure, takich jak aplikacja logiki, połączenia, konta magazynu platformy Azure i tak dalej. Aby uzyskać ogólne informacje, zobacz [Zasoby — Struktura szablonów i składnia Menedżera zasobów](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Szablon aplikacji logiki używa tego formatu nazwy pliku:

**<*logic-app-name*>.json**

> [!IMPORTANT]
> W składni szablonu rozróżniana jest wielkość liter, więc upewnij się, że używasz spójnej wielkości liter. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametry szablonu

Szablon aplikacji logiki `parameters` ma wiele obiektów, które istnieją na różnych poziomach i wykonują różne funkcje. Na przykład na najwyższym poziomie można zadeklarować [parametry szablonu](../azure-resource-manager/templates/template-syntax.md#parameters) dla wartości do zaakceptowania i użycia podczas wdrażania podczas tworzenia i wdrażania zasobów na platformie Azure, na przykład:

* Twoja aplikacja logiki
* Połączenia używane przez logikę do uzyskiwania dostępu do innych usług i systemów za pośrednictwem [łączników zarządzanych](../connectors/apis-list.md)
* Inne zasoby potrzebne aplikacji logiki do wdrożenia

  Na przykład jeśli aplikacja logiki używa [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dla scenariuszy business-to-business (B2B), obiekt najwyższego poziomu `parameters` szablonu deklaruje parametr, który akceptuje identyfikator zasobu dla tego konta integracji.

Oto ogólna struktura i składnia definicji parametrów, która jest w pełni opisana przez [Parametry — Struktura szablonu i składnia Menedżera zasobów:](../azure-resource-manager/templates/template-syntax.md#parameters)

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

W tym przykładzie pokazano tylko parametry szablonu dla wartości używanych do tworzenia i wdrażania tych zasobów na platformie Azure:

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

Z wyjątkiem parametrów, które obsługują wartości, które są poufne lub muszą być zabezpieczone, `defaultValue` takie jak nazwy użytkowników, hasła i wpisy tajne, wszystkie te parametry zawierają atrybuty, chociaż w niektórych przypadkach wartości domyślne są wartościami pustymi. Wartości wdrożenia używane dla tych parametrów szablonu są dostarczane przez [przykładowy plik parametrów](#template-parameter-files) opisany w dalszej części tego tematu.

Aby uzyskać więcej informacji na temat zabezpieczania parametrów szablonu, zobacz następujące tematy:

* [Zalecenia dotyczące zabezpieczeń dla parametrów szablonu](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Zwiększenie bezpieczeństwa parametrów szablonu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Przekazywanie wartości zabezpieczonych parametrów za pomocą usługi Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Inne obiekty szablonu często odwołują się do parametrów szablonu, aby mogły używać wartości, które przechodzą przez parametry szablonu, na przykład:

* [Obiekt zasobów szablonu,](#template-resources)opisany w dalszej części tego tematu, definiuje każdy zasób na platformie Azure, który chcesz utworzyć i wdrożyć, na przykład [definicję zasobów aplikacji logiki.](#logic-app-resource-definition) Te zasoby często używają wartości parametrów szablonu, takich jak nazwa aplikacji logiki oraz informacje o lokalizacji i połączeniu.

* Na głębszym poziomie w definicji zasobów aplikacji logiki [obiekt parametrów definicji przepływu pracy](#workflow-definition-parameters) deklaruje parametry wartości do użycia w czasie wykonywania aplikacji logiki. Na przykład można zadeklarować parametry definicji przepływu pracy dla nazwy użytkownika i hasła używanego przez wyzwalacz HTTP do uwierzytelniania. Aby określić wartości parametrów definicji `parameters` przepływu pracy, należy użyć obiektu, który znajduje *się poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki. W tym `parameters` obiekcie zewnętrznym można odwoływać się do wcześniej zadeklarowanych parametrów szablonu, które mogą akceptować wartości podczas wdrażania z pliku parametrów.

Podczas odwoływania się do parametrów wyrażenia szablonu i funkcje używają innej składni i zachowują się inaczej niż wyrażenia i funkcje definicji przepływu pracy. Aby uzyskać więcej informacji na temat tych różnic, zobacz [odwołania do parametrów](#parameter-references) w dalszej części tego tematu.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Najważniejsze wskazówki — parametry szablonu

Oto kilka sprawdzonych rozwiązań dotyczących definiowania parametrów:

* Zadeklaruj parametry tylko dla wartości, które różnią się w zależności od potrzeb wdrożenia. Nie deklaruj parametrów dla wartości, które pozostają takie same w różnych wymaganiach dotyczących wdrażania.

* Dołącz `defaultValue` atrybut, który można określić puste wartości, dla wszystkich parametrów, z wyjątkiem wartości, które są poufne lub muszą być zabezpieczone. Zawsze używaj zabezpieczonych parametrów dla nazw użytkowników, haseł i wpisów tajnych. Aby ukryć lub chronić wartości parametrów poufnych, postępuj zgodnie ze wskazówkami w następujących tematach:

  * [Zalecenia dotyczące zabezpieczeń dla parametrów szablonu](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Zwiększenie bezpieczeństwa parametrów szablonu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Przekazywanie wartości zabezpieczonych parametrów za pomocą usługi Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Aby odróżnić nazwy parametrów szablonu od nazw parametrów definicji przepływu pracy, można użyć opisowych nazw parametrów szablonu, na przykład:`TemplateFabrikamPassword`

Aby uzyskać więcej najlepszych rozwiązań dotyczących szablonu, zobacz [Najważniejsze wskazówki dotyczące parametrów szablonu](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Plik parametrów szablonu

Aby podać wartości parametrów szablonu, należy przechowywać te wartości w [pliku parametrów](../azure-resource-manager/templates/parameter-files.md). W ten sposób można użyć różnych plików parametrów w zależności od potrzeb wdrożenia. Oto format nazwy pliku do użycia:

* Nazwa pliku szablonu aplikacji logiki: ** <nazwa>.json *logic app***
* Nazwa pliku parametrów: ** <nazwa>.parameters.json *logiki***

Oto struktura wewnątrz pliku parametrów, która zawiera odwołanie do magazynu kluczy do [przekazywania wartości zabezpieczonego parametru za pomocą usługi Azure Key Vault:](../azure-resource-manager/templates/key-vault-parameter.md)

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

W tym przykładowym pliku parametrów określa wartości parametrów szablonu zadeklarowanych wcześniej w tym temacie:

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

Szablon ma `resources` obiekt, który jest tablicą, która zawiera definicje dla każdego zasobu do utworzenia i wdrożenia na platformie Azure, takich jak [definicja zasobów aplikacji logiki,](#logic-app-resource-definition)wszelkie [definicje zasobów połączenia](#connection-resource-definitions)i inne zasoby potrzebne aplikacji logiki do wdrożenia.

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
> Szablony mogą zawierać definicje zasobów dla wielu aplikacji logiki, więc upewnij się, że wszystkie zasoby aplikacji logiki określają tę samą grupę zasobów platformy Azure. Po wdrożeniu szablonu do grupy zasobów platformy Azure przy użyciu programu Visual Studio zostanie wyświetlony monit o otwarcie aplikacji logiki. Ponadto projekt grupy zasobów platformy Azure może zawierać więcej niż jeden szablon, więc upewnij się, że po wyświetleniu monitu wybierz plik poprawnych parametrów.

Aby uzyskać ogólne informacje na temat zasobów szablonu i ich atrybutów, zobacz następujące tematy:

* [Zasoby — struktura i składnia szablonu Menedżera zasobów](../azure-resource-manager/templates/template-syntax.md#resources)
* [Najważniejsze wskazówki dotyczące zasobów szablonów](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definicja zasobów aplikacji logiki

Definicja zasobów aplikacji logiki rozpoczyna `properties` się od obiektu, który zawiera następujące informacje:

* Stan aplikacji logiki podczas wdrażania
* Identyfikator dla dowolnego konta integracji używanego przez aplikację logiki
* Definicja przepływu pracy aplikacji logiki
* Obiekt, `parameters` który ustawia wartości do użycia w czasie wykonywania
* Inne informacje o zasobach dotyczące aplikacji logiki, takie jak nazwa, typ, lokalizacja itd.

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
            "accessControl": {}
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

Oto atrybuty, które są specyficzne dla definicji zasobów aplikacji logiki:

| Atrybut | Wymagany | Typ | Opis |
|-----------|----------|------|-------------|
| `state` | Tak | Ciąg | Stan aplikacji logiki podczas `Enabled` wdrażania, w którym `Disabled` oznacza, że aplikacja logiki jest na żywo i oznacza, że aplikacja logiki jest nieaktywna. Na przykład jeśli nie jesteś gotowy do aplikacji logiki, aby przejść na żywo, `Disabled` ale chcesz wdrożyć wersję roboczą, można użyć tej opcji. |
| `integrationAccount` | Nie | Obiekt | Jeśli aplikacja logiki używa konta integracji, który przechowuje artefakty dla scenariuszy business-to-business (B2B), ten obiekt zawiera `id` atrybut, który określa identyfikator konta integracji. |
| `definition` | Tak | Obiekt | Podstawowa definicja przepływu pracy aplikacji logiki, która jest tym samym obiektem, który pojawia się w widoku kodu i jest w pełni opisany w [odwołaniu do schematu dla tematu Język definicji przepływu pracy.](../logic-apps/logic-apps-workflow-definition-language.md) W tej definicji `parameters` przepływu pracy obiekt deklaruje parametry dla wartości do użycia w czasie wykonywania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Definicja przepływu pracy i parametry](#workflow-definition-parameters). <p><p>Aby wyświetlić atrybuty w definicji przepływu pracy aplikacji logiki, przełącz się z "widoku projektu" na "widok kodu" w witrynie Azure portal lub visual studio lub za pomocą narzędzia, takiego jak [Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | Nie | Obiekt | [Wartości parametrów definicji przepływu pracy](#workflow-definition-parameters) do użycia w czasie wykonywania aplikacji logiki. Definicje parametrów dla tych wartości są wyświetlane wewnątrz [obiektu parametrów definicji przepływu pracy](#workflow-definition-parameters). Ponadto jeśli aplikacja logiki używa [łączników zarządzanych](../connectors/apis-list.md) do uzyskiwania dostępu `$connections` do innych usług i systemów, ten obiekt zawiera obiekt, który ustawia wartości połączenia do użycia w czasie wykonywania. |
| `accessControl` | Nie | Obiekt | Do określania atrybutów zabezpieczeń dla aplikacji logiki, takich jak ograniczenie dostępu IP do wyzwalaczy żądań lub uruchamiania danych wejściowych i wyjściowych historii. Aby uzyskać więcej informacji, zobacz [Bezpieczny dostęp do aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Aby uzyskać informacje o zasobach szablonów specyficzne dla aplikacji logiki, kont integracji i artefaktów kont integracji, zobacz [Typy zasobów firmy Microsoft.Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definicja i parametry przepływu pracy

Definicja przepływu pracy aplikacji logiki `definition` pojawia się w `properties` obiekcie, który pojawia się w obiekcie wewnątrz definicji zasobów aplikacji logiki. Ten `definition` obiekt jest tym samym obiektem, który pojawia się w widoku kodu i jest w pełni opisany w [odwołaniu do schematu dla języka definicji przepływu pracy.](../logic-apps/logic-apps-workflow-definition-language.md) Definicja przepływu pracy `parameters` zawiera obiekt deklaracji wewnętrznej, w którym można zdefiniować nowe lub edytować istniejące parametry dla wartości, które są używane przez definicję przepływu pracy w czasie wykonywania. Następnie można odwołać się do tych parametrów wewnątrz wyzwalacza lub akcji w przepływie pracy. Domyślnie ten `parameters` obiekt jest pusty, chyba że aplikacja logiki tworzy połączenia z innymi usługami i systemami za pośrednictwem [łączników zarządzanych](../connectors/apis-list.md).

Aby ustawić wartości parametrów definicji `parameters` przepływu pracy, należy użyć obiektu, który znajduje *się poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki. W tym `parameters` zewnętrznym obiekcie można następnie odwoływać się do wcześniej zadeklarowanych parametrów szablonu, które mogą akceptować wartości podczas wdrażania z pliku parametrów.

> [!TIP]
>
> Najlepszym rozwiązaniem nie należy bezpośrednio odwoływać się do parametrów szablonu, które są oceniane podczas wdrażania, z wewnątrz definicji przepływu pracy. Zamiast tego zadeklarować parametr definicji przepływu pracy, `parameters` który można następnie ustawić w obiekcie, który znajduje *się poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Odwołania do parametrów](#parameter-references).

Ta składnia pokazuje, gdzie można zadeklarować parametry zarówno na poziomie definicji szablonu, jak i przepływu pracy, a także gdzie można ustawić te wartości parametrów, odwołując się do parametrów definicji szablonu i przepływu pracy:

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

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Bezpieczne parametry definicji przepływu pracy

W przypadku parametru definicji przepływu pracy obsługującego poufne informacje, hasła, klucze dostępu lub `securestring` `secureobject` wpisy tajne w czasie wykonywania, zadeklaruj lub edytuj parametr, aby użyć typu lub parametru. Ten parametr można odwoływać się do tego parametru w definicji przepływu pracy i w jej obrębie. Na najwyższym poziomie szablonu zadeklarować parametr, który ma ten sam typ do obsługi tych informacji podczas wdrażania.

Aby ustawić wartość parametru definicji przepływu `parameters` pracy, należy użyć obiektu, który znajduje *się poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki, aby odwołać się do parametru szablonu. Na koniec, aby przekazać wartość do parametru szablonu podczas wdrażania, należy przechowywać tę wartość w [usłudze Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) i odwoływać się do tego magazynu kluczy w [pliku parametrów,](#template-parameter-files) który jest używany przez szablon podczas wdrażania.

W tym przykładowym szablonie pokazano, jak można wykonać te zadania, definiując zabezpieczone parametry, gdy jest to konieczne, aby można było przechowywać ich wartości w usłudze Azure Key Vault:

* Deklarowanie zabezpieczonych parametrów dla wartości używanych do uwierzytelniania dostępu.
* Użyj tych wartości zarówno na poziomie definicji szablonu, jak i przepływu pracy.
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

## <a name="best-practices---workflow-definition-parameters"></a>Najważniejsze wskazówki — parametry definicji przepływu pracy

Aby upewnić się, że projektant aplikacji logiki może poprawnie wyświetlać parametry definicji przepływu pracy, postępuj zgodnie z tymi najlepszymi rozwiązaniami:

* Dołącz `defaultValue` atrybut, który można określić puste wartości, dla wszystkich parametrów, z wyjątkiem wartości, które są poufne lub muszą być zabezpieczone.

* Zawsze używaj zabezpieczonych parametrów dla nazw użytkowników, haseł i wpisów tajnych. Aby ukryć lub chronić wartości parametrów poufnych, postępuj zgodnie ze wskazówkami w następujących tematach:

  * [Zalecenia dotyczące zabezpieczeń dla parametrów akcji](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Zalecenia dotyczące zabezpieczeń dla parametrów w definicjach przepływu pracy](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Przekazywanie wartości bezpiecznych parametrów za pomocą usługi Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Aby uzyskać więcej informacji na temat parametrów definicji przepływu pracy, zobacz [Parametry — Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definicje zasobów połączeń

Gdy aplikacja logiki tworzy i używa połączeń z innymi usługami i systemem `resources` przy użyciu [łączników zarządzanych,](../connectors/apis-list.md)obiekt szablonu zawiera definicje zasobów dla tych połączeń.

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

Definicje zasobów połączeń odwołują się do parametrów najwyższego poziomu szablonu dla ich wartości, co oznacza, że można podać te wartości podczas wdrażania przy użyciu pliku parametrów. Upewnij się, że połączenia używają tej samej grupy zasobów platformy Azure i lokalizacji co aplikacja logiki.

Oto przykładowa definicja zasobu dla połączenia programu Outlook usługi Office 365 i odpowiadające im parametry szablonu:

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

Definicja zasobów aplikacji logiki działa również z definicjami zasobów połączeń w ten sposób:

* Wewnątrz definicji przepływu `parameters` pracy obiekt `$connections` deklaruje parametr dla wartości połączenia do użycia w czasie wykonywania aplikacji logiki. Ponadto wyzwalacz lub akcja, która tworzy połączenie używa odpowiednich `$connections` wartości, które przechodzą przez ten parametr.

* *Poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki, `parameters` inny `$connections` obiekt ustawia wartości do użycia w czasie wykonywania parametru, odwołując się do odpowiednich parametrów szablonu. Te wartości używają wyrażeń szablonu do odwoływania się do zasobów, które bezpiecznie przechowują metadane dla połączeń w aplikacji logiki.

  Na przykład metadane mogą zawierać parametry połączenia i tokeny dostępu, które można przechowywać w [usłudze Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Aby przekazać te wartości do parametrów szablonu, należy odwołać się do tego magazynu kluczy w [pliku parametrów,](#template-parameter-files) który jest używany przez szablon podczas wdrażania. Aby uzyskać więcej informacji na temat różnic w odwoływaniu się do parametrów, zobacz [odwołania do parametrów](#parameter-references) w dalszej części tego tematu.

  Po otwarciu definicji przepływu pracy aplikacji logiki w widoku kodu `$connections` za pośrednictwem portalu Azure lub programu Visual Studio obiekt pojawia się poza definicją przepływu pracy, ale na tym samym poziomie. Ta kolejność w widoku kodu ułatwia odwoływanie się do tych parametrów podczas ręcznej aktualizacji definicji przepływu pracy:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Definicja zasobów aplikacji logiki `dependsOn` ma obiekt, który określa zależności od połączeń używanych przez aplikację logiki.

Każde utworzone połączenie ma unikatową nazwę na platformie Azure. Podczas tworzenia wielu połączeń z tą samą usługą lub systemem każda nazwa połączenia jest dołączana z liczbą, która zwiększa się wraz z utworzonym nowym połączeniem, `office365`na przykład , `office365-1`itd.

W tym przykładzie przedstawiono interakcje między definicją zasobów aplikacji logiki a definicją zasobu połączenia dla programu Office 365 Outlook:

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

### <a name="secure-connection-parameters"></a>Bezpieczne parametry połączenia

W przypadku parametru połączenia obsługującego poufne informacje, hasła, klucze dostępu lub `parameterValues` wpisy tajne definicja zasobu połączenia zawiera obiekt, który określa te wartości w formacie pary nazwa-wartość. Aby ukryć te informacje, można zadeklarować lub edytować `securestring` `secureobject` parametry szablonu dla tych wartości przy użyciu typów lub parametrów. Następnie można przechowywać te informacje w [usłudze Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Aby przekazać te wartości do parametrów szablonu, należy odwołać się do tego magazynu kluczy w [pliku parametrów,](#template-parameter-files) który jest używany przez szablon podczas wdrażania.

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

Po wdrożeniu aplikacja logiki działa end-to-end z prawidłowymi parametrami. Jednak nadal należy autoryzować wszystkie połączenia OAuth do generowania prawidłowych tokenów dostępu do [uwierzytelniania poświadczeń.](../active-directory/develop/authentication-scenarios.md) Aby uzyskać więcej informacji, zobacz [Autoryzowanie połączeń OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Niektóre połączenia obsługują przy użyciu [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) w celu autoryzacji połączeń dla aplikacji logiki [zarejestrowanej w usłudze Azure AD.](../active-directory/develop/quickstart-register-app.md) Na przykład ta definicja zasobu połączenia usługi Azure Data Lake pokazuje, jak odwoływać się do parametrów szablonu, które obsługują informacje jednostki usługi i jak szablon deklaruje te parametry:

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
| `token:TenantId` | Identyfikator katalogu dzierżawy usługi Azure AD |
| `token:grantType` | Żądany typ dotacji, który `client_credentials`musi być . Aby uzyskać więcej informacji, zobacz [Platformę tożsamości firmy Microsoft i przepływ poświadczeń klienta OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definicje parametrów szablonu**

Obiekt najwyższego poziomu `parameters` szablonu deklaruje te parametry dla przykładowego połączenia:

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

Aby uzyskać więcej informacji na temat pracy z podmiotami usługi, zobacz następujące tematy:

* [Tworzenie jednostki usługi przy użyciu portalu Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Tworzenie jednostki usługi platformy Azure przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Tworzenie jednostki usługi z certyfikatem przy użyciu programu Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Odwołania do parametrów

Aby odwołać się do parametrów szablonu, można użyć wyrażeń szablonu z [funkcjami szablonu,](../azure-resource-manager/templates/template-functions.md)które są oceniane podczas wdrażania. Wyrażenia szablonu używają nawiasów kwadratowych (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Aby odwołać się do parametrów definicji przepływu pracy, należy użyć [wyrażeń i funkcji języka definicji przepływu pracy,](../logic-apps/workflow-definition-language-functions-reference.md)które są oceniane w czasie wykonywania. Można zauważyć, że niektóre funkcje szablonu i funkcje definicji przepływu pracy mają taką samą nazwę. Wyrażenia definicji przepływu pracy zaczynają się**@** od symbolu "at" ( ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Wartości parametrów szablonu można przekazać do definicji przepływu pracy dla aplikacji logiki do użycia w czasie wykonywania. Należy jednak unikać używania parametrów szablonu, wyrażeń i składni w definicji przepływu pracy, ponieważ projektant aplikacji logiki nie obsługuje elementów szablonu. Ponadto składnia szablonu i wyrażenia mogą skomplikować kod ze względu na różnice w czasie, gdy wyrażenia są oceniane.

Zamiast tego wykonaj następujące ogólne kroki, aby zadeklarować i odwołać się do parametrów definicji przepływu pracy do użycia w czasie wykonywania, zadeklarować i odwołać się do parametrów szablonu do użycia podczas wdrażania i określić wartości do przekazania podczas wdrażania przy użyciu pliku parametrów. Aby uzyskać szczegółowe informacje, zobacz sekcję [Definicja i parametry przepływu pracy](#workflow-definition-parameters) wcześniej w tym temacie.

1. Utwórz szablon i zadeklarować parametry szablonu dla wartości do zaakceptowania i użycia podczas wdrażania.

1. W definicji przepływu pracy zadeklarować parametry dla wartości do zaakceptowania i użycia w czasie wykonywania. Następnie można odwoływać się do tych wartości w całej definicji przepływu pracy i w nich.

1. W `parameters` obiekcie, który znajduje się *poza* definicją przepływu pracy, ale nadal *wewnątrz* definicji zasobów aplikacji logiki, ustaw wartości parametrów definicji przepływu pracy, odwołując się do odpowiednich parametrów szablonu. W ten sposób można przekazać wartości parametrów szablonu do parametrów definicji przepływu pracy.

1. W pliku parametrów określ wartości szablonu, które mają być używane podczas wdrażania.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Pełny przykładowy szablon

Oto sparametryzowany szablon próbki, który jest używany w przykładach tego tematu:

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

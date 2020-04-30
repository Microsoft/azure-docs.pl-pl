---
title: Tworzenie pliku rozwiązania do zarządzania na platformie Azure | Microsoft Docs
description: Rozwiązania do zarządzania zapewniają spakowane scenariusze zarządzania, które klienci mogą dodawać do środowiska platformy Azure.  Ten artykuł zawiera szczegółowe informacje dotyczące sposobu tworzenia rozwiązań do zarządzania, które mają być używane w Twoim środowisku lub udostępniane klientom.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275180"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Tworzenie pliku rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.  

Rozwiązania do zarządzania na platformie Azure są implementowane jako [szablony Menedżer zasobów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Głównym zadaniem w nauce tworzenia rozwiązań do zarządzania jest zapoznanie się z sposobem [tworzenia szablonu](../../azure-resource-manager/templates/template-syntax.md).  Ten artykuł zawiera unikatowe szczegóły dotyczące szablonów używanych na potrzeby rozwiązań oraz sposób konfigurowania typowych zasobów rozwiązania.


## <a name="tools"></a>Narzędzia

Możesz użyć dowolnego edytora tekstu do pracy z plikami rozwiązania, ale zalecamy korzystanie z funkcji dostępnych w programie Visual Studio lub Visual Studio Code zgodnie z opisem w poniższych artykułach.

- [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Praca z szablonami usługi Azure Resource Manager w programie Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Podstawowa struktura pliku rozwiązania do zarządzania jest taka sama jak [szablon Menedżer zasobów](../../azure-resource-manager/templates/template-syntax.md#template-format), który jest następujący:  W każdej z poniższych sekcji opisano elementy najwyższego poziomu i ich zawartość w rozwiązaniu.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/templates/template-syntax.md#parameters) są wartościami, które są wymagane od użytkownika podczas instalacji rozwiązania do zarządzania.  Istnieją standardowe parametry, które będą dostępne dla wszystkich rozwiązań, i można dodać dodatkowe parametry wymagane dla danego rozwiązania.  Sposób, w jaki użytkownicy będą podawać wartości parametrów podczas instalacji rozwiązania, będą zależeć od określonego parametru i sposobu instalowania rozwiązania.

Gdy użytkownik [instaluje rozwiązanie do zarządzania](solutions.md#install-a-monitoring-solution) za pomocą witryny Azure Marketplace lub szablonów szybkiego startu platformy Azure, zostanie wyświetlony monit o wybranie [log Analytics obszaru roboczego i konta usługi Automation](solutions.md#log-analytics-workspace-and-automation-account).  Są one używane do wypełniania wartości każdego z parametrów standardowych.  Użytkownik nie jest monitowany o bezpośrednie podanie wartości parametrów standardowych, ale jest monitowany o podanie wartości dla wszelkich dodatkowych parametrów.


Poniżej przedstawiono przykładowy parametr.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

W poniższej tabeli opisano atrybuty parametru.

| Atrybut | Opis |
|:--- |:--- |
| type |Typ danych dla parametru. Kontrolka wejściowa wyświetlana dla użytkownika zależy od typu danych.<br><br>pole listy rozwijanej bool<br>ciąg — pole tekstowe<br>pole tekstowe int<br>SecureString — pole hasła<br> |
| category |Opcjonalna kategoria dla parametru.  Parametry w tej samej kategorii są grupowane razem. |
|  — formant |Dodatkowa funkcjonalność parametrów ciągu.<br><br>wyświetlana jest kontrolka DateTime-DateTime.<br>Identyfikator GUID — wartość GUID jest generowana automatycznie, a parametr nie jest wyświetlany. |
| description |Opcjonalny opis parametru.  Wyświetlany w dymku informacyjnym obok parametru. |

### <a name="standard-parameters"></a>Parametry standardowe
W poniższej tabeli wymieniono standardowe parametry dla wszystkich rozwiązań do zarządzania.  Te wartości są wypełniane dla użytkownika, ale nie są wyświetlane w przypadku, gdy rozwiązanie zostanie zainstalowane z witryny Azure Marketplace lub szablonów szybkiego startu.  Użytkownik musi podać wartości dla nich, jeśli rozwiązanie jest zainstalowane z inną metodą.

> [!NOTE]
> Interfejs użytkownika w portalu Azure Marketplace i Szablony szybkiego startu oczekują nazw parametrów w tabeli.  Jeśli używasz innych nazw parametrów, użytkownik zostanie poproszony o podanie ich, a nie zostanie automatycznie wypełniony.
>
>

| Parametr | Typ | Opis |
|:--- |:--- |:--- |
| accountName |ciąg |Nazwa konta Azure Automation. |
| pricingTier |ciąg |Warstwa cenowa zarówno Log Analytics obszaru roboczego, jak i konta Azure Automation. |
| regionId |ciąg |Region konta Azure Automation. |
| solutionName |ciąg |Nazwa rozwiązania.  W przypadku wdrażania rozwiązania za pomocą szablonów szybkiego startu należy zdefiniować rozwiązanie jako parametr, aby można było zdefiniować ciąg zamiast tego, aby użytkownik mógł go określić. |
| workspaceName |ciąg |Log Analytics nazwa obszaru roboczego. |
| workspaceRegionId |ciąg |Region obszaru roboczego Log Analytics. |


Poniżej znajduje się struktura standardowych parametrów, które można kopiować i wklejać do pliku rozwiązania.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Odwołujesz się do wartości parametrów w innych elementach rozwiązania z **parametrami składni ("Nazwa parametru")**.  Na przykład aby uzyskać dostęp do nazwy obszaru roboczego, należy użyć **parametrów ("WorkspaceName")**

## <a name="variables"></a>Zmienne
[Zmienne](../../azure-resource-manager/templates/template-syntax.md#variables) to wartości, które będą używane w pozostałej części rozwiązania do zarządzania.  Te wartości nie są widoczne dla użytkownika instalującego rozwiązanie.  Są one przeznaczone do udostępniania autorowi pojedynczej lokalizacji, gdzie mogą zarządzać wartościami, które mogą być używane wiele razy w całym rozwiązaniu. Należy umieścić wszystkie wartości charakterystyczne dla Twojego rozwiązania w zmiennych, a nie stałe kodowanie ich w elemencie **resources** .  Sprawia to, że kod jest bardziej czytelny i pozwala łatwo zmienić te wartości w nowszych wersjach.

Poniżej znajduje się przykład elementu **zmiennych** z typowymi parametrami używanymi w rozwiązaniach.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Odwołujesz się do wartości zmiennych za pomocą rozwiązania ze **zmiennymi składni ("nazwa zmiennej")**.  Na przykład aby uzyskać dostęp do zmiennej SolutionName, należy użyć **zmiennych ("SolutionName")**.

Można również zdefiniować złożone zmienne, które mają wiele zestawów wartości.  Są one szczególnie przydatne w rozwiązaniach do zarządzania, w których definiujesz wiele właściwości dla różnych typów zasobów.  Można na przykład zmienić strukturę zmiennych rozwiązań przedstawionych powyżej do poniższego.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

W tym przypadku należy odwołać się do wartości zmiennych za pomocą rozwiązania ze **zmiennymi składni ("Zmienna name"). Właściwość**.  Aby na przykład uzyskać dostęp do zmiennej nazwy rozwiązania, należy użyć **zmiennych ("rozwiązanie"). Nazwa**.

## <a name="resources"></a>Zasoby
[Zasoby](../../azure-resource-manager/templates/template-syntax.md#resources) definiują różne zasoby, które będą instalowane i konfigurowane w ramach rozwiązania do zarządzania.  Będzie to największa i najbardziej złożona część szablonu.  Możesz uzyskać strukturę i pełen opis elementów zasobów w [szablonach tworzenia Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md#resources).  Różne zasoby, które zwykle zdefiniujesz, są szczegółowo opisane w innych artykułach w tej dokumentacji. 


### <a name="dependencies"></a>Zależności
Element **dependsOn** określa [zależność](../../azure-resource-manager/templates/define-resource-dependency.md) od innego zasobu.  Po zainstalowaniu rozwiązania zasób nie zostanie utworzony, dopóki nie zostaną utworzone wszystkie jego zależności.  Na przykład rozwiązanie może [uruchomić element Runbook](solutions-resources-automation.md#runbooks) , gdy zostanie on zainstalowany przy użyciu [zasobu zadania](solutions-resources-automation.md#automation-jobs).  Zasób zadania będzie zależny od zasobu elementu Runbook, aby upewnić się, że element Runbook został utworzony przed utworzeniem zadania.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics obszaru roboczego i konta usługi Automation
Rozwiązania do zarządzania wymagają, aby [obszar roboczy log Analytics](../../azure-monitor/platform/manage-access.md) mógł zawierać widoki i [konto usługi Automation](../../automation/automation-security-overview.md#automation-account-overview) zawierające elementy Runbook i powiązane zasoby.  Muszą one być dostępne przed utworzeniem zasobów w rozwiązaniu i nie powinny być zdefiniowane w samym rozwiązaniu.  Użytkownik [określi obszar roboczy i konto](solutions.md#log-analytics-workspace-and-automation-account) podczas wdrażania rozwiązania, ale jako autora należy wziąć pod uwagę następujące kwestie.


## <a name="solution-resource"></a>Zasób rozwiązania
Każde rozwiązanie wymaga wpisu zasobu w elemencie **resources** , który definiuje samo rozwiązanie.  Będzie to miało typ **Microsoft. OperationsManagement/Solutions** i ma następującą strukturę. Obejmuje to [standardowe parametry](#parameters) i [zmienne](#variables) , które są zwykle używane do definiowania właściwości rozwiązania.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Zależności
Aby można było utworzyć rozwiązanie, zasób rozwiązania musi mieć [zależność](../../azure-resource-manager/templates/define-resource-dependency.md) od każdego innego zasobu w rozwiązaniu.  W tym celu Dodaj wpis dla każdego zasobu w elemencie **dependsOn** .

### <a name="properties"></a>Właściwości
Zasób rozwiązania ma właściwości w poniższej tabeli.  Obejmuje to zasoby, do których istnieją odwołania i zawarte w rozwiązaniu, które definiują sposób zarządzania zasobem po zainstalowaniu rozwiązania.  Każdy zasób w rozwiązaniu powinien być wymieniony we właściwości **referencedResources** lub **containedResources** .

| Właściwość | Opis |
|:--- |:--- |
| workspaceResourceId |Identyfikator obszaru roboczego log Analytics w formularzu * \<identyfikator grupy zasobów> nazwa\<\>obszaru roboczego/Providers/Microsoft.operationalinsights/Workspaces/*. |
| referencedResources |Lista zasobów w rozwiązaniu, które nie powinny zostać usunięte po usunięciu rozwiązania. |
| containedResources |Lista zasobów w rozwiązaniu, które należy usunąć po usunięciu rozwiązania. |

Powyższy przykład dotyczy rozwiązania z elementem Runbook, harmonogramem i widokiem.  W elemencie **Properties** *odwołuje* się do harmonogramu i elementu Runbook, dzięki czemu nie zostaną usunięte po usunięciu rozwiązania.  Widok jest *zawarty* , dlatego jest usuwany po usunięciu rozwiązania.

### <a name="plan"></a>Planowanie
Jednostka **planu** zasobu rozwiązania ma właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| name |Nazwa rozwiązania. |
| Wersja |Wersja rozwiązania określona przez autora. |
| product |Unikatowy ciąg identyfikujący rozwiązanie. |
| publisher |Wydawca rozwiązania. |



## <a name="next-steps"></a>Następne kroki
* [Dodaj zapisane wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy Runbook i inne zasoby usługi Automation](solutions-resources-automation.md) do rozwiązania do zarządzania.
* Poznaj szczegóły dotyczące [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Przeszukaj [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates) , aby zapoznać się z przykładami różnych szablonów Menedżer zasobów.

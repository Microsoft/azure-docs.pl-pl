---
title: Azure Monitor skoroszyty i szablony Azure Resource Manager
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami Azure Monitor wdrożonymi za pośrednictwem Azure Resource Manager szablonów
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929731"
---
# <a name="programmatically-manage-workbooks"></a>Programowe Zarządzanie skoroszytami

Właściciele zasobów mają możliwość programistycznego tworzenia skoroszytów i zarządzania nimi za pomocą szablonów Menedżer zasobów.

Może to być przydatne w scenariuszach takich jak:
* Wdrażanie raportów analiz specyficznych dla organizacji lub dla domeny wraz z wdrożeniami zasobów. Na przykład można wdrożyć dla nowych aplikacji lub maszyn wirtualnych odpowiednie dla organizacji skoroszyty wydajności i niepowodzeń.
* Wdrażanie standardowych raportów lub pulpitów nawigacyjnych przy użyciu skoroszytów dla istniejących zasobów.

Skoroszyt zostanie utworzony w odpowiedniej grupie podrzędnej i w podanej w niej sposób z zawartością określoną w szablonach Menedżer zasobów.

Istnieją dwa typy zasobów skoroszytów, które mogą być zarządzane programowo:
* [Szablony skoroszytów](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Wystąpienia skoroszytu](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Azure Resource Manager szablon do wdrażania szablonu skoroszytu

1. Otwórz skoroszyt, który ma zostać wdrożony programowo.
2. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
3. Otwórz _Edytor zaawansowany_ przy użyciu _</>_ przycisku na pasku narzędzi.
4. Upewnij się, że korzystasz z karty _szablonu galerii_ .

    ![Karta szablonu galerii](./media/workbooks-automate/gallery-template.png)
1. Skopiuj kod JSON w szablonie galerii do Schowka.
2. Poniżej znajduje się przykładowy szablon Azure Resource Manager, który wdraża szablon skoroszytu do Azure Monitor galerii skoroszytów. Wklej skopiowany kod JSON w miejscu `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Szablon Azure Resource Manager odwołania, który tworzy szablon skoroszytu, można znaleźć [tutaj](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. W `galleries` obiekcie Wypełnij `name` `category` wartości i. Więcej informacji o [parametrach](#parameters) znajduje się w następnej sekcji.
2. Wdróż ten szablon Azure Resource Manager przy użyciu [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [interfejsu wiersza polecenia](../../azure-resource-manager/templates/deploy-cli.md), [programu PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)itd.
3. Otwórz Azure Portal i przejdź do galerii skoroszytów wybranego w szablonie Azure Resource Manager. W przykładzie szablonu przejdź do galerii skoroszytów Azure Monitor:
    1. Otwórz Azure Portal i przejdź do Azure Monitor
    2. Otwórz `Workbooks` z spisu treści
    3. Znajdź szablon w galerii w obszarze Kategoria `Deployed Templates` (będzie to jeden z elementów purpurowych).

### <a name="parameters"></a>Parametry

|Parametry                |Objaśnienie                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Nazwa zasobu szablonu skoroszytu w Azure Resource Manager.                                  |
|`type`                    | Zawsze Microsoft. Insights/workbooktemplates                                                            |
| `location`               | Lokalizacja platformy Azure, w której zostanie utworzony skoroszyt.                                               |
| `apiVersion`             | wersja zapoznawcza 2019-10-17                                                                                     |
| `type`                   | Zawsze Microsoft. Insights/workbooktemplates                                                            |
| `galleries`              | Zestaw galerii, w których będzie pokazywany ten szablon skoroszytu.                                                |
| `gallery.name`           | Przyjazna nazwa szablonu skoroszytu w galerii.                                             |
| `gallery.category`       | Grupa w galerii, w której ma zostać umieszczony szablon.                                                     |
| `gallery.order`          | Liczba, która decyduje o kolejności wyświetlania szablonu w kategorii w galerii. Niższa kolejność oznacza wyższy priorytet. |
| `gallery.resourceType`   | Typ zasobu odpowiadający galerii. Jest to zazwyczaj ciąg typu zasobu odpowiadający zasobowi (na przykład Microsoft. operationalinsights/Workspaces). |
|`gallery.type`            | Określany jako typ skoroszytu jest to unikatowy klucz, który odróżnia galerię w ramach typu zasobu. Application Insights, na przykład, mają typy `workbook` i `tsg` odpowiadają różnym galeriom skoroszytów. |

### <a name="galleries"></a>Galerie

| Galeria                                        | Typ zasobu                                      | Typ skoroszytu |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Skoroszyty w Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| Szczegółowe informacje o maszynie wirtualnej w Azure Monitor                   | `Azure Monitor`                                    | `vm-insights` |
| Skoroszyty w obszarze roboczym usługi log Analytics           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Skoroszyty w Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Wskazówki dotyczące rozwiązywania problemów w Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Użycie w Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Skoroszyty w usłudze Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Skoroszyty w grupach zasobów                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Skoroszyty w Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Szczegółowe informacje o maszynie wirtualnej na maszynach wirtualnych                | `microsoft.compute/virtualmachines`                | `insights`    |
| Szczegółowe informacje o maszynie wirtualnej w zestawach skalowania maszyn wirtualnych      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Azure Resource Manager szablon do wdrażania wystąpienia skoroszytu

1. Otwórz skoroszyt, który ma zostać wdrożony programowo.
2. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
3. Otwórz _Edytor zaawansowany_ przy użyciu _</>_ przycisku na pasku narzędzi.
4. W edytorze Przełącz _typ szablonu_ na _Menedżer zasobów szablon_.
5. Szablon Menedżer zasobów do utworzenia zostanie wyświetlony w edytorze. Skopiuj zawartość i użyj jej jako-lub Scal ją z większym szablonem, który również wdraża zasób docelowy.

    ![Obraz przedstawiający sposób pobierania szablonu Menedżer zasobów z poziomu interfejsu użytkownika skoroszytu](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Przykładowy szablon Azure Resource Manager
Ten szablon pokazuje, jak wdrożyć prosty skoroszyt wyświetlający "Hello world!"
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parametry szablonu

| Parametr | Objaśnienie |
| :------------- |:-------------|
| `workbookDisplayName` | Przyjazna nazwa skoroszytu, który jest używany w galerii lub zapisanej liście. Musi być unikatowa w zakresie grupy zasobów i źródła |
| `workbookType` | Galeria, w której będzie pokazywany skoroszyt. Obsługiwane wartości to skoroszyt, `tsg` , Azure monitor itd. |
| `workbookSourceId` | Identyfikator wystąpienia zasobu, do którego zostanie skojarzony skoroszyt. Nowy skoroszyt zostanie wyświetlony względem tego wystąpienia zasobu — na przykład w tabeli zawartości w obszarze _skoroszyt_. Jeśli chcesz, aby skoroszyt był wyświetlany w galerii skoroszytów w Azure Monitor, użyj ciągu _Azure monitor_ zamiast identyfikatora zasobu. |
| `workbookId` | Unikatowy identyfikator GUID dla tego wystąpienia skoroszytu. Użyj _[newGuid ()]_ , aby automatycznie utworzyć nowy identyfikator GUID. |
| `kind` | Służy do określania, czy utworzony skoroszyt jest udostępniony, czy prywatny. Użyj wartości _udostępnione_ dla udostępnionych skoroszytów i _użytkownika_ dla prywatnych. |
| `location` | Lokalizacja platformy Azure, w której zostanie utworzony skoroszyt. Użyj _[Resource Group (). Location]_ , aby utworzyć ją w tej samej lokalizacji co grupa zasobów |
| `serializedData` | Zawiera zawartość lub ładunek do użycia w skoroszycie. Pobieranie wartości przy użyciu szablonu Menedżer zasobów z poziomu interfejsu użytkownika skoroszytów |

### <a name="workbook-types"></a>Typy skoroszytów
Typy skoroszytów określają, który typ galerii skoroszytów zostanie wyświetlony nowy wystąpienie skoroszytu. Dostępne opcje:

| Type | Lokalizacja galerii |
| :------------- |:-------------|
| `workbook` | Wartość domyślna używana w większości raportów, w tym Galeria skoroszytów Application Insights, Azure Monitor itd.  |
| `tsg` | Galeria przewodników rozwiązywania problemów w Application Insights |
| `usage` | Galeria _więcej informacji_ w obszarze _użycie_ w Application Insights |

### <a name="limitations"></a>Ograniczenia
Ze względów technicznych ten mechanizm nie może być używany do tworzenia wystąpień skoroszytów w galerii _skoroszytów_ Application Insights. Pracujemy nad tym ograniczeniem. W tym czasie zalecamy używanie galerii przewodnika rozwiązywania problemów (skoroszyttype: `tsg` ) do wdrażania Application Insights powiązanych skoroszytów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, w jaki sposób skoroszyty są używane do [obsługi nowego Azure monitor na potrzeby środowiska magazynowego](../insights/storage-insights-overview.md).

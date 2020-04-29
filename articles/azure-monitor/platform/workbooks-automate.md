---
title: Azure Monitor skoroszyty i szablony Azure Resource Manager
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami Azure Monitor wdrożonymi za pośrednictwem Azure Resource Manager szablonów
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658408"
---
# <a name="programmatically-manage-workbooks"></a>Programowe Zarządzanie skoroszytami

Właściciele zasobów mają możliwość programistycznego tworzenia skoroszytów i zarządzania nimi za pomocą szablonów Menedżer zasobów. 

Może to być przydatne w scenariuszach takich jak:
* Wdrażanie raportów analiz specyficznych dla organizacji lub dla domeny wraz z wdrożeniami zasobów. Na przykład można wdrożyć dla nowych aplikacji lub maszyn wirtualnych odpowiednie dla organizacji skoroszyty wydajności i niepowodzeń.
* Wdrażanie standardowych raportów lub pulpitów nawigacyjnych przy użyciu skoroszytów dla istniejących zasobów.

Skoroszyt zostanie utworzony w odpowiedniej grupie podrzędnej i w podanej w niej sposób z zawartością określoną w szablonach Menedżer zasobów.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager szablon do wdrażania skoroszytów
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

| Parametr | Wyjaśnienie |
| :------------- |:-------------|
| `workbookDisplayName` | Przyjazna nazwa skoroszytu, który jest używany w galerii lub zapisanej liście. Musi być unikatowa w zakresie grupy zasobów i źródła |
| `workbookType` | Galeria, w której będzie pokazywany skoroszyt. Obsługiwane wartości to skoroszyt, `tsg`, Azure monitor itd. |
| `workbookSourceId` | Identyfikator wystąpienia zasobu, do którego zostanie skojarzony skoroszyt. Nowy skoroszyt zostanie wyświetlony względem tego wystąpienia zasobu — na przykład w tabeli zawartości w obszarze _skoroszyt_. Jeśli chcesz, aby skoroszyt był wyświetlany w galerii skoroszytów w Azure Monitor, użyj ciągu _Azure monitor_ zamiast identyfikatora zasobu. |
| `workbookId` | Unikatowy identyfikator GUID dla tego wystąpienia skoroszytu. Użyj _[newGuid ()]_ , aby automatycznie utworzyć nowy identyfikator GUID. |
| `kind` | Służy do określania, czy utworzony skoroszyt jest udostępniony, czy prywatny. Użyj wartości _udostępnione_ dla udostępnionych skoroszytów i _użytkownika_ dla prywatnych. |
| `location` | Lokalizacja platformy Azure, w której zostanie utworzony skoroszyt. Użyj _[Resource Group (). Location]_ , aby utworzyć ją w tej samej lokalizacji co grupa zasobów |
| `serializedData` | Zawiera zawartość lub ładunek do użycia w skoroszycie. Pobieranie wartości przy użyciu szablonu Menedżer zasobów z poziomu interfejsu użytkownika skoroszytów |

### <a name="workbook-types"></a>Typy skoroszytów
Typy skoroszytów określają, który typ galerii skoroszytów zostanie wyświetlony nowy wystąpienie skoroszytu. Dostępne opcje:

| Typ | Lokalizacja galerii |
| :------------- |:-------------|
| `workbook` | Wartość domyślna używana w większości raportów, w tym Galeria skoroszytów Application Insights, Azure Monitor itd.  |
| `tsg` | Galeria przewodników rozwiązywania problemów w Application Insights |
| `usage` | Galeria _więcej informacji_ w obszarze _użycie_ w Application Insights |

### <a name="limitations"></a>Ograniczenia
Ze względów technicznych ten mechanizm nie może być używany do tworzenia wystąpień skoroszytów w galerii _skoroszytów_ Application Insights. Pracujemy nad tym ograniczeniem. W tym czasie zalecamy używanie galerii przewodnika rozwiązywania problemów (skoroszyttype: `tsg`) do wdrażania Application Insights powiązanych skoroszytów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, w jaki sposób skoroszyty są używane do [obsługi nowego Azure monitor na potrzeby środowiska magazynowego](../insights/storage-insights-overview.md).


---
title: Tworzenie obszaru roboczego Log Analytics przy użyciu Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć obszar roboczy Log Analytics, aby umożliwić zarządzanie rozwiązaniami i zbieraniem danych w środowiskach w chmurze i lokalnych przy użyciu Azure PowerShell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3547062f100eeb4dfa741a0566d737a0eac776a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605111"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Utwórz obszar roboczy Log Analytics z Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. W tym przewodniku szybki start pokazano, jak za pomocą modułu Azure PowerShell wdrożyć obszar roboczy Log Analytics w Azure Monitor. Obszar roboczy Log Analytics jest unikatowym środowiskiem dla Azure Monitor danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Musisz mieć Log Analytics obszar roboczy, jeśli zamierzasz zbierać dane z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji  
* Komputery lokalne monitorowane przez System Center Operations Manager  
* Kolekcje urządzeń z Configuration Manager  
* Dane diagnostyczne lub dziennika z usługi Azure Storage  
 
W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w danym środowisku, zobacz następujące tematy:

* [Zbieranie danych z usługi Azure Virtual Machines](../learn/quick-collect-azurevm.md)
* [Zbieranie danych z hybrydowego komputera z systemem Linux](../learn/quick-collect-linux-computer.md)
* [Zbieranie danych z hybrydowego komputera z systemem Windows](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga Azure PowerShell AZ module. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Utwórz obszar roboczy przy użyciu funkcji [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Poniższy przykład tworzy obszar roboczy w lokalizacji *Wschodnie* przy użyciu szablonu Menedżer zasobów z komputera lokalnego. Szablon JSON jest skonfigurowany tak, aby monitował o nazwę obszaru roboczego i określać wartość domyślną dla innych parametrów, które prawdopodobnie będą używane jako Konfiguracja standardowa w danym środowisku. 

Aby uzyskać informacje o obsługiwanych regionach, zobacz [regiony log Analytics jest dostępny w](https://azure.microsoft.com/regions/services/) i Wyszukaj Azure monitor w polu **Wyszukaj produkt** . 

Następujące parametry ustawiają wartość domyślną:

* Lokalizacja — wartość domyślna to Wschodnie stany USA
* jednostka SKU — wartość domyślna to nowa warstwa cenowa według GB wydana w modelu cen z kwietnia 2018

>[!WARNING]
>W przypadku tworzenia lub konfigurowania obszaru roboczego Log Analytics w ramach subskrypcji, która została wybrana w nowym modelu cen 2018 kwietnia, jedyną prawidłową warstwą cenową Log Analytics jest **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Szablon tworzenia i wdrażania

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edytuj szablon w celu spełnienia wymagań. Zapoznaj się z tematem dokumentacja [szablonu Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) , aby dowiedzieć się, jakie właściwości i wartości są obsługiwane. 
3. Zapisz ten plik jako **deploylaworkspacetemplate. JSON** w folderze lokalnym.   
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyj następujących poleceń z folderu zawierającego szablon. Po wyświetleniu monitu o nazwę obszaru roboczego Podaj nazwę globalnie unikatową we wszystkich subskrypcjach platformy Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego:

![Przykładowy wynik po zakończeniu wdrażania](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy dostępny jest obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiać wyszukiwania w dziennikach, aby analizować te dane, i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i szczegółowe informacje analityczne.  

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Diagnostyka Azure lub Azure Storage, zobacz [zbieranie dzienników usług platformy Azure i metryki do użycia w programie Azure monitor](../platform/collect-azure-metrics-logs.md).  
* Dodaj [System Center Operations Manager jako źródło danych](../platform/om-agents.md) , aby zebrać dane z agentów zgłaszających Operations Manager grupy zarządzania i zapisać je w obszarze roboczym log Analytics.  
* Połącz [Configuration Manager](../platform/collect-sccm.md) , aby zaimportować komputery należące do kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) oraz sposób dodawania lub usuwania rozwiązania z obszaru roboczego.

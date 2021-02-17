---
title: Tworzenie obszaru roboczego Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć obszar roboczy Log Analytics, aby umożliwić zarządzanie rozwiązaniami i zbieraniem danych w środowiskach w chmurze i lokalnych przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 09e98429b42ced58849851f5bb26477adaec9d68
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619543"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Tworzenie obszaru roboczego Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure 2,0

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub za pomocą skryptów. Ten przewodnik Szybki Start przedstawia sposób wdrażania obszaru roboczego Log Analytics w programie Azure Monitor przy użyciu interfejsu wiersza polecenia platformy Azure 2,0. Obszar roboczy Log Analytics jest unikatowym środowiskiem dla Azure Monitor danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Musisz mieć Log Analytics obszar roboczy, jeśli zamierzasz zbierać dane z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji  
* Komputery lokalne monitorowane przez System Center Operations Manager  
* Kolekcje urządzeń z Configuration Manager  
* Dane diagnostyczne lub dziennika z usługi Azure Storage  

W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w danym środowisku, zobacz następujące tematy:

* [Zbieranie danych z usługi Azure Virtual Machines](../vm/quick-collect-azurevm.md)
* [Zbieranie danych z hybrydowego komputera z systemem Linux](../vm/quick-collect-linux-computer.md)
* [Zbieranie danych z hybrydowego komputera z systemem Windows](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.30 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Utwórz obszar roboczy za pomocą [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). Poniższy przykład tworzy obszar roboczy w lokalizacji *Wschodnie* przy użyciu szablonu Menedżer zasobów z komputera lokalnego. Szablon JSON jest skonfigurowany tak, aby monitował o nazwę obszaru roboczego i określać wartość domyślną dla innych parametrów, które prawdopodobnie będą używane jako Konfiguracja standardowa w danym środowisku. Możesz również przechowywać szablon na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

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

2. Edytuj szablon w celu spełnienia wymagań. Zapoznaj się z tematem dokumentacja [szablonu Microsoft. OperationalInsights/Workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) , aby dowiedzieć się, jakie właściwości i wartości są obsługiwane.
3. Zapisz ten plik jako **deploylaworkspacetemplate.jsw** folderze lokalnym.   
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyj następujących poleceń z folderu zawierającego szablon. Po wyświetleniu monitu o nazwę obszaru roboczego Podaj nazwę globalnie unikatową we wszystkich subskrypcjach platformy Azure.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego:

![Przykładowy wynik po zakończeniu wdrażania](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Podczas tworzenia obszaru roboczego, który został usunięty w ciągu ostatnich 14 dni i w [stanie usuwania nietrwałego](../logs/delete-workspace.md#soft-delete-behavior), operacja może mieć różny wynik w zależności od konfiguracji obszaru roboczego:
1. Jeśli podano tę samą nazwę obszaru roboczego, grupę zasobów, subskrypcję i region, jak w usuniętym obszarze roboczym, obszar roboczy zostanie odzyskany, w tym jego dane, konfiguracja i agenci połączone.
2. W przypadku użycia tej samej nazwy obszaru roboczego, ale innej grupy zasobów, subskrypcji lub regionu zostanie wyświetlony błąd *Nazwa obszaru roboczego "Przestrzeń nazw" nie jest unikatowa* lub powoduje *konflikt*. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego i utworzyć nowy obszar roboczy o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usuwanie:
   * [Odzyskiwanie](../logs/delete-workspace.md#recover-workspace) obszaru roboczego
   * [Trwałe usunięcie](../logs/delete-workspace.md#permanent-workspace-delete) obszaru roboczego
   * Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego

## <a name="next-steps"></a>Następne kroki
Teraz, gdy dostępny jest obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiać wyszukiwania w dziennikach, aby analizować te dane, i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i szczegółowe informacje analityczne.  

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Diagnostyka Azure lub Azure Storage, zobacz [zbieranie dzienników usług platformy Azure i metryki do użycia w programie log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Dodaj [System Center Operations Manager jako źródło danych](../agents/om-agents.md) , aby zebrać dane z agentów zgłaszających Operations Manager grupy zarządzania i zapisać je w obszarze roboczym log Analytics.  
* Połącz [Configuration Manager](../logs/collect-sccm.md) , aby zaimportować komputery należące do kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) oraz sposób dodawania lub usuwania rozwiązania z obszaru roboczego.


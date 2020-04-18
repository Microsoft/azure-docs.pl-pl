---
title: Tworzenie obszaru roboczego usługi Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć obszar roboczy usługi Log Analytics, aby umożliwić rozwiązania do zarządzania i zbieranie danych w środowiskach chmurowych i lokalnych za pomocą interfejsu wiersza polecenia platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0e91bc9c994a48b335c3ccb7373a9f4f5dc6d1e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605092"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Tworzenie obszaru roboczego usługi Log Analytics za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub za pomocą skryptów. Ten przewodnik Szybki start pokazuje, jak wdrożyć obszar roboczy usługi Azure CLI 2.0 za pomocą interfejsu wiersza polecenia 2.0 w usłudze Azure Monitor. Obszar roboczy usługi Log Analytics to unikatowe środowisko dla danych dziennika usługi Azure Monitor. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Jeśli zamierzasz zbierać dane z następujących źródeł, potrzebujesz obszaru roboczego usługi Log Analytics:

* Zasoby platformy Azure w ramach subskrypcji  
* Komputery lokalne monitorowane przez program System Center Operations Manager  
* Kolekcje urządzeń z programu Configuration Manager  
* Dane diagnostyczne lub dziennika z usługi Azure Storage  

W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w twoim środowisku, zobacz następujące tematy:

* [Zbieranie danych z maszyn wirtualnych platformy Azure](../learn/quick-collect-azurevm.md)
* [Zbieranie danych z hybrydowego komputera z systemem Linux](../learn/quick-collect-linux-computer.md)
* [Zbieranie danych z hybrydowego komputera z systemem Windows](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Tworzenie obszaru roboczego z [tworzeniem wdrożenia grupy az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Poniższy przykład tworzy obszar roboczy w lokalizacji *eastus* przy użyciu szablonu Menedżera zasobów z komputera lokalnego. Szablon JSON jest skonfigurowany tak, aby monitował tylko o nazwę obszaru roboczego i określa wartość domyślną dla innych parametrów, które prawdopodobnie byłyby używane jako standardowa konfiguracja w twoim środowisku. Możesz też przechowywać szablon na koncie magazynu platformy Azure w celu uzyskania dostępu współużytkowego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby uzyskać informacje na temat obsługiwanych regionów, zobacz [regiony Usługi Log Analytics są dostępne](https://azure.microsoft.com/regions/services/) i wyszukaj usługę Azure Monitor w polu **Wyszukaj produkt.**

Następujące parametry ustawiają wartość domyślną:

* lokalizacja — domyślnie wschodnie stany USA
* sku - domyślnie nowa warstwa cenowa Per-GB wydana w modelu cenowym z kwietnia 2018 r.

>[!WARNING]
>Jeśli tworzenie lub konfigurowanie obszaru roboczego usługi Log Analytics w ramach subskrypcji, która wybrała nowy model cenowy z kwietnia 2018 r., jedyną prawidłową warstwą cenową usługi Log Analytics jest **PerGB2018**.
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

2. Edytuj szablon, aby spełnić wymagania. Przejrzyj odwołanie do [szablonu Microsoft.OperationalInsights/workspaces,](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) aby dowiedzieć się, jakie właściwości i wartości są obsługiwane.
3. Zapisz ten plik jako **deploylaworkspacetemplate.json** w folderze lokalnym.   
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyj następujących poleceń z folderu zawierającego szablon. Po wyświetleniu monitu o podanie nazwy obszaru roboczego podaj nazwę, która jest globalnie unikatowa we wszystkich subskrypcjach platformy Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego, który zawiera wynik:

![Przykładowy wynik po zakończeniu wdrażania](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz dostępny obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchomić wyszukiwania dzienników w celu przeanalizowania tych danych i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i analizy analityczne.  

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Azure Diagnostics lub usługi Azure storage, zobacz [Zbieranie dzienników i metryk usługi platformy Azure do użycia w usłudze Log Analytics.](../platform/collect-azure-metrics-logs.md)  
* Dodaj [program System Center Operations Manager jako źródło danych,](../platform/om-agents.md) aby zbierać dane od agentów raportujących grupę zarządzania programu Operations Manager i przechowywać ją w obszarze roboczym usługi Log Analytics.  
* Połącz [menedżera konfiguracji](../platform/collect-sccm.md) z komputerami importu, które są członkami kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) i sposób dodawania lub usuwania rozwiązania z obszaru roboczego.

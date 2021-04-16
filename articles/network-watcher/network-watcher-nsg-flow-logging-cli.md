---
title: Zarządzanie dziennikami przepływu sieciowej organizacji zabezpieczeń — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowej grupy zabezpieczeń w usłudze Azure Network Watcher za pomocą interfejsu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535254"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher która umożliwia wyświetlanie informacji o przychodzącym i wychodzącym ruchu IP za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla procesów, kartę sieciową, do których ma zastosowanie przepływ, 5-krotną informację o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) oraz informację o tym, czy ruch został dozwolony lub odrzucony.

Aby wykonać kroki opisane w tym artykule, musisz zainstalować interfejs wiersza polecenia platformy Azure dla [komputerów Mac, Linux](/cli/azure/install-azure-cli)i Windows (interfejs wiersza polecenia). Szczegółową specyfikację wszystkich poleceń dzienników przepływu można znaleźć [tutaj](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest)

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, należy zarejestrować dostawcę **Microsoft.Insights.** Jeśli nie masz pewności, czy **dostawca Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Polecenie włączenia dzienników przepływu jest wyświetlane w poniższym przykładzie:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Określone konto magazynu nie może mieć skonfigurowanych reguł sieci, które ograniczają dostęp sieciowy tylko do usługi firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż sieciowa sieciowa żadna z tych sieci, dla których jest włączany dziennik przepływu. Jeśli używasz różnych subskrypcji, obie muszą być skojarzone z tą samą Azure Active Directory dzierżawą. Konto, które jest potrzebne dla każdej subskrypcji, musi mieć [niezbędne uprawnienia.](required-rbac-permissions.md) 

Jeśli konto magazynu znajduje się w innej grupie zasobów lub subskrypcji niż sieciowa grupa zabezpieczeń, określ pełny identyfikator konta magazynu, a nie jego nazwę. Jeśli na przykład konto magazynu znajduje się w grupie zasobów o nazwie *RG-Storage,* zamiast określania wartości *storageAccountName* w poprzednim poleceniu należy określić wartość */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName.*

## <a name="disable-network-security-group-flow-logs"></a>Wyłączanie dzienników przepływu sieciowej grupy zabezpieczeń

Użyj poniższego przykładu, aby wyłączyć dzienniki przepływów:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja magazynu dziennika przepływu jest definiowana podczas tworzenia. Wygodnym narzędziem do uzyskiwania dostępu do tych dzienników przepływu zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej organizacji zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej open source sieciowej](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

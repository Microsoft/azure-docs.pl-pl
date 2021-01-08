---
title: Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowych grup zabezpieczeń w usłudze Azure Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 46d12db413fdf01995bc84ae018065e877afb15e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017820"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowych grup zabezpieczeń są funkcją Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń. Te dzienniki przepływów są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do, 5-informacje o spójnej kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało prawidłowo, dostawca **Microsoft. Insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **Microsoft. Insights** został zarejestrowany, uruchom następujący skrypt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowych grup zabezpieczeń

W poniższym przykładzie przedstawiono polecenie umożliwiające włączenie dzienników przepływu:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Określone konto magazynu nie może mieć skonfigurowanych reguł sieciowych, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż sieciowej grupy zabezpieczeń, dla którego włączono dziennik przepływów. Jeśli używasz różnych subskrypcji, muszą one być skojarzone z tą samą dzierżawą Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [wymagane uprawnienia](required-rbac-permissions.md). 

Jeśli konto magazynu znajduje się w innej grupie zasobów lub subskrypcji niż grupa zabezpieczeń sieci, podaj pełny identyfikator konta magazynu, a nie jego nazwę. Jeśli na przykład konto magazynu znajduje się w grupie zasobów o nazwie *RG-Storage*, a nie określono *storageAccountName* w poprzednim poleceniu, należy określić */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/Providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Wyłącz dzienniki przepływu sieciowych grup zabezpieczeń

Aby wyłączyć dzienniki przepływów, użyj następującego przykładu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest definiowana podczas tworzenia. Wygodnym narzędziem do uzyskiwania dostępu do tych dzienników przepływów zapisanych na koncie magazynu jest Eksplorator usługi Microsoft Azure Storage, które można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

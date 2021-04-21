---
title: Tworzenie środowiska Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko w usłudze Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e2846b7ba07ec0a7678a8287fe6a84bc169497a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785131"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Tworzenie środowiska usługi Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure

Ten dokument zawiera informacje na temat tworzenia nowego środowiska Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Storage dla zimnego magazynu [środowiska.](./concepts-storage.md#cold-store) To konto jest przeznaczone do długoterminowego przechowywania i analizy danych historycznych.

> [!NOTE]
> W kodzie zastąp nazwą `mytsicoldstore` unikatową konta magazynu zimnego.

Najpierw utwórz konto magazynu:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Tworzenie środowiska

Po utworzeniu konta magazynu oraz przypisaniu jego nazwy i klucza zarządzania do zmiennych uruchom poniższe polecenie, aby utworzyć Azure Time Series Insights Environment:

> [!NOTE]
> W kodzie zastąp następujące elementy unikatowymi nazwami dla swojego scenariusza:
>
> * `my-tsi-env` z nazwą środowiska.
> * `my-ts-id-prop` na nazwę właściwości identyfikatora szeregu czasowego.

> [!IMPORTANT]
> Identyfikator szeregu czasowego środowiska jest jak klucz partycji bazy danych. Identyfikator szeregu czasowego działa również jako klucz podstawowy dla modelu szeregów czasowych.
>
> Aby uzyskać więcej informacji, [zobacz Najlepsze rozwiązania dotyczące wybierania identyfikatora szeregu czasowego.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Usuwanie środowiska Azure Time Series Insights danych

Za pomocą interfejsu wiersza polecenia platformy Azure można usunąć pojedynczy zasób, na przykład środowisko Time Series Insights, lub usunąć grupę zasobów i wszystkie jej zasoby, w tym wszystkie Time Series Insights środowiska.

Aby [usunąć Time Series Insights środowiska,](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete)uruchom następujące polecenie:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Aby [usunąć konto magazynu,](/cli/azure/storage/account#az_storage_account_delete)uruchom następujące polecenie:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Aby [usunąć grupę zasobów](/cli/azure/group#az_group_delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o źródłach zdarzeń pozyskiwania danych](./concepts-streaming-ingestion-event-sources.md) za Azure Time Series Insights Gen2.
* Dowiedz się, jak nawiązać połączenie [z IoT Hub](./how-to-ingest-data-iot-hub.md)

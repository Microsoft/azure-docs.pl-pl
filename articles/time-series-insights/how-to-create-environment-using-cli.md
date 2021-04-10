---
title: Tworzenie środowiska Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko w Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 150bf38ebb248f15bf1ed82186c16b6b3f7ac40b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728746"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Tworzenie środowiska Azure Time Series Insights Gen2 przy użyciu interfejsu wiersza polecenia platformy Azure

Ten dokument przeprowadzi Cię przez proces tworzenia nowego środowiska Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Storage dla [chłodnego magazynu](./concepts-storage.md#cold-store)środowiska. To konto jest przeznaczone do długoterminowego przechowywania i analizy danych historycznych.

> [!NOTE]
> W kodzie Zastąp `mytsicoldstore` wartość unikatową nazwą konta magazynu zimnego.

Najpierw utwórz konto magazynu:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Tworzenie środowiska

Teraz, gdy konto magazynu zostało utworzone, a jego nazwa i klucz zarządzania są przypisane do zmiennych, uruchom poniższe polecenie, aby utworzyć środowisko Azure Time Series Insights:

> [!NOTE]
> W kodzie Zastąp następujące nazwy unikatowymi nazwami danego scenariusza:
>
> * `my-tsi-env` przy użyciu nazwy środowiska.
> * `my-ts-id-prop` Nazwa właściwości identyfikatora szeregów czasowych.

> [!IMPORTANT]
> Identyfikator szeregów czasowych Twojego środowiska przypomina klucz partycji bazy danych. Identyfikator szeregów czasowych działa również jako klucz podstawowy dla modelu szeregów czasowych.
>
> Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Usuń środowisko Azure Time Series Insights

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby usunąć pojedynczy zasób, taki jak środowisko Time Series Insights, lub usunąć grupę zasobów i wszystkie jej zasoby, w tym wszystkie środowiska Time Series Insights.

Aby [usunąć środowiska Time Series Insights](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete), uruchom następujące polecenie:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Aby [usunąć konto magazynu](/cli/azure/storage/account#az_storage_account_delete), uruchom następujące polecenie:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Aby [usunąć grupę zasobów](/cli/azure/group#az-group-delete) i wszystkie jej zasoby, uruchom następujące polecenie:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [źródeł zdarzeń pozyskiwania strumieniowego](./concepts-streaming-ingestion-event-sources.md) dla środowiska Azure Time Series Insights Gen2.
* Dowiedz się, jak nawiązać połączenie z [IoT Hub](./how-to-ingest-data-iot-hub.md)

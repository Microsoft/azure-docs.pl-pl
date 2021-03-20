---
title: Rozwiązywanie problemów z usługą Azure VNET Gateway i połączeniami — interfejs wiersza polecenia
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak używać usługi Azure Network Watcher Rozwiązywanie problemów z interfejsem wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d04ba6cc5f8527f8c05fbeb323c65230447e1e2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019707"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Rozwiązywanie problemów z bramą Virtual Network i połączeniami przy użyciu interfejsu wiersza polecenia Azure Network Watcher Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher udostępnia wiele funkcji, które odnoszą się do poznania zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. Rozwiązywanie problemów z zasobami można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub API REST. Gdy jest wywoływana, Network Watcher sprawdza kondycję Virtual Network bramy lub połączenia i zwraca swoje wyniki.

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

Aby zapoznać się z listą obsługiwanych typów bram, należy odwiedzić [obsługiwane typy bram](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z zasobami zapewnia możliwość rozwiązywania problemów związanych z Virtual Network bramami i połączeniami. Gdy żądanie dotyczy rozwiązywania problemów z zasobami, dzienniki są badane i sprawdzane. Po zakończeniu inspekcji są zwracane wyniki. Żądania rozwiązywania problemów z zasobami są długotrwałymi żądaniami, co może potrwać kilka minut. Dzienniki rozwiązywania problemów są przechowywane w kontenerze na koncie magazynu, które jest określone.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy Virtual Network

W tym przykładzie trwa Rozwiązywanie problemów z zasobami w ramach połączenia. Możesz również przekazać ją do bramy Virtual Network. Poniższe polecenie cmdlet wyświetla listę połączeń sieci VPN w grupie zasobów.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Po otrzymaniu nazwy połączenia możesz uruchomić to polecenie, aby uzyskać identyfikator zasobu:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobami zwraca dane dotyczące kondycji zasobu, a także zapisuje dzienniki na koncie magazynu w celu przejrzenia. W tym kroku utworzymy konto magazynu, jeśli istnieje już istniejące konto magazynu.

1. Tworzenie konta magazynu

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Pobierz klucze konta magazynu

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Tworzenie kontenera

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchom Network Watcher Rozwiązywanie problemów z zasobami

Rozwiązywanie problemów z zasobami przy użyciu `az network watcher troubleshooting` polecenia cmdlet. Przekazujemy polecenie cmdlet do grupy zasobów, nazwy Network Watcher, identyfikatora połączenia, identyfikatora konta magazynu oraz ścieżki do obiektu BLOB, w którym mają być przechowywane wyniki rozwiązywania problemów.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Po uruchomieniu polecenia cmdlet Network Watcher przegląda zasób w celu zweryfikowania kondycji. Zwraca wyniki do powłoki i zapisuje dzienniki wyników na określonym koncie magazynu.

## <a name="understanding-the-results"></a>Zrozumienie wyników

Tekst akcji zawiera ogólne wskazówki dotyczące sposobu rozwiązania problemu. Jeśli można wykonać akcję dotyczącą problemu, zostanie podane łącze z dodatkowymi wskazówkami. W przypadku braku dodatkowych wskazówek odpowiedź zawiera adres URL służący do otwierania zgłoszenia do pomocy technicznej.  Aby uzyskać więcej informacji o właściwościach odpowiedzi i uwzględnionych na niej tematach, odwiedź stronę [Network Watcher Rozwiązywanie problemów](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Inne narzędzie, które może być używane, jest Eksplorator usługi Storage. Więcej informacji na temat Eksplorator usługi Storage można znaleźć tutaj przy użyciu następującego linku: [Eksplorator usługi Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Następne kroki

Jeśli zmieniono ustawienia, które zatrzymują łączność z siecią VPN, zobacz [Manage Network Security Groups](../virtual-network/manage-network-security-group.md) to Track The Network Security Groups and Security rules.
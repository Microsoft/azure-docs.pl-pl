---
title: Rozwiązywanie problemów z usługą Azure VNet Gateway i połączeniami — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak korzystać z polecenia cmdlet programu PowerShell dla usługi Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 1461f7f9811145553da6c200111d4db1f013060e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84738689"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Rozwiązywanie problemów z bramą Virtual Network i połączeniami przy użyciu programu Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher udostępnia wiele funkcji, które odnoszą się do poznania zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. Rozwiązywanie problemów z zasobami można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub API REST. Gdy jest wywoływana, Network Watcher sprawdza kondycję Virtual Network bramy lub połączenia i zwraca swoje wyniki.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

Aby zapoznać się z listą obsługiwanych typów bram, należy odwiedzić [obsługiwane typy bram](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z zasobami zapewnia możliwość rozwiązywania problemów związanych z Virtual Network bramami i połączeniami. Gdy żądanie dotyczy rozwiązywania problemów z zasobami, dzienniki są badane i sprawdzane. Po zakończeniu inspekcji są zwracane wyniki. Żądania rozwiązywania problemów z zasobami są długotrwałymi żądaniami, co może potrwać kilka minut. Dzienniki rozwiązywania problemów są przechowywane w kontenerze na koncie magazynu, które jest określone.

## <a name="retrieve-network-watcher"></a>Pobierz Network Watcher

Pierwszym krokiem jest pobranie wystąpienia Network Watcher. `$networkWatcher`Zmienna jest przenoszona do `Start-AzNetworkWatcherResourceTroubleshooting` polecenia cmdlet w kroku 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy Virtual Network

W tym przykładzie trwa Rozwiązywanie problemów z zasobami w ramach połączenia. Możesz również przekazać ją do bramy Virtual Network.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobami zwraca dane dotyczące kondycji zasobu, a także zapisuje dzienniki na koncie magazynu w celu przejrzenia. W tym kroku utworzymy konto magazynu, jeśli istnieje już istniejące konto magazynu.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchom Network Watcher Rozwiązywanie problemów z zasobami

Rozwiązywanie problemów z zasobami przy użyciu `Start-AzNetworkWatcherResourceTroubleshooting` polecenia cmdlet. Przekazujemy polecenie cmdlet Network Watcher obiektu, identyfikatorem połączenia lub bramą Virtual Network, identyfikatorem konta magazynu oraz ścieżką do przechowywania wyników.

> [!NOTE]
> `Start-AzNetworkWatcherResourceTroubleshooting`Polecenie cmdlet jest długotrwałe i może potrwać kilka minut.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Po uruchomieniu polecenia cmdlet Network Watcher przegląda zasób w celu zweryfikowania kondycji. Zwraca wyniki do powłoki i zapisuje dzienniki wyników na określonym koncie magazynu.

## <a name="understanding-the-results"></a>Zrozumienie wyników

Tekst akcji zawiera ogólne wskazówki dotyczące sposobu rozwiązania problemu. Jeśli można wykonać akcję dotyczącą problemu, zostanie podane łącze z dodatkowymi wskazówkami. W przypadku braku dodatkowych wskazówek odpowiedź zawiera adres URL służący do otwierania zgłoszenia do pomocy technicznej.  Aby uzyskać więcej informacji o właściwościach odpowiedzi i uwzględnionych na niej tematach, odwiedź stronę [Network Watcher Rozwiązywanie problemów](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Inne narzędzie, które może być używane, jest Eksplorator usługi Storage. Więcej informacji na temat Eksplorator usługi Storage można znaleźć tutaj przy użyciu następującego linku: [Eksplorator usługi Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Następne kroki

Jeśli zmieniono ustawienia, które zatrzymują łączność z siecią VPN, zobacz [Manage Network Security Groups](../virtual-network/manage-network-security-group.md) to Track The Network Security Groups and Security rules.

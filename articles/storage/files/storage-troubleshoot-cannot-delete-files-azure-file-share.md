---
title: Udział plików platformy Azure — nie można usunąć plików z udziału plików platformy Azure
description: Identyfikowanie i rozwiązywanie problemów z niepowodzeniem usuwania plików z udziału plików platformy Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629686"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Udział plików platformy Azure — nie można usunąć plików z udziału plików platformy Azure

Niepowodzenie usuwania plików z udziału plików platformy Azure może mieć kilka objawów:

**Objaw 1:**

Nie można usunąć pliku w udziale plików platformy Azure z powodu jednego z dwóch następujących problemów:

* Plik oznaczony do usunięcia
* Określony zasób może być używany przez klienta SMB

**Objaw 2:**

Za mało dostępnego limitu przydziału, aby przetworzyć to polecenie

## <a name="cause"></a>Przyczyna

Błąd 1816 występuje, gdy zostanie osiągnięty górny limit równoczesnych otwartych dojść do pliku, na komputerze, na którym jest instalowany udział plików. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wydajności i skalowalności usługi Azure Storage](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Rozwiązanie

Zmniejsz liczbę współbieżnych dojść otwartych, zamykając niektóre uchwyty.

## <a name="prerequisite"></a>Wymaganie wstępne

### <a name="install-the-latest-azure-powershell-module"></a>Zainstaluj najnowszy moduł Azure PowerShell

* [Instalacja modułu Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Połącz z platformą Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Wybierz subskrypcję docelowego konta magazynu:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Utwórz kontekst dla docelowego konta magazynu:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Pobierz bieżące otwarte dojścia udziału plików:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Przykładowy wynik:

|HandleId|Ścieżka|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nowy folder/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Zamknij otwarte dojście:

Aby zamknąć otwarte dojście, użyj następującego polecenia:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
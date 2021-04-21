---
title: Udział plików platformy Azure — nie można usunąć plików z udziału plików platformy Azure
description: Zidentyfikuj i rozwiąż problemy z niepowodzeniem usuwania plików z udziału plików platformy Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: d8cc0cb7df4bb7bfff5a6b9d2f159cb674532927
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789757"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Udział plików platformy Azure — nie można usunąć plików z udziału plików platformy Azure

Niepowodzenie usunięcia plików z udziału plików platformy Azure może mieć kilka objawów:

**Objaw 1:**

Nie można usunąć pliku w udziałach plików platformy Azure z powodu jednego z dwóch poniższych problemów:

* Plik oznaczony do usunięcia
* Określony zasób może być w użyciu przez klienta SMB

**Objaw 2:**

Za mało dostępnego limitu przydziału do przetwarzania tego polecenia

## <a name="cause"></a>Przyczyna

Błąd 1816 występuje, gdy osiągniesz górny limit współbieżnych otwartych dojść dozwolonych dla pliku na komputerze, na którym jest zainstalowany udział plików. Aby uzyskać więcej informacji, zobacz [listę kontrolną wydajności i skalowalności usługi Azure Storage.](../blobs/storage-performance-checklist.md)

## <a name="resolution"></a>Rozwiązanie

Zmniejsz liczbę współbieżnych otwartych dojść, zamykając niektóre dojścia.

## <a name="prerequisite"></a>Wymaganie wstępne

### <a name="install-the-latest-azure-powershell-module"></a>Instalowanie najnowszego Azure PowerShell modułu

* [Instalacja modułu Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z platformą Azure:

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

|HandleId|Ścieżka|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|Parentid|SessionId|
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
* [Rozwiązywanie problemów z usługą Azure File Sync](../file-sync/file-sync-troubleshoot.md)
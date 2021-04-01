---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b26d4af29a92fb0f14c52e76a7eae1d0073a3aa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96005336"
---
Ten błąd może wystąpić, gdy usługa Azure File Sync jest niedostępna z poziomu serwera. Ten błąd można rozwiązać, wykonując następujące czynności:

1. Sprawdź, czy usługa systemu Windows `FileSyncSvc.exe` nie jest blokowana przez zaporę.
2. Sprawdź, czy port 443 jest otwarty dla połączeń wychodzących z usługą Azure File Sync. Można to zrobić za pomocą `Test-NetConnection` polecenia cmdlet. Adres URL symbolu zastępczego `<azure-file-sync-endpoint>` poniżej można znaleźć w dokumencie [Ustawienia zapory i serwera proxy usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Upewnij się, że konfiguracja serwera proxy jest ustawiona zgodnie z oczekiwaniami. Można to zrobić za pomocą polecenia cmdlet `Get-StorageSyncProxyConfiguration`. Więcej informacji na temat konfigurowania serwera proxy dla usługi Azure File Sync można znaleźć w dokumencie [Ustawienia serwera proxy i zapory usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Użyj polecenia cmdlet Test-StorageSyncNetworkConnectivity, aby sprawdzić łączność sieciową z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [test łączności sieciowej z punktami końcowymi usługi](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. Skontaktuj się z administratorem sieci, aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów z siecią.
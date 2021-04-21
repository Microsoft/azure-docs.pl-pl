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
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774584"
---
Ten błąd może wystąpić, gdy usługa Azure File Sync jest niedostępna z poziomu serwera. Ten błąd można rozwiązać, wykonując następujące czynności:

1. Sprawdź, czy usługa systemu Windows `FileSyncSvc.exe` nie jest blokowana przez zaporę.
2. Sprawdź, czy port 443 jest otwarty dla połączeń wychodzących Azure File Sync usługi. Można to zrobić za pomocą `Test-NetConnection` polecenia cmdlet . Adres URL symbolu zastępczego `<azure-file-sync-endpoint>` poniżej można znaleźć w dokumencie [Ustawienia zapory i serwera proxy usługi Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Upewnij się, że konfiguracja serwera proxy jest ustawiona zgodnie z oczekiwaniami. Można to zrobić za pomocą polecenia cmdlet `Get-StorageSyncProxyConfiguration`. Więcej informacji na temat konfigurowania serwera proxy dla usługi Azure File Sync można znaleźć w dokumencie [Ustawienia serwera proxy i zapory usługi Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Użyj Test-StorageSyncNetworkConnectivity cmdlet , aby sprawdzić łączność sieciową z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [Testowanie łączności sieciowej z punktami końcowymi usługi](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. Skontaktuj się z administratorem sieci, aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z łącznością sieciową.
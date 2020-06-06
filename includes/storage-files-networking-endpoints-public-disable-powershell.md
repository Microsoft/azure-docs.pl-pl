---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465116"
---
Następujące polecenie programu PowerShell będzie odrzucać cały ruch do publicznego punktu końcowego konta magazynu. Należy pamiętać, że to polecenie ma `-Bypass` parametr ustawiony na `AzureServices` . Pozwoli to na dostęp do konta magazynu przez publiczny punkt końcowy, takich jak Azure File Sync.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
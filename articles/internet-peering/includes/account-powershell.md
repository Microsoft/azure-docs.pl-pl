---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678484"
---
Przed rozpoczęciem konfiguracji należy zainstalować i zaimportować wymagane moduły. Aby zainstalować moduły w programie PowerShell, są zainstalowane uprawnienia administratora.

1. Zainstaluj i zaimportuj moduł Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Zainstaluj i zaimportuj moduł Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Sprawdź, czy moduły zostały zaimportowane poprawnie za pomocą tego polecenia:
    ```powershell
    Get-Module
    ```
1. Zaloguj się do konta platformy Azure za pomocą tego polecenia:
    ```powershell
    Connect-AzAccount
    ```
1. Sprawdź subskrypcje dla konta i wybierz subskrypcję, w której chcesz utworzyć komunikację równorzędną.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Jeśli nie masz jeszcze grupy zasobów, należy utworzyć jej przed utworzeniem komunikacji równorzędnej. Można to zrobić, uruchamiając następujące polecenie:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Jeśli nie masz jeszcze skojarzonego asn i subskrypcji, wykonaj kroki opisane w [skojarzyć peer ASN](../howto-subscription-association-powershell.md). Ta akcja jest wymagana do żądania komunikacji równorzędnej.

> [!NOTE]
> Lokalizacja grupy zasobów jest niezależna od lokalizacji, w której można skonfigurować komunikację równorzędną.
&nbsp;

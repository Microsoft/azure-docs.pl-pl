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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678484"
---
Przed rozpoczęciem konfiguracji zainstaluj i zaimportuj wymagane moduły. Do zainstalowania modułów w programie PowerShell wymagane są uprawnienia administratora.

1. Zainstaluj i zaimportuj AZ module.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Zainstaluj i zaimportuj moduł AZ. peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Sprawdź, czy moduły zostały prawidłowo zaimportowane przy użyciu tego polecenia:
    ```powershell
    Get-Module
    ```
1. Zaloguj się do konta platformy Azure za pomocą tego polecenia:
    ```powershell
    Connect-AzAccount
    ```
1. Sprawdź subskrypcje konta i wybierz subskrypcję, w której chcesz utworzyć komunikację równorzędną.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed utworzeniem komunikacji równorzędnej. Możesz to zrobić, uruchamiając następujące polecenie:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Jeśli nie skojarzono jeszcze numeru ASN i subskrypcji, wykonaj kroki opisane w sekcji [kojarzenie równorzędnego numeru ASN](../howto-subscription-association-powershell.md). Ta akcja jest wymagana do żądania komunikacji równorzędnej.

> [!NOTE]
> Lokalizacja grupy zasobów jest niezależna od lokalizacji, w której użytkownik zdecyduje się skonfigurować komunikację równorzędną.
&nbsp;

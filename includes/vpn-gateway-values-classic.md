---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103241"
---
Po utworzeniu klasycznego sieci wirtualnych w Azure Portal nazwa wyświetlana nie jest pełną nazwą używaną dla programu PowerShell. Na przykład Sieć wirtualna o nazwie **sieci testvnet1** w portalu może mieć znacznie dłuższą nazwę w pliku konfiguracyjnym sieci. Nazwa sieci wirtualnej w grupie zasobów "ClassicRG" może wyglądać następująco: **Group ClassicRG sieci testvnet1**. Podczas tworzenia połączeń ważne jest, aby użyć wartości widocznych w pliku konfiguracji sieci.

W poniższych krokach nawiążesz połączenie z kontem platformy Azure i pobierzesz plik konfiguracji sieci i wyświetli go w celu uzyskania wartości wymaganych dla połączeń.

1. Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell dla usługi Azure Service Management (SM). Większość osób ma zainstalowane lokalnie moduły Menedżer zasobów, ale nie mają modułów zarządzania usługami. Moduły zarządzania usługami są starsze i muszą zostać zainstalowane osobno. Aby uzyskać więcej informacji, zobacz [Instalowanie poleceń cmdlet usługi Service Management](/powershell/azure/servicemanagement/install-azure-ps).

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Poniższe przykłady ułatwiają nawiązanie połączenia. Te polecenia należy uruchomić lokalnie przy użyciu modułu zarządzania usługą programu PowerShell. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```
1. Sprawdź subskrypcje dostępne na koncie.

   ```powershell
   Get-AzureSubscription
   ```
1. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Utwórz katalog na komputerze. Na przykład C:\AzureVNet
1. Wyeksportuj plik konfiguracji sieci do katalogu. W tym przykładzie plik konfiguracji sieci jest eksportowany do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Otwórz plik za pomocą edytora tekstów i Wyświetl nazwy sieci wirtualnych i witryn. Nazwy te będą nazwami używanymi podczas tworzenia połączeń.<br>Nazwy sieci **wirtualnej** są wyświetlane jako **VirtualNetworkSite nazwa =**<br>Nazwy **lokacji** są wyświetlane jako **Nazwa LocalNetworkSiteRef =**
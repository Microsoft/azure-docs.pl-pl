---
title: dołączanie pliku programu PowerShell dla usługi Azure DNS
description: dołączanie pliku programu PowerShell dla usługi Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67133705"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Konfigurowanie programu Azure PowerShell dla usługi Azure DNS

### <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

Ponadto aby korzystać z usługi Private Zones (publiczna wersja zapoznawcza), należy upewnić się, że zainstalowano poniższe moduły programu PowerShell w określonych wersjach. 
* AzureRM.Dns — [wersja 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) lub nowsza
* AzureRM.Network — [wersja 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) lub nowsza

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
Dane wyjściowe powyższych poleceń muszą wskazywać, że wersja AzureRM. DNS ma 4.1.0 lub nowszą wersję, a dla AzureRM. Network jest 5.4.0 lub nowsza.  

Jeśli w systemie są wcześniejsze wersje, możesz zainstalować najnowszą wersję programu Azure PowerShell lub pobrać i zainstalować powyższe moduły z galerii programu PowerShell, korzystając z linków obok wersji modułu. Następnie możesz zainstalować je przy użyciu poniższych poleceń. Oba moduły są wymagane i są w pełni zgodne z poprzednimi wersjami. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu usługi AzureRM](/powershell/azure/azurerm/authenticate-azureps).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Wybierz subskrypcję
 
Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzureRmSubscription
```

Wybierz subskrypcję platformy Azure do użycia.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Ta lokalizacja będzie używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Ponieważ jednak wszystkie zasoby DNS są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure DNS.

Ten krok można pominąć, jeśli używasz istniejącej grupy zasobów.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Usługa Azure DNS jest zarządzana przez dostawcę zasobów Microsoft.Network. Aby można było korzystać z usługi Azure DNS, Twoja subskrypcja platformy Azure musi być zarejestrowana do używania tego dostawcy zasobów. Jest to jednorazowa operacja dla każdej subskrypcji.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

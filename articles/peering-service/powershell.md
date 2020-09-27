---
title: 'Rejestrowanie połączenia z usługą komunikacji równorzędnej — Azure PowerShell '
description: W tym samouczku dowiesz się, jak zarejestrować połączenie z usługą komunikacji równorzędnej za pomocą programu PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400438"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Samouczek: rejestrowanie połączenia usługi komunikacji równorzędnej za pomocą Azure PowerShell

W tym samouczku dowiesz się, jak zarejestrować usługę komunikacji równorzędnej przy użyciu Azure PowerShell.

Usługa komunikacji równorzędnej Azure to usługa sieciowa, która zwiększa łączność klienta z usługami w chmurze firmy Microsoft, takimi jak Microsoft 365, Dynamics 365, oprogramowanie jako usługa (SaaS), Azure lub wszelkie usługi firmy Microsoft dostępne za pośrednictwem publicznej sieci Internet. W tym artykule dowiesz się, jak zarejestrować połączenie z usługą komunikacji równorzędnej za pomocą Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, Utwórz [konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) teraz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zamiast tego postanowisz zainstalować program PowerShell i używać go lokalnie, ten przewodnik Szybki start wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Informacje dotyczące instalacji i uaktualniania znajdują się w temacie [Install Azure PowerShell module](/powershell/azure/install-az-ps).

Ponadto, jeśli używasz programu PowerShell lokalnie, musisz uruchomić polecenie `Connect-AzAccount`. To polecenie tworzy połączenie z platformą Azure.

Użyj modułu Azure PowerShell, aby zarejestrować usługę komunikacji równorzędnej i zarządzać nią. Usługę komunikacji równorzędnej można zarejestrować lub zarządzać z poziomu wiersza polecenia programu PowerShell lub skryptów.


## <a name="prerequisites"></a>Wymagania wstępne  
Wymagane są następujące elementy:

### <a name="azure-account"></a>Konto platformy Azure

Musisz mieć prawidłowe i aktywne konto Microsoft Azure. To konto jest wymagane do skonfigurowania połączenia z usługą komunikacji równorzędnej. Usługa komunikacji równorzędnej jest zasobem w ramach subskrypcji platformy Azure.

### <a name="connectivity-provider"></a>Dostawca połączenia

Aby uzyskać usługę komunikacji równorzędnej w celu połączenia sieci z siecią firmy Microsoft, możesz współpracować z usługodawcą internetowym lub z internetowym partnerem programu Exchange.

Upewnij się, że dostawcy łączności są partnerem z firmą Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Zarejestruj subskrypcję przy użyciu dostawcy zasobów i flagi funkcji

Przed przejściem do procedury rejestrowania usługi Komunikacja równorzędna Zarejestruj swoją subskrypcję za pomocą flagi dostawca zasobów i funkcji przy użyciu Azure PowerShell. Polecenia Azure PowerShell są określone tutaj:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Pobierz lokalizację i dostawcę usług 

Uruchom następujące polecenia w Azure PowerShell, aby uzyskać dostawcy lokalizacji i usług, do których powinna być włączona usługa komunikacji równorzędnej. 

Pobierz lokalizacje usługi komunikacji równorzędnej:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Pobierz dostawców usług komunikacji równorzędnej:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Rejestrowanie połączenia usługi Komunikacja równorzędna

Zarejestruj połączenie usługi komunikacji równorzędnej za pomocą następującego zestawu poleceń za pośrednictwem Azure PowerShell. Ten przykład rejestruje usługę komunikacji równorzędnej o nazwie myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Zarejestruj prefiks usługi komunikacji równorzędnej

Zarejestruj prefiks dostarczony przez dostawcę połączenia, wykonując następujące polecenia za pośrednictwem Azure PowerShell. Ten przykład rejestruje prefiks o nazwie mój prefiks.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Wyświetl listę wszystkich połączeń usługi Komunikacja równorzędna

Aby wyświetlić listę wszystkich usług komunikacji równorzędnej, uruchom następujące polecenie:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Wyświetl listę wszystkich prefiksów usługi komunikacji równorzędnej

Aby wyświetlić listę wszystkich prefiksów usługi komunikacji równorzędnej, uruchom następujące polecenie:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Usuń prefiks usługi komunikacji równorzędnej

Aby usunąć prefiks usługi komunikacji równorzędnej, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).
- Aby zarejestrować połączenie usługi komunikacji równorzędnej przy użyciu Azure Portal, zobacz temat [Rejestrowanie połączenia usługi Komunikacja równorzędna — Azure Portal](azure-portal.md).
- Aby zarejestrować połączenie usługi komunikacji równorzędnej za pomocą interfejsu wiersza polecenia platformy Azure, zobacz temat [Rejestrowanie połączenia z usługą komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure](cli.md).

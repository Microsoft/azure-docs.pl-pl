---
title: Zarządzanie Azure CDN przy użyciu programu PowerShell | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak za pomocą programu PowerShell zarządzać aspektami profilów i punktów końcowych usługi Azure Content Delivery Network.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/20/2019
ms.author: allensu
ms.openlocfilehash: b5fa1cedf4cc5e87d11dc4b5c453d9cb2a307c3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192654"
---
# <a name="manage-azure-cdn-with-powershell"></a>Zarządzanie Azure CDN przy użyciu programu PowerShell
Program PowerShell udostępnia jedną z najbardziej elastycznych metod zarządzania profilami Azure CDN i punktami końcowymi.  Programu PowerShell można używać interaktywnie lub pisząc skrypty do automatyzowania zadań zarządzania.  W tym samouczku przedstawiono kilka typowych zadań, które można wykonać za pomocą programu PowerShell, aby zarządzać profilami Azure CDN i punktami końcowymi.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby zarządzać profilami i punktami końcowymi Azure CDN za pomocą programu PowerShell, musisz mieć zainstalowany moduł Azure PowerShell.  Aby dowiedzieć się, jak zainstalować Azure PowerShell i nawiązać połączenie z platformą Azure za pomocą `Connect-AzAccount` polecenia cmdlet, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Musisz się zalogować, `Connect-AzAccount` Aby móc wykonywać Azure PowerShell polecenia cmdlet.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Wyświetlanie listy poleceń cmdlet Azure CDN
Wszystkie Azure CDN polecenia cmdlet można wyświetlić przy użyciu `Get-Command` polecenia cmdlet.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Confirm-AzCdnEndpointProbeURL                      1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomain                          1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomainHttps                     1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomainHttps                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEdgeNode                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointResourceUsage                     1.4.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileResourceUsage                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                             1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSupportedOptimizationType          1.4.0      Az.Cdn
Cmdlet          Get-AzCdnSubscriptionResourceUsage                 1.4.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryPolicy                            1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRule                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleAction                        1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleCondition                     1.4.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          New-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                       1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                               1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                                1.4.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                                1.4.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                                 1.4.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                             1.4.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                     1.4.0      Az.Cdn
```

## <a name="getting-help"></a>Uzyskiwanie pomocy
Możesz uzyskać pomoc dotyczącą dowolnego z tych poleceń cmdlet przy użyciu `Get-Help` polecenia cmdlet.  `Get-Help` zapewnia użycie i składnię, a opcjonalnie pokazuje przykłady.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Wyświetlanie listy istniejących profilów Azure CDN
`Get-AzCdnProfile`Polecenie cmdlet bez żadnych parametrów pobiera wszystkie istniejące profile sieci CDN.

```powershell
Get-AzCdnProfile
```

Te dane wyjściowe mogą być przekazywane do poleceń cmdlet dla wyliczenia.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Możesz również zwrócić pojedynczy profil, określając nazwę profilu i grupę zasobów.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Możliwe jest posiadanie wielu profilów usługi CDN o tej samej nazwie, o ile znajdują się one w różnych grupach zasobów.  Pominięcie `ResourceGroupName` parametru zwraca wszystkie profile o zgodnej nazwie.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Wyświetlanie listy istniejących punktów końcowych usługi CDN
`Get-AzCdnEndpoint` może pobrać pojedynczy punkt końcowy lub wszystkie punkty końcowe w profilu.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Tworzenie profilów i punktów końcowych usługi CDN
`New-AzCdnProfile` i `New-AzCdnEndpoint` są używane do tworzenia profilów i punktów końcowych usługi CDN. Obsługiwane są następujące jednostki SKU:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Sprawdzanie dostępności nazwy punktu końcowego
`Get-AzCdnEndpointNameAvailability` zwraca obiekt wskazujący, czy nazwa punktu końcowego jest dostępna.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Dodawanie domeny niestandardowej
`New-AzCdnCustomDomain` dodaje niestandardową nazwę domeny do istniejącego punktu końcowego.

> [!IMPORTANT]
> Należy skonfigurować rekord CNAME za pomocą dostawcy DNS zgodnie z opisem w temacie [Jak mapować domenę niestandardową na Content Delivery Network (CDN)](cdn-map-content-to-custom-domain.md).  Możesz przetestować mapowanie Przed zmodyfikowaniem punktu końcowego przy użyciu `Test-AzCdnCustomDomain` .
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modyfikowanie punktu końcowego
`Set-AzCdnEndpoint` modyfikuje istniejący punkt końcowy.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Przeczyszczanie/ładowanie zasobów usługi CDN
`Unpublish-AzCdnEndpointContent` Przeczyszcza buforowane elementy zawartości podczas `Publish-AzCdnEndpointContent` wstępnego ładowania zasobów dla obsługiwanych punktów końcowych.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Uruchamianie/zatrzymywanie punktów końcowych usługi CDN
`Start-AzCdnEndpoint` i `Stop-AzCdnEndpoint` może służyć do uruchamiania i zatrzymywania poszczególnych punktów końcowych lub grup punktów końcowych.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="creating-standard-rules-engine-policy-and-applying-to-an-existing-cdn-endpoint"></a>Tworzenie zasad standardowego aparatu reguł i stosowanie ich do istniejącego punktu końcowego usługi CDN
`New-AzCdnDeliveryRule`, `New=AzCdnDeliveryRuleCondition` i `New-AzCdnDeliveryRuleAction` mogą być używane do konfigurowania aparatu reguł Azure CDN Standard na Azure CDN z profilów firmy Microsoft. 

```powershell
# Create a new http to https redirect rule
$Condition=New-AzCdnDeliveryRuleCondition -MatchVariable RequestProtocol -Operator Equal -MatchValue HTTP
$Action=New-AzCdnDeliveryRuleAction -RedirectType Found -DestinationProtocol HTTPS
$HttpToHttpsRedirectRule=New-AzCdnDeliveryRule -Name "HttpToHttpsRedirectRule" -Order 2 -Condition $Condition -Action $Action

# Create a path based Response header modification rule. 
$Cond1=New-AzCdnDeliveryRuleCondition -MatchVariable UrlPath -Operator BeginsWith -MatchValue "/images/"
$Action1=New-AzCdnDeliveryRuleAction -HeaderActionType ModifyResponseHeader -Action Overwrite -HeaderName "Access-Control-Allow-Origin" -Value "*"
$PathBasedCacheOverrideRule=New-AzCdnDeliveryRule -Name "PathBasedCacheOverride" -Order 1 -Condition $Cond1 -Action $action1

# Create a delivery policy with above deliveryRules.
$Policy = New-AzCdnDeliveryPolicy -Description "DeliveryPolicy" -Rule $HttpToHttpsRedirectRule,$UrlRewriteRule

# Update existing endpoint with created delivery policy
$ep = Get-AzCdnEndpoint -EndpointName cdndocdemo -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
$ep.DeliveryPolicy = $Policy
Set-AzCdnEndpoint -CdnEndpoint $ep
```

## <a name="deleting-cdn-resources"></a>Usuwanie zasobów usługi CDN
`Remove-AzCdnProfile` i `Remove-AzCdnEndpoint` może służyć do usuwania profilów i punktów końcowych.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zautomatyzować usługę Azure CDN przy użyciu platformy [.NET](cdn-app-dev-net.md) lub [Node.js](cdn-app-dev-node.md).

Aby dowiedzieć się więcej o funkcjach sieci CDN, zobacz temat [Omówienie usługi CDN](cdn-overview.md).


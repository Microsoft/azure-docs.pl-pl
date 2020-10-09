---
title: Przykładowy skrypt Azure PowerShell, który używa reguł niestandardowych WAF
description: Przykładowy skrypt Azure PowerShell — tworzenie zapory aplikacji sieci Web na Application Gateway reguł niestandardowych
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8d800e77ed0e609091d31d9b3177af447ac06f5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079510"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Tworzenie niestandardowych reguł WAF za pomocą Azure PowerShell

Ten skrypt tworzy Application Gateway zaporę aplikacji sieci Web, która korzysta z reguł niestandardowych. Reguła niestandardowa blokuje ruch, jeśli nagłówek żądania zawiera User-Agent *evilbot*.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-powershell-module"></a>Moduł programu Azure PowerShell

Jeśli zdecydujesz się zainstalować program Azure PowerShell i używać go lokalnie, ten skrypt wymaga modułu Azure PowerShell w wersji 2.1.0 lub nowszej.

1. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).
2. Aby utworzyć połączenie z platformą Azure, uruchom polecenie `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną przy użyciu konfiguracji podsieci. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP dla bramy aplikacji. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Tworzy konfigurację, która kojarzy podsieć z bramą aplikacji. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Tworzy konfigurację, która przypisuje publiczny adres IP do bramy aplikacji. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Przypisuje port używany do uzyskiwania dostępu do bramy aplikacji. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Tworzy pulę zaplecza dla bramy aplikacji. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfiguruje ustawienia dla puli zaplecza. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Tworzy odbiornik. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Tworzy regułę routingu. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Określa warstwę i pojemność bramy aplikacji. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Tworzy bramę aplikacji. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Tworzy konfigurację automatycznego skalowania dla Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Tworzy zmienną dopasowania dla warunku zapory.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Tworzy warunek dopasowania dla reguły niestandardowej.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Tworzy nową regułę niestandardową dla zasad zapory Application Gateway.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Tworzy zasady zapory Application Gateway.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Tworzy konfigurację WAF dla bramy aplikacji.|

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat reguł niestandardowych WAF, zobacz [reguły niestandardowe dla zapory aplikacji sieci Web](../ag/custom-waf-rules-overview.md)
- Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

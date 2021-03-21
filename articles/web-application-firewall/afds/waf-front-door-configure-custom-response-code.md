---
title: Konfigurowanie odpowiedzi niestandardowych dla zapory aplikacji sieci Web (WAF) przy użyciu usług frontonu platformy Azure
description: Dowiedz się, jak skonfigurować niestandardowy kod odpowiedzi i komunikat, gdy WAF blokuje żądanie.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563685"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Konfigurowanie niestandardowej odpowiedzi dla zapory aplikacji sieci Web platformy Azure (WAF)

Domyślnie, gdy WAF blokuje żądanie ze względu na dopasowaną regułę, zwraca kod stanu 403 z **żądaniem jest zablokowany** komunikat. Komunikat domyślny zawiera również ciąg odwołania śledzenia, który może służyć do łączenia [wpisów dziennika](./waf-front-door-monitor.md) dla żądania.  Można skonfigurować niestandardowy kod stanu odpowiedzi i komunikat niestandardowy z ciągiem odwołania dla przypadku użycia. W tym artykule opisano sposób konfigurowania niestandardowej strony odpowiedzi, gdy żądanie jest blokowane przez WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Konfigurowanie niestandardowego kodu stanu odpowiedzi i portalu korzystania z komunikatów

Można skonfigurować niestandardowy kod stanu odpowiedzi i treść w obszarze "ustawienia zasad" w portalu WAF.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Ustawienia zasad WAF":::

W powyższym przykładzie kod odpowiedzi został zachowany jako 403 i skonfigurowano krótki komunikat "Skontaktuj się z nami", jak pokazano na poniższym obrazie:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Przykład odpowiedzi niestandardowej":::

"{{Azure-ref}}" Wstawia unikatowy ciąg odwołania w treści odpowiedzi. Wartość pasuje do pola TrackingReference w `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` dziennikach i.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Konfigurowanie niestandardowego kodu stanu odpowiedzi i komunikatów przy użyciu programu PowerShell

### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Możesz zainstalować program [Azure PowerShell](/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj moduł AZ PowerShell module.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Nawiązywanie połączenia z platformą Azure przy użyciu interaktywnego okna dialogowego logowania

```
Connect-AzAccount
Install-Module -Name Az

```
Upewnij się, że masz zainstalowaną bieżącą wersję programu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Zainstaluj AZ. Usługa frontdoor module 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Tutaj tworzymy grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Utwórz nowe zasady WAF z odpowiedzią niestandardową 

Poniżej znajduje się przykład tworzenia nowych zasad WAF z niestandardowym kodem stanu odpowiedzi ustawionym na 405, a komunikat **jest zablokowany.**, za pomocą polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modyfikowanie niestandardowego kodu odpowiedzi lub ustawień treści odpowiedzi istniejących zasad WAF przy użyciu polecenia [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [zaporze aplikacji sieci Web w usłudze Azure front-drzwi](../afds/afds-overview.md)
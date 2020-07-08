---
title: Zakresy prywatnych adresów IP zapory systemu Azure
description: Zakresy adresów IP można skonfigurować dla tego elementu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: be2bf0f9590a23f9def44a1800338c80f69a782c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610527"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Zakresy prywatnych adresów IP zapory systemu Azure

Zapora platformy Azure zapewnia automatyczny ruch przychodzący do publicznych adresów IP. Domyślnie Zapora platformy Azure nie ma protokołu IPSec z regułami sieci, gdy docelowy adres IP należy do zakresu prywatnych adresów IP na podstawie programu [Iana RFC 1918](https://tools.ietf.org/html/rfc1918). Reguły aplikacji są zawsze stosowane przy użyciu [przezroczystego serwera proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) niezależnie od docelowego adresu IP.

Ta logika działa prawidłowo w przypadku kierowania ruchu bezpośrednio do Internetu. Jeśli jednak włączono [tunelowanie wymuszone](forced-tunneling.md), ruch związany z Internetem jest podłączony do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet, ukrywając źródło z lokalnej zapory.

Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure SNATs ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Można jednak skonfigurować zaporę platformy Azure w taki sposób, aby **nie** przywiązać do tego zakresu publicznych adresów IP.

Aby skonfigurować zaporę platformy Azure w taki sposób, aby nigdy nie były kierowane adresy IP, należy użyć wartości **0.0.0.0/0** jako zakresu prywatnych adresów IP. W przypadku tej konfiguracji zapora platformy Azure nigdy nie może kierować ruchu bezpośrednio do Internetu. Aby skonfigurować zaporę w taki sposób, aby zawsze używała adresów IP bez względu na adres docelowy, użyj wartości **255.255.255.255/32** jako zakresu prywatnego adresu.

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania — Azure PowerShell

Możesz użyć Azure PowerShell, aby określić zakresy prywatnych adresów IP dla zapory.

### <a name="new-firewall"></a>Nowa Zapora

W przypadku nowej zapory polecenie Azure PowerShell jest następujące:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges jest rozwinięty do bieżących ustawień domyślnych w zaporze platformy Azure, podczas gdy inne zakresy są do niej dodawane. Aby zachować wartość domyślną IANAPrivateRanges w specyfikacji zakresu prywatnego, należy pozostawać w `PrivateRange` specyfikacji, jak pokazano w poniższych przykładach.

Aby uzyskać więcej informacji, zobacz polecenie [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Istniejąca Zapora

Aby skonfigurować istniejącą zaporę, użyj następujących poleceń Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Szablony

Do sekcji można dodać następujące elementy `additionalProperties` :

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania — Azure Portal

Możesz użyć Azure Portal, aby określić zakresy prywatnych adresów IP dla zapory.

1. Wybierz grupę zasobów, a następnie wybierz zaporę.
2. Na stronie **Przegląd** , **prywatne zakresy adresów IP**wybierz wartość domyślną **Iana RFC 1918**.

   Zostanie otwarta strona **Edytowanie prefiksów prywatnych adresów IP** :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Edytuj prefiksy prywatnych adresów IP":::

1. Domyślnie **IANAPrivateRanges** jest skonfigurowany.
2. Edytuj zakresy prywatnych adresów IP dla danego środowiska, a następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [wymuszonego tunelowania zapory platformy Azure](forced-tunneling.md).
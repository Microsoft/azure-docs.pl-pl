---
title: Zakresy prywatnych adresów IP zapory systemu Azure
description: Zakresy adresów IP można skonfigurować dla tego elementu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508932"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Zakresy prywatnych adresów IP zapory systemu Azure

Zapora platformy Azure zapewnia automatyczny ruch przychodzący do publicznych adresów IP. Domyślnie Zapora platformy Azure nie ma protokołu IPSec z regułami sieci, gdy docelowy adres IP należy do zakresu prywatnych adresów IP na podstawie programu [Iana RFC 1918](https://tools.ietf.org/html/rfc1918). Reguły aplikacji są zawsze stosowane przy użyciu [przezroczystego serwera proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) niezależnie od docelowego adresu IP.

Ta logika działa prawidłowo w przypadku kierowania ruchu bezpośrednio do Internetu. Jeśli jednak włączono [tunelowanie wymuszone](forced-tunneling.md), ruch związany z Internetem jest podłączony do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet, ukrywając źródło z lokalnej zapory.

Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure SNATs ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Można jednak skonfigurować zaporę platformy Azure w taki sposób, aby **nie** przywiązać do tego zakresu publicznych adresów IP. Na przykład, aby określić pojedynczy adres IP, można określić go następująco: `192.168.1.10` . Aby określić zakres adresów IP, można określić go następująco: `192.168.1.0/24` .

- Aby skonfigurować zaporę platformy Azure w taki sposób, aby **nigdy nie** były kierowane adresy IP, należy użyć wartości **0.0.0.0/0** jako zakresu prywatnych adresów IP. W przypadku tej konfiguracji zapora platformy Azure nigdy nie może kierować ruchu bezpośrednio do Internetu. 

- Aby skonfigurować zaporę w taki sposób, aby **zawsze** używała adresów IP bez względu na adres docelowy, użyj wartości **255.255.255.255/32** jako zakresu prywatnego adresu.

> [!IMPORTANT]
> Określony zakres adresów prywatnych dotyczy tylko reguł sieci. Obecnie reguły aplikacji zawsze są poddanymi translatorem adresów sieciowych.

> [!IMPORTANT]
> Jeśli chcesz określić własne zakresy prywatnych adresów IP i zachować domyślne zakresy organizacji IANA RFC 1918, upewnij się, że Lista niestandardowa nadal zawiera zakres w dokumencie IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania — Azure PowerShell

Możesz użyć Azure PowerShell, aby określić zakresy prywatnych adresów IP dla zapory.

### <a name="new-firewall"></a>Nowa Zapora

W przypadku nowej zapory Azure PowerShell polecenie cmdlet to:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Wdrożenie zapory platformy Azure przy użyciu programu `New-AzFirewall` Wymaga istniejącej sieci wirtualnej i publicznego adresu IP. Zapoznaj się z tematem [wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure PowerShell](deploy-ps.md) w przypadku pełnego przewodnika wdrażania.

> [!NOTE]
> IANAPrivateRanges jest rozwinięty do bieżących ustawień domyślnych w zaporze platformy Azure, podczas gdy inne zakresy są do niej dodawane. Aby zachować wartość domyślną IANAPrivateRanges w specyfikacji zakresu prywatnego, należy pozostawać w `PrivateRange` specyfikacji, jak pokazano w poniższych przykładach.

Aby uzyskać więcej informacji, zobacz polecenie [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

### <a name="existing-firewall"></a>Istniejąca Zapora

Aby skonfigurować istniejącą zaporę, użyj następujących poleceń cmdlet Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Konfigurowanie zakresów prywatnych adresów IP współdziałania — interfejs wiersza polecenia platformy Azure

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby określić zakresy prywatnych adresów IP dla zapory.

### <a name="new-firewall"></a>Nowa Zapora

W przypadku nowej zapory polecenie interfejsu wiersza polecenia platformy Azure jest następujące:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Wdrożenie zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure `az network firewall create` wymaga wykonania dodatkowych czynności konfiguracyjnych w celu utworzenia publicznych adresów IP i konfiguracji protokołu IP. Zobacz [wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md) w celu uzyskania pełnego przewodnika wdrażania.

> [!NOTE]
> IANAPrivateRanges jest rozwinięty do bieżących ustawień domyślnych w zaporze platformy Azure, podczas gdy inne zakresy są do niej dodawane. Aby zachować wartość domyślną IANAPrivateRanges w specyfikacji zakresu prywatnego, należy pozostawać w `PrivateRange` specyfikacji, jak pokazano w poniższych przykładach.

### <a name="existing-firewall"></a>Istniejąca Zapora

Aby skonfigurować istniejącą zaporę, polecenie interfejsu wiersza polecenia platformy Azure jest następujące:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania — szablon ARM

Aby skonfigurować w Template deployment ARM, należy dodać następujący do `additionalProperties` Właściwości:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Skonfiguruj zakresy prywatnych adresów IP współdziałania — Azure Portal

Możesz użyć Azure Portal, aby określić zakresy prywatnych adresów IP dla zapory.

1. Wybierz grupę zasobów, a następnie wybierz zaporę.
2. Na stronie **Przegląd** , **prywatne zakresy adresów IP** wybierz wartość domyślną **Iana RFC 1918**.

   Zostanie otwarta strona **Edytowanie prefiksów prywatnych adresów IP** :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Edytuj prefiksy prywatnych adresów IP":::

1. Domyślnie **IANAPrivateRanges** jest skonfigurowany.
2. Edytuj zakresy prywatnych adresów IP dla danego środowiska, a następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [wymuszonego tunelowania zapory platformy Azure](forced-tunneling.md).
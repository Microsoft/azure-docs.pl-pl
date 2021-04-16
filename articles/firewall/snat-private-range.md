---
title: Azure Firewall prywatnych adresów IP SNAT
description: Zakresy adresów IP można skonfigurować dla SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482753"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall prywatnych adresów IP SNAT

Azure Firewall zapewnia automatyczne SNAT dla całego ruchu wychodzącego do publicznych adresów IP. Domyślnie program Azure Firewall SNAT z regułami sieci, gdy docelowy adres IP znajduje się w zakresie prywatnych adresów IP zgodnie ze standardem [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Reguły aplikacji są zawsze stosowane przy użyciu [przezroczystego serwera proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) niezależnie od docelowego adresu IP.

Ta logika działa dobrze w przypadku bezpośredniego przekierowywu ruchu do Internetu. Jeśli jednak włączono [](forced-tunneling.md)wymuszone tunelowanie, ruch internetowy jest przekierowywał ruch do jednego z prywatnych adresów IP zapory w podsieci AzureFirewallSubnet, ukrywając źródło przed lokalną zaporą.

Jeśli Twoja organizacja używa zakresu publicznych adresów IP dla sieci prywatnych, Azure Firewall SNAT ruch do jednego z prywatnych adresów IP zapory w podsieci AzureFirewallSubnet. Można jednak skonfigurować nie Azure Firewall **SNAT** zakresu publicznych adresów IP. Aby na przykład określić pojedynczy adres IP, można go określić w ten sposób: `192.168.1.10` . Aby określić zakres adresów IP, możesz określić go w ten sposób: `192.168.1.0/24` .

- Aby skonfigurować Azure Firewall **SNAT** niezależnie od docelowego adresu IP, użyj **wartości 0.0.0.0/0 jako** zakresu prywatnych adresów IP. Dzięki tej konfiguracji Azure Firewall nigdy nie może przekierowyć ruchu bezpośrednio do Internetu. 

- Aby skonfigurować zaporę do zawsze **SNAT** niezależnie od adresu docelowego, użyj **255.255.255.255/32** jako zakres prywatnych adresów IP.

> [!IMPORTANT]
> Określony zakres adresów prywatnych ma zastosowanie tylko do reguł sieci. Obecnie reguły aplikacji zawsze SNAT.

> [!IMPORTANT]
> Jeśli chcesz określić własne zakresy prywatnych adresów IP i zachować domyślne zakresy adresów IANA RFC 1918, upewnij się, że lista niestandardowa nadal zawiera zakres IANA RFC 1918. 

Prywatne adresy IP SNAT można skonfigurować przy użyciu następujących metod. Prywatne adresy SNAT należy skonfigurować przy użyciu metody odpowiedniej dla twojej konfiguracji. Zapory skojarzone z zasadami zapory muszą określać zakres w zasadach, a nie używać `AdditionalProperties` .


|Metoda            |Używanie reguł klasycznych  |Korzystanie z zasad zapory  |
|---------|---------|---------|
|Azure Portal     | [Obsługiwane](#classic-rules-3)| [Obsługiwane](#firewall-policy-1)|
|Azure PowerShell     |[Skonfigurować `PrivateRange`](#classic-rules)|obecnie nieobsługiwane|
|Interfejs wiersza polecenia platformy Azure|[Skonfigurować `--private-ranges`](#classic-rules-1)|obecnie nieobsługiwane|
|Szablon ARM     |[konfigurowanie `AdditionalProperties` we właściwości zapory](#classic-rules-2)|[konfigurowanie `snat/privateRanges` w zasadach zapory](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Konfigurowanie zakresów prywatnych adresów IP SNAT — Azure PowerShell
### <a name="classic-rules"></a>Reguły klasyczne

Możesz użyć Azure PowerShell, aby określić zakresy prywatnych adresów IP dla zapory.

> [!NOTE]
> Właściwość zapory `PrivateRange` jest ignorowana w przypadku zapór skojarzonych z zasadami zapory. Należy użyć właściwości w pliku zgodnie z opisem w tece `SNAT` Configure SNAT private IP address ranges - ARM template (Konfigurowanie zakresów prywatnych adresów `firewallPolicies` [IP SNAT — szablon usługi ARM).](#firewall-policy)

#### <a name="new-firewall"></a>Nowa zapora

W przypadku nowej zapory korzystającej z reguł klasycznych polecenie cmdlet Azure PowerShell to:

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
> Wdrażanie Azure Firewall pomocą `New-AzFirewall` usługi wymaga istniejącej sieci wirtualnej i publicznego adresu IP. Zobacz [Wdrażanie i konfigurowanie Azure Firewall użyciu Azure PowerShell,](deploy-ps.md) aby uzyskać pełny przewodnik wdrażania.

> [!NOTE]
> Rozszerzenie IANAPrivateRanges jest rozszerzane do bieżących wartości domyślnych na Azure Firewall podczas gdy inne zakresy są do niego dodawane. Aby zachować wartość domyślną IANAPrivateRanges w specyfikacji zakresu prywatnego, musi ona pozostać w Twojej specyfikacji, jak pokazano `PrivateRange` w poniższych przykładach.

Aby uzyskać więcej informacji, zobacz [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

#### <a name="existing-firewall"></a>Istniejąca zapora

Aby skonfigurować istniejącą zaporę przy użyciu reguł klasycznych, użyj następujących Azure PowerShell cmdlet:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Konfigurowanie zakresów prywatnych adresów IP SNAT — interfejs wiersza polecenia platformy Azure
### <a name="classic-rules"></a>Reguły klasyczne

Za pomocą interfejsu wiersza polecenia platformy Azure można określić zakresy prywatnych adresów IP dla zapory przy użyciu reguł klasycznych. 

#### <a name="new-firewall"></a>Nowa zapora

W przypadku nowej zapory korzystającej z reguł klasycznych polecenie interfejsu wiersza polecenia platformy Azure to:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Wdrażanie Azure Firewall za pomocą polecenia interfejsu wiersza polecenia platformy Azure wymaga dodatkowych kroków konfiguracji `az network firewall create` w celu utworzenia publicznych adresów IP i konfiguracji adresów IP. Pełny [przewodnik wdrażania Azure Firewall wdrażania można](deploy-cli.md) znaleźć w tesłudze Deploy and configure Azure Firewall using Azure CLI (Wdrażanie i konfigurowanie aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure).

> [!NOTE]
> Rozszerzenie IANAPrivateRanges jest rozszerzane do bieżących wartości domyślnych na Azure Firewall podczas gdy inne zakresy są do niego dodawane. Aby zachować wartość domyślną IANAPrivateRanges w specyfikacji zakresu prywatnego, musi ona pozostać w Twojej specyfikacji, jak pokazano `private-ranges` w poniższych przykładach.

#### <a name="existing-firewall"></a>Istniejąca zapora

Aby skonfigurować istniejącą zaporę przy użyciu reguł klasycznych, polecenie interfejsu wiersza polecenia platformy Azure to:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Konfigurowanie zakresów prywatnych adresów IP SNAT — szablon usługi ARM
### <a name="classic-rules"></a>Reguły klasyczne

Aby skonfigurować SNAT podczas Template deployment ARM, do właściwości można dodać następujące `additionalProperties` elementy:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Zasady zapory

Zapory platformy Azure skojarzone z zasadami zapory mają obsługiwane zakresy prywatne SNAT od wersji 2020-11-01 interfejsu API. Obecnie można użyć szablonu do zaktualizowania prywatnego zakresu SNAT w zasadach zapory. W poniższym przykładzie skonfigurowano zaporę tak, aby **zawsze ruch** sieciowy SNAT był zawsze:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Konfigurowanie zakresów prywatnych adresów IP SNAT — Azure Portal
### <a name="classic-rules"></a>Reguły klasyczne

Za pomocą tej Azure Portal określić zakresy prywatnych adresów IP dla zapory.

1. Wybierz grupę zasobów, a następnie wybierz zaporę.
2. Na stronie **Przegląd,** **Prywatne zakresy adresów IP,** wybierz wartość domyślną **IANA RFC 1918.**

   Zostanie **otwarta strona Edytowanie prywatnych prefiksów** IP:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Edytowanie prywatnych prefiksów IP":::

1. Domyślnie skonfigurowano **ustawienie IANAPrivateRanges.**
2. Edytuj zakresy prywatnych adresów IP dla swojego środowiska, a następnie wybierz pozycję **Zapisz.**

### <a name="firewall-policy"></a>Zasady zapory

1.  Wybierz grupę zasobów, a następnie wybierz zasady zapory.
2.  Wybierz **pozycję Prywatne zakresy adresów IP (SNAT)** w **kolumnie** Ustawienia.

    Domyślnie jest **zaznaczone ustawienie Użyj domyślnego Azure Firewall SNAT** zasad. 
3. Aby dostosować konfigurację SNAT, wyczyść pole wyboru i w obszarze Wykonywanie **SNAT** wybierz warunki, aby wykonać SNAT dla środowiska.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Prywatne zakresy adresów IP (SNAT)":::


4.   Wybierz przycisk **Zastosuj**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [Azure Firewall wymuszonego tunelowania.](forced-tunneling.md)
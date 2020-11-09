---
title: Ustawienia usługi DNS zapory platformy Azure
description: Zaporę platformy Azure można skonfigurować przy użyciu serwera DNS i ustawień serwera proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380950"
---
# <a name="azure-firewall-dns-settings"></a>Ustawienia usługi DNS zapory platformy Azure

Można skonfigurować niestandardowy serwer DNS i włączyć serwer proxy DNS dla zapory platformy Azure. Te ustawienia można skonfigurować podczas wdrażania zapory lub później ze strony **ustawień DNS** .

## <a name="dns-servers"></a>Serwery DNS

Serwer DNS obsługuje i rozpoznaje nazwy domen do adresów IP. Domyślnie Zapora platformy Azure używa do rozpoznawania nazw Azure DNS. Ustawienie **serwer DNS** umożliwia skonfigurowanie własnych serwerów DNS do rozpoznawania nazw zapory platformy Azure. Można skonfigurować jeden lub wiele serwerów.

> [!NOTE]
> W przypadku zapór platformy Azure zarządzanych przy użyciu Menedżera zapory platformy Azure ustawienia DNS są konfigurowane w skojarzonych zasadach zapory platformy Azure.

### <a name="configure-custom-dns-servers---azure-portal"></a>Skonfiguruj niestandardowe serwery DNS — Azure Portal

1. W obszarze **Ustawienia** zapory platformy Azure wybierz pozycję **Ustawienia DNS**.
2. W obszarze **serwery DNS** można wpisać lub dodać istniejące serwery DNS, które zostały wcześniej określone w Virtual Network.
3. Wybierz pozycję **Zapisz**.
4. Zapora kieruje teraz ruch DNS do określonych serwerów DNS w celu rozpoznawania nazw.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Serwery DNS":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Konfigurowanie niestandardowych serwerów DNS — interfejs wiersza polecenia platformy Azure

Poniższy przykład aktualizuje zaporę platformy Azure za pomocą niestandardowych serwerów DNS przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Polecenie `az network firewall` wymaga zainstalowania rozszerzenia wiersza polecenia platformy Azure `azure-firewall` . Można go zainstalować za pomocą polecenia `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Skonfiguruj niestandardowe serwery DNS — Azure PowerShell

Poniższy przykład aktualizuje zaporę platformy Azure za pomocą niestandardowych serwerów DNS przy użyciu Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Serwer proxy DNS

Zaporę platformy Azure można skonfigurować tak, aby działała jako serwer proxy DNS. Serwer proxy DNS działa jako pośrednik dla żądań DNS z maszyn wirtualnych klienta do serwera DNS. W przypadku konfigurowania niestandardowego serwera DNS należy włączyć serwer proxy DNS, aby uniknąć niezgodności rozpoznawania nazw DNS, i włączyć filtrowanie nazw FQDN w regułach sieci.

Jeśli nie włączysz serwera proxy DNS, żądania DNS od klienta mogą być przesyłane do serwera DNS w innym czasie lub zwracać inną odpowiedź w porównaniu z tą zaporą. Serwer proxy DNS umieszcza w ścieżce żądania klienta zaporę platformy Azure, aby uniknąć niespójności.

Istnieją dwa typy funkcji buforowania, które są wykonywane, gdy Zapora platformy Azure jest serwerem proxy DNS:

- Pozytywna pamięć podręczna — rozpoznawanie nazw DNS zakończyło się pomyślnie. Zapora używa czasu wygaśnięcia (Time-to-Live) pakietu lub obiektu. 

- Ujemna pamięć podręczna — rozpoznawanie nazw DNS skutkuje brakiem odpowiedzi lub bez rozwiązania. Zapora buforuje te informacje przez jedną godzinę.

Serwer proxy DNS przechowuje wszystkie rozpoznane adresy IP z nazw FQDN w regułach sieci. Najlepszym rozwiązaniem jest użycie nazw FQDN, które są rozpoznawane jako jeden adres IP.  

### <a name="dns-proxy-configuration"></a>Konfiguracja serwera proxy DNS

Konfiguracja serwera proxy DNS wymaga wykonania trzech kroków:
1. Włącz serwer proxy DNS w ustawieniach DNS zapory platformy Azure.
2. Opcjonalnie można skonfigurować niestandardowy serwer DNS lub użyć podanego ustawienia domyślnego.
3. Na koniec należy skonfigurować prywatny adres IP zapory platformy Azure jako niestandardowy adres DNS w ustawieniach serwera DNS sieci wirtualnej. Gwarantuje to, że ruch DNS jest kierowany do zapory platformy Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Konfigurowanie serwera proxy DNS — Azure Portal

Aby skonfigurować serwer proxy DNS, należy skonfigurować ustawienie serwerów DNS sieci wirtualnej w taki sposób, aby korzystały z prywatnego adresu IP zapory. Następnie Włącz serwer proxy DNS w **ustawieniach DNS** zapory platformy Azure.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurowanie serwerów DNS sieci wirtualnej 

1. Wybierz sieć wirtualną, w której ruch DNS będzie kierowany przez zaporę platformy Azure.
2. W obszarze **Ustawienia** wybierz pozycję **serwery DNS**.
3. W obszarze **serwery DNS** wybierz opcję **niestandardowe** .
4. Wprowadź prywatny adres IP zapory.
5. Wybierz pozycję **Zapisz**.
6. Uruchom ponownie maszyny wirtualne połączone z siecią wirtualną, aby zostały przypisane nowe ustawienia serwera DNS. Maszyny wirtualne nadal używają bieżących ustawień DNS do momentu ponownego uruchomienia.

##### <a name="enable-dns-proxy"></a>Włącz serwer proxy DNS

1. Wybierz zaporę platformy Azure.
2. W obszarze **Ustawienia** wybierz pozycję **Ustawienia DNS**.
3. Domyślnie **serwer proxy DNS** jest wyłączony. Po włączeniu Zapora nasłuchuje na porcie 53 i przekazuje żądania DNS do skonfigurowanych serwerów DNS.
4. Przejrzyj konfigurację **serwerów DNS** , aby upewnić się, że ustawienia są odpowiednie dla danego środowiska.
5. Wybierz pozycję **Zapisz**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Serwer proxy DNS":::

#### <a name="configure-dns-proxy---azure-cli"></a>Konfigurowanie serwera proxy DNS — interfejs wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do skonfigurowania ustawień serwera proxy DNS w zaporze platformy Azure i aktualizacji sieci wirtualnych do używania zapory platformy Azure jako serwera DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurowanie serwerów DNS sieci wirtualnej

Ten przykład umożliwia skonfigurowanie sieci wirtualnej do używania zapory platformy Azure jako serwera DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Włącz serwer proxy DNS

Ten przykład włącza funkcję serwera proxy DNS w zaporze platformy Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Konfigurowanie serwera proxy DNS — Azure PowerShell

Aby skonfigurować ustawienia serwera proxy DNS w zaporze platformy Azure i zaktualizować sieci wirtualne do używania zapory platformy Azure jako serwera DNS, można użyć Azure PowerShell.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurowanie serwerów DNS sieci wirtualnej

 Ten przykład umożliwia skonfigurowanie sieci wirtualnej do używania zapory platformy Azure jako serwera DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Włącz serwer proxy DNS

Ten przykład włącza funkcję serwera proxy DNS w zaporze platformy Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Następne kroki

[Filtrowanie nazw FQDN w regułach sieci](fqdn-filtering-network-rules.md)

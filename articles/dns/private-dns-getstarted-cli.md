---
title: Szybki Start — tworzenie prywatnej strefy DNS platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start utworzysz i testujesz prywatną strefę DNS i rekord w Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę prywatną i rekord DNS przy użyciu interfejsu wiersza polecenia platformy Azure oraz zarządzać nimi.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 820641af00caea4ffca450be8aa81b5357ba1261
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652970"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Szybki Start: Tworzenie prywatnej strefy DNS platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki tworzenia pierwszej prywatnej strefy i rekordu DNS przy użyciu interfejsu wiersza polecenia platformy Azure.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, należy określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi* sieciami wirtualnymi. Gdy Autorejestracja jest włączona, Azure DNS aktualizuje także rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub został usunięty.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure PowerShell](private-dns-getstarted-powershell.md).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz grupę zasobów, która będzie zawierać strefę DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Poniższy przykład tworzy sieć wirtualną o nazwie **myAzureVNet**. Następnie tworzy strefę DNS o nazwie **Private.contoso.com** w grupie zasobów **MyAzureResourceGroup** , łączy strefę DNS z siecią wirtualną **MyAzureVnet** i włącza rejestrację automatyczną.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Jeśli chcesz utworzyć strefę tylko do rozpoznawania nazw (bez automatycznej rejestracji nazwy hosta), możesz użyć `-e false` parametru.

### <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Aby wyliczyć strefy DNS, należy użyć polecenia `az network private-dns zone list`. Aby uzyskać pomoc, zobacz `az network dns zone list --help`.

Określenie grupy zasobów powoduje wyświetlenie listy tylko następujących stref w grupie zasobów:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Pominięcie grupy zasobów powoduje wyświetlenie listy wszystkich stref w subskrypcji:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Ukończenie tej operacji potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network private-dns record-set [record type] add-record`. Aby uzyskać pomoc dotyczącą dodawania na przykład rekordów A, zobacz `az network private-dns record-set A add-record --help`.

 Poniższy przykład tworzy rekord z względną nazwą **bazy danych** w strefie DNS **Private.contoso.com** w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **DB.private.contoso.com**. Typ rekordu to „A” z adresem IP „10.2.0.4”.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Wyświetlanie rekordów DNS

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz można testować rozpoznawanie nazw dla strefy prywatnej **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:

   ```powershell
   ping myVM01.private.contoso.com
   ```

   Wyświetlone dane wyjściowe powinny wyglądać mniej więcej tak:

   ```output
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:

   ```powershell
   ping db.private.contoso.com
   ```

   Wyświetlone dane wyjściowe powinny wyglądać mniej więcej tak:

   ```output
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów **MyAzureResourceGroup** , aby usunąć zasoby utworzone w tym przewodniku Szybki Start.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze usługi Azure DNS Private Zones](private-dns-scenarios.md)


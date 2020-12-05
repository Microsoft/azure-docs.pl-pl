---
title: Konfigurowanie punktów końcowych sieci Azure Files | Microsoft Docs
description: Dowiedz się, jak skonfigurować punkty końcowe usługi Azure File Network.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 079d7aa9b654a318c7269a41605c3e146b08f127
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621335"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurowanie punktów końcowych sieci Azure Files

Azure Files udostępnia dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w ramach sieci wirtualnej i mają prywatny adres IP w przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki.

W tym artykule omówiono sposób konfigurowania punktów końcowych konta magazynu do bezpośredniego dostępu do udziału plików platformy Azure. Większość szczegółów podanych w tym dokumencie dotyczy również sposobu, w jaki Azure File Sync współdziałają z publicznymi i prywatnymi punktami końcowymi dla konta magazynu, jednak aby uzyskać więcej informacji dotyczących zagadnień sieciowych dotyczących wdrożenia Azure File Sync, zobacz [konfigurowanie Azure File Sync proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

Zalecamy zapoznanie się z [zagadnieniami dotyczącymi sieci Azure Files](storage-files-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- W tym artykule założono, że masz już utworzony udział plików platformy Azure na koncie magazynu, z którym chcesz nawiązać połączenie ze środowiska lokalnego. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Konfiguracje punktów końcowych

Punkty końcowe można skonfigurować w taki sposób, aby ograniczyć dostęp sieciowy do konta magazynu. Istnieją dwa podejścia do ograniczania dostępu do konta magazynu w sieci wirtualnej:

- [Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu](#create-a-private-endpoint)  i Ogranicz dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z żądanych sieci wirtualnych może uzyskiwać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- [Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej](#restrict-public-endpoint-access). Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. W przypadku ograniczenia ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskuje się dostęp do konta magazynu za pośrednictwem publicznego adresu IP, ale dostęp jest możliwy tylko z lokalizacji określonych w konfiguracji.

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Utworzenie prywatnego punktu końcowego dla konta magazynu spowoduje wdrożenie następujących zasobów platformy Azure:

- **Prywatny punkt końcowy**: zasób platformy Azure reprezentujący prywatny punkt końcowy konta magazynu. Można to traktować jako zasób, który nawiązuje połączenie z kontem magazynu i interfejsem sieciowym.
- **Interfejs sieciowy (nic)**: interfejs sieciowy, który utrzymuje prywatny adres IP w określonej podsieci/sieci wirtualnej. Jest to dokładnie ten sam zasób, który jest wdrażany podczas wdrażania maszyny wirtualnej, ale nie jest przypisywany do maszyny wirtualnej, jest własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS**: Jeśli nigdy nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, zostanie wdrożona nowa prywatna strefa DNS dla sieci wirtualnej. Zostanie również utworzony rekord DNS A dla konta magazynu w tej strefie DNS. Jeśli w tej sieci wirtualnej wdrożono już prywatny punkt końcowy, nowy rekord konta magazynu zostanie dodany do istniejącej strefy DNS. Wdrażanie strefy DNS jest opcjonalne, ale zdecydowanie zalecane i wymagane, Jeśli instalujesz udziały plików platformy Azure za pomocą jednostki usługi AD lub przy użyciu interfejsu API FileREST.

> [!Note]  
> W tym artykule jest stosowany sufiks DNS konta magazynu dla publicznych regionów platformy Azure `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura dla instytucji rządowych w Stanach Zjednoczonych i Azure (Chiny) — po prostu zastępuje odpowiednie sufiksy dla danego środowiska. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Sprawdź łączność

# <a name="portal"></a>[Portal](#tab/azure-portal)

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany, uruchamiając następujące polecenia z programu PowerShell, wiersza polecenia lub terminalu (działa dla systemu Windows, Linux lub macOS). Należy zamienić na `<storage-account-name>` odpowiednią nazwę konta magazynu:

```
nslookup <storage-account-name>.file.core.windows.net
```

Jeśli wszystko działało prawidłowo, powinny zostać wyświetlone następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej (dane wyjściowe wyświetlane dla systemu Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany przy użyciu następujących poleceń:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Jeśli wszystko działało prawidłowo, należy zobaczyć następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli masz maszynę wirtualną w sieci wirtualnej lub usługa przekazywania DNS została skonfigurowana zgodnie z opisem w temacie [Konfigurowanie przekazywania DNS dla Azure Files](storage-files-networking-dns.md), możesz sprawdzić, czy prywatny punkt końcowy został prawidłowo skonfigurowany przy użyciu następujących poleceń:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Jeśli wszystko działało prawidłowo, należy zobaczyć następujące dane wyjściowe, gdzie `192.168.0.5` jest prywatnym adresem IP prywatnego punktu końcowego w sieci wirtualnej. Należy nadal używać storageaccount.file.core.windows.net, aby zainstalować udział plików zamiast `privatelink` ścieżki.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Ogranicz dostęp do publicznego punktu końcowego

Ograniczenie dostępu publicznego punktu końcowego najpierw wymaga wyłączenia ogólnego dostępu do publicznego punktu końcowego. Wyłączenie dostępu do publicznego punktu końcowego nie ma wpływu na prywatne punkty końcowe. Po wyłączeniu publicznego punktu końcowego możesz wybrać określone sieci lub adresy IP, które mogą nadal uzyskiwać do niego dostęp. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej.

#### <a name="disable-access-to-the-public-endpoint"></a>Wyłącz dostęp do publicznego punktu końcowego

Gdy dostęp do publicznego punktu końcowego jest wyłączony, nadal można uzyskać dostęp do konta magazynu za pomocą jego prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania dotyczące publicznego punktu końcowego konta magazynu zostaną odrzucone, chyba że pochodzą z konkretnego [dozwolonego źródła](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks). 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego do określonych sieci wirtualnych

W przypadku ograniczenia konta magazynu do określonych sieci wirtualnych można zezwalać na żądania kierowane do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Można go używać z prywatnymi punktami końcowymi lub bez nich.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Zobacz także

- [Zagadnienia dotyczące sieci Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie przekazywania DNS dla usługi Azure Files](storage-files-networking-dns.md)
- [Konfigurowanie sieci VPN S2S dla Azure Files](storage-files-configure-s2s-vpn.md)
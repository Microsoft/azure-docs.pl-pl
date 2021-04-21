---
title: Konfigurowanie Azure Files końcowych sieci | Microsoft Docs
description: Dowiedz się, jak skonfigurować punkty końcowe sieci usługi Azure File.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42e83facec7817b6588bf69977fea5ab74b6b10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759883"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurowanie Azure Files końcowych sieci

Azure Files udostępnia dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w sieci wirtualnej i mają prywatny adres IP z przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob lub kolejki.

W tym artykule opisano sposób konfigurowania punktów końcowych konta magazynu w celu uzyskiwania bezpośredniego dostępu do udziału plików platformy Azure. Większość szczegółów podanych w tym dokumencie dotyczy również sposobu współdziałania usługi Azure File Sync z publicznymi i prywatnymi punktami końcowymi dla konta magazynu. Aby jednak uzyskać więcej informacji o zagadnieniach dotyczących sieci podczas wdrażania usługi Azure File Sync, zobacz konfigurowanie ustawień serwera proxy i zapory usługi [Azure File Sync.](../file-sync/file-sync-firewall-and-proxy.md)

Zalecamy przeczytanie [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- W tym artykule przyjęto założenie, że utworzono już udział plików platformy Azure na koncie magazynu, z którym chcesz nawiązać połączenie ze środowisku lokalnym. Aby dowiedzieć się, jak utworzyć udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure.](storage-how-to-create-file-share.md)
- Jeśli zamierzasz używać programu Azure PowerShell, [zainstaluj najnowszą wersję programu](/powershell/azure/install-az-ps).
- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)

## <a name="endpoint-configurations"></a>Konfiguracje punktów końcowych

Punkty końcowe można skonfigurować tak, aby ograniczyć dostęp sieciowy do konta magazynu. Istnieją dwie metody ograniczania dostępu do konta magazynu do sieci wirtualnej:

- [Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu i](#create-a-private-endpoint)  ogranicz cały dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z odpowiednich sieci wirtualnych może uzyskać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- [Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej.](#restrict-public-endpoint-access) Działa to przy użyciu możliwości sieci wirtualnej nazywanej *punktami końcowymi usługi*. W przypadku ograniczenia ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskujesz dostęp do konta magazynu za pośrednictwem publicznego adresu IP, ale dostęp jest możliwy tylko z lokalizacji podanych w konfiguracji.

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Utworzenie prywatnego punktu końcowego dla konta magazynu spowoduje wdrożenie następujących zasobów platformy Azure:

- **Prywatny punkt końcowy:** zasób platformy Azure reprezentujący prywatny punkt końcowy konta magazynu. Można to myśleć jak o zasobie, który łączy konto magazynu z interfejsem sieciowym.
- **Interfejs sieciowy ( NIC):** interfejs sieciowy, który przechowuje prywatny adres IP w określonej sieci wirtualnej/podsieci. Jest to dokładnie ten sam zasób, który jest wdrażany podczas wdrażania maszyny wirtualnej, jednak zamiast przypisywać ją do maszyny wirtualnej, jest ona własnością prywatnego punktu końcowego.
- **Prywatna strefa DNS:** jeśli wcześniej nie wdrożono prywatnego punktu końcowego dla tej sieci wirtualnej, dla sieci wirtualnej zostanie wdrożona nowa prywatna strefa DNS. Rekord DNS A zostanie również utworzony dla konta magazynu w tej strefie DNS. Jeśli w tej sieci wirtualnej wdrożono już prywatny punkt końcowy, nowy rekord A dla konta magazynu zostanie dodany do istniejącej strefy DNS. Wdrożenie strefy DNS jest opcjonalne, jednak zdecydowanie zalecane i wymagane w przypadku instalowanie udziałów plików platformy Azure przy użyciu jednostki usługi AD lub przy użyciu interfejsu API FileREST.

> [!Note]  
> W tym artykule jest używany sufiks DNS konta magazynu dla regionów publicznych platformy Azure, `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura azure dla instytucji rządowych USA i chmura Azure — Chiny — wystarczy zastąpić odpowiednie sufiksy dla twojego środowiska. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Weryfikowanie łączności

# <a name="portal"></a>[Portal](#tab/azure-portal)

Jeśli masz maszynę wirtualną wewnątrz sieci wirtualnej lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany, uruchamiając następujące polecenia w programie PowerShell, wierszu polecenia lub terminalu (działa w systemach Windows, Linux lub macOS). Należy zastąpić `<storage-account-name>` odpowiednią nazwą konta magazynu:

```
nslookup <storage-account-name>.file.core.windows.net
```

Jeśli wszystko działa prawidłowo, powinny zostać wyświetlone następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego w sieci wirtualnej (dane wyjściowe widoczne dla `192.168.0.5` systemu Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Jeśli wszystko zadziałało pomyślnie, powinny zostać wyświetlony następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego `192.168.0.5` w sieci wirtualnej:

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

Jeśli w sieci wirtualnej znajduje się maszyna wirtualna lub skonfigurowano przekazywanie DNS zgodnie z opisem w temacie Konfigurowanie przekazywania DNS dla usługi [Azure Files,](storage-files-networking-dns.md)możesz sprawdzić, czy prywatny punkt końcowy został poprawnie skonfigurowany za pomocą następujących poleceń:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Jeśli wszystko zadziałało pomyślnie, powinny zostać wyświetlony następujące dane wyjściowe, gdzie to prywatny adres IP prywatnego punktu końcowego `192.168.0.5` w sieci wirtualnej. Nadal należy używać storageaccount.file.core.windows.net do instalacji udziału plików zamiast `privatelink` ścieżki.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Ograniczanie dostępu do publicznego punktu końcowego

Najpierw ograniczenie dostępu do publicznego punktu końcowego wymaga wyłączenia ogólnego dostępu do publicznego punktu końcowego. Wyłączenie dostępu do publicznego punktu końcowego nie ma wpływu na prywatne punkty końcowe. Po wyłączeniu publicznego punktu końcowego możesz wybrać określone sieci lub adresy IP, które mogą nadal mieć do niego dostęp. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej.

#### <a name="disable-access-to-the-public-endpoint"></a>Wyłączanie dostępu do publicznego punktu końcowego

Gdy dostęp do publicznego punktu końcowego jest wyłączony, dostęp do konta magazynu jest nadal możliwy za pośrednictwem jego prywatnych punktów końcowych. W przeciwnym razie prawidłowe żądania do publicznego punktu końcowego konta magazynu zostaną odrzucone, chyba że pochodzą z [specjalnie dozwolonego źródła](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks). 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Ograniczanie dostępu do publicznego punktu końcowego do określonych sieci wirtualnych

W przypadku ograniczenia konta magazynu do określonych sieci wirtualnych zezwalasz na żądania do publicznego punktu końcowego z określonych sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej nazywanej *punktami końcowymi usługi*. Można jej używać z prywatnymi punktami końcowymi lub bez niego.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Zobacz też

- [Azure Files dotyczące sieci](storage-files-networking-overview.md)
- [Konfigurowanie przekazywania DNS dla usługi Azure Files](storage-files-networking-dns.md)
- [Konfigurowanie sieci VPN S2S dla Azure Files](storage-files-configure-s2s-vpn.md)
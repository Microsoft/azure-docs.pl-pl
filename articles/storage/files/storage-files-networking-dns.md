---
title: Konfigurowanie przekazywania DNS dla Azure Files | Microsoft Docs
description: Dowiedz się, jak skonfigurować przekazywanie DNS dla Azure Files.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94630196"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurowanie przekazywania DNS dla usługi Azure Files
Azure Files umożliwia tworzenie prywatnych punktów końcowych dla kont magazynu zawierających udziały plików. Mimo że jest to przydatne w przypadku wielu różnych aplikacji, prywatne punkty końcowe są szczególnie przydatne do łączenia się z udziałami plików platformy Azure z sieci lokalnej przy użyciu połączenia VPN lub ExpressRoute za pomocą komunikacji równorzędnej. 

Aby połączenia z kontem magazynu były przenoszone przez tunel sieciowy, w pełni kwalifikowana nazwa domeny (FQDN) konta magazynu musi zostać rozpoznana jako prywatny adres IP prywatnego punktu końcowego. Aby to osiągnąć, należy przekazać sufiks punktu końcowego magazynu ( `core.windows.net` dla regionów chmury publicznej) do prywatnej usługi DNS platformy Azure dostępnej w sieci wirtualnej. W tym przewodniku przedstawiono sposób konfigurowania i konfigurowania funkcji przekazywania DNS w celu prawidłowego rozpoznania adresu IP prywatnego punktu końcowego konta magazynu.

Zdecydowanie zalecamy zapoznanie się z [planowaniem Azure Files wdrożenia](storage-files-planning.md) i [Azure Files zagadnień dotyczących sieci](storage-files-networking-overview.md) przed wykonaniem kroków opisanych w tym artykule.

## <a name="overview"></a>Omówienie
Azure Files udostępnia dwa główne typy punktów końcowych do uzyskiwania dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w ramach sieci wirtualnej i mają prywatny adres IP w przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki.

Każde konto magazynu ma w pełni kwalifikowaną nazwę domeny (FQDN). W przypadku regionów chmury publicznej ta nazwa FQDN jest zgodna ze wzorcem, `storageaccount.file.core.windows.net` gdzie `storageaccount` jest nazwą konta magazynu. Po wprowadzeniu żądań dotyczących tej nazwy, takich jak zainstalowanie udziału na stacji roboczej przy użyciu protokołu SMB, system operacyjny wykonuje wyszukiwanie DNS, aby rozpoznać w pełni kwalifikowaną nazwę domeny adres IP, za pomocą którego można wysyłać żądania SMB do programu.

Domyślnie program `storageaccount.file.core.windows.net` jest rozpoznawany jako adres IP publicznego punktu końcowego. Publiczny punkt końcowy dla konta magazynu jest hostowany w klastrze usługi Azure Storage, który hostuje wiele innych publicznych punktów końcowych kont magazynu. Gdy tworzysz prywatny punkt końcowy, prywatna strefa DNS jest połączona z siecią wirtualną, do której została dodana, z mapowaniem rekordu CNAME `storageaccount.file.core.windows.net` na wpis rekordu a dla prywatnego adresu IP prywatnego punktu końcowego konta magazynu. Dzięki temu można używać `storageaccount.file.core.windows.net` nazwy FQDN w sieci wirtualnej i rozpoznać ją jako adres IP prywatnego punktu końcowego.

Najważniejszym celem jest uzyskanie dostępu do udziałów plików platformy Azure hostowanych w ramach konta magazynu lokalnego przy użyciu tunelu sieciowego, takiego jak połączenie VPN lub ExpressRoute, należy skonfigurować lokalne serwery DNS do przesyłania dalej żądań wysyłanych do usługi Azure Files do prywatnej usługi DNS platformy Azure. Aby to osiągnąć, należy skonfigurować *warunkowe przekazywanie* `*.core.windows.net` (lub odpowiedni sufiks punktu końcowego magazynu dla instytucji rządowych Stanów Zjednoczonych, Niemiec lub Chin) do serwera DNS hostowanego w sieci wirtualnej platformy Azure. Ten serwer DNS następnie rekursywnie przekaże żądanie do prywatnej usługi DNS platformy Azure, która będzie rozpoznawać w pełni kwalifikowaną nazwę domeny konta magazynu na odpowiednim prywatnym adresie IP.

Skonfigurowanie przesyłania dalej DNS dla Azure Files będzie wymagało uruchomienia maszyny wirtualnej do hostowania serwera DNS w celu przesyłania dalej żądań, ale jest to jeden krok dla wszystkich udziałów plików platformy Azure hostowanych w ramach sieci wirtualnej. Ponadto nie jest to jedyne wymaganie dotyczące Azure Files — dowolna usługa platformy Azure, która obsługuje prywatne punkty końcowe, do których chcesz uzyskać dostęp z lokalnego systemu, może korzystać z funkcji przesyłania dalej DNS, która zostanie skonfigurowana w tym przewodniku: Azure Blob Storage, SQL Azure, Cosmos DB itd. 

W tym przewodniku przedstawiono procedurę konfigurowania przesyłania dalej DNS dla punktu końcowego usługi Azure Storage, a także Azure Files żądania rozpoznawania nazw DNS dla wszystkich innych usług Azure Storage (Azure Blob Storage, Azure Table Storage, Azure Queue Storage itp.) zostaną przekazane do prywatnej usługi DNS platformy Azure. W razie potrzeby można również dodać dodatkowe punkty końcowe dla innych usług platformy Azure. Przekierowywanie DNS z powrotem do lokalnych serwerów DNS również zostanie skonfigurowane, aby umożliwić zasobom chmury w sieci wirtualnej (na przykład serwer DFS-N) rozpoznawanie nazw maszyn lokalnych. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było skonfigurować przekazywanie DNS do Azure Files, należy wykonać następujące czynności:

- Konto magazynu zawierające udział plików platformy Azure, który chcesz zainstalować. Aby dowiedzieć się, jak utworzyć konto magazynu i udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Prywatny punkt końcowy dla konta magazynu. Aby dowiedzieć się, jak utworzyć prywatny punkt końcowy dla Azure Files, zobacz [Tworzenie prywatnego punktu końcowego](storage-files-networking-endpoints.md#create-a-private-endpoint).
- [Najnowsza wersja](/powershell/azure/install-az-ps) modułu Azure PowerShell.

> [!Important]  
> W tym przewodniku przyjęto założenie, że używasz serwera DNS w systemie Windows Server w środowisku lokalnym. Wszystkie kroki opisane w tym przewodniku są możliwe z dowolnym serwerem DNS, a nie tylko z serwerem DNS systemu Windows.

## <a name="manually-configuring-dns-forwarding"></a>Ręczne konfigurowanie przekazywania DNS
Jeśli masz już serwery DNS w sieci wirtualnej platformy Azure lub jeśli wolisz wdrożyć własne maszyny wirtualne jako serwery DNS przez dowolną metodologię używaną przez organizację, możesz skonfigurować usługę DNS ręcznie przy użyciu wbudowanych poleceń cmdlet programu PowerShell serwera DNS.

Na lokalnych serwerach DNS Utwórz usługę przesyłania dalej warunkowego za pomocą polecenia `Add-DnsServerConditionalForwarderZone` . Ta warunkowa usługa przesyłania dalej musi być wdrożona na wszystkich lokalnych serwerach DNS, aby zapewnić skuteczne przekazywanie ruchu do platformy Azure. Pamiętaj, aby zamienić na `<azure-dns-server-ip>` odpowiednie adresy IP dla danego środowiska.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Na serwerach DNS w sieci wirtualnej platformy Azure należy również umieścić usługę przesyłania dalej w taki sposób, aby żądania dla strefy DNS konta magazynu były kierowane do prywatnej usługi DNS platformy Azure, która jest przedstawiona przez zastrzeżony adres IP `168.63.129.16` . (Pamiętaj, aby wypełnić w `$storageAccountEndpoint` przypadku uruchamiania poleceń w ramach innej sesji programu PowerShell).

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Używanie modułu hybrydowego Azure Files do konfigurowania przekazywania DNS
Aby jak najszybciej skonfigurować przekazywanie DNS, w module hybrydowym Azure Files podano automatyzację. Polecenia cmdlet zapewniające manipulowanie usługą DNS w tym module ułatwią wdrażanie serwerów DNS w sieci wirtualnej platformy Azure i aktualizowanie lokalnych serwerów DNS w celu przekazywania ich do nich. 

Jeśli nie korzystasz z modułu hybrydowego Azure Files, musisz najpierw zainstalować go na stacji roboczej. Pobierz [najnowszą wersję](https://github.com/Azure-Samples/azure-files-samples/releases) Azure Files hybrydowego modułu programu PowerShell:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Wdrożenie rozwiązania przekazującego DNS zawiera dwa kroki, utworzenie zestawu reguł przekazywania DNS, który definiuje usługi platformy Azure, do których mają być przekazywane żądania, oraz rzeczywiste wdrożenie usług przesyłania dalej DNS. 

Poniższy przykład przekazuje żądania do konta magazynu, włącznie z żądaniami do Azure Files, Azure Blob Storage, Azure Table Storage i Azure queue storage. W razie potrzeby można dodać przekazywanie dla dodatkowej usługi platformy Azure do reguły za pośrednictwem `-AzureEndpoints` parametru `New-AzDnsForwardingRuleSet` polecenia cmdlet. Pamiętaj, aby zamienić `<virtual-network-resource-group>` , `<virtual-network-name>` i `<subnet-name>` z odpowiednimi wartościami dla danego środowiska.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Warto również sprawdzić, czy jest to przydatne/konieczne, aby podać kilka dodatkowych parametrów:

| Nazwa parametru | Typ | Opis |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Domyślnie serwery DNS zostaną wdrożone w tej samej grupie zasobów co sieć wirtualna. Jeśli nie jest to potrzebne, ten parametr pozwala wybrać alternatywną grupę zasobów, która ma zostać wdrożona w programie. |
| `DnsForwarderRootName` | `string` | Domyślnie serwery DNS wdrożone na platformie Azure mają nazwy `DnsFwder-*` , w których gwiazdka jest wypełniana przez iterator. Ten parametr zmienia katalog główny tej nazwy (tj. `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Domyślnie dla tymczasowego konta domyślnego jest wybierane hasło losowe, a maszyna wirtualna jest przyłączona do domeny. Po przyłączeniu do domeny konto domyślne jest wyłączone. |
| `DomainToJoin` | `string` | Domena do przyłączenia do maszyn wirtualnych DNS do przyłączenia. Domyślnie ta domena jest wybierana na podstawie domeny komputera, na którym są uruchamiane polecenia cmdlet programu. |
| `DnsForwarderRedundancyCount` | `int` | Liczba maszyn wirtualnych DNS do wdrożenia w sieci wirtualnej. Domyślnie program `New-AzDnsForwarder` wdraża dwa serwery DNS w sieci wirtualnej platformy Azure w zestawie dostępności w celu zapewnienia nadmiarowości. Tę liczbę można zmodyfikować zgodnie z potrzebami. |
| `OnPremDnsHostNames` | `HashSet<string>` | Ręcznie określona lista lokalnych nazw hostów DNS w celu utworzenia usług przesyłania dalej. Ten parametr jest przydatny, gdy nie chcesz stosować usług przesyłania dalej na wszystkich lokalnych serwerach DNS, takich jak w przypadku wielu klientów z ręcznie określonymi nazwami DNS. |
| `Credential` | `PSCredential` | Poświadczenie do użycia podczas aktualizacji serwerów DNS. Jest to przydatne, gdy konto użytkownika, które zostało zalogowane, nie ma uprawnień do modyfikowania ustawień DNS. |
| `SkipParentDomain` | `SwitchParameter` | Domyślnie usługi przesyłania dalej DNS są stosowane do domeny najwyższego poziomu, która istnieje w danym środowisku. Na przykład jeśli `northamerica.corp.contoso.com` jest domeną podrzędną `corp.contoso.com` , usługa przesyłania dalej zostanie utworzona dla serwerów DNS skojarzonych z programem `corp.contoso.com` . Ten parametr spowoduje utworzenie usług przesyłania dalej w `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Potwierdź usługi przesyłania dalej DNS
Przed rozpoczęciem testowania, aby sprawdzić, czy usługi przesyłania dalej DNS zostały pomyślnie zastosowane, zalecamy wyczyszczenie pamięci podręcznej DNS na lokalnej stacji roboczej przy użyciu programu `Clear-DnsClientCache` . Aby sprawdzić, czy można pomyślnie rozpoznać w pełni kwalifikowaną nazwę domeny konta magazynu, użyj `Resolve-DnsName` lub `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Jeśli rozpoznawanie nazw powiedzie się, powinien zostać wyświetlony rozpoznany adres IP zgodny z adresem IP Twojego konta magazynu.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Jeśli skonfigurowano już połączenie sieci VPN lub ExpressRoute, można również użyć, `Test-NetConnection` Aby zobaczyć, że można pomyślnie nawiązać połączenie TCP z kontem magazynu.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Zagadnienia dotyczące sieci Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie punktów końcowych sieci Azure Files](storage-files-networking-endpoints.md)
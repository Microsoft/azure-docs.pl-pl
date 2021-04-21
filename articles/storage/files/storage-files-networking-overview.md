---
title: Azure Files dotyczące sieci | Microsoft Docs
description: Omówienie opcji sieciowych dla Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759691"
---
# <a name="azure-files-networking-considerations"></a>Azure Files dotyczące sieci 
Połączenie z udziałem plików platformy Azure można nawiązać na dwa sposoby:

- Uzyskiwanie dostępu do udziału bezpośrednio za pośrednictwem protokołu SMB (Server Message Block), sieciowego systemu plików (NFS) (wersja zapoznawcza) lub protokołów FileREST. Ten wzorzec dostępu jest używany głównie w celu wyeliminowania jak najwięcej serwerów lokalnych.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym (lub na maszynie wirtualnej platformy Azure) przy użyciu usługi Azure File Sync oraz uzyskiwanie dostępu do danych udziału plików z serwera lokalnego przy użyciu wybranego protokołu (SMB, NFS, FTPS itp.) na użytek własnego przypadku użycia. Ten wzorzec dostępu jest przydatny, ponieważ łączy najlepsze cechy wydajności lokalnej i skali chmury oraz bez serwera usług dołączanych, takich jak Azure Backup.

W tym artykule opisano sposób konfigurowania sieci w przypadku, gdy przypadek użycia wymaga bezpośredniego dostępu do udziału plików platformy Azure, a nie do Azure File Sync. Aby uzyskać więcej informacji o zagadnieniach dotyczących sieci Azure File Sync wdrożenia, zobacz [Azure File Sync dotyczące sieci.](../file-sync/file-sync-networking-overview.md)

Konfiguracja sieci dla udziałów plików platformy Azure jest wykonywana na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob lub kolejki. Konta magazynu ujawniają wiele ustawień, które ułatwiają zabezpieczanie dostępu sieciowego do udziałów plików: punkty końcowe sieci, ustawienia zapory konta magazynu i szyfrowanie podczas przesyłania. 

Zalecamy przeczytanie [tematu Planowanie wdrożenia Azure Files](storage-files-planning.md) przed przeczytaniem tego przewodnika koncepcyjnego.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ten film wideo zawiera przewodnik i demonstrację bezpiecznego uwidocznienia udziałów plików platformy Azure bezpośrednio dla pracowników informacyjnych i aplikacji w pięciu prostych krokach. Poniższe sekcje zawierają linki i dodatkowy kontekst do dokumentacji, do których odwołuje się film wideo.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Uzyskiwanie dostępu do udziałów plików platformy Azure
Po wdrożeniu udziału plików platformy Azure w ramach konta magazynu udział plików jest natychmiast dostępny za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą pochodzić bezpiecznie z platformy Azure lub poza platformą Azure. 

W wielu środowiskach klienta początkowa instalacji udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, mimo że instalacji z maszyn wirtualnych platformy Azure zakończy się pomyślnie. Wynika to z tego, że wiele organizacji i usługodawców internetowych blokuje port używany przez SMB do komunikacji ( port 445). Udziały NFS nie mają tego problemu. Ta praktyka wynika ze wskazówek dotyczących zabezpieczeń dotyczących starszych i przestarzałych wersji protokołu SMB. Chociaż protokół SMB 3.0 jest protokołem bezpiecznym z Internetu, starsze wersje protokołu SMB, zwłaszcza SMB 1.0, nie są. Dostęp do udziałów plików platformy Azure można uzyskiwać tylko zewnętrznie za pośrednictwem protokołu SMB 3.0 i protokołu FileREST (który jest również bezpiecznym protokołem internetowym) za pośrednictwem publicznego punktu końcowego.

Ponieważ najprostszym sposobem uzyskania dostępu do udziału plików SMB platformy Azure ze środowiska lokalnego jest otwarcie sieci lokalnej na port 445, firma Microsoft zaleca następujące kroki w celu usunięcia rozwiązania SMB 1.0 ze środowiska:

1. Upewnij się, że na urządzeniach organizacji jest usuwany lub wyłączany SMB 1.0. Wszystkie obecnie obsługiwane wersje systemów Windows i Windows Server obsługują usuwanie lub wyłączanie funkcji SMB 1.0. Począwszy od wersji Windows 10, wersja 1709, SMB 1.0 nie jest domyślnie instalowana w systemie Windows. Aby dowiedzieć się więcej na temat wyłączania funkcji SMB 1.0, zobacz nasze strony dotyczące systemu operacyjnego:
    - [Zabezpieczanie systemu Windows lub Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpieczanie systemu Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Upewnij się, że żadne produkty w organizacji nie wymagają SMB 1.0 i usuń te, które to robią. Utrzymujemy magazyn [rozliczeń produktów SMB1,](https://aka.ms/stillneedssmb1)który zawiera wszystkie produkty firmy Microsoft znane firmie Microsoft jako pierwsze i inne, które wymagają SMB 1.0. 
1. (Opcjonalnie) Użyj zapory innej firmy z siecią lokalną organizacji, aby zapobiec opuszczeniu przez ruch SMB 1.0 granic organizacji.

Jeśli Twoja organizacja wymaga zablokowania portu 445 zgodnie z zasadami lub regulacjami lub organizacja wymaga, aby ruch do platformy Azure podążał ścieżką deterministyczną, możesz użyć usługi Azure VPN Gateway lub ExpressRoute do tunelowania ruchu do udziałów plików platformy Azure. Udziały NFS nie wymagają żadnego z tych, ponieważ nie wymagają portu 445.

> [!Important]  
> Nawet jeśli zdecydujesz się użyć alternatywnej metody uzyskiwania dostępu do udziałów plików platformy Azure, firma Microsoft nadal zaleca usunięcie ze środowiska funkcji SMB 1.0.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelowanie ruchu przez wirtualną sieć prywatną lub usługę ExpressRoute
Podczas ustanawiania tunelu sieciowego między siecią lokalną a platformą Azure komunikacja równorzędna sieci lokalnej jest nawiązywana za pomocą co najmniej jednej sieci wirtualnej na platformie Azure. Sieć [wirtualna](../../virtual-network/virtual-networks-overview.md), czyli sieć wirtualna, jest podobna do tradycyjnej sieci, która działa w środowisku lokalnym. Podobnie jak w przypadku konta usługi Azure Storage lub maszyny wirtualnej platformy Azure, sieć wirtualna jest zasobem platformy Azure wdrożonym w grupie zasobów. 

Azure Files obsługuje następujące mechanizmy tunelowania ruchu między lokalnymi stacjami roboczymi i serwerami a udziałami plików SMB/NFS platformy Azure:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)Brama sieci VPN to określony typ bramy sieci wirtualnej, który jest używany do wysyłania zaszyfrowanego ruchu sieciowego między siecią wirtualną platformy Azure a lokalizacją alternatywną (np. lokalną) przez Internet. Usługa Azure VPN Gateway to zasób platformy Azure, który można wdrożyć w grupie zasobów obok konta magazynu lub innych zasobów platformy Azure. Bramy sieci VPN uwidoczniają dwa różne typy połączeń:
    - [Połączenia bramy sieci VPN typu punkt-lokacja (P2S, point-to-site),](../../vpn-gateway/point-to-site-about.md) które są połączeniami sieci VPN między platformą Azure i indywidualnym klientem. To rozwiązanie jest przydatne głównie w przypadku urządzeń, które nie są częścią sieci lokalnej organizacji, takich jak osoby telekomunikacyjne, które chcą mieć możliwość instalacji udziału plików platformy Azure w domu, kawiarni lub hotelu w podróży. Aby użyć połączenia sieci VPN P2S z Azure Files, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Aby uprościć wdrażanie połączenia sieci VPN typu punkt-lokacja, zobacz Configure [a Point-to-Site (P2S) VPN on Windows for](storage-files-configure-p2s-vpn-windows.md) use with Azure Files (Konfigurowanie połączenia sieci VPN typu punkt-lokacja) w systemie Linux do użycia z systemem [Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Sieć VPN typu lokacja-lokacja (S2S),](../../vpn-gateway/design.md#s2smulti)która jest połączeniami sieci VPN między platformą Azure i siecią organizacji. Połączenie sieci VPN typu lokacja-lokacja umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanych w sieci organizacji, a nie dla każdego urządzenia klienckiego, które musi uzyskać dostęp do udziału plików platformy Azure. Aby uprościć wdrażanie połączenia sieci VPN typu lokacja-lokacja, zobacz [Configure a Site-to-Site (S2S) VPN for use with Azure Files](storage-files-configure-s2s-vpn.md).
- [Usługa ExpressRoute](../../expressroute/expressroute-introduction.md), która umożliwia utworzenie zdefiniowanej trasy między platformą Azure i siecią lokalną, która nie przechodzi przez Internet. Ponieważ usługa ExpressRoute zapewnia dedykowaną ścieżkę między lokalnym centrum danych i platformą Azure, usługa ExpressRoute może być przydatna, gdy należy wziąć pod uwagę wydajność sieci. Usługa ExpressRoute jest również dobrą opcją, gdy wymagania prawne lub zasady organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

Niezależnie od metody tunelowania, której używasz do uzyskiwania dostępu do udziałów plików platformy Azure, potrzebujesz mechanizmu zapewniającego, że ruch do konta magazynu przechodzi przez tunel, a nie zwykłe połączenie internetowe. Technicznie jest możliwe przekierowanie do publicznego punktu końcowego konta magazynu, jednak wymaga to stałego kodowania wszystkich adresów IP klastrów usługi Azure Storage w regionie, ponieważ konta magazynu można przenosić między klastrami magazynu w dowolnym momencie. Wymaga to również ciągłego aktualizowania mapowań adresów IP, ponieważ nowe klastry są dodawane przez cały czas.

Zamiast kodowania adresów IP kont magazynu na reguły routingu sieci VPN, zalecamy użycie prywatnych punktów końcowych, które zapewniają kontu magazynu adres IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ponieważ utworzenie tunelu do platformy Azure umożliwia nawiązanie komunikacji równorzędnej między siecią lokalną i co najmniej jedną siecią wirtualną, umożliwia to prawidłowy routing w trwały sposób.

### <a name="private-endpoints"></a>Prywatne punkty końcowe
Oprócz domyślnego publicznego punktu końcowego dla konta magazynu Azure Files opcję co najmniej jednego prywatnego punktu końcowego. Prywatny punkt końcowy to punkt końcowy, który jest dostępny tylko w sieci wirtualnej platformy Azure. Podczas tworzenia prywatnego punktu końcowego dla konta magazynu konto magazynu otrzymuje prywatny adres IP z przestrzeni adresowej sieci wirtualnej, podobnie jak w przypadku sposobu, w jaki lokalne serwery plików lub urządzenie NAS odbiera adres IP w dedykowanej przestrzeni adresowej sieci lokalnej. 

Pojedynczy prywatny punkt końcowy jest skojarzony z określoną podsiecią sieci wirtualnej platformy Azure. Konto magazynu może mieć prywatne punkty końcowe w więcej niż jednej sieci wirtualnej.

Używanie prywatnych punktów końcowych z Azure Files umożliwia:
- Bezpiecznie nawiązaj połączenie z udziałami plików platformy Azure z sieci lokalnych przy użyciu sieci VPN lub połączenia usługi ExpressRoute z prywatną komunikacja równorzędną.
- Zabezpiecz udziały plików platformy Azure, konfigurując zaporę konta magazynu w celu blokowania wszystkich połączeń w publicznym punkcie końcowym. Domyślnie utworzenie prywatnego punktu końcowego nie blokuje połączeń z publicznym punktem końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej (i granic komunikacji równorzędnej).

Aby utworzyć prywatny punkt końcowy, zobacz [Konfigurowanie prywatnych punktów końcowych dla Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Prywatne punkty końcowe i system DNS
Podczas tworzenia prywatnego punktu końcowego domyślnie tworzymy również (lub aktualizujemy istniejącą) prywatną strefę DNS odpowiadającą `privatelink` poddomenie. Ściślej rzecz biorąc, utworzenie prywatnej strefy DNS nie jest wymagane do korzystania z prywatnego punktu końcowego dla konta magazynu, ale zdecydowanie zaleca się to ogólnie i jawnie wymagane podczas instalowanie udziału plików platformy Azure za pomocą nazwy głównej użytkownika usługi Active Directory lub uzyskiwania dostępu z interfejsu API FileREST.

> [!Note]  
> W tym artykule jest używany sufiks DNS konta magazynu dla regionów publicznych platformy Azure, `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura azure dla instytucji rządowych USA i chmura Azure — Chiny — wystarczy zastąpić odpowiednie sufiksy dla swojego środowiska. 

W prywatnej strefie DNS utworzymy rekord A dla i rekord CNAME dla zwykłej nazwy konta magazynu, zgodnie ze `storageaccount.privatelink.file.core.windows.net` wzorcem `storageaccount.file.core.windows.net` . Ponieważ prywatna strefa DNS platformy Azure jest połączona z siecią wirtualną zawierającą prywatny punkt końcowy, możesz obserwować konfigurację DNS, wywołując polecenie cmdlet z programu PowerShell na maszynie wirtualnej platformy Azure (alternatywnie w systemach Windows i `Resolve-DnsName` `nslookup` Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

W tym przykładzie konto magazynu jest rozpoznawczym prywatnym adresem IP prywatnego punktu `storageaccount.file.core.windows.net` końcowego, który ma dzieje się `192.168.0.4` .

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


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Jeśli to samo polecenie zostanie uruchomione lokalnie, zobaczysz, że ta sama nazwa konta magazynu jest rozpoznawczy jako publiczny adres IP konta magazynu. to rekord CNAME dla , który z kolei jest rekordem CNAME klastra `storageaccount.file.core.windows.net` `storageaccount.privatelink.file.core.windows.net` magazynu platformy Azure hostującym konto magazynu:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Odzwierciedla to fakt, że konto magazynu może uwidocznić zarówno publiczny punkt końcowy, jak i co najmniej jeden prywatny punkt końcowy. Aby upewnić się, że nazwa konta magazynu jest rozpoznawczy jako prywatny adres IP prywatnego punktu końcowego, należy zmienić konfigurację lokalnych serwerów DNS. Można to zrobić na kilka sposobów:

- Modyfikowanie pliku hosts na klientach w celu rozpoznania prywatnego adresu IP żądanego `storageaccount.file.core.windows.net` prywatnego punktu końcowego. Jest to zdecydowanie zalecane w środowiskach produkcyjnych, ponieważ należy wprowadzić te zmiany w każdym kliencie, który chce zainstalować udziały plików platformy Azure, a zmiany na koncie magazynu lub prywatnym punkcie końcowym nie będą obsługiwane automatycznie.
- Tworzenie rekordu A na `storageaccount.file.core.windows.net` lokalnych serwerach DNS. Ma to tę zaletę, że klienci w środowisku lokalnym będą mogli automatycznie rozpoznać konto magazynu bez konieczności konfigurowania poszczególnych klientów, jednak to rozwiązanie jest podobne do modyfikowania pliku hostów, ponieważ zmiany nie są odzwierciedlane. Chociaż to rozwiązanie jest brittle, może być najlepszym wyborem w niektórych środowiskach.
- Przekaż `core.windows.net` strefę z lokalnych serwerów DNS do prywatnej strefy DNS platformy Azure. Prywatny host DNS platformy Azure jest dostępny za pośrednictwem specjalnego adresu IP ( ), który jest dostępny tylko wewnątrz sieci wirtualnych połączonych z prywatną strefą `168.63.129.16` DNS platformy Azure. Aby obejść to ograniczenie, możesz uruchomić dodatkowe serwery DNS w sieci wirtualnej, które będą przekazywane `core.windows.net` dalej do prywatnej strefy DNS platformy Azure. Aby uprościć tę konfigurację, oferujemy polecenia cmdlet programu PowerShell, które automatycznie wdrażają serwery DNS w sieci wirtualnej platformy Azure i konfigurują je zgodnie z potrzebami. Aby dowiedzieć się, jak skonfigurować przekazywanie DNS, zobacz [Konfigurowanie usługi DNS przy użyciu Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Ustawienia zapory konta magazynu
Zapora to zasady sieci, które kontrolują, które żądania mogą uzyskać dostęp do publicznego punktu końcowego dla konta magazynu. Za pomocą zapory konta magazynu można ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych adresów IP lub zakresów albo do sieci wirtualnej. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej. 

Istnieją dwa podejścia do ograniczania dostępu do konta magazynu do sieci wirtualnej:
- Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu i ogranicz cały dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z odpowiednich sieci wirtualnych może uzyskać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej. Działa to przy użyciu możliwości sieci wirtualnej nazywanej *punktami końcowymi usługi*. Po ograniczeniu ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskujesz dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

> [!NOTE]
> Udziały NFS nie mogą uzyskać dostępu do publicznego punktu końcowego konta magazynu za pośrednictwem publicznego adresu IP. Dostęp do publicznego punktu końcowego konta magazynu można uzyskać tylko przy użyciu sieci wirtualnych. Udziały NFS mogą również uzyskać dostęp do konta magazynu przy użyciu prywatnych punktów końcowych.

Aby dowiedzieć się więcej na temat konfigurowania zapory konta magazynu, zobacz konfigurowanie zapór i sieci wirtualnych [usługi Azure Storage.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!IMPORTANT]
> Ta sekcja obejmuje szyfrowanie w szczegółach przesyłania dla udziałów SMB. Aby uzyskać szczegółowe informacje dotyczące szyfrowania podczas przesyłania z udziałami NFS, zobacz [Zabezpieczenia](storage-files-compare-protocols.md#security).

Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. Oznacza to, że w przypadku uwierzytelniania udziału plików za pośrednictwem protokołu SMB lub uzyskiwania do niego dostępu za pośrednictwem protokołu FileREST (na przykład za pośrednictwem protokołu Azure Portal, programu PowerShell/interfejsu wiersza polecenia lub zestawów SDK platformy Azure) program Azure Files zezwoli na połączenie tylko wtedy, gdy zostanie na nim nawiązaniu przy użyciu protokołu SMB 3.0+ z szyfrowaniem lub protokołem HTTPS. Klienci, którzy nie obsługują szyfrowania SMB 3.0 lub obsługują SMB 3.0, ale nie obsługują szyfrowania SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie podczas przesyłania jest włączone. Aby uzyskać więcej informacji o tym, które systemy operacyjne obsługują szyfrowanie za pomocą szyfrowania SMB 3.0, zobacz szczegółową dokumentację dla systemów [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)i [Linux.](storage-how-to-use-files-linux.md) Wszystkie bieżące wersje programu PowerShell, interfejsu wiersza polecenia i zestawów SDK obsługują protokół HTTPS.  

Szyfrowanie podczas przesyłania dla konta usługi Azure Storage można wyłączyć. Jeśli szyfrowanie jest wyłączone, Azure Files również zezwalać na protokół SMB 2.1, protokół SMB 3.0 bez szyfrowania i niezaszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Głównym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak windows Server 2008 R2 lub starsza dystrybucja systemu Linux. Azure Files zezwala tylko na połączenia SMB 2.1 w tym samym regionie platformy Azure co udział plików platformy Azure; Klient SMB 2.1 spoza regionu platformy Azure udziału plików platformy Azure, takiego jak lokalnie lub w innym regionie świadczenia usługi Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Zobacz też
- [Omówienie usługi Azure Files](storage-files-introduction.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
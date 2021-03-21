---
title: Zagadnienia dotyczące sieci Azure Files | Microsoft Docs
description: Omówienie opcji sieciowych dla Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cec386b798b843a5badc9d52d9c71bd7df54b59a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601937"
---
# <a name="azure-files-networking-considerations"></a>Zagadnienia dotyczące sieci Azure Files 
Możesz połączyć się z udziałem plików platformy Azure na dwa sposoby:

- Uzyskiwanie dostępu do udziału bezpośrednio za pośrednictwem protokołu SMB (Server Message Block), sieciowego systemu plików (NFS) lub protokołów FileREST. Ten wzorzec dostępu jest używany przede wszystkim wtedy, gdy można wyeliminować dowolną liczbę serwerów lokalnych.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym (lub na maszynie wirtualnej platformy Azure) przy użyciu Azure File Sync i uzyskiwanie dostępu do danych udziału plików z serwera lokalnego przy użyciu wybranego przez Ciebie protokołu (SMB, NFS, FTPS itp.). Ten wzorzec dostępu jest przydatny, ponieważ łączy się z najlepszą z nich zarówno w przypadku lokalnego, jak i skalowalnych usług, takich jak Azure Backup.

W tym artykule opisano sposób konfigurowania sieci w przypadku, gdy przypadek użycia wywołuje bezpośrednio dostęp do udziału plików platformy Azure, a nie za pomocą Azure File Sync. Aby uzyskać więcej informacji dotyczących zagadnień sieciowych dotyczących wdrażania Azure File Sync, zobacz [Azure File Sync zagadnienia dotyczące sieci](storage-sync-files-networking-overview.md).

Konfiguracja sieci dla udziałów plików platformy Azure odbywa się na koncie usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Konta magazynu uwidaczniają wiele ustawień, które ułatwiają Zabezpieczanie dostępu sieciowego do udziałów plików: punktów końcowych sieci, ustawień zapory konta magazynu i szyfrowania podczas przesyłania. 

Zalecamy zapoznanie się z [planowaniem wdrożenia Azure Files](storage-files-planning.md) przed przeczytaniem tego przewodnika koncepcyjnego.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ten film wideo to przewodnik i demonstracja, jak bezpiecznie ujawniać udziały plików platformy Azure bezpośrednio pracownikom przetwarzającym informacje i aplikacje w pięciu prostych krokach. Poniższe sekcje zawierają linki i dodatkowy kontekst do dokumentacji, do której odwołuje się film wideo.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Uzyskiwanie dostępu do udziałów plików platformy Azure
Gdy wdrażasz udział plików platformy Azure w ramach konta magazynu, udział plików jest natychmiast dostępny za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą bezpiecznie pochodziły z platformy Azure lub spoza niej. 

W wielu środowiskach klienta początkowa instalacja udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, nawet jeśli instalacje z maszyn wirtualnych platformy Azure powiodą się. Przyczyną tego jest to, że wiele organizacji i usługodawców internetowych (ISP) blokują port wykorzystywany przez protokół SMB do komunikacji, port 445. Udziały NFS nie mają tego problemu. To rozwiązanie pochodzi z wskazówek dotyczących zabezpieczeń dotyczących starszych i przestarzałych wersji protokołu SMB. Mimo że protokół SMB 3,0 jest bezpiecznym protokołem internetowym, starsze wersje protokołu SMB, szczególnie SMB 1,0, nie są. Do udziałów plików platformy Azure można uzyskać dostęp tylko zewnętrznie za pośrednictwem protokołu SMB 3,0 i protokół FileREST (który jest również bezpiecznym protokołem internetowym) za pośrednictwem publicznego punktu końcowego.

Ze względu na to, że Najprostszym sposobem uzyskania dostępu do udziału plików SMB platformy Azure ze środowiska lokalnego jest otworzenie sieci lokalnej do portu 445, firma Microsoft zaleca wykonanie następujących kroków w celu usunięcia protokołu SMB 1,0 z danego programu:

1. Upewnij się, że protokół SMB 1,0 jest usuwany lub wyłączony na urządzeniach organizacji. Wszystkie obecnie obsługiwane wersje systemu Windows i systemu Windows Server obsługują usunięcie lub wyłączenie protokołu SMB 1,0 i począwszy od systemu Windows 10, wersja 1709, protokół SMB 1,0 nie jest domyślnie zainstalowany w systemie Windows. Aby dowiedzieć się więcej o tym, jak wyłączyć protokół SMB 1,0, zobacz nasze strony specyficzne dla systemu operacyjnego:
    - [Zabezpieczanie systemu Windows lub Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpieczanie systemu Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Upewnij się, że żadne produkty w organizacji nie wymagają protokołu SMB 1,0 i usuń te, które to zrobią. Utrzymujemy pakiet wydawania [produktów SMB1](https://aka.ms/stillneedssmb1), który zawiera wszystkie produkty pierwszej i innej firmy znane firmie Microsoft w celu wymagania protokołu SMB 1,0. 
1. Obowiązkowe Użyj zapory innej firmy z siecią lokalną w organizacji, aby zapobiec wyjściu z granicy organizacyjnej przez ruch SMB 1,0.

Jeśli Twoja organizacja wymaga, aby port 445 został zablokowany zgodnie z zasadami lub rozporządzeniem, lub organizacja wymaga, aby ruch do platformy Azure był zgodny ze deterministyczną ścieżką, możesz użyć platformy Azure VPN Gateway lub ExpressRoute do tunelowania ruchu do udziałów plików platformy Azure. Udziały NFS nie wymagają żadnego z tych elementów, ponieważ nie potrzebują portu 445.

> [!Important]  
> Nawet jeśli zdecydujesz się na uzyskanie dostępu do udziałów plików platformy Azure przy użyciu alternatywnej metody, firma Microsoft zaleca usunięcie protokołu SMB 1,0 ze środowiska.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelowanie ruchu przez wirtualną sieć prywatną lub ExpressRoute
Po ustanowieniu tunelu sieciowego między siecią lokalną i platformą Azure jest to Komunikacja równorzędna sieci lokalnej z co najmniej jedną siecią wirtualną na platformie Azure. [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md)lub wirtualna jest podobna do tradycyjnej sieci, która działa lokalnie. Podobnie jak konto usługi Azure Storage lub maszyna wirtualna platformy Azure, Sieć wirtualna jest zasobem platformy Azure wdrożonym w grupie zasobów. 

Azure Files obsługuje następujące mechanizmy do tunelowania ruchu między lokalnymi stacjami roboczymi i serwerami oraz udziałami plików SMB/NFS platformy Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brama sieci VPN jest określonym typem bramy usługi Virtual Network, która jest używana do wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a alternatywną lokalizacją (na przykład lokalnie) przez Internet. VPN Gateway platformy Azure to zasób platformy Azure, który można wdrożyć w grupie zasobów obok konta magazynu lub innych zasobów platformy Azure. Bramy sieci VPN uwidaczniają dwa różne typy połączeń:
    - Połączenia bramy [sieci VPN typu punkt-lokacja (P2S)](../../vpn-gateway/point-to-site-about.md) , które są połączeniami sieci VPN między platformą Azure i pojedynczym klientem. To rozwiązanie jest szczególnie przydatne w przypadku urządzeń, które nie są częścią sieci lokalnej w organizacji, takiej jak Telepracownicy, którzy chcą móc instalować udział plików platformy Azure z domu, kawiarnia lub hotelu w podróży. Aby można było użyć połączenia sieci VPN P2S z Azure Files, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Aby uprościć wdrażanie połączenia sieci VPN P2S, zobacz [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z programem Azure Files](storage-files-configure-p2s-vpn-windows.md) i [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Sieci VPN typu lokacja-lokacja (S2S)](../../vpn-gateway/design.md#s2smulti), które są połączeniami sieci VPN między platformą Azure i siecią organizacji. Połączenie sieci VPN S2S umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanego w sieci organizacji, a nie dla każdego urządzenia klienckiego, które wymaga dostępu do udziału plików platformy Azure. Aby uprościć wdrażanie połączenia sieci VPN S2S, zobacz [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), który umożliwia utworzenie zdefiniowanej trasy między platformą Azure i siecią lokalną, która nie przechodzi przez Internet. Ponieważ ExpressRoute zapewnia dedykowaną ścieżkę między lokalnym centrum danych a platformą Azure, ExpressRoute może być przydatne, gdy wydajność sieci jest uwzględniana. ExpressRoute jest również dobrym rozwiązaniem, gdy zasady lub wymagania prawne organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

Niezależnie od tego, która metoda tunelowania służy do uzyskiwania dostępu do udziałów plików platformy Azure, musisz mieć mechanizm, aby upewnić się, że ruch do konta magazynu przeszedł przez tunel, a nie zwykłe połączenie internetowe. Jest to technicznie możliwe kierowanie do publicznego punktu końcowego konta magazynu, ale wymaga to, aby wszystkie adresy IP dla klastrów usługi Azure Storage zostały nakodowane w danym regionie, ponieważ konta magazynu mogą być przenoszone między klastrami magazynu w dowolnym momencie. Wymaga to również stale aktualizowane mapowania adresów IP, ponieważ nowe klastry są dodawane przez cały czas.

Zamiast kodowania adresów IP kont magazynu w regułach routingu sieci VPN, zalecamy używanie prywatnych punktów końcowych, które nadają konto magazynu adres IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ponieważ utworzenie tunelu do platformy Azure powoduje ustanowienie komunikacji równorzędnej między siecią lokalną a co najmniej jedną siecią wirtualną, umożliwia to poprawne kierowanie w sposób trwały.

### <a name="private-endpoints"></a>Prywatne punkty końcowe
Oprócz domyślnego publicznego punktu końcowego dla konta magazynu Azure Files zapewnia opcję mającą co najmniej jeden prywatny punkt końcowy. Prywatny punkt końcowy jest punktem końcowym, który jest dostępny tylko w ramach sieci wirtualnej platformy Azure. Po utworzeniu prywatnego punktu końcowego dla konta magazynu konto magazynu otrzymuje prywatny adres IP z przestrzeni adresowej sieci wirtualnej, podobnie jak lokalny serwer plików lub urządzenie NAS odbiera adres IP w ramach dedykowanej przestrzeni adresowej sieci lokalnej. 

Pojedynczy prywatny punkt końcowy jest skojarzony z określoną podsiecią sieci wirtualnej platformy Azure. Konto magazynu może mieć prywatne punkty końcowe w więcej niż jednej sieci wirtualnej.

Używanie prywatnych punktów końcowych z Azure Files umożliwia:
- Bezpiecznie łącz się z udziałami plików platformy Azure z sieci lokalnych przy użyciu połączenia VPN lub ExpressRoute z prywatną komunikację równorzędną.
- Zabezpiecz udziały plików platformy Azure, konfigurując zaporę konta magazynu w celu blokowania wszystkich połączeń w publicznym punkcie końcowym. Domyślnie tworzenie prywatnego punktu końcowego nie blokuje połączeń z publicznym punktem końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej (i granic komunikacji równorzędnej).

Aby utworzyć prywatny punkt końcowy, zobacz [Konfigurowanie prywatnych punktów końcowych dla Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Prywatne punkty końcowe i system DNS
Gdy tworzysz prywatny punkt końcowy, domyślnie utworzymy również (lub zaktualizuj istniejącą) prywatną strefę DNS odpowiadającą `privatelink` poddomenie. Ściśle mówiąc, tworzenie prywatnej strefy DNS nie jest wymagane do korzystania z prywatnego punktu końcowego dla konta magazynu, ale jest to zdecydowanie zalecane i wymagane w przypadku instalowania udziału plików platformy Azure za pomocą nazwy głównej użytkownika Active Directory lub uzyskiwania dostępu z interfejsu API FileREST.

> [!Note]  
> W tym artykule jest stosowany sufiks DNS konta magazynu dla publicznych regionów platformy Azure `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura dla instytucji rządowych w Stanach Zjednoczonych i Azure (Chiny) — po prostu zastępuje odpowiednie sufiksy dla danego środowiska. 

W prywatnej strefie DNS tworzymy rekord A `storageaccount.privatelink.file.core.windows.net` i rekord CNAME dla zwykłej nazwy konta magazynu, która następuje po wzorcu `storageaccount.file.core.windows.net` . Ze względu na to, że prywatna strefa DNS platformy Azure jest połączona z siecią wirtualną zawierającą prywatny punkt końcowy, można obserwować konfigurację DNS, wywołując `Resolve-DnsName` polecenie cmdlet z programu PowerShell na maszynie wirtualnej platformy Azure (Alternatywnie `nslookup` w systemach Windows i Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

W tym przykładzie konto magazynu jest `storageaccount.file.core.windows.net` rozpoznawane jako prywatny adres IP prywatnego punktu końcowego `192.168.0.4` .

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

Jeśli uruchomisz to samo polecenie z lokalnego, zobaczysz, że ta sama nazwa konta magazynu jest rozpoznawana jako publiczny adres IP konta magazynu; `storageaccount.file.core.windows.net` jest rekordem CNAME dla `storageaccount.privatelink.file.core.windows.net` , który z kolei jest rekordem CNAME dla klastra usługi Azure Storage obsługującego konto magazynu:

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

Odzwierciedla to fakt, że konto magazynu może uwidaczniać zarówno publiczny punkt końcowy, jak i jeden lub więcej prywatnych punktów końcowych. Aby upewnić się, że nazwa konta magazynu jest rozpoznawana jako prywatny adres IP punktu końcowego, należy zmienić konfigurację na lokalnych serwerach DNS. Można to zrobić na kilka sposobów:

- Modyfikowanie pliku Hosts na klientach w celu `storageaccount.file.core.windows.net` rozwiązania problemu do prywatnego adresu IP wybranego prywatnego punktu końcowego. Jest to zdecydowanie odradzane w środowiskach produkcyjnych, ponieważ konieczne będzie wprowadzenie tych zmian do każdego klienta, który chce zainstalować udziały plików platformy Azure, a zmiany na koncie magazynu lub prywatnym punkcie końcowym nie będą automatycznie obsługiwane.
- Tworzenie rekordu A dla `storageaccount.file.core.windows.net` lokalnych serwerów DNS. Dzięki temu klienci w środowisku lokalnym będą mogli automatycznie rozwiązywać konto magazynu bez konieczności konfigurowania poszczególnych klientów, jednak to rozwiązanie będzie podobne kruchy do modyfikowania pliku hosts, ponieważ zmiany nie są uwzględniane. Chociaż to rozwiązanie jest kruchy, najlepszym rozwiązaniem może być w niektórych środowiskach.
- Prześlij dalej `core.windows.net` strefę od lokalnych serwerów DNS do prywatnej strefy DNS platformy Azure. Prywatny Host DNS platformy Azure można uzyskać za pomocą specjalnego adresu IP ( `168.63.129.16` ), który jest dostępny tylko w sieciach wirtualnych połączonych z prywatną strefą DNS platformy Azure. Aby obejść to ograniczenie, można uruchomić dodatkowe serwery DNS w sieci wirtualnej, które będą przekazywać `core.windows.net` do prywatnej strefy DNS platformy Azure. Aby uprościć tę konfigurację, udostępniono polecenia cmdlet programu PowerShell, które będą automatycznie wdrażać serwery DNS w sieci wirtualnej platformy Azure i konfigurować je zgodnie z potrzebami. Aby dowiedzieć się, jak skonfigurować przekazywanie DNS, zobacz [Konfigurowanie systemu DNS przy użyciu Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Ustawienia zapory konta magazynu
Zapora to zasada sieciowa, która kontroluje, które żądania mogą uzyskać dostęp do publicznego punktu końcowego dla konta magazynu. Za pomocą zapory konta magazynu można ograniczyć dostęp do publicznego punktu końcowego konta magazynu do określonych adresów IP lub zakresów lub do sieci wirtualnej. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do co najmniej jednej sieci wirtualnej. 

Istnieją dwa podejścia do ograniczania dostępu do konta magazynu w sieci wirtualnej:
- Utwórz co najmniej jeden prywatny punkt końcowy dla konta magazynu i Ogranicz dostęp do publicznego punktu końcowego. Dzięki temu tylko ruch pochodzący z żądanych sieci wirtualnych może uzyskiwać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do co najmniej jednej sieci wirtualnej. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. W przypadku ograniczenia ruchu do konta magazynu za pośrednictwem punktu końcowego usługi nadal uzyskuje się dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

> [!NOTE]
> Udziały NFS nie mogą uzyskać dostępu do publicznego punktu końcowego konta magazynu za pośrednictwem publicznego adresu IP, ale mogą uzyskać dostęp do publicznego punktu końcowego konta magazynu przy użyciu sieci wirtualnych. Udziały NFS mogą również uzyskiwać dostęp do konta magazynu przy użyciu prywatnych punktów końcowych.

Aby dowiedzieć się więcej o konfigurowaniu zapory konta magazynu, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!IMPORTANT]
> Ta sekcja zawiera szczegółowe informacje dotyczące szyfrowania dla udziałów SMB. Aby uzyskać szczegółowe informacje dotyczące szyfrowania podczas przesyłania z udziałami NFS, zobacz [zabezpieczenia](storage-files-compare-protocols.md#security).

Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. Oznacza to, że podczas instalowania udziału plików przez protokół SMB lub uzyskiwania dostępu do niego za pośrednictwem protokołu FileREST (na przykład za pośrednictwem Azure Portal, PowerShell/interfejsu wiersza polecenia lub zestawów SDK platformy Azure), Azure Files będzie zezwalać tylko na połączenie, jeśli zostało wykonane przy użyciu protokołu SMB 3.0 + z szyfrowaniem lub HTTPS. Klienci, którzy nie obsługują protokołu SMB 3,0 ani klientów obsługujących protokół SMB 3,0, ale nie szyfrowania SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie jest włączone. Więcej informacji o tym, które systemy operacyjne obsługują protokół SMB 3,0 z szyfrowaniem, można znaleźć w naszej szczegółowej dokumentacji dotyczącej [systemów Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md). Wszystkie bieżące wersje programu PowerShell, interfejsu wiersza polecenia i zestawów SDK obsługują protokół HTTPS.  

Można wyłączyć szyfrowanie podczas przesyłania dla konta usługi Azure Storage. Gdy szyfrowanie jest wyłączone, Azure Files również zezwala na SMB 2,1, SMB 3,0 bez szyfrowania i nieszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Podstawowym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchomiona w starszym systemie operacyjnym, takim jak Windows Server 2008 R2 lub starsza dystrybucja systemu Linux. Azure Files zezwala tylko na połączenia SMB 2,1 w tym samym regionie świadczenia usługi Azure co udział plików platformy Azure; klient SMB 2,1 spoza regionu platformy Azure udziału plików platformy Azure, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Zobacz też
- [Omówienie usługi Azure Files](storage-files-introduction.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
---
title: Azure File Sync dotyczące sieci | Microsoft Docs
description: Dowiedz się, jak skonfigurować sieć do używania Azure File Sync do buforowania plików w środowisku lokalnym.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6c761edec571f404a538025c868750bc5712eced
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797304"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync dotyczące sieci
Połączenie z udziałem plików platformy Azure można nawiązać na dwa sposoby:

- Uzyskiwanie dostępu do udziału bezpośrednio za pośrednictwem protokołów SMB lub FileREST. Ten wzorzec dostępu jest stosowany głównie wtedy, gdy należy wyeliminować jak najwięcej serwerów lokalnych.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym (lub na maszynie wirtualnej platformy Azure) przy użyciu usługi Azure File Sync oraz uzyskiwanie dostępu do danych udziału plików z serwera lokalnego przy użyciu wybranego protokołu (SMB, NFS, FTPS itp.) na użytek twojego przypadku użycia. Ten wzorzec dostępu jest przydatny, ponieważ łączy najlepsze rozwiązania zarówno z wydajnością lokalną, jak i skalą chmury oraz z dołączalnymi usługami bez serwera, takimi jak Azure Backup.

W tym artykule opisano sposób konfigurowania sieci w przypadku, gdy przypadek użycia wymaga użycia usługi Azure File Sync do buforowania plików lokalnie, zamiast bezpośredniego instalowanie udziału plików platformy Azure za pośrednictwem SMB. Aby uzyskać więcej informacji o zagadnieniach dotyczących sieci Azure Files wdrażania, zobacz [Azure Files networking considerations](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

Konfiguracja sieci dla Azure File Sync obejmuje dwa różne obiekty platformy Azure: usługę synchronizacji magazynu i konto usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob lub kolejki. Usługa synchronizacji magazynu to konstrukcja zarządzania reprezentująca zarejestrowane serwery, które są serwerami plików systemu Windows z ustanowioną relacją zaufania z usługą Azure File Sync i grupami synchronizacji, które definiują topologię relacji synchronizacji. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Łączenie serwera plików z systemem Windows z platformą Azure przy użyciu Azure File Sync 
Do skonfigurowania i używania Azure Files i Azure File Sync z lokalnym serwerem plików z systemem Windows nie jest wymagana żadna specjalna sieć dla platformy Azure poza podstawowym połączeniem internetowym. Aby wdrożyć Azure File Sync, należy zainstalować agenta Azure File Sync na serwerze plików systemu Windows, który chcesz zsynchronizować z platformą Azure. Agent Azure File Sync osiąga synchronizację z udziałem plików platformy Azure za pośrednictwem dwóch kanałów:

- Protokół FileREST, który jest protokołem opartym na protokole HTTPS do uzyskiwania dostępu do udziału plików platformy Azure. Ponieważ protokół FileREST używa standardowego protokołu HTTPS do transferu danych, tylko port 443 musi być dostępny dla ruchu wychodzącego. Azure File Sync nie używa protokołu SMB do transferu danych z lokalnych serwerów z systemem Windows do udziału plików platformy Azure.
- Protokół Azure File Sync synchronizacji, który jest protokołem opartym na protokole HTTPS do wymiany wiedzy o synchronizacji, tj. informacjami o wersji plików i folderów w środowisku, między punktami końcowymi w środowisku. Ten protokół jest również używany do wymiany metadanych dotyczących plików i folderów w środowisku, takich jak znaczniki czasu i listy kontroli dostępu (ACL). 

Ponieważ usługa Azure Files oferuje bezpośredni dostęp do protokołu SMB w udziałach plików platformy Azure, klienci często zastanawiają się, czy muszą skonfigurować specjalną sieć w celu instalacji udziałów plików platformy Azure przy użyciu protokołu SMB, aby uzyskać dostęp agentowi usługi Azure File Sync. Nie jest to wymagane, ale jest również zalecane, z wyjątkiem scenariuszy dla administratorów, ze względu na brak szybkiego wykrywania zmian w przypadku zmian wprowadzonych bezpośrednio w udziałach plików platformy Azure (zmiany mogą nie być wykrywane przez więcej niż 24 godziny w zależności od rozmiaru i liczby elementów w udziałach plików platformy Azure). Jeśli chcesz bezpośrednio korzystać z udziału plików platformy Azure, tzn. nie używać usługi Azure File Sync do buforowania w środowisku lokalnym, możesz dowiedzieć się więcej o zagadnieniach dotyczących sieci w przypadku bezpośredniego dostępu, konsultacja z Azure Files [omówieniem sieci.](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

Mimo Azure File Sync nie wymaga żadnej specjalnej konfiguracji sieci, niektórzy klienci mogą chcieć skonfigurować zaawansowane ustawienia sieci, aby włączyć następujące scenariusze:

- Współdziałanie z konfiguracją serwera proxy organizacji.
- Otwórz lokalną zaporę organizacji dla usługi Azure Files i Azure File Sync usług.
- Tunelowanie Azure Files i Azure File Sync za pośrednictwem usługi ExpressRoute lub połączenia sieci VPN.

### <a name="configuring-proxy-servers"></a>Konfigurowanie serwerów proxy
Wiele organizacji używa serwera proxy jako pośrednika między zasobami w sieci lokalnej i zasobami poza siecią, na przykład na platformie Azure. Serwery proxy są przydatne w przypadku wielu aplikacji, takich jak izolacja sieci i zabezpieczenia oraz monitorowanie i rejestrowanie. Azure File Sync może w pełni współdziałać z serwerem proxy, należy jednak ręcznie skonfigurować ustawienia punktu końcowego serwera proxy dla środowiska przy użyciu Azure File Sync. Należy to zrobić za pośrednictwem programu PowerShell przy użyciu Azure File Sync cmdlet serwera. 

Aby uzyskać więcej informacji na temat konfigurowania usługi Azure File Sync serwerem proxy, zobacz Konfigurowanie Azure File Sync [przy użyciu serwera proxy.](file-sync-firewall-and-proxy.md)

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurowanie zapór i tagów usługi
Ze względów bezpieczeństwa serwery plików można odizolować od większości lokalizacji internetowych. Aby używać Azure File Sync w środowisku, musisz dostosować zaporę, aby otworzyć ją, aby wybrać usługi platformy Azure. W tym celu można uzyskać zakresy adresów IP dla wymaganych usług za pomocą mechanizmu nazywanego [tagami usług](../../virtual-network/service-tags-overview.md).

Azure File Sync zakresów adresów IP dla następujących usług określonych za pomocą tagów usług:

| Usługa | Opis | Tag usługi |
|---------|-------------|-------------|
| Azure File Sync | Usługa Azure File Sync, reprezentowana przez obiekt usługi synchronizacji magazynu, jest odpowiedzialna za podstawowe działanie synchronizacji danych między udziałem plików platformy Azure i serwerem plików systemu Windows. | `StorageSyncService` |
| Azure Files | Wszystkie dane synchronizowane za pośrednictwem Azure File Sync są przechowywane w udziałach plików platformy Azure. Pliki zmienione na serwerach plików z systemem Windows są replikowane do udziału plików platformy Azure, a pliki warstwowe na lokalnym serwerze plików są bezproblemowo pobierane, gdy użytkownik zażąda ich. | `Storage` |
| Azure Resource Manager | Interfejs Azure Resource Manager to interfejs zarządzania dla platformy Azure. Wszystkie wywołania zarządzania, w Azure File Sync rejestracji serwera i bieżące zadania serwera synchronizacji, są wykonywane za pośrednictwem Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory (azure AD) zawiera podmioty zabezpieczeń użytkownika wymagane do autoryzowania rejestracji serwera w usłudze synchronizacji magazynu oraz jednostki usługi wymagane do autoryzacji usługi Azure File Sync do uzyskiwania dostępu do zasobów w chmurze. | `AzureActiveDirectory` |

Jeśli używasz usługi Azure File Sync na platformie Azure, nawet jeśli jest to inny region, możesz użyć nazwy tagu usługi bezpośrednio w sieciowej grupie zabezpieczeń, aby zezwolić na ruch do tej usługi. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Sieciowe grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md). 

Jeśli używasz lokalnego Azure File Sync, możesz użyć interfejsu API tagu usługi, aby uzyskać określone zakresy adresów IP dla listy zezwalań zapory. Istnieją dwie metody uzyskiwania tych informacji:

- Bieżąca lista zakresów adresów IP dla wszystkich usług platformy Azure, które obsługują tagi usług, jest publikowana co tydzień w Centrum pobierania Microsoft w postaci dokumentu JSON. Każda chmura platformy Azure ma własny dokument JSON z zakresami adresów IP odpowiednimi dla tej chmury:
    - [Publiczna platforma Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Chiny na platformie Azure](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
- Interfejs API odnajdywania tagów usług (wersja zapoznawcza) umożliwia programowe pobieranie bieżącej listy tagów usługi. W wersji zapoznawczej interfejs API odnajdywania tagów usług może zwracać informacje, które są mniej aktualne niż informacje zwrócone z dokumentów JSON opublikowanych w Centrum pobierania Microsoft. Powierzchni interfejsu API można używać zgodnie z preferencjami automatyzacji:
    - [Interfejs API REST](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfejs wiersza polecenia platformy Azure](/cli/azure/network#az_network_list_service_tags)

Aby dowiedzieć się więcej na temat używania interfejsu API tagu usługi do pobierania adresów usług, zobacz Lista zezwalań dla Azure File Sync [adresów IP.](file-sync-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelowanie ruchu przez wirtualną sieć prywatną lub usługę ExpressRoute
Niektóre organizacje wymagają komunikacji z platformą Azure w celu zapewnienia tunelu sieciowego, takiego jak wirtualna prywatna sieć prywatna (VPN) lub usługa ExpressRoute, w celu zapewnienia dodatkowej warstwy zabezpieczeń lub zapewnienia, że komunikacja z platformą Azure jest zgodna z deterministyczną trasą. 

Podczas ustanawiania tunelu sieciowego między siecią lokalną a platformą Azure komunikacja równorzędna sieci lokalnej jest nawiązywana za pomocą co najmniej jednej sieci wirtualnej na platformie Azure. Sieć [wirtualna](../../virtual-network/virtual-networks-overview.md)lub sieć wirtualna jest podobna do tradycyjnej sieci, która działa lokalnie. Podobnie jak w przypadku konta usługi Azure Storage lub maszyny wirtualnej platformy Azure, sieć wirtualna jest zasobem platformy Azure wdrożonym w grupie zasobów. 

Azure Files i File Sync obsługują następujące mechanizmy tunelowania ruchu między serwerami lokalnymi i platformą Azure:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)brama sieci VPN jest określonym typem bramy sieci wirtualnej, która jest używana do wysyłania zaszyfrowanego ruchu sieciowego między siecią wirtualną platformy Azure a lokalizacją alternatywną (taką jak lokalna) przez Internet. Usługa Azure VPN Gateway to zasób platformy Azure, który można wdrożyć w grupie zasobów obok konta magazynu lub innych zasobów platformy Azure. Ponieważ usługa Azure File Sync jest przeznaczona do użycia z lokalnym serwerem plików systemu Windows, zwykle używa się sieci VPN typu [lokacja-lokacja (S2S),](../../vpn-gateway/design.md#s2smulti)chociaż technicznie jest możliwe użycie sieci VPN typu [punkt-lokacja (P2S).](../../vpn-gateway/point-to-site-about.md) 

    Połączenia sieci VPN typu lokacja-lokacja (S2S) łączą sieć wirtualną platformy Azure i sieć lokalną organizacji. Połączenie sieci VPN typu lokacja-lokacja umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanych w sieci organizacji, a nie dla każdego urządzenia klienckiego, które musi uzyskać dostęp do udziału plików platformy Azure. Aby uprościć wdrażanie połączenia sieci VPN typu lokacja-lokacja, zobacz [Configure a Site-to-Site (S2S) VPN for use with Azure Files](../files/storage-files-configure-s2s-vpn.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

- [Usługa ExpressRoute](../../expressroute/expressroute-introduction.md), która umożliwia utworzenie zdefiniowanej trasy między platformą Azure i siecią lokalną, która nie przechodzi przez Internet. Ponieważ usługa ExpressRoute zapewnia dedykowaną ścieżkę między lokalnym centrum danych i platformą Azure, usługa ExpressRoute może być przydatna, gdy należy wziąć pod uwagę wydajność sieci. Usługa ExpressRoute jest również dobrą opcją, gdy wymagania prawne lub zasady organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

### <a name="private-endpoints"></a>Prywatne punkty końcowe
Oprócz domyślnych publicznych punktów końcowych usług Azure Files i File Sync, które są dostępne za pośrednictwem konta magazynu i usługi synchronizacji magazynu, usługi Azure Files i File Sync oferują możliwość użycia co najmniej jednego prywatnego punktu końcowego na zasób. Po utworzeniu prywatnego punktu końcowego dla zasobu platformy Azure uzyskuje on prywatny adres IP z przestrzeni adresowej sieci wirtualnej, podobnie jak w przypadku lokalnego serwera plików z systemem Windows, który ma adres IP w dedykowanej przestrzeni adresowej sieci lokalnej. 

> [!Important]  
> Aby można było używać prywatnych punktów końcowych w zasobie usługi synchronizacji magazynu, należy użyć agenta Azure File Sync w wersji 10.1 lub większej. Wersje agenta wcześniejsze niż 10.1 nie obsługują prywatnych punktów końcowych w usłudze synchronizacji magazynu. Wszystkie wcześniejsze wersje agenta obsługują prywatne punkty końcowe w zasobie konta magazynu.

Pojedynczy prywatny punkt końcowy jest skojarzony z określoną podsiecią sieci wirtualnej platformy Azure. Konta magazynu i usługi synchronizacji magazynu mogą mieć prywatne punkty końcowe w więcej niż jednej sieci wirtualnej.

Korzystanie z prywatnych punktów końcowych umożliwia:
- Bezpiecznie łącz się z zasobami platformy Azure z sieci lokalnych przy użyciu sieci VPN lub połączenia usługi ExpressRoute z prywatną komunikacja równorzędną.
- Zabezpiecz zasoby platformy Azure, wyłączając publiczne punkty końcowe dla Azure Files i File Sync. Domyślnie utworzenie prywatnego punktu końcowego nie blokuje połączeń z publicznym punktem końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej (i granic komunikacji równorzędnej).

Aby utworzyć prywatny punkt końcowy, zobacz [Konfigurowanie prywatnych punktów końcowych dla Azure File Sync](file-sync-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Prywatne punkty końcowe i system DNS
Podczas tworzenia prywatnego punktu końcowego domyślnie tworzymy również (lub aktualizujemy istniejącą) prywatną strefę DNS odpowiadającą `privatelink` poddomenie. W przypadku regionów chmury publicznej te strefy DNS są Azure Files `privatelink.file.core.windows.net` i `privatelink.afs.azure.net` dla Azure File Sync.

> [!Note]  
> W tym artykule jest używany sufiks DNS konta magazynu dla regionów publicznych platformy Azure, `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura azure dla instytucji rządowych USA i chmura Azure — Chiny — wystarczy zastąpić odpowiednie sufiksy dla swojego środowiska. 

Gdy tworzysz prywatne punkty końcowe dla konta magazynu i usługi synchronizacji magazynu, tworzymy dla nich rekordy A w odpowiednich prywatnych strefach DNS. Aktualizujemy również publiczny wpis DNS w taki sposób, aby zwykłe w pełni kwalifikowane nazwy domen to rekordy CNA dla odpowiedniej nazwy łącza prywatnego. Dzięki temu w pełni kwalifikowane nazwy domen mogą wskazać adresy IP prywatnego punktu końcowego, gdy żąda on znajduje się w sieci wirtualnej, i wskazać publiczne adresy IP punktu końcowego, gdy żądający znajduje się poza siecią wirtualną. 

Na Azure Files każdy prywatny punkt końcowy ma jedną w pełni kwalifikowaną nazwę domeny, zgodnie ze wzorcem , zamapowany na jeden prywatny adres `storageaccount.privatelink.file.core.windows.net` IP prywatnego punktu końcowego. Na Azure File Sync każdy prywatny punkt końcowy ma cztery w pełni kwalifikowane nazwy domen dla czterech różnych punktów końcowych, które Azure File Sync uwidacznia: zarządzanie, synchronizacja (podstawowa), synchronizacja (pomocnicza) i monitorowanie. W pełni kwalifikowane nazwy domen dla tych punktów końcowych zwykle będą zgodne z nazwą usługi synchronizacji magazynu, chyba że nazwa zawiera znaki inne niż ASCII. Jeśli na przykład nazwa usługi synchronizacji magazynu znajduje się w regionie Zachodnie stany USA 2, równoważne punkty `mysyncservice` końcowe to , , i `mysyncservicemanagement.westus2.afs.azure.net` `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` `mysyncservicemonitoring.westus2.afs.azure.net` . Każdy prywatny punkt końcowy usługi synchronizacji magazynu będzie zawierać 4 różne adresy IP. 

Ponieważ prywatna strefa DNS platformy Azure jest połączona z siecią wirtualną zawierającą prywatny punkt końcowy, konfigurację DNS można obserwować podczas wywoływania polecenia cmdlet z programu PowerShell na maszynie wirtualnej platformy Azure (alternatywnie w systemach Windows i `Resolve-DnsName` `nslookup` Linux):

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

Jeśli to samo polecenie zostanie uruchomione lokalnie, zobaczysz, że ta sama nazwa konta magazynu jest rozpoznawczy jako publiczny adres IP konta magazynu. jest rekordem CNAME dla , który z kolei jest rekordem CNAME dla klastra `storageaccount.file.core.windows.net` `storageaccount.privatelink.file.core.windows.net` usługi Azure Storage hostującym konto magazynu:

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

Wynika to z faktu, że Azure Files i Azure File Sync mogą uwidaczniać zarówno ich publiczne punkty końcowe, jak i co najmniej jeden prywatny punkt końcowy na zasób. Aby zapewnić rozpoznawanie w pełni kwalifikowanych nazw domen dla zasobów jako prywatnych adresów IP prywatnych punktów końcowych, należy zmienić konfigurację lokalnych serwerów DNS. Można to zrobić na kilka sposobów:

- Modyfikowanie pliku hosts na klientach w celu rozpoznawania w pełni kwalifikowanych nazw domen dla kont magazynu i usług synchronizacji magazynu do żądanych prywatnych adresów IP. Jest to zdecydowanie odradzane w środowiskach produkcyjnych, ponieważ należy wprowadzić te zmiany w każdym kliencie, który musi uzyskać dostęp do prywatnych punktów końcowych. Zmiany prywatnych punktów końcowych/zasobów (usunięcia, modyfikacje itp.) nie będą obsługiwane automatycznie.
- Tworzenie stref DNS na serwerach lokalnych dla i `privatelink.file.core.windows.net` `privatelink.afs.azure.net` z rekordami A dla zasobów platformy Azure. Ma to tę zaletę, że klienci w środowisku lokalnym będą mogli automatycznie rozwiązywać zasoby platformy Azure bez konieczności konfigurowania poszczególnych klientów, jednak to rozwiązanie jest podobne do modyfikowania pliku hosts, ponieważ zmiany nie są odzwierciedlane. Chociaż to rozwiązanie jest brittle, może być najlepszym wyborem w niektórych środowiskach.
- Przekazywanie stref i z lokalnych serwerów DNS do prywatnej strefy `core.windows.net` `afs.azure.net` DNS platformy Azure. Prywatny host DNS platformy Azure jest dostępny za pośrednictwem specjalnego adresu IP ( ), który jest dostępny tylko wewnątrz sieci wirtualnych połączonych z prywatną strefą `168.63.129.16` DNS platformy Azure. Aby obejść to ograniczenie, możesz uruchomić dodatkowe serwery DNS w sieci wirtualnej, które będą przesyłać dalej i na równoważne prywatne `core.windows.net` `afs.azure.net` strefy DNS platformy Azure. Aby uprościć tę konfigurację, oferujemy polecenia cmdlet programu PowerShell, które będą automatycznie wdrażać serwery DNS w sieci wirtualnej platformy Azure i konfigurować je zgodnie z potrzebami. Aby dowiedzieć się, jak skonfigurować przekazywanie DNS, zobacz [Konfigurowanie usługi DNS przy użyciu Azure Files](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Połączenia wykonane z agenta Azure File Sync do udziału plików platformy Azure lub usługi synchronizacji magazynu są zawsze szyfrowane. Mimo że konta usługi Azure Storage mają ustawienie wyłączające wymaganie szyfrowania podczas przesyłania do usługi Azure Files (i innych usług magazynu platformy Azure zarządzanych poza kontem magazynu), wyłączenie tego ustawienia nie ma wpływu na szyfrowanie usługi Azure File Sync podczas komunikacji z usługą Azure Files. Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. 

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Wdrażanie funkcji Azure File Sync](file-sync-deployment-guide.md)
---
title: Zagadnienia dotyczące sieci Azure File Sync | Microsoft Docs
description: Dowiedz się, jak skonfigurować sieć do korzystania z Azure File Sync w celu buforowania plików w środowisku lokalnym.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629363"
---
# <a name="azure-file-sync-networking-considerations"></a>Zagadnienia dotyczące sieci Azure File Sync
Możesz połączyć się z udziałem plików platformy Azure na dwa sposoby:

- Uzyskiwanie dostępu do udziału bezpośrednio za pośrednictwem protokołów SMB lub FileREST. Ten wzorzec dostępu jest używany przede wszystkim wtedy, gdy można wyeliminować dowolną liczbę serwerów lokalnych.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym (lub na maszynie wirtualnej platformy Azure) przy użyciu Azure File Sync i uzyskiwanie dostępu do danych udziału plików z serwera lokalnego przy użyciu wybranego przez Ciebie protokołu (SMB, NFS, FTPS itp.). Ten wzorzec dostępu jest przydatny, ponieważ łączy się z najlepszą z nich zarówno w przypadku lokalnego, jak i skalowalnych usług, takich jak Azure Backup.

Ten artykuł koncentruje się na sposobie konfigurowania sieci dla programu, gdy przypadek użycia wywołuje Azure File Sync do lokalnego umieszczania plików w pamięci podręcznej, a nie bezpośrednio instalując udział plików platformy Azure przy użyciu protokołu SMB. Aby uzyskać więcej informacji dotyczących zagadnień sieciowych dotyczących wdrażania Azure Files, zobacz [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md).

Konfiguracja sieci dla Azure File Sync obejmuje dwa różne obiekty platformy Azure: usługi synchronizacji magazynu i konta usługi Azure Storage. Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Usługa synchronizacji magazynu to konstrukcja zarządzania reprezentująca zarejestrowane serwery, które są serwerami plików systemu Windows z ustanowioną relacją zaufania z Azure File Sync i grupami synchronizacji, które definiują topologię relacji synchronizacji. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Łączenie serwera plików systemu Windows z platformą Azure za pomocą Azure File Sync 
Aby skonfigurować i używać Azure Files i Azure File Sync z lokalnym serwerem plików systemu Windows, nie jest wymagana żadna specjalna sieć na platformę Azure poza podstawowym połączeniem internetowym. Aby wdrożyć Azure File Sync, Zainstaluj agenta Azure File Sync na serwerze plików z systemem Windows, który chcesz zsynchronizować z platformą Azure. Agent Azure File Sync uzyskuje synchronizację z udziałem plików platformy Azure za pośrednictwem dwóch kanałów:

- Protokół FileREST, który jest protokołem opartym na protokole HTTPS do uzyskiwania dostępu do udziału plików platformy Azure. Ponieważ protokół FileREST używa standardowego protokołu HTTPS do transferu danych, tylko port 443 musi być dostępny na wyjściu. Azure File Sync nie używa protokołu SMB do transferowania danych z lokalnych serwerów z systemem Windows do udziału plików platformy Azure.
- Protokół synchronizacji Azure File Sync, który jest protokołem opartym na protokole HTTPS do wymiany wiedzy o synchronizacji, tj. Informacje o wersji dotyczące plików i folderów w danym środowisku, między punktami końcowymi w środowisku. Ten protokół jest również używany do wymiany metadanych dotyczących plików i folderów w środowisku, takich jak sygnatury czasowe i listy kontroli dostępu (ACL). 

Ponieważ Azure Files oferuje bezpośredni dostęp do protokołu SMB w udziałach plików platformy Azure, klienci często zastanawiają się, jeśli potrzebują skonfigurować specjalne sieci do instalowania udziałów plików platformy Azure przy użyciu protokołu SMB dla agenta Azure File Sync do uzyskania dostępu. Nie tylko jest to wymagane, ale również jest niezalecane, z wyjątkiem sytuacji administratorów, z powodu braku szybkiego wykrywania zmian wprowadzonych bezpośrednio do udziału plików platformy Azure (zmiany mogą nie być odnajdywane przez więcej niż 24 godziny w zależności od rozmiaru i liczby elementów w udziale plików platformy Azure). Jeśli chcesz bezpośrednio korzystać z udziału plików platformy Azure, czyli nie używać Azure File Sync do buforowania lokalnego, możesz dowiedzieć się więcej na temat zagadnień dotyczących sieci w przypadku bezpośredniego dostępu przy użyciu doradcy [Azure Files Omówienie sieci](storage-files-networking-overview.md).

Chociaż Azure File Sync nie wymaga żadnej specjalnej konfiguracji sieci, niektórzy klienci mogą chcieć skonfigurować zaawansowane ustawienia sieciowe, aby umożliwić korzystanie z następujących scenariuszy:

- Współdziałanie z konfiguracją serwera proxy w organizacji.
- Otwórz Zaporę lokalną organizacji w usługach Azure Files i Azure File Sync.
- Tunelowanie Azure Files i Azure File Sync za pośrednictwem ExpressRoute lub połączenia sieci VPN.

### <a name="configuring-proxy-servers"></a>Konfigurowanie serwerów proxy
Wiele organizacji używa serwera proxy jako pośrednika między zasobami w sieci lokalnej i zasobami spoza sieci, na przykład na platformie Azure. Serwery proxy są przydatne w przypadku wielu aplikacji, takich jak izolacja sieci i zabezpieczenia oraz monitorowanie i rejestrowanie. Azure File Sync może współdziałać w pełni z serwerem proxy, jednak należy ręcznie skonfigurować ustawienia punktu końcowego serwera proxy dla danego środowiska przy użyciu Azure File Sync. Należy to zrobić za pomocą programu PowerShell przy użyciu poleceń cmdlet serwera Azure File Sync. 

Aby uzyskać więcej informacji na temat konfigurowania Azure File Sync z serwerem proxy, zobacz [konfigurowanie Azure File Sync z serwerem proxy](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurowanie zapór i tagów usług
Serwery plików można izolować z większości lokalizacji internetowych ze względów bezpieczeństwa. Aby użyć Azure File Sync w Twoim środowisku, musisz dostosować zaporę, aby otworzyć ją w celu wybrania usług platformy Azure. Można to zrobić, pobierając zakresy adresów IP dla usług, które są wymagane za pomocą mechanizmu o nazwie [Tagi usług](../../virtual-network/service-tags-overview.md).

Azure File Sync wymaga zakresów adresów IP dla następujących usług, identyfikowanych przez ich znaczniki usługi:

| Usługa | Opis | Tag usługi |
|---------|-------------|-------------|
| Azure File Sync | Usługa Azure File Sync, reprezentowana przez obiekt usługi synchronizacji magazynu, jest odpowiedzialna za podstawową aktywność synchronizacji danych między udziałem plików platformy Azure a serwerem plików systemu Windows. | `StorageSyncService` |
| Azure Files | Wszystkie dane zsynchronizowane za pośrednictwem Azure File Sync są przechowywane w udziale plików platformy Azure. Pliki zmienione na serwerach plików systemu Windows są replikowane do udziału plików platformy Azure, a pliki warstwowe na lokalnym serwerze plików są bezproblemowo pobierane podczas żądania przez użytkownika. | `Storage` |
| Azure Resource Manager | Azure Resource Manager jest interfejsem zarządzania dla platformy Azure. Wszystkie wywołania zarządzania, w tym Azure File Sync rejestracji serwera i trwającego serwera synchronizacji, są tworzone przez Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory lub usługa Azure AD zawiera podmioty zabezpieczeń wymagane do autoryzacji rejestracji serwera względem usługi synchronizacji magazynu oraz jednostki usługi wymagane do Azure File Sync mają autoryzację w celu uzyskania dostępu do zasobów w chmurze. | `AzureActiveDirectory` |

Jeśli używasz Azure File Sync w ramach platformy Azure, nawet jeśli jest to inny region, możesz użyć nazwy znacznika usługi bezpośrednio w sieciowej grupie zabezpieczeń, aby zezwolić na ruch do tej usługi. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Network Security Groups](../../virtual-network/network-security-groups-overview.md). 

Jeśli używasz Azure File Sync lokalnie, możesz użyć interfejsu API znacznika usługi, aby uzyskać określone zakresy adresów IP dla listy dozwolonych dla zapory. Istnieją dwie metody uzyskiwania tych informacji:

- Bieżąca lista zakresów adresów IP dla wszystkich usług platformy Azure obsługujących znaczniki usług jest publikowana co tydzień w centrum pobierania Microsoft w formie dokumentu JSON. Każda Chmura platformy Azure ma własny dokument JSON z zakresami adresów IP istotnymi dla tej chmury:
    - [Usługa Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Chiny platformy Azure](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
- Interfejs API odnajdowania tagów usługi (wersja zapoznawcza) umożliwia programistyczne pobieranie bieżącej listy tagów usługi. W wersji zapoznawczej interfejs API odnajdowania tagów usługi może zwracać informacje, które są mniej aktualne niż zwracane przez informacje z dokumentów JSON opublikowanych w centrum pobierania Microsoft. Możesz użyć powierzchni interfejsu API na podstawie preferencji automatyzacji:
    - [Interfejs API REST](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfejs wiersza polecenia platformy Azure](/cli/azure/network#az-network-list-service-tags)

Aby dowiedzieć się więcej o tym, jak używać interfejsu API tagów usługi do pobierania adresów usług, zobacz [Lista dozwolonych adresów IP Azure File Sync](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelowanie ruchu przez wirtualną sieć prywatną lub ExpressRoute
Niektóre organizacje wymagają komunikacji z platformą Azure, aby przejść przez tunel sieciowy, taki jak wirtualna prywatna sieć prywatna (VPN) lub ExpressRoute, w celu uzyskania dodatkowej warstwy zabezpieczeń lub zapewnienia komunikacji z platformą Azure z użyciem deterministycznej trasy. 

Po ustanowieniu tunelu sieciowego między siecią lokalną i platformą Azure jest to Komunikacja równorzędna sieci lokalnej z co najmniej jedną siecią wirtualną na platformie Azure. [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md)lub wirtualna jest podobna do tradycyjnej sieci, która działa lokalnie. Podobnie jak konto usługi Azure Storage lub maszyna wirtualna platformy Azure, Sieć wirtualna jest zasobem platformy Azure wdrożonym w grupie zasobów. 

Azure Files i File Sync obsługują następujące mechanizmy do tunelowania ruchu między serwerami lokalnymi i platformą Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brama sieci VPN jest określonym typem bramy usługi Virtual Network, która jest używana do wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a alternatywną lokalizacją (na przykład lokalnie) przez Internet. VPN Gateway platformy Azure to zasób platformy Azure, który można wdrożyć w grupie zasobów obok konta magazynu lub innych zasobów platformy Azure. Ponieważ Azure File Sync jest przeznaczony do użycia z lokalnym serwerem plików systemu Windows, zazwyczaj należy używać [sieci VPN typu lokacja-lokacja (S2S)](../../vpn-gateway/design.md#s2smulti), chociaż jest technicznie możliwe użycie [sieci VPN typu punkt-lokacja (P2S)](../../vpn-gateway/point-to-site-about.md). 

    Połączenia sieci VPN typu lokacja-lokacja (S2S) łączą się z siecią wirtualną platformy Azure i siecią lokalną w organizacji. Połączenie sieci VPN S2S umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanego w sieci organizacji, a nie dla każdego urządzenia klienckiego, które wymaga dostępu do udziału plików platformy Azure. Aby uprościć wdrażanie połączenia sieci VPN S2S, zobacz [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), który umożliwia utworzenie zdefiniowanej trasy między platformą Azure i siecią lokalną, która nie przechodzi przez Internet. Ponieważ ExpressRoute zapewnia dedykowaną ścieżkę między lokalnym centrum danych a platformą Azure, ExpressRoute może być przydatne, gdy wydajność sieci jest uwzględniana. ExpressRoute jest również dobrym rozwiązaniem, gdy zasady lub wymagania prawne organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

### <a name="private-endpoints"></a>Prywatne punkty końcowe
Poza domyślnymi publicznymi punktami końcowymi Azure Files i File Sync zapewniania za pomocą konta magazynu i usługi synchronizacji magazynu, Azure Files i File Sync zapewnia opcję posiadania co najmniej jednego prywatnego punktu końcowego na zasób. Po utworzeniu prywatnego punktu końcowego dla zasobu platformy Azure jest on pobiera prywatny adres IP z przestrzeni adresowej sieci wirtualnej, podobnie jak lokalny serwer plików systemu Windows ma adres IP w ramach dedykowanej przestrzeni adresowej sieci lokalnej. 

> [!Important]  
> Aby można było używać prywatnych punktów końcowych w zasobie usługi synchronizacji magazynu, należy użyć agenta Azure File Sync w wersji 10,1 lub nowszej. Wersje agenta wcześniejsze niż 10,1 nie obsługują prywatnych punktów końcowych w usłudze synchronizacji magazynu. Wszystkie wcześniejsze wersje agenta obsługują prywatne punkty końcowe w ramach zasobu konta magazynu.

Pojedynczy prywatny punkt końcowy jest skojarzony z określoną podsiecią sieci wirtualnej platformy Azure. Konta magazynu i usługi synchronizacji magazynu mogą mieć prywatne punkty końcowe w więcej niż jednej sieci wirtualnej.

Używanie prywatnych punktów końcowych umożliwia:
- Bezpiecznie łącz się z zasobami platformy Azure z sieci lokalnych za pomocą sieci VPN lub połączenia ExpressRoute z prywatną komunikację równorzędną.
- Zabezpiecz swoje zasoby platformy Azure, wyłączając publiczne punkty końcowe dla Azure Files i File Sync. Domyślnie tworzenie prywatnego punktu końcowego nie blokuje połączeń z publicznym punktem końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej (i granic komunikacji równorzędnej).

Aby utworzyć prywatny punkt końcowy, zobacz [Konfigurowanie prywatnych punktów końcowych dla Azure File Sync](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Prywatne punkty końcowe i system DNS
Gdy tworzysz prywatny punkt końcowy, domyślnie utworzymy również (lub zaktualizuj istniejącą) prywatną strefę DNS odpowiadającą `privatelink` poddomenie. W przypadku regionów chmury publicznej te strefy DNS są `privatelink.file.core.windows.net` przeznaczone dla Azure Files i `privatelink.afs.azure.net` Azure File Sync.

> [!Note]  
> W tym artykule jest stosowany sufiks DNS konta magazynu dla publicznych regionów platformy Azure `core.windows.net` . Ten komentarz dotyczy również suwerennych chmur platformy Azure, takich jak chmura dla instytucji rządowych w Stanach Zjednoczonych i Azure (Chiny) — po prostu zastępuje odpowiednie sufiksy dla danego środowiska. 

Gdy tworzysz prywatne punkty końcowe dla konta magazynu i usługi synchronizacji magazynu, utworzymy rekordy dla nich w odpowiednich prywatnych strefach DNS. Aktualizujemy również publiczny wpis DNS w taki sposób, że regularne w pełni kwalifikowane nazwy domen są CNAME dla odpowiedniej nazwy privatelink. Dzięki temu w pełni kwalifikowane nazwy domen mogą wskazywać adresy IP prywatnych punktów końcowych, gdy obiekt żądający znajduje się w sieci wirtualnej i aby wskazywał adresy IP publicznego punktu końcowego, gdy obiekt żądający znajduje się poza siecią wirtualną. 

W przypadku Azure Files każdy prywatny punkt końcowy ma jedną w pełni kwalifikowaną nazwę domeny, zgodnie z wzorcem `storageaccount.privatelink.file.core.windows.net` , zamapowane na jeden prywatny adres IP dla prywatnego punktu końcowego. W przypadku Azure File Sync każdy prywatny punkt końcowy ma cztery w pełni kwalifikowane nazwy domeny dla czterech różnych punktów końcowych, które Azure File Sync uwidacznia: zarządzanie, synchronizacja (podstawowa), synchronizacja (pomocnicza) i monitorowanie. W pełni kwalifikowane nazwy domen dla tych punktów końcowych zwykle są zgodne z nazwą usługi synchronizacji magazynu, chyba że nazwa zawiera znaki inne niż ASCII. Jeśli na przykład nazwa usługi synchronizacji magazynu znajduje się `mysyncservice` w regionie zachodnie stany USA 2, równoważne punkty końcowe byłyby `mysyncservicemanagement.westus2.afs.azure.net` , `mysyncservicesyncp.westus2.afs.azure.net` , `mysyncservicesyncs.westus2.afs.azure.net` , i `mysyncservicemonitoring.westus2.afs.azure.net` . Każdy prywatny punkt końcowy usługi synchronizacji magazynu będzie zawierać 4 odrębne adresy IP. 

Ze względu na to, że prywatna strefa DNS platformy Azure jest połączona z siecią wirtualną zawierającą prywatny punkt końcowy, można obserwować konfigurację DNS, wywołując `Resolve-DnsName` polecenie cmdlet z programu PowerShell na maszynie wirtualnej platformy Azure (Alternatywnie `nslookup` w systemach Windows i Linux):

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

Odzwierciedla to fakt, że Azure Files i Azure File Sync mogą uwidaczniać zarówno publiczne punkty końcowe, jak i co najmniej jeden prywatny punkt końcowy na zasób. Aby upewnić się, że w pełni kwalifikowane nazwy domen dla zasobów są rozpoznawane jako prywatne adresy IP prywatnych punktów końcowych, należy zmienić konfigurację na lokalnych serwerach DNS. Można to zrobić na kilka sposobów:

- Modyfikowanie pliku Hosts na klientach w celu wprowadzenia w pełni kwalifikowanych nazw domen dla kont magazynu i usług synchronizacji magazynu do żądanych prywatnych adresów IP. Jest to zdecydowanie odradzane w środowiskach produkcyjnych, ponieważ konieczne będzie wprowadzenie tych zmian do każdego klienta, który musi uzyskać dostęp do prywatnych punktów końcowych. Zmiany prywatnych punktów końcowych/zasobów (usunięć, modyfikacji itp.) nie będą automatycznie obsługiwane.
- Tworzenie stref DNS na serwerach lokalnych dla `privatelink.file.core.windows.net` i `privatelink.afs.azure.net` przy użyciu rekordów zasobów platformy Azure. Dzięki temu klienci w środowisku lokalnym będą mogli automatycznie rozwiązywać zasoby platformy Azure bez konieczności konfigurowania poszczególnych klientów, jednak to rozwiązanie będzie podobne kruchy do modyfikowania pliku hosts, ponieważ zmiany nie są uwzględniane. Chociaż to rozwiązanie jest kruchy, najlepszym rozwiązaniem może być w niektórych środowiskach.
- Prześlij dalej `core.windows.net` `afs.azure.net` strefy i z lokalnych serwerów DNS do prywatnej strefy DNS platformy Azure. Prywatny Host DNS platformy Azure można uzyskać za pomocą specjalnego adresu IP ( `168.63.129.16` ), który jest dostępny tylko w sieciach wirtualnych połączonych z prywatną strefą DNS platformy Azure. Aby obejść to ograniczenie, można uruchomić dodatkowe serwery DNS w sieci wirtualnej, które będą przekazywać `core.windows.net` i `afs.azure.net` włączać do równorzędnych prywatnych stref DNS platformy Azure. Aby uprościć tę konfigurację, udostępniono polecenia cmdlet programu PowerShell, które będą automatycznie wdrażać serwery DNS w sieci wirtualnej platformy Azure i konfigurować je zgodnie z potrzebami. Aby dowiedzieć się, jak skonfigurować przekazywanie DNS, zobacz [Konfigurowanie systemu DNS przy użyciu Azure Files](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Połączenia wykonane z agenta Azure File Sync do udziału plików platformy Azure lub usługi synchronizacji magazynu są zawsze szyfrowane. Mimo że konta usługi Azure Storage mają ustawienie, aby wyłączyć Wymaganie szyfrowania podczas przesyłania komunikacji do Azure Files (i innych usług magazynu platformy Azure, które są zarządzane poza kontem magazynu), wyłączenie tego ustawienia nie będzie miało wpływu na szyfrowanie Azure File Sync podczas komunikowania się z Azure Filesem. Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. 

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie funkcji Azure File Sync](storage-sync-files-deployment-guide.md)
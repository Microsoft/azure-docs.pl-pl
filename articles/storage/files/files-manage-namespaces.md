---
title: Jak używać systemu plików DFS-N z Azure Files
description: Typowe przypadki użycia systemu plików DFS-N z Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741991"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Jak używać przestrzeni nazw systemu plików DFS z Azure Files
[Rozproszone przestrzenie](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)nazw systemów plików , powszechnie nazywane przestrzeniami nazw systemu plików DFS lub DFS-N, to rola serwera systemu Windows Server, która jest powszechnie używana do upraszczania wdrażania i obsługi udziałów plików SMB w środowisku produkcyjnym. Przestrzenie nazw systemu plików DFS to technologia wirtualizacji przestrzeni nazw magazynu, co oznacza, że umożliwia zapewnienie warstwy pośredniej między ścieżką UNC udziałów plików a rzeczywistymi udziałami plików. Przestrzenie nazw systemu plików DFS współpracuje z udziałami plików SMB, niezależne od tych udziałów plików są hostowane: mogą być używane z udziałami SMB hostowanych na lokalnym serwerze plików systemu Azure File Sync Windows z bezpośrednio lub bez udziału plików platformy Azure, udziałami plików SMB hostowanych w usługach Azure NetApp Files lub innych firm, a nawet z udziałami plików hostowanych w innych chmurach. 

Usługi Przestrzenie nazw systemu plików DFS zapewniają mapowanie między przyjazną dla użytkownika ścieżką UNC, na przykład i podstawową ścieżką UNC udziału `\\contoso\shares\ProjectX` SMB, na przykład lub `\\Server01-Prod\ProjectX` `\\storageaccount.file.core.windows.net\projectx` . Gdy użytkownik końcowy chce przejść do swojego udziału plików, wpisz przyjazną dla użytkownika ścieżkę UNC, ale jego klient SMB uzyskuje dostęp do podstawowej ścieżki SMB mapowania. Można również rozszerzyć tę podstawową koncepcję, aby przejąć istniejącą nazwę serwera plików, taką jak `\\MyServer\ProjectX` . Ta funkcja umożliwia osiągnięcie następujących scenariuszy:

- Podaj nazwę weryfikacji migracji dla logicznego zestawu danych. W tym przykładzie masz mapowanie podobne `\\contoso\shares\Engineering` do tego, mapowanie na `\\OldServer\Engineering` . Po zakończeniu migracji do Azure Files można zmienić mapowanie tak, aby ścieżka UNC przyjazna dla użytkownika wskazuje na `\\storageaccount.file.core.windows.net\engineering` . Gdy użytkownik końcowy uzyskuje dostęp do przyjaznej dla użytkownika ścieżki UNC, zostanie bezproblemowo przekierowany do ścieżki udziału plików platformy Azure.

- Ustanów nazwę pospolitą dla logicznego zestawu danych, który jest dystrybuowany do wielu serwerów w różnych lokacjach fizycznych, takich jak Azure File Sync. W tym przykładzie nazwa taka jak jest mapowana na wiele ścieżek `\\contoso\shares\FileSyncExample` UNC, takich `\\FileSyncServer1\ExampleShare` jak , , `\\FileSyncServer2\DifferentShareName` `\\FileSyncServer3\ExampleShare` . Gdy użytkownik uzyskuje dostęp do przyjaznej dla użytkownika ścieżki UNC, uzyskuje listę możliwych ścieżek UNC i wybiera tę, która jest najbliżej nich, na podstawie definicji witryny usługi Windows Server Active Directory (AD).

- Rozszerzanie logicznego zestawu danych między rozmiarami, we/wy lub innymi progami skalowania. Jest to typowe w przypadku pracy z katalogami użytkowników, w których każdy użytkownik otrzymuje własny folder w ramach udziału lub z udziałami tymczasowymi, gdzie użytkownicy mają dowolne miejsce do obsługi tymczasowych potrzeb dotyczących danych. Dzięki przestrzeniom nazw systemu plików DFS można ze sobą zesłać wiele folderów w spójną przestrzeń nazw. Na przykład `\\contoso\shares\UserShares\user1` mapuje na `\\storageaccount.file.core.windows.net\user1` , `\\contoso\shares\UserShares\user2` mapuje na , i tak `\\storageaccount.file.core.windows.net\user2` dalej.  

Przykład sposobu używania przestrzeni nazw systemu plików DFS z wdrożeniem usługi Azure Files można znaleźć w poniższym przeglądzie wideo.

[![Pokaz sposobu skonfigurowania systemu plików DFS-N za pomocą Azure Files — kliknij, aby odtworzyć!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Przejdź do godziny 10:10 w filmie wideo, aby zobaczyć, jak skonfigurować przestrzenie nazw systemu plików DFS.

Jeśli masz już przestrzeń nazw systemu plików DFS, nie są wymagane żadne specjalne kroki, aby używać jej z Azure Files i File Sync. Jeśli uzyskujesz dostęp do udziału plików platformy Azure ze środowisk lokalnych, mają zastosowanie normalne zagadnienia dotyczące sieci. Zobacz [Azure Files dotyczące sieci, aby](./storage-files-networking-overview.md) uzyskać więcej informacji.

## <a name="namespace-types"></a>Typy przestrzeni nazw
Przestrzenie nazw systemu plików DFS zapewniają dwa główne typy przestrzeni nazw:

- **Przestrzeń nazw oparta na domenie:** przestrzeń nazw hostowana jako Windows Server AD domeny. Przestrzenie nazw hostowane jako część usługi AD będą mieć ścieżkę UNC zawierającą nazwę domeny, na przykład `\\contoso.com\shares\myshare` , jeśli domena to `contoso.com` . Przestrzenie nazw oparte na domenie obsługują większe limity skalowania i wbudowaną nadmiarowość za pośrednictwem usługi AD. Przestrzenie nazw oparte na domenie nie mogą być zasobem klastra w klastrze trybu failover. 
- **Autonomiczna przestrzeń** nazw: przestrzeń nazw hostowana na jednym serwerze, która nie jest hostowana Windows Server AD. Autonomiczne przestrzenie nazw będą mieć nazwę opartą na nazwie serwera autonomicznego, na przykład , gdzie `\\MyStandaloneServer\shares\myshare` serwer autonomiczny ma nazwę `MyStandaloneServer` . Autonomiczne przestrzenie nazw obsługują cele o mniejszej skali niż przestrzenie nazw oparte na domenie, ale mogą być hostowane jako zasób klastrowany w klastrze trybu failover.

## <a name="requirements"></a>Wymagania
Aby używać przestrzeni nazw systemu plików DFS z Azure Files i File Sync, musisz mieć następujące zasoby:

- Domena usługi Active Directory. Może być hostowana w dowolnym miejscu, na przykład lokalnie, na maszynie wirtualnej platformy Azure, a nawet w innej chmurze.
- System Windows Server, który może hostować przestrzeń nazw. Wspólnym wzorcem wdrażania dla przestrzeni nazw systemu plików DFS jest użycie kontrolera domeny usługi Active Directory do hostowania przestrzeni nazw, jednak przestrzenie nazw można skonfigurować z dowolnego serwera z zainstalowaną rolą serwera Przestrzenie nazw systemu plików DFS. Przestrzenie nazw systemu plików DFS są dostępne we wszystkich obsługiwanych wersjach systemu Windows Server.
- Udział plików SMB hostowany w środowisku przyłączony do domeny, taki jak udział plików platformy Azure hostowany na koncie magazynu przyłączony do domeny lub udział plików hostowany na przyłącznym do domeny serwerze plików systemu Windows przy użyciu Azure File Sync. Aby uzyskać więcej informacji na temat przyłączanie konta magazynu do domeny, zobacz [Identity-based authentication (Uwierzytelnianie oparte na tożsamościach).](storage-files-active-directory-overview.md) Serwery plików systemu Windows są przyłączone do domeny w taki sam sposób, niezależnie od tego, czy używasz Azure File Sync.
- Udziały plików SMB, których chcesz używać z przestrzeniami nazw systemu plików DFS, muszą być dostępne z sieci lokalnych. Jest to przede wszystkim problem z udziałami plików platformy Azure, jednak technicznie ma zastosowanie do dowolnego udziału plików hostowanych na platformie Azure lub w dowolnej innej chmurze. Aby uzyskać więcej informacji na temat sieci, zobacz [Networking considerations for direct access (Zagadnienia dotyczące sieci związane z dostępem bezpośrednim).](storage-files-networking-overview.md)

## <a name="install-the-dfs-namespaces-server-role"></a>Instalowanie roli serwera Przestrzenie nazw systemu plików DFS
Jeśli już używasz przestrzeni nazw systemu plików DFS lub chcesz skonfigurować przestrzenie nazw systemu plików DFS na kontrolerze domeny, możesz bezpiecznie pominąć te kroki.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby zainstalować rolę serwera Przestrzenie nazw systemu plików DFS, otwórz Menedżer serwera na serwerze. Wybierz **pozycję Zarządzaj**, a następnie wybierz pozycję Dodaj role i **funkcje.** Wynikowy kreator przeprowadzi Cię przez proces instalacji składników systemu Windows niezbędnych do uruchamiania przestrzeni nazw systemu plików DFS i zarządzania nimi. 

W sekcji **Typ instalacji** kreatora instalacji  wybierz przycisk radiowy Instalacja oparta na rolach lub funkcjach, a następnie wybierz przycisk **Dalej.** W sekcji **Wybór serwera** wybierz żądane serwera, na których chcesz zainstalować rolę serwera Przestrzenie nazw systemu plików DFS, a następnie wybierz przycisk **Dalej.** 

W sekcji **Role serwera** wybierz i sprawdź rolę Przestrzenie nazw systemu plików **DFS** z listy ról. Można je znaleźć w obszarze **Usługi plików i magazynowania**  >  **usługi plików i usług ISCSI.** Wybranie roli serwera Przestrzenie nazw systemu plików DFS może również spowodować dodanie wymaganych ról lub funkcji serwera, które nie zostały jeszcze zainstalowane.

![Zrzut ekranu przedstawiający kreatora **Dodaj role i funkcje** z wybraną rolą **Przestrzenie nazw systemu plików DFS**.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Po sprawdzeniu roli Przestrzenie nazw systemu plików **DFS** możesz wybrać  przycisk **Dalej** na wszystkich kolejnych ekranach, a następnie wybrać pozycję Zainstaluj, gdy tylko kreator włącza przycisk. Po zakończeniu instalacji możesz skonfigurować przestrzeń nazw.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
W sesji programu PowerShell z podwyższonym poziomem uprawnień (lub przy użyciu komunikacji zdalnej programu PowerShell) wykonaj następujące polecenia.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Przejmij istniejące nazwy serwerów z konsolidacją serwerów głównych
Ważnym zastosowaniem przestrzeni nazw systemu plików DFS jest przejąnie istniejącej nazwy serwera na potrzeby refaktoryzacji fizycznego układu udziałów plików. Na przykład podczas migracji modernizacji można skonsolidować udziały plików z wielu starych serwerów plików na jednym serwerze plików. Tradycyjnie znajomość użytkownika końcowego i łączenie dokumentów ograniczają możliwość konsolidowania udziałów plików z różnych serwerów plików na jednym hoście, ale funkcja konsolidacji głównej przestrzeni nazw systemu plików DFS umożliwia tworzenie jednego serwera z wieloma nazwami serwerów i trasowanie do odpowiedniej nazwy udziału.

Chociaż jest to przydatne w różnych scenariuszach migracji centrum danych, konsolidacja główna jest szczególnie przydatna w przypadku adoptowania natywnych dla chmury udziałów plików platformy Azure, ponieważ:

- Udziały plików platformy Azure nie pozwalają zachować istniejących nazw serwerów lokalnych.
- Dostęp do udziałów plików platformy Azure należy uzyskać za pośrednictwem w pełni kwalifikowanej nazwy domeny (FQDN) konta magazynu. Na przykład udział plików platformy Azure o nazwie `share` na koncie magazynu jest zawsze dostępny za `storageaccount` pośrednictwem ścieżki `\\storageaccount.file.core.windows.net\share` UNC. Może to być mylące dla użytkowników końcowych, którzy oczekują krótkiej nazwy (np. `\\MyServer\share`) lub nazwa, która jest poddomeną nazwy domeny organizacji (np. `\\MyServer.contoso.com\share`).

Konsolidacja katalogu głównego może być używana tylko z autonomicznymi przestrzeniami nazw. Jeśli masz już istniejącą przestrzeń nazw opartą na domenie dla udziałów plików, nie musisz tworzyć skonsolidowanej głównej przestrzeni nazw.

### <a name="enabling-root-consolidation"></a>Włączanie konsolidacji głównej
Konsolidację katalogu głównego można włączyć, ustawiając następujące klucze rejestru z sesji programu PowerShell z podwyższonym poziomem uprawnień (lub używając komunikacji zdalnej programu PowerShell).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Tworzenie wpisów DNS dla istniejących nazw serwerów plików
Aby przestrzenie nazw systemu plików DFS odpowiadały na istniejące nazwy serwerów plików, utwórz rekordy aliasów (CNAME) dla istniejących serwerów plików, które wskazują nazwę serwera przestrzeni nazw systemu plików DFS. Dokładna procedura aktualizowania rekordów DNS może zależeć od serwerów, z których korzysta organizacja, oraz od tego, czy organizacja używa niestandardowych narzędzi do automatyzacji zarządzania systemem DNS. Poniższe kroki są wyświetlane dla serwera DNS dołączonego do systemu Windows Server i automatycznie używane przez usługę Windows AD.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na serwerze DNS systemu Windows otwórz konsolę zarządzania DNS. Można to znaleźć, wybierając przycisk **Start** i wpisując **DNS**. Przejdź do strefy wyszukiwania do przodu dla swojej domeny. Jeśli na przykład Domena to , strefę wyszukiwania do przodu można znaleźć w obszarze Strefy wyszukiwania do przodu `contoso.com`   >  **`contoso.com`** w konsoli zarządzania. Dokładna hierarchia wyświetlana w tym oknie dialogowym będzie zależeć od konfiguracji DNS dla sieci.

Kliknij prawym przyciskiem myszy strefę wyszukiwania do przodu i wybierz pozycję **Nowy alias (CNAME).** W wynikowym oknie dialogowym wprowadź krótką nazwę zamienianego serwera plików (w pełni kwalifikowana nazwa domeny zostanie automatycznie wypełniona w polu tekstowym z etykietą W pełni kwalifikowana **nazwa domeny).** W polu tekstowym z etykietą W pełni kwalifikowana nazwa domeny **(FQDN)** dla hosta docelowego wprowadź nazwę serwera DFS-N, który został ustawiony. Możesz użyć przycisku **Przeglądaj,** aby w razie potrzeby wybrać serwer. Wybierz **przycisk OK,** aby utworzyć rekord CNAME dla serwera.

![Zrzut ekranu przedstawiający **nowy rekord zasobu** dla wpisu DNS CNAME.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Na serwerze DNS z systemem Windows otwórz sesję programu PowerShell (lub użyj komunikacji zdalnej programu PowerShell), aby wykonać następujące polecenia, wypełniając polecenia i , wartościami odpowiednimi dla danego środowiska ( zostaną automatycznie wypełnione nazwą domeny, ale możesz również wpisać ją `$oldServer` `$dfsnServer` `$domain` ręcznie.

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw
Podstawową jednostką zarządzania dla przestrzeni nazw systemu plików DFS jest przestrzeń nazw . Katalog główny lub nazwa przestrzeni nazw jest punktem początkowym przestrzeni nazw, tak aby w ścieżce UNC katalog główny przestrzeni nazw `\\contoso.com\Public\` to `Public` . 

Jeśli używasz przestrzeni nazw systemu plików DFS do przejąnia istniejącej nazwy serwera z konsolidacją katalogu głównego, nazwa przestrzeni nazw powinna być nazwą serwera, którą chcesz przejąć, wraz ze `#` znakiem . Jeśli na przykład chcesz przejąć istniejący serwer o nazwie , utwórz przestrzeń nazw `MyServer` DFS-N o nazwie `#MyServer` . W poniższej sekcji programu PowerShell jest dołączany plik , ale w przypadku tworzenia za pomocą konsoli zarządzania systemu plików DFS należy do nich do dołączać `#` odpowiednio. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć nową przestrzeń nazw, otwórz konsolę **zarządzania systemu plików DFS.** Można to znaleźć, wybierając przycisk **Start** i wpisując **Zarządzanie systemami plików DFS.** Wynikowa konsola zarządzania ma dwie sekcje **Namespaces** (Przestrzenie nazw) i **Replication**(Replikacja), które odnoszą się odpowiednio do nazw systemu plików DFS i Replikacja systemu plików DFS (DFS-R). Azure File Sync zapewnia nowoczesny mechanizm replikacji i synchronizacji, który może być używany w miejsce systemu plików DFS-R, jeśli replikacja jest również pożądana.

Wybierz **sekcję Przestrzenie** nazw, a następnie wybierz przycisk **Nowa** przestrzeń nazw (możesz również kliknąć prawym przyciskiem myszy **sekcję Przestrzenie** nazw). Wynikowy **Kreator nowej przestrzeni nazw** przeprowadzi Cię przez proces tworzenia przestrzeni nazw. 

Pierwsza sekcja w kreatorze wymaga wyboru serwera przestrzeni nazw systemu plików DFS do obsługi przestrzeni nazw. Przestrzeń nazw może być hostem wielu serwerów, ale konieczne będzie skonfigurowanie przestrzeni nazw systemu plików DFS z jednym serwerem na raz. Wprowadź nazwę odpowiedniego serwera przestrzeni nazw systemu plików DFS i wybierz przycisk **Dalej.** W sekcji **Nazwa przestrzeni nazw i ustawienia** możesz wprowadzić żądaną nazwę przestrzeni nazw i wybrać przycisk **Dalej.** 

Sekcja **Typ przestrzeni** nazw umożliwia wybór między przestrzenią nazw opartą na **domenie** a **autonomiczną przestrzenią nazw**. Jeśli zamierzasz użyć przestrzeni nazw systemu plików DFS do zachowania istniejącej nazwy serwera plików/urządzenia NAS, wybierz opcję autonomicznej przestrzeni nazw. W przypadku innych scenariuszy należy wybrać przestrzeń nazw opartą na domenie. Zapoznaj się z [powyższymi typami](#namespace-types) przestrzeni nazw, aby uzyskać więcej informacji na temat wybierania między typami przestrzeni nazw.

![Zrzut ekranu przedstawiający wybieranie między przestrzenią nazw opartą na domenie a autonomiczną przestrzenią nazw w kreatorze **Nowa przestrzeń nazw**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Wybierz żądany typ przestrzeni nazw dla środowiska, a następnie wybierz pozycję **Dalej.** Następnie kreator podsumuje przestrzeń nazw do utworzenia. Wybierz **pozycję Utwórz,** aby utworzyć przestrzeń nazw, i **pozycję** Zamknij po zakończeniu okna dialogowego.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
W sesji programu PowerShell na serwerze przestrzeni nazw systemu plików DFS wykonaj następujące polecenia programu PowerShell, wypełnij pola , i odpowiednimi `$namespace` `$type` `$takeOverName` wartościami dla danego środowiska.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Konfigurowanie folderów i obiektów docelowych folderów
Aby przestrzeń nazw była przydatna, musi mieć foldery i obiekty docelowe folderów. Każdy folder może mieć co najmniej jeden folder docelowy, który jest wskaźnikiem do udziałów plików SMB, które hostują zawartość. Gdy użytkownicy przeglądają folder z folderami docelowymi, komputer kliencki odbiera polecenie, które w sposób niewidoczny przekierowuje komputer kliencki do jednego z obiektów docelowych folderów. Możesz również mieć foldery bez obiektów docelowych folderów, aby dodać strukturę i hierarchię do przestrzeni nazw.

Foldery przestrzeni nazw systemu plików DFS można uważać za analogiczne do udziałów plików. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
W konsoli zarządzania systemu plików DFS wybierz właśnie utworzoną przestrzeń nazw, a następnie wybierz **pozycję Nowy folder.** Wynikowe **okno dialogowe Nowy folder** umożliwia utworzenie folderu i jego obiektów docelowych.

![Zrzut ekranu przedstawiający okno dialogowe **Nowy folder**.](./media/files-manage-namespaces/dfs-folder-targets.png)

W polu tekstowym z **etykietą Nazwa** podaj nazwę folderu. Wybierz **pozycję Dodaj...,** aby dodać obiekty docelowe folderów dla tego folderu. Wynikowe okno **dialogowe Dodawanie obiektu docelowego** folderu zawiera pole tekstowe z etykietą Ścieżka do folderu **docelowego,** w którym można podać ścieżkę UNC do żądanego folderu. Wybierz **przycisk OK** w **oknie dialogowym Dodawanie folderu docelowego.** Jeśli dodajesz ścieżkę UNC do udziału plików platformy Azure, może zostać wyświetlony komunikat informujący o tym, że serwer `storageaccount.file.core.windows.net` nie może być kontaktami. Jest to oczekiwane; Wybierz **pozycję Tak,** aby kontynuować. Na koniec wybierz **przycisk OK** w **oknie dialogowym Nowy folder,** aby utworzyć foldery i foldery docelowe.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Po utworzeniu przestrzeni nazw, folderu i obiektu docelowego folderu powinno być możliwe zamontowanie udziału plików za pośrednictwem przestrzeni nazw systemu plików DFS. Jeśli używasz przestrzeni nazw opartej na domenie, pełną ścieżką udziału powinna być `\\<domain-name>\<namespace>\<share>` . Jeśli używasz autonomicznej przestrzeni nazw, pełną ścieżką udziału powinna być `\\<DFS-server>\<namespace>\<share>` . Jeśli używasz autonomicznej przestrzeni nazw z konsolidacją katalogów głównych, możesz uzyskać dostęp bezpośrednio za pośrednictwem starej nazwy serwera, takiej jak `\\<old-server>\<share>` .

## <a name="see-also"></a>Zobacz też
- Wdrażanie udziału plików platformy Azure: [planowanie wdrożenia Azure Files i](storage-files-planning.md) Jak utworzyć udział [plików.](storage-how-to-create-file-share.md)
- Konfigurowanie dostępu do udziału plików: [Uwierzytelnianie oparte na](storage-files-active-directory-overview.md) tożsamościach i [zagadnienia dotyczące sieci w przypadku dostępu bezpośredniego.](storage-files-networking-overview.md)
- [Przestrzenie nazw rozproszony system plików Windows Server](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)
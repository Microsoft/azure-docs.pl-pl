---
title: Informacje o wersji dla agenta Azure File Sync | Microsoft Docs
description: Przeczytaj informacje o wersji dla agenta Azure File Sync, który umożliwia scentralizowanie udziałów plików w organizacji w Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 7/28/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 00590c2b12e8600b2a650444b97b6558e3a52da1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904942"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Rozwoju | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| V 10.1 wydanie- [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 czerwca 2020 | Obsługiwane |
| 2020 — pakiet zbiorczy aktualizacji — [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 r. | Obsługiwane |
| V10 wydanie — [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 kwietnia 2020 | Obsługiwane |
| Grudzień 2019 — pakiet zbiorczy aktualizacji — [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 grudnia 2019 | Obsługiwane |
| V9 wydanie — [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 grudnia 2019 | Obsługiwane |
| V8 wydanie — [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 października 2019 r. | Obsługiwane |
| 2019 lipca zbiorczych aktualizacji — [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 lipca 2019 r. | Obsługiwane — wersja agenta wygaśnie 1 września 2020 |
| 2019 lipca zbiorczych aktualizacji — [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 lipca 2019 r. | Obsługiwane — wersja agenta wygaśnie 1 września 2020 |
| Wersji 7 wydanie — [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 czerwca 2019 | Obsługiwane — wersja agenta wygaśnie 1 września 2020 |
| Wydanie w wersji 6 | 6.0.0.0 - 6.3.0.0 | Brak | Nieobsługiwane — wersje agenta wygasły 21 kwietnia 2020 |
| Wersja V5 | 5.0.2.0 - 5.2.0.0 | Brak | Nieobsługiwane — wersje agenta wygasły 18 marca, 2020 |
| Wersja v4 | 4.0.1.0 - 4.3.0.0 | Brak | Nieobsługiwane — wersje agenta wygasły 6 listopada, 2019 |
| Wersja V3 | 3.1.0.0 - 3.4.0.0 | Brak | Nieobsługiwane — wersje agenta wygasły 19 sierpnia 2019 |
| Agenci wstępnie GA | 1.1.0.0 — 3.0.13.0 | Brak | Nieobsługiwane — wersje agenta wygasły 1 października 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>10.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 10.1.0.0 agenta Azure File Sync wydanej 5 czerwca 2020. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 10.0.0.0 i 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa prywatnego punktu końcowego platformy Azure
    - Ruch synchronizacji do usługi synchronizacji magazynu można teraz wysłać do prywatnego punktu końcowego. Umożliwia to tunelowanie za pośrednictwem połączenia ExpressRoute lub sieci VPN. Aby dowiedzieć się więcej, zobacz [Konfigurowanie punktów końcowych sieci Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints).
- Po zsynchronizowaniu plików Metryka będzie teraz wyświetlana postęp, gdy jest uruchomiona duża synchronizacja, a nie na końcu.
- Różne ulepszenia niezawodności dotyczące instalacji agentów, warstw chmur, synchronizacji i telemetrii

## <a name="agent-version-10020"></a>10.0.2.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 10.0.2.0 agenta Azure File Sync wydana 19 maja 2020. Te informacje są uzupełnieniem informacji o wersji w wersji 10.0.0.0.

Problem rozwiązany w tej wersji:  
- Agent synchronizacji magazynu (FileSyncSvc) często ulega awarii po zainstalowaniu agenta Azure File Sync v10.

> [!Note]  
>W tej wersji nie przeprowadzono żadnych inspekcji na serwerach, które są skonfigurowane do automatycznej aktualizacji, gdy nowa wersja będzie dostępna. Aby zainstalować tę aktualizację, użyj Microsoft Update lub wykazu Microsoft Update (zobacz temat [KB4522412](https://support.microsoft.com/help/4522412) for Installation Instructions).

## <a name="agent-version-10000"></a>Wersja agenta 10.0.0.0
Poniższe informacje o wersji dotyczą wersji 10.0.0.0 agenta Azure File Sync (wydanie 9 kwietnia 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Ulepszony postęp synchronizacji w portalu
    - W przypadku wydania agenta v10 Azure Portal wkrótce rozpocznie wyświetlanie typu sesji synchronizacji, która jest uruchomiona. Na przykład Pobieranie początkowe, regularne pobieranie, wycofywanie w tle (szybkie odzyskiwanie po awarii) i podobne. 

- Udoskonalone środowisko portalu obsługi warstw w chmurze
    - Jeśli masz pliki, które nie mogą zostać przełączone do warstwy lub odwołania, możesz teraz wyświetlić błędy dotyczące warstw we właściwościach punktu końcowego serwera.
    - Dodatkowe informacje o warstwach w chmurze są dostępne dla punktu końcowego serwera:
        - Rozmiar lokalnej pamięci podręcznej
        - Pamięć podręczna wydajności użycia
        - Szczegóły zasad obsługi warstw w chmurze: rozmiar woluminu, bieżące wolne miejsce lub czas ostatniego dostępu najstarszego pliku w lokalnej pamięci podręcznej.
    - Te zmiany zostaną wydane Azure Portal wkrótce po początkowej wersji agenta v10.

- Obsługa przeniesienia usługi synchronizacji magazynu i/lub konta magazynu do innej dzierżawy Azure Active Directory (AAD)
    - Azure File Sync teraz obsługuje przeniesienie usługi synchronizacji magazynu i/lub konta magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD.
    
- Różne ulepszenia dotyczące wydajności i niezawodności
    - Wykrywanie zmian w udziale plików platformy Azure może się nie powieść, jeśli na koncie magazynu skonfigurowano reguły sieci wirtualnej (VNET) i zapory.
    - Zmniejszone użycie pamięci skojarzone z odwołaniem. 
    - Zwiększona wydajność podczas korzystania z polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Inne różne ulepszenia dotyczące niezawodności. 
    
### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchomienie programu Sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesieniu usługi synchronizacji magazynu lub konta magazynu musisz nadać aplikacji Microsoft. StorageSync dostęp do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze można przenieść usługę synchronizacji magazynu i konto magazynu do różnych dzierżawców usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.

## <a name="agent-version-9100"></a>9.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 9.1.0.0 agenta Azure File Sync wydanej 12 grudnia 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 9.0.0.0.

Problem rozwiązany w tej wersji:  
- Synchronizacja nie powiedzie się z jednym z następujących błędów po uaktualnieniu do agenta Azure File Sync w wersji 9,0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>9.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 9.0.0.0 agenta Azure File Sync (wydanie 2 grudnia 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa przywracania samoobsługowego
    - Użytkownicy mogą teraz przywracać pliki przy użyciu poprzedniej wersji funkcji. Przed wydaniem V9 funkcja poprzedniej wersji nie była obsługiwana na woluminach, na których włączono obsługę warstw w chmurze. Ta funkcja musi być włączona osobno dla każdego woluminu, na którym istnieje punkt końcowy z włączoną obsługą warstw w chmurze. Aby dowiedzieć się więcej, zobacz  
[Samoobsługowe Przywracanie za poorednictwem wcześniejszych wersji i usługi VSS (usługa kopiowania woluminów w tle)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Obsługa większych rozmiarów udziałów plików 
    - Azure File Sync obsługuje teraz do 64TiB i 100 000 000 plików w pojedynczej, synchronizowanej przestrzeni nazw.  
 
- Obsługa deduplikacji danych na serwerze 2019 
    - Funkcja deduplikacji danych jest teraz obsługiwana w przypadku obsługi warstw w chmurze w systemie Windows Server 2019. Aby zapewnić obsługę deduplikacji danych na woluminach z obsługą warstw w chmurze, należy zainstalować usługę Windows Update [KB4520062](https://support.microsoft.com/help/4520062) . 
 
- Ulepszony minimalny rozmiar pliku na warstwę 
    - Minimalny rozmiar pliku do warstwy jest teraz oparty na rozmiarze klastra systemu plików (podwójny rozmiar klastra systemu plików). Na przykład Domyślnie rozmiar klastra systemu plików NTFS to 4 KB, a minimalny rozmiar pliku do warstwy to rozmiarze 8 KB. 
 
- Polecenie cmdlet testu łączności sieciowej 
    - W ramach konfiguracji Azure File Sync należy skontaktować się z wieloma punktami końcowymi usługi. Każdy z nich ma własną nazwę DNS, która musi być dostępna dla serwera. Te adresy URL są również specyficzne dla regionu, w którym zarejestrowano serwer. Po zarejestrowaniu serwera do testowania komunikacji ze wszystkimi adresami URL, które są specyficzne dla tego serwera, można użyć polecenia cmdlet testu łączności (narzędzia do rejestracji programu PowerShell i serwera). To polecenie cmdlet może pomóc w rozwiązywaniu problemów w przypadku niekompletnej komunikacji uniemożliwia serwerowi pełną pracę z Azure File Sync i może służyć do precyzyjnego dostrajania konfiguracji serwera proxy i zapory.  
 
        Aby uruchomić test łączności sieciowej, uruchom następujące polecenia programu PowerShell: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Usuń ulepszenie punktu końcowego serwera, gdy włączono obsługę warstw w chmurze 
    - Tak jak wcześniej Usuwanie punktu końcowego serwera nie powoduje usunięcia plików w udziale plików platformy Azure. Jednak zachowanie punktów ponownej analizy na serwerze lokalnym uległo zmianie. Punkty ponownej analizy (wskaźniki do plików, które nie są lokalne na serwerze) są teraz usuwane podczas usuwania punktu końcowego serwera. W pełni buforowane pliki pozostaną na serwerze. To ulepszenie zostało wykonane w celu zapobieżenia oddzielnym [plikom warstwowym](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) podczas usuwania punktu końcowego serwera. Jeśli punkt końcowy serwera zostanie odtworzony, punkty ponownej analizy dla plików warstwowych zostaną ponownie utworzone na serwerze.  
 
- Ulepszenia wydajności i niezawodności 
    - Zmniejszono błędy odwoływania. Rozmiar przywoływany jest teraz automatycznie dostosowywany na podstawie przepustowości sieci. 
    - Ulepszona wydajność pobierania podczas dodawania nowego serwera do grupy synchronizacji. 
    - Nie można zsynchronizować zredukowanych plików z powodu konfliktów ograniczeń. 
    - W niektórych scenariuszach pliki nie są w warstwie lub są nieoczekiwanie wywoływane, jeśli ścieżka punktu końcowego serwera jest punktem instalacji woluminu.
    
### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchomienie programu Sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane. Aby dowiedzieć się więcej, zobacz [pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Jeśli pagefile.sys znajduje się na woluminie, na którym jest włączona obsługa warstw w chmurze, może się nie powieść. pagefile.sys powinna znajdować się na woluminie z wyłączonym warstwą chmury.

## <a name="agent-version-8000"></a>8.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 8.0.0.0 agenta Azure File Sync (wydanej 8 października 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Udoskonalenia wydajności przywracania
    - Krótszy czas odzyskiwania dla odzyskiwania wykonywanego za Azure Backup. Przywrócone pliki zsynchronizują się z serwerami Azure File Sync znacznie szybciej. 
- Udoskonalone środowisko portalu obsługi warstw w chmurze  
    - Jeśli masz pliki warstwowe, które nie mogą zostać odwołane, możesz teraz wyświetlić błędy odwołań we właściwościach punktu końcowego serwera. Ponadto kondycja punktu końcowego serwera wyświetli teraz błąd i kroki zaradcze, jeśli sterownik filtru warstwy chmury nie zostanie załadowany na serwerze.
- Uproszczona instalacja agenta
    - Moduł Az\AzureRM PowerShell nie jest już wymagany do zarejestrowania serwera, co upraszcza i szybko.
- Różne ulepszenia dotyczące wydajności i niezawodności

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchomienie programu Sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane. Aby dowiedzieć się więcej, zobacz [pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.

## <a name="agent-version-7200"></a>7.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.2.0.0 agenta Azure File Sync wydanej 24 lipca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Agent synchronizacji magazynu (FileSyncSvc) ulega awarii, jeśli konfiguracja serwera proxy ma wartość null.
- Punkt końcowy serwera zacznie BCDR (błąd 0x80c80257-ECS_E_BCDR_IN_PROGRESS), jeśli wiele punktów końcowych na serwerze ma taką samą nazwę.
- Ulepszenia niezawodności warstw w chmurze.

## <a name="agent-version-7100"></a>7.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.1.0.0 agenta Azure File Sync wydanej 12 lipca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lub przeglądanie lokalizacji punktu końcowego serwera za pośrednictwem protokołu SMB działa wolno w systemie Windows Server 2012 R2. 
- Zwiększone wykorzystanie procesora po zainstalowaniu agenta Azure File Sync w wersji 6.
- Udoskonalenia danych telemetrycznych dotyczących warstw w chmurze.
- Różne ulepszenia niezawodności dotyczące warstw i synchronizacji w chmurze.

## <a name="agent-version-7000"></a>7.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 7.0.0.0 agenta Azure File Sync (wydanie z 19 czerwca 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa większych rozmiarów udziałów plików
    - W wersji zapoznawczej większych udziałów plików platformy Azure zwiększamy również limity pomocy technicznej dotyczące synchronizacji plików. W pierwszym kroku Azure File Sync teraz obsługuje do 25 TB i 50 000 000 plików w pojedynczej, synchronizowanej przestrzeni nazw. Aby złożyć udział w wersji zapoznawczej dużego udziału plików, Wypełnij ten formularz https://aka.ms/azurefilesatscalesurvey . 
- Obsługa ustawień zapory i sieci wirtualnej na kontach magazynu
    - Azure File Sync teraz obsługuje ustawienia zapory i sieci wirtualnej na kontach magazynu. Aby skonfigurować wdrożenie do pracy z ustawieniami zapory i sieci wirtualnej, zobacz [Konfigurowanie ustawień zapory i sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Polecenie cmdlet programu PowerShell do natychmiastowej synchronizacji plików zmienionych w udziale plików platformy Azure
    - Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet Invoke-AzStorageSyncChangeDetection programu PowerShell. To polecenie cmdlet jest przeznaczone dla scenariuszy, w których jakiś typ zautomatyzowany proces wprowadza zmiany w udziale plików platformy Azure lub zmiany są wykonywane przez administratora (na przykład przeniesienie plików i katalogów do udziału). W przypadku zmian wprowadzonych przez użytkownika końcowego zaleca się zainstalowanie agenta Azure File Sync na maszynie wirtualnej IaaS, a użytkownicy końcowi uzyskują dostęp do udziału plików za pomocą maszyny wirtualnej IaaS. W ten sposób wszystkie zmiany zostaną szybko zsynchronizowane z innymi agentami bez konieczności korzystania z polecenia cmdlet Invoke-AzStorageSyncChangeDetection. Aby dowiedzieć się więcej, zobacz dokumentację [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
- Udoskonalone środowisko portalu w przypadku wystąpienia plików, które nie są synchronizowane
    - Jeśli masz pliki, których synchronizacja nie powiedzie się, rozróżnimy od błędów przejściowych i trwałych w portalu. Błędy przejściowe zwykle rozwiązują się samodzielnie bez konieczności działania administratora. Na przykład plik, który jest aktualnie używany, nie będzie synchronizowany do momentu zamknięcia dojścia do pliku. W przypadku błędów trwałych jest teraz wyświetlana liczba plików, na które mają wpływ poszczególne błędy. Liczba trwałych błędów jest również wyświetlana w kolumnie brak synchronizacji plików wszystkich punktów końcowych serwera w grupie synchronizacji.
- Ulepszone Azure Backup przywracanie na poziomie plików
    - Pojedyncze pliki przywrócone przy użyciu Azure Backup są teraz wykrywane i synchronizowane z punktem końcowym serwera.
- Ulepszona niezawodność poleceń cmdlet odwoływania się do warstw w chmurze 
    - Polecenie cmdlet Invoke-StorageSyncFileRecall umożliwia klientom określenie liczby ponownych prób dla plików i opóźnienie ponowienia pliku podobne do Robocopy. Wcześniej to polecenie cmdlet spowoduje odzyskanie wszystkich plików warstwowych pod daną ścieżką w kolejności losowej. W przypadku parametru New-Order to polecenie cmdlet najpierw przywróci dane okienko i przestrzega zasad obsługi warstw w chmurze (Zatrzymywanie wywołania w przypadku spełnienia zasad dotyczących daty lub spełnienia wolnego miejsca na woluminie, w zależności od tego, co się dzieje).
- Obsługa tylko protokołu TLS 1,2 (protokół TLS 1,0 i 1,1 jest wyłączony)
    - Azure File Sync teraz obsługuje używanie protokołu TLS 1,2 tylko na serwerach z wyłączonymi protokołami TLS 1,0 i 1,1. Przed wprowadzeniem tej poprawy Rejestracja serwera nie powiedzie się, jeśli na serwerze wyłączono protokoły TLS 1,0 i 1,1.
- Różne ulepszenia dotyczące wydajności i niezawodności dla synchronizacji i warstw w chmurze
    - W tej wersji istnieje kilka ulepszeń dotyczących niezawodności i wydajności. Niektóre z nich umożliwiają wydajniejszą obsługę warstw w chmurze, a Azure File Sync jako całość działają lepiej w tych sytuacjach, gdy ustawiono harmonogram ograniczania przepustowości.

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchomienie programu Sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.

## <a name="agent-version-6300"></a>6.3.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.3.0.0 agenta Azure File Sync wydanej 27 czerwca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lub przeglądanie lokalizacji punktu końcowego serwera za pośrednictwem protokołu SMB jest wolne w systemie Windows Server 2012 R2 
- Zwiększone wykorzystanie procesora po zainstalowaniu agenta Azure File Sync w wersji 6
- Udoskonalenia danych telemetrycznych w warstwach chmury

## <a name="agent-version-6200"></a>6.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.2.0.0 agenta Azure File Sync wydanego od 13 czerwca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Po utworzeniu punktu końcowego serwera może wystąpić duże użycie procesora CPU, gdy funkcja odwoływania w tle pobiera pliki na serwer
- Operacje synchronizacji i obsługi warstw w chmurze mogą zakończyć się niepowodzeniem z powodu błędu ECS_E_SERVER_CREDENTIAL_NEEDED ze względu na wygaśnięcie tokenu
- Wywołanie pliku może się nie powieść, jeśli adres URL pobierania pliku zawiera zastrzeżone znaki 

## <a name="agent-version-6100"></a>6.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.1.0.0 agenta Azure File Sync wydanej 6 maja 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Centrum administracyjne systemu Windows nie może wyświetlić wersji agenta i konfiguracji punktu końcowego serwera na serwerach, na których zainstalowano agenta Azure File Sync w wersji 6,0.

## <a name="agent-version-6000"></a>6.0.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 6.0.0.0 agenta Azure File Sync (wydanie 22 kwietnia 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa autoaktualizacji agenta
  - Otrzymaliśmy Twoją opinię i dodano funkcję autoaktualizowania do agenta serwera Azure File Sync. Aby uzyskać więcej informacji, zobacz [Azure File Sync zasad aktualizacji agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Obsługa list ACL udziałów plików platformy Azure
  - Azure File Sync zawsze obsługiwał synchronizowanie list ACL między punktami końcowymi serwera, ale listy ACL nie zostały zsynchronizowane z punktem końcowym w chmurze (udziałem plików platformy Azure). W tej wersji dodano obsługę synchronizowania list kontroli dostępu między serwerem a punktami końcowymi w chmurze.
- Równoległe przekazywanie i pobieranie sesji synchronizacji dla punktu końcowego serwera 
  - Punkty końcowe serwera obsługują teraz przekazywanie i pobieranie plików w tym samym czasie. Nie można czekać na ukończenie pobierania, aby umożliwić przekazywanie plików do udziału plików platformy Azure. 
- Nowe polecenia cmdlet do obsługi warstw w chmurze umożliwiające pobieranie stanu woluminu i warstwowego
  - Dwa nowe polecenia cmdlet programu PowerShell w ramach serwera mogą być teraz używane do uzyskiwania informacji o ustawieniach i odwoływania plików w chmurze. Udostępniają one informacje rejestrowania z dwóch kanałów zdarzeń na serwerze:
    - Get-StorageSyncFileTieringResult wyświetli listę wszystkich plików i ich ścieżek, które nie zostały warstwowe i raporty z przyczyn.
    - Get-StorageSyncFileRecallResult raportuje wszystkie zdarzenia odwołania do pliku. Wyświetla wszystkie pliki, które zostały odwołane, oraz jego ścieżkę, a także powodzenie lub błąd dla tego odwołania.
  - Domyślnie oba kanały zdarzeń mogą przechowywać do 1 MB każdego — można zwiększyć ilość plików raportowanych przez zwiększenie rozmiaru kanału zdarzenia.
- Obsługa trybu FIPS
  - Azure File Sync teraz obsługuje Włączanie trybu FIPS na serwerach z zainstalowanym agentem Azure File Sync.
    - Przed włączeniem trybu FIPS na serwerze Zainstaluj agenta Azure File Sync i [moduł PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwerze. Jeśli na serwerze jest już włączona funkcja FIPS, [ręcznie Pobierz](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) [moduł PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwer.
- Różne ulepszenia niezawodności dotyczące warstw i synchronizacji w chmurze

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-5200"></a>5.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.2.0.0 agenta Azure File Sync wydanej 4 kwietnia 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Ulepszenia niezawodności dotyczące funkcji transferu danych w trybie offline i wznawiania transferu danych
- Udoskonalenia synchronizacji danych telemetrycznych

## <a name="agent-version-5100"></a>5.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.1.0.0 agenta Azure File Sync wydanego 7 marca 2019. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Synchronizacja plików może zakończyć się niepowodzeniem z błędem 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED), jeśli Wyliczenie zmian kończy się niepowodzeniem na serwerze
- Jeśli sesja synchronizacji lub plik otrzymuje błąd 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizacja spowoduje teraz ponowną próbę wykonania operacji
- Synchronizacja plików może zakończyć się niepowodzeniem z powodu błędu 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Duże użycie pamięci może wystąpić podczas odwoływania plików
- Udoskonalenia danych telemetrycznych w warstwach chmury 

## <a name="agent-version-5020"></a>5.0.2.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 5.0.2.0 agenta Azure File Sync (wydane 12 lutego 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa Azure Government w chmurze
  - Dodaliśmy obsługę wersji zapoznawczej dla chmury Azure Government. Wymaga to białej subskrypcji i specjalnego pobrania agenta od firmy Microsoft. Aby uzyskać dostęp do wersji zapoznawczej, Wyślij wiadomość e-mail bezpośrednio na adres [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com) .
- Obsługa deduplikacji danych
    - Funkcja deduplikacji danych jest teraz w pełni obsługiwana przy użyciu obsługi warstw w chmurze w systemach Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji na woluminie z włączonym obsługą warstw w chmurze umożliwia przechowywanie w pamięci podręcznej większej liczby plików bez udostępniania większej ilości miejsca w magazynie.
- Obsługa transferu danych w trybie offline (na przykład za pośrednictwem urządzenie Data Box)
    - Z łatwością Migruj duże ilości danych do Azure File Sync za pośrednictwem dowolnych środków. Można wybrać Azure Data Box, AzCopy i nawet usług migracji innych firm. Nie ma potrzeby korzystania z dużej ilości przepustowości w celu pobierania danych na platformę Azure, w przypadku urządzenie Data Box — po prostu wyślij ją tam. Aby dowiedzieć się więcej, zobacz [Offline transfer danych docs](https://aka.ms/AFS/OfflineDataTransfer).
- Ulepszona wydajność synchronizacji
    - Klienci z wieloma punktami końcowymi serwera na tym samym woluminie mogą mieć powolną wydajność synchronizacji przed tą wersją. Azure File Sync tworzy tymczasową migawkę usługi VSS raz dziennie na serwerze w celu synchronizowania plików, które mają otwarte dojścia. Usługa Sync obsługuje teraz synchronizację wielu punktów końcowych serwera na woluminie, gdy sesja synchronizacji usługi VSS jest aktywna. Nie można czekać na ukończenie sesji synchronizacji usługi VSS w celu wznowienia synchronizacji w innych punktach końcowych serwera w woluminie.
- Ulepszone monitorowanie w portalu
    - Dodano wykresy do portalu usługi synchronizacji magazynu w celu wyświetlenia:
        - Liczba synchronizowanych plików
        - Rozmiar transferowanych danych
        - Liczba niesynchronizowanych plików
        - Rozmiar danych, które zostały odwołane
        - Stan łączności z serwerem
    - Aby dowiedzieć się więcej, zobacz [Monitor Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Zwiększona skalowalność i niezawodność
    - Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu została zwiększona do 1 000 000. Poprzedni limit to 200 000.
    - Funkcja synchronizacji podejmie próbę wznowienia transferu danych zamiast retransmisji w przypadku przerwania transferu dla dużych plików 

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwany i musi być wyłączony. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure File Sync](storage-sync-files-troubleshoot.md).
- Ekrany plików Menedżer zasobów serwera plików (Menedżer zasobów plików) mogą spowodować nieograniczone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu Sysprep na serwerze, na którym zainstalowano agenta Azure File Sync, nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy zainstalować agenta Azure File Sync.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) , aby wyświetlić listę nieobsługiwanych znaków.
- Pliki lub katalogi kończące się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe staną się niedostępne, jeśli nie zostaną one wywołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli upłynął 30 dni od momentu, gdy punkt końcowy serwera został usunięty lub punkt końcowy w chmurze został usunięty, pliki warstwowe, które nie zostały odwołane, nie będą używane.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana w przypadku zmiany nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

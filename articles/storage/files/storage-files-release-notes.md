---
title: Informacje o wersji dla agenta usługi Azure File Sync | Dokumenty firmy Microsoft
description: Informacje o wersji dla agenta usługi Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 82d8c8ca94f3d0f4289902ae40928ff507e2d1ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460668"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Kamień milowy | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Wersja V10 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 9 kwietnia 2020 r. | W locie |
| Pakiet zbiorczy aktualizacji z grudnia 2019 r. — [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 grudnia 2019 r. | Obsługiwane |
| Wersja V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 grudnia 2019 r. | Obsługiwane |
| Wydanie V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 października 2019 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji z lipca 2019 r. — [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 lipca 2019 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji z lipca 2019 r. — [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 lipca 2019 r. | Obsługiwane |
| Wydanie V7 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 czerwca 2019 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji z czerwca 2019 r. — [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 czerwca 2019 r. | Obsługiwane — wersja agenta wygaśnie 21 kwietnia 2020 r. |
| Pakiet zbiorczy aktualizacji z czerwca 2019 r. — [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 czerwca 2019 r. | Obsługiwane — wersja agenta wygaśnie 21 kwietnia 2020 r. |
| Pakiet zbiorczy aktualizacji z maja 2019 r. — [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 maja 2019 r. | Obsługiwane — wersja agenta wygaśnie 21 kwietnia 2020 r. |
| Wydanie V6 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 kwietnia 2019 r. | Obsługiwane — wersja agenta wygaśnie 21 kwietnia 2020 r. |
| Wydanie V5 | 5.0.2.0 - 5.2.0.0 | Nie dotyczy | Nie obsługiwane — wersje agenta wygasły 18 marca 2020 r. |
| Wersja V4 | 4.0.1.0 - 4.3.0.0 | Nie dotyczy | Nie obsługiwane — wersje agenta wygasły 6 listopada 2019 r. |
| Wersja V3 | 3.1.0.0 - 3.4.0.0 | Nie dotyczy | Nie obsługiwane — wersje agenta wygasły 19 sierpnia 2019 r. |
| Agenci Pre-GA | 1.1.0.0 - 3.0.13.0 | Nie dotyczy | Nie obsługiwane — wersje agenta wygasły 1 października 2018 r. |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>Wersja agenta 10.0.0.0
Poniższe informacje o wersji są dla wersji 10.0.0.0 agenta usługi Azure File Sync (wydany 9 kwietnia 2020 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Ulepszony postęp synchronizacji w portalu
    - Wraz z wydaniem agenta v10 witryna Azure portal wkrótce rozpocznie wyświetlanie typu uruchomionej sesji synchronizacji. Na przykład pobieranie początkowe, regularne pobieranie, wycofywanie w tle (szybkie odzyskiwanie po awarii) i podobne. 

- Ulepszone środowisko portalu warstwowego w chmurze
    - Jeśli masz pliki, które nie są warstwy lub odwołania, można teraz wyświetlić błędy warstwowe we właściwości punktu końcowego serwera.
    - Dodatkowe informacje o warstwie w chmurze są dostępne dla punktu końcowego serwera:
        - Rozmiar lokalnej pamięci podręcznej
        - Wydajność użycia pamięci podręcznej
        - Szczegóły zasad warstwowych w chmurze: rozmiar woluminu, bieżąca ilość wolnego miejsca lub ostatni dostępny czas najstarszego pliku w lokalnej pamięci podręcznej.
    - Te zmiany zostaną wysłane w witrynie Azure portal wkrótce po wydaniu agenta v10 początkowej.

- Obsługa przenoszenia usługi synchronizacji magazynu i/lub konta magazynu do innej dzierżawy usługi Azure Active Directory (AAD)
    - Usługa Azure File Sync obsługuje teraz przenoszenie konta usługi synchronizacji magazynu i/lub magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD.
    
- Różne ulepszenia wydajności i niezawodności
    - Wykrywanie zmian w udziale plików platformy Azure może zakończyć się niepowodzeniem, jeśli reguły sieci wirtualnej (VNET) i zapory są skonfigurowane na koncie magazynu.
    - Mniejsze zużycie pamięci związane z wycofaniem. 
    - Zwiększona wydajność podczas korzystania z polecenia cmdlet [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Inne różne ulepszenia niezawodności. 
    
### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Aby natychmiast zsynchronizować pliki, które zostały zmienione w udziale plików platformy Azure, polecenie cmdlet [invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell może służyć do ręcznego inicjowania wykrywania zmian w udziale plików platformy Azure. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Podczas tworzenia punktu końcowego chmury, usługi synchronizacji magazynu i konta magazynu musi znajdować się w tej samej dzierżawy usługi Azure AD. Po utworzeniu punktu końcowego chmury, usługi synchronizacji magazynu i konta magazynu można przenieść do różnych dzierżaw usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.

## <a name="agent-version-9100"></a>Agent w wersji 9.1.0.0
Poniższe informacje o wersji są dla wersji 9.1.0.0 agenta usługi Azure File Sync wydany 12 grudnia 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 9.0.0.0.

W tej wersji rozwiązana kwestia:  
- Synchronizacja kończy się niepowodzeniem z jednym z następujących błędów po uaktualnieniu do agenta usługi Azure File Sync w wersji 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent w wersji 9.0.0.0
Poniższe informacje o wersji są dla wersji 9.0.0.0 agenta usługi Azure File Sync (wydany 2 grudnia 2019 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa samoobsługowego przywracania
    - Użytkownicy mogą teraz przywracać swoje pliki przy użyciu funkcji poprzedniej wersji. Przed wydaniem wersji 9 poprzednia wersja nie była obsługiwana na woluminach z włączoną warstwą w chmurze. Ta funkcja musi być włączona dla każdego woluminu oddzielnie, na którym istnieje punkt końcowy z włączoną warstwą chmury. Aby dowiedzieć się więcej, zobacz  
[Przywracanie samoobsługowe za pośrednictwem poprzednich wersji i usługi VSS (usługa kopiowania woluminów w tle)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Obsługa większych rozmiarów udziałów plików 
    - Usługa Azure File Sync obsługuje teraz do 64TiB i 100 milionów plików w jednym, synchronizacji przestrzeni nazw.  
 
- Obsługa deduplikacji danych na serwerze 2019 
    - Deduplikacja danych jest teraz obsługiwana z warstwą chmury włączoną w systemie Windows Server 2019. Aby obsługiwać deduplikację danych na woluminach z warstwą chmury, należy zainstalować aktualizację systemu Windows [KB4520062.](https://support.microsoft.com/help/4520062) 
 
- Poprawiono minimalny rozmiar pliku do warstwy 
    - Minimalny rozmiar pliku do warstwy jest teraz oparty na rozmiarze klastra systemu plików (dwukrotnie większy niż rozmiar klastra systemu plików). Na przykład domyślnie rozmiar klastra systemu plików NTFS wynosi 4 KB, wynikowy minimalny rozmiar pliku dla warstwy pliku to 8 KB. 
 
- Polecenie cmdlet testu łączności sieciowej 
    - W ramach konfiguracji synchronizacji plików platformy Azure należy skontaktować się z wieloma punktami końcowymi usługi. Każdy z nich ma własną nazwę DNS, która musi być dostępna dla serwera. Te adresy URL są również specyficzne dla regionu, w który jest zarejestrowany serwer. Po zarejestrowaniu serwera polecenie cmdlet testu łączności (PowerShell i narzędzie rejestracji serwera) może służyć do testowania komunikacji ze wszystkimi adresami URL specyficznymi dla tego serwera. To polecenie cmdlet może pomóc w rozwiązywaniu problemów, gdy niekompletna komunikacja uniemożliwia serwerowi pełną pracę z synchronizacją plików platformy Azure i może służyć do precyzyjnego dostrajania konfiguracji serwera proxy i zapory.  
 
        Aby uruchomić test łączności sieciowej, uruchom następujące polecenia programu PowerShell: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Usuwanie poprawy punktu końcowego serwera po włączeniu warstwowania w chmurze 
    - Jak poprzednio, usunięcie punktu końcowego serwera nie powoduje usunięcia plików w udziale plików platformy Azure. Jednak zachowanie dla punktów ponownejparieny na serwerze lokalnym uległa zmianie. Punkty ponownejpary (wskaźniki do plików, które nie są lokalne na serwerze) są teraz usuwane podczas usuwania punktu końcowego serwera. W pełni buforowane pliki pozostaną na serwerze. To ulepszenie zostało wprowadzone, aby zapobiec [osieroconych plików warstwowych](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) podczas usuwania punktu końcowego serwera. Jeśli punkt końcowy serwera zostanie odtworzony, punkty ponownejparacji dla plików warstwowych zostaną odtworzone na serwerze.  
 
- Ulepszenia wydajności i niezawodności 
    - Zmniejszono liczbę błędów wycofywania. Rozmiar odwołania jest teraz automatycznie dostosowywany na podstawie przepustowości sieci. 
    - Poprawiono wydajność pobierania podczas dodawania nowego serwera do grupy synchronizacji. 
    - Zmniejszono synchronizację plików z powodu konfliktów ograniczeń. 
    - Pliki nie warstwy lub są nieoczekiwanie odwołane w niektórych scenariuszach, jeśli ścieżka punktu końcowego serwera jest punktem instalacji woluminu.
    
### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, ponownie utworzyć punkt końcowy serwera. Jeśli upłynęło 30 dni od usunięcia punktu końcowego serwera lub jeśli punkt końcowy chmury został usunięty, pliki warstwowe, które nie zostały odwołane, będą bezużyteczne. Aby dowiedzieć się więcej, zobacz [Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Aby natychmiast zsynchronizować pliki, które zostały zmienione w udziale plików platformy Azure, polecenie cmdlet [invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell może służyć do ręcznego inicjowania wykrywania zmian w udziale plików platformy Azure. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.
- Pliki mogą zakończyć się niepowodzeniem, jeśli pagefile.sys znajduje się na woluminie, który ma włączoną warstwę chmury. Pagefile.sys powinny znajdować się na woluminie, który ma chmura warstwowa wyłączona.

## <a name="agent-version-8000"></a>Agent w wersji 8.0.0.0
Poniższe informacje o wersji są dla wersji 8.0.0.0 agenta usługi Azure File Sync (wydany 8 października 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Przywracanie ulepszeń wydajności
    - Krótszy czas odzyskiwania w celu odzyskania wykonywanej za pośrednictwem usługi Azure Backup. Przywrócone pliki będą synchronizowane z powrotem do serwerów synchronizacji plików platformy Azure znacznie szybciej. 
- Ulepszone środowisko portalu warstwowego w chmurze  
    - Jeśli masz warstwowe pliki, które nie można odwołać, można teraz wyświetlić błędy odwołania we właściwościach punktu końcowego serwera. Ponadto kondycja punktu końcowego serwera będzie teraz wyświetlać kroki błędu i ograniczenia, jeśli sterownik filtru warstwowego chmury nie jest ładowany na serwerze.
- Prostsza instalacja agenta
    - Moduł programu Az\AzureRM PowerShell nie jest już wymagany do rejestrowania instalacji na serwerze, dzięki czemu instalacja jest prostsza i szybka.
- Różne ulepszenia wydajności i niezawodności

### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, ponownie utworzyć punkt końcowy serwera. Jeśli upłynęło 30 dni od usunięcia punktu końcowego serwera lub jeśli punkt końcowy chmury został usunięty, pliki warstwowe, które nie zostały odwołane, będą bezużyteczne. Aby dowiedzieć się więcej, zobacz [Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Aby natychmiast zsynchronizować pliki, które zostały zmienione w udziale plików platformy Azure, polecenie cmdlet [invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell może służyć do ręcznego inicjowania wykrywania zmian w udziale plików platformy Azure. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.

## <a name="agent-version-7200"></a>Agent w wersji 7.2.0.0
Poniższe informacje o wersji są dla wersji 7.2.0.0 agenta usługi Azure File Sync wydany 24 lipca 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Agent synchronizacji magazynu (FileSyncSvc) ulega awarii, jeśli konfiguracja serwera proxy ma wartość null.
- Punkt końcowy serwera uruchomi BCDR (błąd 0x80c80257 - ECS_E_BCDR_IN_PROGRESS), jeśli wiele punktów końcowych na serwerze ma taką samą nazwę.
- Ulepszenia niezawodności warstw w chmurze.

## <a name="agent-version-7100"></a>Agent w wersji 7.1.0.0
Poniższe informacje o wersji są dla wersji 7.1.0.0 agenta usługi Azure File Sync wydany 12 lipca 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 7.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lokalizacji punktu końcowego serwera lub przeglądanie ich za ok.mb jest powolne w systemie Windows Server 2012 R2. 
- Zwiększone wykorzystanie procesora CPU po zainstalowaniu agenta usługi Azure File Sync w wersji 6.
- Ulepszenia telemetrii warstwowej chmury.
- Różne ulepszenia niezawodności dla warstw w chmurze i synchronizacji.

## <a name="agent-version-7000"></a>Agent w wersji 7.0.0.0
Poniższe informacje o wersji są dla wersji 7.0.0.0 agenta usługi Azure File Sync (wydany 19 czerwca 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa większych rozmiarów udziałów plików
    - W przypadku podglądu większych udziałów plików platformy Azure zwiększamy również nasze limity pomocy technicznej dotyczące synchronizacji plików. W tym pierwszym kroku usługa Azure File Sync obsługuje teraz do 25 TB i 50 milionów plików w jednym, synchronizacji obszaru nazw. Aby ubiegać się o podgląd dużego udziału https://aka.ms/azurefilesatscalesurveyplików, wypełnij ten formularz . 
- Obsługa ustawień zapory i sieci wirtualnej na kontach magazynu
    - Usługa Azure File Sync obsługuje teraz ustawienie zapory i sieci wirtualnej na kontach magazynu. Aby skonfigurować wdrożenie do pracy z ustawieniem zapory i sieci wirtualnej, zobacz [Konfigurowanie ustawień zapory i sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Polecenie cmdlet programu PowerShell do natychmiastowej synchronizacji plików zmienionych w udziale plików platformy Azure
    - Aby natychmiast zsynchronizować pliki, które zostały zmienione w udziale plików platformy Azure, polecenie cmdlet invoke-AzStorageSyncChangeDetection programu PowerShell może służyć do ręcznego inicjowania wykrywania zmian w udziale plików platformy Azure. To polecenie cmdlet jest przeznaczone dla scenariuszy, w których jakiś typ zautomatyzowanego procesu wprowadza zmiany w udziale plików platformy Azure lub zmiany są wykonywane przez administratora (na przykład przenoszenie plików i katalogów do udziału). W przypadku zmian użytkowników końcowych zaleca się zainstalowanie agenta usługi Azure File Sync na maszynie Wirtualnej IaaS i udostępnienie udziału plików przez użytkowników końcowych za pośrednictwem maszyny Wirtualnej IaaS. W ten sposób wszystkie zmiany zostaną szybko zsynchronizowane z innymi agentami bez konieczności używania polecenia cmdlet Invoke-AzStorageSyncChangeDetection. Aby dowiedzieć się więcej, zobacz [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentacji.
- Ulepszone środowisko portalu w przypadku napotkania plików, które nie są synchronizowane
    - Jeśli masz pliki, które nie są synchronizowane, teraz rozróżniamy błędy przejściowe i trwałe w portalu. Błędy przejściowe zwykle rozwiązują się bez konieczności działania administratora. Na przykład plik, który jest obecnie używany, nie zostanie zsynchronizowany, dopóki dojście do pliku nie zostanie zamknięte. W przypadku błędów trwałych pokazujemy teraz liczbę plików, na które ma wpływ każdy błąd. Liczba błędów trwałych jest również wyświetlana w kolumnie pliki nie synchronizujące wszystkich punktów końcowych serwera w grupie synchronizacji.
- Ulepszone przywracanie na poziomie pliku kopii zapasowej platformy Azure
    - Poszczególne pliki przywrócone przy użyciu usługi Azure Backup są teraz wykrywane i synchronizowane z punktem końcowym serwera szybciej.
- Ulepszona niezawodność polecenia cmdlet wycofywania z warstw w chmurze 
    - Polecenie cmdlet Invoke-StorageSyncFileRecall umożliwia teraz klientom określenie liczby ponownych prób na jeden plik i opóźnienia ponawiania próby na jeden plik, podobnie jak robocopy. Wcześniej to polecenie cmdlet przywodzi na myśl wszystkie pliki warstwowe w danej ścieżce w kolejności losowej. Dzięki nowemu parametrowi -Order to polecenie cmdlet najpierw odwoła najgorętsze dane i będzie honorować zasady warstwowe w chmurze (przestań się odwoływać, jeśli zasady daty są spełnione lub ilość wolnego miejsca jest spełniona; w zależności od tego, co nastąpi wcześniej).
- Obsługa tylko protokołu TLS 1.2 (TLS 1.0 i 1.1 są wyłączone)
    - Usługa Azure File Sync obsługuje teraz przy użyciu protokołu TLS 1.2 tylko na serwerach z wyłączonymi usługami TLS 1.0 i 1.1. Przed tym ulepszeniem rejestracja serwera zakończy się niepowodzeniem, jeśli TLS 1.0 i 1.1 zostały wyłączone na serwerze.
- Różne ulepszenia wydajności i niezawodności dla synchronizacji i warstw w chmurze
    - Istnieje kilka ulepszeń niezawodności i wydajności w tej wersji. Niektóre z nich są ukierunkowane, aby warstwy w chmurze bardziej wydajne i Azure File Sync jako całość działa lepiej w takich sytuacjach, gdy masz harmonogram ograniczania przepustowości.

### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, ponownie utworzyć punkt końcowy serwera. Jeśli upłynęło 30 dni od usunięcia punktu końcowego serwera lub jeśli punkt końcowy chmury został usunięty, pliki warstwowe, które nie zostały odwołane, będą bezużyteczne.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.

## <a name="agent-version-6300"></a>Agent w wersji 6.3.0.0
Poniższe informacje o wersji są dla wersji 6.3.0.0 agenta usługi Azure File Sync wydany 27 czerwca 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Uzyskiwanie dostępu do lokalizacji punktu końcowego serwera za przejmującej szybkość serwera za przejmując dostęp do punktu końcowego serwera za 2012 R2 lub przeglądanie jej za jego 2012 r2 
- Zwiększone wykorzystanie procesora po zainstalowaniu agenta usługi Azure File Sync w wersji 6
- Ulepszenia telemetrii warstwowej w chmurze

## <a name="agent-version-6200"></a>Agent w wersji 6.2.0.0
Poniższe informacje o wersji są dla wersji 6.2.0.0 agenta usługi Azure File Sync wydany 13 czerwca 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Po utworzeniu punktu końcowego serwera może wystąpić wysokie użycie procesora CPU podczas pobierania plików na serwer
- Operacje synchronizacji i warstw w chmurze mogą zakończyć się niepowodzeniem z powodu błędu ECS_E_SERVER_CREDENTIAL_NEEDED z powodu wygaśnięcia tokenu
- Odwoływanie pliku może zakończyć się niepowodzeniem, jeśli adres URL do pobrania pliku zawiera znaki zastrzeżone 

## <a name="agent-version-6100"></a>Agent w wersji 6.1.0.0
Poniższe informacje o wersji są dla wersji 6.1.0.0 agenta usługi Azure File Sync wydany 6 maja 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 6.0.0.0.

Lista problemów rozwiązanych w tej wersji:  
- Centrum administracyjne systemu Windows nie wyświetla wersji agenta i konfiguracji punktu końcowego serwera na serwerach z zainstalowanym agentem usługi Azure File Sync w wersji 6.0.

## <a name="agent-version-6000"></a>Agent w wersji 6.0.0.0
Poniższe informacje o wersji są dla wersji 6.0.0.0 agenta usługi Azure File Sync (wydany 22 kwietnia 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa automatycznej aktualizacji agenta
  - Wysłuchaliśmy twojej opinii i dodaliśmy funkcję automatycznej aktualizacji do agenta serwera synchronizacji plików azure. Aby uzyskać więcej informacji, zobacz [zasady aktualizacji agenta usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Obsługa list ACL udziału plików platformy Azure
  - Usługa Azure File Sync zawsze obsługiwała synchronizowanie list ACL między punktami końcowymi serwera, ale listy ACL nie zostały zsynchronizowane z punktem końcowym chmury (udział plików platformy Azure). Ta wersja dodaje obsługę synchronizowania list ACL między punktami końcowymi serwera i chmury.
- Równoległe sesje synchronizacji przekazywania i pobierania dla punktu końcowego serwera 
  - Punkty końcowe serwera obsługują teraz przekazywanie i pobieranie plików w tym samym czasie. Nie więcej czekania na pobranie do ukończenia, dzięki czemu pliki mogą być przekazywane do udziału plików platformy Azure. 
- Nowe polecenia cmdlet warstwowego w chmurze, aby uzyskać stan woluminu i warstw
  - Dwa nowe, lokalne na serwerze polecenia cmdlet programu PowerShell mogą teraz służyć do uzyskiwania informacji o warstwach w chmurze i wycofywaniu plików. Udostępniają one informacje o rejestrowaniu z dwóch kanałów zdarzeń na serwerze:
    - Get-StorageSyncFileTieringResult wyświetli listę wszystkich plików i ich ścieżek, które nie warstwowe i raporty na dlaczego.
    - Get-StorageSyncFileRecallResult zgłasza wszystkie zdarzenia odwołania pliku. Wyświetla listę każdego pliku przypomnieć i jego ścieżkę, jak również sukces lub błąd dla tego odwołania.
  - Domyślnie oba kanały zdarzeń mogą przechowywać do 1 MB każdy — można zwiększyć ilość plików zgłoszonych przez zwiększenie rozmiaru kanału zdarzenia.
- Obsługa trybu FIPS
  - Usługa Azure File Sync obsługuje teraz włączanie trybu FIPS na serwerach z zainstalowanym agentem usługi Azure File Sync.
    - Przed włączeniem trybu FIPS na serwerze należy zainstalować agenta usługi Azure File Sync i [moduł egozarządzania pakietami](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwerze. Jeśli fips jest już włączony na serwerze, [ręcznie pobierz](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) [moduł PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) na serwer.
- Różne ulepszenia niezawodności dla warstw i synchronizacji w chmurze

### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, ponownie utworzyć punkt końcowy serwera. Jeśli upłynęło 30 dni od usunięcia punktu końcowego serwera lub jeśli punkt końcowy chmury został usunięty, pliki warstwowe, które nie zostały odwołane, będą bezużyteczne.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-5200"></a>Agent w wersji 5.2.0.0
Poniższe informacje o wersji są dla wersji 5.2.0.0 agenta usługi Azure File Sync wydany 4 kwietnia 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Ulepszenia niezawodności funkcji transferu danych w trybie offline i wznowienia transferu danych
- Ulepszenia synchronizacji danych telemetrycznych

## <a name="agent-version-5100"></a>Agent w wersji 5.1.0.0
Poniższe informacje o wersji są dla wersji 5.1.0.0 agenta usługi Azure File Sync wydany 7 marca 2019. Te uwagi są dodatkiem do informacji o wersji wymienionej dla wersji 5.0.2.0.

Lista problemów rozwiązanych w tej wersji:  
- Synchronizacja plików z błędem 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) w przypadku niepowodzenia wyliczenia zmian na serwerze
- Jeśli sesja synchronizacji lub plik otrzyma błąd 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), synchronizacja ponowi próbę wykonania operacji
- Synchronizacja plików z błędem 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Podczas przywoływania plików może wystąpić duże użycie pamięci
- Ulepszenia telemetrii warstwowej w chmurze 

## <a name="agent-version-5020"></a>Agent w wersji 5.0.2.0
Poniższe informacje o wersji są dla wersji 5.0.2.0 agenta usługi Azure File Sync (wydany 12 lutego 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa chmury platformy Azure dla instytucji rządowych
  - Dodaliśmy obsługę wersji zapoznawczej dla chmury azure dla instytucji rządowych. Wymaga to subskrypcji na białej liście i pobrania specjalnego agenta od firmy Microsoft. Aby uzyskać dostęp do podglądu, napisz [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)do nas bezpośrednio na .
- Obsługa deduplikacji danych
    - Deduplikacja danych jest teraz w pełni obsługiwana z obsługą warstw w chmurze w systemach Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji na woluminie z włączoną warstwą chmury umożliwia buforowanie większej liczby plików lokalnie bez inicjowania obsługi administracyjnej większej ilości miejsca do magazynowania.
- Obsługa transferu danych w trybie offline (np. za pośrednictwem data box)
    - Łatwo migruj duże ilości danych do usługi Azure File Sync za pomocą dowolnych środków. Można wybrać usługi Azure Data Box, AzCopy, a nawet usługi migracji innych firm. Nie ma potrzeby używania ogromnych ilości przepustowości, aby uzyskać dane na platformie Azure, w przypadku Data Box - po prostu wyślij je tam! Aby dowiedzieć się więcej, zobacz [Dokumenty transferu danych w trybie offline](https://aka.ms/AFS/OfflineDataTransfer).
- Zwiększona wydajność synchronizacji
    - Klienci z wieloma punktami końcowymi serwera na tym samym woluminie mogą mieć wydajność powolnej synchronizacji przed tą wersją. Usługa Azure File Sync tworzy tymczasową migawkę vss raz dziennie na serwerze, aby zsynchronizować pliki, które mają otwarte uchwyty. Synchronizacja obsługuje teraz wiele punktów końcowych serwera synchronizacji na woluminie, gdy sesja synchronizacji vss jest aktywny. Nie więcej oczekiwania na sesji synchronizacji vss, aby zakończyć synchronizację, dzięki czemu można wznowić na innych punktach końcowych serwera na woluminie.
- Ulepszone monitorowanie w portalu
    - Wykresy zostały dodane w portalu usługi synchronizacji magazynu w celu wyświetlenia:
        - Liczba zsynchronizowanych plików
        - Wielkość przesyłanych danych
        - Liczba plików niezsynchronizowanych
        - Wielkość przywoływania danych
        - Stan łączności z serwerem
    - Aby dowiedzieć się więcej, zobacz [Monitorowanie synchronizacji plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Większa skalowalność i niezawodność
    - Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu wzrosła do 1 000 000. Poprzedni limit wynosił 200 000.
    - Synchronizacja spróbuje wznowić transfer danych, a nie retransmisję, gdy transfer zostanie przerwany dla dużych plików 

### <a name="evaluation-tool"></a>Narzędzie ewaluacyjne
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny synchronizacji plików azure. To narzędzie jest polecenie cmdlet programu Azure PowerShell, który sprawdza potencjalne problemy z systemem plików i zestawem danych, takich jak nieobsługiwały znaki lub nieobsługiwione wersji systemu operacyjnego. Aby uzyskać instrukcje instalacji i użytkowania, zobacz [sekcję Narzędzia ewaluacyjne w](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta synchronizacji plików platformy Azure w systemie Windows Server, zobacz [Planowanie wdrożenia synchronizacji plików platformy Azure](storage-sync-files-planning.md) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z uprawnieniami z podwyższonym poziomem uprawnień (administratora).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.
- Usługa Agent synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, na który skompresowany jest katalog informacji o woluminie systemowym (SVI). Ta konfiguracja doprowadzi do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwany i musi być wyłączony. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md).
- Osłony plików Menedżera zasobów serwera plików (FSRM) mogą powodować niekończące się błędy synchronizacji, gdy pliki są blokowane z powodu osłony plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agent usługi Azure File Sync powinien zostać zainstalowany po wdrożeniu obrazu serwera i zakończeniu miniinstalacji sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwałymi znakami. Zobacz [przewodnik rozwiązywania problemów,](storage-sync-files-troubleshoot.md#handling-unsupported-characters) aby uzyskać listę nieobsługiconych znaków.
- Pliki lub katalogi, które kończą się kropką.
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
- Pliki warstwowe staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, ponownie utworzyć punkt końcowy serwera. Jeśli upłynęło 30 dni od usunięcia punktu końcowego serwera lub jeśli punkt końcowy chmury został usunięty, pliki warstwowe, które nie zostały odwołane, będą bezużyteczne.
- Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie jest aktualizowana, jeśli nazwa serwera została zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy chmury
- Usługa Azure File Sync obsługuje wprowadzanie zmian w udziale plików platformy Azure bezpośrednio. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury raz na 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pomocą protokołu REST nie zaktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą postrzegane jako zmiana według synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą roboskopii użyj opcji /MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są warstwowe wcześniej niż ostatnio dostępne pliki.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

---
title: Informacje o wersji dla agenta Azure File Sync | Microsoft Docs
description: Przeczytaj informacje o wersji dla agenta Azure File Sync, który umożliwia scentralizowanie udziałów plików w organizacji w Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435192"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Obsługiwane są następujące wersje agenta Azure File Sync:

| Rozwoju | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Wersja 11.2 — [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 lutego 2021 | Obsługiwane |
| Wersja v 11.1 — [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 listopada 2020 | Obsługiwane |
| V 10.1 wydanie- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 czerwca 2020 | Obsługiwane — wersja agenta wygaśnie w dniu 7 czerwca 2021 |
| 2020 — pakiet zbiorczy aktualizacji — [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 r. | Obsługiwane — wersja agenta wygaśnie w dniu 7 czerwca 2021 |
| V10 wydanie — [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 kwietnia 2020 | Obsługiwane — wersja agenta wygaśnie w dniu 7 czerwca 2021 |

## <a name="unsupported-versions"></a>Nieobsługiwane wersje
Następujące wersje agenta Azure File Sync wygasły i nie są już obsługiwane:

| Rozwoju | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Wersja V9 | 9.0.0.0 - 9.1.0.0 | Nie dotyczy | Nieobsługiwane — wersja agenta wygasła 16 lutego 2021 |
| Wersja V8 | 8.0.0.0 | Nie dotyczy | Nieobsługiwane — wersja agenta wygasła 12 stycznia 2021 |
| Wersja wersji 7 | 7.0.0.0 - 7.2.0.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 1 września 2020 |
| Wydanie w wersji 6 | 6.0.0.0 - 6.3.0.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 21 kwietnia 2020 |
| Wersja V5 | 5.0.2.0 - 5.2.0.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 18 marca, 2020 |
| Wersja v4 | 4.0.1.0 - 4.3.0.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 6 listopada, 2019 |
| Wersja V3 | 3.1.0.0 - 3.4.0.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 19 sierpnia 2019 |
| Agenci wstępnie GA | 1.1.0.0 — 3.0.13.0 | Nie dotyczy | Nieobsługiwane — wersje agenta wygasły 1 października 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>11.2.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 11.2.0.0 agenta Azure File Sync wydanej 2 lutego 2021. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione 
- Jeśli sesja synchronizacji została anulowana z powodu dużej liczby błędów dla elementów, synchronizacja może przechodzić przez uzgadnianie podczas uruchamiania nowej sesji, jeśli usługa Azure File Sync określa niestandardową sesję synchronizacji wymaganą do skorygowania błędów poszczególnych elementów.
- Zarejestrowanie serwera za pomocą polecenia cmdlet Register-AzStorageSyncServer może zakończyć się niepowodzeniem z powodu błędu "nieobsługiwany wyjątek".
- Nowe polecenie cmdlet programu PowerShell (Add-StorageSyncAllowedServerEndpointPath) do konfigurowania dozwolonych ścieżek punktów końcowych serwera na serwerze. To polecenie cmdlet jest przydatne w scenariuszach, w których wdrożenie Azure File Sync jest zarządzane przez dostawcę rozwiązań w chmurze (CSP) lub dostawcę usług, a klient chce skonfigurować na serwerze ścieżki dozwolonych punktów końcowych serwera. W przypadku tworzenia punktu końcowego serwera, jeśli określona ścieżka nie znajduje się na liście dozwolonych, tworzenie punktu końcowego serwera zakończy się niepowodzeniem. Należy pamiętać, że jest to funkcja opcjonalna, a wszystkie obsługiwane ścieżki są domyślnie dozwolone podczas tworzenia punktu końcowego serwera.  

    
    - Aby dodać ścieżkę do punktu końcowego serwera, która jest dozwolona, uruchom następujące polecenia programu PowerShell na serwerze:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Aby uzyskać listę obsługiwanych ścieżek, uruchom następujące polecenie programu PowerShell:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Aby usunąć ścieżkę, uruchom następujące polecenie programu PowerShell:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>11.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 11.1.0.0 agenta Azure File Sync (wydanej 4 listopada 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione
- Nowe tryby obsługi warstw w chmurze, które umożliwiają kontrolowanie początkowego pobierania i aktywnego odwoływania
    - Początkowy tryb pobierania: Możesz teraz wybrać sposób, w jaki pliki mają być początkowo pobierane do nowego punktu końcowego serwera. Czy chcesz, aby wszystkie pliki były w warstwach lub tak jak wiele plików pobieranych na serwer według sygnatury czasowej ostatniej modyfikacji? Możesz to zrobić. Nie można użyć warstw chmurowych? Teraz możesz wybrać opcję uniknięcia plików warstwowych w systemie. Aby dowiedzieć się więcej, zobacz sekcję [Tworzenie punktu końcowego serwera](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) w dokumentacji wdrażania Azure File Sync.
    - Aktywny tryb odwoływania: za każdym razem, gdy plik jest tworzony lub modyfikowany, można go aktywnie odwoływać do serwerów określonych w ramach tej samej grupy synchronizacji. Sprawia to, że plik jest łatwo dostępny do użycia na każdym określonym serwerze. Czy zespoły na całym świecie pracują nad tymi samymi danymi? Włącz aktywne odwoływanie, tak aby kiedy zespół dotarł do następnego dnia rano, wszystkie pliki zaktualizowane przez zespół w innej strefie czasowej są pobierane i gotowe do użycia. Aby dowiedzieć się więcej, zobacz [proaktywne odwoływanie nowych i zmienionych plików z sekcji udział plików platformy Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) w dokumentacji wdrażania Azure File Sync.

- Wykluczanie aplikacji z warstwy Cloud śledzenie czasu ostatniego dostępu możesz teraz wykluczyć aplikacje z ostatniego śledzenia czasu dostępu. Gdy aplikacja uzyskuje dostęp do pliku, czas ostatniego dostępu do pliku zostanie zaktualizowany w bazie danych o warstwach w chmurze. Aplikacje skanujące system plików, takie jak oprogramowanie antywirusowe, powodują, że wszystkie pliki mają taki sam czas ostatniego dostępu, który ma wpływ na warstwowe pliki.

    Aby wykluczyć aplikacje ze śledzenia czasu ostatniego dostępu, Dodaj nazwę procesu do ustawienia rejestru HeatTrackingProcessNameExclusionList, które znajduje się w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Przykład: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Deduplikacja danych i procesy Menedżer zasobów serwera plików (Menedżera zasobów) są domyślnie wykluczone (stałe), a lista wykluczeń procesów jest odświeżana co 5 minut.

- Różne ulepszenia dotyczące wydajności i niezawodności
    - Ulepszona wydajność wykrywania zmian w celu wykrycia plików, które uległy zmianie w udziale plików platformy Azure.
    - Ulepszona wydajność przekazywania synchronizacji.
    - Początkowe przekazywanie jest teraz wykonywane z migawki VSS, która zmniejsza błędy poszczególnych elementów i błędy sesji synchronizacji.
    - Udoskonalenia synchronizacji niektórych wzorców we/wy.
    - Naprawiono usterkę uniemożliwiającą przechodzenie do trybu failover przez bazę danych synchronizacji w przypadku klastrów trybu failover.
    - Ulepszona wydajność odwoływania podczas uzyskiwania dostępu do pliku warstwowego.

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](./storage-sync-files-planning.md#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync przy użyciu systemu Windows Server, zobacz [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

- Pakiet instalacyjny agenta musi być zainstalowany z podniesionymi uprawnieniami (administratora).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna mieć skonfigurowaną minimalną 2048 MiB pamięci. Aby uzyskać więcej informacji, zobacz [zalecane zasoby systemowe](./storage-sync-files-planning.md#recommended-system-resources) .
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
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesieniu usługi synchronizacji magazynu lub konta magazynu musisz nadać aplikacji Microsoft. StorageSync dostęp do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze można przenieść usługę synchronizacji magazynu i konto magazynu do różnych dzierżawców usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.
    > [!Warning]  
    > Przełącznik Robocopy/B nie jest obsługiwany w przypadku Azure File Sync. Użycie przełącznika Robocopy/B z punktem końcowym serwera Azure File Sync jako źródło może prowadzić do uszkodzenia plików.

## <a name="agent-version-10100"></a>10.1.0.0 wersja agenta
Poniższe informacje o wersji dotyczą wersji 10.1.0.0 agenta Azure File Sync wydanej 5 czerwca 2020. Te informacje są uzupełnieniem informacji o wersji wymienionych dla wersji 10.0.0.0 i 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały naprawione

- Obsługa prywatnego punktu końcowego platformy Azure
    - Ruch synchronizacji do usługi synchronizacji magazynu można teraz wysłać do prywatnego punktu końcowego. Umożliwia to tunelowanie za pośrednictwem połączenia ExpressRoute lub sieci VPN. Aby dowiedzieć się więcej, zobacz [Konfigurowanie punktów końcowych sieci Azure File Sync](./storage-sync-files-networking-endpoints.md).
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

- Obsługa przeniesienia usługi synchronizacji magazynu i/lub konta magazynu do innej dzierżawy Azure Active Directory
    - Azure File Sync teraz obsługuje przeniesienie usługi synchronizacji magazynu i/lub konta magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD.
    
- Różne ulepszenia dotyczące wydajności i niezawodności
    - Wykrywanie zmian w udziale plików platformy Azure może się nie powieść, jeśli na koncie magazynu skonfigurowano reguły sieci wirtualnej (VNET) i zapory.
    - Zmniejszone użycie pamięci skojarzone z odwołaniem. 
    - Zwiększona wydajność podczas korzystania z polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Inne różne ulepszenia dotyczące niezawodności. 
    
### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia do oceny Azure File Sync. To narzędzie jest Azure PowerShell poleceniem cmdlet, które sprawdza potencjalne problemy związane z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Instrukcje dotyczące instalacji i użycia znajdują się w sekcji [Narzędzie do oceny](./storage-sync-files-planning.md#evaluation-cmdlet) w przewodniku planowania. 

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
- Azure File Sync obsługuje wprowadzanie zmian bezpośrednio w udziale plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw muszą zostać odnalezione za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell. Ponadto zmiany wprowadzone w udziale plików platformy Azure za pośrednictwem protokołu REST nie będą aktualizować czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesieniu usługi synchronizacji magazynu lub konta magazynu musisz nadać aplikacji Microsoft. StorageSync dostęp do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze można przenieść usługę synchronizacji magazynu i konto magazynu do różnych dzierżawców usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików przy użyciu Robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe plików. Zapewni to, że starsze pliki są dostępne wcześniej niż ostatnio używane pliki.

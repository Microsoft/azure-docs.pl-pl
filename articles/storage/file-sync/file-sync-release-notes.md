---
title: Informacje o wersji Azure File Sync agenta | Microsoft Docs
description: Przeczytaj informacje o wersji agenta Azure File Sync, który umożliwia scentralizowanie udziałów plików organizacji w Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796822"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Obsługiwane są Azure File Sync agentów:

| Kamień milowy | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Wersja 12 — [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 marca 2021 r. | Obsługiwane — flighting (Lot) |
| Wersja 11.3 — [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7 kwietnia 2021 r. | Obsługiwane |
| Wersja 11.2 — [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 lutego 2021 r. | Obsługiwane |
| Wersja 11.1 — [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 listopada 2020 r. | Obsługiwane |
| Wersja 10.1 — [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 czerwca 2020 r. | Obsługiwane — wersja agenta wygaśnie 7 czerwca 2021 r. |
| Zbiorczy aktualizacja z maja 2020 r. [— KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 r. | Obsługiwane — wersja agenta wygaśnie 7 czerwca 2021 r. |
| Wersja 10 — [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 kwietnia 2020 r. | Obsługiwane — wersja agenta wygaśnie 7 czerwca 2021 r. |

## <a name="unsupported-versions"></a>Nieobsługiwane wersje
Następujące wersje Azure File Sync agenta wygasły i nie są już obsługiwane:

| Kamień milowy | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| Wersja 9 | 9.0.0.0 - 9.1.0.0 | Nie dotyczy | Nie jest obsługiwana — wersja agenta wygasła 16 lutego 2021 r. |
| Wersja 8 | 8.0.0.0 | Nie dotyczy | Nie jest obsługiwana — wersja agenta wygasła 12 stycznia 2021 r. |
| Wersja 7 | 7.0.0.0 - 7.2.0.0 | Nie dotyczy | Nie są obsługiwane — wersje agenta wygasły 1 września 2020 r. |
| Wersja 6 | 6.0.0.0 - 6.3.0.0 | Nie dotyczy | Nie są obsługiwane — wersje agenta wygasły 21 kwietnia 2020 r. |
| Wersja 5 | 5.0.2.0 - 5.2.0.0 | Nie dotyczy | Nie są obsługiwane — wersje agenta wygasły 18 marca 2020 r. |
| Wersja 4 | 4.0.1.0 - 4.3.0.0 | Nie dotyczy | Nie są obsługiwane — wersje agenta wygasły 6 listopada 2019 r. |
| Wersja 3 | 3.1.0.0 - 3.4.0.0 | Nie dotyczy | Obsługiwane — wersje agenta wygasły 19 sierpnia 2019 r. |
| Agenci przed gałędą | 1.1.0.0 - 3.0.13.0 | Nie dotyczy | Nie są obsługiwane — wersje agenta wygasły 1 października 2018 r. |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Wersja agenta 12.0.0.0
Poniższe informacje o wersji są dla wersji 12.0.0.0 agenta Azure File Sync (wydanej 26 marca 2021 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane
- Nowe środowisko portalu do konfigurowania zasad dostępu do sieci i połączeń z prywatnym punktem końcowym
    - Teraz możesz użyć portalu, aby wyłączyć dostęp do publicznego punktu końcowego usługi synchronizacji magazynu oraz zatwierdzić, odrzucić i usunąć połączenia prywatnego punktu końcowego. Aby skonfigurować zasady dostępu do sieci i połączenia prywatnego punktu końcowego, otwórz portal usługi synchronizacji magazynu, przejdź do sekcji Ustawienia i kliknij pozycję Sieć.
 
- Obsługa warstw w chmurze dla klastrów woluminów o rozmiarach większych niż 64KiB
    - Obsługa warstw w chmurze obsługuje teraz klastry woluminów o rozmiarach do 2MiB na serwerze 2019. Aby dowiedzieć się więcej, zobacz What is the minimum file size for a file to tier? (Jaki jest minimalny rozmiar pliku do [warstwy?).](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier)
 
- Mierzenie przepustowości i opóźnienia Azure File Sync usługi i konta magazynu
    - Polecenia cmdlet Test-StorageSyncNetworkConnectivity można teraz używać do mierzenia opóźnienia i przepustowości Azure File Sync usługi i konta magazynu. Opóźnienie dla usługi Azure File Sync i konta magazynu jest mierzone domyślnie podczas uruchamiania polecenia cmdlet.  Przepustowość przekazywania i pobierania na konto magazynu jest mierzona przy użyciu parametru "-MeasureBandwidth".
 
        Aby na przykład zmierzyć przepustowość i opóźnienie Azure File Sync usługi i konta magazynu, uruchom następujące polecenia programu PowerShell:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Ulepszone komunikaty o błędach w portalu, gdy tworzenie punktu końcowego serwera kończy się niepowodzeniem
    - Wysłuchaliśmy opinii użytkowników i ulepszyliśmy komunikaty o błędach oraz wskazówki w przypadku niepowodzenia tworzenia punktu końcowego serwera.
 
- Różne ulepszenia wydajności i niezawodności
    - Zwiększona wydajność wykrywania zmian w celu wykrywania plików, które uległy zmianie w udziałach plików platformy Azure.
    - Ulepszenia wydajności sesji synchronizacji uzgodnień. 
    - Ulepszenia synchronizacji w celu zmniejszenia liczby ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED i ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED błędów.
    - Usunięto usterkę, która powoduje uszkodzenie danych, jeśli włączono obsługę warstw w chmurze, a pliki warstwowe są kopiowane przy użyciu narzędzia Robocopy z parametrem /B.
    - Usunięto usterkę, która może powodować niepowodzenie obsługi warstw plików na serwerze 2019, jeśli na woluminie jest włączona deduplikacja danych.
    - Usunięto usterkę, która może powodować niepowodzenie kompresowania plików przez diagnostykę AFSDiag, jeśli rozmiar pliku jest większy niż 2GiB.

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia Azure File Sync oceny. To narzędzie to Azure PowerShell cmdlet, które sprawdza potencjalne problemy z systemem plików i zestawem danych, takie jak nieobsługiwane znaki lub nieobsługiwana wersja systemu operacyjnego. Aby uzyskać instrukcje dotyczące instalacji i użycia, zobacz [sekcję Narzędzie do oceny](file-sync-planning.md#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta usługi Azure File Sync w systemie Windows Server, zobacz Planowanie wdrożenia usługi [Azure File Sync](file-sync-planning.md) i Jak [wdrożyć](file-sync-deployment-guide.md)program Azure File Sync .

- Ponowne uruchomienie jest wymagane w przypadku serwerów z istniejącymi Azure File Sync instalacji agenta.
- Pakiet instalacyjny agenta musi być zainstalowany z podwyższonym poziomem uprawnień (administrator).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, należy skonfigurować maszynę wirtualną z co najmniej 2048 MiB pamięci. Aby [uzyskać więcej informacji, zobacz](file-sync-planning.md#recommended-system-resources) Zalecane zasoby systemowe.
- Usługa Agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja spowoduje nieoczekiwane wyniki.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie Azure File Sync](file-sync-troubleshoot.md).
- Ekrany Resource Manager plików serwera plików (FSRM) mogą powodować nieskończone niepowodzenia synchronizacji, gdy pliki są zablokowane z powodu ekranu plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agenta Azure File Sync należy zainstalować po wdrożeniu obrazu serwera i ukończeniu miniinstalacyjnie programu Sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Listę [nieobsługiwanych](file-sync-troubleshoot.md#handling-unsupported-characters) znaków można znaleźć w przewodniku rozwiązywania problemów.
- Pliki lub katalogi, które kończą się na okres.
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
- Nie należy przechowywać pliku stronicowania systemu operacyjnego ani aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie zostanie zaktualizowana, jeśli nazwa serwera zostanie zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje bezpośrednie wprowadzenie zmian w udziałach plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziałach plików platformy Azure muszą najpierw zostać odnalezione przez Azure File Sync wykrywania zmian. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki zmienione w udziałach plików platformy Azure, można użyć polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell do ręcznego zainicjowania wykrywania zmian w udziałach plików platformy Azure. Ponadto zmiany wprowadzone w udziałach plików platformy Azure za pośrednictwem protokołu REST nie aktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą widoczne jako zmiana w synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesionej usłudze synchronizacji magazynu lub koncie magazynu należy udzielić aplikacji Microsoft.StorageSync dostępu do konta magazynu (zobacz Upewnij się, Azure File Sync ma dostęp do [konta magazynu).](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze usługę synchronizacji magazynu i konto magazynu można przenieść do różnych dzierżaw usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą narzędzia Robocopy użyj opcji /CSV, aby zachować znaczniki czasu pliku. Dzięki temu starsze pliki będą warstwowe wcześniej niż pliki, do których ostatnio uzyskano dostęp.

## <a name="agent-version-11300"></a>Wersja agenta 11.3.0.0
Poniższe informacje o wersji są dla wersji 11.3.0.0 agenta Azure File Sync wydanej 7 kwietnia 2021 r. Te informacje są dodatkiem do informacji o wersji wymienionych w wersji 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane 
Usunięto usterkę, która powoduje uszkodzenie danych, jeśli włączono obsługę warstw w chmurze, a pliki warstwowe są kopiowane przy użyciu narzędzia Robocopy z parametrem /B.

## <a name="agent-version-11200"></a>Wersja agenta 11.2.0.0
Poniższe informacje o wersji są dla wersji 11.2.0.0 agenta Azure File Sync wydanej 2 lutego 2021 r. Te informacje są dodatkiem do informacji o wersji 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane 
- Jeśli sesja synchronizacji zostanie anulowana z powodu dużej liczby błędów niestandardowych elementów, synchronizacja może przejść przez uzgadnianie podczas uruchamiania nowej sesji, jeśli usługa Azure File Sync określi, że niestandardowa sesja synchronizacji jest potrzebna do skorygowania błędów niestandardowych elementów.
- Zarejestrowanie serwera przy użyciu polecenia cmdlet Register-AzStorageSyncServer może się nie powieść z błędem "Nieobsługiwany wyjątek".
- Nowe polecenie cmdlet programu PowerShell (Add-StorageSyncAllowedServerEndpointPath) do konfigurowania dozwolonych ścieżek punktów końcowych serwera na serwerze. To polecenie cmdlet jest przydatne w scenariuszach, w których wdrożenie usługi Azure File Sync jest zarządzane przez dostawcę usług lub dostawcę usług Dostawca rozwiązań w chmurze, a klient chce skonfigurować dozwolone ścieżki punktów końcowych serwera na serwerze. Jeśli podczas tworzenia punktu końcowego serwera określona ścieżka nie znajduje się na liście zezwalania, tworzenie punktu końcowego serwera nie powiedzie się. Należy pamiętać, że jest to funkcja opcjonalna i wszystkie obsługiwane ścieżki są domyślnie dozwolone podczas tworzenia punktu końcowego serwera.  

    
    - Aby dodać dozwoloną ścieżkę punktu końcowego serwera, uruchom następujące polecenia programu PowerShell na serwerze:

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
## <a name="agent-version-11100"></a>Wersja agenta 11.1.0.0
Poniższe informacje o wersji są dla wersji 11.1.0.0 agenta Azure File Sync (wydanej 4 listopada 2020 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane
- Nowe tryby warstw w chmurze do kontrolowania początkowego pobierania i aktywnego odwoływania
    - Tryb pobierania początkowego: możesz teraz wybrać sposób początkowego pobierania plików do nowego punktu końcowego serwera. Chcesz, aby wszystkie pliki zostały pobrane warstwami lub jak najwięcej plików na serwer przez znacznik czasu ostatniej modyfikacji? Możesz to zrobić! Nie można używać warstw w chmurze? Teraz możesz zdecydować się na unikanie plików warstwowych w systemie. Aby dowiedzieć się więcej, [zobacz sekcję Tworzenie punktu końcowego serwera](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) w dokumentacji wdrażania Azure File Sync serwera.
    - Proaktywny tryb odwoływania: zawsze, gdy plik zostanie utworzony lub zmodyfikowany, można aktywnie odwołać go do serwerów, które określisz w tej samej grupie synchronizacji. Dzięki temu plik będzie łatwo dostępny do użycia na każdym określonym serwerze. Czy zespoły na całym świecie pracują nad tym samymi danymi? Włącz aktywne odwoływanie się, aby po przyjściu zespołu następnego ranka wszystkie pliki zaktualizowane przez zespół w innej strefie czasowej zostały pobrane i gotowe do pracy. Aby dowiedzieć się więcej, [zobacz sekcję Aktywne odwoływanie](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) nowych i zmienionych plików z udziału plików platformy Azure w dokumentacji Azure File Sync plików.

- Wykluczanie aplikacji ze śledzenia czasu ostatniego dostępu do warstw w chmurze Teraz możesz wykluczać aplikacje ze śledzenia czasu ostatniego dostępu. Gdy aplikacja uzyskuje dostęp do pliku, czas ostatniego dostępu do pliku jest aktualizowany w bazie danych warstw w chmurze. Aplikacje, które skanują system plików, taki jak oprogramowanie antywirusowe, powodują, że wszystkie pliki mają ten sam czas ostatniego dostępu, który ma wpływ na warstwy plików.

    Aby wykluczyć aplikacje ze śledzenia czasu ostatniego dostępu, dodaj nazwę procesu do ustawienia rejestru HeatTrackingProcessNameExclusionList znajdującego się w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Przykład: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Procesy deduplikacji danych i serwera plików Resource Manager (FSRM) są domyślnie wykluczane (zakodowane natywnie), a lista wykluczeń procesów jest odświeżana co 5 minut.

- Różne ulepszenia wydajności i niezawodności
    - Zwiększona wydajność wykrywania zmian w celu wykrywania plików, które uległy zmianie w udziałach plików platformy Azure.
    - Zwiększona wydajność przekazywania synchronizacji.
    - Wstępne przekazywanie jest teraz wykonywane z migawki usługi VSS, co zmniejsza liczbę błędów 1 elementu i niepowodzenia sesji synchronizacji.
    - Ulepszenia niezawodności synchronizacji dla niektórych wzorców we/wy.
    - Usunięto usterkę uniemożliwiającą synchronizowanie bazy danych z powrotem w czasie w klastrach trybu failover w przypadku wystąpienia trybu failover.
    - Zwiększona wydajność odwoływania podczas uzyskiwania dostępu do pliku warstwowego.

### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu Azure File Sync oceny. To narzędzie to Azure PowerShell cmdlet, które sprawdza potencjalne problemy z systemem plików i zestawem danych, takie jak nieobsługiwane znaki lub nieobsługiwana wersja systemu operacyjnego. Aby uzyskać instrukcje dotyczące instalacji i użycia, zobacz [sekcję Narzędzie do oceny](../file-sync/file-sync-planning.md#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta usługi Azure File Sync w systemie Windows Server, zobacz Planowanie wdrożenia usługi [Azure File Sync](../file-sync/file-sync-planning.md) i Jak [wdrożyć](../file-sync/file-sync-deployment-guide.md)program Azure File Sync .

- Pakiet instalacyjny agenta musi być zainstalowany z podwyższonym poziomem uprawnień (administrator).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, należy skonfigurować maszynę wirtualną z co najmniej 2048 MiB pamięci. Aby [uzyskać więcej informacji, zobacz](../file-sync/file-sync-planning.md#recommended-system-resources) Zalecane zasoby systemowe.
- Usługa Agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja spowoduje nieoczekiwane wyniki.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie Azure File Sync](../file-sync/file-sync-troubleshoot.md).
- Ekrany Resource Manager plików serwera plików (FSRM) mogą powodować nieskończone niepowodzenia synchronizacji, gdy pliki są zablokowane z powodu ekranu plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agenta Azure File Sync należy zainstalować po wdrożeniu obrazu serwera i ukończeniu miniinstalacyjnie programu Sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Listę [nieobsługiwanych](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) znaków można znaleźć w przewodniku rozwiązywania problemów.
- Pliki lub katalogi, które kończą się okresem.
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
- Nie przechowuj pliku stronicowania systemu operacyjnego ani aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie zostanie zaktualizowana, jeśli nazwa serwera zostanie zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje bezpośrednie wprowadzenie zmian w udziałach plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziałach plików platformy Azure muszą najpierw zostać odnalezione przez Azure File Sync wykrywania zmian. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki zmienione w udziałach plików platformy Azure, można użyć polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell do ręcznego zainicjowania wykrywania zmian w udziałach plików platformy Azure. Ponadto zmiany wprowadzone w udziałach plików platformy Azure za pośrednictwem protokołu REST nie aktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą widoczne jako zmiana w synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesionej usłudze synchronizacji magazynu lub koncie magazynu należy udzielić aplikacji Microsoft.StorageSync dostępu do konta magazynu (zobacz Upewnij się, Azure File Sync ma dostęp do [konta magazynu).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze usługę synchronizacji magazynu i konto magazynu można przenieść do różnych dzierżaw usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą narzędzia Robocopy użyj opcji /CSV, aby zachować znaczniki czasu pliku. Dzięki temu starsze pliki będą warstwowe wcześniej niż pliki, do których ostatnio uzyskano dostęp.
    > [!Warning]  
    > Przełącznik Robocopy /B nie jest obsługiwany w Azure File Sync. Użycie przełącznika Robocopy /B z punktem końcowym Azure File Sync jako źródła może prowadzić do uszkodzenia pliku.

## <a name="agent-version-10100"></a>Wersja agenta 10.1.0.0
Poniższe informacje o wersji są dla wersji 10.1.0.0 agenta Azure File Sync wydanej 5 czerwca 2020 r. Te informacje są dodatkiem do informacji o wersji wymienionych w wersjach 10.0.0.0 i 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane

- Obsługa prywatnego punktu końcowego platformy Azure
    - Ruch synchronizowania z usługą synchronizacji magazynu można teraz wysyłać do prywatnego punktu końcowego. Umożliwia to tunelowanie za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby dowiedzieć się więcej, [zobacz Konfigurowanie Azure File Sync końcowych sieci](../file-sync/file-sync-networking-endpoints.md).
- Metryka Synchronizowane pliki będzie teraz wyświetlać postęp, gdy duża synchronizacja jest uruchomiona, a nie na końcu.
- Różne ulepszenia niezawodności instalacji agentów, warstw w chmurze, synchronizacji i telemetrii

## <a name="agent-version-10020"></a>Wersja agenta 10.0.2.0
Poniższe informacje o wersji są dla wersji 10.0.2.0 agenta Azure File Sync wydanej 19 maja 2020 r. Te informacje są dodatkiem do informacji o wersji wymienionych dla wersji 10.0.0.0.

Problem rozwiązany w tej wersji:  
- Agent synchronizacji magazynu (FileSyncSvc) często ulega awarii po zainstalowaniu agenta Azure File Sync w wersji 10.

> [!Note]  
>Ta wersja nie została wyemimowana na serwery, które są skonfigurowane do automatycznego aktualizowania, gdy nowa wersja stanie się dostępna. Aby zainstalować tę aktualizację, użyj katalogu Microsoft Update lub Microsoft Update (instrukcje instalacji można znaleźć w artykule [KB4522412).](https://support.microsoft.com/help/4522412)

## <a name="agent-version-10000"></a>Wersja agenta 10.0.0.0
Poniższe informacje o wersji są dla wersji 10.0.0.0 agenta Azure File Sync (wydanej 9 kwietnia 2020 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały rozwiązane

- Ulepszono postęp synchronizacji w portalu
    - Wraz z wydaniem agenta w wersji 10 Azure Portal wkrótce rozpocznie się wyświetlanie typu uruchomionej sesji synchronizacji. Na przykład pobieranie początkowe, regularne pobieranie, odwoływanie w tle (przypadki szybkiego odzyskiwania po awarii) i podobne. 

- Ulepszone środowisko portalu obsługi warstw w chmurze
    - Jeśli masz pliki, których nie można utworzyć warstwy lub odwołać, możesz teraz wyświetlić błędy warstw we właściwościach punktu końcowego serwera.
    - Dla punktu końcowego serwera są dostępne dodatkowe informacje dotyczące warstw w chmurze:
        - Rozmiar lokalnej pamięci podręcznej
        - Wydajność użycia pamięci podręcznej
        - Szczegóły zasad warstw w chmurze: rozmiar woluminu, bieżące wolne miejsce lub czas ostatniego dostępu najstarszego pliku w lokalnej pamięci podręcznej.
    - Te zmiany zostaną wprowadzone w Azure Portal wkrótce po początkowym wydaniu agenta w wersji 10.

- Obsługa przenoszenia usługi synchronizacji magazynu i/lub konta magazynu do innej dzierżawy Azure Active Directory magazynu
    - Azure File Sync teraz obsługuje przenoszenie usługi synchronizacji magazynu i/lub konta magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD.
    
- Różne ulepszenia wydajności i niezawodności
    - Wykrywanie zmian w udziałach plików platformy Azure może się nie powieść, jeśli na koncie magazynu skonfigurowano reguły sieci wirtualnej i zapory.
    - Mniejsze zużycie pamięci związane z odwoływaniem. 
    - Zwiększona wydajność podczas korzystania z polecenia cmdlet [Invoke-AzStorageSyncChangeDetection.](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Inne różne ulepszenia niezawodności. 
    
### <a name="evaluation-tool"></a>Narzędzie do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu narzędzia Azure File Sync oceny. To narzędzie to Azure PowerShell cmdlet, które sprawdza potencjalne problemy z systemem plików i zestawem danych, takie jak nieobsługiwane znaki lub nieobsługiwana wersja systemu operacyjnego. Aby uzyskać instrukcje dotyczące instalacji i użycia, zobacz [sekcję Narzędzie do oceny](../file-sync/file-sync-planning.md#evaluation-cmdlet) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta Azure File Sync z systemem Windows Server, zobacz Planowanie wdrożenia Azure File Sync [i](../file-sync/file-sync-planning.md) Jak wdrożyć Azure File Sync [.](../file-sync/file-sync-deployment-guide.md)

- Pakiet instalacyjny agenta musi być zainstalowany z podwyższonym poziomem uprawnień (administrator).
- Agent nie jest obsługiwany w przypadku opcji wdrażania serwera Nano Server.
- Agent jest obsługiwany tylko w systemach Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, należy skonfigurować maszynę wirtualną z co najmniej 2048 MiB pamięci.
- Usługa Agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punktów końcowych serwera znajdujących się na woluminie, który ma skompresowany katalog informacji o woluminie systemowym (SVI). Ta konfiguracja spowoduje nieoczekiwane wyniki.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie Azure File Sync](../file-sync/file-sync-troubleshoot.md).
- Ekrany Resource Manager plików serwera plików (FSRM) mogą powodować nieskończone niepowodzenia synchronizacji, gdy pliki są zablokowane z powodu ekranu plików.
- Uruchamianie narzędzia sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Agenta Azure File Sync należy zainstalować po wdrożeniu obrazu serwera i ukończeniu miniinstalacyjnie programu Sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanymi znakami. Listę [nieobsługiwanych](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) znaków można znaleźć w przewodniku rozwiązywania problemów.
- Pliki lub katalogi, które kończą się na okres.
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
- Nie należy przechowywać pliku stronicowania systemu operacyjnego ani aplikacji w lokalizacji punktu końcowego serwera.
- Nazwa serwera w portalu nie zostanie zaktualizowana, jeśli nazwa serwera zostanie zmieniona.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Azure File Sync obsługuje bezpośrednie wprowadzenie zmian w udziałach plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziałach plików platformy Azure muszą zostać najpierw odnalezione przez zadanie Azure File Sync wykrywania zmian. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze co 24 godziny. Aby natychmiast synchronizować pliki zmienione w udziałach plików platformy Azure, można użyć polecenia cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) programu PowerShell do ręcznego zainicjowania wykrywania zmian w udziałach plików platformy Azure. Ponadto zmiany wprowadzone w udziałach plików platformy Azure za pośrednictwem protokołu REST nie aktualizują czasu ostatniej modyfikacji protokołu SMB i nie będą widoczne jako zmiana w synchronizacji.
- Usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesionej usłudze synchronizacji magazynu lub koncie magazynu należy udzielić aplikacji Microsoft.StorageSync dostępu do konta magazynu (zobacz Upewnij się, Azure File Sync ma dostęp do [konta magazynu).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze usługę synchronizacji magazynu i konto magazynu można przenieść do różnych dzierżaw usługi Azure AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą narzędzia Robocopy użyj opcji /CSV, aby zachować znaczniki czasu pliku. Dzięki temu starsze pliki będą warstwowe wcześniej niż pliki, do których ostatnio uzyskano dostęp.

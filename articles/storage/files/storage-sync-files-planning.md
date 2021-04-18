---
title: Planowanie wdrożenia Azure File Sync wdrożenia | Microsoft Docs
description: Zaplanuj wdrożenie za pomocą usługi Azure File Sync, która umożliwia buforowanie kilku udziałów plików platformy Azure na lokalnej maszynie wirtualnej z systemem Windows Server lub w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 9382feeb16f2f6a82b946c05a6b4780866fdda5c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600152"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planowanie wdrażania usługi Azure File Sync

:::row:::
    :::column:::
        [![Rozmowa kwalifikacyjna i pokaz wprowadzający Azure File Sync — kliknij, aby odtworzyć!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync to usługa, która umożliwia buforowanie kilku udziałów plików platformy Azure na lokalnej maszynie wirtualnej z systemem Windows Server lub w chmurze. 
        
        Ten artykuł zawiera wprowadzenie do Azure File Sync i funkcji. Gdy zapoznasz się z Azure File Sync, rozważ zapoznanie się z Azure File Sync [wdrażaniem,](storage-sync-files-deployment-guide.md) aby wypróbować tę usługę.        
    :::column-end:::
:::row-end:::

Pliki będą przechowywane w chmurze w udziałach [plików platformy Azure.](storage-files-introduction.md) Udziałów plików platformy Azure można używać na dwa sposoby: przez bezpośrednie instalowanie tych bez serwera udziałów plików platformy Azure (SMB) lub przez buforowanie udziałów plików platformy Azure lokalnie przy użyciu Azure File Sync. Wybór opcji wdrażania zmienia aspekty, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- **Bezpośrednia** instalacji udziału plików platformy Azure: ponieważ usługa Azure Files zapewnia dostęp do SMB, udziały plików platformy Azure można zainstalować lokalnie lub w chmurze przy użyciu standardowego klienta SMB dostępnego w systemach Windows, macOS i Linux. Ponieważ udziały plików platformy Azure są bez serwera, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- Buforowanie lokalnego udziału plików platformy Azure za pomocą programu **Azure File Sync**: Azure File Sync umożliwia scentralizowanie udziałów plików organizacji w usłudze Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Azure File Sync przekształca lokalną (lub w chmurze) system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. 

## <a name="management-concepts"></a>Pojęcia związane z zarządzaniem
Wdrożenie Azure File Sync ma trzy podstawowe obiekty zarządzania:

- **Udział plików platformy Azure:** udział plików platformy Azure to  bez serwera udział plików w chmurze, który zapewnia punkt końcowy chmury relacji Azure File Sync synchronizacji. Dostęp do plików w udziałach plików platformy Azure można uzyskać bezpośrednio za pomocą protokołu SMB lub FileREST, chociaż zachęcamy do uzyskiwania do nich głównie dostępu za pośrednictwem pamięci podręcznej systemu Windows Server, gdy udział plików platformy Azure jest używany z Azure File Sync. Jest to spowodowane tym, Azure Files obecnie brakuje wydajnego mechanizmu wykrywania zmian, takiego jak system Windows Server, więc bezpośrednie propagowanie zmian w udziałach plików platformy Azure do punktów końcowych serwera będzie trwać długo.
- **Punkt końcowy** serwera: ścieżka w systemie Windows Server synchronizowana z udziałem plików platformy Azure. Może to być określony folder na woluminie lub w katalogu głównym woluminu. Na tym samym woluminie może istnieć wiele punktów końcowych serwera, jeśli ich przestrzenie nazw nie nakładają się na siebie.
- **Grupa synchronizacji:** obiekt definiujący relację synchronizacji między punktem końcowym **chmury** lub udziałem plików platformy Azure i punktem końcowym serwera. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Jeśli na przykład masz dwa różne zestawy plików, które chcesz zarządzać za pomocą usługi Azure File Sync, należy utworzyć dwie grupy synchronizacji i dodać różne punkty końcowe do każdej grupy synchronizacji.

### <a name="azure-file-share-management-concepts"></a>Pojęcia związane z zarządzaniem udziałami plików platformy Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync zarządzania
Grupy synchronizacji są wdrażane w **usługach synchronizacji** magazynu , które są obiektami najwyższego poziomu, które rejestrują serwery do użycia z Azure File Sync i zawierają relacje grupy synchronizacji. Zasób usługi synchronizacji magazynu jest elementu równorzędnego zasobu konta magazynu i można go podobnie wdrożyć w grupach zasobów platformy Azure. Usługa synchronizacji magazynu może tworzyć grupy synchronizacji zawierające udziały plików platformy Azure na wielu kontach magazynu i w wielu zarejestrowanych serwerach z systemem Windows.

Aby można było utworzyć grupę synchronizacji w usłudze synchronizacji magazynu, należy najpierw zarejestrować system Windows Server w usłudze synchronizacji magazynu. Powoduje to utworzenie **zarejestrowanego obiektu serwera,** który reprezentuje relację zaufania między serwerem lub klastrem a usługą synchronizacji magazynu. Aby zarejestrować usługę synchronizacji magazynu, należy najpierw zainstalować agenta Azure File Sync na serwerze. Pojedynczy serwer lub klaster można jednocześnie zarejestrować tylko w jednej usłudze synchronizacji magazynu.

Grupa synchronizacji zawiera jeden punkt końcowy w chmurze lub udział plików platformy Azure i co najmniej jeden punkt końcowy serwera. Obiekt punktu końcowego serwera zawiera  ustawienia, które konfigurują możliwość obsługi warstw w chmurze, która zapewnia możliwość buforowania Azure File Sync. Aby można było synchronizować z udziałem plików platformy Azure, konto magazynu zawierające udział plików platformy Azure musi znajdować się w tym samym regionie świadczenia usługi Azure co usługa synchronizacji magazynu.

> [!Important]  
> Możesz wprowadzić zmiany w przestrzeni nazw dowolnego punktu końcowego chmury lub punktu końcowego serwera w grupie synchronizacji i zsynchronizować pliki z innymi punktami końcowymi w grupie synchronizacji. Jeśli bezpośrednio wprowadzasz zmiany w punkcie końcowym w chmurze (udział plików platformy Azure), zmiany należy najpierw wykryć za pomocą Azure File Sync wykrywania zmian. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, [zobacz Azure Files często zadawanych pytań.](storage-files-faq.md#afs-change-detection)

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Weź pod uwagę liczbę wymaganych usług synchronizacji magazynu
W poprzedniej sekcji omówiono podstawowy zasób do skonfigurowania dla usługi Azure File Sync: usługa *synchronizacji magazynu.* System Windows Server można zarejestrować tylko w jednej usłudze synchronizacji magazynu. Dlatego często najlepiej jest wdrożyć tylko jedną usługę synchronizacji magazynu i zarejestrować wszystkie jej serwery. 

Utwórz wiele usług synchronizacji magazynu tylko wtedy, gdy masz:
* różne zestawy serwerów, które nigdy nie mogą wymieniać między sobą danych. W tym przypadku chcesz zaprojektować system tak, aby wykluczał niektóre zestawy serwerów do synchronizacji z udziałem plików platformy Azure, który jest już w użyciu jako punkt końcowy w chmurze w grupie synchronizacji w innej usłudze synchronizacji magazynu. Innym sposobem na to jest to, że serwery z systemem Windows zarejestrowane w innej usłudze synchronizacji magazynu nie mogą synchronizować się z tym samym udziałem plików platformy Azure.
* potrzeba większej liczby zarejestrowanych serwerów lub grup synchronizacji niż może obsługiwać pojedyncza usługa synchronizacji magazynu. Przejrzyj cele [Azure File Sync skalowania,](storage-files-scale-targets.md#azure-file-sync-scale-targets) aby uzyskać więcej szczegółów.

## <a name="plan-for-balanced-sync-topologies"></a>Planowanie zrównoważonych topologii synchronizacji
Przed wdrożeniem jakichkolwiek zasobów należy zaplanować to, co zostanie zsynchronizowane na serwerze lokalnym, z którym udział plików platformy Azure. Tworzenie planu pomoże określić, ile kont magazynu, udziałów plików platformy Azure i zasobów synchronizacji będzie potrzebnych. Te zagadnienia są nadal istotne, nawet jeśli dane nie znajdują się obecnie w systemie Windows Server lub na serwerze, który ma być długotrwały. Sekcja [migracji może](#migration) pomóc w określeniu odpowiednich ścieżek migracji dla twojej sytuacji.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Zagadnienia dotyczące serwera plików systemu Windows
Aby włączyć funkcję synchronizacji w systemie Windows Server, należy zainstalować Azure File Sync agenta do pobrania. Agent Azure File Sync udostępnia dwa główne składniki: usługę systemu Windows w tle odpowiedzialną za monitorowanie zmian w punktach końcowych serwera i inicjowanie sesji synchronizacji oraz filtr systemu plików, który umożliwia obsługę warstw w chmurze i szybkie odzyskiwanie po `FileSyncSvc.exe` `StorageSync.sys` awarii.  

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego
Azure File Sync są obsługiwane w następujących wersjach systemu Windows Server:

| Wersja | Obsługiwane jednostki SKU | Obsługiwane opcje wdrażania |
|---------|----------------|------------------------------|
| Windows Server 2019 | Centrum danych, standard i IoT | Pełne i Podstawowe |
| Windows Server 2016 | Serwer centrum danych, standardu i magazynu | Pełne i Podstawowe |
| Windows Server 2012 z dodatkiem R2 | Serwer centrum danych, standardu i magazynu | Pełne i Podstawowe |

Przyszłe wersje systemu Windows Server zostaną dodane w czasie ich wydań.

> [!Important]  
> Zalecamy, aby wszystkie serwery, których używasz z Azure File Sync, było na bieżąco z najnowszymi aktualizacjami z Windows Update. 

### <a name="minimum-system-resources"></a>Minimalne zasoby systemowe
Azure File Sync wymaga serwera fizycznego lub wirtualnego z co najmniej jednym procesorem CPU i co najmniej 2 GiB pamięci.

> [!Important]  
> Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, należy skonfigurować maszynę wirtualną z co najmniej 2048 MiB pamięci.

W przypadku większości obciążeń produkcyjnych nie zalecamy konfigurowania serwera synchronizacji Azure File Sync z minimalnymi wymaganiami. Aby [uzyskać więcej informacji, zobacz Zalecane](#recommended-system-resources) zasoby systemowe.

### <a name="recommended-system-resources"></a>Zalecane zasoby systemowe
Podobnie jak każda funkcja serwera lub aplikacja, wymagania dotyczące zasobów systemowych Azure File Sync są określane przez skalę wdrożenia; większe wdrożenia na serwerze wymagają większych zasobów systemowych. W Azure File Sync skala jest określana na podstawie liczby obiektów w punktach końcowych serwera i współczynnika zmian w zestawie danych. Pojedynczy serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji, a liczba obiektów wymienionych w poniższej tabeli odpowiada pełnej przestrzeni nazw, do których jest dołączony serwer. 

Na przykład punkt końcowy serwera A z 10 milionami obiektów + punkt końcowy serwera B z 10 milionami obiektów = 20 milionów obiektów. W tym przykładowym wdrożeniu zalecamy 8 procesorów CPU, 16 GiB pamięci dla stanu stałego i (jeśli to możliwe) 48 GiB pamięci dla początkowej migracji.
 
Dane przestrzeni nazw są przechowywane w pamięci ze względu na wydajność. W związku z tym większe przestrzenie nazw wymagają większej ilości pamięci w celu utrzymania dobrej wydajności, a większa ilość zmian wymaga przetwarzania większej ilości procesora CPU. 
 
W poniższej tabeli podano zarówno rozmiar przestrzeni nazw, jak i konwersję na pojemność dla typowych udziałów plików ogólnego przeznaczenia, gdzie średni rozmiar pliku wynosi 512 KiB. Jeśli rozmiary plików są mniejsze, rozważ dodanie dodatkowej pamięci dla tej samej pojemności. Na podstawie konfiguracji pamięci należy opierać się na rozmiarze przestrzeni nazw.

| Rozmiar przestrzeni nazw — & katalogów (miliony)  | Typowa pojemność (TiB)  | Rdzenie procesora CPU  | Zalecana pamięć (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (synchronizacja początkowa)/ 2 (typowy rezygnacja)      |
| 5        | 2.3     | 2        | 16 (synchronizacja początkowa)/ 4 (typowy rezygnacja)    |
| 10       | 4.7     | 4        | 32 (synchronizacja początkowa)/ 8 (typowy rezygnacja)   |
| 30       | 14,0    | 8        | 48 (synchronizacja początkowa)/ 16 (typowy rezygnacja)   |
| 50       | 23,3    | 16       | 64 (synchronizacja początkowa)/ 32 (typowy rezygnacja)  |
| 100*     | 46.6    | 32       | 128 (synchronizacja początkowa)/ 32 (typowy rezygnacja)  |

\*Synchronizowanie ponad 100 milionów plików & katalogami nie jest obecnie zalecane. Jest to limit miękki oparty na przetestowanych progach. Aby uzyskać więcej informacji, [zobacz Azure Files cele dotyczące skalowalności i wydajności.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> Synchronizacja początkowa przestrzeni nazw jest operacją intensywnie obciążaną i zalecamy przydzielenie większej ilości pamięci do czasu ukończenia synchronizacji początkowej. Nie jest to wymagane, ale może przyspieszyć synchronizację początkową. 
> 
> Typowy rezygnacja to 0,5% zmieniającej się przestrzeni nazw dziennie. W przypadku wyższych poziomów zmian warto rozważyć dodanie większej liczby procesorów CPU. 

- Wolumin dołączony lokalnie sformatowany przy użyciu systemu plików NTFS.

### <a name="evaluation-cmdlet"></a>Polecenie cmdlet oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu Azure File Sync cmdlet oceny. To polecenie cmdlet sprawdza potencjalne problemy z systemem plików i zestawem danych, takie jak nieobsługiwane znaki lub nieobsługiwana wersja systemu operacyjnego. Testy obejmują większość, ale nie wszystkie funkcje wymienione poniżej; Zalecamy dokładne przeczytanie pozostałej części tej sekcji, aby upewnić się, że wdrożenie przebiega bezproblemowo. 

Polecenie cmdlet oceny można zainstalować, instalując moduł Az programu PowerShell, który można zainstalować zgodnie z instrukcjami podanymi tutaj: Instalowanie i konfigurowanie programu [Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Użycie  
Narzędzie do oceny można wywołać na kilka różnych sposobów: testy systemowe, testy zestawu danych lub oba te sposoby. Aby przeprowadzić sprawdzanie zarówno systemu, jak i zestawu danych: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Aby przetestować tylko zestaw danych:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Aby przetestować tylko wymagania systemowe:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Aby wyświetlić wyniki w pliku CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Zgodność systemu plików
Azure File Sync jest obsługiwana tylko na bezpośrednio dołączonych woluminach NTFS. Bezpośrednio dołączony magazyn (DAS) w systemie Windows Server oznacza, że system operacyjny Windows Server jest właścicielem systemu plików. Usługi DAS można dostarczać za pośrednictwem fizycznego dołączania dysków do serwera plików, dołączania dysków wirtualnych do maszyny wirtualnej serwera plików (takiej jak maszyna wirtualna hostowana przez funkcji Hyper-V) lub nawet za pośrednictwem interfejsu ISCSI.

Obsługiwane są tylko woluminy NTFS; Systemy plików ReFS, FAT, FAT32 i inne nie są obsługiwane.

W poniższej tabeli przedstawiono stan między działaniami funkcji systemu plików NTFS: 

| Cecha | Stan obsługi | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Listy kontroli dostępu w stylu systemu Windows są zachowywane przez Azure File Sync i są wymuszane przez system Windows Server w punktach końcowych serwera. Adresy ACL można również wymusić podczas bezpośredniego instalowanie udziału plików platformy Azure, jednak wymaga to dodatkowej konfiguracji. Aby uzyskać [więcej informacji, zobacz](#identity) sekcję Tożsamość. |
| Twarde linki | Pominięto | |
| Linki symboliczne | Pominięto | |
| Punkty instalacji | Częściowo obsługiwane | Punkty instalacji mogą być katalogami głównymi punktu końcowego serwera, ale są pomijane, jeśli znajdują się w przestrzeni nazw punktu końcowego serwera. |
| Skrzyżowania | Pominięto | Na przykład rozproszony system plików foldery DfrsrPrivate i DFSRoots. |
| Punkty ponownej analizy | Pominięto | |
| Kompresja NTFS | W pełni obsługiwane | |
| Pliki rozrzednia | W pełni obsługiwane | Pliki rozrzedzione są synchronizowane (nie są blokowane), ale są synchronizowane z chmurą jako pełny plik. Jeśli zawartość pliku zmieni się w chmurze (lub na innym serwerze), plik nie będzie już rozrzedowy po pobraniu zmiany. |
| Alternatywne strumienie danych (ADS) | Zachowane, ale nie zsynchronizowane | Na przykład tagi klasyfikacji utworzone przez infrastrukturę klasyfikacji plików nie są synchronizowane. Istniejące tagi klasyfikacji w plikach w poszczególnych punktach końcowych serwera są pozostawiane bez zmian. |

<a id="files-skipped"></a>Azure File Sync również pominąć niektóre pliki tymczasowe i foldery systemowe:

| Plik/folder | Uwaga |
|-|-|
| pagefile.sys | Plik specyficzny dla systemu |
| Desktop.ini | Plik specyficzny dla systemu |
| Thumbs.db | Plik tymczasowy dla miniatur |
| ehthumbs.db | Plik tymczasowy dla miniatur multimediów |
| ~$\*.\* | Plik tymczasowy pakietu Office |
| \*.tmp | Plik tymczasowy |
| \*.laccdb | Uzyskiwanie dostępu do pliku blokowania bazy danych|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Plik synchronizacji wewnętrznej|
| \\Informacje o woluminie systemowym | Folder specyficzny dla woluminu |
| $RECYCLE. Bin| Folder |
| \\SyncShareState | Folder do synchronizacji |

### <a name="failover-clustering"></a>Klaster trybu failover
Klaster trybu failover systemu Windows Server jest obsługiwany Azure File Sync dla opcji wdrażania "Serwer plików do użytku ogólnego". Klaster trybu failover nie jest obsługiwany na "Serwer plików skalowalny w poziomie danych aplikacji" (SOFS) ani na udostępnionych woluminach klastra (CSV).

> [!Note]  
> Agent Azure File Sync musi być zainstalowany w każdym węźle klastra trybu failover, aby synchronizacja działała prawidłowo.

### <a name="data-deduplication"></a>Deduplikacja danych
**Windows Server 2016 i Windows Server 2019**   
Deduplikacja danych jest obsługiwana niezależnie od tego, czy obsługę warstw w chmurze włączono, czy wyłączono w co najmniej jednym punkcie końcowym serwera na woluminie dla systemów Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji danych na woluminie z włączoną obsługą warstw w chmurze umożliwia buforowanie większej liczby plików lokalnie bez aprowizowania większej ilości miejsca do magazynowania. 

Po włączeniu deduplikacji danych na woluminie z włączoną obsługą warstw w chmurze pliki zoptymalizowane pod kątem deduplikacji w lokalizacji punktu końcowego serwera będą warstwami podobnymi do normalnego pliku na podstawie ustawień zasad obsługi warstw w chmurze. Po podaniu warstw plików zoptymalizowanych pod kątem deduplikacji zadanie odzyskiwania pamięci deduplikacji danych zostanie automatycznie uruchomione w celu odzyskania miejsca na dysku przez usunięcie niepotrzebnych fragmentów, które nie są już przywoływały innych plików na woluminie.

Należy pamiętać, że oszczędności woluminu dotyczą tylko serwera; Dane w udziałach plików platformy Azure nie będą dedumentowane.

> [!Note]  
> Aby zapewnić obsługę deduplikacji danych na woluminach z włączoną obsługą warstw w chmurze w systemie Windows Server 2019, należy zainstalować aktualizację [KB4520062](https://support.microsoft.com/help/4520062) systemu Windows — październik 2019 r. lub nowszą comiesięcznych aktualizacji zbiorczych i wymagane jest agenta programu Azure File Sync w wersji 12.0.0.0 lub nowszej.

**Windows Server 2012 z dodatkiem R2**  
Azure File Sync nie obsługuje deduplikacji danych i obsługi warstw w chmurze na tym samym woluminie w systemie Windows Server 2012 R2. Jeśli deduplikacja danych jest włączona na woluminie, należy wyłączyć obsługę warstw w chmurze. 

**Uwagi**
- Jeśli deduplikacja danych jest zainstalowana przed zainstalowaniem agenta programu Azure File Sync, do obsługi deduplikacji danych i obsługi warstw w chmurze na tym samym woluminie jest wymagane ponowne uruchomienie.
- Jeśli deduplikacja danych jest włączona na woluminie po włączeniu obsługi warstw w chmurze, początkowe zadanie optymalizacji deduplikacji optymalizuje pliki na woluminie, który nie jest jeszcze warstwowy i będzie miało następujący wpływ na obsługę warstw w chmurze:
    - Zasady dotyczące wolnego miejsca będą w dalszym ciągu warstw plików według wolnego miejsca na woluminie przy użyciu mapy cieplnej.
    - Zasady daty pomijają tworzenie warstw plików, które w przeciwnym razie kwalifikują się do korzystania z warstw ze względu na zadanie optymalizacji deduplikacji, które uzyskuje dostęp do plików.
- W przypadku bieżących zadań optymalizacji deduplikacji warstwy w chmurze z zasadami daty zostaną opóźnione przez ustawienie [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) deduplikacji danych, jeśli plik nie jest jeszcze warstwowy. 
    - Przykład: jeśli ustawienie MinimumFileAgeDays ma wartość siedem dni, a zasady daty warstw w chmurze to 30 dni, zasady daty będą warstwami plików po upływie 37 dni.
    - Uwaga: gdy plik jest warstwowy Azure File Sync, zadanie optymalizacji deduplikacji pominie plik.
- Jeśli serwer z systemem Windows Server 2012 R2 z zainstalowanym agentem usługi Azure File Sync został uaktualniony do systemu Windows Server 2016 lub Windows Server 2019, należy wykonać następujące kroki w celu obsługi deduplikacji danych i obsługi warstw w chmurze na tym samym woluminie:  
    - Odinstaluj Azure File Sync agenta dla systemu Windows Server 2012 R2 i uruchom ponownie serwer.
    - Pobierz agenta Azure File Sync dla nowej wersji systemu operacyjnego serwera (Windows Server 2016 lub Windows Server 2019).
    - Zainstaluj agenta Azure File Sync i uruchom ponownie serwer.  
    
    Uwaga: Azure File Sync konfiguracji serwera są zachowywane po odinstalowaniu i ponownym zainstalowaniu agenta.

### <a name="distributed-file-system-dfs"></a>rozproszony system plików (DFS)
Azure File Sync obsługuje międzyoptyk z przestrzeniami nazw systemu plików DFS (DFS-N) i Replikacja systemu plików DFS (DFS-R).

**Przestrzenie nazw systemu plików DFS (DFS-N):** Azure File Sync jest w pełni obsługiwana na serwerach DFS-N. Agenta usługi można zainstalować Azure File Sync jednym lub większej liczby elementów członkowskich DFS-N, aby synchronizować dane między punktami końcowymi serwera a punktem końcowym w chmurze. Aby uzyskać więcej informacji, zobacz [Przestrzenie nazw systemu plików DFS — omówienie.](/windows-server/storage/dfs-namespaces/dfs-overview)
 
**Replikacja systemu plików DFS (DFS-R):** ponieważ usługi DFS-R i Azure File Sync są rozwiązaniami replikacji, w większości przypadków zaleca się zastąpienie plików DFS-R Azure File Sync. Istnieje jednak kilka scenariuszy, w których warto używać systemu plików DFS-R i Azure File Sync razem:

- Jest migrowanie z wdrożenia systemu plików DFS-R do wdrożenia Azure File Sync wdrożenia. Aby uzyskać więcej informacji, zobacz [Migrowanie wdrożenia Replikacja systemu plików DFS (DFS-R) do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy serwer lokalnie, który wymaga kopii danych pliku, może być połączony bezpośrednio z Internetem.
- Serwery gałęzi konsolidują dane na jednym serwerze koncentratora, dla którego chcesz używać Azure File Sync.

Aby Azure File Sync i DFS-R działały obok siebie:

1. Azure File Sync w chmurze musi być wyłączona na woluminach z folderami replikowanym systemu plików DFS-R.
2. Punkty końcowe serwera nie powinny być skonfigurowane w folderach replikacji tylko do odczytu systemu plików DFS-R.

Aby uzyskać więcej informacji, zobacz [Replikacja systemu plików DFS omówienie systemu](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
Używanie narzędzia sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Instalacja agenta i rejestracja serwera powinny nastąpić po wdrożeniu obrazu serwera i ukończeniu miniinstalacyjnie instalatora sysprep.

### <a name="windows-search"></a>Usługa Windows Search
Jeśli obsługę warstw w chmurze włączono w punkcie końcowym serwera, pliki, które są warstwowe, są pomijane i nie indeksowane przez Windows Search. Pliki niewarstwowe są prawidłowo indeksowane.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Inne rozwiązania do hierarchicznego zarządzania magazynem (HSM)
Nie należy używać żadnych innych rozwiązań modułu HSM z Azure File Sync.

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Ponieważ agent usługi Azure File Sync działa na maszynie z systemem Windows Server, która łączy się z udziałami plików platformy Azure, wydajność efektywnej synchronizacji zależy od wielu czynników w infrastrukturze: systemu Windows Server i podstawowej konfiguracji dysku, przepustowości sieci między serwerem a magazynem platformy Azure, rozmiaru pliku, całkowitego rozmiaru zestawu danych i działania na zestawie danych. Ponieważ Azure File Sync działa na poziomie plików, charakterystyki wydajności rozwiązania opartego na Azure File Sync są lepiej mierzone w liczbie przetworzonych obiektów (plików i katalogów) na sekundę.

Zmiany wprowadzone w udziałach plików platformy Azure przy użyciu Azure Portal lub SMB nie są natychmiast wykrywane i replikowane jak zmiany w punkcie końcowym serwera. Azure Files nie ma jeszcze powiadomień o zmianie ani dziennika, więc nie ma możliwości automatycznego zainicjowania sesji synchronizacji po zmianie plików. W systemie Windows Server usługa Azure File Sync [dzienników USN](/windows/win32/fileio/change-journals) systemu Windows w celu automatycznego zainicjowania sesji synchronizacji po zmianie plików

Aby wykryć zmiany w udziałach plików platformy Azure, Azure File Sync ma zaplanowane zadanie o nazwie zadanie wykrywania zmian. Zadanie wykrywania zmian wylicza każdy plik w udziałach plików, a następnie porównuje go z wersją synchronizacji dla tego pliku. Gdy zadanie wykrywania zmian ustali, że pliki zostały zmienione, Azure File Sync inicjuje sesję synchronizacji. Zadanie wykrywania zmian jest inicjowane co 24 godziny. Ponieważ zadanie wykrywania zmian działa przez wyliczenie każdego pliku w udziałach plików platformy Azure, wykrywanie zmian trwa dłużej w większych przestrzeniach nazw niż w mniejszych przestrzeniach nazw. W przypadku dużych przestrzeni nazw ustalenie, które pliki zostały zmienione, może trwać dłużej niż co 24 godziny.

Aby uzyskać więcej informacji, [zobacz Azure File Sync metryki wydajności](storage-files-scale-targets.md#azure-file-sync-performance-metrics) i cele Azure File Sync [skalowania](storage-files-scale-targets.md#azure-file-sync-scale-targets)

## <a name="identity"></a>Tożsamość
Azure File Sync współpracuje ze standardową tożsamością opartą na u usługach AD bez żadnej specjalnej konfiguracji poza skonfigurowaniem synchronizacji. W przypadku korzystania z Azure File Sync ogólnie oczekiwano, że większość dostępu przechodzi przez serwery Azure File Sync buforowania, a nie za pośrednictwem udziału plików platformy Azure. Ponieważ punkty końcowe serwera znajdują się w systemie Windows Server, a system Windows Server od dłuższego czasu obsługuje biblioteki ACL w stylu usług AD i systemu Windows, nic nie jest potrzebne poza zapewnieniem, że serwery plików systemu Windows zarejestrowane w usłudze synchronizacji magazynu są przyłączone do domeny. Azure File Sync będą przechowywać adresy ACL w plikach w udziałach plików platformy Azure i replikować je do wszystkich punktów końcowych serwera.

Mimo że synchronizacja zmian wprowadzonych bezpośrednio w udziałach plików platformy Azure z punktami końcowymi serwera w grupie synchronizacji będzie trwać dłużej, warto również upewnić się, że można wymusić uprawnienia usługi AD do udziału plików bezpośrednio w chmurze. W tym celu należy dołączyć konto magazynu do lokalnej usługi AD w taki sam sposób, jak serwery plików systemu Windows są przyłączone do domeny. Aby dowiedzieć się więcej na temat przyłączanie konta magazynu do należącej do klienta usługi Active Directory, zobacz [Azure Files Active Directory overview (Omówienie](storage-files-active-directory-overview.md)usługi Active Directory).

> [!Important]  
> Domeny przyłączanie konta magazynu do usługi Active Directory nie jest wymagane do pomyślnego wdrożenia Azure File Sync. Jest to ściśle opcjonalny krok, który pozwala udziałowi plików platformy Azure wymuszać lokalne adresy ACL, gdy użytkownicy bezpośrednio zainstalują udział plików platformy Azure.

## <a name="networking"></a>Sieć
Agent Azure File Sync komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST Azure File Sync i protokołu FileREST, które zawsze używają protokołu HTTPS przez port 443. Funkcja SMB nigdy nie jest używana do przekazywania ani pobierania danych między systemem Windows Server i udziałem plików platformy Azure. Ponieważ większość organizacji zezwala na ruch HTTPS przez port 443, ze względu na konieczność odwiedzania większości witryn sieci Web, do wdrożenia sieci nie jest zazwyczaj wymagana specjalna Azure File Sync.

W zależności od zasad organizacji lub unikatowych wymagań prawnych może być konieczne bardziej restrykcyjna komunikacja z platformą Azure, Azure File Sync udostępnia kilka mechanizmów konfigurowania sieci. W zależności od wymagań można:

- Synchronizacja tunelu i ruch przekazywania/pobierania plików za pośrednictwem usługi ExpressRoute lub sieci VPN platformy Azure. 
- Korzystanie z Azure Files i Azure Networking, takich jak punkty końcowe usługi i prywatne punkty końcowe.
- Skonfiguruj Azure File Sync do obsługi serwera proxy w środowisku.
- Ograniczanie aktywności sieci z Azure File Sync.

Aby dowiedzieć się więcej na Azure File Sync i sieci, zobacz [Azure File Sync dotyczące sieci.](storage-sync-files-networking-overview.md)

## <a name="encryption"></a>Szyfrowanie
W przypadku korzystania z usługi Azure File Sync istnieją trzy różne warstwy szyfrowania, które należy wziąć pod uwagę: szyfrowanie w magazynie magazynowym systemu Windows Server, szyfrowanie podczas przesyłania między agentem usługi Azure File Sync i platformą Azure oraz szyfrowanie danych magazynowych w udziałach plików platformy Azure. 

### <a name="windows-server-encryption-at-rest"></a>Szyfrowanie w spoczynku w systemie Windows Server 
Istnieją dwie strategie szyfrowania danych w systemie Windows Server, które ogólnie działają z usługą Azure File Sync: szyfrowanie pod systemem plików, takie jak szyfrowanie systemu plików i wszystkich zapisywanych w nim danych oraz szyfrowanie w samym formacie pliku. Te metody nie wykluczają się wzajemnie; W razie potrzeby można ich używać razem, ponieważ cel szyfrowania jest inny.

Aby zapewnić szyfrowanie pod systemem plików, system Windows Server udostępnia skrzynkę odbiorczą funkcji BitLocker. BitLocker jest w pełni niewidoczny dla Azure File Sync. Głównym powodem korzystania z mechanizmu szyfrowania, takiego jak bitLocker, jest zapobieganie fizycznej eksfiltracji danych z lokalnego centrum danych przez kradzież dysków przez osobę niepowołaną oraz zapobieganie ładowaniu bezpośrednio nieautoryzowanego systemu operacyjnego w celu wykonania nieautoryzowanych operacji odczytu/zapisu danych. Aby dowiedzieć się więcej na temat funkcji BitLocker, zobacz [Omówienie funkcji BitLocker.](/windows/security/information-protection/bitlocker/bitlocker-overview)

Produkty innych firm, które działają podobnie do funkcji BitLocker, ponieważ są one pod woluminem NTFS, powinny podobnie działać w pełni w sposób niewidoczny dla Azure File Sync. 

Drugą główną metodą szyfrowania danych jest szyfrowanie strumienia danych pliku podczas zapisania pliku przez aplikację. Niektóre aplikacje mogą robić to natywnie, jednak zwykle nie jest to przypadek. Przykładem metody szyfrowania strumienia danych pliku jest Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Głównym powodem używania mechanizmu szyfrowania, takiego jak AIP/RMS, jest zapobieganie eksfiltracji danych z udziału plików przez kopiowanie ich do lokalizacji alternatywnych, takich jak dysk flash, lub wysyłanie ich pocztą e-mail do nieautoryzowanej osoby. Gdy strumień danych pliku jest szyfrowany w ramach formatu pliku, ten plik będzie nadal szyfrowany w udziałach plików platformy Azure. 

Azure File Sync nie współdziała z szyfrowanym systemem plików NTFS (NTFS EFS) ani rozwiązaniami szyfrowania innych firm, które znajdują się nad systemem plików, ale poniżej strumienia danych pliku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!NOTE]
> Azure File Sync usługa usunie obsługę TLS1.0 i 1.1 od 1 sierpnia 2020 r. Wszystkie obsługiwane Azure File Sync agentów domyślnie używają już standardu TLS1.2. Użycie starszej wersji usługi TLS może wystąpić, jeśli na serwerze wyłączono na serwerze TLS 1.2 lub używany jest serwer proxy. Jeśli używasz serwera proxy, zalecamy sprawdzenie konfiguracji serwera proxy. Azure File Sync usługi dodane po 1.05.2020 r. będą obsługiwać tylko TLS1.2, a obsługa TLS1.0 i 1.1 zostanie usunięta z istniejących regionów 1 sierpnia 2020 r.  Aby uzyskać więcej informacji, zobacz [przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Azure File Sync komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST usługi Azure File Sync i protokołu FileREST, które zawsze używają protokołu HTTPS przez port 443. Azure File Sync nie wysyła niezaszyfrowanych żądań za pośrednictwem protokołu HTTP. 

Konta usługi Azure Storage zawierają przełącznik wymagający szyfrowania podczas przesyłania, które jest domyślnie włączone. Nawet jeśli przełącznik na poziomie konta magazynu jest wyłączony, co oznacza, że nieszyfrowane połączenia z udziałami plików platformy Azure są możliwe, usługa Azure File Sync nadal będzie używać tylko zaszyfrowanych kanałów w celu uzyskania dostępu do udziału plików.

Głównym powodem wyłączenia szyfrowania podczas przesyłania dla konta magazynu jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak windows Server 2008 R2 lub starsza dystrybucja systemu Linux, bezpośrednio w rozmowie z udziałami plików platformy Azure. Jeśli starsza aplikacja będzie rozmawiała z pamięcią podręczną systemu Windows Server udziału plików, przełączanie tego ustawienia nie będzie mieć żadnego efektu. 

Zdecydowanie zalecamy zapewnienie włączonego szyfrowania danych podczas przesyłania.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-file-share-encryption-at-rest"></a>Szyfrowanie udziałów plików platformy Azure w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włączanie standardowych udziałów plików o rozmiarze do 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Dostępność w regionach
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Dostępność regionu usługi Azure File Sync

Aby uzyskać informacje o dostępności regionalnej, [zobacz Dostępność produktów według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

Następujące regiony wymagają zażądania dostępu do usługi Azure Storage przed użyciem Azure File Sync z nimi:

- Francja Południowa
- Zachodnia Republika Południowej Afryki
- Środkowe Zjednoczone Emiraty Zjednoczone

Aby zażądać dostępu do tych regionów, postępuj zgodnie z procesem w [tym dokumencie.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Magazyn geograficznie nadmiarowy i strefowo nadmiarowy mają możliwość ręcznego przechowywania w trybu failover w regionie pomocniczym. Zalecamy, aby nie robić tego poza awarią, gdy używasz usługi Azure File Sync ze względu na zwiększone prawdopodobieństwo utraty danych. W przypadku awarii, w której chcesz zainicjować ręczne tryb failover magazynu, musisz otworzyć przypadek pomocy technicznej dla firmy Microsoft, aby uzyskać Azure File Sync wznowienia synchronizacji z pomocniczym punktem końcowym.

## <a name="migration"></a>Migracja
Jeśli masz istniejący serwer plików systemu Windows 2012R2 lub nowszego, Azure File Sync można zainstalować bezpośrednio bez konieczności przenoszenia danych na nowy serwer. Jeśli planujesz migrację na nowy serwer plików systemu Windows w ramach przyjęcia usługi Azure File Sync lub jeśli dane znajdują się obecnie w magazynie dołączonym do sieci (NAS), istnieje kilka możliwych metod migracji, które mogą być Azure File Sync z danymi. To, które podejście migracji należy wybrać, zależy od tego, gdzie obecnie znajdują się dane. 

Zapoznaj się z [artykułem Azure File Sync i omówieniem](storage-files-migration-overview.md) migracji udziału plików platformy Azure, w którym można znaleźć szczegółowe wskazówki dotyczące scenariusza.

## <a name="antivirus"></a>Oprogramowanie antywirusowe
Ponieważ oprogramowanie antywirusowe działa przez skanowanie plików w celu wyszukiwania znanego złośliwego kodu, produkt antywirusowy może powodować odwoływanie plików warstwowych, co powoduje naliczanie wysokich opłat za ruch wychodzący. W wersjach 4.0 i starszych agenta Azure File Sync pliki warstwowe mają ustawiony bezpieczny atrybut systemu Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS bezpiecznego. Zalecamy skonskonsultować się z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować rozwiązanie, aby pominąć odczytywanie plików z tym zestawem atrybutów (wiele z nich robi to automatycznie). 

Rozwiązania antywirusowe firmy Microsoft, takie jak Windows Defender i System Center Endpoint Protection (SCEP), automatycznie pomijają odczytywanie plików, które mają ustawiony ten atrybut. Przetestowaliśmy je i zidentyfikowaliśmy jeden drobny problem: po dodaniu serwera do istniejącej grupy synchronizacji pliki mniejsze niż 800 bajtów są przywołyne (pobierane) na nowym serwerze. Te pliki pozostaną na nowym serwerze i nie będą warstwowe, ponieważ nie spełniają wymagań rozmiaru warstw (>64 kb).

> [!Note]  
> Dostawcy oprogramowania antywirusowego mogą sprawdzić zgodność między swoimi produktami i Azure File Sync przy użyciu zestawu testów zgodności oprogramowania antywirusowego programu [Azure File Sync,](https://www.microsoft.com/download/details.aspx?id=58322)który jest dostępny do pobrania w Centrum pobierania Microsoft.

## <a name="backup"></a>Backup 
Jeśli włączono obsługę warstw w chmurze, nie należy używać rozwiązań, które bezpośrednio kopię zapasową punktu końcowego serwera lub maszyny wirtualnej, na której znajduje się punkt końcowy serwera. Przetwarzanie warstw w chmurze powoduje, że tylko podzbiór danych ma być przechowywany w punkcie końcowym serwera, a pełny zestaw danych jest przechowywany w twoim udziałach plików platformy Azure. W zależności od używanego rozwiązania do tworzenia kopii zapasowych pliki warstwowe będą pomijane i nie będą tworzyć kopii zapasowej (ponieważ mają ustawiony atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS) lub zostaną przywołane na dysku, co spowoduje naliszczenie wysokich opłat za ruch wychodzący. Zalecamy używanie rozwiązania do tworzenia kopii zapasowych w chmurze w celu bezpośredniego tworzenia kopii zapasowej udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz Informacje o kopii [zapasowej](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) udziału plików platformy Azure lub skontaktuj się z dostawcą kopii zapasowych, aby sprawdzić, czy obsługuje tworzenie kopii zapasowych udziałów plików platformy Azure.

Jeśli wolisz korzystać z lokalnego rozwiązania do tworzenia kopii zapasowych, kopie zapasowe powinny być wykonywane na serwerze w grupie synchronizacji, na którym wyłączono warstwy w chmurze. Podczas przywracania użyj opcji przywracania na poziomie woluminu lub pliku. Pliki przywrócone przy użyciu opcji przywracania na poziomie pliku zostaną zsynchronizowane ze wszystkimi punktami końcowymi w grupie synchronizacji, a istniejące pliki zostaną zastąpione wersją przywróconą z kopii zapasowej.  Przywracanie na poziomie woluminu nie zastąpi nowszej wersji plików w udziałach plików platformy Azure ani innych punktach końcowych serwera.

> [!WARNING]
> Jeśli konieczne jest użycie robocopy /B z agentem programu Azure File Sync uruchomionym na serwerze źródłowym lub docelowym, przeszlij do wersji 12.0 agenta programu Azure File Sync lub nowszą. Użycie narzędzia Robocopy /B z wersjami agenta mniejszymi niż 12.0 spowoduje uszkodzenie plików warstwowych podczas kopiowania.

> [!Note]  
> Przywracanie systemu od zerowego (BMR) może spowodować nieoczekiwane wyniki i nie jest obecnie obsługiwane.

> [!Note]  
> W wersji 9 agenta usługi Azure File Sync migawki usługi VSS (w tym karta Poprzednie wersje) są teraz obsługiwane na woluminach z włączoną obsługą warstw w chmurze. Należy jednak włączyć zgodność poprzedniej wersji za pomocą programu PowerShell. [Dowiedz się, jak](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)to zrobić.

## <a name="data-classification"></a>Data Classification
Jeśli masz zainstalowane oprogramowanie do klasyfikacji danych, włączenie warstw w chmurze może spowodować zwiększenie kosztów z dwóch powodów:

1. Po włączeniu obsługi warstw w chmurze najbardziej aktywne pliki są buforowane lokalnie, a najfajniejsze pliki są warstwowe w udziałach plików platformy Azure w chmurze. Jeśli klasyfikacja danych regularnie skanuje wszystkie pliki w udziałach plików, pliki warstwowe w chmurze muszą być przysyłane przy każdym skanowaniu. 

2. Jeśli oprogramowanie do klasyfikacji danych używa metadanych w strumieniu danych pliku, plik musi zostać w pełni odwołany, aby oprogramowanie widziało klasyfikację. 

Zwiększa to zarówno liczbę odwoływek, jak i ilość przywołyowanych danych, może zwiększyć koszty.

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Następne kroki
* [Rozważ ustawienia zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Wdrażanie usługi Pliki Azure](./storage-how-to-create-file-share.md)
* [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)

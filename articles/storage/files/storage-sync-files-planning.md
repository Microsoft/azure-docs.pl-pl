---
title: Planowanie wdrożenia Azure File Sync | Microsoft Docs
description: Zaplanuj wdrożenie z Azure File Sync, usługi, która pozwala na buforowanie wielu udziałów plików platformy Azure na lokalnym serwerze z systemem Windows lub na maszynie wirtualnej w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 28b7419dfc889d1149765b884f1a5990a1e28f03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898108"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planowanie wdrażania usługi Azure File Sync

:::row:::
    :::column:::
        [![Wywiad i Demonstracja przedstawiające Azure File Sync — kliknij, aby odtworzyć!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync to usługa, która umożliwia buforowanie wielu udziałów plików platformy Azure na lokalnym serwerze Windows lub na maszynie wirtualnej w chmurze. 
        
        W tym artykule przedstawiono Azure File Sync pojęć i funkcji. Po zapoznaniu się z Azure File Sync należy wziąć pod uwagę Poniższy [Przewodnik wdrażania Azure File Sync](storage-sync-files-deployment-guide.md) , aby wypróbować tę usługę.        
    :::column-end:::
:::row-end:::

Pliki będą przechowywane w chmurze w [udziałach plików platformy Azure](storage-files-introduction.md). Udziały plików platformy Azure mogą być używane na dwa sposoby: przez bezpośrednie zainstalowanie udziałów plików platformy Azure bezserwerowych (SMB) lub buforowanie udziałów plików platformy Azure lokalnie przy użyciu Azure File Sync. Wybór opcji wdrożenia powoduje zmianę aspektów, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- **Bezpośrednia instalacja udziału plików platformy Azure**: ponieważ Azure Files zapewnia dostęp do protokołu SMB, można instalować udziały plików platformy Azure lokalnie lub w chmurze przy użyciu standardowego klienta protokołu SMB dostępnego w systemach Windows, MacOS i Linux. Ponieważ udziały plików platformy Azure są bezserwerowe, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- **Buforowanie udziałów plików platformy Azure w środowisku lokalnym za pomocą Azure File Sync**: Azure File Sync pozwala na scentralizowanie udziały plików w organizacji w Azure Files, przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Azure File Sync przekształca lokalnego (lub w chmurze) systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. 

## <a name="management-concepts"></a>Pojęcia związane z zarządzaniem
Wdrożenie Azure File Sync ma trzy podstawowe obiekty zarządzania:

- **Udział plików platformy Azure**: udział plików platformy Azure to bezserwerowy udział plików w chmurze, który udostępnia *punkt końcowy w chmurze* relacji synchronizacji Azure File Sync. Dostęp do plików w udziale plików platformy Azure można uzyskać bezpośrednio przy użyciu protokołu SMB lub FileREST, chociaż zachęcamy do uzyskiwania dostępu głównie do plików za pomocą pamięci podręcznej systemu Windows Server, gdy udział plików platformy Azure jest używany z Azure File Sync. Wynika to z faktu, że Azure Files dzisiaj nie ma skutecznego mechanizmu wykrywania zmian, takiego jak system Windows Server, więc bezpośrednie zmiany udziału plików platformy Azure będą przenoszone do punktów końcowych serwera.
- **Punkt końcowy serwera**: ścieżka w systemie Windows Server, która jest synchronizowana z udziałem plików platformy Azure. Może to być określony folder na woluminie lub w katalogu głównym woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli ich przestrzenie nazw nie nakładają się na siebie.
- **Grupa synchronizacji**: obiekt, który definiuje relację synchronizacji między **punktem końcowym w chmurze** lub udziałem plików platformy Azure, a punktem końcowym serwera. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Jeśli na przykład istnieją dwa odrębne zestawy plików, którymi chcesz zarządzać za pomocą Azure File Sync, utworzysz dwie grupy synchronizacji i dodasz różne punkty końcowe do każdej grupy synchronizacji.

### <a name="azure-file-share-management-concepts"></a>Pojęcia związane z zarządzaniem udziałem plików platformy Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Pojęcia związane z zarządzaniem Azure File Sync
Grupy synchronizacji są wdrażane w **usługach synchronizacji magazynu**, które są obiektami najwyższego poziomu, które rejestrują serwery do użytku z Azure File Sync i zawierają relacje grupy synchronizacji. Zasób usługi synchronizacji magazynu jest elementem równorzędnym zasobu konta magazynu i można go również wdrożyć w grupach zasobów platformy Azure. Usługa synchronizacji magazynu może tworzyć grupy synchronizacji zawierające udziały plików platformy Azure między wieloma kontami magazynu i wieloma zarejestrowanymi serwerami z systemem Windows.

Aby można było utworzyć grupę synchronizacji w usłudze synchronizacji magazynu, należy najpierw zarejestrować serwer z systemem Windows za pomocą usługi synchronizacji magazynu. Spowoduje to utworzenie **zarejestrowanego obiektu serwera** , który reprezentuje relację zaufania między serwerem lub klastrem a usługą synchronizacji magazynu. Aby zarejestrować usługę synchronizacji magazynu, należy najpierw zainstalować agenta Azure File Sync na serwerze programu. Pojedynczy serwer lub klaster można zarejestrować tylko w jednej usłudze synchronizacji magazynu jednocześnie.

Grupa synchronizacji zawiera jeden punkt końcowy w chmurze lub udział plików platformy Azure oraz co najmniej jeden punkt końcowy serwera. Obiekt punktu końcowego serwera zawiera ustawienia służące do konfigurowania możliwości obsługi **warstw w chmurze** , która zapewnia możliwość buforowania Azure File Sync. Aby można było synchronizować się z udziałem plików platformy Azure, konto magazynu zawierające udział plików platformy Azure musi znajdować się w tym samym regionie platformy Azure co usługa synchronizacji magazynu.

> [!Important]  
> Można wprowadzać zmiany w dowolnym punkcie końcowym chmury lub w punkcie końcowym serwera w grupie synchronizacji i zsynchronizować pliki z innymi punktami końcowymi w grupie synchronizacji. Jeśli zmienisz bezpośrednio punkt końcowy w chmurze (udział plików platformy Azure), najpierw muszą zostać odnalezione zmiany za pomocą zadania wykrywania zmian Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, zobacz [Azure Files często zadawanych pytań](storage-files-faq.md#afs-change-detection).

### <a name="management-guidance"></a>Wskazówki dotyczące zarządzania
W przypadku wdrażania Azure File Sync zalecamy:

- Wdrażanie udziałów plików platformy Azure 1:1 z udziałami plików systemu Windows. Obiekt punktu końcowego serwera zapewnia doskonały stopień elastyczności konfigurowania topologii synchronizacji po stronie serwera relacji synchronizacji. Aby uprościć zarządzanie, ścieżka punktu końcowego serwera jest zgodna ze ścieżką udziału plików systemu Windows. 

- Użyj jako kilku usług synchronizacji magazynu, jak to możliwe. Upraszcza to zarządzanie, gdy istnieją grupy synchronizacji zawierające wiele punktów końcowych serwera, ponieważ system Windows Server może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu jednocześnie. 

- Podczas wdrażania udziałów plików platformy Azure należy zwrócić uwagę na ograniczenia IOPS konta magazynu. W idealnym przypadku należy zamapować udziały plików 1:1 z kontami magazynu, ale może to nie zawsze być możliwe z powodu różnych limitów i ograniczeń zarówno z organizacji, jak i z platformy Azure. Gdy nie można mieć tylko jednego udziału plików wdrożonego na jednym koncie magazynu, należy wziąć pod uwagę, które udziały będą wysoce aktywne i które udziały będą mniej aktywne, aby upewnić się, że udziały plików okienko nie zostaną umieszczone w tym samym koncie magazynu jednocześnie.

## <a name="windows-file-server-considerations"></a>Zagadnienia dotyczące serwera plików systemu Windows
Aby włączyć funkcję synchronizacji w systemie Windows Server, należy zainstalować Azure File Sync agenta do pobrania. Agent Azure File Sync udostępnia dwa główne składniki: `FileSyncSvc.exe` , usługę w tle systemu Windows, która jest odpowiedzialna za monitorowanie zmian w punktach końcowych serwera i Inicjowanie sesji synchronizacji, a także `StorageSync.sys` filtr systemu plików, który umożliwia obsługę warstw w chmurze i szybkie odzyskiwanie po awarii.  

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego
Azure File Sync jest obsługiwana w następujących wersjach systemu Windows Server:

| Wersja | Obsługiwane jednostki SKU | Obsługiwane opcje wdrażania |
|---------|----------------|------------------------------|
| Windows Server 2019 | Centrum danych, standard i IoT | Pełne i podstawowe |
| Windows Server 2016 | Serwer Datacenter, standard i Storage | Pełne i podstawowe |
| Windows Server 2012 z dodatkiem R2 | Serwer Datacenter, standard i Storage | Pełne i podstawowe |

Przyszłe wersje systemu Windows Server zostaną dodane po ich wydaniu.

> [!Important]  
> Zalecamy przechowywanie wszystkich serwerów, które są używane z programem Azure File Sync z najnowszymi aktualizacjami z Windows Update. 

### <a name="minimum-system-resources"></a>Minimalna ilość zasobów systemowych
Azure File Sync wymaga serwera fizycznego lub wirtualnego z co najmniej jednym procesorem CPU i minimalnie 2 GiB pamięci.

> [!Important]  
> Jeśli serwer działa na maszynie wirtualnej z włączoną pamięcią dynamiczną, należy skonfigurować MASZYNę wirtualną z co najmniej 2048 MiB pamięci.

W przypadku większości obciążeń produkcyjnych nie zaleca się konfigurowania serwera synchronizacji Azure File Sync tylko z minimalnymi wymaganiami. Aby uzyskać więcej informacji, zobacz [zalecane zasoby systemowe](#recommended-system-resources) .

### <a name="recommended-system-resources"></a>Zalecane zasoby systemowe
Podobnie jak w przypadku każdej funkcji serwera lub aplikacji, wymagania dotyczące zasobów systemowych dla Azure File Sync są określane przez skalę wdrożenia. większe wdrożenia na serwerze wymagają większych zasobów systemowych. W przypadku Azure File Sync skalowanie zależy od liczby obiektów w punktach końcowych serwera i zmian w zestawie danych. Pojedynczy serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji oraz liczbę obiektów wymienionych w poniższej tabeli kont dla pełnej przestrzeni nazw, do której jest dołączony serwer. 

Na przykład punkt końcowy serwera A z 10 000 000 obiektów + serwer punktu końcowego B z 10 000 000 obiektów = 20 000 000 obiektów. W tym przykładowym wdrożeniu zalecamy 8 procesorów CPU, 16 GiB pamięci dla stanu stałego oraz (jeśli to możliwe) 48 GiB pamięci dla początkowej migracji.
 
Dane przestrzeni nazw są przechowywane w pamięci ze względu na wydajność. Ze względu na to, że większe przestrzenie nazw wymagają większej ilości pamięci, aby zapewnić dobrą wydajność, a większa liczba operacji wymaga więcej czasu procesora. 
 
W poniższej tabeli podano zarówno rozmiar przestrzeni nazw, jak i konwersję do pojemności typowych udziałów plików ogólnego przeznaczenia, gdzie średni rozmiar pliku to 512 KiB. Jeśli rozmiary plików są mniejsze, należy rozważyć dodanie dodatkowej pamięci dla tej samej pojemności. Zmień konfigurację pamięci na rozmiar przestrzeni nazw.

| Przestrzeń nazw — pliki & katalogów (miliony)  | Typowa pojemność (TiB)  | Rdzenie procesora CPU  | Zalecana pamięć (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (synchronizacja początkowa)/2 (typowe zmiany)      |
| 5        | 2.3     | 2        | 16 (synchronizacja początkowa)/4 (typowe zmiany)    |
| 10       | 4.7     | 4        | 32 (synchronizacja początkowa)/8 (typowe zmiany)   |
| 30       | 14,0    | 8        | 48 (synchronizacja początkowa)/16 (typowe zmiany)   |
| 50       | 23,3    | 16       | 64 (synchronizacja początkowa)/32 (typowe zmiany)  |
| 100 *     | 46,6    | 32       | 128 (synchronizacja początkowa)/32 (typowe zmiany)  |

\*W tej chwili nie zaleca się synchronizowania więcej niż 100 000 000 plików & katalogów. Jest to ograniczenie elastyczne na podstawie naszych przetestowanych progów. Aby uzyskać więcej informacji, zobacz [Azure Files celów skalowalności i wydajności](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Początkowa synchronizacja przestrzeni nazw jest operacją intensywną, a zalecamy przydzielenie większej ilości pamięci do czasu zakończenia synchronizacji początkowej. Nie jest to wymagane, ale może przyspieszyć synchronizację początkową. 
> 
> Typowa zmiana to 0,5% przestrzeni nazw zmienianej dziennie. W przypadku wyższych poziomów zmian należy rozważyć dodanie większej liczby procesorów. 

- Lokalnie dołączony wolumin sformatowany przy użyciu systemu plików NTFS.

### <a name="evaluation-cmdlet"></a>Polecenie cmdlet do oceny
Przed wdrożeniem Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu polecenia cmdlet do oceny Azure File Sync. To polecenie cmdlet sprawdza potencjalne problemy z systemem plików i zestawem danych, na przykład nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. Kontrole obejmują większość funkcji wymienionych poniżej: Zalecamy zapoznanie się z pozostałą częścią tej sekcji, aby upewnić się, że wdrożenie przebiega bezproblemowo. 

Polecenie cmdlet do oceny można zainstalować, instalując moduł AZ PowerShell module, który można zainstalować, postępując zgodnie z instrukcjami tutaj: [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Użycie  
Narzędzie do oceny można wywołać na kilka różnych sposobów: można wykonać testy systemowe, zestawy danych lub oba te elementy. Aby przeprowadzić testy systemu i zestawu danych: 

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
 
Aby wyświetlić wyniki w formacie CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Zgodność systemu plików
Azure File Sync jest obsługiwane tylko na woluminach z systemem plików NTFS bezpośrednio dołączone. Bezpośrednio dołączony magazyn lub DAS w systemie Windows Server oznacza, że system operacyjny Windows Server jest właścicielem systemu plików. DAS można dostarczyć za pomocą fizycznego dołączania dysków do serwera plików, dołączając dyski wirtualne do maszyny wirtualnej serwera plików (na przykład maszyny wirtualnej hostowanej przez funkcję Hyper-V) lub nawet za pomocą interfejsu ISCSI.

Obsługiwane są tylko woluminy NTFS; Systemy plików ReFS, FAT, FAT32 i inne nie są obsługiwane.

W poniższej tabeli przedstawiono stan międzyoperacyjności funkcji systemu plików NTFS: 

| Cechy | Stan obsługi | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Poufne listy kontroli dostępu w stylu systemu Windows są zachowywane przez Azure File Sync i są wymuszane przez system Windows Server w punktach końcowych serwera. Listy ACL można również wymuszać podczas bezpośredniego instalowania udziału plików platformy Azure, jednak wymaga to dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [tożsamości](#identity) . |
| Twarde linki | Pominięto | |
| Linki symboliczne | Pominięto | |
| Punkty instalacji | Częściowo obsługiwane | Punkty instalacji mogą być głównym punktem końcowym serwera, ale są pomijane, jeśli są zawarte w przestrzeni nazw punktu końcowego serwera. |
| Połączenia | Pominięto | Na przykład rozproszony system plików folderów DfrsrPrivate i DFSRoots. |
| Punkty ponownej analizy | Pominięto | |
| Kompresja NTFS | W pełni obsługiwane | |
| Pliki rozrzedzone | W pełni obsługiwane | Pliki rozrzedzone — synchronizacja (nie są blokowane), ale są synchronizowane z chmurą jako pełny plik. Jeśli zawartość pliku zostanie zmieniona w chmurze (lub na innym serwerze), plik nie jest już rozrzedzony, gdy zmiana zostanie pobrana. |
| Alternatywne strumienie danych (ADS) | Zachowane, ale nie zsynchronizowane | Na przykład Tagi klasyfikacji utworzone przez infrastrukturę klasyfikacji plików nie są synchronizowane. Istniejące znaczniki klasyfikacji dla plików na każdym z punktów końcowych serwera pozostaną niezmienione. |

<a id="files-skipped"></a>Azure File Sync spowoduje również pominięcie niektórych plików tymczasowych i folderów systemowych:

| Plik/folder | Uwaga |
|-|-|
| pagefile.sys | Plik specyficzny dla systemu |
| Desktop.ini | Plik specyficzny dla systemu |
| kciuki. DB | Plik tymczasowy dla miniatur |
| ehThumbs. DB | Plik tymczasowy dla miniatur multimediów |
| ~$\*.\* | Plik tymczasowy pakietu Office |
| \*. tmp | Plik tymczasowy |
| \*.laccdb | Plik blokowania dostępu do bazy danych|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Plik synchronizacji wewnętrznej|
| \\Informacje o woluminie systemowym | Folder określony dla woluminu |
| $RECYCLE. OKREŚLONEJ| Folder |
| \\SyncShareState | Folder do synchronizacji |

### <a name="failover-clustering"></a>Klaster trybu failover
Usługa Windows Server Failover Clustering jest obsługiwana przez Azure File Sync dla opcji wdrażania "serwer plików do użytku ogólnego". Klaster trybu failover nie jest obsługiwany na "Serwer plików skalowalny w poziomie dla danych aplikacji" (SOFS) ani na udostępnionych woluminach klastra (CSV).

> [!Note]  
> Aby synchronizacja działała poprawnie, Agent Azure File Sync musi być zainstalowany na każdym węźle w klastrze trybu failover.

### <a name="data-deduplication"></a>Deduplikacja danych
**Windows Server 2016 i Windows Server 2019**   
Funkcja deduplikacji danych jest obsługiwana niezależnie od tego, czy obsługa warstw w chmurze jest włączona, czy wyłączona w co najmniej jednym punkcie końcowym serwera na woluminie dla systemu Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji danych na woluminie z włączonym obsługą usług Cloud Storage umożliwia przechowywanie w pamięci podręcznej większej liczby plików bez udostępniania większej ilości miejsca w magazynie. 

Po włączeniu deduplikacji danych na woluminie z włączonym obsługą obsługi warstw w chmurze pliki zoptymalizowane pod kątem deduplikacji w lokalizacji punktu końcowego serwera będą warstwowo podobne do normalnego pliku na podstawie ustawień zasad dotyczących warstw chmurowych. Po przeprowadzeniu warstwowych plików zoptymalizowanych pod kątem deduplikacji zadanie odzyskiwania pamięci deduplikacji danych zostanie automatycznie uruchomione w celu odtworzenia miejsca na dysku przez usunięcie niepotrzebnych fragmentów, do których nie odwołują się już inne pliki w woluminie.

Zwróć uwagę, że oszczędności woluminu dotyczą tylko serwera programu; Twoje dane w udziale plików platformy Azure nie zostaną deduplikowane.

> [!Note]  
> Aby zapewnić obsługę deduplikacji danych na woluminach z włączoną obsługą warstw w chmurze w systemie Windows Server 2019, należy zainstalować usługę Windows Update [KB4520062](https://support.microsoft.com/help/4520062) , a wymagany jest Agent Azure File Sync w wersji 9.0.0.0 lub nowszej.

**Windows Server 2012 z dodatkiem R2**  
Azure File Sync nie obsługuje deduplikacji danych i warstw w chmurze na tym samym woluminie w systemie Windows Server 2012 R2. Jeśli Deduplikacja danych jest włączona w woluminie, Obsługa warstw w chmurze musi być wyłączona. 

**Uwagi**
- Jeśli Deduplikacja danych jest zainstalowana przed zainstalowaniem agenta Azure File Sync, ponowne uruchomienie jest wymagane do obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie.
- Jeśli Deduplikacja danych jest włączona w woluminie po włączeniu obsługi warstw w chmurze, początkowe zadanie optymalizacji deduplikacji zoptymalizuje pliki na woluminie, które nie zostały jeszcze warstwowe i będzie miało następujący wpływ na obsługę warstw w chmurze:
    - Zasady wolnego miejsca będą nadal korzystać z warstw plików zgodnie z ilością wolnego miejsca na woluminie przy użyciu mapę cieplną.
    - Zasady dotyczące dat spowodują pominięcie warstw plików, które mogą być w inny sposób kwalifikujące się do obsługi warstw z powodu zadania optymalizacji deduplikacji uzyskującego dostęp do plików.
- W przypadku trwających zadań optymalizacji deduplikacji Obsługa warstw w chmurze z zasadami daty zostanie opóźniona przez ustawienie [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume?view=win10-ps) deduplikacji danych, jeśli plik nie jest już warstwowy. 
    - Przykład: Jeśli ustawienie MinimumFileAgeDays to siedem dni, a zasady dotyczące warstw w chmurze to 30 dni, zasady dat będą mieć pliki warstwy po 37 dniach.
    - Uwaga: gdy plik jest warstwą Azure File Sync, zadanie optymalizacji deduplikacji pominie ten plik.
- Jeśli serwer z systemem Windows Server 2012 R2 z zainstalowanym agentem Azure File Sync został uaktualniony do systemu Windows Server 2016 lub Windows Server 2019, należy wykonać następujące czynności w celu zapewnienia obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie:  
    - Odinstaluj agenta Azure File Sync dla systemu Windows Server 2012 R2 i ponownie uruchom serwer.
    - Pobierz agenta Azure File Sync dla nowej wersji systemu operacyjnego serwera (Windows Server 2016 lub Windows Server 2019).
    - Zainstaluj agenta Azure File Sync i ponownie uruchom serwer.  
    
    Uwaga: ustawienia konfiguracji Azure File Sync na serwerze są zachowywane, gdy agent zostanie odinstalowany i ponownie zainstalowany.

### <a name="distributed-file-system-dfs"></a>Rozproszony system plików (system plików DFS)
Azure File Sync obsługuje międzyoperacyjność z przestrzeniami nazw systemu plików DFS (DFS-N) i Replikacja systemu plików DFS (DFS-R).

**Przestrzenie nazw systemu plików DFS (DFS-n)**: Azure File Sync jest w pełni obsługiwany na serwerach systemu plików DFS-n. Aby synchronizować dane między punktami końcowymi serwera i punktem końcowym w chmurze, można zainstalować agenta Azure File Sync na co najmniej jednym członku systemu plików DFS-N. Aby uzyskać więcej informacji, zobacz [Omówienie przestrzeni nazw systemu plików DFS](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikacja systemu plików DFS (DFS-r)**: ponieważ system plików DFS-R i Azure File Sync są rozwiązaniami replikacji, zalecamy zastępowanie systemu plików DFS-r i Azure File Sync. Istnieje jednak kilka scenariuszy, w których warto użyć systemu plików DFS-R i Azure File Sync ze sobą:

- Przeprowadzasz migrację z wdrożenia DFS-R do wdrożenia Azure File Sync. Aby uzyskać więcej informacji, zobacz [Migrowanie wdrożenia replikacja systemu plików DFS (DFS-R) do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy serwer lokalny, który wymaga kopii danych pliku, może być połączony bezpośrednio z Internetem.
- Serwery gałęzi konsolidują dane na jeden serwer centralny, dla którego chcesz używać Azure File Sync.

Aby Azure File Sync i DFS-R działały obok siebie:

1. Azure File Sync Obsługa warstw w chmurze musi być wyłączona na woluminach z replikowanymi folderami systemu plików DFS-R.
2. Punktów końcowych serwera nie należy konfigurować w folderach replikacji tylko do odczytu systemu plików DFS.

Aby uzyskać więcej informacji, zobacz [Replikacja systemu plików DFS przegląd](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
Korzystanie z programu Sysprep na serwerze z zainstalowanym agentem Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Po wdrożeniu obrazu serwera i zakończeniu miniinstalacji programu Sysprep należy przeprowadzić instalację agenta i rejestrację serwera.

### <a name="windows-search"></a>Usługa Windows Search
Jeśli Obsługa warstw w chmurze jest włączona w punkcie końcowym serwera, pliki, które są warstwami, są pomijane i nie są indeksowane przez funkcję wyszukiwania systemu Windows. Pliki niewarstwowe są indeksowane prawidłowo.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Inne rozwiązania do zarządzania magazynem hierarchicznym (HSM)
W przypadku Azure File Sync nie należy używać innych rozwiązań modułu HSM.

## <a name="identity"></a>Tożsamość
Azure File Sync współpracuje ze standardową tożsamością opartą na usłudze AD bez żadnej specjalnej instalacji poza konfiguracją synchronizacji. W przypadku korzystania z Azure File Sync, ogólna oczekuje, że większość dostępu przechodzi przez serwery buforowania Azure File Sync, a nie za pośrednictwem udziału plików platformy Azure. Ponieważ punkty końcowe serwera znajdują się w systemie Windows Server, a system Windows Server obsługuje listy ACL dla usług AD i Windows przez dłuższy czas, nic nie jest jednak dłużej, ponieważ serwery plików systemu Windows zarejestrowane w usłudze synchronizacji magazynu są przyłączone do domeny. W Azure File Sync będą przechowywane listy ACL plików w udziale plików platformy Azure i zostaną one zreplikowane do wszystkich punktów końcowych serwera.

Mimo że zmiany wprowadzone bezpośrednio do udziału plików platformy Azure przestaną być synchronizowane z punktami końcowymi serwera w grupie synchronizacji, można również upewnić się, że można wymusić uprawnienia usługi AD do udziału plików bezpośrednio w chmurze. W tym celu należy dołączyć domenę do konta magazynu do lokalnej usługi AD, tak jak w przypadku przyłączenia serwerów plików systemu Windows do domeny. Aby dowiedzieć się więcej na temat dołączania do konta magazynu do Active Directory należącego do klienta, zobacz [Azure Files Active Directory przegląd](storage-files-active-directory-overview.md).

> [!Important]  
> Do pomyślnego wdrożenia Azure File Sync domeny dołączenia do konta magazynu Active Directory nie jest wymagane. Jest to ściśle opcjonalny krok umożliwiający udział plików platformy Azure wymuszanie lokalnych list ACL, gdy użytkownicy instalują udział plików platformy Azure bezpośrednio.

## <a name="networking"></a>Sieć
Agent Azure File Sync komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST Azure File Sync i protokołu FileREST, z których korzystają zawsze protokół HTTPS przez port 443. Protokół SMB nie jest nigdy używany do przekazywania ani pobierania danych między serwerem Windows i udziałem plików platformy Azure. Ponieważ większość organizacji zezwala na ruch HTTPS na porcie 443, ponieważ wymaga to odwiedzania większości witryn sieci Web, specjalna konfiguracja sieci zwykle nie jest wymagana do wdrażania Azure File Sync.

Zgodnie z zasadami organizacji lub unikatowymi wymaganiami prawnymi może być wymagana bardziej restrykcyjna komunikacja z platformą Azure, dlatego Azure File Sync udostępnia kilka mechanizmów konfigurowania sieci. Na podstawie Twoich wymagań można:

- Synchronizacja tunelu i przekazywanie plików/pobieranie ruchu przez ExpressRoute lub sieć VPN platformy Azure. 
- Korzystaj z Azure Files i funkcji sieciowych platformy Azure, takich jak punkty końcowe usługi i prywatne punkty końcowe.
- Skonfiguruj Azure File Sync do obsługi serwera proxy w Twoim środowisku.
- Ogranicz aktywność sieci z Azure File Sync.

Aby dowiedzieć się więcej na temat Azure File Sync i sieci, zobacz [zagadnienia dotyczące sieci Azure File Sync](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Szyfrowanie
Korzystając z Azure File Sync, istnieją trzy różne warstwy szyfrowania, które należy wziąć pod uwagę: szyfrowanie magazynu systemu Windows Server, szyfrowanie podczas przesyłania między agentem Azure File Sync i platformą Azure oraz szyfrowanie danych w udziale plików platformy Azure. 

### <a name="windows-server-encryption-at-rest"></a>Szyfrowanie systemu Windows Server w czasie spoczynku 
Istnieją dwie strategie szyfrowania danych w systemie Windows Server, które zwykle działają w Azure File Sync: szyfrowanie poniżej systemu plików, tak że system plików i wszystkie zapisywane w nim dane są szyfrowane, a szyfrowanie w samym formacie pliku. Te metody nie wykluczają się wzajemnie. mogą być używane razem, jeśli jest to wymagane, ponieważ nie ma potrzeby szyfrowania.

Aby zapewnić szyfrowanie poniżej systemu plików, system Windows Server udostępnia skrzynkę odbiorczą funkcji BitLocker. Funkcja BitLocker jest w pełni niewidoczna dla Azure File Sync. Podstawowym powodem korzystania z mechanizmu szyfrowania, takiego jak funkcja BitLocker, jest zapobieganie fizycznym eksfiltracji danych z lokalnego centrum dane przez kogoś kradzieży dysków i uniemożliwienie ładowania bezpośredniego do nieautoryzowanego systemu operacyjnego w celu wykonywania operacji odczytu/zapisu danych. Aby dowiedzieć się więcej na temat funkcji BitLocker, zobacz [Omówienie funkcji BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkty innych firm, które działają podobnie do funkcji BitLocker, w tym, że znajdują się poniżej woluminu NTFS, powinny podobnie działać w pełni w sposób przezroczysty przy użyciu Azure File Sync. 

Druga podstawowa metoda szyfrowania danych polega na zaszyfrowaniu strumienia danych pliku, gdy aplikacja zapisze plik. Niektóre aplikacje mogą to zrobić natywnie, jednak zazwyczaj nie jest to przypadek. Przykładem metody szyfrowania strumienia danych pliku jest Azure Information Protection (/Active)/Azure Rights Management Services (Azure RMS) Directory RMS. Podstawowym powodem korzystania z mechanizmu szyfrowania, takiego jak podstawą/RMS, jest zapobieganie eksfiltracji danych z udziału plików przez osoby kopiujące je do alternatywnych lokalizacji, takich jak dysk flash, lub wysyłanie ich pocztą e-mail do nieautoryzowanej osoby. Gdy strumień danych pliku jest szyfrowany w ramach formatu pliku, ten plik będzie nadal szyfrowany w udziale plików platformy Azure. 

Azure File Sync nie współdziała z szyfrowanym systemem plików NTFS (NTFS EFS) ani rozwiązaniami do szyfrowania innych firm, które znajdują się powyżej systemu plików, ale poniżej strumienia danych pliku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

> [!NOTE]
> Usługa Azure File Sync spowoduje usunięcie obsługi protokołów TLS 1.0 i 1,1 1 sierpnia 2020. Wszystkie obsługiwane wersje agentów Azure File Sync używają już domyślnie protokołu TLS 1.2. Użycie starszej wersji protokołu TLS może wystąpić, jeśli protokół TLS 1.2 został wyłączony na serwerze lub używany jest serwer proxy. W przypadku korzystania z serwera proxy zalecamy sprawdzenie konfiguracji serwera proxy. Azure File Sync regiony usługi dodane po 5/1/2020 będą obsługiwały protokół TLS 1.2, a obsługa protokołu TLS 1.0 i 1,1 zostaną usunięte z istniejących regionów 1 sierpnia 2020.  Aby uzyskać więcej informacji, zobacz [Przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Agent Azure File Sync komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST Azure File Sync i protokołu FileREST, z których korzystają zawsze protokół HTTPS przez port 443. Azure File Sync nie wysyła nieszyfrowanych żądań za pośrednictwem protokołu HTTP. 

Konta usługi Azure Storage zawierają przełącznik umożliwiający Wymaganie szyfrowania podczas przesyłania, który jest domyślnie włączony. Nawet jeśli przełącznik na poziomie konta magazynu jest wyłączony, co oznacza, że nieszyfrowane połączenia z udziałami plików platformy Azure są możliwe, Azure File Sync będzie nadal używać tylko zaszyfrowanych kanałów w celu uzyskania dostępu do udziału plików.

Podstawowym powodem wyłączania szyfrowania podczas przesyłania dla konta magazynu jest obsługa starszej aplikacji, która musi być uruchomiona w starszym systemie operacyjnym, takim jak Windows Server 2008 R2 lub starsza dystrybucja systemu Linux, z udziałem plików platformy Azure bezpośrednio. Jeśli Starsza aplikacja znajduje się w pamięci podręcznej systemu Windows Server w udziale plików, przełączanie tego ustawienia nie będzie miało żadnego efektu. 

Zdecydowanie zalecamy zagwarantowanie, że szyfrowanie danych jest włączone.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Szyfrowanie udziałów plików platformy Azure w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włącz standardowe udziały plików do 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Dostępność regionów usługi Azure File Sync
Azure File Sync jest dostępny w następujących regionach:

| Chmura Azure | Region geograficzny | Region platformy Azure | Kod regionu |
|-------------|-------------------|--------------|-------------|
| Publiczne | Azja | Azja Wschodnia | `eastasia` |
| Publiczne | Azja | Southeast Asia | `southeastasia` |
| Publiczne | Australia | Australia Wschodnia | `australiaeast` |
| Publiczne | Australia | Australia Południowo-Wschodnia | `australiasoutheast` |
| Publiczne | Brazylia | Brazylia Południowa | `brazilsouth` |
| Publiczne | Kanada | Kanada Środkowa | `canadacentral` |
| Publiczne | Kanada | Kanada Wschodnia | `canadaeast` |
| Publiczne | Europa | Europa Północna | `northeurope` |
| Publiczne | Europa | West Europe | `westeurope` |
| Publiczne | Francja | Francja Środkowa | `francecentral` |
| Publiczne | Francja | Francja Południowa * | `francesouth` |
| Publiczne | Indie | Indie Środkowe | `centralindia` |
| Publiczne | Indie | Indie Południowe | `southindia` |
| Publiczne | Japonia | Japonia Wschodnia | `japaneast` |
| Publiczne | Japonia | Japonia Zachodnia | `japanwest` |
| Publiczne | Korea | Korea Środkowa | `koreacentral` |
| Publiczne | Korea | Korea Południowa | `koreasouth` |
| Publiczne | Republika Południowej Afryki | Północna Republika Południowej Afryki | `southafricanorth` |
| Publiczne | Republika Południowej Afryki | Zachodnia Republika Południowej Afryki * | `southafricawest` |
| Publiczne | Zjednoczone Emiraty Arabskie | Środkowe Zjednoczone Emiraty Arabskie * | `uaecentral` |
| Publiczne | Zjednoczone Emiraty Arabskie | Północne Zjednoczone Emiraty Arabskie | `uaenorth` |
| Publiczne | Zjednoczone Królestwo | Południowe Zjednoczone Królestwo | `uksouth` |
| Publiczne | Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | `ukwest` |
| Publiczne | USA | Central US | `centralus` |
| Publiczne | USA | East US | `eastus` |
| Publiczne | USA | Wschodnie stany USA 2 | `eastus2` |
| Publiczne | USA | Północno-środkowe stany USA | `northcentralus` |
| Publiczne | USA | South Central US | `southcentralus` |
| Publiczne | USA | Zachodnio-środkowe stany USA | `westcentralus` |
| Publiczne | USA | Zachodnie stany USA | `westus` |
| Publiczne | USA | Zachodnie stany USA 2 | `westus2` |
| US Gov | USA | US Gov Arizona | `usgovarizona` |
| US Gov | USA | US Gov Teksas | `usgovtexas` |
| US Gov | USA | US Gov Wirginia | `usgovvirginia` |

Azure File Sync obsługuje synchronizowanie tylko z udziałem plików platformy Azure, który znajduje się w tym samym regionie co usługa synchronizacji magazynu.

W przypadku regionów oznaczonych gwiazdkami należy skontaktować się z pomocą techniczną platformy Azure, aby zażądać dostępu do usługi Azure Storage w tych regionach. Proces został przedstawiony w [tym dokumencie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Magazyn Geograficznie nadmiarowy i strefa Geograficznie nadmiarowy mają możliwość ręcznego przełączenia w tryb failover do regionu pomocniczego. Firma Microsoft zaleca, aby nie robić tego poza awarią w przypadku korzystania z Azure File Sync ze względu na zwiększone prawdopodobieństwo utraty danych. W przypadku awarii, w której chcesz zainicjować ręczne przełączenie w tryb failover magazynu, musisz otworzyć przypadek pomocy technicznej w firmie Microsoft, aby uzyskać Azure File Sync wznowić synchronizację z pomocniczym punktem końcowym.

## <a name="migration"></a>Migracja
Jeśli masz istniejący serwer plików systemu Windows, Azure File Sync mogą być instalowane bezpośrednio, bez konieczności przenoszenia danych na nowy serwer. Jeśli planujesz migrację do nowego serwera plików systemu Windows w ramach wdrażania Azure File Sync, istnieje kilka możliwych podejścia do przenoszenia danych:

- Utwórz punkty końcowe serwera dla starego udziału plików i nowy udział plików, a Azure File Sync Zsynchronizuj dane między punktami końcowymi serwera. Zaletą tego podejścia jest to, że bardzo łatwo można zasubskrybować magazyn na nowym serwerze plików, ponieważ Azure File Sync to Obsługa warstw w chmurze. Gdy wszystko będzie gotowe, można wyciąć użytkowników końcowych do udziału plików na nowym serwerze i usunąć stary punkt końcowy serwera udziału plików.

- Utwórz punkt końcowy serwera tylko na nowym serwerze plików i skopiuj dane do ze starego udziału plików za pomocą polecenia `robocopy` . W zależności od topologii udziałów plików na nowym serwerze (liczba udziałów w poszczególnych woluminach, jak długo każdy wolumin jest itp.), może być konieczne tymczasowe zainicjowanie dodatkowego magazynu w miarę jak oczekiwano, że `robocopy` ze starego serwera do nowego serwera w lokalnym centrum danych zakończy się szybciej niż Azure File Sync przeniesie dane na platformę Azure.

Można również użyć urządzenie Data Box do migracji danych do wdrożenia Azure File Sync. W większości przypadków, gdy klienci chcą korzystać z urządzenie Data Box do pozyskiwania danych, to zrobią to, ponieważ ich zdaniem zwiększy szybkość wdrożenia lub będzie pomocna w scenariuszach z ograniczoną przepustowością. W związku z tym, że użycie urządzenie Data Box do pozyskiwania danych we wdrożeniu Azure File Sync spowoduje zmniejszenie wykorzystania przepustowości, prawdopodobnie będzie szybsze w przypadku większości scenariuszy w celu przeprowadzenia przekazywania danych online za pomocą jednej z metod opisanych powyżej. Aby dowiedzieć się więcej o tym, jak używać urządzenie Data Box do pozyskiwania danych do wdrożenia Azure File Sync, zobacz [Migrowanie danych do Azure File Sync z Azure Data Box](storage-sync-offline-data-transfer.md).

Typowym błędom podejmowanym przez klientów podczas migrowania danych do nowego wdrożenia Azure File Sync jest skopiowanie danych bezpośrednio do udziału plików platformy Azure, a nie na serwerach plików systemu Windows. Mimo że Azure File Sync zidentyfikuje wszystkie nowe pliki w udziale plików platformy Azure, a następnie zsynchronizuje je z udziałami plików systemu Windows, jest to zwykle znacznie wolniejsze niż ładowanie danych za pomocą serwera plików systemu Windows. W przypadku korzystania z narzędzi do kopiowania platformy Azure, takich jak AzCopy, ważne jest, aby użyć najnowszej wersji. Zapoznaj się z [tabelą narzędzia kopiowania plików](storage-files-migration-overview.md#file-copy-tools) , aby zapoznać się z omówieniem narzędzi do kopiowania na platformie Azure w celu zagwarantowania, że można skopiować wszystkie ważne metadane pliku, takie jak sygnatury czasowe i listy ACL.

## <a name="antivirus"></a>Oprogramowanie antywirusowe
Ponieważ oprogramowanie antywirusowe działa przez skanowanie plików pod kątem znanego złośliwego kodu, produkt antywirusowy może powodować odwoływanie się do plików warstwowych, co spowodowało wysokie opłaty za ruch wychodzący. W wersji 4,0 i większej od agenta Azure File Sync pliki warstwowe mają ustawiony atrybut Secure Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS. Firma Microsoft zaleca zapoznanie się z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować swoje rozwiązanie, aby pominąć odczytywanie plików z tym zestawem atrybutów (wiele do nich jest automatycznie). 

Wewnętrzne rozwiązania firmy Microsoft dotyczące oprogramowania antywirusowego, Windows Defender i System Center Endpoint Protection (SCEP) automatycznie pomijają odczytywanie plików, które mają ten zestaw atrybutów. Przetestowano i zidentyfikowano jeden drobny problem: po dodaniu serwera do istniejącej grupy synchronizacji pliki o rozmiarze mniejszym niż 800 bajtów są ponownie wywoływane (pobrane) na nowym serwerze. Te pliki pozostaną na nowym serwerze i nie zostaną warstwowe, ponieważ nie spełniają wymagań dotyczących rozmiaru warstwowego (>KB).

> [!Note]  
> Dostawcy oprogramowania antywirusowego mogą sprawdzić zgodność swojego produktu i Azure File Sync przy użyciu [zestawu testów zgodności z programem Azure File Sync Antivirus](https://www.microsoft.com/download/details.aspx?id=58322), który jest dostępny do pobrania w centrum pobierania Microsoft.

## <a name="backup"></a>Backup 
Jeśli włączono obsługę warstw w chmurze, nie należy używać rozwiązań bezpośrednio tworzących kopie zapasowe punktu końcowego serwera lub maszyny wirtualnej, na której znajduje się punkt końcowy serwera. Obsługa warstw w chmurze powoduje przechowywanie tylko podzestawu danych w punkcie końcowym serwera z pełnym zestawem danych znajdującym się w udziale plików platformy Azure. W zależności od użytego rozwiązania do tworzenia kopii zapasowych, pliki warstwowe zostaną pominięte, a ich kopia zapasowa nie zostanie utworzona (ponieważ mają zestaw atrybutów FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS) lub zostaną one odwołane do dysku, co spowodowało wysokie opłaty za ruch wychodzący. Zalecamy używanie rozwiązania do tworzenia kopii zapasowych w chmurze do bezpośredniego tworzenia kopii zapasowej udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Informacje o kopii zapasowej udziału plików platformy Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) lub skontaktuj się z dostawcą kopii zapasowej, aby sprawdzić, czy obsługują kopie zapasowe udziałów plików platformy Azure.

Jeśli wolisz używać lokalnego rozwiązania do tworzenia kopii zapasowych, kopie zapasowe powinny być wykonywane na serwerze w grupie synchronizacji z wyłączonymi warstwami chmury. Podczas przywracania należy użyć opcji przywracania na poziomie woluminu lub na poziomie pliku. Pliki przywrócone przy użyciu opcji przywracania na poziomie pliku zostaną zsynchronizowane ze wszystkimi punktami końcowymi w grupie synchronizacji, a istniejące pliki zostaną zastąpione wersją przywróconą z kopii zapasowej.  Przywrócenie na poziomie woluminu nie spowoduje zastąpienia nowszych wersji plików w udziale plików platformy Azure ani w innych punktach końcowych serwera.

> [!Note]  
> Przywracanie bez systemu operacyjnego (BMR) może spowodować nieoczekiwane wyniki i nie jest obecnie obsługiwane.

> [!Note]  
> W wersji 9 agenta Azure File Sync, migawki usługi VSS (w tym poprzednie wersje karty) są teraz obsługiwane na woluminach, na których włączono obsługę warstw w chmurze. Należy jednak włączyć zgodność poprzedniej wersji za poorednictwem programu PowerShell. [Dowiedz się, jak](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Następne kroki
* [Rozważ użycie ustawień zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
* [Wdrażanie usługi Pliki Azure](./storage-how-to-create-file-share.md)
* [Wdrażanie funkcji Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)

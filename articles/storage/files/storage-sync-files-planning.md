---
title: Planowanie wdrożenia usługi Azure File Sync | Dokumenty firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8666f51b88d2a70a2cb27e3606f24010771c8017
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460713"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planowanie wdrażania usługi Azure File Sync

:::row:::
    :::column:::
        [![Wywiad i demo wprowadzenie Azure File Sync - kliknij, aby grać!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Usługa Azure File Sync to usługa umożliwiająca buforowanie wielu udziałów plików platformy Azure na lokalnej maszynie wirtualnej systemu Windows Server lub cloud. 
        
        W tym artykule przedstawiono pojęcia i funkcje usługi Azure File Sync. Po zapoznaniu się z synchronizacji plików platformy Azure, należy rozważyć następujące [przewodnik wdrażania usługi Azure File Sync,](storage-sync-files-deployment-guide.md) aby wypróbować tę usługę.        
    :::column-end:::
:::row-end:::

Pliki będą przechowywane w chmurze w [udziałach plików platformy Azure](storage-files-introduction.md). Udziały plików platformy Azure mogą być używane na dwa sposoby: przez bezpośrednie instalowanie tych bezserwerowych udziałów plików platformy Azure (SMB) lub przez buforowanie udziałów plików platformy Azure lokalnie przy użyciu usługi Azure File Sync. Wybrana opcja wdrażania zmienia aspekty, które należy wziąć pod uwagę podczas planowania wdrożenia. 

- **Bezpośrednie instalowanie udziału plików platformy Azure:** Ponieważ usługa Azure Files zapewnia dostęp do SMB, można zainstalować udziały plików platformy Azure lokalnie lub w chmurze przy użyciu standardowego klienta SMB dostępnego w systemach Windows, macOS i Linux. Ponieważ udziały plików platformy Azure są bezserwerowe, wdrażanie w scenariuszach produkcyjnych nie wymaga zarządzania serwerem plików ani urządzeniem NAS. Oznacza to, że nie trzeba stosować poprawek oprogramowania ani wymieniać dysków fizycznych. 

- **Buforuj udział plików platformy Azure lokalnie za pomocą usługi Azure File Sync:** Usługa Azure File Sync umożliwia scentralizowanie udziałów plików organizacji w plikach azure, przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca lokalny (lub chmurowy) system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. 

## <a name="management-concepts"></a>Koncepcje zarządzania
Wdrożenie usługi Azure File Sync ma trzy podstawowe obiekty zarządzania:

- **Udział plików platformy Azure:** udział plików platformy Azure jest bezserwerowym udziałem plików w chmurze, który zapewnia *punkt końcowy w chmurze* relacji synchronizacji synchronizacji plików platformy Azure. Pliki w udziale plików platformy Azure są dostępne bezpośrednio za pomocą protokołu SMB lub FileREST, chociaż zachęcamy do przede wszystkim dostępu do plików za pośrednictwem pamięci podręcznej systemu Windows Server, gdy udział plików platformy Azure jest używany z synchronizacją plików platformy Azure. Dzieje się tak, ponieważ usługa Azure Files obecnie nie ma mechanizmu wykrywania zmian, takich jak Windows Server, więc zmiany w udziale plików platformy Azure bezpośrednio zajmie trochę czasu, aby propagować z powrotem do punktów końcowych serwera.
- **Punkt końcowy serwera:** ścieżka w systemie Windows Server, która jest synchronizowana z udziałem plików platformy Azure. Może to być określony folder na woluminie lub katalog główny woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli ich przestrzenie nazw nie nakładają się na siebie.
- **Grupa synchronizacji:** Obiekt definiujący relację synchronizacji między **punktem końcowym chmury**lub udziałem plików platformy Azure a punktem końcowym serwera. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Jeśli na przykład masz dwa różne zestawy plików, które chcesz zarządzać za pomocą usługi Azure File Sync, należy utworzyć dwie grupy synchronizacji i dodać różne punkty końcowe do każdej grupy synchronizacji.

### <a name="azure-file-share-management-concepts"></a>Pojęcia dotyczące zarządzania udziałami plików platformy Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Pojęcia dotyczące zarządzania synchronizacją plików platformy Azure
Grupy synchronizacji są wdrażane w **usługach synchronizacji magazynu,** które są obiektami najwyższego poziomu, które rejestrują serwery do użycia z synchronizacją plików platformy Azure i zawierają relacje z grupami synchronizacji. Zasób usługi synchronizacji magazynu jest równorzędnym zasobu konta magazynu i można je wdrożyć w grupach zasobów platformy Azure. Usługa synchronizacji magazynu może tworzyć grupy synchronizacji, które zawierają udziały plików platformy Azure na wielu kontach magazynu i wielu zarejestrowanych serwerach systemu Windows.

Przed utworzeniem grupy synchronizacji w usłudze synchronizacji magazynu należy najpierw zarejestrować system Windows Server w usłudze synchronizacji magazynu. Spowoduje to utworzenie zarejestrowanego obiektu **serwera,** który reprezentuje relację zaufania między serwerem lub klastrem a usługą synchronizacji magazynu. Aby zarejestrować usługę synchronizacji magazynu, należy najpierw zainstalować agenta usługi Azure File Sync na serwerze. Pojedynczy serwer lub klaster można zarejestrować tylko w jednej usłudze synchronizacji magazynu naraz.

Grupa synchronizacji zawiera jeden punkt końcowy chmury lub udział plików platformy Azure i co najmniej jeden punkt końcowy serwera. Obiekt punktu końcowego serwera zawiera ustawienia, które konfigurują możliwość **warstwowania w chmurze,** która zapewnia możliwość buforowania usługi Azure File Sync. Aby można było zsynchronizować z udziałem plików platformy Azure, konto magazynu zawierające udział plików platformy Azure musi znajdować się w tym samym regionie platformy Azure, co usługa synchronizacji magazynu.

### <a name="management-guidance"></a>Wskazówki dotyczące zarządzania
Podczas wdrażania usługi Azure File Sync zalecamy:

- Wdrażanie udziałów plików platformy Azure 1:1 z udziałami plików systemu Windows. Obiekt punktu końcowego serwera zapewnia duży stopień elastyczności w sposobie konfigurowania topologii synchronizacji po stronie serwera relacji synchronizacji. Aby uprościć zarządzanie, aby ścieżka punktu końcowego serwera była zgodna ze ścieżką udziału plików systemu Windows. 

- Użyj jak najmniejszej części usług synchronizacji magazynu, jak to możliwe. Uprości to zarządzanie, gdy masz grupy synchronizacji, które zawierają wiele punktów końcowych serwera, ponieważ system Windows Server może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu naraz. 

- Zwracanie uwagi na ograniczenia kont IOPS konta magazynu podczas wdrażania udziałów plików platformy Azure. W idealnym przypadku należy mapować udziały plików 1:1 z kontami magazynu, jednak nie zawsze może to być możliwe ze względu na różne ograniczenia i ograniczenia, zarówno w organizacji, jak i z platformy Azure. Jeśli nie jest możliwe wdrożenie tylko jednego udziału plików na jednym koncie magazynu, należy wziąć pod uwagę, które udziały będą bardzo aktywne, a które udziały będą mniej aktywne, aby upewnić się, że najgorętsze udziały plików nie zostaną umieszczone na tym samym koncie magazynu razem.

## <a name="windows-file-server-considerations"></a>Zagadnienia dotyczące serwera plików systemu Windows
Aby włączyć funkcję synchronizacji w systemie Windows Server, należy zainstalować agenta do pobrania synchronizacji plików azure. Agent usługi Azure File Sync `FileSyncSvc.exe`udostępnia dwa główne składniki: , w tle usługi systemu Windows, który `StorageSync.sys`jest odpowiedzialny za monitorowanie zmian w punktach końcowych serwera i inicjowanie sesji synchronizacji i filtr systemu plików, który umożliwia warstwowanie chmury i szybkie odzyskiwanie po awarii.  

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego
Usługa Azure File Sync jest obsługiwana w następujących wersjach systemu Windows Server:

| Wersja | Obsługiwane jednostki SKU | Obsługiwane opcje wdrażania |
|---------|----------------|------------------------------|
| Windows Server 2019 | Centrum danych, Standard i IoT | Pełny i podstawowy |
| Windows Server 2016 | Centrum danych, serwer standardowy i magazynowy | Pełny i podstawowy |
| Windows Server 2012 R2 | Centrum danych, serwer standardowy i magazynowy | Pełny i podstawowy |

Przyszłe wersje systemu Windows Server zostaną dodane w miarę ich wydania.

> [!Important]  
> Firma Microsoft zaleca aktualizowanie wszystkich serwerów używanych w usłudze Azure File Sync z najnowszymi aktualizacjami usługi Windows Update. 

### <a name="minimum-system-resources"></a>Minimalne zasoby systemowe
Usługa Azure File Sync wymaga serwera, fizycznego lub wirtualnego, z co najmniej jednym procesorem CPU i co najmniej 2 GiB pamięci.

> [!Important]  
> Jeśli serwer jest uruchomiony na maszynie wirtualnej z włączoną pamięcią dynamiczną, maszyna wirtualna powinna być skonfigurowana z co najmniej 2048 MiB pamięci.

W przypadku większości obciążeń produkcyjnych nie zaleca się konfigurowania serwera synchronizacji synchronizacji plików platformy Azure z minimalnymi wymaganiami. Aby uzyskać więcej informacji, zobacz [Zalecane zasoby systemowe.](#recommended-system-resources)

### <a name="recommended-system-resources"></a>Zalecane zasoby systemowe
Podobnie jak każda funkcja serwera lub aplikacji, wymagania dotyczące zasobów systemowych dla usługi Azure File Sync są określane przez skalę wdrożenia; większe wdrożenia na serwerze wymagają większych zasobów systemowych. W przypadku usługi Azure File Sync skala jest określana przez liczbę obiektów w punktach końcowych serwera i zmiany w zestawie danych. Pojedynczy serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji, a liczba obiektów wymienionych w poniższej tabeli odpowiada za pełny obszar nazw, do których jest dołączony serwer. 

Na przykład punkt końcowy serwera A z 10 milionami obiektów + punkt końcowy serwera B z 10 milionami obiektów = 20 milionów obiektów. W tym przykładzie wdrożenia zaleca się 8 procesorów, 16 GiB pamięci dla stanu ustalonego i (jeśli to możliwe) 48 GiB pamięci dla migracji początkowej.
 
Dane obszaru nazw są przechowywane w pamięci ze względu na wydajność. Z tego powodu większe przestrzenie nazw wymagają więcej pamięci, aby utrzymać dobrą wydajność, a więcej zmian wymaga więcej procesora CPU do przetworzenia. 
 
W poniższej tabeli podaliśmy zarówno rozmiar obszaru nazw, jak i konwersję na pojemność typowych udziałów plików ogólnego przeznaczenia, gdzie średni rozmiar pliku wynosi 512 KiB. Jeśli rozmiary plików są mniejsze, należy rozważyć dodanie dodatkowej pamięci dla tej samej pojemności. Oprzeć konfigurację pamięci na rozmiar przestrzeni nazw.

| Rozmiar obszaru nazw - pliki & katalogów (w milionach)  | Typowa pojemność (TiB)  | Rdzenie procesora  | Zalecana pamięć (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (synchronizacja początkowa)/ 2 (typowa churn)      |
| 5        | 2.3     | 2        | 16 (synchronizacja początkowa)/ 4 (typowa churn)    |
| 10       | 4.7     | 4        | 32 (synchronizacja początkowa)/ 8 (typowa churn)   |
| 30       | 14.0    | 8        | 48 (synchronizacja początkowa)/ 16 (typowa churn)   |
| 50       | 23.3    | 16       | 64 (synchronizacja początkowa)/ 32 (typowa churn)  |
| 100*     | 46.6    | 32       | 128 (synchronizacja początkowa)/ 32 (typowa churn)  |

\*Synchronizacja ponad 100 milionów plików & katalogów nie jest obecnie zalecana. Jest to miękki limit oparty na naszych testowanych progach. Aby uzyskać więcej informacji, zobacz [Usługi Azure Files skalowalności i docelowych wydajności](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Początkowa synchronizacja obszaru nazw jest operacją intensywną i zaleca się przydzielanie większej ilości pamięci do czasu zakończenia synchronizacji początkowej. Nie jest to wymagane, ale może przyspieszyć synchronizację początkową. 
> 
> Typowy współczynnik zmian wynosi 0,5% przestrzeni nazw zmieniającej się dziennie. W przypadku wyższych poziomów zmian należy rozważyć dodanie większej liczby procesorów. 

- Wolumin dołączony lokalnie sformatowany z systemem plików NTFS.

### <a name="evaluation-cmdlet"></a>Polecenie cmdlet oceny
Przed wdrożeniem usługi Azure File Sync należy ocenić, czy jest on zgodny z systemem przy użyciu polecenia cmdlet oceny usługi Azure File Sync. To polecenie cmdlet sprawdza potencjalne problemy z systemem plików i zestawem danych, takie jak nieobsługiwały znaki lub nieobsługiwała wersja systemu operacyjnego. Jego kontrole obejmują większość, ale nie wszystkie cechy wymienione poniżej; Zalecamy zapoznanie się z pozostałą częścią tej sekcji, aby upewnić się, że wdrożenie przebiega sprawnie. 

Polecenie cmdlet oceny można zainstalować, instalując moduł Az PowerShell, który można zainstalować, postępując zgodnie z instrukcjami tutaj: [Zainstaluj i skonfiguruj program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Sposób użycia  
Narzędzie do oceny można wywołać na kilka różnych sposobów: można przeprowadzać kontrole systemu, sprawdzać zestaw danych lub oba te elementy. Aby przeprowadzić kontrolę zarówno systemu, jak i zestawu danych: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Aby przetestować tylko zestaw danych:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Aby przetestować tylko wymagania systemowe:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Aby wyświetlić wyniki w pliku CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Zgodność z systemem plików
Usługa Azure File Sync jest obsługiwana tylko na bezpośrednio dołączonych woluminach NTFS. Bezpośrednio dołączony magazyn lub DAS w systemie Windows Server oznacza, że system operacyjny Windows Server jest właścicielem systemu plików. Usługa DAS może być dostarczana za pośrednictwem fizycznie dołączanych dysków do serwera plików, dołączanych dysków wirtualnych do maszyny Wirtualnej serwera plików (takich jak maszyna wirtualna obsługiwana przez funkcję Hyper-V), a nawet za pośrednictwem interfejsu ISCSI.

Obsługiwane są tylko woluminy NTFS; Systemy plików ReFS, FAT, FAT32 i inne systemy plików nie są obsługiwane.

W poniższej tabeli przedstawiono stan międzyoperacyjny funkcji systemu plików NTFS: 

| Funkcja | Stan obsługi | Uwagi |
|---------|----------------|-------|
| Listy kontroli dostępu (ACL) | W pełni obsługiwane | Listy uznaniowej kontroli dostępu w stylu systemu Windows są zachowywane przez usługę Azure File Sync i są wymuszane przez system Windows Server w punktach końcowych serwera. Listy ACL można również wymusić podczas bezpośredniego instalowania udziału plików platformy Azure, jednak wymaga to dodatkowej konfiguracji. Zobacz [sekcję Tożsamość, aby](#identity) uzyskać więcej informacji. |
| Twarde linki | Pominięto | |
| Dowiązania symboliczne | Pominięto | |
| Punkty montażu | Częściowo obsługiwane | Punkty instalacji mogą być katalogiem głównym punktu końcowego serwera, ale są pomijane, jeśli znajdują się w obszarze nazw punktu końcowego serwera. |
| Skrzyżowania | Pominięto | Na przykład foldery Rozproszony system plików DfrsrPrivate i DFSRoots. |
| Punkty ponownej analizy | Pominięto | |
| Kompresja NTFS | W pełni obsługiwane | |
| Rozrzedzone pliki | W pełni obsługiwane | Synchronizacja plików rozrzedzonych (nie są blokowane), ale są synchronizowane z chmurą jako pełny plik. Jeśli zawartość pliku zmieni się w chmurze (lub na innym serwerze), plik nie jest już rozrzedzony po pobraniu zmiany. |
| Alternatywne strumienie danych (ADS) | Zachowane, ale niezsynchronizowane | Na przykład tagi klasyfikacji utworzone przez infrastrukturę klasyfikacji plików nie są synchronizowane. Istniejące znaczniki klasyfikacji w plikach w każdym z punktów końcowych serwera pozostają nietknięte. |

<a id="files-skipped"></a>Usługa Azure File Sync pominie również niektóre pliki tymczasowe i foldery systemowe:

| Plik/folder | Uwaga |
|-|-|
| pagefile.sys | Plik specyficzny dla systemu |
| Desktop.ini | Plik specyficzny dla systemu |
| Thumbs.db | Tymczasowy plik miniatur |
| ehthumbs.db | Plik tymczasowy dla miniatur multimediów |
| ~$\*.\* | Plik tymczasowy pakietu Office |
| \*.tmp | Plik tymczasowy |
| \*.laccdb | Dostęp do pliku blokowania bazy danych|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Plik synchronizacji wewnętrznej|
| \\Informacje o woluminie systemowym | Folder specyficzny dla woluminu |
| $RECYCLE. Bin| Folder |
| \\SyncShareState (SyncShareState) | Folder do synchronizacji |

### <a name="failover-clustering"></a>Klaster trybu failover
Klastrowanie trybu failover systemu Windows Server jest obsługiwane przez usługę Azure File Sync dla opcji wdrażania "Serwer plików do użytku ogólnego". Klastrowanie trybu failover nie jest obsługiwane w programie "Skalowano w poziomie serwer plików dla danych aplikacji" (SOFS) ani w klastrowanych woluminach udostępnionych (CSV).

> [!Note]  
> Agent usługi Azure File Sync musi być zainstalowany w każdym węźle w klastrze trybu failover, aby synchronizacja działała poprawnie.

### <a name="data-deduplication"></a>Deduplikacja danych
**Systemy Windows Server 2016 i Windows Server 2019**   
Deduplikacja danych jest obsługiwana na woluminach z obsługą warstw w chmurze w systemach Windows Server 2016 i Windows Server 2019. Włączenie deduplikacji danych na woluminie z włączoną warstwą chmury umożliwia buforowanie większej liczby plików lokalnie bez inicjowania obsługi administracyjnej większej ilości miejsca do magazynowania. 

Gdy deduplikacja danych jest włączona na woluminie z włączoną warstwą chmury, zoptymalizowane pliki Dedup w lokalizacji punktu końcowego serwera będą warstwowe podobne do normalnego pliku opartego na ustawieniach zasad warstwowych w chmurze. Po Dedup zoptymalizowane pliki zostały warstwowe, zadanie wyrzucania elementów bezużytecznych deduplikacji danych zostanie uruchomiony automatycznie, aby odzyskać miejsce na dysku, usuwając niepotrzebne fragmenty, które nie są już odwołuje się do innych plików na woluminie.

Należy zauważyć, że oszczędności woluminów dotyczą tylko serwera; dane w udziale plików platformy Azure nie zostaną deduped.

> [!Note]  
> Aby obsługiwać deduplikację danych na woluminach z warstwą chmury włączoną w systemie Windows Server 2019, należy zainstalować aktualizację systemu Windows [KB4520062](https://support.microsoft.com/help/4520062) i być wymagany agent synchronizacji plików azure w wersji 9.0.0.0 lub nowszej.

**Windows Server 2012 R2**  
Usługa Azure File Sync nie obsługuje deduplikacji danych i warstw w chmurze na tym samym woluminie w systemie Windows Server 2012 R2. Jeśli deduplikacja danych jest włączona na woluminie, warstwowanie w chmurze musi być wyłączone. 

**Uwagi**
- Jeśli deduplikacja danych jest zainstalowana przed zainstalowaniem agenta usługi Azure File Sync, ponowne uruchomienie jest wymagane do obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie.
- Jeśli deduplikacja danych jest włączona na woluminie po włączeniu warstwy w chmurze, początkowe zadanie optymalizacji deduplikacji zoptymalizuje pliki na woluminie, które nie są jeszcze warstwowe i będą miały następujący wpływ na warstwy w chmurze:
    - Zasady wolnego miejsca będą nadal warstwy plików, jak na wolne miejsce na woluminie przy użyciu mapy cieplnej.
    - Zasady daty pominąć warstwowe plików, które mogły w przeciwnym razie kwalifikować się do warstwowania ze względu na zadanie optymalizacji deduplikacji dostępu do plików.
- W przypadku bieżących zadań optymalizacji deduplikacji warstwa chmury z zasadami daty zostanie opóźniona przez ustawienie [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) deduplikacji danych, jeśli plik nie jest jeszcze warstwowy. 
    - Przykład: Jeśli ustawienie MinimumFileAgeDays wynosi siedem dni, a zasady dotyczące warstw w chmurze to 30 dni, zasady dotyczące dat będą warstwowo aktualizować pliki po 37 dniach.
    - Uwaga: Gdy plik jest warstwowy przez usługę Azure File Sync, zadanie optymalizacji deduplikacji pominie plik.
- Jeśli serwer z systemem Windows Server 2012 R2 z zainstalowanym agentem synchronizacji plików azure jest uaktualniany do systemu Windows Server 2016 lub Windows Server 2019, należy wykonać następujące kroki w celu obsługi deduplikacji danych i warstw w chmurze na tym samym woluminie:  
    - Odinstaluj agenta usługi Azure File Sync dla systemu Windows Server 2012 R2 i uruchom ponownie serwer.
    - Pobierz agenta usługi Azure File Sync dla nowej wersji systemu operacyjnego serwera (Windows Server 2016 lub Windows Server 2019).
    - Zainstaluj agenta usługi Azure File Sync i uruchom ponownie serwer.  
    
    Uwaga: Ustawienia konfiguracji synchronizacji plików platformy Azure na serwerze są zachowywane po odinstalowaniu agenta i ponownym zainstalowaniu.

### <a name="distributed-file-system-dfs"></a>Rozproszony system plików (DFS)
Usługa Azure File Sync obsługuje interop z obszarami nazw systemu plików DFS (DFS-N) i replikacją systemu plików DFS (DFS-R).

**Obszary nazw systemu plików DFS (DFS-N)**: Usługa Azure File Sync jest w pełni obsługiwana na serwerach DFS-N. Agent usługi Azure File Sync można zainstalować na co najmniej jednym człowienicy systemu plików DFS-N, aby zsynchronizować dane między punktami końcowymi serwera a punktem końcowym w chmurze. Aby uzyskać więcej informacji, zobacz [Omówienie obszarów nazw systemu plików DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikacja systemu plików DFS (DFS-R)**: Ponieważ DFS-R i Azure File Sync są rozwiązaniami replikacji, w większości przypadków zalecamy zastąpienie systemu DFS-R synchronizacją plików azure. Istnieje jednak kilka scenariuszy, w których chcesz używać DFS-R i Azure File Sync razem:

- Przeprowadzasz migrację z wdrożenia systemu DFS-R do wdrożenia usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [Migrowanie wdrożenia replikacji systemu plików DFS (DFS-R) do usługi Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nie każdy serwer lokalny, który potrzebuje kopii danych plików, może być połączony bezpośrednio z Internetem.
- Serwery oddziałów konsolidują dane na jednym serwerze centralnym, dla którego chcesz użyć usługi Azure File Sync.

Aby usługa Azure File Sync i DFS-R działała obok siebie:

1. Warstwowa chmura usługi Azure File Sync musi być wyłączona na woluminach z folderami replikowanymi systemu plików DFS-R.
2. Nie należy konfigurować punktów końcowych serwera w folderach replikacji tylko do odczytu systemu DFS-R.

Aby uzyskać więcej informacji, zobacz [Omówienie replikacji systemu plików DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Korzystanie z narzędzia sysprep na serwerze z zainstalowanym agentem usługi Azure File Sync nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. Instalacja agenta i rejestracja serwera powinny nastąpić po wdrożeniu obrazu serwera i zakończeniu minikonfiguracji sysprep.

### <a name="windows-search"></a>Usługa Windows Search
Jeśli warstwa w chmurze jest włączona w punkcie końcowym serwera, pliki, które są warstwowe są pomijane i nie indeksowane przez usługę Windows Search. Pliki niewarstwowe są poprawnie indeksowane.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Inne rozwiązania do zarządzania pamięcią masową hierarchiczną (HSM)
Żadne inne rozwiązania HSM nie powinny być używane z synchronizacją plików platformy Azure.

## <a name="identity"></a>Tożsamość
Usługa Azure File Sync współpracuje ze standardową tożsamością opartą na usłudze AD bez żadnych specjalnych konfiguracji poza konfigurowaniem synchronizacji. Podczas korzystania z usługi Azure File Sync ogólne oczekiwania jest to, że większość dostępów przejść za pośrednictwem serwerów buforowania usługi Azure File Sync, a nie za pośrednictwem udziału plików platformy Azure. Ponieważ punkty końcowe serwera znajdują się w systemie Windows Server, a system Windows Server obsługuje listy ACL w stylu AD i Windows przez bardzo długi czas, nic nie jest potrzebne poza zapewnieniem, że serwery plików systemu Windows zarejestrowane w usłudze synchronizacji magazynu są przyłączone do domeny. Usługa Azure File Sync będzie przechowywać listy ACL na plikach w udziale plików platformy Azure i będzie replikować je do wszystkich punktów końcowych serwera.

Mimo że zmiany wprowadzone bezpośrednio do udziału plików platformy Azure potrwa dłużej, aby zsynchronizować z punktami końcowymi serwera w grupie synchronizacji, można również upewnić się, że można wymusić uprawnienia usługi AD do udziału plików bezpośrednio w chmurze, jak również. Aby to zrobić, musisz dołączyć do konta magazynu do lokalnej usługi AD, podobnie jak przyłączenie serwerów plików systemu Windows do domeny. Aby dowiedzieć się więcej o dołączaniu konta magazynu do należącej do klienta usługi Active Directory, zobacz [Omówienie usługi Azure Files Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> Do pomyślnego wdrożenia usługi Azure File Sync nie jest wymagane dołączanie konta magazynu do usługi Active Directory. Jest to ściśle opcjonalny krok, który umożliwia udział plików platformy Azure do wymuszania lokalnych list ACL, gdy użytkownicy montują udział plików platformy Azure bezpośrednio.

## <a name="networking"></a>Networking
Agent synchronizacji plików platformy Azure komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST synchronizacji plików azure i protokołu FileREST, z których oba zawsze używają protokołu HTTPS przez port 443. SMB nigdy nie jest używany do przekazywania lub pobierania danych między systemem Windows Server i udziału plików platformy Azure. Ponieważ większość organizacji zezwala na ruch HTTPS przez port 443, jako wymóg odwiedzania większości witryn sieci Web, specjalna konfiguracja sieci zwykle nie jest wymagana do wdrożenia usługi Azure File Sync.

Na podstawie zasad organizacji lub unikatowych wymagań regulacyjnych może wymagać bardziej restrykcyjnej komunikacji z platformą Azure, a zatem usługa Azure File Sync zapewnia kilka mechanizmów konfigurowania sieci. W zależności od wymagań możesz:

- Synchronizacja tuneli i ruch przesyłania/pobierania plików za pośrednictwem usługi ExpressRoute lub usługi Azure VPN. 
- Korzystaj z usług Azure Files i funkcji sieci azure, takich jak punkty końcowe usługi i prywatne punkty końcowe.
- Skonfiguruj usługę Azure File Sync do obsługi serwera proxy w twoim środowisku.
- Ograniczanie aktywności sieciowej z synchronizacji plików platformy Azure.

Aby dowiedzieć się więcej na temat konfigurowania funkcji sieciowej usługi Azure File Sync, zobacz:
- [Ustawienia serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md)
- [Zapewnienie, że usługa Azure File Sync jest dobrym sąsiadem w twoim centrum danych](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Szyfrowanie
Podczas korzystania z usługi Azure File Sync istnieją trzy różne warstwy szyfrowania do rozważenia: szyfrowanie w magazynie w spoczynku systemu Windows Server, szyfrowanie podczas przesyłania między agentem usługi Azure File Sync i azure oraz szyfrowanie w pozostałej części danych w udziale plików platformy Azure. 

### <a name="windows-server-encryption-at-rest"></a>Szyfrowanie systemu Windows Server w spoczynku 
Istnieją dwie strategie szyfrowania danych w systemie Windows Server, które działają ogólnie z synchronizacją plików platformy Azure: szyfrowanie pod systemem plików, takie, że system plików i wszystkie zapisane do niego dane są szyfrowane, a szyfrowanie w samym formacie pliku. Metody te nie wykluczają się wzajemnie; mogą być używane razem w razie potrzeby, ponieważ cel szyfrowania jest inny.

Aby zapewnić szyfrowanie pod systemem plików, system Windows Server udostępnia skrzynkę odbiorczą funkcji BitLocker. Funkcja BitLocker jest w pełni przezroczysta dla usługi Azure File Sync. Głównym powodem użycia mechanizmu szyfrowania, takiego jak funkcja BitLocker, jest zapobieganie fizycznemu eksfiltracji danych z lokalnego centrum danych przez osobę kradnącą dyski i zapobieganie obciążaniu bocznemu nieautoryzowanego systemu operacyjnego w celu wykonywania nieautoryzowanych odczytów/zapisów na danych. Aby dowiedzieć się więcej o funkcji BitLocker, zobacz [Omówienie funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkty innych firm, które działają podobnie do funkcji BitLocker, ponieważ znajdują się pod woluminem NTFS, powinny podobnie działać w pełni przejrzyście z synchronizacją plików platformy Azure. 

Inną główną metodą szyfrowania danych jest szyfrowanie strumienia danych pliku, gdy aplikacja zapisuje plik. Niektóre aplikacje mogą to zrobić natywnie, jednak zwykle tak nie jest. Przykładem metody szyfrowania strumienia danych pliku jest usługa Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Głównym powodem użycia mechanizmu szyfrowania, takiego jak AIP/RMS, jest zapobieganie eksfiltracji danych z udziału plików przez osoby kopiujące je do alternatywnych lokalizacji, na przykład na dysk flash, lub wysyłające je do osoby nieupoważnionej. Gdy strumień danych pliku jest szyfrowany jako część formatu pliku, ten plik będzie nadal szyfrowany w udziale plików platformy Azure. 

Usługa Azure File Sync nie współpracuje z systemem plików szyfrowania NTFS (NTFS EFS) ani rozwiązaniami szyfrowania innych firm, które znajdują się nad systemem plików, ale poniżej strumienia danych pliku. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Agent usługi Azure File Sync komunikuje się z usługą synchronizacji magazynu i udziałem plików platformy Azure przy użyciu protokołu REST synchronizacji plików Azure i protokołu FileREST, z których oba zawsze używają protokołu HTTPS za pośrednictwem portu 443. Usługa Azure File Sync nie wysyła niezaszyfrowanych żądań za pośrednictwem protokołu HTTP. 

Konta magazynu platformy Azure zawierają przełącznik wymagający szyfrowania podczas przesyłania, który jest domyślnie włączony. Nawet jeśli przełącznik na poziomie konta magazynu jest wyłączona, co oznacza, że możliwe są niezaszyfrowane połączenia z udziałami plików platformy Azure, usługa Azure File Sync nadal będzie używać tylko zaszyfrowanych kanałów w celu uzyskania dostępu do udziału plików.

Głównym powodem wyłączenia szyfrowania podczas przesyłania dla konta magazynu jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak dystrybucja systemu Windows Server 2008 R2 lub starsza, rozmawiając bezpośrednio z udziałem plików platformy Azure. Jeśli starsza aplikacja rozmawia z pamięcią podręczną windows server udziału plików, przełączanie to ustawienie nie będzie miało wpływu. 

Zdecydowanie zalecamy zapewnienie szyfrowania przesyłanych danych.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w magazynie platformy Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Szyfrowanie udziału plików platformy Azure w spoczynku
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Warstwy magazynowania
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Włącz standardowe udziały plików do 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Dostępność regionu synchronizacji plików platformy Azure
Usługa Azure File Sync jest dostępna w następujących regionach:

| Chmura Azure | Region geograficzny | Region platformy Azure | Kod regionu |
|-------------|-------------------|--------------|-------------|
| Public | Azja | Azja Wschodnia | `eastasia` |
| Public | Azja | Azja Południowo-Wschodnia | `southeastasia` |
| Public | Australia | Australia Wschodnia | `australiaeast` |
| Public | Australia | Australia Południowo-Wschodnia | `australiasoutheast` |
| Public | Brazylia | Brazylia Południowa | `brazilsouth` |
| Public | Kanada | Kanada Środkowa | `canadacentral` |
| Public | Kanada | Kanada Wschodnia | `canadaeast` |
| Public | Europa | Europa Północna | `northeurope` |
| Public | Europa | Europa Zachodnia | `westeurope` |
| Public | Francja | Francja Środkowa | `francecentral` |
| Public | Francja | Francja Południowa* | `francesouth` |
| Public | Indie | Indie Środkowe | `centralindia` |
| Public | Indie | Indie Południowe | `southindia` |
| Public | Japonia | Japonia Wschodnia | `japaneast` |
| Public | Japonia | Japonia Zachodnia | `japanwest` |
| Public | Korea | Korea Środkowa | `koreacentral` |
| Public | Korea | Korea Południowa | `koreasouth` |
| Public | Republika Południowej Afryki | Republika Południowej Afryki Północ | `southafricanorth` |
| Public | Republika Południowej Afryki | Republika Południowej Afryki Zachód* | `southafricawest` |
| Public | Zjednoczone Emiraty Arabskie | Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Ameryki* | `uaecentral` |
| Public | Zjednoczone Emiraty Arabskie | Zjednoczone Emiraty Północne | `uaenorth` |
| Public | Zjednoczone Królestwo | Południowe Zjednoczone Królestwo | `uksouth` |
| Public | Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | `ukwest` |
| Public | USA | Środkowe stany USA | `centralus` |
| Public | USA | Wschodnie stany USA | `eastus` |
| Public | USA | Wschodnie stany USA 2 | `eastus2` |
| Public | USA | Północno-środkowe stany USA | `northcentralus` |
| Public | USA | Południowo-środkowe stany USA | `southcentralus` |
| Public | USA | Zachodnio-środkowe stany USA | `westcentralus` |
| Public | USA | Zachodnie stany USA | `westus` |
| Public | USA | Zachodnie stany USA 2 | `westus2` |
| US Gov | USA | US Gov Arizona | `usgovarizona` |
| US Gov | USA | US Gov Teksas | `usgovtexas` |
| US Gov | USA | US Gov Wirginia | `usgovvirginia` |

Usługa Azure File Sync obsługuje synchronizację tylko z udziałem plików platformy Azure, który jest w tym samym regionie co usługa synchronizacji magazynu.

W przypadku regionów oznaczonych gwiazdkami należy skontaktować się z pomocą techniczną platformy Azure, aby zażądać dostępu do usługi Azure Storage w tych regionach. Proces ten jest opisany w [tym dokumencie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Nadmiarowość
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Magazyn geograficznie nadmiarowy i nadmiarowy strefy geograficznej ma możliwość ręcznego pracy awaryjnej magazynu do regionu pomocniczego. Zaleca się, aby nie robić tego poza katastrofą podczas korzystania z usługi Azure File Sync ze względu na zwiększone prawdopodobieństwo utraty danych. W przypadku awarii, w której chcesz zainicjować ręczną opcję pracy awaryjnej magazynu, należy otworzyć przypadek pomocy technicznej z firmą Microsoft, aby uzyskać synchronizację plików platformy Azure, aby wznowić synchronizację z pomocniczym punktem końcowym.

## <a name="migration"></a>Migracja
Jeśli masz istniejący serwer plików systemu Windows, usługa Azure File Sync może być instalowana bezpośrednio w miejscu, bez konieczności przenoszenia danych na nowy serwer. Jeśli planujesz migrację na nowy serwer plików systemu Windows w ramach wdrażania usługi Azure File Sync, istnieje kilka możliwych metod przenoszenia danych:

- Utwórz punkty końcowe serwera dla starego udziału plików i nowego udziału plików i pozwól synchronizacji danych między punktami końcowymi serwera. Zaletą tego podejścia jest to, że bardzo ułatwia oversubscribe magazynu na nowym serwerze plików, ponieważ usługa Azure File Sync jest chmura warstwowa. Gdy będzie można gotowe, można wyciąć użytkowników końcowych do udziału plików na nowym serwerze i usunąć stary udział plików punktu końcowego serwera.

- Utwórz punkt końcowy serwera tylko na nowym serwerze plików i `robocopy`skopiuj dane ze starego udziału plików przy użyciu programu . W zależności od topologii udziałów plików na nowym serwerze (ile udziałów masz na każdym woluminie, jak wolny jest każdy `robocopy` wolumin itp.), może być konieczne tymczasowe aprowizowanie dodatkowego magazynu, ponieważ oczekuje się, że ze starego serwera na nowy serwer w lokalnym centrum danych zostanie ukończony szybciej niż usługa Azure File Sync przeniesie dane na platformę Azure.

Istnieje również możliwość użycia pola danych do migracji danych do wdrożenia usługi Azure File Sync. W większości przypadków, gdy klienci chcą używać pola danych do pozyskiwania danych, robią to, ponieważ uważają, że zwiększy szybkość ich wdrożenia lub ponieważ pomoże w scenariuszach ograniczonej przepustowości. Chociaż prawdą jest, że przy użyciu pola danych do pozyskiwania danych do wdrożenia usługi Azure File Sync zmniejszy wykorzystanie przepustowości, prawdopodobnie będzie szybciej dla większości scenariuszy do realizacji przekazywania danych online za pomocą jednej z metod opisanych powyżej. Aby dowiedzieć się więcej na temat używania pola danych do pozyskiwania danych do wdrożenia usługi Azure File Sync, zobacz [Migrowanie danych do usługi Azure File Sync za pomocą usługi Azure Data Box](storage-sync-offline-data-transfer.md).

Typowym błędem popełnianym przez klientów podczas migracji danych do nowego wdrożenia usługi Azure File Sync jest kopiowanie danych bezpośrednio do udziału plików platformy Azure, a nie na ich serwerach plików systemu Windows. Mimo że usługa Azure File Sync zidentyfikuje wszystkie nowe pliki w udziale plików platformy Azure i zsynchronizuje je z powrotem do udziałów plików systemu Windows, jest to zazwyczaj znacznie wolniejsze niż ładowanie danych za pośrednictwem serwera plików systemu Windows. Wiele narzędzi do kopiowania platformy Azure, takich jak AzCopy, ma dodatkową wadę nie kopiowania wszystkich ważnych metadanych pliku, takich jak znaczniki czasu i listy ACL.

## <a name="antivirus"></a>Oprogramowanie antywirusowe
Ponieważ program antywirusowy działa poprzez skanowanie plików w poszukiwaniu znanego złośliwego kodu, produkt antywirusowy może spowodować wycofanie plików warstwowych. W wersjach 4.0 i powyżej agenta usługi Azure File Sync pliki warstwowe mają bezpieczny atrybut systemu Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony. Zalecamy skonsultowanie się z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować ich rozwiązanie, aby pominąć czytanie plików z tym zestawem atrybutów (wielu robi to automatycznie). 

Rozwiązania antywirusowe firmy Microsoft, Windows Defender i System Center Endpoint Protection (SCEP), automatycznie pomijają odczyt plików, które mają ten atrybut. Przetestowaliśmy je i zidentyfikowaliśmy jeden drobny problem: po dodaniu serwera do istniejącej grupy synchronizacji pliki mniejsze niż 800 bajtów są przywoływanye (pobierane) na nowym serwerze. Pliki te pozostaną na nowym serwerze i nie będą warstwowe, ponieważ nie spełniają wymagań dotyczących rozmiaru warstwowego (>64 kb).

> [!Note]  
> Dostawcy oprogramowania antywirusowego mogą sprawdzać zgodność między swoim produktem a synchronizacją plików platformy Azure za pomocą [pakietu testów zgodności antywirusowej synchronizacji plików azure,](https://www.microsoft.com/download/details.aspx?id=58322)który jest dostępny do pobrania w Centrum pobierania Firmy Microsoft.

## <a name="backup"></a>Backup 
Podobnie jak rozwiązania antywirusowe, rozwiązania do tworzenia kopii zapasowych mogą powodować wycofywanie plików warstwowych. Zalecamy użycie rozwiązania do tworzenia kopii zapasowych w chmurze do tworzenia kopii zapasowych udziału plików platformy Azure zamiast lokalnego produktu do tworzenia kopii zapasowych.

Jeśli używasz lokalnego rozwiązania do tworzenia kopii zapasowych, kopie zapasowe powinny być wykonywane na serwerze w grupie synchronizacji, która ma warstwy chmurowe wyłączone. Podczas przywracania należy użyć opcji przywracania na poziomie woluminu lub pliku. Pliki przywrócone przy użyciu opcji przywracania na poziomie pliku zostaną zsynchronizowane ze wszystkimi punktami końcowymi w grupie synchronizacji, a istniejące pliki zostaną zastąpione wersją przywróconą z kopii zapasowej.  Przywracanie na poziomie woluminu nie zastąpi nowszych wersji plików w udziale plików platformy Azure lub innych punktach końcowych serwera.

> [!Note]  
> Przywracanie typu BARE-metal (BMR) może spowodować nieoczekiwane wyniki i nie jest obecnie obsługiwane.

> [!Note]  
> W wersji 9 agenta synchronizacji plików platformy Azure migawki usługi VSS (w tym karta Poprzednie wersje) są teraz obsługiwane na woluminach, które mają włączoną warstwę chmury. Jednak należy włączyć zgodność poprzedniej wersji za pośrednictwem programu PowerShell. [Dowiedz się, jak to zrobić](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Następne kroki
* [Rozważ ustawienia zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
* [Planowanie wdrażania usługi Pliki Azure](storage-files-planning.md)
* [Wdrażanie usługi Pliki Azure](storage-files-deployment-guide.md)
* [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
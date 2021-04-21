---
title: Jak zarządzać Azure File Sync plików warstwowych | Microsoft Docs
description: Porady i polecenia poleceń programu PowerShell, które ułatwiają zarządzanie plikami warstwami
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796870"
---
# <a name="how-to-manage-tiered-files"></a>Jak zarządzać plikami warstwami

Ten artykuł zawiera wskazówki dla użytkowników, którzy mają pytania dotyczące zarządzania plikami warstwowym. Aby uzyskać koncepcyjne pytania dotyczące warstw w chmurze, zobacz [często zadawane pytania Azure Files chmurze.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Jak sprawdzić, czy pliki są warstwowe

Określa, czy pliki muszą być warstwowe na zestaw zasad jest oceniane raz na godzinę. Po utworzeniu nowego punktu końcowego serwera mogą natknąć się na dwie sytuacje:

Po pierwszym dodaniu nowego punktu końcowego serwera pliki często istnieją w tej lokalizacji serwera. Należy je przekazać przed rozpoczęciem warstw w chmurze. Zasady wolnego miejsca na woluminie nie rozpoczną pracy, dopóki początkowe przekazywanie wszystkich plików nie zostanie zakończone. Jednak opcjonalne zasady daty zaczną działać na podstawie poszczególnych plików, natychmiast po przesłaniu pliku. W tym miejscu jest również stosowana jedna godzina interwału. 

Po dodaniu nowego punktu końcowego serwera można połączyć pustą lokalizację serwera z udziałem plików platformy Azure z danymi w nim. Jeśli zdecydujesz się pobrać przestrzeń nazw i odwołać zawartość podczas początkowego pobierania na serwer, po jej zakończeniu pliki zostaną odwołane na podstawie sygnatury czasowej ostatniej modyfikacji do momentu, gdy zostaną osiągnięte zasady wolnego miejsca na woluminie i opcjonalne limity zasad daty.

Istnieje kilka sposobów sprawdzania, czy plik został warstwowy w udziałach plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku.**
     Kliknij prawym przyciskiem myszy plik, przejdź do **szczegółów**, a następnie przewiń w dół do **właściwości** Atrybuty. Plik warstwowy ma ustawione następujące atrybuty:     
        
        | Litera atrybutu | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że kopia zapasowa pliku powinna być backup oprogramowania. Ten atrybut jest zawsze ustawiony, niezależnie od tego, czy plik jest warstwowy, czy przechowywany w pełni na dysku. |
        | P | Plik rozrzedowy | Wskazuje, że plik jest plikiem rozrzedowym. Plik rozrzedny to wyspecjalizowany typ pliku, który oferuje system plików NTFS do wydajnego użycia, gdy plik w strumieniu dysku jest w większości pusty. Azure File Sync używa plików rozrzedowanych, ponieważ plik jest w pełni warstwowy lub częściowo odwołany. W pliku w pełni warstwowym strumień plików jest przechowywany w chmurze. W częściowo odwołanej pliku ta część pliku znajduje się już na dysku. Może się to zdarzyć, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip. Jeśli plik jest w pełni odwołany do dysku, Azure File Sync konwertuje go z pliku rozrzedziowego na zwykły plik. Ten atrybut jest ustawiany tylko w systemie Windows Server 2016 i starszych.|
        | M | Przypomnij sobie informacje o dostępie do danych | Wskazuje, że dane pliku nie są w pełni obecne w magazynie lokalnym. Odczytanie pliku spowoduje pobranie co najmniej części zawartości pliku z udziału plików platformy Azure, z którym jest połączony punkt końcowy serwera. Ten atrybut jest ustawiany tylko w systemie Windows Server 2019. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej ponownej dystrybucji. Punkt ponownej kontroli jest specjalnym wskaźnikiem do użycia przez filtr systemu plików. Azure File Sync używa punktów ponownej kontroli do definiowania Azure File Sync systemu plików (StorageSync.sys) lokalizacji w chmurze, w której plik jest przechowywany. Obsługuje to bezproblemowy dostęp. Użytkownicy nie muszą wiedzieć, że Azure File Sync jest używany ani jak uzyskać dostęp do pliku w twoim udziałach plików platformy Azure. Gdy plik jest w pełni odwołany, Azure File Sync usuwa punkt ponownej dystrybucji z pliku. |
        | O | Tryb offline | Wskazuje, że część lub cała zawartość pliku nie jest przechowywana na dysku. Gdy plik jest w pełni odwołany, Azure File Sync usuwa ten atrybut. |

        ![Okno dialogowe Właściwości pliku z wybraną kartą Szczegóły](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Atrybuty dla wszystkich plików w folderze można wyświetlić, dodając pole **Atrybuty** do wyświetlania tabeli Eksplorator plików. W tym celu kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **Rozmiar),** wybierz pozycję **Więcej,** a następnie wybierz pozycję **Atrybuty** z listy rozwijanej.
        
        > [!NOTE]
        > Wszystkie te atrybuty będą widoczne również dla częściowo odwołanych plików.
        
   * **Użyj `fsutil` , aby sprawdzić punkty ponownej kontroli w pliku.**
       Jak opisano w poprzedniej opcji, plik warstwowy zawsze ma ustawiony punkt ponownej dystrybucji. Punkt ponownej kontroli umożliwia sterownikowi Azure File Sync plików (StorageSync.sys) pobieranie zawartości z udziałów plików platformy Azure, które nie są przechowywane lokalnie na serwerze. 

       Aby sprawdzić, czy plik ma punkt ponownej kontroli, w wierszu polecenia z podwyższonym poziomem uprawnień lub w oknie programu PowerShell uruchom `fsutil` narzędzie:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Jeśli plik ma punkt ponownej ponownej dystrybucji, możesz oczekiwać, że zobaczysz wartość tagu ponownej 0x8000001e **.** Ta wartość szesnastkowa jest wartością punktu ponownej ponownej sprzedaży, która jest własnością Azure File Sync. Dane wyjściowe zawierają również dane ponownej wymiany, które reprezentują ścieżkę do pliku w udziałach plików platformy Azure.
        
        > [!WARNING]  
        > Narzędzie ma również możliwość usunięcia punktu `fsutil reparsepoint` ponownej ponownej ponownej konfiguracji. Nie należy wykonywać tego polecenia, chyba że Azure File Sync inżynieryjny poprosi o to. Uruchomienie tego polecenia może spowodować utratę danych. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Jak wykluczyć aplikacje ze śledzenia czasu ostatniego dostępu do warstw w chmurze

Dzięki Azure File Sync 11.1 można teraz wykluczać aplikacje ze śledzenia ostatniego dostępu. Gdy aplikacja uzyskuje dostęp do pliku, czas ostatniego dostępu do pliku jest aktualizowany w bazie danych warstw w chmurze. Aplikacje, które skanują system plików, taki jak oprogramowanie antywirusowe, powodują, że wszystkie pliki mają ten sam czas ostatniego dostępu, co ma wpływ na warstwy plików.

Aby wykluczyć aplikacje ze śledzenia czasu ostatniego dostępu, dodaj nazwę procesu do ustawienia rejestru HeatTrackingProcessNameExclusionList znajdującego się w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Przykład: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Procesy deduplikacji danych i Resource Manager plików (FSRM) są domyślnie wykluczone. Zmiany na liście wykluczeń procesów są honorowane przez system co 5 minut.

## <a name="how-to-access-the-heat-store"></a>Jak uzyskać dostęp do magazynu cieplnego

Warstwy w chmurze wykorzystują czas ostatniego dostępu i częstotliwość dostępu do pliku, aby określić, które pliki mają być warstwowe. Sterownik filtru warstw w chmurze (storagesync.sys) śledzi czas ostatniego dostępu i rejestruje informacje w magazynie cieplnym warstw w chmurze. Magazyn cieplny można pobrać i zapisać w pliku CSV za pomocą polecenia cmdlet programu PowerShell na serwerze lokalnym.

Istnieje jeden magazyn ciepła dla wszystkich plików na tym samym woluminie. Magazyn ciepła może być bardzo duży. Jeśli musisz pobrać tylko "najfajnową" liczbę elementów, użyj wartości -Limit i liczby, a także rozważ filtrowanie według ścieżki podrzędnej względem katalogu głównego woluminu.

- Zaimportuj moduł programu PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- WOLNE MIEJSCE NA WOLUMINIE: aby uzyskać kolejność, w której pliki będą warstwowe przy użyciu zasad wolnego miejsca na woluminie:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- ZASADY DATY: aby uzyskać kolejność, w jakiej pliki będą warstwowe, przy użyciu zasad daty:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Znajdź informacje o magazynie cieplnym dla określonego pliku:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Wyświetl wszystkie pliki w kolejności malejącej według czasu ostatniego dostępu:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Zobacz kolejność, według której pliki warstwowe będą przywoływyne w tle lub na żądanie za pośrednictwem programu PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Jak wymusić, aby plik lub katalog był warstwowy

> [!NOTE]
> Po wybraniu katalogu, który ma być warstwowy, tylko pliki aktualnie w katalogu są warstwowe. Wszystkie pliki utworzone po tym czasie nie są automatycznie warstwowe.

Po włączeniu funkcji obsługi warstw w chmurze funkcja obsługi warstw w chmurze automatycznie warstwuje pliki na podstawie czasu ostatniego dostępu i modyfikacji, aby osiągnąć procent wolnego miejsca w woluminie określony w punkcie końcowym chmury. Czasami jednak może być konieczne ręczne wymuszczenie pliku na warstwie. Może to być przydatne, jeśli zapiszemy duży plik, którego nie zamierzasz używać ponownie przez długi czas, i chcesz, aby wolne miejsce na woluminie było teraz dostępne dla innych plików i folderów. Możesz wymusić stosowanie warstw przy użyciu następujących poleceń programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Jak odwołać plik warstwowy do dysku

Najprostszym sposobem odwołania pliku na dysk jest otwarcie pliku. Filtr Azure File Sync plików (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez żadnej pracy po Twojej stronie. W przypadku typów plików, które mogą być częściowo odczytywane lub przesyłane strumieniowo, takich jak pliki multimedialne lub zip, po prostu otwarcie pliku nie gwarantuje pobrania całego pliku.

Aby mieć pewność, że plik zostanie w pełni pobrany na dysk lokalny, należy użyć programu PowerShell, aby wymusić pełne odwołanie pliku. Ta opcja może być również przydatna, jeśli chcesz odwołać wiele plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell w węźle serwera, w którym Azure File Sync jest zainstalowana, a następnie uruchom następujące polecenia programu PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametry opcjonalne:
* `-Order CloudTieringPolicy` Program będzie najpierw przywołyowyał ostatnio zmodyfikowane lub używane pliki i jest dozwolony przez bieżące zasady warstw. 
    * Jeśli skonfigurowano zasady dotyczące wolnego miejsca na woluminie, pliki będą przywoływyne do momentu osiągnięciu ustawienia zasad wolnego miejsca na woluminie. Jeśli na przykład ustawienie zasad wolnego woluminu wynosi 20%, odwołanie zostanie zatrzymane, gdy ilość wolnego miejsca na woluminie osiągnie 20%.  
    * Jeśli skonfigurowano zasady ilości wolnego miejsca i daty, pliki będą przywoływyne do momentu, aż zostanie osiągnięte ustawienie zasad ilości wolnego miejsca lub daty woluminu. Jeśli na przykład ustawienie zasad wolnego woluminu wynosi 20%, a zasady daty to 7 dni, odwołanie zostanie zatrzymane, gdy ilość wolnego miejsca na woluminie osiągnie 20% lub wszystkie pliki, do których uzyskano dostęp lub zmodyfikowano w ciągu 7 dni, będą lokalne.
* `-ThreadCount` Określa, ile plików można odwołać równolegle.
* `-PerFileRetryCount`Określa, jak często będzie podejmowana próba odwołania pliku, który jest obecnie zablokowany.
* `-PerFileRetryDelaySeconds`Określa czas (w sekundach) między ponowieniem próby odwołania i powinien być zawsze używany w połączeniu z poprzednim parametrem.

Przykład:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Jeśli wolumin lokalny hostowany na serwerze nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, polecenie `Invoke-StorageSyncFileRecall` cmdlet zakończy się niepowodzeniem.  

> [!Note]
> Aby odwołać pliki, które zostały warstwowe, przepustowość sieci powinna być co najmniej 1 Mb/s. Jeśli przepustowość sieci jest mniejsza niż 1 Mb/s, pliki mogą nie zostać odwołane z błędem limitu czasu.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące usługi Azure Files](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
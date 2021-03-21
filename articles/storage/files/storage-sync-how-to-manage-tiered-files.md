---
title: Jak zarządzać plikami warstwowymi Azure File Sync | Microsoft Docs
description: Porady i polecenia cmdlet programu PowerShell ułatwiające zarządzanie plikami warstwowymi
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204430"
---
# <a name="how-to-manage-tiered-files"></a>Jak zarządzać plikami warstwowymi

Ten artykuł zawiera wskazówki dla użytkowników z pytaniami związanymi z zarządzaniem plikami warstwowymi. Pytania koncepcyjne dotyczące obsługi warstw w chmurze można znaleźć w temacie [Azure Files często zadawane pytania](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Jak sprawdzić, czy pliki są w warstwach

Niezależnie od tego, czy pliki muszą być warstwowe dla poszczególnych ustawionych zasad, są oceniane co godzinę. Po utworzeniu nowego punktu końcowego serwera można utworzyć dwa sytuacje:

Przy pierwszym dodawaniu nowego punktu końcowego serwera często istnieją pliki znajdujące się w tej lokalizacji serwera. Muszą zostać przekazane przed rozpoczęciem obsługi warstw w chmurze. Zasady wolnego miejsca na woluminie nie rozpoczną pracy do momentu zakończenia wstępnego przekazania wszystkich plików. Jednak opcjonalne zasady dotyczące dat rozpoczną pracę nad pojedynczym plikiem, gdy tylko plik zostanie przekazany. W tym miejscu obowiązuje również interwał jednorazowy. 

Po dodaniu nowego punktu końcowego serwera możliwe jest połączenie pustej lokalizacji serwera z udziałem plików platformy Azure z danymi. Jeśli zdecydujesz się pobrać przestrzeń nazw i odwołać zawartość podczas wstępnego pobierania do serwera, po rozpoczęciu przestrzeni nazw pliki zostaną odwołane w oparciu o ostatnią zmodyfikowaną sygnaturę czasową w ramach zasad wolnego miejsca na woluminie, a także opcjonalne limity zasad daty.

Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwowy w udziale plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku w pliku.**
     Kliknij prawym przyciskiem myszy plik, przejdź do **szczegółów**, a następnie przewiń w dół do właściwości **atrybuty** . Plik warstwowy ma ustawione następujące atrybuty:     
        
        | Litera atrybutu | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że należy utworzyć kopię zapasową pliku przez oprogramowanie kopii zapasowej. Ten atrybut jest zawsze ustawiany, niezależnie od tego, czy plik jest warstwowy czy przechowywany w całości na dysku. |
        | P | Plik rozrzedzony | Wskazuje, że plik jest plikiem rozrzedzonym. Plik rozrzedzony to wyspecjalizowany typ pliku, który oferuje system plików NTFS do wydajnego użycia, gdy plik w strumieniu dysku jest w większości pusty. Azure File Sync używa plików rozrzedzonych, ponieważ plik jest w pełni warstwowy lub częściowo ponownie wywoływany. W przypadku w pełni warstwowego pliku strumień plików jest przechowywany w chmurze. W częściowo odwywoływanym pliku, ta część pliku znajduje się już na dysku. Taka sytuacja może wystąpić, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip. Jeśli plik jest w pełni wywoływany na dysk, Azure File Sync konwertuje go z pliku rozrzedzonego na zwykły plik. Ten atrybut jest ustawiany tylko w systemie Windows Server 2016 i starszych.|
        | M | Odwołaj dostęp do danych | Wskazuje, że dane pliku nie są w pełni obecne w magazynie lokalnym. Odczytanie pliku spowoduje pobranie co najmniej niektórych zawartości pliku z udziału plików platformy Azure, do którego jest podłączony punkt końcowy serwera. Ten atrybut jest ustawiany tylko w systemie Windows Server 2019. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej analizy. Punkt ponownej analizy to specjalny wskaźnik używany przez filtr systemu plików. Azure File Sync używa punktów ponownej analizy do zdefiniowania do Azure File Sync filtr systemu plików (StorageSync.sys) lokalizacji w chmurze, w której jest przechowywany plik. Zapewnia to bezproblemowe dostęp. Użytkownicy nie muszą wiedzieć, że Azure File Sync są używane, lub jak uzyskać dostęp do pliku w udziale plików platformy Azure. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa punkt ponownej analizy z pliku. |
        | O | Tryb offline | Wskazuje, że część lub cała zawartość pliku nie jest przechowywana na dysku. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa ten atrybut. |

        ![Okno dialogowe właściwości dla pliku, z wybraną kartą szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Możesz zobaczyć atrybuty dla wszystkich plików w folderze, dodając pole **atrybuty** do wyświetlania tabeli Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **rozmiar**), wybierz pozycję **więcej**, a następnie z listy rozwijanej wybierz pozycję **atrybuty** .
        
        > [!NOTE]
        > Wszystkie te atrybuty będą widoczne tylko dla częściowo odwywoływanych plików.
        
   * **Służy `fsutil` do sprawdzania punktów ponownej analizy pliku.**
       Zgodnie z opisem w poprzedniej opcji, plik warstwowy zawsze ma zestaw punktów ponownej analizy. Punkt ponownej analizy umożliwia Azure File Sync sterownika filtru systemu plików (StorageSync.sys) do pobierania zawartości z udziałów plików platformy Azure, które nie są przechowywane lokalnie na serwerze. 

       Aby sprawdzić, czy plik ma punkty ponownej analizy, w wierszu polecenia z podwyższonym poziomem uprawnień lub w oknie programu PowerShell uruchom `fsutil` Narzędzie:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Jeśli plik ma punkty ponownej analizy, można oczekiwać, że zostanie wyświetlona **wartość tagu ponownej analizy: 0x8000001E**. Ta wartość szesnastkowa to wartość punktu ponownej analizy, która jest własnością Azure File Sync. Dane wyjściowe zawierają również dane dotyczące ponownej analizy, które reprezentują ścieżkę do pliku w udziale plików platformy Azure.
        
        > [!WARNING]  
        > `fsutil reparsepoint`Polecenie Narzędzia ma także możliwość usunięcia punktu ponownej analizy. Nie wykonuj tego polecenia, chyba że zespół inżynierów Azure File Sync prosi o. Uruchomienie tego polecenia może spowodować utratę danych. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Jak wykluczać aplikacje z poziomu ostatniego dostępu do warstw w chmurze śledzenie

W przypadku agenta Azure File Sync w wersji 11,1 można teraz wykluczyć aplikacje z ostatniego śledzenia dostępu. Gdy aplikacja uzyskuje dostęp do pliku, czas ostatniego dostępu do pliku zostanie zaktualizowany w bazie danych o warstwach w chmurze. Aplikacje skanujące system plików, takie jak oprogramowanie antywirusowe, powodują, że wszystkie pliki mają taki sam czas ostatniego dostępu, który ma wpływ na warstwowe pliki.

Aby wykluczyć aplikacje ze śledzenia czasu ostatniego dostępu, Dodaj nazwę procesu do ustawienia rejestru HeatTrackingProcessNameExclusionList, które znajduje się w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Przykład: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> Deduplikacja danych i procesy Menedżer zasobów serwera plików (Menedżer zasobów) są domyślnie wyłączone. Zmiany na liście wykluczeń procesów są uznawane przez system co 5 minut.

## <a name="how-to-access-the-heat-store"></a>Jak uzyskać dostęp do magazynu ciepła

Obsługa warstw w chmurze polega na ostatnim czasie dostępu i częstotliwości dostępu do pliku, aby określić, które pliki powinny być warstwowe. Sterownik filtru obsługi warstw w chmurze (storagesync.sys) śledzi czas ostatniego dostępu i rejestruje informacje w magazynie ciepła w warstwach w chmurze. Magazyn ciepła można pobrać i zapisać w pliku CSV za pomocą polecenia cmdlet programu PowerShell na serwerze lokalnym.

Istnieje pojedynczy magazyn ciepła dla wszystkich plików na tym samym woluminie. Magazyn ciepła może być bardzo duży. Jeśli konieczne jest tylko pobranie "najchłodnej" liczby elementów, użycie-limit i liczba, a także Rozważ filtrowanie według ścieżki podrzędnej w porównaniu z elementem głównym woluminu.

- Zaimportuj moduł programu PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- WOLNE miejsce na WOLUMINie: Aby uzyskać kolejność, w której pliki będą warstwowo przy użyciu zasad wolnego miejsca na woluminie:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- ZASADY dotyczące daty: Aby uzyskać kolejność, w jakiej pliki będą warstwowo przy użyciu zasad daty:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Znajdź informacje o sklepie dla określonego pliku:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Zobacz wszystkie pliki w kolejności malejącej według czasu ostatniego dostępu:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Zobacz kolejność, w jakiej pliki warstwowe będą ponownie wywoływane przez odwołanie w tle lub odwoływanie na żądanie za pomocą programu PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Jak wymusić warstwowy plik lub katalog

> [!NOTE]
> W przypadku wybrania katalogu, który ma być warstwowy, w warstwach są dostępne tylko pliki znajdujące się obecnie w katalogu. Wszystkie pliki utworzone po tym czasie nie są automatycznie warstwowe.

Po włączeniu funkcji obsługi warstw w chmurze usługa Cloud warstws automatycznie warstwuje pliki na podstawie ostatniego dostępu i modyfikuje czasy, aby osiągnąć procent wolnego miejsca na woluminie określony w punkcie końcowym w chmurze. Czasami może być konieczne ręczne wymuszenie pliku w warstwie. Może to być przydatne, jeśli zapiszesz duży plik, którego nie zamierzasz ponownie używać przez dłuższy czas, i chcesz, aby wolne miejsce na woluminie było teraz używane dla innych plików i folderów. Można wymusić obsługę warstw przy użyciu następujących poleceń programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Jak odwołać plik warstwowy do dysku

Najprostszym sposobem odwoływania pliku na dysk jest otwarcie pliku. Filtr systemu plików Azure File Sync (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez żadnej pracy z Twoją częścią. W przypadku typów plików, które mogą być częściowo odczytywane lub przesyłane strumieniowo, takich jak pliki multimedialne lub zip, otwarcie pliku nie gwarantuje, że cały plik zostanie pobrany.

Aby upewnić się, że plik jest w pełni pobrany na dysk lokalny, musisz użyć programu PowerShell, aby wymusić całkowite oddzwonienie pliku. Ta opcja może być również przydatna, jeśli chcesz odwołać wiele plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell w węźle serwera, na którym zainstalowano Azure File Sync, a następnie uruchom następujące polecenia programu PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parametry opcjonalne:
* `-Order CloudTieringPolicy` najpierw przywróci ostatnio zmodyfikowane lub dostęp do plików, które są dozwolone przez bieżące zasady dotyczące warstw. 
    * Jeśli skonfigurowano zasady wolnego miejsca na woluminie, pliki zostaną odwołane do momentu osiągnięcia ustawienia zasad wolnego miejsca na woluminie. Jeśli na przykład ustawienie zasad wolnego woluminu to 20%, odwołanie zostanie zatrzymane, gdy ilość wolnego miejsca woluminu osiągnie 20%.  
    * W przypadku skonfigurowania ilości wolnego miejsca i zasad dotyczących ilości danych zostaną one odwołane, dopóki nie zostanie osiągnięty limit ilości wolnego miejsca lub ustawienia zasad daty. Jeśli na przykład ustawienie zasad wolnego woluminu to 20%, a zasady dat to 7 dni, funkcja odwoływania zostanie zatrzymana, gdy ilość wolnego miejsca na woluminie osiągnie 20%, a wszystkie pliki, do których dostęp lub zmodyfikowano w ciągu 7 dni, będą lokalne.
* `-ThreadCount` Określa, ile plików można wielokrotnie odwoływać.
* `-PerFileRetryCount`Określa, jak często zostanie podjęta próba odwołania pliku, który jest aktualnie zablokowany.
* `-PerFileRetryDelaySeconds`Określa czas (w sekundach) między ponownymi próbami odwołania i powinna być zawsze używana w połączeniu z poprzednim parametrem.

Przykład:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Jeśli na lokalnym woluminie hostującym serwer nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, `Invoke-StorageSyncFileRecall` polecenie cmdlet kończy się niepowodzeniem.  

> [!Note]
> Aby można było odwołać pliki, które zostały warstwowe, przepustowość sieci powinna wynosić co najmniej 1 MB/s. Jeśli przepustowość sieci jest mniejsza niż 1 MB/s, pliki mogą zakończyć się niepowodzeniem z błędem przekroczenia limitu czasu.

## <a name="next-steps"></a>Następne kroki
* [Często zadawane pytania dotyczące usługi Azure Files](storage-files-faq.md)

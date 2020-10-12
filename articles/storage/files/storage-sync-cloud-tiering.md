---
title: Informacje na temat obsługi warstw w chmurze Azure File Sync | Microsoft Docs
description: Przeczytaj o warstwach chmurowych — opcjonalna funkcja Azure File Sync. Często używane pliki są buforowane lokalnie na serwerze; inne są w warstwach do Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5ca65a428af02eaf5ae6ac461006c720da4461bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538184"
---
# <a name="cloud-tiering-overview"></a>Omówienie obsługi warstw w chmurze
Obsługa warstw w chmurze jest opcjonalną funkcją Azure File Sync, w której często używane pliki są buforowane lokalnie na serwerze, podczas gdy wszystkie inne pliki są warstwami do Azure Files na podstawie ustawień zasad. Gdy plik jest warstwowy, filtr systemu plików Azure File Sync (StorageSync.sys) zastępuje plik lokalnie za pomocą wskaźnika lub punktu ponownej analizy. Punkt ponownej analizy reprezentuje adres URL pliku w Azure Files. Plik warstwowy ma zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony w systemie plików NTFS, aby aplikacje innych firm mogły bezpiecznie identyfikować pliki warstwowe.
 
Gdy użytkownik otwiera plik warstwowy, Azure File Sync bezproblemowo oddzwoni dane pliku od Azure Files bez konieczności znajomości, że plik jest przechowywany na platformie Azure. 
 
 > [!Important]  
 > Obsługa warstw w chmurze nie jest obsługiwana w woluminie systemowym systemu Windows.
    
 > [!Important]  
 > Aby można było odwołać pliki, które zostały warstwowe, przepustowość sieci powinna wynosić co najmniej 1 MB/s. Jeśli przepustowość sieci jest mniejsza niż 1 MB/s, pliki mogą zakończyć się niepowodzeniem z błędem przekroczenia limitu czasu.

## <a name="cloud-tiering-faq"></a>Obsługa warstw w chmurze — często zadawane pytania

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Jak działa Obsługa warstw w chmurze?
Filtr systemu Azure File Sync kompiluje "mapę cieplną" przestrzeni nazw na każdym punkcie końcowym serwera. Monitoruje dostęp (operacje odczytu i zapisu) w miarę upływu czasu, a następnie, w zależności od częstotliwości i recency dostępu, przypisuje wynik ciepła do każdego pliku. Plik, który był ostatnio otwarty, jest uznawany za gorącą, podczas gdy plik, który jest stanowią jedynie ułamek i nie jest dostępny przez jakiś czas, będzie uznawany za chłodną. Gdy wolumin plików na serwerze przekracza ustawiony próg ilości wolnego miejsca na woluminie, zostanie podwyższony poziom największej liczby plików do Azure Files, dopóki nie zostanie osiągnięty procent wolnego miejsca.

Ponadto można określić zasady daty dla każdego punktu końcowego serwera, który będzie warstwować wszystkie pliki, które nie są dostępne w ciągu określonej liczby dni, niezależnie od dostępnego miejsca do magazynowania lokalnego. Jest to dobry wybór, aby proaktywnie zwolnić miejsce na dysku lokalnym, Jeśli wiesz, że pliki w tym punkcie końcowym serwera nie muszą być przechowywane lokalnie poza określonym okresem ważności. Pozwala to zwiększyć cenne pojemności dysku lokalnego dla innych punktów końcowych w tym samym woluminie, aby buforować więcej plików.

Mapę cieplną warstw w chmurze jest zasadniczo uporządkowaną listą wszystkich synchronizowanych plików i znajdują się w lokalizacji, w której włączono obsługę warstw w chmurze. Aby określić względną pozycję pojedynczego pliku w tym mapę cieplną, system używa maksymalnie jednego z następujących sygnatur czasowych, w tej kolejności: MAX (godzina ostatniego dostępu, czas ostatniej modyfikacji, godzina utworzenia). Zazwyczaj czas ostatniego dostępu jest śledzony i dostępny. Jeśli jednak zostanie utworzony nowy punkt końcowy serwera z włączoną obsługą warstw w chmurze, początkowo nie upłynął wystarczająco dużo czasu, aby obserwować dostęp do pliku. W przypadku braku czasu ostatniego dostępu czas ostatniej modyfikacji jest używany do obliczania pozycji względnej w mapę cieplną. Ta sama wartość rezerwowa dotyczy zasad daty. W przypadku braku czasu ostatniego dostępu zasady daty będą działać od czasu ostatniej modyfikacji. Powinna być niedostępna, nastąpi powrót do czasu utworzenia pliku. W miarę upływu czasu system będzie obserwować więcej i więcej żądań dostępu do plików i przestawiać je w celu wykorzystania wstępnie śledzonego czasu ostatniego dostępu.

Obsługa warstw w chmurze nie zależy od funkcji NTFS do śledzenia czasu ostatniego dostępu. Ta funkcja systemu plików NTFS jest domyślnie wyłączona i ze względu na wydajność nie zaleca się ręcznego włączania tej funkcji. Obsługa warstw w chmurze śledzi czas ostatniego dostępu osobno i bardzo wydajnie.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Jaki jest minimalny rozmiar pliku do warstwy?

W przypadku agenta w wersji 9 i nowszych minimalny rozmiar pliku do warstwy jest oparty na rozmiarze klastra systemu plików. Minimalny rozmiar pliku kwalifikującego się do obsługi warstw w chmurze jest obliczany przez 2. rozmiar klastra i co najmniej 8 KB. W poniższej tabeli przedstawiono minimalne rozmiary plików, które mogą być warstwowe, w oparciu o rozmiar klastra objętościowego:

|Rozmiar klastra objętościowego (w bajtach) |Pliki o tym rozmiarze lub większe mogą być warstwowe  |
|----------------------------|---------|
|4 KB lub mniejsze (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536) i większych    | 128 KB  |

W przypadku systemu Windows Server 2019 i agenta Azure File Sync w wersji 12 (przyszła wersja agenta) obsługiwane są również rozmiary klastrów o rozmiarze do 2 MB, a warstwa dla większych rozmiarów klastrów działa w ten sam sposób. Starsze wersje systemów operacyjnych i agentów obsługują rozmiary klastrów nawet do 64 KB, ale nie działają.

Wszystkie systemy plików, które są używane przez system Windows, organizują dysk twardy w oparciu o rozmiar klastra (nazywany także rozmiarem jednostki alokacji). Rozmiar klastra reprezentuje najmniejszą ilość miejsca na dysku, która może być użyta do przechowywania pliku. Gdy rozmiary plików nie są dostępne nawet dla wielu rozmiarów klastra, należy użyć dodatkowego miejsca do przechowywania plików do kolejnej wielokrotności rozmiaru klastra.

Azure File Sync jest obsługiwana na woluminach NTFS w systemie Windows Server 2012 R2 lub nowszym. W poniższej tabeli opisano domyślne rozmiary klastra podczas tworzenia nowego woluminu NTFS. 

|Rozmiar woluminu    |System Windows Server 2012R2 i nowsze |
|---------------|---------------|
|7 MB — 16 TB   | 4 KB          |
|16TB – 32 TB   | 8 KB          |
|32 TB MIEJSCA – 64 TB   | 16 KB         |
|64TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 KB         |
|> 256 TB       | Nieobsługiwane |

Po utworzeniu woluminu można ręcznie sformatować wolumin z innym rozmiarem klastra. Jeśli wolumin jest rdzeniem ze starszej wersji systemu Windows, domyślne rozmiary klastra mogą być różne. [Ten artykuł zawiera więcej informacji na temat domyślnych rozmiarów klastra.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Nawet w przypadku wybrania rozmiaru klastra mniejszego niż 4 KB obowiązuje limit 8 KB jako najmniejszy rozmiar pliku, który może być warstwowy. (Nawet jeśli technicznie rozmiar klastra będzie równy mniej niż 8 KB).

Przyczyna bezwzględnego minimum znajduje się w sposobie, w jaki system NTFS przechowuje bardzo małe pliki — 1 KB do 4 plików z rozmiarem. W zależności od innych parametrów woluminu jest możliwe, że małe pliki nie są przechowywane w klastrze na dysku. Pliki te można przechowywać bezpośrednio w głównej tabeli plików woluminu lub w rekordzie tabeli MFT. Punkt ponownej analizy warstw w chmurze jest zawsze przechowywany na dysku i pobiera dokładnie jeden klaster. Obsługa warstw takich małych plików może się nie potrwać bez oszczędzania miejsca. Ekstremalne przypadki mogą nawet kończyć się większą ilością miejsca z włączoną obsługą warstw w chmurze. Aby zapewnić ochronę przed tym, najmniejszy rozmiar pliku, który będzie warstwą chmury, to 8 KB w rozmiarze 4 KB lub mniejszym.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Jak działają zasady obsługi warstw wolnego miejsca na woluminie?
Wolne miejsce w woluminie to ilość wolnego miejsca, które chcesz zarezerwować na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli ilość wolnego miejsca na woluminie jest ustawiona na 20% na woluminie z jednym punktem końcowym serwera, do 80% miejsca na woluminie zostaną zajęte ostatnio używane pliki, z wszelkimi innymi plikami, które nie mieszczą się w tym miejscu na platformie Azure. Wolne miejsce na woluminie jest stosowane na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub grup synchronizacji. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Jak działają zasady obsługi warstw wolnego miejsca na woluminie w odniesieniu do nowych punktów końcowych serwera?
Gdy punkt końcowy serwera jest nowo zainicjowany i połączony z udziałem plików platformy Azure, serwer najpierw pobierze przestrzeń nazw, a następnie pobierze rzeczywiste pliki do momentu, aż osiągnie próg wolnego miejsca na woluminie. Ten proces jest również znany jako szybkie odzyskiwanie po awarii lub szybkie przywracanie przestrzeni nazw.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Jak jest traktowane wolne miejsce w woluminie, gdy znajduje się w nim wiele punktów końcowych serwera?
Jeśli na woluminie znajduje się więcej niż jeden punkt końcowy serwera, próg ilości wolnego miejsca na woluminie jest największą ilością wolnego miejsca określonego w dowolnym punkcie końcowym serwera. Pliki będą warstwowo zgodne z wzorcem użycia, niezależnie od tego, który punkt końcowy serwera, do którego należą. Na przykład jeśli masz dwa punkty końcowe serwera w woluminie, Endpoint1 i Endpoint2, gdzie Endpoint1 ma próg wolnego miejsca na woluminie równy 25%, a Endpoint2 ma próg wolnego miejsca na woluminie równy 50%, próg wolnego miejsca na woluminie dla obu punktów końcowych serwera będzie 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Jak działają zasady obsługi warstw dat w połączeniu z zasadami obsługi poziomów wolnego miejsca na woluminie? 
Podczas włączania obsługi warstw w chmurze w punkcie końcowym serwera ustawia się zasady wolnego miejsca na woluminie. Zawsze ma pierwszeństwo przed innymi zasadami, w tym zasadami daty. Opcjonalnie można włączyć zasady daty dla każdego punktu końcowego serwera na tym woluminie. Te zasady umożliwiają zarządzanie tylko tymi plikami, do których dostęp (czyli odczyt lub zapis) w zakresie dni, w ramach których te zasady są przechowywane lokalnie. Nie można uzyskać dostępu do plików z określoną liczbą dni, zostanie warstwowa. 

Obsługa warstw w chmurze polega na określeniu, które pliki powinny być warstwowe. Sterownik filtru obsługi warstw w chmurze (storagesync.sys) śledzi czas ostatniego dostępu i rejestruje informacje w magazynie ciepła w warstwach w chmurze. Magazyn ciepła można pobrać i zapisać w pliku CSV za pomocą polecenia cmdlet programu PowerShell na serwerze lokalnym.

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> Sygnatura czasowa ostatniego dostępu nie jest właściwością śledzoną przez system plików NTFS i dlatego nie jest domyślnie widoczna w Eksploratorze plików. Nie należy używać sygnatury czasowej Last-Modified w pliku, aby sprawdzić, czy zasady daty działają zgodnie z oczekiwaniami. Ta sygnatura czasowa śledzi tylko operacje zapisu, nie odczytuje. Użyj podanego polecenia cmdlet, aby uzyskać ostatnią dostępną sygnaturę czasową dla tej oceny.

> [!WARNING]
> Nie włączaj funkcji NTFS do śledzenia ostatniego dostępu do sygnatur czasowych dla plików i folderów. Ta funkcja jest domyślnie wyłączona, ponieważ ma duży wpływ na wydajność. Azure File Sync będzie śledzić czas ostatniego dostępu automatycznie i bardzo wydajnie i nie korzysta z tej funkcji systemu plików NTFS.

Należy pamiętać, że zasady wolnego miejsca na woluminie zawsze mają pierwszeństwo, a w przypadku braku wystarczającej ilości wolnego miejsca na woluminie, aby zachować tyle dni, ile jest w tym samym pliku, jak opisano w zasadach daty, Azure File Sync będzie kontynuować obsługę warstw plików Coldest do momentu spełnienia wartości procentowej ilości wolnego miejsca na woluminie.

Załóżmy na przykład, że masz zasady warstwowe oparte na dacie wynoszące 60 dni i zasady wolnego miejsca na woluminie o wartości 20%. Jeśli po zastosowaniu zasad dotyczących dat jest mniej niż 20% wolnego miejsca na woluminie, zasady wolnego miejsca na woluminie zostaną rozpoczęte i zastąpią zasady daty. Spowoduje to zwiększenie warstw plików, dzięki czemu ilość danych przechowywanych na serwerze może ulec zmniejszeniu z 60 dni danych do 45 dni. Z kolei te zasady wymuszą przedziały warstw plików, które znajdują się poza zakresem czasu, nawet jeśli nie osiągnięto progu wolnego miejsca — dlatego plik o 61 dni starszej wersji będzie warstwowy nawet wtedy, gdy wolumin jest pusty.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Jak mogę określić odpowiednią ilość wolnego miejsca na woluminie?
Ilość danych, które powinny być przechowywane lokalnie, zależy od kilku czynników: przepustowości, wzorca dostępu zestawu danych i budżetu. Jeśli masz połączenie o niskiej przepustowości, możesz chcieć zachować więcej danych lokalnych, aby zapewnić użytkownikom minimalne opóźnienia. W przeciwnym razie można oprzeć ją na współczynniku zmian w danym okresie. Jeśli na przykład wiesz, że około 10% zmian zestawu danych o pojemności 1 TB lub jest aktywnie dostępna w każdym miesiącu, możesz chcieć zachować lokalne 100 GB, aby nie często odwoływać plików. Jeśli wolumin jest 2 TB, należy pozostawić wartość 5% (lub 100 GB) na komputerze lokalnym, co oznacza, że pozostała liczba 95% to ilość wolnego miejsca na woluminie. Zaleca się jednak dodanie buforu do konta w przypadku okresów o większej liczbie zmian — innymi słowy, zaczynając od mniejszej ilości wolnego miejsca na woluminie, a następnie dostosowując go w razie potrzeby później. 

Utrzymywanie większej ilości danych może prowadzić do obniżenia kosztów ruchu wychodzącego, ponieważ mniejsza liczba plików zostanie odłączona od platformy Azure, ale wymagane jest również, aby zachować większą ilość magazynu lokalnego, który ma własny koszt. Po wdrożeniu wystąpienia Azure File Sync można przyjrzeć się wychodzącemu kontu magazynu, aby określić, czy ustawienia wolnego miejsca na woluminie są odpowiednie do użycia. Założenie, że konto magazynu zawiera tylko Azure File Sync punkt końcowy w chmurze (czyli udział synchronizacji), a następnie wysoki ruch wychodzący oznacza, że wiele plików jest wywoływanych z chmury i należy rozważyć zwiększenie lokalnej pamięci podręcznej.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Dodaliśmy nowy punkt końcowy serwera. Jak długo do moich plików w tej warstwie serwera?

Niezależnie od tego, czy pliki muszą być warstwowe dla poszczególnych ustawionych zasad, są oceniane co godzinę. Po utworzeniu nowego punktu końcowego serwera mogą wystąpić dwie sytuacje:

1. Po dodaniu nowego punktu końcowego serwera często istnieją pliki znajdujące się w tej lokalizacji serwera. Przed rozpoczęciem obsługi warstw w chmurze należy je najpierw przekazać. Zasady wolnego miejsca na woluminie nie rozpoczną pracy do momentu zakończenia wstępnego przekazania wszystkich plików. Jednak opcjonalne zasady dotyczące dat rozpoczną pracę nad pojedynczym plikiem, gdy tylko plik zostanie przekazany. W tym miejscu obowiązuje również interwał jednorazowy. 
2. Po dodaniu nowego punktu końcowego serwera możliwe jest połączenie pustej lokalizacji serwera z udziałem plików platformy Azure z danymi. Czy jest to dla drugiego serwera, czy podczas odzyskiwania po awarii. Jeśli zdecydujesz się pobrać przestrzeń nazw i odwołać zawartość podczas wstępnego pobierania na serwer, po rozpoczęciu przestrzeni nazw pliki zostaną odwołane na podstawie ostatniej zmodyfikowanej sygnatury czasowej. Tylko tyle plików będzie można odwołać w ramach zasad wolnego miejsca na woluminie i opcjonalnych zasad dotyczących daty.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Jak mogę sprawdzić, czy plik został warstwowy?
Istnieje kilka sposobów, aby sprawdzić, czy plik został warstwowy w udziale plików platformy Azure:
    
   *  **Sprawdź atrybuty pliku w pliku.**
     Kliknij prawym przyciskiem myszy plik, przejdź do **szczegółów**, a następnie przewiń w dół do właściwości **atrybuty** . Plik warstwowy ma ustawione następujące atrybuty:     
        
        | Litera atrybutu | Atrybut | Definicja |
        |:----------------:|-----------|------------|
        | A | Archiwum | Wskazuje, że należy utworzyć kopię zapasową pliku przez oprogramowanie kopii zapasowej. Ten atrybut jest zawsze ustawiany, niezależnie od tego, czy plik jest warstwowy czy przechowywany w całości na dysku. |
        | P | Plik rozrzedzony | Wskazuje, że plik jest plikiem rozrzedzonym. Plik rozrzedzony to wyspecjalizowany typ pliku, który oferuje system plików NTFS do wydajnego użycia, gdy plik w strumieniu dysku jest w większości pusty. Azure File Sync używa plików rozrzedzonych, ponieważ plik jest w pełni warstwowy lub częściowo ponownie wywoływany. W przypadku w pełni warstwowego pliku strumień plików jest przechowywany w chmurze. W częściowo odwywoływanym pliku, ta część pliku znajduje się już na dysku. Jeśli plik jest w pełni wywoływany na dysk, Azure File Sync konwertuje go z pliku rozrzedzonego na zwykły plik. Ten atrybut jest ustawiany tylko w systemie Windows Server 2016 i starszych.|
        | M | Odwołaj dostęp do danych | Wskazuje, że dane pliku nie są w pełni obecne w magazynie lokalnym. Odczytanie pliku spowoduje pobranie co najmniej niektórych zawartości pliku z udziału plików platformy Azure, do którego jest podłączony punkt końcowy serwera. Ten atrybut jest ustawiany tylko w systemie Windows Server 2019. |
        | L | Punkt ponownej analizy | Wskazuje, że plik ma punkt ponownej analizy. Punkt ponownej analizy to specjalny wskaźnik używany przez filtr systemu plików. Azure File Sync używa punktów ponownej analizy do zdefiniowania do Azure File Sync filtr systemu plików (StorageSync.sys) lokalizacji w chmurze, w której jest przechowywany plik. Zapewnia to bezproblemowe dostęp. Użytkownicy nie muszą wiedzieć, że Azure File Sync są używane, lub jak uzyskać dostęp do pliku w udziale plików platformy Azure. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa punkt ponownej analizy z pliku. |
        | O | Tryb offline | Wskazuje, że część lub cała zawartość pliku nie jest przechowywana na dysku. Gdy plik jest w pełni wywoływany, Azure File Sync usuwa ten atrybut. |

        ![Okno dialogowe właściwości dla pliku, z wybraną kartą szczegóły](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Możesz zobaczyć atrybuty dla wszystkich plików w folderze, dodając pole **atrybuty** do wyświetlania tabeli Eksploratora plików. Aby to zrobić, kliknij prawym przyciskiem myszy istniejącą kolumnę (na przykład **rozmiar**), wybierz pozycję **więcej**, a następnie z listy rozwijanej wybierz pozycję **atrybuty** .
        
   * **Służy `fsutil` do sprawdzania punktów ponownej analizy pliku.**
       Zgodnie z opisem w poprzedniej opcji, plik warstwowy zawsze ma zestaw punktów ponownej analizy. Wskaźnik ponownej analizy jest specjalnym wskaźnikiem filtru systemu plików Azure File Sync (StorageSync.sys). Aby sprawdzić, czy plik ma punkty ponownej analizy, w wierszu polecenia z podwyższonym poziomem uprawnień lub w oknie programu PowerShell uruchom `fsutil` Narzędzie:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Jeśli plik ma punkty ponownej analizy, można oczekiwać, że zostanie wyświetlona **wartość tagu ponownej analizy: 0x8000001E**. Ta wartość szesnastkowa to wartość punktu ponownej analizy, która jest własnością Azure File Sync. Dane wyjściowe zawierają również dane dotyczące ponownej analizy, które reprezentują ścieżkę do pliku w udziale plików platformy Azure.

        > [!WARNING]  
        > `fsutil reparsepoint`Polecenie Narzędzia ma także możliwość usunięcia punktu ponownej analizy. Nie wykonuj tego polecenia, chyba że zespół inżynierów Azure File Sync prosi o. Uruchomienie tego polecenia może spowodować utratę danych. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Plik, którego chcę użyć, został warstwowy. Jak można przywołać plik na dysk, aby użyć go lokalnie?
Najprostszym sposobem odwoływania pliku na dysk jest otwarcie pliku. Filtr systemu plików Azure File Sync (StorageSync.sys) bezproblemowo pobiera plik z udziału plików platformy Azure bez żadnej pracy z Twoją częścią. W przypadku typów plików, które mogą być częściowo odczytywane z, takich jak pliki multimedialne lub zip, otwarcie pliku nie powoduje pobrania całego pliku.

Można też użyć programu PowerShell, aby wymusić oddzwonienie pliku. Ta opcja może być przydatna, jeśli chcesz odwołać wiele plików jednocześnie, takich jak wszystkie pliki w folderze. Otwórz sesję programu PowerShell w węźle serwera, na którym zainstalowano Azure File Sync, a następnie uruchom następujące polecenia programu PowerShell:
    
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
> - Przy dodawaniu nowego punktu końcowego serwera do istniejącej grupy synchronizacji można także użyć polecenia cmdlet Invoke-StorageSyncFileRecall.  
>- Jeśli na lokalnym woluminie hostującym serwer nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, `Invoke-StorageSyncFileRecall` polecenie cmdlet kończy się niepowodzeniem.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Dlaczego rozmiar właściwości *dysku* dla pliku jest zgodny z właściwością *size* po użyciu Azure File Sync? 
Eksplorator plików systemu Windows udostępnia dwie właściwości, aby reprezentować rozmiar pliku: **rozmiar** i **rozmiar na dysku**. Te właściwości różnią się w znaczenie. **Rozmiar** reprezentuje całkowity rozmiar pliku. **Rozmiar na dysku** reprezentuje rozmiar strumienia pliku przechowywanego na dysku. Wartości tych właściwości mogą się różnić z różnych powodów, takich jak kompresja, korzystanie z deduplikacji danych lub Obsługa warstw w chmurze przy użyciu Azure File Sync. Jeśli plik jest warstwą udziału plików platformy Azure, rozmiar na dysku wynosi zero, ponieważ strumień plików jest przechowywany w udziale plików platformy Azure, a nie na dysku. Istnieje również możliwość, że plik jest częściowo warstwowy (lub częściowo odwołujący). W pliku częściowo warstwowego część pliku znajduje się na dysku. Taka sytuacja może wystąpić, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Jak mogę wymusić przeprowadzenie warstwowego pliku lub katalogu?

> [!NOTE]
> W przypadku wybrania katalogu, który ma być warstwowy, w warstwach są dostępne tylko pliki znajdujące się obecnie w katalogu. Wszystkie pliki utworzone po tym czasie nie są automatycznie warstwowe.

Po włączeniu funkcji obsługi warstw w chmurze usługa Cloud warstws automatycznie warstwuje pliki na podstawie ostatniego dostępu i modyfikuje czasy, aby osiągnąć procent wolnego miejsca na woluminie określony w punkcie końcowym w chmurze. Czasami może być konieczne ręczne wymuszenie pliku w warstwie. Może to być przydatne, jeśli zapiszesz duży plik, którego nie zamierzasz ponownie używać przez dłuższy czas, i chcesz, aby wolne miejsce na woluminie było teraz używane dla innych plików i folderów. Można wymusić obsługę warstw przy użyciu następujących poleceń programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Dlaczego moje pliki warstwowe nie pokazują miniatur lub podglądów w Eksploratorze Windows?
W przypadku plików warstwowych miniatury i podglądy nie będą widoczne w punkcie końcowym serwera. To zachowanie jest oczekiwane, ponieważ funkcja pamięci podręcznej miniatur w systemie Windows celowo pomija odczytywanie plików z atrybutem offline. Po włączeniu obsługi warstw w chmurze odczytywanie za pomocą plików warstwowych spowodowałoby ich pobranie (są one wywoływane).

To zachowanie nie jest specyficzne dla Azure File Sync, Eksplorator Windows wyświetla "szary X" dla wszystkich plików, które mają ustawiony atrybut offline. Podczas uzyskiwania dostępu do plików za pośrednictwem protokołu SMB zobaczysz ikonę X. Szczegółowe wyjaśnienie tego zachowania można znaleźć w [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Mam wyłączone warstwy chmury, dlaczego istnieją pliki warstwowe w lokalizacji punktu końcowego serwera?

Istnieją dwa powody, dla których w lokalizacji punktu końcowego serwera mogą znajdować się pliki warstwowe:

- Podczas dodawania nowego punktu końcowego serwera do istniejącej grupy synchronizacji metadane są najpierw synchronizowane z serwerem, a następnie pobierane do serwera w tle. Pliki będą wyświetlane jako warstwowe, dopóki nie zostaną pobrane lokalnie. Aby zwiększyć wydajność pobierania plików podczas dodawania nowego serwera do grupy synchronizacji, należy użyć polecenia cmdlet [Invoke-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) .

- Jeśli Obsługa warstw w chmurze została włączona w punkcie końcowym serwera, a następnie wyłączona, pliki pozostaną warstwą do momentu uzyskania do nich dostępu.


## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)

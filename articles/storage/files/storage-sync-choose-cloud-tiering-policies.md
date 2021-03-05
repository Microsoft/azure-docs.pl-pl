---
title: Wybierz Azure File Sync zasad obsługi warstw w chmurze | Microsoft Docs
description: Szczegółowe informacje o tym, co należy wziąć pod uwagę podczas wybierania Azure File Sync zasad obsługi warstw w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2ed1b8162c49ccc26cb98dd02897a9c40f809d14
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204478"
---
# <a name="choose-cloud-tiering-policies"></a>Wybieranie zasad obsługi warstw w chmurze

Ten artykuł zawiera wskazówki dla użytkowników, którzy wybierają i dostosowują zasady obsługi warstw w chmurze. Przed przeczytaniem z tego artykułu upewnij się, że rozumiesz, jak działa Obsługa warstw w chmurze. Podstawowe informacje na temat obsługi warstw w chmurze znajdują się w temacie [Omówienie obsługi warstw w chmurze Azure File Sync](storage-sync-cloud-tiering-overview.md). Aby uzyskać szczegółowe omówienie zasad obsługi warstw w chmurze z przykładami, zobacz [Azure File Sync zasad obsługi warstw w chmurze](storage-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Ograniczenia
- Obsługa warstw w chmurze nie jest obsługiwana w woluminie systemowym systemu Windows.

- W przypadku przydziału Menedżera zasobów na poziomie woluminu nadal można włączyć obsługę warstw w chmurze. Gdy limit przydziału Menedżera zasobów serwera plików jest ustawiony, interfejsy API zapytania wolnego miejsca, które są wywoływane, automatycznie zgłaszają wolne miejsce na woluminie zgodnie z ustawieniem limitu przydziału. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minimalny rozmiar pliku do warstwy

W przypadku agenta w wersji 9 i nowszych minimalny rozmiar pliku do warstwy jest oparty na rozmiarze klastra systemu plików. Minimalny rozmiar pliku kwalifikującego się do obsługi warstw w chmurze jest obliczany przez 2. rozmiar klastra i co najmniej 8 KB. W poniższej tabeli przedstawiono minimalne rozmiary plików, które mogą być warstwowe, w oparciu o rozmiar klastra objętościowego:

|Rozmiar klastra objętościowego (w bajtach) |Pliki o tym rozmiarze lub większe mogą być warstwowe  |
|----------------------------|---------|
|4 KB lub mniejsze (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |

Rozmiary klastrów o rozmiarze do 64 KB są obecnie obsługiwane, ale w przypadku większych rozmiarów Obsługa warstw w chmurze nie działa.

Wszystkie systemy plików, które są używane przez system Windows, organizują dysk twardy w oparciu o rozmiar klastra (nazywany także rozmiarem jednostki alokacji). Rozmiar klastra reprezentuje najmniejszą ilość miejsca na dysku, która może być użyta do przechowywania pliku. Gdy rozmiary plików nie są dostępne nawet dla wielu rozmiarów klastra, należy użyć dodatkowego miejsca do przechowywania plików do kolejnej wielokrotności rozmiaru klastra.

Azure File Sync jest obsługiwana na woluminach NTFS w systemie Windows Server 2012 R2 lub nowszym. W poniższej tabeli opisano domyślne rozmiary klastra podczas tworzenia nowego woluminu NTFS przy użyciu systemu Windows Server 2019.

|Rozmiar woluminu    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB — 16 TB   | 4 KB                |
|16TB – 32 TB   | 8 KB                |
|32 TB MIEJSCA – 64 TB   | 16 KB               |
|64TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (starsza wersja) |
|256 TB – 512 TB| 128 KB              |
|512 TB — 1 GB  | 256 KB              |
|1 PB — 2 PB    | 512 KB              |
|2 TB — 4 GB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (maksymalny rozmiar)  |
|> 8 TB         | nieobsługiwane       |

Po utworzeniu woluminu można ręcznie sformatować wolumin z innym rozmiarem klastra. Jeśli wolumin jest rdzeniem ze starszej wersji systemu Windows, domyślne rozmiary klastra mogą być różne. [Ten artykuł zawiera więcej informacji na temat domyślnych rozmiarów klastra.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Nawet w przypadku wybrania rozmiaru klastra mniejszego niż 4 KB obowiązuje limit 8 KB jako najmniejszy rozmiar pliku, który może być warstwowy. (Nawet jeśli technicznie rozmiar klastra będzie równy mniej niż 8 KB).

Przyczyna bezwzględnego minimum znajduje się w sposobie, w jaki system NTFS przechowuje bardzo małe pliki — 1 KB do 4 plików z rozmiarem. W zależności od innych parametrów woluminu jest możliwe, że małe pliki nie są przechowywane w klastrze na dysku. Pliki te można przechowywać bezpośrednio w głównej tabeli plików woluminu lub w rekordzie tabeli MFT. Punkt ponownej analizy warstw w chmurze jest zawsze przechowywany na dysku i pobiera dokładnie jeden klaster. Obsługa warstw takich małych plików może się nie potrwać bez oszczędzania miejsca. Ekstremalne przypadki mogą nawet kończyć się większą ilością miejsca z włączoną obsługą warstw w chmurze. Aby zapewnić ochronę przed tym, najmniejszy rozmiar pliku, który będzie warstwą chmury, to 8 KB w rozmiarze 4 KB lub mniejszym. 

## <a name="selecting-your-initial-policies"></a>Wybieranie zasad początkowych

Ogólnie rzecz biorąc, po włączeniu obsługi warstw w chmurze w punkcie końcowym serwera należy utworzyć jeden lokalny dysk wirtualny dla każdego pojedynczego punktu końcowego serwera. Izolowanie punktu końcowego serwera ułatwia zrozumienie, jak działa Obsługa warstw w chmurze i odpowiednio dostosowuje zasady. Jednak Azure File Sync działa nawet w przypadku wielu punktów końcowych serwera na tym samym dysku, aby uzyskać szczegółowe informacje, zobacz sekcję [punkty końcowe wielu serwerów w woluminie lokalnym](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) . Zalecamy również, aby podczas pierwszego włączania obsługi warstw w chmurze zachować zasady dotyczące dat wyłączony i zasady wolnego miejsca na poziomie około 10% do 20%. W przypadku większości woluminów serwera plików 20% wolnego miejsca jest zwykle najlepszą opcją.

Dla uproszczenia i aby jasno zrozumieć, jak elementy będą warstwowe, zalecamy głównie dostosowanie zasad wolnego miejsca na woluminie i pozostawienie zasad daty wyłączenia, chyba że będzie to konieczne. Zalecamy to, ponieważ większość klientów może znaleźć cenną pamięć podręczną z możliwie jak największą ilością aktywnych plików i warstwą reszty w chmurze. Jednak zasady daty mogą być przydatne, jeśli chcesz proaktywnie zwolnić miejsce na dysku lokalnym i wiadomo, że pliki w tym punkcie końcowym serwera są dostępne po upływie liczby dni określonej w zasadach dotyczących dat, nie musisz być przechowywane lokalnie. Ustawienie zasad daty zwalnia cenne pojemności dysku lokalnego dla innych punktów końcowych na tym samym woluminie w celu przetworzenia pamięci podręcznej więcej plików.

Po ustawieniu zasad Monitoruj ruch wychodzący i odpowiednio Dostosuj obie zasady. Zalecamy, aby przede wszystkim sprawdzać **rozmiar odwołania do warstw w chmurze** i **rozmiar przywoływany w warstwach chmury według metryk aplikacji** w Azure monitor. Aby dowiedzieć się, jak monitorować ruch wychodzący, zobacz [monitorowanie warstw chmury](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Dostosowywanie zasad

Jeśli ilość plików, które są stale ponownie wywoływane z platformy Azure, jest większa niż chcesz, możesz mieć więcej aktywnych plików niż miejsce na dysku lokalnym. Zwiększ rozmiar woluminu lokalnego, jeśli jest to możliwe, i/lub zmniejsz wartość procentową zasad wolnego miejsca na woluminie w małych przyrostach. Zmniejszenie procentu ilości wolnego miejsca na woluminie zbyt duże może również mieć negatywne konsekwencje. Większe zmiany w zestawie danych wymagają większej ilości wolnego miejsca — dla nowych plików i odwoływania plików "zimnych". Obsługa warstw zaczyna się z opóźnieniem do godziny, a następnie wymaga czasu przetwarzania, dlatego należy zawsze mieć wystarczającą ilość wolnego miejsca na woluminie.

Utrzymywanie większej ilości danych oznacza obniżenie kosztów ruchu wychodzącego z platformy Azure, a także wymaga większej ilości magazynu lokalnego, który będzie miał własny koszt. 

Podczas dostosowywania zasad wolnego miejsca na woluminie ilość danych, które powinny być przechowywane lokalnie, zależy od następujących czynników: przepustowości, wzorca dostępu zestawu danych i budżetu. Dzięki połączeniu z niską przepustowością możesz chcieć uzyskać więcej danych lokalnych, aby zapewnić użytkownikom minimalne opóźnienia. W przeciwnym razie można oprzeć ją na współczynniku zmian w danym okresie. Przykładowo, Jeśli wiesz, że 10% zestawu danych o pojemności 1 TB lub jest aktywnie do niego dostęp w każdym miesiącu, możesz chcieć zachować lokalne 100 GB, aby nie były często odwołujące plików. Jeśli wolumin jest 2 TB, warto zachować wartość 5% (lub 100 GB) na komputerze lokalnym, co oznacza, że pozostała liczba 95% to ilość wolnego miejsca na woluminie. Należy jednak dodać bufor dla okresów o większym obciążeniu — innymi słowy, rozpoczynając od większej ilości wolnego miejsca na woluminie, a następnie dostosowując go w razie potrzeby później.

## <a name="standard-operating-procedures"></a>Standardowe procedury operacyjne

- Podczas pierwszej migracji do Azure Files za pośrednictwem Azure File Sync Usługa Cloud warstw jest zależna od początkowego przekazania
- Obsługa warstw w chmurze sprawdza zgodność z ilością wolnego miejsca na woluminie i zasadami dotyczącymi dat co 60 minut
- Użycie przełącznika/LFSM na Robocopy podczas migrowania plików umożliwi synchronizowanie plików i warstw w chmurze w celu przetworzenia miejsca podczas początkowego przekazywania 
- Jeśli Obsługa warstw występuje przed utworzeniem mapę cieplną, pliki będą warstwą według sygnatury czasowej ostatniej modyfikacji

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)

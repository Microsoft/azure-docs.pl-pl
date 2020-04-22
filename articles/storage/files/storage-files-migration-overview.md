---
title: Migracja do udziałów plików usługi Azure File
description: Dowiedz się więcej o migracjach do udziałów plików platformy Azure i znajdź przewodnik po migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685987"
---
# <a name="migrate-to-azure-file-shares"></a>Migracja do udziałów plików usługi Azure File

W tym artykule opisano podstawowe aspekty migracji do udziałów plików platformy Azure.

Ten artykuł zawiera podstawowe informacje o migracji i tabelę przewodników migracji. Te przewodniki ułatwiają przenoszenie plików do udziałów plików platformy Azure. Przewodniki są zorganizowane na podstawie tego, gdzie znajdują się dane i jaki model wdrażania (tylko w chmurze lub hybrydowy) przenosisz.

## <a name="migration-basics"></a>Podstawy migracji

Platforma Azure ma wiele dostępnych typów magazynu w chmurze. Podstawowym aspektem migracji plików na platformę Azure jest określenie, która opcja magazynu platformy Azure jest odpowiednia dla twoich danych.

[Udziały plików platformy Azure](storage-files-introduction.md) są odpowiednie dla danych plików ogólnego przeznaczenia. Te dane obejmują wszystko, do czego używasz lokalnego udziału SMB lub NFS. Za [pomocą usługi Azure File Sync](storage-sync-files-planning.md)można buforować zawartość kilku udziałów plików platformy Azure na serwerach z systemem Windows Server lokalnie.

W przypadku aplikacji, która obecnie działa na serwerze lokalnym, przechowywanie plików w udziale plików platformy Azure może być dobrym wyborem. Możesz przenieść aplikację na platformę Azure i używać udziałów plików platformy Azure jako udostępnionego magazynu. Można również wziąć pod uwagę [dyski azure](../../virtual-machines/windows/managed-disks-overview.md) dla tego scenariusza.

Niektóre aplikacje w chmurze nie zależą od SMB ani dostępu do danych lokalnych komputera lub udostępnionego dostępu. Dla tych aplikacji magazynu obiektów, takich jak [obiekty blob platformy Azure](../blobs/storage-blobs-overview.md) jest często najlepszym wyborem.

Kluczem w każdej migracji jest przechwycenie całej odpowiedniej wierności plików podczas przenoszenia plików z ich bieżącej lokalizacji magazynu na platformę Azure. Ile wierności obsługuje opcja magazynu platformy Azure i ile wymaga scenariusza, pomaga również wybrać odpowiedni magazyn platformy Azure. Dane plików ogólnego przeznaczenia tradycyjnie zależy od metadanych pliku. Dane aplikacji mogą nie.

Oto dwa podstawowe składniki pliku:

- **Strumień danych:** Strumień danych pliku przechowuje zawartość pliku.
- **Metadane pliku**: Metadane pliku mają następujące podskładnie:
   * Atrybuty plików, takie jak tylko do odczytu
   * Uprawnienia do plików, które mogą być określane jako *uprawnienia NTFS* lub *listy ACL plików i folderów*
   * Znaczniki czasu, w szczególności tworzenie i ostatnio zmodyfikowane znaczniki czasu
   * Alternatywny strumień danych, który jest miejscem do przechowywania większych ilości niestandardowych właściwości

Wierność plików w migracji można zdefiniować jako możliwość:

- Przechowuj wszystkie odpowiednie informacje o pliku w źródle.
- Przesyłaj pliki za pomocą narzędzia do migracji.
- Przechowuj pliki w docelowym magazynie migracji.

Aby zapewnić płynne przebiegi migracji, zidentyfikuj [najlepsze narzędzie do kopiowania do swoich potrzeb](#migration-toolbox) i dopasuj cel pamięci masowej do źródła.

Biorąc pod uwagę poprzednie informacje, można zobaczyć, że magazyn docelowy dla plików ogólnego przeznaczenia na platformie Azure jest [udziały plików platformy Azure.](storage-files-introduction.md)

W przeciwieństwie do magazynu obiektów w obiektach blob platformy Azure udział plików platformy Azure może natywnie przechowywać metadane plików. Udziały plików platformy Azure zachowują również hierarchię plików i folderów, atrybuty i uprawnienia. Uprawnienia NTFS mogą być przechowywane w plikach i folderach, ponieważ są one lokalne.

Użytkownik usługi Active Directory, która jest ich lokalnym kontrolerem domeny, może natywnie uzyskiwać dostęp do udziału plików platformy Azure. Tak może być użytkownik usług domenowych Active Directory azure (Usługi Azure AD DS). Każdy używa swojej bieżącej tożsamości, aby uzyskać dostęp na podstawie uprawnień udziału oraz list ACL plików i folderów. To zachowanie jest podobne do użytkownika łączącego się z lokalnym udziałem plików.

Alternatywny strumień danych jest podstawowym aspektem wierności plików, który obecnie nie może być przechowywany w pliku w udziale plików platformy Azure. Jest zachowywany lokalnie, gdy używana jest usługa Azure File Sync.

Dowiedz się więcej o [uwierzytelnianiu usługi Azure AD](storage-files-identity-auth-active-directory-enable.md) i [uwierzytelnianiu usług Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) dla udziałów plików platformy Azure.

## <a name="migration-guides"></a>Przewodniki po migracji

W poniższej tabeli wymieniono szczegółowe przewodniki dotyczące migracji.

Jak korzystać z tabeli:

1. Znajdź wiersz systemu źródłowego, na który są aktualnie przechowywane pliki.

1. Wybierz jeden z następujących celów:

   - Wdrożenie hybrydowe przy użyciu usługi Azure File Sync do buforowania zawartości udziałów plików platformy Azure w środowisku lokalnym
   - Udziały plików platformy Azure w chmurze

   Wybierz kolumnę docelową, która odpowiada Twojemu wyborowi.

1. W obrębie przecięcia źródła i obiektu docelowego komórka tabeli wyświetla dostępne scenariusze migracji. Wybierz jeden, który ma być bezpośrednio połączony ze szczegółowym przewodnikiem migracji.

Scenariusz bez łącza nie ma jeszcze opublikowanego przewodnika po migracji. Od czasu do czasu sprawdzaj tę tabelę pod kątem aktualizacji. Nowe przewodniki zostaną opublikowane, gdy będą dostępne.

| Element źródłowy | Cel: </br>Wdrożenie hybrydowe | Cel: </br>Wdrażanie tylko w chmurze |
|:---|:--|:--|
| | Kombinacja narzędzi:| Kombinacja narzędzi: |
| Windows Server 2012 R2 i nowsze | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Usługa Azure File Sync i azure data box](storage-sync-offline-data-transfer.md)</li><li>Usługa synchronizacji plików i migracji plików platformy Azure</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync and Data Box</li><li>Usługa synchronizacji plików i migracji plików platformy Azure</li><li>Robocopy</li></ul> |
| Windows Server 2012 i starsze | <ul><li>Azure File Sync and Data Box</li><li>Usługa synchronizacji plików i migracji plików platformy Azure</li></ul> | <ul><li>Usługa synchronizacji plików i migracji plików platformy Azure</li><li>Robocopy</li></ul> |
| Pamięć masowa podłączana w sieci (NAS) | <ul><li>[Synchronizacja plików platformy Azure i RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux lub Samba | <ul><li>[Synchronizacja plików platformy Azure i RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 lub StorSimple Cloud Appliance 8600 | <ul><li>[Usługa Azure File Sync i StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| Urządzenie w chmurze StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Przybornik migracji

### <a name="file-copy-tools"></a>Narzędzia do kopiowania plików

Istnieje kilka narzędzi do kopiowania plików dostępnych od firmy Microsoft i innych. Aby wybrać odpowiednie narzędzie dla scenariusza migracji, należy wziąć pod uwagę następujące podstawowe pytania:

* Czy narzędzie obsługuje lokalizacje źródłowe i docelowe dla kopii pliku?

* Czy narzędzie obsługuje ścieżkę sieciową lub dostępne protokoły (takie jak REST, SMB lub NFS) między lokalizacjami magazynu źródłowego i docelowego?

* Czy narzędzie zachowuje niezbędną wierność plików obsługiwana przez lokalizacje źródłowe i docelowe?

    W niektórych przypadkach magazyn docelowy nie obsługuje takiej samej wierności jak źródło. Jeśli magazyn docelowy jest wystarczający do twoich potrzeb, narzędzie musi być zgodne tylko z możliwościami wierności plików obiektu docelowego.

* Czy narzędzie ma funkcje, które pozwalają mu dopasować się do strategii migracji?

    Rozważmy na przykład, czy narzędzie umożliwia zminimalizowanie przestojów.
    
    Gdy narzędzie obsługuje opcję dublowania źródła do obiektu docelowego, często można uruchomić go wiele razy na tym samym źródle i miejscu docelowym, gdy źródło pozostaje dostępne.

    Przy pierwszym uruchomieniu narzędzia kopiuje większość danych. Ten początkowy przebieg może trwać jakiś czas. Często trwa dłużej niż chcesz, aby przełączyć źródło danych w tryb offline dla procesów biznesowych.

    Dublowanie źródła do obiektu docelowego (tak jak **w robocopy /MIR),** można ponownie uruchomić narzędzie na tym samym źródle i miejscu docelowym. Uruchomienie jest znacznie szybsze, ponieważ musi transportować tylko zmiany źródłowe, które występują po poprzednim uruchomieniu. Ponowne uruchomienie narzędzia do kopiowania w ten sposób może znacznie skrócić czas przestoju.

W poniższej tabeli klasyfikuje się narzędzia firmy Microsoft i ich bieżącą przydatność do udziału plików platformy Azure:

| Zalecane | Narzędzie | Obsługa udziałów plików platformy Azure | Zachowanie wierności plików |
| :-: | :-- | :---- | :---- |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Obsługiwane. Udziały plików platformy Azure mogą być montowane jako dyski sieciowe. | Pełna wierność.* |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natywnie zintegrowane z udziałami plików platformy Azure. | Pełna wierność.* |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Usługa migracji magazynu | Pośrednio obsługiwane. Udziały plików platformy Azure mogą być montowane jako dyski sieciowe na serwerach docelowych programu SMS. | Pełna wierność.* |
|![Nie w pełni zalecane](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Obsługiwane. | Nie kopiuje metadanych. [Data Box może być używany z synchronizacją plików azure](storage-sync-offline-data-transfer.md). |
|![Niezalecane](media/storage-files-migration-overview/circle-red-x.png)| Narzędzie AzCopy | Obsługiwane. | Nie kopiuje metadanych. |
|![Niezalecane](media/storage-files-migration-overview/circle-red-x.png)| Eksplorator usługi Azure Storage | Obsługiwane. | Nie kopiuje metadanych. |
|![Niezalecane](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Obsługiwane. | Nie kopiuje metadanych. |
|||||

*\*Pełna wierność: spełnia lub przekracza możliwości udostępniania plików platformy Azure.*

### <a name="migration-helper-tools"></a>Narzędzia pomocnicze migracji

W tej sekcji opisano narzędzia ułatwiające planowanie i uruchamianie migracji.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy z firmy Microsoft Corporation

RoboCopy jest jednym z narzędzi najbardziej stosowanych do migracji plików. Jest to część systemu Windows. Główną [dokumentację RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) jest pomocnym zasobem dla wielu opcji tego narzędzia.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize od JAM Software GmbH

Usługa Azure File Sync skaluje się głównie z liczbą elementów (plików i folderów), a nie z całkowitą ilością magazynu. Narzędzie TreeSize umożliwia określenie liczby elementów na woluminach systemu Windows Server.

Za pomocą tego narzędzia można utworzyć perspektywę przed [wdrożeniem usługi Azure File Sync](storage-sync-files-deployment-guide.md). Można go również użyć, gdy warstwa w chmurze jest zaangażowana po wdrożeniu. W tym scenariuszu jest widoczna liczba elementów i katalogi, które używają pamięci podręcznej serwera najbardziej.

Testowana wersja narzędzia jest w wersji 4.4.1. Jest kompatybilny z plikami warstwowymi w chmurze. Narzędzie nie spowoduje wycofywania plików warstwowych podczas normalnego działania.

## <a name="next-steps"></a>Następne kroki

1. Utwórz plan, dla którego wdrożenia udziałów plików platformy Azure (tylko w chmurze lub hybrydowe) chcesz.
1. Przejrzyj listę dostępnych przewodników migracji, aby znaleźć szczegółowy przewodnik, który pasuje do źródła i wdrożenia udziałów plików platformy Azure.

Oto więcej informacji na temat technologii usługi Azure Files wymienionych w tym artykule:

* [Omówienie udziału plików platformy Azure](storage-files-introduction.md)
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
* [Synchronizacja plików platformy Azure: warstwa w chmurze](storage-sync-cloud-tiering.md)

---
title: Migracja do udziałów plików usługi Azure File
description: Dowiedz się więcej na temat migracji do udziałów plików platformy Azure i znajdź przewodnik migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785221"
---
# <a name="migrate-to-azure-file-shares"></a>Migracja do udziałów plików usługi Azure File

Ten artykuł zawiera podstawowe aspekty migracji do udziałów plików platformy Azure.

Ten artykuł zawiera podstawowe informacje dotyczące migracji i tabelę przewodników migracji. Te przewodniki ułatwiają przenoszenie plików do udziałów plików platformy Azure. Przewodniki są zorganizowane w oparciu o miejsce danych i model wdrażania (tylko w chmurze lub hybrydowy), do którego przenosisz się.

## <a name="migration-basics"></a>Podstawy migracji

Platforma Azure oferuje wiele dostępnych typów magazynu w chmurze. Podstawowym aspektem migracji plików na platformę Azure jest określenie, która opcja usługi Azure Storage jest właściwa dla Twoich danych.

[Udziały plików platformy Azure](storage-files-introduction.md) są odpowiednie dla danych plików ogólnego przeznaczenia. Te dane obejmują wszystkie elementy, dla których używasz lokalnego udziału SMB lub NFS. Za [Azure File Sync](../file-sync/file-sync-planning.md)można buforować zawartość kilku udziałów plików platformy Azure na serwerach z lokalnym systemem Windows Server.

W przypadku aplikacji, która obecnie działa na serwerze lokalnym, dobrym wyborem może być przechowywanie plików w udziałach plików platformy Azure. Aplikację można przenieść na platformę Azure i używać udziałów plików platformy Azure jako magazynu udostępnionego. W tym scenariuszu można również rozważyć korzystanie z dysków platformy [Azure.](../../virtual-machines/managed-disks-overview.md)

Niektóre aplikacje w chmurze nie zależą od SMB, dostępu do danych lokalnych komputera ani dostępu współdzielonych. W przypadku tych aplikacji najlepszym wyborem jest często magazyn obiektów, taki jak obiekty blob platformy [Azure.](../blobs/storage-blobs-overview.md)

Kluczem w każdej migracji jest przechwycenie wszystkich wierności plików podczas przenoszenia plików z bieżącej lokalizacji przechowywania na platformę Azure. Jak duża wierność zapewnia opcja usługi Azure Storage i ile wymaga scenariusz, pomaga również wybrać odpowiednią usługę Azure Storage. Dane plików ogólnego przeznaczenia tradycyjnie zależą od metadanych plików. Dane aplikacji mogą nie być.

Oto dwa podstawowe składniki pliku:

- **Strumień danych:** strumień danych pliku przechowuje zawartość pliku.
- **Metadane pliku:** metadane pliku mają następujące składniki podrzędne:
   * Atrybuty pliku, takie jak tylko do odczytu
   * Uprawnienia do plików, które mogą być określane jako uprawnienia *systemu plików NTFS* lub listy *ACL plików i folderów*
   * Znaczniki czasu, w szczególności tworzenie i ostatnio modyfikowane znaczniki czasu
   * Alternatywny strumień danych, który jest przestrzenią do przechowywania większych ilości niestandardowych właściwości

Wierność plików w migracji można zdefiniować jako możliwość:

- Przechowuj wszystkie odpowiednie informacje o plikach w źródle.
- Transfer plików za pomocą narzędzia do migracji.
- Przechowywanie plików w docelowym magazynie migracji.

Aby zapewnić bezproblemowe kontynuowanie [](#migration-toolbox) migracji, zidentyfikuj najlepsze narzędzie do kopiowania zgodnie z potrzebami i dopasuj miejsce docelowe magazynu do źródła.

Biorąc pod uwagę poprzednie informacje, można zobaczyć, że docelowym magazynem plików ogólnego przeznaczenia na platformie Azure są [udziały plików platformy Azure.](storage-files-introduction.md)

W przeciwieństwie do magazynu obiektów blob platformy Azure udział plików platformy Azure może natywnie przechowywać metadane plików. Udziały plików platformy Azure zachowują również hierarchię plików i folderów, atrybuty i uprawnienia. Uprawnienia NTFS można przechowywać w plikach i folderach, ponieważ są one lokalne.

Użytkownik usługi Active Directory, który jest ich lokalnym kontrolerem domeny, może natywnie uzyskać dostęp do udziału plików platformy Azure. Użytkownik aplikacji może więc Azure Active Directory Domain Services (Azure AD DS). Każdy z nich używa swojej bieżącej tożsamości, aby uzyskać dostęp na podstawie uprawnień udziału oraz listy ACL plików i folderów. To zachowanie jest podobne do użytkownika łączącego się z lokalnym udziałem plików.

Alternatywny strumień danych to podstawowy aspekt wierności plików, który obecnie nie może być przechowywany w pliku w udziałach plików platformy Azure. Jest on zachowywany lokalnie, gdy Azure File Sync jest używany.

Dowiedz się więcej na [temat uwierzytelniania usługi Azure AD](storage-files-identity-auth-active-directory-enable.md) Azure AD DS [uwierzytelniania](storage-files-identity-auth-active-directory-domain-service-enable.md) dla udziałów plików platformy Azure.

## <a name="migration-guides"></a>Przewodniki po migracji

W poniższej tabeli wymieniono szczegółowe przewodniki dotyczące migracji.

Jak używać tabeli:

1. Znajdź wiersz dla systemu źródłowego, w których obecnie są przechowywane pliki.

1. Wybierz jeden z tych celów:

   - Wdrożenie hybrydowe przy Azure File Sync do buforowania zawartości lokalnych udziałów plików platformy Azure
   - Udziały plików platformy Azure w chmurze

   Wybierz kolumnę docelową, która odpowiada wybranej wartości.

1. W ramach części części źródłowej i docelowej komórka tabeli zawiera listę dostępnych scenariuszy migracji. Wybierz jedną z nich, aby bezpośrednio połączyć się ze szczegółowym przewodnikiem migracji.

Scenariusz bez linku nie ma jeszcze opublikowanego przewodnika migracji. Sprawdź tę tabelę od czasu do czasu, aby uzyskać aktualizacje. Nowe przewodniki zostaną opublikowane, gdy będą dostępne.

| Element źródłowy | Cel: </br>Wdrożenie hybrydowe | Cel: </br>Wdrożenie tylko w chmurze |
|:---|:--|:--|
| | Kombinacja narzędzi:| Kombinacja narzędzi: |
| Windows Server 2012 R2 i nowsze | <ul><li>[Azure File Sync](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure File Sync i Azure DataBox](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>Za pomocą narzędzia RoboCopy do zainstalowanego udziału plików platformy Azure</li><li>Za pośrednictwem Azure File Sync</li></ul> |
| Windows Server 2012 i starsze | <ul><li>Za pomocą usługi DataBox i Azure File Sync do najnowszego systemu operacyjnego serwera</li><li>Za pomocą usługi Storage Migration Service na najnowszy serwer z Azure File Sync, a następnie przekaż</li></ul> | <ul><li>Za pośrednictwem usługi Storage Migration Service do ostatniego serwera z Azure File Sync</li><li>Za pomocą narzędzia RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Magazyn dołączony do sieci (NAS) | <ul><li>[Za pośrednictwem Azure File Sync przekazywania](storage-files-migration-nas-hybrid.md)</li><li>[Za pomocą narzędzia DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Za pośrednictwem usługi DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>Za pomocą narzędzia RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync i robocopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Za pomocą narzędzia RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 lub StorSimple Cloud Appliance 8600 | <ul><li>[Za pośrednictwem dedykowanej usługi migracji danych w chmurze](storage-files-migration-storsimple-8000.md)</li></ul> | |
| Urządzenie StorSimple w chmurze 1200 | <ul><li>[Za pośrednictwem Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Przybornik migracji

### <a name="file-copy-tools"></a>Narzędzia do kopiowania plików

Istnieje kilka narzędzi do kopiowania plików dostępnych od firmy Microsoft i innych. Aby wybrać odpowiednie narzędzie dla scenariusza migracji, należy wziąć pod uwagę następujące podstawowe pytania:

* Czy narzędzie obsługuje lokalizację źródłową i docelową dla kopii plików?

* Czy narzędzie obsługuje ścieżkę sieciową lub dostępne protokoły (takie jak REST, SMB lub NFS) między źródłową i docelową lokalizacją magazynu?

* Czy narzędzie zachowuje niezbędną wierność plików obsługiwaną przez lokalizację źródłową i docelową?

    W niektórych przypadkach docelowy magazyn nie obsługuje takiej samej wierności jak źródło. Jeśli magazyn docelowy jest wystarczający dla Twoich potrzeb, narzędzie musi odpowiadać tylko możliwościom wierności plików obiektu docelowego.

* Czy narzędzie ma funkcje, które umożliwiają dopasowanie go do strategii migracji?

    Rozważ na przykład, czy narzędzie pozwala zminimalizować przestoje.
    
    Gdy narzędzie obsługuje opcję dublowania źródła do miejsca docelowego, często można uruchamiać je wielokrotnie na tym samym źródle i w tym samym miejscu docelowym, gdy źródło pozostaje dostępne.

    Przy pierwszym uruchomieniu narzędzia kopiuje ono większość danych. Ten początkowy przebieg może potrwać jakiś czas. Często trwa dłużej niż chcesz, aby źródło danych było przesłonene w tryb offline dla procesów biznesowych.

    Dublując źródło do obiektu docelowego (tak jak w przypadku **narzędzia Robocopy /ŹRÓDLE),** można ponownie uruchomić narzędzie w tym samym źródle i tym samym celu. Przebieg jest znacznie szybszy, ponieważ musi transportować tylko zmiany źródłowe, które występują po poprzednim uruchomieniu. Ponowne uruchomić narzędzie kopiowania w ten sposób może znacznie skrócić przestoje.

W poniższej tabeli zaklasyfikuje się narzędzia firmy Microsoft i ich bieżącą przydatność dla udziałów plików platformy Azure:

| Zalecane | Narzędzie | Obsługa udziałów plików platformy Azure | Zachowywanie wierności plików |
| :-: | :-- | :---- | :---- |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Obsługiwane. Udziały plików platformy Azure mogą być instalowane jako dyski sieciowe. | Pełna wierność.* |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natywnie zintegrowane z udziałami plików platformy Azure. | Pełna wierność.* |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Storage Migration Service | Obsługiwane pośrednio. Udziały plików platformy Azure mogą być instalowane jako dyski sieciowe na serwerach docelowych programu SMS. | Pełna wierność.* |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>wersja 10.6 | Obsługiwane. | Nie obsługuje kopii źródłowej głównej listy ACL, w przeciwnym razie pełnej wierności.* </br>[Dowiedz się, jak używać narzędzia AzCopy z udziałami plików platformy Azure](../common/storage-use-azcopy-files.md) |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Obsługiwane. | Urządzenie DataBox w pełni obsługuje metadane. |
|![Nie w pełni zalecane](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Eksplorator usługi Azure Storage </br>wersja 1.14 | Obsługiwane. | Nie kopiuje adresów ACL. Obsługuje znaczniki czasu.  |
|![Niezalecane](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Obsługiwane. | Nie kopiuje metadanych. |
|||||

*\* Pełna wierność: spełnia lub przekracza możliwości udziału plików platformy Azure.*

### <a name="migration-helper-tools"></a>Narzędzia pomocnika migracji

W tej sekcji opisano narzędzia, które ułatwiają planowanie i uruchamianie migracji.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy z Microsoft Corporation

RoboCopy to jedno z narzędzi, które ma największe zastosowanie do migracji plików. Jest ona częścią systemu Windows. Główna dokumentacja [narzędzia RoboCopy](/windows-server/administration/windows-commands/robocopy) jest przydatnym zasobem dla wielu opcji tego narzędzia.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize firmy JAM Software GmbH

Azure File Sync jest skalowana głównie z liczbą elementów (plików i folderów), a nie z całkowitą ilością miejsca do magazynowania. Narzędzie TreeSize umożliwia określenie liczby elementów na woluminach systemu Windows Server.

Za pomocą tego narzędzia można utworzyć perspektywę przed wdrożeniem [Azure File Sync wdrożenia.](../file-sync/file-sync-deployment-guide.md) Można go również używać, gdy po wdrożeniu jest zaangażowany udział w obsłudze warstw w chmurze. W tym scenariuszu zobaczysz liczbę elementów i katalogi, które najbardziej używają pamięci podręcznej serwera.

Przetestowana wersja narzędzia to 4.4.1. Jest ona zgodna z plikami warstw w chmurze. Narzędzie nie spowoduje odwołania plików warstwowych podczas normalnego działania.

## <a name="next-steps"></a>Następne kroki

1. Utwórz plan, dla którego chcesz utworzyć wdrożenie udziałów plików platformy Azure (tylko w chmurze lub hybrydowe).
1. Przejrzyj listę dostępnych przewodników migracji, aby znaleźć szczegółowy przewodnik, który odpowiada Twojeowi źródłowemu i wdrożeniu udziałów plików platformy Azure.

Więcej informacji na temat technologii Azure Files wymienionych w tym artykule:

* [Omówienie udziału plików platformy Azure](storage-files-introduction.md)
* [Planowanie wdrażania usługi Azure File Sync](../file-sync/file-sync-planning.md)
* [Azure File Sync: warstwy w chmurze](../file-sync/file-sync-cloud-tiering-overview.md)
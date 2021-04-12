---
title: Migracja do udziałów plików usługi Azure File
description: Dowiedz się więcej na temat migracji do udziałów plików platformy Azure i Znajdź Przewodnik migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 0c2de0c1b024d093bd0276a852d9b97ba3320f4b
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286338"
---
# <a name="migrate-to-azure-file-shares"></a>Migracja do udziałów plików usługi Azure File

W tym artykule opisano podstawowe aspekty migracji do udziałów plików platformy Azure.

Ten artykuł zawiera podstawowe informacje dotyczące migracji oraz tabelę przewodników migracji. Te przewodniki ułatwiają przenoszenie plików do udziałów plików platformy Azure. Przewodniki są zorganizowane na podstawie tego, gdzie znajdują się dane i w jakim modelu wdrożenia (tylko w chmurze lub hybrydowe).

## <a name="migration-basics"></a>Podstawowe informacje o migracji

Platforma Azure ma wiele dostępnych typów magazynu w chmurze. Podstawowym aspektem migracji plików na platformę Azure jest określenie, która opcja usługi Azure Storage jest odpowiednia dla danych.

[Udziały plików platformy Azure](storage-files-introduction.md) są odpowiednie do danych plików ogólnego przeznaczenia. Te dane obejmują wszystkie elementy korzystające z lokalnego udziału SMB lub NFS w systemie. Za pomocą [Azure File Sync](storage-sync-files-planning.md)można buforować zawartość kilku udziałów plików platformy Azure na serwerach z systemem Windows Server w środowisku lokalnym.

W przypadku aplikacji, która jest aktualnie uruchomiona na serwerze lokalnym, przechowywanie plików w udziale plików platformy Azure może być dobrym wyborem. Możesz przenieść aplikację na platformę Azure i użyć udziałów plików platformy Azure jako magazynu udostępnionego. W tym scenariuszu można również rozważyć użycie [dysków platformy Azure](../../virtual-machines/managed-disks-overview.md) .

Niektóre aplikacje w chmurze nie zależą od protokołu SMB ani dostępu do danych na komputerze lokalnym lub dostępu współdzielonego. W przypadku tych aplikacji często najlepszym wyborem jest magazyn obiektów, taki jak [obiekty blob platformy Azure](../blobs/storage-blobs-overview.md) .

Klucz w każdej migracji ma na celu przechwycenie całej odpowiedniej wierności plików podczas przenoszenia plików z bieżącej lokalizacji magazynu na platformę Azure. Stopień wierności obsługi opcji usługi Azure Storage oraz ilość wymaganego scenariusza również ułatwia wybranie odpowiedniej usługi Azure Storage. Dane ogólnego przeznaczenia są tradycyjnie zależne od metadanych pliku. Dane aplikacji mogą nie być obsługiwane.

Poniżej przedstawiono dwa podstawowe składniki pliku:

- **Strumień danych**: strumień danych pliku przechowuje zawartość pliku.
- **Metadane pliku**: w metadanych pliku znajdują się następujące składniki:
   * Atrybuty plików, takie jak tylko do odczytu
   * Uprawnienia do plików, które mogą być określane jako *uprawnienia systemu plików NTFS* lub *listy ACL plików i folderów*
   * Sygnatury czasowe, w szczególności tworzenie i ostatnio modyfikowane sygnatury czasowe
   * Alternatywny strumień danych, który jest miejscem do przechowywania większych ilości niestandardowych właściwości

Wierność pliku w migracji można zdefiniować jako możliwość:

- Przechowuj wszystkie odpowiednie informacje o plikach w źródle.
- Transferowanie plików za pomocą narzędzia do migracji.
- Przechowuj pliki w docelowym magazynie migracji.

Aby zapewnić bezproblemową migrację, zidentyfikuj [najlepsze narzędzie do kopiowania dla potrzeb](#migration-toolbox) i Dopasuj miejsce docelowe magazynu do źródła.

Biorąc pod uwagę poprzednie informacje, można zobaczyć, że magazyn docelowy dla plików ogólnego przeznaczenia na platformie Azure to [udziały plików platformy Azure](storage-files-introduction.md).

W przeciwieństwie do magazynu obiektów w obiektach Blob platformy Azure, udział plików platformy Azure umożliwia natywne przechowywanie metadanych plików. Udziały plików platformy Azure zachowują również hierarchię plików i folderów, atrybuty i uprawnienia. Uprawnienia NTFS mogą być przechowywane w plikach i folderach, ponieważ są one lokalne.

Użytkownik Active Directory, który jest lokalnym kontrolerem domeny, może natywnie uzyskiwać dostęp do udziału plików platformy Azure. Może więc być użytkownikiem Azure Active Directory Domain Services (AD DS platformy Azure). Każda z nich używa ich bieżącej tożsamości w celu uzyskania dostępu na podstawie uprawnień udziału oraz list ACL plików i folderów. To zachowanie jest podobne do użytkownika łączącego się z lokalnym udziałem plików.

Alternatywny strumień danych jest podstawowym aspektem dokładności pliku, który obecnie nie może być przechowywany w pliku w udziale plików platformy Azure. Jest on przechowywany lokalnie, gdy Azure File Sync jest używany.

Dowiedz się więcej o [uwierzytelnianiu usługi Azure AD](storage-files-identity-auth-active-directory-enable.md) i [uwierzytelnianiu usługi Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) dla udziałów plików platformy Azure.

## <a name="migration-guides"></a>Przewodniki po migracji

W poniższej tabeli wymieniono szczegółowe przewodniki dotyczące migracji.

Jak korzystać z tabeli:

1. Znajdź wiersz systemu źródłowego, w którym przechowywane są pliki.

1. Wybierz jeden z następujących elementów docelowych:

   - Wdrożenie hybrydowe używające Azure File Sync do buforowania zawartości udziałów plików platformy Azure w środowisku lokalnym
   - Udziały plików platformy Azure w chmurze

   Wybierz docelową kolumnę, która odpowiada wybranej opcji.

1. W ramach przecięcia elementu źródłowego i docelowego komórka tabeli zawiera dostępne scenariusze migracji. Wybierz jeden z nich, aby połączyć się bezpośrednio ze szczegółowym przewodnikiem migracji.

Scenariusz bez linku jeszcze nie ma opublikowanego przewodnika migracji. Sprawdź, czy w tej tabeli od czasu są aktualizowane aktualizacje. Nowe prowadnice zostaną opublikowane, gdy staną się dostępne.

| Element źródłowy | Cel: </br>Wdrożenie hybrydowe | Cel: </br>Wdrażanie tylko w chmurze |
|:---|:--|:--|
| | Kombinacja narzędzi:| Kombinacja narzędzi: |
| System Windows Server 2012 R2 lub nowszy | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync i Azure DataBox](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>Za pośrednictwem RoboCopy do zainstalowanego udziału plików platformy Azure</li><li>Za pośrednictwem Azure File Sync</li></ul> |
| Windows Server 2012 i starsze | <ul><li>Za pośrednictwem DataBox i Azure File Sync do ostatniego systemu operacyjnego serwera</li><li>Za pomocą usługi migracji magazynu na ostatni serwer z Azure File Sync, a następnie Przekaż</li></ul> | <ul><li>Za pomocą usługi migracji magazynu na ostatni serwer z Azure File Sync</li><li>Za pośrednictwem RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Magazyn dołączony do sieci (NAS) | <ul><li>[Za pośrednictwem przekazywania Azure File Sync](storage-files-migration-nas-hybrid.md)</li><li>[Za pośrednictwem DataBox i Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[Za pośrednictwem DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>Za pośrednictwem RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync i RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Za pośrednictwem RoboCopy do zainstalowanego udziału plików platformy Azure</li></ul> |
| Microsoft Azure StorSimple urządzenie w chmurze 8100 lub StorSimple w chmurze 8600 | <ul><li>[Za pośrednictwem dedykowanej usługi w chmurze migracji danych](storage-files-migration-storsimple-8000.md)</li></ul> | |
| Urządzenie w chmurze StorSimple 1200 | <ul><li>[Za pośrednictwem Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Przybornik migracji

### <a name="file-copy-tools"></a>Narzędzia do kopiowania plików

Istnieje kilka narzędzi do kopiowania plików dostępnych w firmie Microsoft i innych. Aby wybrać odpowiednie narzędzie dla scenariusza migracji, należy wziąć pod uwagę następujące podstawowe pytania:

* Czy narzędzie obsługuje lokalizację źródłową i docelową dla kopii pliku?

* Czy narzędzie obsługuje ścieżkę sieciową lub dostępne protokoły (takie jak REST, SMB lub NFS) między źródłową i docelową lokalizacją magazynu?

* Czy narzędzie zachowuje wymaganą wierność plików obsługiwaną przez lokalizację źródłową i docelową?

    W niektórych przypadkach magazyn docelowy nie obsługuje takiej samej wierności jak źródło. Jeśli docelowy magazyn jest wystarczający dla Twoich potrzeb, narzędzie musi być zgodne tylko z funkcjami dokładności pliku.

* Czy narzędzie ma funkcje, które umożliwiają dopasowanie do strategii migracji?

    Rozważmy na przykład, czy narzędzie pozwala zminimalizować czas przestoju.
    
    Gdy narzędzie obsługuje opcję dublowania źródła do obiektu docelowego, można często uruchamiać ją wielokrotnie w tym samym źródle i miejscu docelowym, gdy źródło pozostaje dostępne.

    Przy pierwszym uruchomieniu narzędzia są kopiowane zbiorczo dane. Ten początkowy przebieg może być ostatnim czasem. Często trwa dłużej niż chcesz, aby dane były przełączane do trybu offline w ramach procesów firmy.

    Przez dublowanie źródła do obiektu docelowego (podobnie jak w przypadku **ROBOCOPY/MIR**), można uruchomić narzędzie ponownie w tym samym źródle i miejscu docelowym. Przebieg działa znacznie szybciej, ponieważ wymaga przetransportowania tylko zmian źródłowych, które wystąpiły po poprzednim uruchomieniu. Oddziałanie narzędzia kopiowania w ten sposób może znacznie skrócić czas przestoju.

Poniższa tabela klasyfikuje narzędzia firmy Microsoft i ich bieżącą przydatność dla udziałów plików platformy Azure:

| Zalecane | Narzędzie | Obsługa udziałów plików platformy Azure | Zachowywanie wierności plików |
| :-: | :-- | :---- | :---- |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Obsługiwane. Udziały plików platformy Azure można instalować jako dyski sieciowe. | Pełna wierność. * |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natywnie zintegrowane z udziałami plików platformy Azure. | Pełna wierność. * |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Usługa migracji magazynu | Obsługiwane pośrednio. Udziały plików platformy Azure można instalować jako dyski sieciowe na serwerach docelowych programu SMS. | Pełna wierność. * |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>wersja 10,6 | Obsługiwane. | Program nie obsługuje kopiowania głównej listy kontroli dostępu do źródła, w przeciwnym razie pełna wierność. * </br>[Dowiedz się, jak używać AzCopy z udziałami plików platformy Azure](../common/storage-use-azcopy-files.md) |
|![Tak, zalecane](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Obsługiwane. | DataBox w pełni obsługuje metadane. |
|![Nie w pełni zalecane](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Eksplorator usługi Azure Storage </br>Wersja 1,14 | Obsługiwane. | Nie kopiuje list ACL. Obsługuje sygnatury czasowe.  |
|![Niezalecane](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Obsługiwane. | Nie kopiuje metadanych. |
|||||

*\* Pełna wierność: spełnia lub przekracza możliwości udostępniania plików platformy Azure.*

### <a name="migration-helper-tools"></a>Narzędzia pomocnika migracji

W tej sekcji opisano narzędzia, które ułatwiają planowanie i uruchamianie migracji.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy firmy Microsoft Corporation

RoboCopy jest jednym z narzędzi, które są najbardziej odpowiednie do migracji plików. Jest to część systemu Windows. Główna [Dokumentacja Robocopy](/windows-server/administration/windows-commands/robocopy) to przydatne zasoby dla wielu opcji tego narzędzia.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize od oprogramowania DŻEMów GmbH

Azure File Sync skaluje się przede wszystkim do liczby elementów (plików i folderów), a nie całkowitej ilości miejsca do magazynowania. Narzędzie TreeSize pozwala określić liczbę elementów na woluminach z systemem Windows Server.

Możesz użyć narzędzia, aby utworzyć perspektywę przed [wdrożeniem Azure File Sync](storage-sync-files-deployment-guide.md). Można go również użyć, gdy obsługa warstw w chmurze jest zajęta po wdrożeniu. W tym scenariuszu zobaczysz liczbę elementów i katalogów, które używają pamięci podręcznej serwera.

Przetestowana wersja narzędzia jest w wersji 4.4.1. Jest on zgodny z plikami w warstwach w chmurze. Narzędzie nie będzie powodowało odwoływania plików warstwowych podczas normalnego działania.

## <a name="next-steps"></a>Następne kroki

1. Utwórz plan, dla którego chcesz wdrożyć udziały plików platformy Azure (tylko w chmurze lub hybrydowe).
1. Zapoznaj się z listą dostępnych przewodników migracji, aby znaleźć szczegółowy przewodnik, który jest zgodny ze źródłem i wdrożeniem udziałów plików platformy Azure.

Więcej informacji o Azure Files technologiach wymienionych w tym artykule:

* [Omówienie udziału plików platformy Azure](storage-files-introduction.md)
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
* [Azure File Sync: Obsługa warstw w chmurze](storage-sync-cloud-tiering-overview.md)
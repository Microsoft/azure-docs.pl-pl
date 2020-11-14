---
title: Migrowanie danych do Azure File Sync z Azure Data Box
description: Migruj dane zbiorcze w trybie offline, które są zgodne z Azure File Sync. Unikaj konfliktów plików i zachowuj listy ACL plików i folderów oraz sygnatury czasowe po włączeniu synchronizacji.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630230"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrowanie danych zbiorczych do usługi Azure File Sync za pomocą usługi Azure DataBox
Dane zbiorcze można migrować do Azure File Sync na dwa sposoby:

* **Przekaż pliki przy użyciu Azure File Sync.** Jest to najprostsza metoda. Przenieś pliki lokalnie do systemu Windows Server 2012 R2 lub nowszego i Zainstaluj agenta Azure File Sync. Po skonfigurowaniu synchronizacji pliki zostaną przekazane z serwera. (Nasi klienci obecnie mają średnią szybkość przekazywania wynoszącą 1 TiB co dwa dni). Aby upewnić się, że serwer nie używa zbyt dużej przepustowości w centrum danych, można skonfigurować [harmonogram ograniczania przepustowości](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Przenoszenie plików do trybu offline.** Jeśli nie masz wystarczającej przepustowości, możesz nie być w stanie przekazywać plików na platformę Azure w rozsądnym czasie. Wyzwanie jest początkową synchronizacją całego zestawu plików. Aby rozwiązać ten problem, należy użyć narzędzi do migracji zbiorczej w trybie offline, takich jak [rodzina Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

W tym artykule wyjaśniono, jak migrować pliki w tryb offline w taki sposób, który jest zgodny z Azure File Sync. Postępuj zgodnie z tymi instrukcjami, aby uniknąć konfliktów plików i zachować listy kontroli dostępu do plików i folderów (ACL) oraz sygnatury czasowe po włączeniu synchronizacji.

## <a name="migration-tools"></a>Narzędzia migracji
Proces opisywany w tym artykule działa nie tylko w przypadku urządzenie Data Box, ale również dla innych narzędzi migracji w trybie offline. Działa również w przypadku narzędzi, takich jak AzCopy, Robocopy lub narzędzia partnerskie i usługi działające bezpośrednio przez Internet. Jednak w celu przezwyciężenia początkowego wyzwania przekazywania wykonaj kroki opisane w tym artykule, aby użyć tych narzędzi w sposób zgodny z Azure File Sync.

W niektórych przypadkach należy przenieść z jednego systemu Windows Server na inny serwer systemu Windows przed przyjęciem Azure File Sync. [Usługa migracji magazynu](/windows-server/storage/storage-migration-service/overview) (SMS) może Ci pomóc. Bez względu na to, czy chcesz przeprowadzić migrację do wersji systemu operacyjnego serwera, która jest obsługiwana przez Azure File Sync (Windows Server 2012R2 i w górę), lub po prostu musisz przeprowadzić migrację, ponieważ kupujesz nowy system dla Azure File Sync, program SMS ma wiele funkcji i korzyści, które ułatwią proces migracji.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Zalety korzystania z narzędzia do transferowania danych w trybie offline
Poniżej przedstawiono główne korzyści wynikające z używania narzędzia do transferu, takiego jak urządzenie Data Box do migracji w trybie offline:

- Nie trzeba przekazywać wszystkich plików za pośrednictwem sieci. W przypadku dużych przestrzeni nazw to narzędzie może oszczędzić znaczną przepustowość sieci i czas.
- W przypadku korzystania z Azure File Sync niezależnie od używanego narzędzia do transferu (urządzenie Data Box, usługi Azure Import/Export itd.) serwer na żywo przekazuje tylko te pliki, które zmieniają się po przeniesieniu danych na platformę Azure.
- Azure File Sync synchronizuje listy ACL plików i folderów, nawet jeśli narzędzie do migracji zbiorczej w trybie offline nie transportuje list ACL.
- Urządzenie Data Box i Azure File Sync nie wymagają przestojów. W przypadku używania urządzenie Data Box do transferowania danych na platformę Azure można wydajnie korzystać z przepustowości sieci i zachować wierność pliku. Możesz również zachować aktualność przestrzeni nazw, przekazując tylko te pliki, które zmieniły się po przeniesieniu danych na platformę Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Wymagania wstępne dotyczące transferu danych w trybie offline
Nie należy włączać synchronizacji na serwerze, który jest migrowany przed ukończeniem transferu danych w trybie offline. Inne kwestie, które należy wziąć pod uwagę przed rozpoczęciem, są następujące:

- Jeśli planujesz używać urządzenie Data Box do migracji zbiorczej: Zapoznaj się z [wymaganiami wstępnymi dotyczącymi wdrożenia urządzenie Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planowanie ostatecznej topologii Azure File Sync: [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
- Wybierz konta usługi Azure Storage, w których będą przechowywane udziały plików, z którymi chcesz przeprowadzić synchronizację. Upewnij się, że migracja Zbiorcza odbywa się do tymczasowych udziałów przemieszczania na tych samych kontach magazynu. Migrację zbiorczą można włączyć tylko przy użyciu końcowego i przejściowego udziału znajdującego się w tym samym koncie magazynu.
- Migracji zbiorczej można używać tylko podczas tworzenia nowej relacji synchronizacji z lokalizacją serwera. Nie można włączyć migracji zbiorczej z istniejącą relacją synchronizacji.


## <a name="process-for-offline-data-transfer"></a>Proces transferu danych w trybie offline
Poniżej przedstawiono sposób konfigurowania Azure File Sync w sposób zgodny z narzędziami do migracji zbiorczej, takimi jak Azure Data Box:

![Diagram przedstawiający sposób konfigurowania Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Szczegóły |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Zamów urządzenie Data Box](../../databox/data-box-deploy-ordered.md). Rodzina urządzenie Data Box oferuje [kilka produktów](https://azure.microsoft.com/services/storage/databox/data) spełniających Twoje potrzeby. Po otrzymaniu urządzenie Data Box postępuj zgodnie z [dokumentacją, aby skopiować dane](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) do tej ścieżki UNC na urządzenie Data Box: *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*. Tutaj, *ShareName* jest nazwą udziału przemieszczania. Wyślij urządzenie Data Box z powrotem do platformy Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Poczekaj, aż pliki zostaną wyświetlone w udziałach plików platformy Azure, które zostały wybrane jako tymczasowe udziały przejściowe. *Nie należy włączać synchronizacji do tych udziałów.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Utwórz nowy pusty udział dla każdego udziału plików, który urządzenie Data Box utworzony przez Ciebie. Ten nowy udział powinien znajdować się na tym samym koncie magazynu co udział urządzenie Data Box. [Jak utworzyć nowy udział plików platformy Azure](storage-how-to-create-file-share.md).</li><li>[Utwórz grupę synchronizacji](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) w usłudze synchronizacji magazynu. Odwołuje się do pustego udziału jako punktu końcowego w chmurze. Powtórz ten krok dla każdego urządzenie Data Box udziału plików. [Skonfiguruj Azure File Sync](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Dodaj swój katalog serwera na żywo jako punkt końcowy serwera](storage-sync-files-deployment-guide.md#create-a-server-endpoint). W procesie należy określić, że pliki zostały przeniesione na platformę Azure, i odwołać się do udziałów przemieszczania. W razie konieczności można włączyć lub wyłączyć obsługę warstw w chmurze. Podczas tworzenia punktu końcowego serwera na serwerze aktywnym należy odwołać się do udziału przemieszczania. W bloku **Dodawanie serwera punktu końcowego** w obszarze **transfer danych offline** wybierz pozycję **włączone** , a następnie wybierz udział przemieszczania, który musi znajdować się na tym samym koncie magazynu co punkt końcowy w chmurze. W tym miejscu lista dostępnych udziałów jest filtrowana według kont magazynu i udziałów, które nie są już synchronizowane. Zrzut ekranu poniżej poniższej tabeli pokazuje, jak odwoływać się do udziału DataBox podczas tworzenia punktu końcowego serwera w Azure Portal. |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Po dodaniu punktu końcowego serwera w poprzednim kroku dane zaczynają się automatycznie przepływają z prawej strony. Sekcja [synchronizowanie udziału wyjaśnia,](#syncing-the-share) kiedy dane są przepływów z udziału DataBox lub z systemu Windows Server |
| |

![Zrzut ekranu przedstawiający interfejs użytkownika Azure Portal, pokazujący sposób włączania transferu danych w trybie offline podczas tworzenia nowego punktu końcowego serwera](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronizowanie udziału
Po utworzeniu punktu końcowego serwera rozpocznie się synchronizacja. Proces synchronizacji określa, czy każdy plik na serwerze istnieje również w udziale tymczasowym, w którym urządzenie Data Box zdeponowane pliki. Jeśli plik istnieje, proces synchronizacji kopiuje plik z udziału przemieszczania zamiast przekazywania go z serwera. Jeśli plik nie istnieje w udziale tymczasowym lub jeśli na serwerze lokalnym jest dostępna nowsza wersja, proces synchronizacji przekazuje plik z serwera lokalnego.

Podczas synchronizacji udziału synchronizacja scala wszystkie brakujące atrybuty pliku, uprawnienia lub sygnatury czasowe z wariantów plików na serwerze lokalnym, łącząc je z odpowiednimi odpowiednikami plików z udziału DataBox. Dzięki temu każdy plik i folder docierają do wszystkich możliwych wierności plików w udziale plików platformy Azure.

> [!IMPORTANT]
> Tryb migracji zbiorczej można włączyć tylko podczas tworzenia punktu końcowego serwera. Po ustanowieniu punktu końcowego serwera nie można zintegrować danych z migracji zbiorczej z już synchronizowanego serwera z przestrzenią nazw.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Listy ACL i sygnatury czasowe dla plików i folderów
Azure File Sync zapewnia synchronizację list ACL plików i folderów z serwera na żywo, nawet jeśli używane narzędzie do migracji zbiorczej nie zakończyło wstępnego transportu list ACL. Z tego powodu udział przemieszczania nie musi zawierać list ACL dla plików i folderów. Po włączeniu funkcji migracji danych w trybie offline podczas tworzenia nowego punktu końcowego serwera wszystkie listy ACL plików są synchronizowane na serwerze. Nowo utworzone i zmodyfikowane sygnatury czasowe są również synchronizowane.

## <a name="shape-of-the-namespace"></a>Kształt przestrzeni nazw
Po włączeniu synchronizacji zawartość serwera określa kształt przestrzeni nazw. Jeśli pliki zostaną usunięte z serwera lokalnego po wykonaniu migawki urządzenie Data Box i zakończeniu migracji, te pliki nie przechodzą na żywo, synchronizują przestrzeń nazw. Pozostają one w udziale przemieszczania, ale nie są kopiowane. Jest to konieczne, ponieważ synchronizacja zachowuje przestrzeń nazw zgodnie z serwerem działającym na żywo. *Migawka* urządzenie Data Box jest tylko tymczasowym środowiskiem do wydajnego kopiowania plików. Nie jest to władza dla kształtu aktywnej przestrzeni nazw.

## <a name="cleaning-up-after-bulk-migration"></a>Czyszczenie po migracji zbiorczej 
Ponieważ serwer kończy swoją początkową synchronizację przestrzeni nazw, urządzenie Data Box pliki z migracją zbiorczą używają tymczasowego udziału plików. W bloku **właściwości punktu końcowego serwera** w Azure Portal w sekcji **transfer danych offline** stan zmieni się z **w toku** na **ukończono**. 

![Zrzut ekranu przedstawiający blok właściwości punktu końcowego serwera, w którym znajdują się kontrolki stan i Wyłącz dla transferu danych w trybie offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Teraz możesz wyczyścić udział przemieszczania w celu oszczędności kosztów:

1. W bloku **właściwości punktu końcowego serwera** , gdy stan jest **zakończony** , wybierz pozycję **Wyłącz transfer danych w trybie offline**.
2. Rozważ usunięcie udziału przemieszczania w celu zaoszczędzenia kosztów. Udział przemieszczania prawdopodobnie nie zawiera list ACL plików i folderów, dlatego jest mało prawdopodobne. W celu utworzenia punktu w czasie wykonywania kopii zapasowych należy utworzyć rzeczywistą [migawkę synchronizowanego udziału plików platformy Azure](storage-snapshots-files.md). Można [skonfigurować Azure Backup, aby wykonać migawki]( ../../backup/backup-afs.md) zgodnie z harmonogramem.

Wyłącz tryb transferu danych w trybie offline tylko wtedy, gdy stan jest **zakończony** lub gdy chcesz anulować z powodu niepoprawnej konfiguracji. Jeśli wyłączysz tryb podczas wdrażania, pliki rozpoczną przekazywanie z serwera, nawet jeśli udział przemieszczania jest nadal dostępny.

> [!IMPORTANT]
> Po wyłączeniu trybu transferu danych w trybie offline nie można włączyć go ponownie, nawet jeśli udział przemieszczania z migracji zbiorczej jest nadal dostępny.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync i wstępnie rozsiane pliki w chmurze

Jeśli pliki są umieszczane w udziale plików platformy Azure w inny sposób niż DataBox — na przykład za pośrednictwem AzCopy, RoboCopy z kopii zapasowej w chmurze lub innej metody — należy nadal postępować zgodnie z [procesem transfer danych w trybie offline](#process-for-offline-data-transfer) opisanym w tym artykule. Musisz zignorować DataBox jako metodę przenoszenia plików do chmury. Jednak jest to najważniejsze, aby upewnić się, że nadal następuje proces umieszczania plików w *udziale przemieszczania* , a nie na ostatnim, Azure File Sync połączony udział.

> [!WARNING]
> **Postępuj zgodnie z procesem umieszczania plików w udziale tymczasowym, a nie końcowym** , Azure File Sync połączonego udziału. Jeśli tego nie zrobisz, mogą wystąpić konflikty plików (wersje plików zostaną zapisane), a także pliki usunięte na serwerze na żywo, jeśli nadal istnieją w starszym, wyznaczonym do użycia zestawie plików. Ponadto zmiany folderów zostaną scalone ze sobą, co bardzo trudne do oddzielenia przestrzeni nazw po wystąpieniu błędu.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie funkcji Azure File Sync](storage-sync-files-deployment-guide.md)
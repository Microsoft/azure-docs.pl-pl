---
title: Migrowanie danych do Azure File Sync za pomocą Azure Data Box
description: Migrowanie danych zbiorczych w trybie offline zgodnych z Azure File Sync. Unikaj konfliktów plików i zachowaj ACL plików i folderów oraz sygnatury czasowe po włączeniu synchronizacji.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796845"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrowanie danych zbiorczych do usługi Azure File Sync za pomocą usługi Azure DataBox
Dane zbiorcze można migrować do Azure File Sync na dwa sposoby:

* **Przekaż pliki przy użyciu Azure File Sync.** Jest to najprostsza metoda. Przenieś pliki lokalnie do systemu Windows Server 2012 R2 lub nowszego i zainstaluj Azure File Sync agenta. Po skonfigurowaniu synchronizacji pliki zostaną przekazane z serwera. (Obecnie nasi klienci mają średnią szybkość przekazywania 1 TiB co około dwa dni). Aby upewnić się, że serwer nie używa zbyt dużej przepustowości dla centrum danych, warto skonfigurować harmonogram [ograniczania przepustowości.](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Przenieś pliki w trybie offline.** Jeśli nie masz wystarczającej przepustowości, być może nie będziesz w stanie przekazać plików na platformę Azure w rozsądnym czasie. Wyzwaniem jest początkowa synchronizacja całego zestawu plików. Aby rozwiązać ten problem, użyj narzędzi migracji zbiorczej w trybie offline, takich [jak Azure Data Box rodziny .](https://azure.microsoft.com/services/storage/databox) 

W tym artykule wyjaśniono, jak migrować pliki w trybie offline w sposób zgodny z Azure File Sync. Postępuj zgodnie z tymi instrukcjami, aby uniknąć konfliktów plików i zachować listy kontroli dostępu do plików i folderów (ACL) i znaczniki czasu po włączeniu synchronizacji.

## <a name="migration-tools"></a>Narzędzia migracji
Proces, który opisano w tym artykule, działa nie tylko w przypadku urządzenie Data Box ale także w przypadku innych narzędzi migracji w trybie offline. Działa również w przypadku narzędzi, takich jak AzCopy, Robocopy, lub narzędzi partnerskich i usług, które działają bezpośrednio przez Internet. Aby jednak rozwiązać początkowe wyzwanie związane z przekazywaniem, wykonaj kroki opisane w tym artykule, aby użyć tych narzędzi w sposób zgodny z Azure File Sync.

W niektórych przypadkach należy przejść z jednego systemu Windows Server do innego systemu Windows Server przed Azure File Sync. [Usługa Storage Migration Service](/windows-server/storage/storage-migration-service/overview) (SMS) może w tym pomóc. Bez względu na to, czy musisz przeprowadzić migrację do wersji systemu operacyjnego serwera obsługiwanej przez usługę Azure File Sync (Windows Server 2012R2 i nowsze), czy po prostu przeprowadzić migrację, ponieważ kupujesz nowy system dla usługi Azure File Sync, program SMS ma wiele funkcji i korzyści, które ułatwiają bezproblemowe przeprowadzenie migracji.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Zalety używania narzędzia do transferowania danych w trybie offline
Oto główne zalety korzystania z narzędzia transferu, takiego jak urządzenie Data Box migracji w trybie offline:

- Nie musisz przekazywać wszystkich plików za pośrednictwem sieci. W przypadku dużych przestrzeni nazw to narzędzie może zaoszczędzić znaczną przepustowość i czas sieci.
- Gdy używasz usługi Azure File Sync, niezależnie od tego, którego narzędzia transferu używasz (urządzenie Data Box, usługi Azure Import/Export itp.), serwer na żywo przekaże tylko pliki, które zmieniają się po przeniesieniu danych na platformę Azure.
- Azure File Sync synchronizuje pliki i foldery ACL, nawet jeśli narzędzie migracji zbiorczej w trybie offline nie transportuje ACL.
- urządzenie Data Box i Azure File Sync nie wymagają przestoju. W przypadku używania urządzenie Data Box do transferu danych na platformę Azure wydajnie używasz przepustowości sieci i zachowujesz wierność plików. Przestrzeń nazw jest również na bieżąco, przesyłając tylko pliki, które zmieniają się po przenoszeniu danych na platformę Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Wymagania wstępne dotyczące transferu danych w trybie offline
Przed zakończeniem transferu danych w trybie offline nie należy włączać synchronizacji na migrowanych serwerach. Przed rozpoczęciem należy wziąć pod uwagę następujące inne czynności:

- Jeśli planujesz używać usługi urządzenie Data Box migracji zbiorczej: zapoznaj się z warunkami wstępnymi wdrożenia dla usługi [urządzenie Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planowanie końcowej Azure File Sync topologii: [planowanie wdrożenia Azure File Sync wdrożenia](file-sync-planning.md)
- Wybierz konta usługi Azure Storage, które będą przechowywać udziały plików, z których chcesz synchronizować. Upewnij się, że migracja zbiorcza odbywa się z tymczasowymi udziałami przejściowymi na tych samych kontach magazynu. Migrację zbiorczą można włączyć tylko przy użyciu końcowego i przejściowego udziału, który znajduje się na tym samym koncie magazynu.
- Migrację zbiorczą można wykorzystać tylko podczas tworzenia nowej relacji synchronizacji z lokalizacją serwera. Nie można włączyć migracji zbiorczej z istniejącą relacją synchronizacji.


## <a name="process-for-offline-data-transfer"></a>Proces transferu danych w trybie offline
Poniżej opisano sposób Azure File Sync w sposób zgodny z narzędziami migracji zbiorczej, takimi jak Azure Data Box:

![Diagram przedstawiający sposób Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Krok | Szczegóły |
|---|---------------------------------------------------------------------------------------|
| ![Krok 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Zamów urządzenie Data Box](../../databox/data-box-deploy-ordered.md). Rodzina urządzenie Data Box oferuje [kilka produktów spełniających](https://azure.microsoft.com/services/storage/databox/data) Twoje potrzeby. Po otrzymaniu konta urządzenie Data Box postępuj [](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) zgodnie z jego dokumentacją, aby skopiować dane do tej ścieżki UNC na stronie urządzenie Data Box: *\\<\> \<StorageAccountName_AzFile\> \<ShareName\> DeviceIPAddres.* W tym *miejscu ShareName* jest nazwą udziału przejściowego. Wyślij urządzenie Data Box z powrotem na platformę Azure. |
| ![Krok 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Poczekaj, aż pliki będą wyświetlane w udziałach plików platformy Azure, które zostały wybrane jako tymczasowe udziały przejściowe. *Nie należy włączać synchronizacji z tymi udziałami.* |
| ![Krok 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Utwórz nowy pusty udział dla każdego utworzonego urządzenie Data Box plików. Ten nowy udział powinien być na tym samym koncie magazynu co udział urządzenie Data Box magazynu. [Jak utworzyć nowy udział plików platformy Azure.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)</li><li>[Utwórz grupę synchronizacji](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) w usłudze synchronizacji magazynu. Odwołaj się do pustego udziału jako punktu końcowego w chmurze. Powtórz ten krok dla każdego urządzenie Data Box plików. [Skonfiguruj Azure File Sync](file-sync-deployment-guide.md).</li></ul> |
| ![Krok 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Dodaj katalog serwera na żywo jako punkt końcowy serwera](file-sync-deployment-guide.md#create-a-server-endpoint). W trakcie tego procesu określ, że pliki zostały przeniesione na platformę Azure, i odwołaj się do udziałów przejściowych. W razie potrzeby można włączyć lub wyłączyć obsługę warstw w chmurze. Podczas tworzenia punktu końcowego serwera na serwerze na żywo należy odwołać się do udziału przejściowego. W bloku **Dodawanie punktu końcowego** serwera w obszarze Transfer danych w trybie **offline** wybierz pozycję **Włączone,** a następnie wybierz udział przejściowy, który musi znajdować się na tym samym koncie magazynu co punkt końcowy w chmurze. W tym miejscu lista dostępnych udziałów jest filtrowana według konta magazynu i udziałów, które nie są jeszcze synchronizowane. Zrzut ekranu poniżej tej tabeli przedstawia sposób odwołania do udziału DataBox podczas tworzenia punktu końcowego serwera w Azure Portal. |
| ![Krok 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | Po dodaniu punktu końcowego serwera w poprzednim kroku dane zaczynają automatycznie przepływać z odpowiedniego źródła. W [sekcji Synchronizowanie udziału wyjaśniono,](#syncing-the-share) kiedy dane przepływają z udziału DataBox lub z systemu Windows Server |
| |

![Zrzut ekranu przedstawiający interfejs Azure Portal, który pokazuje, jak włączyć transfer danych w trybie offline podczas tworzenia nowego punktu końcowego serwera](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synchronizowanie udziału
Po utworzeniu punktu końcowego serwera rozpocznie się synchronizacja. Proces synchronizacji określa, czy każdy plik na serwerze również istnieje w przejściowym udziału, w którym urządzenie Data Box pliki. Jeśli plik istnieje, proces synchronizacji kopiuje plik z udziału przejściowego zamiast przekazywać go z serwera. Jeśli plik nie istnieje w udziałach przejściowych lub jeśli na serwerze lokalnym jest dostępna nowsza wersja, proces synchronizacji przekaże plik z serwera lokalnego.

Podczas synchronizowania udziału synchronizacja scali wszystkie brakujące atrybuty pliku, uprawnienia lub znaczniki czasu z wariantów plików na serwerze lokalnym, łącząc je z ich odpowiednikami plików z udziału DataBox. Gwarantuje to, że każdy plik i folder docierają ze wszystkimi możliwymi wiernościami plików w udziałach plików platformy Azure.

> [!IMPORTANT]
> Tryb migracji zbiorczej można włączyć tylko podczas tworzenia punktu końcowego serwera. Po nawiązyniu punktu końcowego serwera nie można zintegrować danych migrowanych zbiorczo z już synchronizowanych serwerów z przestrzenią nazw.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL i znaczniki czasu dla plików i folderów
Azure File Sync, że z serwera na żywo są synchronizowane pliki i foldery, nawet jeśli użyte narzędzie do migracji zbiorczej nie transportuje początkowo ACL. W związku z tym udział przejściowy nie musi zawierać żadnych adresów ACL dla plików i folderów. Po włączeniu funkcji migracji danych w trybie offline podczas tworzenia nowego punktu końcowego serwera wszystkie pliki ACL są synchronizowane na serwerze. Nowo utworzone i zmodyfikowane znaczniki czasu również są synchronizowane.

## <a name="shape-of-the-namespace"></a>Kształt przestrzeni nazw
Po włączeniu synchronizacji zawartość serwera określa kształt przestrzeni nazw. Jeśli pliki zostaną usunięte z serwera lokalnego po urządzenie Data Box migawki i migracji, te pliki nie przejdą do zsynchronizowanej przestrzeni nazw na żywo. Pozostają one w udziałach przejściowych, ale nie są kopiowane. Jest to konieczne, ponieważ synchronizacja zachowuje przestrzeń nazw zgodnie z serwerem na żywo. Migawka *urządzenie Data Box jest* po prostu platformą przejściowym do wydajnego kopiowania plików. Nie jest to urząd dla kształtu przestrzeni nazw na żywo.

## <a name="cleaning-up-after-bulk-migration"></a>Czyszczenie po migracji zbiorczej 
Gdy serwer ukończy synchronizację początkową przestrzeni nazw, urządzenie Data Box pliki zmigrowane zbiorczo używają przejściowego udziału plików. W bloku **Właściwości punktu końcowego serwera** w Azure Portal sekcji Transfer danych w trybie **offline** stan zmieni się z W toku **na** **Ukończono.** 

![Zrzut ekranu przedstawiający blok Właściwości punktu końcowego serwera, w którym znajdują się kontrolki stanu i wyłączania transferu danych w trybie offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Teraz możesz wyczyścić udział przejściowy, aby zaoszczędzić koszty:

1. W bloku **Właściwości punktu końcowego** serwera, gdy stan ma stan **Ukończono,** wybierz pozycję Wyłącz transfer danych **w trybie offline.**
2. Rozważ usunięcie udziału przejściowego, aby zaoszczędzić koszty. Udział przejściowy prawdopodobnie nie zawiera plików i folderów ACL, więc jest mało prawdopodobne, że będzie przydatny. Na potrzeby tworzenia kopii zapasowej punktu w czasie utwórz rzeczywistą migawkę [synchronizowania udziału plików platformy Azure.](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) Możesz skonfigurować [Azure Backup tworzenia migawek]( ../../backup/backup-afs.md) zgodnie z harmonogramem.

Wyłącz tryb transferu danych w trybie offline tylko wtedy, gdy stan to **Ukończono** lub gdy chcesz anulować z powodu nieprawidłowej konfiguracji. Jeśli wyłączysz tryb podczas wdrażania, rozpocznie się przekazywanie plików z serwera, nawet jeśli udział przejściowy jest nadal dostępny.

> [!IMPORTANT]
> Po wyłączeniu trybu transferu danych w trybie offline nie można go ponownie włączyć, nawet jeśli udział przejściowy z migracji zbiorczej jest nadal dostępny.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync wstępnie wstępnie wstępnie iniekcjowane pliki w chmurze

Jeśli masz pliki w udziałach plików platformy Azure w inny sposób niż dataBox — np. za pomocą narzędzia AzCopy, narzędzia RoboCopy z kopii zapasowej w chmurze lub dowolnej innej metody — nadal należy postępować zgodnie z procesem transferu danych w trybie [offline](#process-for-offline-data-transfer) opisanym w tym artykule. Wystarczy zignorować rozwiązanie DataBox, ponieważ metoda przenoszenia plików do chmury. Jednak najważniejszą sprawą jest zapewnienie, że nadal będziesz podążać za procesem dzielenia plików na udział przejściowy, *a* nie końcowy, Azure File Sync połączony udział.

> [!WARNING]
> **Postępuj zgodnie z procesem przemieszczania plików do udziału przejściowego, a** nie końcowego Azure File Sync połączonego udziału. Jeśli tak nie jest, mogą wystąpić konflikty plików (będą przechowywane obie wersje plików), a pliki usunięte na serwerze na żywo mogą wrócić, jeśli nadal istnieją w Twoim starszym, iniekcjonowym zestawie plików. Ponadto zmiany folderów będą scalane ze sobą, co bardzo trudno oddzielić przestrzeń nazw po takim błędzie.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia Azure File Sync wdrożenia](file-sync-planning.md)
- [Wdrażanie funkcji Azure File Sync](file-sync-deployment-guide.md)
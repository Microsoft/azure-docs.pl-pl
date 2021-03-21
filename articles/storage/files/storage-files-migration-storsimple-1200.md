---
title: Migracja urządzeń StorSimple 1200 do usługi Azure File Sync
description: Dowiedz się, jak przeprowadzić migrację urządzenia wirtualnego z serii StorSimple 1200 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882284"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migracja urządzeń StorSimple 1200 do usługi Azure File Sync

Seria 1200 StorSimple jest urządzeniem wirtualnym, które jest uruchamiane w lokalnym centrum danych. Możliwe jest Migrowanie danych z tego urządzenia do środowiska Azure File Syncowego. Azure File Sync to domyślna i strategiczna usługa platformy Azure, w ramach której można migrować urządzenia StorSimple.

Seria StorSimple 1200 osiągnie [koniec cyklu życia](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) w grudniu 2022.  Ważne jest, aby rozpocząć planowanie migracji najszybciej, jak to możliwe. Ten artykuł zawiera informacje na temat niezbędnej wiedzy i migracji w celu pomyślnej migracji do Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Firma Microsoft dokłada starań, aby pomóc klientom w migracji. Wyślij wiadomość e-mail AzureFilesMigration@microsoft . com dla niestandardowego planu migracji, a także pomoc podczas migracji.

Azure File Sync jest usługą firmy Microsoft w chmurze opartą na dwóch głównych składnikach:

* Synchronizacja plików i Obsługa warstw w chmurze.
* Udziały plików jako natywny magazyn na platformie Azure, do którego można uzyskać dostęp za pośrednictwem wielu protokołów, takich jak SMB i File REST. Udział plików platformy Azure jest porównywalny z udziałem plików w systemie Windows Server, który można natywnie zainstalować jako dysk sieciowy. Obsługuje ona ważne aspekty odtwarzania plików, takie jak atrybuty, uprawnienia i sygnatury czasowe. W przeciwieństwie do StorSimple, żadna aplikacja/usługa nie jest wymagana do interpretacji plików i folderów przechowywanych w chmurze. Idealnym i najbardziej elastycznym podejściem do przechowywania danych serwera plików ogólnego przeznaczenia oraz niektórych danych aplikacji w chmurze.

Ten artykuł koncentruje się na krokach migracji. Jeśli przed migracją chcesz dowiedzieć się więcej o Azure File Sync, zalecamy następujące artykuły:

* [Azure File Sync — przegląd](./storage-sync-files-planning.md "Omówienie")
* [Azure File Sync — Przewodnik wdrażania](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cele migracji

Celem jest zagwarantowanie integralności danych produkcyjnych, a także zagwarantowania dostępności. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Ścieżka migracji StorSimple 1200 do Azure File Sync

Do uruchomienia agenta Azure File Sync jest wymagany lokalny serwer systemu Windows. System Windows Server może być co najmniej serwerem 2012R2, ale najlepiej jest w systemie Windows Server 2019.

Istnieje wiele alternatywnych ścieżek migracji, które mogłyby utworzyć zbyt dużo artykułu, aby udokumentować wszystkie z nich i zilustrować, dlaczego są one ryzykowne lub wady w stosunku do trasy zalecamy jako najlepsze rozwiązanie w tym artykule.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Omówienie trasy migracji kroków opisanych poniżej w tym artykule.":::

Na poprzedniej ilustracji przedstawiono kroki, które są zgodne z sekcjami w tym artykule.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Krok 1: zainicjowanie obsługi administracyjnej lokalnego serwera i magazynu systemu Windows

1. Utwórz system Windows Server 2019 — co najmniej 2012R2 maszyny wirtualnej lub serwera fizycznego. Obsługiwany jest również klaster trybu failover systemu Windows Server.
2. Udostępnianie lub Dodawanie bezpośredniego dołączonego magazynu (DAS w porównaniu z serwerem NAS, co nie jest obsługiwane). Rozmiar magazynu systemu Windows Server musi być równy lub większy od rozmiaru dostępnej pojemności wirtualnego urządzenia StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Krok 2. Konfigurowanie magazynu systemu Windows Server

W tym kroku zamapujesz strukturę magazynu StorSimple (woluminy i udziały) na strukturę magazynu systemu Windows Server.
Jeśli planujesz wprowadzanie zmian w strukturze magazynu, co oznacza liczbę woluminów, skojarzenie folderów danych z woluminami lub strukturę podfolderów powyżej lub poniżej bieżących udziałów SMB/NFS, teraz jest to czas, który należy wziąć pod uwagę.
Zmiana struktury plików i folderów po skonfigurowaniu Azure File Sync jest niepomyślna i należy ją uniknąć.
W tym artykule przyjęto założenie, że jest używane mapowanie 1:1, dlatego należy uwzględnić zmiany w mapowaniu po wykonaniu kroków opisanych w tym artykule.

* Żadne dane produkcyjne nie powinny kończyć się na woluminie systemu Windows Server. Obsługa warstw w chmurze nie jest obsługiwana na woluminach systemowych. Jednak ta funkcja jest wymagana do migracji, a także do operacji ciągłych jako zastąpienie StorSimple.
* Zapewnij taką samą liczbę woluminów na serwerze Windows, jak na urządzeniu wirtualnym z systemem StorSimple 1200.
* Skonfiguruj wszystkie wymagane role i funkcje systemu Windows Server. Zalecamy rezygnację z aktualizacji systemu Windows Server, aby zapewnić bezpieczeństwo i aktualność systemu operacyjnego. Podobnie zalecamy włączenie Microsoft Update, aby zapewnić aktualność aplikacji firmy Microsoft, w tym agenta Azure File Sync.
* Nie należy konfigurować folderów ani udziałów przed przeczytaniem poniższych kroków.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Krok 3. wdrażanie pierwszego Azure File Sync zasobu chmury

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Krok 4. dopasowanie struktury lokalnego woluminu i folderu do Azure File Sync i zasobów udziału plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Krok 5. udostępnianie udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Krok 6. Konfigurowanie folderów docelowych systemu Windows Server

W poprzednich krokach uważasz wszystkie aspekty, które będą określać składniki topologii synchronizacji. Teraz można przygotować serwer do odbierania plików do przekazania.

Utwórz **wszystkie** foldery, które będą synchronizować każdy z nich z własnym udziałem plików platformy Azure.
Ważne jest, aby przestrzegać opisanej wcześniej struktury folderów. Jeśli na przykład użytkownik zdecydował się synchronizować wiele lokalnych udziałów SMB w pojedynczy udział plików platformy Azure, należy umieścić je w ramach wspólnego folderu głównego na woluminie. Utwórz teraz ten docelowy folder główny na woluminie.

Liczba zainicjowanych udziałów plików platformy Azure powinna być zgodna z liczbą folderów utworzonych w tym kroku i liczbą woluminów, które zostaną zsynchronizowane na poziomie głównym.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Krok 7. wdrażanie agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Krok 8. Konfigurowanie synchronizacji

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Upewnij się, że włączona jest obsługa warstw w chmurze!** Jest to wymagane, jeśli na serwerze lokalnym nie ma wystarczającej ilości miejsca do przechowywania łącznego rozmiaru danych w magazynie w chmurze StorSimple. Ustaw zasady dotyczące warstw tymczasowo dla migracji na 99% wolnego miejsca na woluminie.

Powtórz kroki tworzenia grupy synchronizacji i Dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure/lokalizacji serwera, które muszą zostać skonfigurowane do synchronizacji.

### <a name="step-9-copy-your-files"></a>Krok 9. Kopiowanie plików

Podstawowe podejście migracji to RoboCopy z urządzenia wirtualnego StorSimple do systemu Windows Server, a Azure File Sync do udziałów plików platformy Azure.

Uruchom pierwszą kopię lokalną w folderze docelowym systemu Windows Server:

* Zidentyfikuj pierwszą lokalizację na wirtualnym urządzeniu StorSimple.
* Zidentyfikuj pasujący folder na serwerze z systemem Windows, dla którego skonfigurowano już Azure File Sync.
* Rozpocznij kopiowanie przy użyciu RoboCopy

Następujące polecenie RoboCopy spowoduje odwołanie plików z usługi StorSimple Azure Storage do lokalnego StorSimple, a następnie przeniesienie ich do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje go z udziałami plików platformy Azure. W miarę jak wolumin lokalnego systemu Windows Server jest pełny, Obsługa warstw w chmurze zostanie rozpoczęta i pliki warstw, które zostały już pomyślnie zsynchronizowane. Obsługa warstw w chmurze spowoduje wygenerowanie wystarczającej ilości miejsca, aby kontynuować kopiowanie z urządzenia wirtualnego StorSimple. Obsługa warstw w chmurze jest sprawdzana raz na godzinę, aby zobaczyć, co zostało zsynchronizowane, i zwolnić miejsce na dysku, aby uzyskać dostęp do 99% wolnego miejsca na woluminie.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Tle

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Zezwala na uruchamianie wielowątkowości przez RoboCopy. Wartość domyślna to 8, wartość maksymalna to 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Wyprowadza stan do pliku dziennika jako UNICODE (Zastępuje istniejący dziennik).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Dane wyjściowe do okna konsoli. Używane w połączeniu z danymi wyjściowymi do pliku dziennika.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Uruchamia RoboCopy w tym samym trybie, w którym będzie używana aplikacja kopii zapasowej. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Umożliwia uruchamianie tego polecenia RoboCopy kilka razy, sekwencyjnie w tym samym miejscu docelowym/miejscu docelowym. Identyfikuje, co zostało wcześniej skopiowane, i pominie go. Tylko zmiany, dodatki i "*usunięcia*" zostaną przetworzone, które wystąpiły od momentu ostatniego uruchomienia. Jeśli polecenie nie było wcześniej uruchamiane, nic nie zostanie pominięte. Jest to doskonałe rozwiązanie dla lokalizacji źródłowych, które są nadal aktywnie używane i zmieniane.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      wierność kopiowania plików (wartość domyślna to/COPY: DAT), Copy flags: D = Data, A = Attributes, T = Timestamps, S = Security = NTFS list ACL, O = Owner info, U = informacje o inspekcji
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiuj wszystkie informacje o pliku (równoważne do/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      wierność kopiowania katalogów (wartość domyślna to/DCOPY: DA), Copy flags: D = Data, A = Attributes, T = Timestamps
   :::column-end:::
:::row-end:::

Po uruchomieniu polecenia RoboCopy po raz pierwszy Użytkownicy i aplikacje nadal uzyskują dostęp do plików i folderów StorSimple i mogą je zmienić. Istnieje możliwość, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (StorSimple) dodaje, zmienia lub usuwa plik, który nie zostanie przetworzony w bieżącym przebiegu RoboCopy. Jest to dokładne.

Pierwszy przebieg ma na celu przeniesienie zbiorczych danych z powrotem do lokalizacji lokalnej, do systemu Windows Server i kopii zapasowej w chmurze za pośrednictwem Azure File Sync. Może to zająć dużo czasu, w zależności od:

* przepustowość pobierania
* szybkość wycofywania usługi StorSimple w chmurze
* przepustowość przekazywania
* Liczba elementów (plików i folderów), które muszą być przetwarzane przez każdą usługę

Po zakończeniu pierwszego uruchomienia Uruchom polecenie ponownie.

Drugi raz, który zakończy się szybciej, ponieważ wymaga jedynie transportowania zmian, które wystąpiły od momentu ostatniego uruchomienia. Te zmiany prawdopodobnie StorSimple już lokalnie, ponieważ są one ostatnie. Pozwala to skrócić czas, ponieważ potrzeba odzyskania z chmury zostanie zmniejszona. W trakcie tego drugiego uruchomienia nadal można zbierać nowe zmiany.

Powtarzaj ten proces do momentu, gdy okaże się, że ilość czasu potrzebnego na zakończenie jest akceptowalnym przestojem.

Gdy rozważasz niedopuszczalne przestoje i przygotowasz lokalizację StorSimple w trybie offline, zrób to teraz: na przykład Usuń udział SMB, aby żaden użytkownik nie mógł uzyskać dostępu do folderu, lub wykonaj inne odpowiednie czynności, które uniemożliwiają zmianę zawartości w tym folderze na StorSimple.

Uruchom jeden ostatni RoboCopy zaokrąglenie. Spowoduje to pobranie wszelkich zmian, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestoju), mierząc, jak długo trwało wykonywanie poprzedniego uruchomienia.

Utwórz udział w folderze systemu Windows Server i ewentualnie Dostosuj wdrożenie systemu plików DFS-N, aby wskazywało na niego. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału, jak w udziale SMB StorSimple.

Zakończono Migrowanie udziału/grupy udziałów do wspólnego katalogu głównego lub woluminu. (W zależności od zamapowanego i podjęcie decyzji, które są potrzebne do przechodzenia do tego samego udziału plików platformy Azure).

Można spróbować uruchomić kilka z tych kopii równolegle. Zalecamy przetwarzanie zakresu jednego udziału plików platformy Azure w danym momencie.

> [!WARNING]
> Po przeniesieniu wszystkich danych z StorSimple do systemu Windows Server i zakończeniu migracji: Wróć do ***wszystkich***  grup synchronizacji w Azure Portal i Dostosuj wartość procentową ilości wolnego miejsca na woluminie w chmurze do bardziej dopasowanej do wykorzystania pamięci podręcznej, wypowiedz 20%. 

Zasady wolnego miejsca na woluminie w chmurze działają na poziomie woluminu z potencjalnie wieloma punktami końcowymi serwera. Jeśli zapomnisz o dostosowaniu wolnego miejsca w nawet jednym punkcie końcowym serwera, synchronizacja będzie nadal stosowała najbardziej restrykcyjną regułę i podejmie próbę utrzymania 99% wolnego miejsca na dysku, dzięki czemu lokalna pamięć podręczna nie będzie działała zgodnie z oczekiwaniami. O ile nie jest to cel, aby mieć tylko przestrzeń nazw dla woluminu, który zawiera tylko rzadko używane dane archiwalne.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej prawdopodobną przyczyną problemu może być uruchomienie polecenia *Robocopy po stronie* serwera systemu Windows. W takim przypadku szybkość pobierania jest prawdopodobnie lepsza niż szybkość przekazywania. Obsługa warstw w chmurze jest przeprowadzana co godzinę, aby wypróbować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany.

Zezwalaj na postęp synchronizacji i warstwowanie w chmurze Zwolnij miejsce na dysku. Można obserwować, że w Eksploratorze plików systemu Windows Server.

Gdy system Windows Server ma wystarczającą ilość dostępnej pojemności, polecenie spowoduje rozwiązanie problemu. Nic nie przerywa się w przypadku przechodzenia do tej sytuacji i można ją szybko przenieść do przodu. Jedyną konsekwencją jest to, że w przypadku ponownego uruchomienia polecenia.

Można również uruchamiać inne problemy Azure File Sync.
W takiej sytuacji, w jakim się znajdują, zapoznaj się z **przewodnikiem rozwiązywania problemów Azure File Sync łączem**.

## <a name="relevant-links"></a>Odpowiednie linki

Zawartość migracji:

* [Podręcznik migracji z serii StorSimple 8000](storage-files-migration-storsimple-8000.md)

Azure File Sync zawartość:

* [Omówienie usługi AFS](./storage-sync-files-planning.md)
* [Podręcznik wdrażania AFS](./storage-how-to-create-file-share.md)
* [Rozwiązywanie problemów z systemem AFS](storage-sync-files-troubleshoot.md)
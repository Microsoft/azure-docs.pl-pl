---
title: Seria 8000 StorSimple jako miejsce docelowe kopii zapasowej z Veeam | Microsoft Docs
description: Dowiedz się więcej o konfiguracji docelowej kopii zapasowej StorSimple z Veeam i najlepszymi rozwiązaniami dotyczącymi integracji obu rozwiązań.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: 052859e99ffd0082994d313508ebb6f0496d980b
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710349"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple jako miejsce docelowe kopii zapasowej za pomocą Veeam

## <a name="overview"></a>Omówienie

Azure StorSimple to hybrydowe rozwiązanie do magazynowania w chmurze firmy Microsoft. StorSimpleą w zakresie złożoności wykładniczego wzrostu danych przy użyciu konta usługi Azure Storage jako rozszerzenia rozwiązania lokalnego i automatycznego tworzenia warstw danych w magazynie lokalnym i w magazynie w chmurze.

W tym artykule omówiono integrację usługi StorSimple z usługą Veeam oraz najlepsze rozwiązania związane z integracją obu rozwiązań. Zawarto również zalecenia dotyczące konfigurowania Veeam, aby zapewnić najlepszą integrację z usługą StorSimple. Zachęcamy do Veeam najlepszych rozwiązań, architektów kopii zapasowych i administratorów w celu uzyskania najlepszego sposobu konfigurowania Veeam w celu spełnienia indywidualnych wymagań związanych z tworzeniem kopii zapasowych i umów dotyczących poziomu usług (umowy SLA).

Chociaż ilustrujemy kroki konfiguracji i kluczowe koncepcje, ten artykuł nie obejmuje konfiguracji krok po kroku ani przewodnika instalacji. Przyjęto założenie, że podstawowe składniki i infrastruktura działają w odpowiedniej kolejności i są gotowe do wspierania opisywanych koncepcji.

### <a name="who-should-read-this"></a>Kto powinien przeczytać ten element?

Informacje przedstawione w tym artykule są najbardziej przydatne dla administratorów kopii zapasowych, administratorów magazynu i architektów magazynu, którzy wiedzą o magazynie, systemie Windows Server 2012 R2, usługach w chmurze i Veeam.

### <a name="supported-versions"></a>Obsługiwane wersje

-   Veeam 9 i nowsze wersje
-   [StorSimple Update 3 i nowsze wersje](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Dlaczego StorSimple jako miejsce docelowe kopii zapasowej?

StorSimple to dobry wybór dla miejsca docelowego kopii zapasowej, ponieważ:

-   Jest to standardowy magazyn lokalny przeznaczony dla aplikacji do tworzenia kopii zapasowych, który ma być używany jako lokalizacja docelowa szybkiego tworzenia kopii zapasowej bez żadnych zmian. Można również użyć StorSimple do szybkiego przywrócenia ostatnich kopii zapasowych.
-   Jej Obsługa warstw w chmurze jest bezproblemowo zintegrowana z kontem magazynu w chmurze Azure, aby można było korzystać z ekonomicznej usługi Azure Storage.
-   Automatycznie zapewnia magazyn poza siedzibą firmy na potrzeby odzyskiwania po awarii.


## <a name="key-concepts"></a>Kluczowe pojęcia

Podobnie jak w przypadku dowolnego rozwiązania magazynu, staranna ocena wydajności magazynu rozwiązania, umowy SLA, współczynnika zmian i wzrostu pojemności ma kluczowe znaczenie dla sukcesu. Głównym pomysłem jest to, że dzięki wprowadzeniu warstwy chmurowej czasy dostępu i przepływność w chmurze odgrywają fundamentalną rolę w StorSimple.

StorSimple zaprojektowano w celu zapewnienia magazynu dla aplikacji, które działają na dobrze zdefiniowanym zestawie roboczym danych (gorącą dane). W tym modelu zestaw roboczy danych jest przechowywany w warstwach lokalnych, a pozostała część danych niepracujących/zimnych/zarchiwizowanych jest warstwą w chmurze. Ten model jest reprezentowany na poniższej ilustracji. Prawie płaski zielony wiersz reprezentuje dane przechowywane w warstwach lokalnych urządzenia StorSimple. Czerwona linia reprezentuje łączną ilość danych przechowywanych w rozwiązaniu StorSimple we wszystkich warstwach. Odstęp między płaską zieloną linią a wykładniczą czerwoną krzywą reprezentuje łączną ilość danych przechowywanych w chmurze.

Obsługa **warstw StorSimple** 
 ![ Diagram warstwowy StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

W tej architekturze należy zauważyć, że StorSimple idealnie nadaje się do działania jako miejsce docelowe kopii zapasowej. Możesz użyć StorSimple, aby:

-   Wykonaj najczęstsze przywracanie z lokalnego zestawu roboczego danych.
-   Korzystaj z chmury na potrzeby odzyskiwania po awarii i starszych danych, gdzie operacje przywracania są mniejsze.

## <a name="storsimple-benefits"></a>Korzyści z StorSimple

Usługa StorSimple zapewnia lokalne rozwiązanie, które bezproblemowo integruje się z Microsoft Azure, dzięki wykorzystaniu bezproblemowego dostępu do magazynu lokalnego i w chmurze.

Usługa StorSimple używa automatycznej obsługi warstw między urządzeniem lokalnym, które ma magazyn półprzewodnikowy (SSD) i Serial-Attached SCSI (SAS) oraz magazyn platformy Azure. Automatyczna obsługa warstw zachowuje często używane dane w warstwach SSD i SAS. Powoduje przeniesienie rzadko używanych danych do usługi Azure Storage.

StorSimple oferuje następujące korzyści:

-   Unikatowe algorytmy deduplikacji i kompresji korzystające z chmury w celu osiągnięcia niespotykaną poziomów deduplikacji
-   Wysoka dostępność
-   Replikacja geograficzna przy użyciu replikacji geograficznej platformy Azure
-   Integracja z Azure
-   Szyfrowanie danych w chmurze
-   Udoskonalone odzyskiwanie po awarii i zgodność

Mimo że StorSimple przedstawia dwa główne scenariusze wdrażania (podstawowa kopia zapasowa i pomocnicza lokalizacja docelowa kopii zapasowej), zasadniczo jest to proste, blokowe urządzenie magazynujące. StorSimple wykonuje całą kompresję i deduplikację. Bezproblemowo wysyła i pobiera dane między chmurą i systemem plików.

Aby uzyskać więcej informacji na temat StorSimple, zobacz [StorSimple 8000 Series: rozwiązanie hybrydowego magazynu w chmurze](storsimple-overview.md). Można również przejrzeć [specyfikacje serii StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Używanie urządzenia StorSimple jako miejsca docelowego kopii zapasowej jest obsługiwane tylko dla wersji StorSimple 8000 Update 3 i nowszych.

## <a name="architecture-overview"></a>Omówienie architektury

W poniższych tabelach przedstawiono wskazówki wstępne dotyczące modelu urządzenia do architektury.

**StorSimple pojemności dla magazynu lokalnego i w chmurze**

| Pojemność magazynu | 8100 | 8600 |
|---|---|---|
| Pojemność magazynu lokalnego | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Pojemność magazynu w chmurze | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Rozmiar magazynu nie zakłada deduplikacji ani kompresji.

**StorSimple pojemności dla podstawowych i pomocniczych kopii zapasowych**

| Scenariusz tworzenia kopii zapasowej  | Pojemność magazynu lokalnego  | Pojemność magazynu w chmurze  |
|---|---|---|
| Podstawowa kopia zapasowa  | Ostatnie kopie zapasowe przechowywane w magazynie lokalnym na potrzeby szybkiego odzyskiwania w celu spełnienia celu punktu odzyskiwania (RPO) | Historia kopii zapasowych (RPO) pasuje do pojemności chmury |
| Pomocnicza kopia zapasowa | Dodatkowa kopia kopii zapasowej danych może być przechowywana w pojemności chmury  | Brak  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako podstawowy element docelowy kopii zapasowej

W tym scenariuszu woluminy StorSimple są prezentowane dla aplikacji kopii zapasowej jako jedyne repozytorium dla kopii zapasowych. Na poniższej ilustracji przedstawiono architekturę rozwiązania, w której wszystkie kopie zapasowe używają StorSimple woluminów warstwowych do tworzenia kopii zapasowych i przywracania.

![StorSimple jako podstawowy Diagram logiczny tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Podstawowe etapy logicznej kopii zapasowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane na woluminach warstwowych StorSimple.
3.  Serwer kopii zapasowej aktualizuje bazę danych wykazu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Skrypt migawek wyzwala StorSimple Cloud Snapshot Manager (Rozpocznij lub Usuń).
5.  Serwer kopii zapasowej usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="primary-target-restore-logical-steps"></a>Etapy logiczne przywracania podstawowego elementu docelowego

1.  Serwer kopii zapasowej uruchamia przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej otrzymuje dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako pomocniczy cel kopii zapasowej

W tym scenariuszu woluminy StorSimple są używane głównie do długoterminowego przechowywania lub archiwizowania.

Na poniższej ilustracji przedstawiono architekturę, w której początkowe kopie zapasowe i przywracają miejsce docelowe woluminu o wysokiej wydajności. Te kopie zapasowe są kopiowane i archiwizowane na woluminie warstwowym StorSimple zgodnie z ustalonym harmonogramem.

Ważne jest, aby rozmiar woluminu o wysokiej wydajności mógł obsłużyć wymagania dotyczące pojemności i wydajności zasad przechowywania.

![StorSimple jako docelowy Diagram logiczny pomocniczej kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Pomocnicza procedura logiczna docelowej kopii zapasowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane do magazynu o wysokiej wydajności.
3.  Serwer kopii zapasowej aktualizuje bazę danych wykazu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Serwer kopii zapasowej kopiuje kopie zapasowe do StorSimple na podstawie zasad przechowywania.
5.  Skrypt migawek wyzwala StorSimple Cloud Snapshot Manager (Rozpocznij lub Usuń).
6.  Serwer kopii zapasowej usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="secondary-target-restore-logical-steps"></a>Kroki logiczne przywracania pomocniczego elementu docelowego

1.  Serwer kopii zapasowej uruchamia przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej otrzymuje dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wdrożenie rozwiązania wymaga wykonania trzech kroków:

1. Przygotuj infrastrukturę sieciową.
2. Wdróż Urządzenie StorSimple jako miejsce docelowe kopii zapasowej.
3. Wdróż Veeam.

Każdy krok został szczegółowo omówiony w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple to rozwiązanie zintegrowane z chmurą platformy Azure, StorSimple wymaga aktywnego i działającego połączenia z chmurą platformy Azure. To połączenie służy do wykonywania operacji, takich jak migawki chmury, zarządzanie danymi i transfer metadanych, oraz do warstwy starszej, mniej dostępnych danych do magazynu w chmurze platformy Azure.

Aby można było optymalnie wykonać rozwiązanie, zalecamy przestrzeganie następujących najlepszych rozwiązań w zakresie sieci:

-   Link łączący warstwę StorSimple z platformą Azure musi spełniać wymagania dotyczące przepustowości. Aby to osiągnąć, należy zastosować odpowiedni poziom Quality of Service (QoS) do swoich przełączników infrastruktury, aby pasował do celu punktu odzyskiwania i celu odzyskania (RTO) umowy SLA.
-   Maksymalna liczba opóźnień dostępu do magazynu obiektów blob platformy Azure powinna wynosić około 80 MS.

### <a name="deploy-storsimple"></a>Wdróż StorSimple

Aby uzyskać wskazówki krok po kroku dotyczące wdrażania StorSimple, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Wdróż Veeam

Aby zapoznać się z najlepszymi rozwiązaniami instalacji Veeam, zobacz temat [Veeam Backup & Best Practices](https://bp.veeam.expert/)i przeczytaj Podręcznik użytkownika w witrynie [Veeam Help Center (dokumentacja techniczna)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Skonfiguruj rozwiązanie

W tej sekcji przedstawiono przykłady konfiguracji. Poniższe przykłady i zalecenia ilustrują najważniejsze i podstawowe implementacje. Ta implementacja może nie dotyczyć bezpośrednio konkretnych wymagań dotyczących kopii zapasowych.

### <a name="set-up-storsimple"></a>Konfigurowanie StorSimple

| StorSimple zadania wdrażania  | Dodatkowe komentarze |
|---|---|
| Wdróż lokalne urządzenie StorSimple. | Obsługiwane wersje: Update 3 i nowsze wersje. |
| Włącz miejsce docelowe kopii zapasowej. | Te polecenia służą do włączania lub wyłączania trybu docelowego kopii zapasowej oraz pobierania stanu. Aby uzyskać więcej informacji, zobacz [zdalne nawiązywanie połączenia z urządzeniem StorSimple](storsimple-remote-connect.md).</br> Aby włączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -enable` . </br> Aby wyłączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -disable` . </br> Aby uzyskać bieżący stan ustawień trybu tworzenia kopii zapasowej: `Get-HCSBackupApplianceMode` . |
| Utwórz wspólny kontener woluminów dla woluminu, który przechowuje dane kopii zapasowej. Wszystkie dane w kontenerze woluminów są deduplikowane. | Kontenery woluminów StorSimple definiują domeny deduplikacji.  |
| Utwórz woluminy StorSimple. | Utwórz woluminy o rozmiarach jak najbliżej przewidywanego użycia, ponieważ rozmiar woluminu wpływa na czas trwania migawki w chmurze. Aby uzyskać informacje o sposobie rozmiaru woluminu, Przeczytaj o [zasadach przechowywania](#retention-policies).</br> </br> Użyj StorSimple woluminów warstwowych i zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** . </br> Używanie tylko woluminów przypiętych lokalnie nie jest obsługiwane. |
| Utwórz unikatowe zasady tworzenia kopii zapasowych StorSimple dla wszystkich woluminów docelowych kopii zapasowych. | Zasady tworzenia kopii zapasowych StorSimple definiują grupę spójności woluminu. |
| Wyłącz harmonogram, ponieważ migawki wygasną. | Migawki są wyzwalane jako operacje wykonywane po zakończeniu przetwarzania. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurowanie magazynu serwera kopii zapasowej hosta

Skonfiguruj magazyn serwera kopii zapasowej hosta zgodnie z następującymi wskazówkami:  

- Nie używaj woluminów łączonych (utworzonych przez Zarządzanie dyskami systemu Windows). Woluminy łączone nie są obsługiwane.
- Sformatuj woluminy przy użyciu systemu plików NTFS z rozmiarem jednostki alokacji 64 KB.
- Mapuj woluminy StorSimple bezpośrednio na serwer Veeam.
    - Używaj protokołu iSCSI dla serwerów fizycznych.


## <a name="best-practices-for-storsimple-and-veeam"></a>Najlepsze rozwiązania dotyczące StorSimple i Veeam

Skonfiguruj swoje rozwiązanie zgodnie z wytycznymi w poniższych sekcjach.

### <a name="operating-system-best-practices"></a>Najlepsze rozwiązania dotyczące systemu operacyjnego

- Wyłącz szyfrowanie i deduplikację systemu Windows Server dla systemu plików NTFS.
- Wyłącz funkcję defragmentacji systemu Windows Server na woluminach StorSimple.
- Wyłącz indeksowanie systemu Windows Server na woluminach StorSimple.
- Uruchom skanowanie antywirusowe na hoście źródłowym (nie na woluminach StorSimple).
- Wyłącz domyślną [konserwację systemu Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) w Menedżerze zadań. Wykonaj tę czynność w jeden z następujących sposobów:
  - Wyłącz konfiguratora konserwacji w systemie Windows Harmonogram zadań.
  - Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z programu Windows Sysinternals. Po pobraniu PsExec Uruchom program Windows PowerShell jako administrator, a następnie wpisz:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Najlepsze rozwiązania StorSimple

-   Upewnij się, że urządzenie StorSimple zostało zaktualizowane do [wersji Update 3 lub nowszej](storsimple-install-update-3.md).
-   Izoluj ruch związany z technologią iSCSI i chmurą. Używaj dedykowanych połączeń iSCSI dla ruchu między StorSimple a serwerem kopii zapasowych.
-   Upewnij się, że urządzenie StorSimple jest dedykowanym miejscem docelowym kopii zapasowej. Obciążenia mieszane nie są obsługiwane, ponieważ wpływają na RTO i cel punktu odzyskiwania.

### <a name="veeam-best-practices"></a>Najlepsze rozwiązania Veeam

-   Baza danych Veeam powinna być lokalna na serwerze i nie znajduje się na woluminie StorSimple.
-   W przypadku odzyskiwania po awarii wykonaj kopię zapasową bazy danych Veeam na woluminie StorSimple.
-   Obsługujemy Veeam pełne i przyrostowe kopie zapasowe dla tego rozwiązania. Zalecamy, aby nie używać syntetycznych i różnicowych kopii zapasowych.
-   Pliki danych kopii zapasowej powinny zawierać tylko dane dotyczące konkretnego zadania. Na przykład nie można dołączać nośników między różnymi zadaniami.
-   Wyłącz weryfikację zadań. W razie potrzeby weryfikacja powinna zostać zaplanowana po zakończeniu ostatniego zadania tworzenia kopii zapasowej. Ważne jest, aby zrozumieć, że to zadanie ma wpływ na okno kopii zapasowej.
-   Włącz wstępną alokację nośnika.
-   Upewnij się, że przetwarzanie równoległe jest włączone.
-   Wyłącz kompresję.
-   Wyłącz funkcję deduplikacji w zadaniu tworzenia kopii zapasowej.
-   Ustaw optymalizację na **element docelowy sieci LAN**.
-   Włącz opcję **Utwórz aktywną pełną kopię zapasową** (co 2 tygodnie).
-   W repozytorium kopii zapasowych Skonfiguruj **Używanie plików kopii zapasowej dla maszyny wirtualnej**.
-   Ustaw dla **każdego zadania użycie wielu strumieni przekazywania na** wartość **8** (maksymalna dozwolona liczba to 16). Dostosuj tę wartość w górę lub w dół na podstawie użycia procesora na urządzeniu StorSimple.

## <a name="retention-policies"></a>Zasady przechowywania

Jednym z najpopularniejszych typów zasad przechowywania kopii zapasowych są zasady dziadka, ojciec i syn (GFS). W zasadach GFS wykonywana jest przyrostowa kopia zapasowa codziennie, a pełne kopie zapasowe są wykonywane co tydzień i co miesiąc. Ta zasada powoduje sześć StorSimple woluminów warstwowych: jeden wolumin zawiera tygodniowe, miesięczne i roczne kopie zapasowe; pozostałe pięć woluminów zapisuje codzienne przyrostowe kopie zapasowe.

W poniższym przykładzie używamy obrotu GFS. W przykładzie założono następujące kwestie:

-   Używane są dane deduplikowane lub skompresowane.
-   Pełne kopie zapasowe są 1 TiB każdy.
-   Codzienne przyrostowe kopie zapasowe to 500 GiB każdy.
-   Cztery cotygodniowe kopie zapasowe są przechowywane przez miesiąc.
-   12 comiesięcznych kopii zapasowych jest przechowywanych przez rok.
-   Roczna kopia zapasowa jest przechowywana przez 10 lat.

W oparciu o powyższe założenia Utwórz wolumin warstwowy z 26 TiB StorSimple dla miesięcznych i rocznych kopii zapasowych. Utwórz wolumin warstwowy 5 TiB StorSimple dla każdego przyrostowego codziennego tworzenia kopii zapasowych.

| Przechowywanie typu kopii zapasowej | Rozmiar (TiB) | Mnożnik GFS\* | Całkowita pojemność (TiB)  |
|---|---|---|---|
| Tydzień pełny | 1 | 4  | 4 |
| Dzienne przyrosty | 0,5 | 20 (cykle równej liczbie tygodni miesięcznie) | 12 (2 dla dodatkowego przydziału) |
| Pełny miesięczny | 1 | 12 | 12 |
| Roczna pełna | 1  | 10 | 10 |
| GFS wymaganie |   | 38 |   |
| Dodatkowy przydział  | 4  |   | 42 łączny wymóg GFS  |

\* Mnożnik GFS to liczba kopii, które należy chronić i zachować, aby spełnić wymagania dotyczące zasad tworzenia kopii zapasowych.

## <a name="set-up-veeam-storage"></a>Konfigurowanie magazynu Veeam

### <a name="to-set-up-veeam-storage"></a>Aby skonfigurować magazyn Veeam

1.  W konsoli **Narzędzia**do tworzenia kopii zapasowych i replikacji programu Veeam przejdź do pozycji **infrastruktura kopii zapasowych**. Kliknij prawym przyciskiem myszy pozycję **repozytoria kopii zapasowych**, a następnie wybierz pozycję **Dodaj repozytorium kopii zapasowych**.

    ![Zrzut ekranu pokazujący konsolę zarządzania Veeam i podświetl opcję Dodaj repozytorium kopii zapasowych.](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  W oknie dialogowym **nowe repozytorium kopii zapasowych** wprowadź nazwę i opis repozytorium. Wybierz pozycję **Dalej**.

    ![Strona Veeam Management Console, nazwa i opis](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  W polu Typ wybierz pozycję **Microsoft Windows Server**. Wybierz serwer Veeam. Wybierz pozycję **Dalej**.

    ![Veeam Management Console, wybierz typ repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Aby określić **lokalizację**, Przeglądaj i wybierz wolumin. Zaznacz pole wyboru **Ogranicz maksymalną liczbę współbieżnych zadań do:** , a następnie ustaw wartość **4**. Gwarantuje to, że tylko cztery dyski wirtualne są przetwarzane współbieżnie podczas przetwarzania każdej maszyny wirtualnej. Wybierz przycisk **Zaawansowane** .

    ![Veeam Management Console, wybierz pozycję wolumin](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  W oknie dialogowym **Ustawienia zgodności magazynu** zaznacz pole wyboru **Użyj plików kopii zapasowej dla maszyny wirtualnej** .

    ![Veeam Management Console, ustawienia zgodności magazynu](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  W oknie dialogowym **nowe repozytorium kopii zapasowych** zaznacz pole wyboru **Włącz usługę NFS vPower na serwerze instalacji (zalecane)** . Wybierz pozycję **Dalej**.

    ![Zrzut ekranu pokazujący konsolę zarządzania Veeam, w której można dodać nowe repozytorium kopii zapasowej.](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Konsola zarządzania Veeam, Strona repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Repozytorium jest dodawane do serwera Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Skonfiguruj StorSimple jako podstawowy element docelowy kopii zapasowej

> [!IMPORTANT]
> Przywracanie danych z kopii zapasowej, która została przedzielona do chmury, odbywa się z szybkością chmury.

Poniższy rysunek przedstawia mapowanie typowego woluminu do zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe są mapowane na pełny dysk w sobotę, a przyrostowe kopie zapasowe są mapowane na dyski przyrostowe z poniedziałek do piątku. Wszystkie kopie zapasowe i przywracane są z woluminu warstwowego StorSimple.

![Diagram logiczny konfiguracji podstawowej lokalizacji docelowej kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Przykład StorSimple jako podstawowy element docelowy kopii zapasowej GFS Schedule

Oto przykład harmonogramu rotacji GFS przez cztery tygodnie, co miesiąc i rok:

| Częstotliwość/typ kopii zapasowej | Pełne | Przyrostowe (dni 1-5)  |   
|---|---|---|
| Co tydzień (tygodnie 1-4) | Sobota | Poniedziałek — piątek |
| Co miesiąc  | Sobota  |   |
| Rocznie | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Przypisywanie woluminów StorSimple do zadania tworzenia kopii zapasowej Veeam

W przypadku scenariusza cel głównej kopii zapasowej Utwórz codzienne zadanie przy użyciu podstawowego woluminu Veeam StorSimple. W przypadku scenariusza miejsce docelowe kopii zapasowej Utwórz codzienne zadanie przy użyciu bezpośredniego dołączonego magazynu (DAS), magazynu podłączonego do sieci (NAS) lub tylko wielu platform dysków (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Aby przypisać woluminy StorSimple do zadania tworzenia kopii zapasowej Veeam

1.  W konsoli kopia zapasowa Veeam i replikacja wybierz pozycję **kopia zapasowa & replikacja**. Kliknij prawym przyciskiem myszy pozycję **kopia zapasowa**, a następnie wybierz pozycję **VMware** lub **funkcji Hyper-V**w zależności od środowiska.

    ![Konsola zarządzania Veeam, nowe zadanie tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  W oknie dialogowym **nowe zadanie tworzenia kopii zapasowej** wprowadź nazwę i opis codziennego zadania tworzenia kopii zapasowej.

    ![Zrzut ekranu przedstawiający konsolę zarządzania Veeam, w której można dodać nazwę i opis.](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Wybierz maszynę wirtualną, do której ma zostać utworzona kopia zapasowa.

    ![Zrzut ekranu pokazujący konsolę zarządzania Veeamą, w której można wybrać maszynę wirtualną.](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Wybierz wartości, dla których chcesz utworzyć **kopię zapasową serwera proxy** i **kopii zapasowych**. Wybierz wartość **punktów przywracania, aby zachować dostęp do dysku** zgodnie z definicjami RPO i RTO dla danego środowiska w lokalnym magazynie dołączonym. Wybierz pozycję **Zaawansowane**.

    ![Konsola zarządzania Veeam, Nowa strona zadania tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. W oknie dialogowym **Ustawienia zaawansowane** na karcie **kopia zapasowa** wybierz pozycję **przyrostowe**. Upewnij się, że pole wyboru **Utwórz syntetyczne pełne kopie zapasowe** jest wyczyszczone. Zaznacz pole wyboru **Utwórz aktywne pełne kopie zapasowe okresowo** . W obszarze **aktywna pełna kopia zapasowa**zaznacz pole wyboru **co tydzień w wybranym** dniu dla soboty.

    ![Zrzut ekranu przedstawiający konsolę zarządzania Veeam, na przykład nową stronę ustawień zaawansowanych zadania tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na karcie **Magazyn** upewnij się, że pole wyboru **Włącz funkcję deduplikacji danych wbudowanych** jest wyczyszczone. Zaznacz pole wyboru **Wyklucz bloki wymiany plików** , a następnie zaznacz pole wyboru **Wyklucz bloki usuniętego pliku** . Ustaw **poziom kompresji** na **Brak**. W celu zapewnienia zrównoważonej wydajności i deduplikacji ustaw opcję **Optymalizacja magazynu** na wartość **docelową sieci LAN**. Wybierz przycisk **OK**.

    ![Strona ustawień zaawansowanych zadania tworzenia kopii zapasowej Veeam, konsola zarządzania](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Aby uzyskać informacje o Veeam deduplikacji i ustawieniach kompresji, zobacz [kompresja i Deduplikacja danych](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  W oknie dialogowym **Edytowanie zadania tworzenia kopii zapasowej** można zaznaczyć pole wyboru **Włącz przetwarzanie aplikacji obsługującej aplikacje** (opcjonalnie).

    ![Veeam Management Console, Nowa strona przetwarzania gościa zadania tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Ustaw harmonogram, który ma być uruchamiany raz dziennie, na czas, który można określić.

    ![Konsola zarządzania Veeam, Nowa strona harmonogramu zadań tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Skonfiguruj StorSimple jako pomocniczy cel kopii zapasowej

> [!NOTE]
> Dane są przywracane z kopii zapasowej, która została przeprowadzona w warstwach w chmurze.

W tym modelu trzeba dysponować nośnikiem magazynu (innym niż StorSimple), który będzie używany jako tymczasowa pamięć podręczna. Można na przykład użyć nadmiarowej macierzy dysków (RAID) do obsługi miejsca, wejścia/wyjścia (we/wy) i przepustowości. Zalecamy używanie macierzy RAID 5, 50 i 10.

Na poniższej ilustracji przedstawiono typowe krótkoterminowe przechowywanie danych lokalnych (na serwerze) i długoterminowe woluminy archiwalne przechowywania danych. W tym scenariuszu wszystkie kopie zapasowe są uruchamiane na woluminie RAID lokalnym (na serwerze). Te kopie zapasowe są okresowo duplikowane i archiwizowane na woluminie archiwum. Ważne jest, aby wielkość woluminu RAID (na serwerze) była możliwa do obsługi krótkoterminowej pojemności przechowywania i wymagań dotyczących wydajności.

![StorSimple jako docelowy Diagram logiczny pomocniczej kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Przykład StorSimple jako pomocnicze miejsce docelowe kopii zapasowej GFS

W poniższej tabeli przedstawiono sposób konfigurowania kopii zapasowych do uruchamiania na dyskach lokalnych i StorSimple. Obejmuje to indywidualne i łączne wymagania dotyczące pojemności.

| Typ i przechowywanie kopii zapasowych | Skonfigurowany magazyn | Rozmiar (TiB) | Mnożnik GFS | Całkowita pojemność \* (TIB) |
|---|---|---|---|---|
| Tydzień 1 (pełny i przyrostowy) |Dysk lokalny (krótkoterminowy)| 1 | 1 | 1 |
| StorSimple tygodni 2-4 |Dysk StorSimple (długoterminowy) | 1 | 4 | 4 |
| Pełny miesięczny |Dysk StorSimple (długoterminowy) | 1 | 12 | 12 |
| Roczna pełna |Dysk StorSimple (długoterminowy) | 1 | 1 | 1 |
|Wymaganie rozmiaru woluminów GFS |  |  |  | postanowienia|

\* Całkowita pojemność obejmuje 17 TiB z dysków StorSimple i 1 TiB lokalnego woluminu RAID.


### <a name="gfs-example-schedule"></a>Przykładowy harmonogram GFS

GFS rotacja co tydzień, co miesiąc i co rok

| Tydzień | Pełne | Przyrostowy dzień 1 | Przyrostowy dzień 2 | Przyrostowy dzień 3 | Przyrostowy dzień 4 | Przyrostowy dzień 5 |
|---|---|---|---|---|---|---|
| Tydzień 1 | Lokalny wolumin RAID  | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID |
| Tydzień 2 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Tydzień 3 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Tydzień 4 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Co miesiąc | StorSimple miesięcznie |   |   |   |   |   |
| Rocznie | StorSimple rocznie  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Przypisywanie woluminów StorSimple do zadania kopiowania Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Aby przypisać woluminy StorSimple do zadania kopiowania Veeam

1.  W konsoli kopia zapasowa Veeam i replikacja wybierz pozycję **kopia zapasowa & replikacja**. Kliknij prawym przyciskiem myszy pozycję **kopia zapasowa**, a następnie wybierz pozycję **VMware** lub **funkcji Hyper-V**w zależności od środowiska.

    ![Zrzut ekranu pokazujący konsolę zarządzania Veeam z opcjami programu VMware i funkcji Hyper-V, które można wybrać.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  W oknie dialogowym **nowe zadanie tworzenia kopii zapasowej** wprowadź nazwę i opis zadania.

    ![Zrzut ekranu pokazujący konsolę zarządzania Veeam, w której można wprowadzić nazwę i opis zadania.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Wybierz Maszyny wirtualne, które chcesz przetworzyć. Wybierz pozycję z kopii zapasowych, a następnie wybierz utworzoną wcześniej kopię zapasową.

    ![Konsola zarządzania Veeam, Nowa strona zadania tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Wyklucz obiekty z zadania kopii zapasowej, jeśli jest to potrzebne.

5.  Wybierz repozytorium kopii zapasowych i ustaw wartość dla **punktów przywracania, które mają być zachowane**. Pamiętaj, aby zaznaczyć pole wyboru **Zachowaj następujące punkty przywracania dla celów archiwizowania** . Zdefiniuj częstotliwość tworzenia kopii zapasowych, a następnie wybierz pozycję **Zaawansowane**.

    ![Zrzut ekranu pokazujący, gdzie definiujesz częstotliwość tworzenia kopii zapasowych.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Określ następujące ustawienia zaawansowane:

    * Na karcie **Obsługa** Wyłącz funkcję Ochrona przed uszkodzeniem na poziomie magazynu.

    ![Zrzut ekranu przedstawiający kartę Obsługa w konsoli zarządzania Veeam.](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na karcie **Magazyn** upewnij się, że deduplikacja i kompresja są wyłączone.

    ![Strona Zaawansowane ustawienia konsoli zarządzania Veeam, nowej kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Określ, że transfer danych jest bezpośredni.

8.  Zdefiniuj harmonogram okna kopii zapasowej zgodnie z potrzebami, a następnie Zakończ pracę kreatora.

Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych zadań](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Migawki w chmurze StorSimple

Migawki w chmurze StorSimple chronią dane, które znajdują się na urządzeniu StorSimple. Tworzenie migawek w chmurze jest równoznaczne z wysyłaniem lokalnych taśm kopii zapasowych do funkcji poza siedzibą firmy. W przypadku korzystania z magazynu geograficznie nadmiarowego platformy Azure tworzenie migawek w chmurze jest równoważne z wysyłaniem taśm kopii zapasowych do wielu lokacji. Jeśli zachodzi potrzeba przywrócenia urządzenia po awarii, można przenieść inne urządzenie StorSimple do trybu online i wykonać pracę w trybie failover. Po przejściu w tryb failover będziesz mieć dostęp do danych (w szybkościach chmury) od najnowszej migawki w chmurze.

W poniższej sekcji opisano, jak utworzyć krótki skrypt do uruchamiania i usuwania migawek w chmurze StorSimple podczas przetwarzania po wykonaniu kopii zapasowej.

> [!NOTE]
> Migawki, które są tworzone ręcznie lub programowo, nie są zgodne z zasadami wygasania migawek StorSimple. Te migawki należy ręcznie lub programowo usunąć.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchamianie i usuwanie migawek w chmurze za pomocą skryptu

> [!NOTE]
> Starannie Oceń zgodność i nieskutki przechowywania danych przed usunięciem migawki StorSimple. Więcej informacji o sposobach uruchamiania skryptu po wykonaniu kopii zapasowej znajduje się w dokumentacji Veeam.


### <a name="backup-lifecycle"></a>Cykl życia kopii zapasowej

![Diagram cyklu życia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Wymagania

-   Serwer, na którym działa skrypt, musi mieć dostęp do zasobów w chmurze platformy Azure.
-   Konto użytkownika musi mieć wymagane uprawnienia.
-   Zasady tworzenia kopii zapasowych StorSimple ze skojarzonymi woluminami StorSimple muszą zostać skonfigurowane, ale nie włączone.
-   Wymagana jest nazwa zasobu StorSimple, klucz rejestracji, nazwa urządzenia i identyfikator zasad kopii zapasowych.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Aby rozpocząć lub usunąć migawkę w chmurze

1. [Zainstalowanie programu Azure PowerShell](/powershell/azure/).
2. Pobierz i skonfiguruj [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skrypt programu PowerShell.
3. Na serwerze, na którym działa skrypt, uruchom program PowerShell jako administrator. Upewnij się, że skrypt został uruchomiony za pomocą programu, `-WhatIf $true` Aby zobaczyć, jakie zmiany wprowadzi skrypt. Po zakończeniu walidacji zakończono pomyślnie `-WhatIf $false` . Uruchom następujące polecenie:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Aby dodać skrypt do zadania tworzenia kopii zapasowej, Edytuj opcje zaawansowane zadania Veeam.

    ![Karta skryptów ustawień zaawansowanych kopii zapasowych Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Zalecamy uruchomienie zasad tworzenia kopii zapasowej migawek w chmurze StorSimple jako skryptu wykonywanego po przetworzeniu na końcu codziennego zadania tworzenia kopii zapasowej. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania środowiska aplikacji do tworzenia kopii zapasowych, aby pomóc Ci zaspokoić cel punktu odzyskiwania i RTO, zapoznaj się z architektem tworzenia kopii zapasowych.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako źródło przywracania

Przywraca z urządzenia StorSimple, jak przywraca z dowolnego blokowego urządzenia magazynującego. Przywracanie danych warstwowych do chmury odbywa się z szybkością chmury. W przypadku danych lokalnych przywracanie odbywa się na szybkości dysku lokalnego urządzenia.

Dzięki programowi Veeam można szybko, szczegółowe i odzyskiwać na poziomie plików za pośrednictwem StorSimple za pośrednictwem wbudowanych widoków Eksploratora w konsoli Veeam. Użyj Eksploratora Veeam do odzyskania poszczególnych elementów, takich jak wiadomości e-mail, obiekty Active Directory i elementy programu SharePoint z kopii zapasowych. Odzyskiwanie można wykonać bez przerwy w działaniu maszyny wirtualnej. Można również wykonywać odzyskiwanie do punktu w czasie dla baz danych Azure SQL Database i Oracle. Veeam i StorSimple umożliwiają szybkie i łatwe tworzenie procesu odzyskiwania na poziomie elementu z platformy Azure. Informacje o sposobie wykonywania przywracania znajdują się w dokumentacji Veeam:

- Dla [programu Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Dla [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Dla [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Dla [programu SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- W przypadku oprogramowania [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple trybu failover i odzyskiwania po awarii

> [!NOTE]
> W przypadku scenariuszy docelowych kopii zapasowych urządzenie w chmurze StorSimple nie jest obsługiwane jako element docelowy przywracania.

Awaria może być spowodowana przez różne czynniki. W poniższej tabeli wymieniono typowe scenariusze odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| Awaria urządzenia StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Zastąp urządzenie zakończone niepowodzeniem i przeprowadź [StorSimple tryb failover i odzyskiwanie po awarii](storsimple-device-failover-disaster-recovery.md). | Jeśli trzeba wykonać przywracanie po odzyskiwaniu urządzenia, do nowego urządzenia zostaną pobrane pełne zestawy robocze z danymi z chmury. Wszystkie operacje są z szybkością chmury. Proces ponownego skanowania indeksu i wykazu może spowodować, że wszystkie zestawy kopii zapasowych zostaną przeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych, co może być czasochłonnym procesem. |
| Awaria serwera Veeam | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Skompiluj ponownie serwer kopii zapasowej i wykonaj przywracanie bazy danych zgodnie z opisem w [centrum pomocy Veeam (dokumentacja techniczna)](https://www.veeam.com/documentation-guides-datasheets.html).  | Należy ponownie skompilować lub przywrócić serwer Veeam w lokacji odzyskiwania po awarii. Przywróć bazę danych do najnowszego punktu. Jeśli przywrócona baza danych Veeam nie jest zsynchronizowana z najnowszymi zadaniami tworzenia kopii zapasowych, wymagane jest indeksowanie i wykazanie. Ten proces ponownego skanowania indeksu i wykazu może spowodować, że wszystkie zestawy kopii zapasowych będą skanowane i pobrane z warstwy chmury do warstwy urządzenia lokalnego. Zwiększa to intensywnie czasochłonne. |
| Awaria lokacji, która powoduje utratę zarówno serwera kopii zapasowej, jak i StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Najpierw Przywróć StorSimple, a następnie Przywróć Veeam. | Najpierw Przywróć StorSimple, a następnie Przywróć Veeam. Jeśli trzeba wykonać przywracanie po odzyskiwaniu urządzenia, do nowego urządzenia zostaną pobrane wszystkie zestawy robocze z danymi z chmury. Wszystkie operacje są z szybkością chmury. |


## <a name="references"></a>Dokumentacja

Następujące dokumenty odwołują się do tego artykułu:

- [Konfiguracja wielościeżkowego we/wy StorSimple](storsimple-configure-mpio-windows-server.md)
- [Scenariusze magazynu: alokowanie elastyczne](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Korzystanie z dysków GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Skonfiguruj kopie w tle dla folderów udostępnionych](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobach [przywracania z zestawu kopii zapasowych](storsimple-restore-from-backup-set-u2.md).
- Dowiedz się więcej na temat wykonywania [trybu failover urządzeń i odzyskiwania po awarii](storsimple-device-failover-disaster-recovery.md).

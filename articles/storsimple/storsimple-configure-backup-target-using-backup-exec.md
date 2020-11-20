---
title: Seria 8000 StorSimple jako miejsce docelowe kopii zapasowej przy użyciu programu Backup Exec | Microsoft Docs
description: Opisuje konfigurację docelową StorSimple kopii zapasowej za pomocą programu Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: 66a1e22282864d0425173504735d6beb42b76ad7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967265"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple jako miejsce docelowe kopii zapasowej przy użyciu programu Backup Exec

## <a name="overview"></a>Omówienie

Azure StorSimple to hybrydowe rozwiązanie do magazynowania w chmurze firmy Microsoft. StorSimpleą w zakresie złożoności wykładniczego wzrostu danych przy użyciu konta usługi Azure Storage jako rozszerzenia rozwiązania lokalnego i automatycznego tworzenia warstw danych w magazynie lokalnym i w magazynie w chmurze.

W tym artykule omówiono integrację usługi StorSimple z Veritas Backup Exec i najlepszymi rozwiązaniami dotyczącymi integracji obu rozwiązań. Zawarto również zalecenia dotyczące konfigurowania narzędzia Backup Exec w celu zapewnienia najlepszej integracji z usługą StorSimple. Zachęcamy do korzystania z Veritas najlepszych rozwiązań, architektów kopii zapasowych i administratorów w celu uzyskania najlepszego sposobu konfigurowania usługi Backup Exec do spełnienia wymagań indywidualnych kopii zapasowych i umów dotyczących poziomu usług (umowy SLA).

Chociaż ilustrujemy kroki konfiguracji i kluczowe koncepcje, ten artykuł nie obejmuje konfiguracji krok po kroku ani przewodnika instalacji. Przyjęto założenie, że podstawowe składniki i infrastruktura działają w odpowiedniej kolejności i są gotowe do wspierania opisywanych koncepcji.

### <a name="who-should-read-this"></a>Kto powinien przeczytać ten element?

Informacje przedstawione w tym artykule są najbardziej przydatne dla administratorów kopii zapasowych, administratorów magazynu i architektów magazynu, którzy wiedzą o magazynie, systemie Windows Server 2012 R2, usługach w chmurze i usłudze Backup Exec.

## <a name="supported-versions"></a>Obsługiwane wersje

-   [Backup Exec 16 i nowsze wersje](https://www.veritas.com/content/support/en_US/article.100040087)
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
 ![ Diagram warstwowy StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

Aby uzyskać więcej informacji na temat StorSimple, zobacz [StorSimple 8000 Series: rozwiązanie hybrydowego magazynu w chmurze](storsimple-overview.md). Można również przejrzeć [specyfikacje serii StorSimple 8000](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Używanie urządzenia StorSimple jako miejsca docelowego kopii zapasowej jest obsługiwane tylko dla wersji StorSimple 8000 Update 3 i nowszych.

## <a name="architecture-overview"></a>Omówienie architektury

W poniższych tabelach przedstawiono wskazówki wstępne dotyczące modelu urządzenia do architektury.

**StorSimple pojemności dla magazynu lokalnego i w chmurze**

| Pojemność magazynu       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Pojemność magazynu lokalnego | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Pojemność magazynu w chmurze | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Rozmiar magazynu nie zakłada deduplikacji ani kompresji.

**StorSimple pojemności dla podstawowych i pomocniczych kopii zapasowych**

| Scenariusz tworzenia kopii zapasowej  | Pojemność magazynu lokalnego  | Pojemność magazynu w chmurze  |
|---|---|---|
| Podstawowa kopia zapasowa  | Ostatnie kopie zapasowe przechowywane w magazynie lokalnym na potrzeby szybkiego odzyskiwania w celu spełnienia celu punktu odzyskiwania (RPO) | Historia kopii zapasowych (RPO) pasuje do pojemności chmury |
| Pomocnicza kopia zapasowa | Dodatkowa kopia kopii zapasowej danych może być przechowywana w pojemności chmury  | Nie dotyczy  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako podstawowy element docelowy kopii zapasowej

W tym scenariuszu woluminy StorSimple są prezentowane dla aplikacji kopii zapasowej jako jedyne repozytorium dla kopii zapasowych. Na poniższej ilustracji przedstawiono architekturę rozwiązania, w której wszystkie kopie zapasowe używają StorSimple woluminów warstwowych do tworzenia kopii zapasowych i przywracania.

![StorSimple jako podstawowy Diagram logiczny tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple jako docelowy Diagram logiczny pomocniczej kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Wdróż kopię zapasową exec.

Każdy krok został szczegółowo omówiony w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple to rozwiązanie zintegrowane z chmurą platformy Azure, StorSimple wymaga aktywnego i działającego połączenia z chmurą platformy Azure. To połączenie służy do wykonywania operacji, takich jak migawki chmury, zarządzanie i transfer metadanych, oraz do warstwy starszej, mniejszej ilości danych dostępnych do magazynu w chmurze platformy Azure.

Aby można było optymalnie wykonać rozwiązanie, zalecamy przestrzeganie następujących najlepszych rozwiązań w zakresie sieci:

-   Link łączący warstwę StorSimple z platformą Azure musi spełniać wymagania dotyczące przepustowości. Aby to osiągnąć, Zastosuj wymagany poziom Quality of Service (QoS) do swoich przełączników infrastruktury, aby dopasować go do celu punktu odzyskiwania i celu odzyskania (RTO) umowy SLA.
-   Maksymalna liczba opóźnień dostępu do magazynu obiektów blob platformy Azure powinna wynosić około 80 MS.

### <a name="deploy-storsimple"></a>Wdróż StorSimple

Aby uzyskać wskazówki krok po kroku dotyczące wdrażania StorSimple, zobacz [wdrażanie lokalnego urządzenia StorSimple](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Wdróż kopię zapasową exec

Aby zapoznać się z najlepszymi rozwiązaniami instalacji tworzenia kopii zapasowych, zobacz [najlepsze rozwiązania dotyczące instalacji usługi Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>Skonfiguruj rozwiązanie

W tej sekcji przedstawiono przykłady konfiguracji. Poniższe przykłady i zalecenia ilustrują najważniejsze i podstawowe implementacje. Ta implementacja może nie dotyczyć bezpośrednio konkretnych wymagań dotyczących kopii zapasowych.

### <a name="set-up-storsimple"></a>Konfigurowanie StorSimple

| StorSimple zadania wdrażania  | Dodatkowe komentarze |
|---|---|
| Wdróż lokalne urządzenie StorSimple. | Obsługiwane wersje: Update 3 i nowsze wersje. |
| Włącz miejsce docelowe kopii zapasowej. | Te polecenia służą do włączania lub wyłączania trybu docelowego kopii zapasowej oraz pobierania stanu. Aby uzyskać więcej informacji, zobacz [zdalne nawiązywanie połączenia z urządzeniem StorSimple](./storsimple-8000-remote-connect.md).</br> Aby włączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -enable` . </br> Aby wyłączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -disable` . </br> Aby uzyskać bieżący stan ustawień trybu tworzenia kopii zapasowej: `Get-HCSBackupApplianceMode` . |
| Utwórz wspólny kontener woluminów dla woluminu, który przechowuje dane kopii zapasowej. Wszystkie dane w kontenerze woluminów są deduplikowane. | Kontenery woluminów StorSimple definiują domeny deduplikacji.  |
| Utwórz woluminy StorSimple. | Utwórz woluminy o rozmiarach jak najbliżej przewidywanego użycia, ponieważ rozmiar woluminu wpływa na czas trwania migawki w chmurze. Aby uzyskać informacje o sposobie rozmiaru woluminu, Przeczytaj o [zasadach przechowywania](#retention-policies).</br> </br> Użyj StorSimple woluminów warstwowych i zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** . </br> Używanie tylko woluminów przypiętych lokalnie nie jest obsługiwane. |
| Utwórz unikatowe zasady tworzenia kopii zapasowych StorSimple dla wszystkich woluminów docelowych kopii zapasowych. | Zasady tworzenia kopii zapasowych StorSimple definiują grupę spójności woluminu. |
| Wyłącz harmonogram, ponieważ migawki wygasną. | Migawki są wyzwalane jako operacje wykonywane po zakończeniu przetwarzania. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurowanie magazynu serwera kopii zapasowej hosta

Skonfiguruj magazyn serwera kopii zapasowej hosta zgodnie z następującymi wskazówkami:  

- Nie używaj woluminów łączonych (utworzonych przez Zarządzanie dyskami systemu Windows). Dyski łączone nie są obsługiwane.
- Sformatuj woluminy przy użyciu systemu plików NTFS z rozmiarem alokacji 64 KB.
- Mapuj woluminy StorSimple bezpośrednio na serwer programu Backup Exec.
    - Używaj protokołu iSCSI dla serwerów fizycznych.
    - Używaj dysków pass-through dla serwerów wirtualnych.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Najlepsze rozwiązania dotyczące StorSimple i tworzenia kopii zapasowych

Skonfiguruj swoje rozwiązanie zgodnie z wytycznymi w poniższych sekcjach.

### <a name="operating-system-best-practices"></a>Najlepsze rozwiązania dotyczące systemu operacyjnego

- Wyłącz szyfrowanie i deduplikację systemu Windows Server dla systemu plików NTFS.
- Wyłącz funkcję defragmentacji systemu Windows Server na woluminach StorSimple.
- Wyłącz indeksowanie systemu Windows Server na woluminach StorSimple.
- Uruchom skanowanie antywirusowe na hoście źródłowym (nie na woluminach StorSimple).
- Wyłącz domyślną [konserwację systemu Windows Server](/windows/win32/w8cookbook/automatic-maintenance) w Menedżerze zadań. Wykonaj tę czynność w jeden z następujących sposobów:
  - Wyłącz konfiguratora konserwacji w systemie Windows Harmonogram zadań.
  - Pobierz [PsExec](/sysinternals/downloads/psexec) z programu Windows Sysinternals. Po pobraniu PsExec Uruchom Azure PowerShell jako administrator, a następnie wpisz:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Najlepsze rozwiązania StorSimple

  -   Upewnij się, że urządzenie StorSimple zostało zaktualizowane do [wersji Update 3 lub nowszej](./index.yml).
  -   Izoluj ruch związany z technologią iSCSI i chmurą. Używaj dedykowanych połączeń iSCSI dla ruchu między StorSimple a serwerem kopii zapasowych.
  -   Upewnij się, że urządzenie StorSimple jest dedykowanym miejscem docelowym kopii zapasowej. Obciążenia mieszane nie są obsługiwane, ponieważ wpływają na RTO i cel punktu odzyskiwania.

### <a name="backup-exec-best-practices"></a>Najlepsze rozwiązania w zakresie tworzenia kopii zapasowych

-   Tworzenie kopii zapasowej musi być zainstalowane na lokalnym dysku serwera, a nie na woluminie StorSimple.
-   Ustaw maksymalną dozwoloną liczbę **operacji zapisu** w magazynie wykonywania kopii zapasowych.
    -   Ustaw blok magazyn kopii zapasowych **i rozmiar buforu** na 512 KB.
    -   Włącz **buforowany odczyt i zapis** w magazynie kopii zapasowych.
-   StorSimple obsługuje pełne i przyrostowe kopie zapasowe. Zalecamy, aby nie używać syntetycznych i różnicowych kopii zapasowych.
-   Pliki danych kopii zapasowej powinny zawierać tylko dane dla określonego zadania. Na przykład nie można dołączać nośników między różnymi zadaniami.
-   Wyłącz weryfikację zadania. W razie potrzeby weryfikacja powinna zostać zaplanowana po zakończeniu ostatniego zadania tworzenia kopii zapasowej. Ważne jest, aby zrozumieć, że to zadanie ma wpływ na okno kopii zapasowej.
-   Wybierz pozycję **Magazyn**  >  **Your disk**  >  **Details**  >  **Właściwości** szczegóły dysku. Wyłącz **wstępnie przydzielone miejsce na dysku**.

Aby zapoznać się z najnowszymi ustawieniami tworzenia kopii zapasowych i najlepszymi rozwiązaniami dotyczącymi wdrażania tych wymagań, zobacz [witrynę sieci Web firmy Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Zasady przechowywania

Jednym z najpopularniejszych typów zasad przechowywania kopii zapasowych są zasady dziadka, ojciec i syn (GFS). W zasadach GFS wykonywana jest przyrostowa kopia zapasowa codziennie, a pełne kopie zapasowe są wykonywane co tydzień i co miesiąc. Ta zasada powoduje sześć StorSimple woluminów warstwowych. Jeden wolumin zawiera cotygodniowe, comiesięczne i corocznie pełne kopie zapasowe. Pozostałe pięć woluminów zapisuje codzienne przyrostowe kopie zapasowe.

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

## <a name="set-up-backup-exec-storage"></a>Konfigurowanie magazynu tworzenia kopii zapasowych

### <a name="to-set-up-backup-exec-storage"></a>Aby skonfigurować magazyn tworzenia kopii zapasowych

1.  W konsoli zarządzania Backup Exec wybierz pozycję **Magazyn**  >  **Skonfiguruj** magazyn  >  **na dysku** magazynu  >  **dalej**.

    ![Konsola zarządzania Backup Exec, Konfigurowanie strony magazynu](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Wybierz pozycję **Disk Storage**, a następnie wybierz pozycję **dalej**.

    ![Konsola zarządzania Backup Exec, Wybieranie strony magazynu](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Wprowadź nazwę reprezentatywną, na przykład **zapełnienie** i opis. Wybierz pozycję **Dalej**.

    ![Strona zarządzania, nazwa i opis programu Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Wybierz dysk, na którym chcesz utworzyć urządzenie magazynujące dysk, a następnie wybierz przycisk **dalej**.

    ![Konsola zarządzania Backup Exec, Strona wyboru dysku magazynu](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Zwiększ liczbę operacji zapisu do **16**, a następnie wybierz przycisk **dalej**.

    ![Strona ustawień operacji tworzenia kopii zapasowych w konsoli administracyjnej](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**.

    ![Konsola zarządzania programu Backup Exec, strona podsumowania konfiguracji magazynu](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Na końcu każdego przypisywania woluminów Zmień ustawienia urządzenia magazynującego tak, aby odpowiadały one zalecanym [najlepszym praktykom StorSimple i Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Konsola zarządzania Backup Exec, Strona ustawień urządzenia magazynującego](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Powtórz kroki 1-7 do momentu zakończenia przypisywania woluminów StorSimple do programu Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Skonfiguruj StorSimple jako podstawowy element docelowy kopii zapasowej

> [!NOTE]
> Przywracanie danych z kopii zapasowej, która została przedzielona do chmury, odbywa się z szybkością chmury.

Poniższy rysunek przedstawia mapowanie typowego woluminu do zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe są mapowane na pełny dysk w sobotę, a przyrostowe kopie zapasowe są mapowane na Monday-Friday dysków przyrostowych. Wszystkie kopie zapasowe i przywracane są z woluminu warstwowego StorSimple.

![Diagram logiczny konfiguracji podstawowej lokalizacji docelowej kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Przykład StorSimple jako podstawowy element docelowy kopii zapasowej GFS Schedule

Oto przykład harmonogramu rotacji GFS przez cztery tygodnie, co miesiąc i rok:

| Częstotliwość/typ kopii zapasowej | Pełne | Przyrostowe (dni 1-5)  |   
|---|---|---|
| Co tydzień (tygodnie 1-4) | Sobota | Monday-Friday |
| Miesięczne  | Sobota  |   |
| Roczne | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Przypisywanie woluminów StorSimple do zadania tworzenia kopii zapasowej wykonywania kopii zapasowej

W następującej kolejności przyjęto założenie, że program Backup Exec i host docelowy są skonfigurowane zgodnie z zaleceniami agenta Backup Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Aby przypisać woluminy StorSimple do zadania tworzenia kopii zapasowej wykonywania kopii zapasowej

1.  W konsoli zarządzania Backup **exec wybierz pozycję Utwórz** kopię zapasową  >  **Backup**  >  **na dysku**.

    ![Tworzenie kopii zapasowej konsoli zarządzania programu, wybieranie hosta, kopii zapasowej i tworzenie kopii zapasowej na dysku](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  W oknie dialogowym **właściwości definicji kopii zapasowej** w obszarze **kopia zapasowa** wybierz pozycję **Edytuj**.

    ![Konsola zarządzania programu Backup Exec, okno dialogowe właściwości definicji kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Skonfiguruj pełne i przyrostowe kopie zapasowe, tak aby spełniały wymagania dotyczące punktu odzyskiwania i RTO, i są zgodne z Veritas Best Practices.

4.  W oknie dialogowym **Opcje tworzenia kopii zapasowej** wybierz pozycję **Magazyn**.

    ![Konsola zarządzania programu Backup Exec, okno dialogowe magazyn opcji kopii zapasowych](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Przypisz odpowiednie woluminy StorSimple do harmonogramu tworzenia kopii zapasowych.

    > [!NOTE]
    > **Kompresja** i **typ szyfrowania** mają ustawioną **wartość Brak**.

6.  W obszarze **Weryfikuj** zaznacz pole wyboru nie **Weryfikuj danych dla tego zadania** . Użycie tej opcji może mieć wpływ na obsługę warstw StorSimple.

    > [!NOTE]
    > Proces defragmentacji, indeksowania i weryfikacji w tle ma negatywny wpływ na warstwę StorSimple.

    ![Tworzenie kopii zapasowej — Konsola zarządzania, opcje tworzenia kopii zapasowej Weryfikuj ustawienia](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Po skonfigurowaniu pozostałej części opcji tworzenia kopii zapasowej w celu spełnienia wymagań wybierz pozycję **OK** , aby zakończyć.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Skonfiguruj StorSimple jako pomocniczy cel kopii zapasowej

> [!NOTE]
>Dane są przywracane z kopii zapasowej, która została przeprowadzona w warstwach w chmurze.

W tym modelu trzeba dysponować nośnikiem magazynu (innym niż StorSimple), który będzie używany jako tymczasowa pamięć podręczna. Można na przykład użyć nadmiarowej macierzy dysków (RAID) do obsługi miejsca, wejścia/wyjścia (we/wy) i przepustowości. Zalecamy używanie macierzy RAID 5, 50 i 10.

Na poniższej ilustracji przedstawiono typowe krótkoterminowe przechowywanie danych lokalnych (na serwerze) i długoterminowe przechowywanie woluminów archiwów. W tym scenariuszu wszystkie kopie zapasowe są uruchamiane na woluminie RAID lokalnym (na serwerze). Te kopie zapasowe są okresowo duplikowane i archiwizowane na woluminie archiwów. Ważne jest, aby wielkość woluminu RAID (na serwerze) była możliwa do obsługi krótkoterminowej pojemności przechowywania i wymagań dotyczących wydajności.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Przykład StorSimple jako pomocnicze miejsce docelowe kopii zapasowej GFS

![StorSimple jako docelowy Diagram logiczny pomocniczej kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

W poniższej tabeli przedstawiono sposób konfigurowania kopii zapasowych do uruchamiania na dyskach lokalnych i StorSimple. Obejmuje to indywidualne i łączne wymagania dotyczące pojemności.

### <a name="backup-configuration-and-capacity-requirements"></a>Wymagania dotyczące konfiguracji i pojemności kopii zapasowych

| Typ i przechowywanie kopii zapasowych | Skonfigurowany magazyn | Rozmiar (TiB) | Mnożnik GFS | Całkowita pojemność \* (TIB) |
|---|---|---|---|---|
| Tydzień 1 (pełny i przyrostowy) |Dysk lokalny (krótkoterminowy)| 1 | 1 | 1 |
| StorSimple tygodni 2-4 |Dysk StorSimple (długoterminowy) | 1 | 4 | 4 |
| Pełny miesięczny |Dysk StorSimple (długoterminowy) | 1 | 12 | 12 |
| Roczna pełna |Dysk StorSimple (długoterminowy) | 1 | 1 | 1 |
|Wymaganie rozmiaru woluminów GFS |  |  |  | postanowienia|

\* Całkowita pojemność obejmuje 17 TiB z dysków StorSimple i 1 TiB lokalnego woluminu RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Przykładowy harmonogram GFS: GFS rotacja co tydzień, co miesiąc i co rok

| Tydzień | Pełne | Przyrostowy dzień 1 | Przyrostowy dzień 2 | Przyrostowy dzień 3 | Przyrostowy dzień 4 | Przyrostowy dzień 5 |
|---|---|---|---|---|---|---|
| Tydzień 1 | Lokalny wolumin RAID  | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID |
| Tydzień 2 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Tydzień 3 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Tydzień 4 | StorSimple tygodni 2-4 |   |   |   |   |   |
| Miesięczne | StorSimple miesięcznie |   |   |   |   |   |
| Roczne | StorSimple rocznie  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Przypisywanie woluminów StorSimple do archiwum tworzenia kopii zapasowych i zadania deduplikacji

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Aby przypisać woluminy StorSimple do archiwum tworzenia kopii zapasowych i zadania duplikowania

1.  W konsoli zarządzania Backup Exec kliknij prawym przyciskiem myszy zadanie, które chcesz zarchiwizować na woluminie StorSimple, a następnie wybierz pozycję **właściwości definicji kopii zapasowej**  >  **Edytuj**.

    ![Konsola zarządzania programu Backup Exec, karta właściwości definicji kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Wybierz pozycję **Dodaj**  >  **powielony etap do**  >  **edycji** dysku.

    ![Konsola zarządzania Backup Exec, Dodawanie etapu](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  W oknie dialogowym **Opcje duplikowane** wybierz wartości, które mają być używane jako **Źródło** i **harmonogram**.

    ![Konsola zarządzania programu Backup Exec, właściwości definicji kopii zapasowych i opcje duplikowania](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Z listy rozwijanej **Magazyn** Wybierz wolumin StorSimple, w którym ma być przechowywane dane zadanie archiwum.

    ![Zrzut ekranu pokazujący listę, w której należy wybrać pozycję Magazyn.](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Wybierz opcję **Weryfikuj**, a następnie zaznacz pole wyboru **nie Weryfikuj danych dla tego zadania** .

    ![Zrzut ekranu pokazujący, gdzie wybierasz opcję nie Weryfikuj danych dla tego zadania.](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Wybierz przycisk **OK**.

    ![Zrzut ekranu, na którym są wyświetlane właściwości definicji kopii zapasowej.](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  W kolumnie **kopia zapasowa** Dodaj nowy etap. Dla źródła użyj **przyrostowej**. Dla elementu docelowego wybierz wolumin StorSimple, w którym jest archiwizowane zadanie tworzenia kopii zapasowej. Powtórz kroki 1-6.

## <a name="storsimple-cloud-snapshots"></a>Migawki w chmurze StorSimple

Migawki w chmurze StorSimple chronią dane, które znajdują się na urządzeniu StorSimple. Tworzenie migawek w chmurze jest równoznaczne z wysyłaniem lokalnych taśm kopii zapasowych do funkcji poza siedzibą firmy. W przypadku korzystania z magazynu geograficznie nadmiarowego platformy Azure tworzenie migawek w chmurze jest równoważne z wysyłaniem taśm kopii zapasowych do wielu lokacji. Jeśli zachodzi potrzeba przywrócenia urządzenia po awarii, można przenieść inne urządzenie StorSimple do trybu online i wykonać pracę w trybie failover. Po przejściu w tryb failover będziesz mieć dostęp do danych (w szybkościach chmury) od najnowszej migawki w chmurze.

W poniższej sekcji opisano, jak utworzyć krótki skrypt do uruchamiania i usuwania migawek w chmurze StorSimple podczas przetwarzania po wykonaniu kopii zapasowej.

> [!NOTE]
> Migawki, które są tworzone ręcznie lub programowo, nie są zgodne z zasadami wygasania migawek StorSimple. Te migawki należy ręcznie lub programowo usunąć.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchamianie i usuwanie migawek w chmurze za pomocą skryptu

> [!NOTE]
> Starannie Oceń zgodność i nieskutki przechowywania danych przed usunięciem migawki StorSimple. Aby uzyskać więcej informacji na temat sposobu uruchamiania skryptu wykonywanego po wykonaniu kopii zapasowej, zobacz [dokumentację dotyczącą tworzenia kopii zapasowych](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Cykl życia kopii zapasowej

![Diagram cyklu życia kopii zapasowej](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Dodaj skrypt do zadania tworzenia kopii zapasowej w usłudze Backup Exec, edytując opcje zadania wstępnego przetwarzania i przetwarzania danych kopii zapasowej.

   ![Kopia zapasowa konsoli programu exec, opcje tworzenia kopii zapasowej, karty poleceń przednich i po zakończeniu przetwarzania](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Zalecamy uruchomienie zasad tworzenia kopii zapasowej migawek w chmurze StorSimple jako skryptu wykonywanego po przetworzeniu na końcu codziennego zadania tworzenia kopii zapasowej. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania środowiska aplikacji do tworzenia kopii zapasowych, aby pomóc Ci zaspokoić cel punktu odzyskiwania i RTO, zapoznaj się z architektem tworzenia kopii zapasowych.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako źródło przywracania

Przywraca z urządzenia StorSimple, jak przywraca z dowolnego blokowego urządzenia magazynującego. Przywracanie danych warstwowych do chmury odbywa się z szybkością chmury. W przypadku danych lokalnych przywracanie odbywa się na szybkości dysku lokalnego urządzenia. Informacje o sposobach wykonywania przywracania znajdują się w dokumentacji dotyczącej tworzenia kopii zapasowych. Zalecamy, aby zachować zgodność z najlepszymi rozwiązaniami w zakresie przywracania kopii zapasowych.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple trybu failover i odzyskiwania po awarii

> [!NOTE]
> W przypadku scenariuszy docelowych kopii zapasowych urządzenie w chmurze StorSimple nie jest obsługiwane jako element docelowy przywracania.

Awaria może być spowodowana przez różne czynniki. W poniższej tabeli wymieniono typowe scenariusze odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| Awaria urządzenia StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Zastąp urządzenie zakończone niepowodzeniem i przeprowadź [StorSimple tryb failover i odzyskiwanie po awarii](./storsimple-8000-device-failover-disaster-recovery.md). | Jeśli trzeba wykonać przywracanie po odzyskiwaniu urządzenia, do nowego urządzenia zostaną pobrane pełne zestawy robocze z danymi z chmury. Wszystkie operacje są z szybkością chmury. Proces indeksowania i ponownego skanowania wykazu może spowodować, że wszystkie zestawy kopii zapasowych zostaną przeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych, co może być czasochłonnym procesem. |
| Błąd serwera programu Backup Exec | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Skompiluj ponownie serwer kopii zapasowej i wykonaj przywracanie bazy danych zgodnie z opisem w temacie [jak wykonać ręczną kopię zapasową i przywrócić bazę danych Backup Exec (BEDB)](http://www.veritas.com/docs/000041083). | Należy ponownie skompilować lub przywrócić serwer Backup Exec w lokacji odzyskiwania po awarii. Przywróć bazę danych do najnowszego punktu. Jeśli przywrócona baza danych Backup Exec nie jest zsynchronizowana z najnowszymi zadaniami tworzenia kopii zapasowych, wymagane jest indeksowanie i wykazanie. Ten proces ponownego skanowania indeksu i wykazu może spowodować, że wszystkie zestawy kopii zapasowych będą skanowane i pobrane z warstwy chmury do warstwy urządzenia lokalnego. Zwiększa to intensywnie czasochłonne. |
| Awaria lokacji, która powoduje utratę zarówno serwera kopii zapasowej, jak i StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Najpierw Przywróć StorSimple, a następnie Przywróć kopię zapasową exec. | Najpierw Przywróć StorSimple, a następnie Przywróć kopię zapasową exec. Jeśli trzeba wykonać przywracanie po odzyskiwaniu urządzenia, do nowego urządzenia zostaną pobrane wszystkie zestawy robocze z danymi z chmury. Wszystkie operacje są z szybkością chmury. |

## <a name="references"></a>Dokumentacja

Następujące dokumenty odwołują się do tego artykułu:

- [Konfiguracja wielościeżkowego we/wy StorSimple](./storsimple-8000-configure-mpio-windows-server.md)
- [Scenariusze magazynu: alokowanie elastyczne](/windows-hardware/drivers/storage/thin-provisioning)
- [Korzystanie z dysków GPT](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Skonfiguruj kopie w tle dla folderów udostępnionych](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobach [przywracania z zestawu kopii zapasowych](./storsimple-8000-restore-from-backup-set-u2.md).
- Dowiedz się więcej na temat wykonywania [trybu failover urządzeń i odzyskiwania po awarii](./storsimple-8000-device-failover-disaster-recovery.md).
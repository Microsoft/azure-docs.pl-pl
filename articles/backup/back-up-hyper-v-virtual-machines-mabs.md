---
title: Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą serwera usługi MAB
description: Ten artykuł zawiera procedury tworzenia kopii zapasowych i odzyskiwania maszyn wirtualnych przy użyciu programu Microsoft Azure Backup Server (serwera usługi MAB).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509510"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą Azure Backup Server

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V przy użyciu serwera Microsoft Azure Backup (serwera usługi MAB).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

SERWERA usługi MAB może tworzyć kopie zapasowe maszyn wirtualnych działających na serwerach hostów funkcji Hyper-V w następujących scenariuszach:

- **Maszyny wirtualne z magazynem lokalnym lub podłączonym bezpośrednio** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych na autonomicznych serwerach hosta funkcji Hyper-V mających lokalną lub podłączoną bezpośrednio pamięć masową. Na przykład: dysk twardy, urządzenie sieci magazynowania (SAN) lub urządzenie magazynu podłączonego do sieci (NAS). Na wszystkich hostach musi być zainstalowany agent ochrony serwera usługi MAB.

- **Maszyny wirtualne w klastrze z magazynem CSV** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych w klastrze funkcji Hyper-V z woluminem udostępnionym klastra (CSV). Agent ochrony serwera usługi MAB jest instalowany na każdym węźle klastra.

## <a name="host-versus-guest-backup"></a>Tworzenie kopii zapasowej hosta i gościa

SERWERA usługi MAB może wykonywać kopie zapasowe maszyn wirtualnych funkcji Hyper-V na poziomie hosta lub gościa. Na poziomie hosta agent ochrony serwera usługi MAB jest instalowany na serwerze lub klastrze hosta funkcji Hyper-V i chroni całe maszyny wirtualne i pliki danych uruchomione na tym hoście.   Na poziomie gościa Agent jest instalowany na każdej maszynie wirtualnej i chroni obciążenie obecne na tym komputerze.

Obie metody mają swoje zalety i wady:

- Tworzenie kopii zapasowych na poziomie hosta jest elastyczne, ponieważ działają niezależnie od typu systemu operacyjnego działającego na maszynach gościa i nie wymagają instalacji agenta ochrony serwera usługi MAB na każdej maszynie wirtualnej. W przypadku wdrożenia kopii zapasowej na poziomie hosta możliwe jest odzyskanie całej maszyny wirtualnej lub plików i folderów (odzyskiwanie na poziomie elementu).

- Tworzenie kopii zapasowej na poziomie gościa jest przydatne, jeśli chcesz chronić określone obciążenia działające na maszynie wirtualnej. Na poziomie hosta możliwe jest odzyskanie całej maszyny wirtualnej lub określonych plików, ale nie zapewnia to odzyskiwania w kontekście określonej aplikacji. Na przykład w celu odzyskania określonych elementów programu SharePoint z kopii zapasowej maszyny wirtualnej należy wykonać kopię zapasową tej maszyny wirtualnej na poziomie gościa. Jeśli chcesz chronić dane przechowywane na dyskach przekazujących, użyj kopii zapasowej na poziomie gościa. Przekazywanie umożliwia maszynie wirtualnej bezpośredni dostęp do urządzenia magazynującego i nie przechowuje danych woluminu wirtualnego w pliku VHD.

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

SERWERA usługi MAB wykonuje kopię zapasową za pomocą usługi VSS w następujący sposób. Kroki znajdujące się w tym opisie są ponumerowane w celu zwiększenia przejrzystości.

1. Aparat synchronizacji oparty na blokach serwera usługi MAB tworzy początkową kopię chronionej maszyny wirtualnej i gwarantuje, że kopia maszyny wirtualnej jest kompletna i spójna.

2. Po utworzeniu i sprawdzeniu kopii początkowej serwera usługi MAB używa składnika zapisywania usługi VSS funkcji Hyper-V do przechwytywania kopii zapasowych. Składnik zapisywania usługi VSS zawiera zestaw bloków dysków spójnych z danymi, które są synchronizowane z serwerem serwera usługi MAB. Takie podejście umożliwia korzystanie z "pełnej kopii zapasowej" przy użyciu serwera serwera usługi MAB, jednocześnie minimalizując dane kopii zapasowej, które muszą być przesyłane przez sieć.

3. Agent ochrony serwera usługi MAB na serwerze z uruchomioną funkcją Hyper-V korzysta z istniejących interfejsów API funkcji Hyper-V, aby określić, czy chroniona maszyna wirtualna również obsługuje usługę VSS.

   - Jeśli maszyna wirtualna spełnia wymagania kopii zapasowej online i ma zainstalowany składnik usług integracji funkcji Hyper-V, to element zapisujący usług VSS funkcji Hyper-V rekursywnie przekazuje żądania usług VSS do wszystkich procesów obsługujących takie żądania na maszynie wirtualnej. Ta operacja jest wykonywana bez instalacji agenta ochrony serwera usługi MAB na maszynie wirtualnej. Dzięki tym cyklicznym żądaniom składnik zapisywania usługi VSS funkcji Hyper-V może się upewnić, że operacje zapisu na dysku są zsynchronizowane, aby przechwycić migawkę usługi VSS bez utraty danych.

     Składnik usług integracji funkcji Hyper-V wywołuje składnik zapisywania usługi VSS funkcji Hyper-V w ramach usług kopiowania woluminów w tle (VSS) w maszynach wirtualnych, aby sprawdzić, czy dane aplikacji są spójne.

   - Jeśli maszyna wirtualna nie jest zgodna z wymaganiami dotyczącymi kopii zapasowych online, program serwera usługi MAB automatycznie używa interfejsów API funkcji Hyper-V, aby wstrzymać maszynę wirtualną przed przechwyceniem plików danych.

4. Po zsynchronizowaniu początkowej bazowej kopii maszyny wirtualnej z serwerem serwera usługi MAB wszystkie zmiany wprowadzone w zasobach maszyny wirtualnej są przechwytywane w nowym punkcie odzyskiwania. Punkt odzyskiwania przedstawia stan spójności maszyny wirtualnej w określonym czasie. Przechwytywanie w ramach punktu odzyskiwania może odbywać się co najmniej raz dziennie. Po utworzeniu nowego punktu odzyskiwania program serwera usługi MAB korzysta z replikacji na poziomie bloku w połączeniu ze składnikiem zapisywania usługi VSS funkcji Hyper-V, aby określić, które bloki zostały zmienione na serwerze z uruchomioną funkcją Hyper-V po utworzeniu ostatniego punktu odzyskiwania. Te bloki danych zostają następnie przetransferowane na serwer serwera usługi MAB i są stosowane do repliki chronionych danych.

5. Serwer serwera usługi MAB używa usługi VSS na woluminach, które obsługują dane odzyskiwania, aby można było korzystać z wielu kopii w tle. Każda z tych kopii w tle zapewnia osobne zadania odzyskiwania. Punkty odzyskiwania usługi VSS są przechowywane na serwerze serwera usługi MAB. Kopia tymczasowa wprowadzona na serwerze z uruchomioną funkcją Hyper-V jest przechowywana tylko na czas trwania synchronizacji serwera usługi MAB.

>[!NOTE]
>
>Począwszy od systemu Windows Server 2016 wirtualne dyski twarde funkcji Hyper-V mają wbudowane śledzenie zmian znane jako odporne śledzenie zmian (RCT). SERWERA usługi MAB korzysta z RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnych kontroli spójności w scenariuszach, takich jak awarie maszyn wirtualnych. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. SERWERA usługi MAB v3 optymalizuje użycie sieci i magazynu przez transfer tylko zmienionych danych podczas kontroli spójności.

## <a name="backup-prerequisites"></a>Wymagania wstępne dotyczące kopii zapasowej

Poniżej przedstawiono wymagania wstępne dotyczące tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą serwera usługi MAB:

|Wymaganie wstępne|Szczegóły|
|------------|-------|
|Wymagania wstępne serwera usługi MAB|— Jeśli chcesz przeprowadzić odzyskiwanie na poziomie elementu dla maszyn wirtualnych (odzyskanie plików, folderów, woluminów), musisz zainstalować rolę funkcji Hyper-V na serwerze serwera usługi MAB.  Jeśli chcesz tylko odzyskać maszynę wirtualną, a nie na poziomie elementu, rola nie jest wymagana.<br />— Można chronić maksymalnie 800 maszyn wirtualnych z 100 GB każdego na jednym serwerze serwera usługi MAB i zezwalać na wiele serwerów serwera usługi MAB obsługujących większe klastry.<br />-SERWERA usługi MAB wyklucza plik stronicowania z przyrostowych kopii zapasowych w celu zwiększenia wydajności kopii zapasowej maszyny wirtualnej.<br />-SERWERA usługi MAB może utworzyć kopię zapasową serwera lub klastra funkcji Hyper-V w tej samej domenie co serwer serwera usługi MAB lub w domenie podrzędnej lub zaufanej. Jeśli chcesz utworzyć kopię zapasową funkcji Hyper-V w grupie roboczej lub niezaufanej domenie, musisz skonfigurować uwierzytelnianie. W przypadku pojedynczego serwera funkcji Hyper-V można użyć uwierzytelniania NTLM lub certyfikatu. W przypadku klastra można używać tylko uwierzytelniania certyfikatów.<br />— Przy tworzeniu kopii zapasowej danych maszyny wirtualnej na dyskach przekazujących nie jest obsługiwane korzystanie z kopii zapasowych na poziomie hosta. W tym scenariuszu zalecamy użycie kopii zapasowej na poziomie hosta do wykonywania kopii zapasowych plików VHD i kopii zapasowej na poziomie gościa w celu utworzenia kopii zapasowej innych danych, które nie są widoczne na hoście.<br />   — Można utworzyć kopię zapasową maszyn wirtualnych przechowywanych na deduplikowanych woluminach.|
|Wymagania wstępne maszyny wirtualnej funkcji Hyper-V|— Wersja składników integracji uruchamiana na maszynie wirtualnej powinna być taka sama jak wersja hosta funkcji Hyper-V. <br />— Dla każdej kopii zapasowej maszyny wirtualnej będzie wymagane wolne miejsce na woluminie obsługującym pliki wirtualnego dysku twardego, aby dostarczyć funkcji Hyper-V wystarczającą ilość miejsca na dyski różnicowe (AVHD) podczas wykonywania kopii zapasowej. Ilość miejsca musi wynosić co najmniej równą czasowi **wstępnemu obliczenia współczynnik zmian rozmiaru dysku w programie \* \* kopia zapasowa** . Jeśli używasz wielu kopii zapasowych w klastrze, konieczna będzie obliczona w ten sposób wystarczająca ilość miejsca do magazynowania dysków AVHD dla poszczególnych maszyn wirtualnych.<br />— Aby utworzyć kopię zapasową maszyn wirtualnych znajdujących się na serwerach hosta funkcji Hyper-V z systemem Windows Server 2012 R2, maszyna wirtualna powinna mieć określony kontroler SCSI, nawet jeśli nie jest połączony z żadnymi elementami. (W usłudze Kopia zapasowa online systemu Windows Server 2012 R2, host funkcji Hyper-V instaluje nowy wirtualny dysk twardy w maszynie wirtualnej, a następnie Odinstalowuje go później. Może to obsługiwać tylko kontroler SCSI i dlatego jest wymagany do tworzenia kopii zapasowych online maszyny wirtualnej.  Bez tego ustawienia zostanie wygenerowane zdarzenie o IDENTYFIKATORze 10103 podczas próby utworzenia kopii zapasowej maszyny wirtualnej.|
|Wymagania wstępne systemu Linux|— Można utworzyć kopię zapasową maszyn wirtualnych z systemem Linux za pomocą serwera usługi MAB. Obsługiwane są tylko migawki zgodne z plikami.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem CSV|— Na potrzeby magazynu CSV należy zainstalować na serwerze funkcji Hyper-V dostawcę sprzętowego usługi kopiowania woluminów w tle (VSS). W sprawie dostawcy sprzętowego usługi VSS skontaktuj się z dostawcą swojej sieci magazynowania (SAN).<br />— Jeśli pojedynczy węzeł zostanie nieoczekiwanie zamknięty w klastrze CSV, serwera usługi MAB przeprowadzi sprawdzanie spójności dla maszyn wirtualnych, które były uruchomione w tym węźle.<br />— Jeśli potrzebujesz uruchomić ponownie serwer funkcji Hyper-V z włączoną funkcją szyfrowania dysków BitLocker w klastrze CSV, musisz uruchomić sprawdzenie spójności dla maszyn wirtualnych funkcji Hyper-V.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem SMB|-Włącz funkcję automatycznego instalowania na serwerze z uruchomioną funkcją Hyper-V w celu włączenia ochrony maszyny wirtualnej.<br />   — Wyłącz odciążanie przy użyciu technologii TCP Chimney.<br />— Upewnij się, że wszystkie konta machine$ funkcji Hyper-V mają pełne uprawnienia w określonych udziałach zdalnych plików SMB.<br />-Upewnij się, że ścieżka pliku dla wszystkich składników maszyny wirtualnej podczas odzyskiwania do lokalizacji alternatywnej jest krótsza niż 260 znaków. W przeciwnym razie odzyskiwanie może zakończyć się pomyślnie, ale funkcja Hyper-V nie będzie mogła zainstalować maszyny wirtualnej.<br />— Następujące scenariusze nie są obsługiwane:<br />     Wdrożenia, w których niektóre składniki maszyny wirtualnej znajdują się na woluminach lokalnych, a niektóre składniki znajdują się na woluminach zdalnych; adres IPv4 lub IPv6 dla serwera plików lokalizacji magazynu oraz odzyskiwanie maszyny wirtualnej na komputerze, który używa zdalnych udziałów SMB.<br />— Należy włączyć usługę agenta VSS serwera plików na każdym serwerze SMB — Dodaj ją w obszarze **Dodaj role i funkcje,**  >  **Wybierz pozycję Role serwera**  >  **plik i magazyn** usługi plików usług plików serwer plików usługi  >    >    >  **agenta VSS usługi**.|

## <a name="back-up-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych

1. Skonfiguruj [serwer serwera usługi MAB](backup-azure-microsoft-azure-backup.md) i [Magazyn](backup-mabs-add-storage.md). Podczas konfigurowania magazynu użyj następujących wytycznych dotyczących pojemności magazynu.
   - Średni rozmiar maszyny wirtualnej — 100 GB
   - Liczba maszyn wirtualnych na serwer serwera usługi MAB — 800
   - Całkowity rozmiar 800 maszyn wirtualnych — 80 TB
   - Wymagane miejsce na magazyn kopii zapasowych — 80 TB

2. Skonfiguruj agenta ochrony serwera usługi MAB na serwerze funkcji Hyper-V lub w węzłach klastra funkcji Hyper-V. Jeśli wykonujesz kopię zapasową na poziomie gościa, Zainstaluj agenta na maszynach wirtualnych, dla których chcesz utworzyć kopię zapasową na poziomie gościa.

3. W konsoli administratora serwera usługi MAB wybierz pozycję **Ochrona**  >  **Utwórz grupę ochrony** , aby otworzyć kreatora **tworzenia nowej grupy ochrony** .

4. Na stronie **Wybierz członków grupy** wybierz maszyny wirtualne, które chcesz chronić za pomocą serwerów hostów funkcji Hyper-V, na których się znajdują. Zalecane jest umieszczenie wszystkich maszyn wirtualnych, które będą miały te same zasady ochrony, w jednej grupie ochrony. W celu efektywnego wykorzystania miejsca włącz wspólną lokalizację. Przekazywanie danych do wspólnej lokalizacji umożliwia umieszczanie danych pochodzących z różnych grup ochrony w tym samym magazynie dyskowym lub taśmowym, tak aby różne źródła danych miały jedną replikę i wolumin punktu odzyskiwania.

5. Na stronie **Wybierz metodę ochrony danych** podaj nazwę grupy ochrony. Wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku** oraz opcję **Chcę uzyskać ochronę online** , aby tworzyć kopie zapasowe danych na platformie Azure za pomocą usługi Kopia zapasowa Azure.

6. W obszarze **Określ Short-Term cele**  >  **Zakres przechowywania** Określ, jak długo mają być przechowywane dane dysku. W obszarze **częstotliwość synchronizacji** Określ, jak często mają być uruchamiane przyrostowe kopie zapasowe danych. Zamiast określać interwał tworzenia przyrostowych kopii zapasowych, możesz włączyć opcję **Bezpośrednio przed punktem odzyskiwania**. Po włączeniu tego ustawienia serwera usługi MAB będzie uruchamiać ekspresową pełną kopię zapasową tuż przed każdym zaplanowanym punktem odzyskiwania.

    > [!NOTE]
    >
    >W przypadku ochrony obciążeń aplikacji punkty odzyskiwania są tworzone zgodnie z opcją Częstotliwość synchronizacji, przy założeniu, że aplikacja obsługuje przyrostowe kopie zapasowe. Jeśli tak nie jest, serwera usługi MAB uruchamia ekspresową pełną kopię zapasową zamiast przyrostowej kopii zapasowej i tworzy punkty odzyskiwania zgodnie z harmonogramem ekspresowego tworzenia kopii zapasowych.

7. Na stronie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów przydzielone danej grupie ochrony.

   **Łączny rozmiar danych** to rozmiar danych, których kopia zapasowa ma zostać utworzona, oraz miejsce na dysku, które ma **zostać zainicjowane w systemie serwera usługi MAB** , to miejsce, które serwera usługi MAB zaleca dla grupy ochrony. SERWERA usługi MAB wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Opcje wielkości kopii zapasowej można jednak edytować w obszarze **Szczegóły przydziału dysku**. W przypadku obciążeń wybierz z menu rozwijanego preferowany magazyn. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Zajęte miejsce to ilość serwera usługi MAB magazynu sugerująca dodanie do woluminu, co pozwala na płynne wykonywanie kopii zapasowych w przyszłości.

8. Na stronie **Wybierz metodę tworzenia repliki** określ sposób, w jaki ma zostać wykonana początkowa replikacja danych w grupie ochrony. Jeśli wybierzesz **automatyczną replikację za pośrednictwem sieci**, zalecamy wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych Rozważ **Ręczne** wybranie, które wymaga replikacji danych w trybie offline przy użyciu nośnika wymiennego.

9. Na stronie **Opcje sprawdzania spójności** wybierz odpowiedni sposób automatyzacji sprawdzania spójności. Można włączyć uruchamianie sprawdzania tylko wtedy, gdy dane są niespójne, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony i wybierając polecenie **Przeprowadź sprawdzanie spójności**.

    Po utworzeniu grupy ochrony następuje replikacja początkowa danych zgodnie z wybraną metodą. Po replikacji początkowej tworzone są poszczególne kopie zapasowe zgodnie z ustawieniami grupy ochrony. Aby odzyskać kopię zapasową danych, należy pamiętać o następujących kwestiach:

## <a name="back-up-replica-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych repliki

Jeśli serwera usługi MAB jest uruchomiony w systemie Windows Server 2012 R2 lub nowszym, można utworzyć kopię zapasową maszyn wirtualnych repliki. Jest to przydatne z kilku powodów:

**Ogranicza wpływ tworzenia kopii zapasowej na obciążenie pracą** — wykonywanie kopii zapasowej maszyny wirtualnej powoduje pewne nadmiarowe obciążenie podczas tworzenia migawki. Przeciążanie procesu tworzenia kopii zapasowej do dodatkowej lokacji zdalnej powoduje, że uruchomione obciążenie nie ma już znaczenia dla operacji tworzenia kopii zapasowej. Ma to zastosowanie tylko do wdrożeń, w których kopia zapasowa jest przechowywana w lokalizacji zdalnej. Na przykład możesz korzystać z codziennych kopii zapasowych i przechowywać dane lokalnie w celu zapewnienia szybkiego przywracania, ale co miesiąc lub co kwartał tworzyć kopie zapasowe z maszyn wirtualnych replik przechowywanych zdalnie w celu przechowywania długoterminowego.

**Oszczędza przepustowość** — w typowych wdrożeniach w zdalnym oddziale lub siedzibie firmy potrzebne jest zapewnienie odpowiedniej przepustowości do transferu danych kopii zapasowych między lokacjami. Jeśli tworzysz strategię replikacji i pracy awaryjnej, oprócz opracowania strategii wykonywania kopii zapasowej danych możesz zmniejszyć ilość nadmiarowych danych przesyłanych w sieci. Wykonując kopię zapasową danych maszyny wirtualnej repliki, a nie podstawowe, oszczędzasz obciążenie związane z wysyłaniem kopii zapasowych danych za pośrednictwem sieci.

**Umożliwia wykonywanie kopii zapasowych dostawcy usług hostingowych** — hostowanego centrum danych można użyć jako lokalizacji repliki, co powoduje, że pomocnicze centrum danych nie jest potrzebne. W takim przypadku umowa SLA dostawcy usług hostingowych wymaga spójnej kopii zapasowej maszyn wirtualnych repliki.

Maszyna wirtualna repliki jest wyłączona do momentu zainicjowania trybu failover, a usługa VSS nie może zagwarantować kopii zapasowej maszyny wirtualnej repliki spójnej na poziomie aplikacji. W związku z tym kopia zapasowa maszyny wirtualnej repliki będzie tylko spójna w razie awarii. Jeśli nie można zagwarantować spójności na poziomie awarii, tworzenie kopii zapasowej zakończy się niepowodzeniem, a taka sytuacja może wystąpić w kilku przypadkach:

- Maszyna wirtualna repliki nie jest w dobrej kondycji i jest w stanie krytycznym.

- Maszyna wirtualna repliki jest ponownie synchronizowana (w stanie ponownej synchronizacji w toku lub w stanie konieczności wykonania ponownej synchronizacji).

- Początkowa replikacja maszyny wirtualnej między lokalizacją główną a pomocniczą jest w toku lub jest oczekiwana.

- Dzienniki. HRL są stosowane do maszyny wirtualnej repliki lub poprzednia czynność zastosowania dzienników. HRL na dysku wirtualnym nie powiodła się lub została anulowana lub przerwana.

- Trwa migracja lub praca w trybie failover maszyny wirtualnej repliki

## <a name="recover-backed-up-virtual-machines"></a>Odzyskiwanie kopii zapasowych maszyn wirtualnych

Gdy możesz odzyskać kopię zapasową maszyny wirtualnej, użyj Kreatora odzyskiwania, aby wybrać maszynę wirtualną i określony punkt odzyskiwania. Aby otworzyć Kreatora odzyskiwania i odzyskać maszynę wirtualną:

1. W konsoli administratora serwera usługi MAB wpisz nazwę maszyny wirtualnej lub rozwiń listę chronionych elementów i wybierz maszynę wirtualną, którą chcesz odzyskać.

2. W okienku **punkty odzyskiwania dla** w kalendarzu wybierz dowolną datę, aby wyświetlić dostępne punkty odzyskiwania. Następnie w okienku **Ścieżka** wybierz punkt odzyskiwania, którego chcesz użyć w Kreatorze odzyskiwania.

3. Z menu **Akcje** wybierz polecenie **Odzyskaj** , aby otworzyć Kreatora odzyskiwania.

    Wybrana maszyna wirtualna i punkt odzyskiwania są wyświetlane na ekranie **Przegląd wybranego elementu do odzyskania**. Wybierz opcję **Dalej**.

4. Na ekranie **Wybieranie typu odzyskiwania** wybierz miejsce, w którym chcesz przywrócić dane, a następnie wybierz pozycję **dalej**.

    - **Odzyskaj do oryginalnego wystąpienia**: gdy odzyskasz do oryginalnego wystąpienia, oryginalny wirtualny dysk twardy i wszystkie skojarzone punkty kontrolne zostaną usunięte. SERWERA usługi MAB odzyskuje dysk VHD i inne pliki konfiguracji do oryginalnej lokalizacji za pomocą składnika zapisywania usługi VSS funkcji Hyper-V. Po zakończeniu procesu odzyskiwania maszyny wirtualne nadal zapewniają dużą dostępność.
        Aby odzyskiwanie mogło się odbyć, musi być dostępna grupa zasobów. Jeśli grupa nie jest dostępna, wykonaj odzyskiwanie do lokalizacji alternatywnej, a następnie przełącz maszynę wirtualną w tryb wysokiej dostępności.

    - **Odzyskaj jako maszynę wirtualną do dowolnego hosta**: serwera usługi MAB obsługuje odzyskiwanie do lokalizacji alternatywnej (ALR), które zapewnia bezproblemowe odzyskiwanie chronionej maszyny wirtualnej funkcji Hyper-v do innego hosta funkcji Hyper-v niezależnie od architektury procesora. Maszyny wirtualne funkcji Hyper-V odzyskiwane do węzła klastra nie będą miały dużej dostępności. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.
    
        >[!NOTE]
        >W przypadku wybrania oryginalnego hosta zachowanie jest takie samo jak **odzyskiwanie do oryginalnego wystąpienia**. Oryginalny wirtualny dysk twardy i wszystkie skojarzone punkty kontrolne zostaną usunięte.

    - **Kopiuj do folderu sieciowego**: serwera usługi MAB obsługuje odzyskiwanie na poziomie elementu (ILR), które umożliwia odzyskiwanie plików, folderów, woluminów i wirtualnych dysków twardych (VHD) z kopii zapasowej na poziomie hosta maszyn wirtualnych funkcji Hyper-V do udziału sieciowego lub woluminu na serwerze chronionym serwera usługi MAB. Aby można było przeprowadzić odzyskiwanie na poziomie elementu, Agent ochrony serwera usługi MAB nie musi być zainstalowany w ramach gościa. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.

5. W obszarze **Określ opcje odzyskiwania** Skonfiguruj opcje odzyskiwania i wybierz pozycję **dalej**:

    - W przypadku odzyskiwania maszyny wirtualnej z niską przepustowością wybierz pozycję **Modyfikuj** , aby włączyć **ograniczanie użycia przepustowości sieci**. Po włączeniu opcji ograniczania przepustowości możesz określić przepustowość, którą chcesz udostępnić, i czas, kiedy ta przepustowość jest dostępna.
    - Wybierz opcję **Włącz odzyskiwanie na podstawie sieci SAN, używając migawek sprzętowych** , jeśli skonfigurowano sieć.
    - Wybierz pozycję **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**, a następnie wprowadź adresy e-mail, jeśli chcesz, aby powiadomienia e-mail były wysyłane po zakończeniu procesu odzyskiwania.

6. Na ekranie Podsumowanie upewnij się, że wszystkie szczegóły są poprawne. Jeśli szczegóły są nieprawidłowe lub chcesz wprowadzić zmianę, wybierz pozycję **Wstecz**. Jeśli ustawienia są zadowalające, wybierz pozycję **Odzyskaj** , aby rozpocząć proces odzyskiwania.

7. Ekran **Stan odzyskiwania** zawiera informacje o zadaniu odzyskiwania.

## <a name="next-steps"></a>Następne kroki

[Odzyskiwanie danych z usługi Azure Backup Server](./backup-azure-alternate-dpm-server.md)

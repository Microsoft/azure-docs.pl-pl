---
title: Kopii zapasowej maszyn wirtualnych funkcji Hyper-V za pomocą usługi MABS
description: Ten artykuł zawiera procedury dotyczące kopii zapasowej i odzyskiwania maszyn wirtualnych przy użyciu Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b4de791269161b477fc07d6539feaa975fdd72ad
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740002"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Back up Hyper-V virtual machines with Azure Backup Server

W tym artykule opisano sposób kopii zapasowej maszyn wirtualnych funkcji Hyper-V przy użyciu Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Za pomocą usługi MABS można utworzyć kopię zapasową maszyn wirtualnych działających na serwerach hostów funkcji Hyper-V w następujących scenariuszach:

- **Maszyny wirtualne z magazynem lokalnym lub podłączonym bezpośrednio** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych na autonomicznych serwerach hosta funkcji Hyper-V mających lokalną lub podłączoną bezpośrednio pamięć masową. Na przykład: dysk twardy, urządzenie sieci MAGAZYNOWANIA (SAN) lub urządzenie magazynujące dołączone do sieci (NAS). Agent ochrony mabs musi być zainstalowany na wszystkich hostach.

- **Maszyny wirtualne w klastrze z magazynem CSV** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych w klastrze funkcji Hyper-V z woluminem udostępnionym klastra (CSV). Agent ochrony usługi MABS jest instalowany w każdym węźle klastra.

## <a name="host-versus-guest-backup"></a>Kopia zapasowa hosta i gościa

Za pomocą usługi MABS można tworzyć kopie zapasowe maszyn wirtualnych funkcji Hyper-V na poziomie hosta lub gościa. Na poziomie hosta agent ochrony usługi MABS jest instalowany na serwerze hosta lub klastrze funkcji Hyper-V i chroni całe maszyny wirtualne i pliki danych uruchomione na tym hoście.   Na poziomie gościa agent jest instalowany na każdej maszynie wirtualnej i chroni obciążenie obecne na tej maszynie.

Obie metody mają swoje zalety i wady:

- Kopie zapasowe na poziomie hosta są elastyczne, ponieważ działają niezależnie od typu systemu operacyjnego uruchomionego na maszynach gościa i nie wymagają instalacji agenta ochrony usługi MABS na każdej maszynie wirtualnej. W przypadku wdrażania kopii zapasowej na poziomie hosta można odzyskać całą maszynę wirtualną lub pliki i foldery (odzyskiwanie na poziomie elementu).

- Kopia zapasowa na poziomie gościa jest przydatna, jeśli chcesz chronić określone obciążenia uruchomione na maszynie wirtualnej. Na poziomie hosta można odzyskać całą maszynę wirtualną lub określone pliki, ale nie zapewni to odzyskiwania w kontekście określonej aplikacji. Aby na przykład odzyskać określone elementy programu SharePoint z kopii zapasowej maszyny wirtualnej, należy wykonać kopię zapasową tej maszyny wirtualnej na poziomie gościa. Jeśli chcesz chronić dane przechowywane na dyskach passthrough, użyj kopii zapasowej na poziomie gościa. Sposób przekazania umożliwia maszynie wirtualnej bezpośredni dostęp do urządzenia magazynującego i nie przechowuje danych woluminu wirtualnego w pliku VHD.

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

Usługa MABS wykonuje kopię zapasową za pomocą usługi VSS w następujący sposób. Kroki znajdujące się w tym opisie są ponumerowane w celu zwiększenia przejrzystości.

1. Aparat synchronizacji opartej na blokach usługi MABS tworzy początkową kopię chronionej maszyny wirtualnej i zapewnia kompletną i spójną kopię maszyny wirtualnej.

2. Po wstępnym skopiowaniu i zweryfikowaniu usługa MABS przechwyci kopie zapasowe za pomocą funkcji hyper-V— zapisujący usługi VSS. Usługa zapisujący usługi VSS udostępnia spójny na danych zestaw bloków dysków, które są synchronizowane z serwerem USŁUGI MABS. Takie podejście zapewnia korzyści z tworzenia "pełnej kopii zapasowej" z serwerem USŁUGI MABS, jednocześnie minimalizując dane kopii zapasowej, które muszą być przesyłane przez sieć.

3. Agent ochrony usługi MABS na serwerze z uruchomionym programem Hyper-V używa istniejących interfejsów API funkcji Hyper-V do określenia, czy chroniona maszyna wirtualna również obsługuje usługę VSS.

   - Jeśli maszyna wirtualna spełnia wymagania kopii zapasowej online i ma zainstalowany składnik usług integracji funkcji Hyper-V, to element zapisujący usług VSS funkcji Hyper-V rekursywnie przekazuje żądania usług VSS do wszystkich procesów obsługujących takie żądania na maszynie wirtualnej. Ta operacja odbywa się bez instalacji agenta ochrony usługi MABS na maszynie wirtualnej. Dzięki tym cyklicznym żądaniom składnik zapisywania usługi VSS funkcji Hyper-V może się upewnić, że operacje zapisu na dysku są zsynchronizowane, aby przechwycić migawkę usługi VSS bez utraty danych.

     Składnik usług integracji funkcji Hyper-V wywołuje składnik zapisywania usługi VSS funkcji Hyper-V w ramach usług kopiowania woluminów w tle (VSS) w maszynach wirtualnych, aby sprawdzić, czy dane aplikacji są spójne.

   - Jeśli maszyna wirtualna nie jest zgodna z wymaganiami kopii zapasowej online, mabs automatycznie używa interfejsów API funkcji Hyper-V, aby wstrzymać maszynę wirtualną przed przechwyceniem plików danych.

4. Po zsynchronizowaniu początkowej kopii linii bazowej maszyny wirtualnej z serwerem USŁUGI MABS wszystkie zmiany wprowadzone w zasobach maszyny wirtualnej są przechwytywane w nowym punkcie odzyskiwania. Punkt odzyskiwania przedstawia stan spójności maszyny wirtualnej w określonym czasie. Przechwytywanie w ramach punktu odzyskiwania może odbywać się co najmniej raz dziennie. Po utworzeniu nowego punktu odzyskiwania usługa MABS używa replikacji na poziomie bloku w połączeniu ze składowym usługi VSS funkcji Hyper-V w celu określenia, które bloki zostały zmienione na serwerze z funkcją Hyper-V po utworzeniu ostatniego punktu odzyskiwania. Te bloki danych są następnie przesyłane na serwer USŁUGI MABS i stosowane do repliki chronionych danych.

5. Serwer MABS używa usługi VSS na woluminach, które hostują dane odzyskiwania, dzięki czemu dostępnych jest wiele kopii w tle. Każda z tych kopii w tle zapewnia osobne zadania odzyskiwania. Punkty odzyskiwania usługi VSS są przechowywane na serwerze USŁUGI MABS. Kopia tymczasowa wykonana na serwerze z systemem Hyper-V jest przechowywana tylko przez czas trwania synchronizacji usługi MABS.

>[!NOTE]
>
>Począwszy od systemu Windows Server 2016, wirtualne dyski twarde funkcji Hyper-V mają wbudowane śledzenie zmian znane jako śledzenie zmian odporne (RCT). MaBS używa RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonne sprawdzanie spójności w scenariuszach, takich jak awarie maszyny wirtualnej. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. Program MABS w wersji 3 dodatkowo optymalizuje zużycie sieci i magazynu, przesyłając tylko zmienione dane podczas wszelkich kontroli spójności.

## <a name="backup-prerequisites"></a>Wymagania wstępne dotyczące kopii zapasowej

Są to wymagania wstępne dotyczące kopii zapasowej maszyn wirtualnych funkcji Hyper-V za pomocą usługi MABS:

|Wymaganie wstępne|Szczegóły|
|------------|-------|
|Wymagania wstępne dotyczące mabs|— Jeśli chcesz przeprowadzić odzyskiwanie na poziomie elementu dla maszyn wirtualnych (odzyskiwanie plików, folderów, woluminów), musisz zainstalować rolę funkcji Hyper-V na serwerze usługi MABS.  Jeśli chcesz tylko odzyskać maszynę wirtualną, a nie na poziomie elementu, rola nie jest wymagana.<br />— Na jednym serwerze MABS można chronić maksymalnie 800 maszyn wirtualnych o rozmiarze 100 GB i zezwalać na wiele serwerów MABS, które obsługują większe klastry.<br />- MaBS wyklucza plik stronicowania z przyrostowych kopii zapasowych, aby zwiększyć wydajność tworzenia kopii zapasowych maszyny wirtualnej.<br />— Usługa MABS może utworzyć kopię zapasową serwera lub klastra funkcji Hyper-V w tej samej domenie co serwer USŁUGI MABS albo w domenie podrzędnej lub zaufanej. Jeśli chcesz wrócić do kopii zapasowej funkcji Hyper-V w grupie roboczej lub w niezaufanej domenie, musisz skonfigurować uwierzytelnianie. W przypadku pojedynczego serwera funkcji Hyper-V można użyć uwierzytelniania NTLM lub certyfikatu. W przypadku klastra można używać tylko uwierzytelniania za pomocą certyfikatu.<br />— Przy tworzeniu kopii zapasowej danych maszyny wirtualnej na dyskach przekazujących nie jest obsługiwane korzystanie z kopii zapasowych na poziomie hosta. W tym scenariuszu zalecamy używanie kopii zapasowej na poziomie hosta do tworzenia kopii zapasowych plików VHD i kopii zapasowej na poziomie gościa w celu tworzenia kopii zapasowych innych danych, które nie są widoczne na hoście.<br />   — Możesz utworzyć kopię zapasową maszyn wirtualnych przechowywanych na deduplikowanych woluminach.|
|Wymagania wstępne maszyny wirtualnej funkcji Hyper-V|— Wersja składników integracji uruchomiona na maszynie wirtualnej powinna być taka sama jak wersja hosta funkcji Hyper-V. <br />— Dla każdej kopii zapasowej maszyny wirtualnej będzie wymagane wolne miejsce na woluminie obsługującym pliki wirtualnego dysku twardego, aby dostarczyć funkcji Hyper-V wystarczającą ilość miejsca na dyski różnicowe (AVHD) podczas wykonywania kopii zapasowej. Miejsce musi być co najmniej równe obliczaniu początkowy rozmiar dysku Współczynnika zmian kopii zapasowych czasu okna. **\* \*** Jeśli używasz wielu kopii zapasowych w klastrze, konieczna będzie obliczona w ten sposób wystarczająca ilość miejsca do magazynowania dysków AVHD dla poszczególnych maszyn wirtualnych.<br />— Aby wrócić do kopii zapasowych maszyn wirtualnych znajdujących się na serwerach hostów funkcji Hyper-V z systemem Windows Server 2012 R2, maszyna wirtualna powinna mieć określony kontroler SCSI, nawet jeśli nie jest podłączona do niczego. (W przypadku kopii zapasowej systemu Windows Server 2012 R2 host funkcji Hyper-V zainstaluje nowy wirtualny dysk twardy na maszynie wirtualnej, a następnie odinstaluje go. Tylko kontroler SCSI może to obsługiwać i dlatego jest wymagany do tworzenia kopii zapasowej online maszyny wirtualnej.  Bez tego ustawienia podczas próby kopii zapasowej maszyny wirtualnej zostanie wystawione zdarzenie o identyfikatorze 10103).|
|Wymagania wstępne systemu Linux|— Możesz utworzyć kopię zapasową maszyn wirtualnych z systemem Linux przy użyciu usługi MABS. Obsługiwane są tylko migawki zgodne z plikami.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem CSV|— Na potrzeby magazynu CSV należy zainstalować na serwerze funkcji Hyper-V dostawcę sprzętowego usługi kopiowania woluminów w tle (VSS). W sprawie dostawcy sprzętowego usługi VSS skontaktuj się z dostawcą swojej sieci magazynowania (SAN).<br />— Jeśli jeden węzeł zostanie nieoczekiwanie zamknięty w klastrze CSV, usługa MABS wykona sprawdzanie spójności maszyn wirtualnych uruchomionych w tym węźle.<br />— Jeśli potrzebujesz uruchomić ponownie serwer funkcji Hyper-V z włączoną funkcją szyfrowania dysków BitLocker w klastrze CSV, musisz uruchomić sprawdzenie spójności dla maszyn wirtualnych funkcji Hyper-V.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem SMB|— Włącz funkcję automatycznej instalacji na serwerze, na którym działa funkcja Hyper-V, aby włączyć ochronę maszyny wirtualnej.<br />   — Wyłącz odciążanie przy użyciu technologii TCP Chimney.<br />— Upewnij się, że wszystkie konta machine$ funkcji Hyper-V mają pełne uprawnienia w określonych udziałach zdalnych plików SMB.<br />- Upewnij się, że ścieżka pliku dla wszystkich składników maszyny wirtualnej podczas odzyskiwania do lokalizacji alternatywnej jest mniejsza niż 260 znaków. Jeśli nie, odzyskiwanie może zakończyć się pomyślnie, ale funkcja Hyper-V nie będzie mogła zainstalować maszyny wirtualnej.<br />— Następujące scenariusze nie są obsługiwane:<br />     Wdrożenia, w których niektóre składniki maszyny wirtualnej znajdują się na woluminach lokalnych, a niektóre składniki znajdują się na woluminach zdalnych; adres IPv4 lub IPv6 dla serwera plików lokalizacji przechowywania oraz odzyskiwanie maszyny wirtualnej na komputer, który używa zdalnych udziałów SMB.<br />- Należy włączyć usługę agenta VSS serwera plików na każdym serwerze SMB — dodaj ją w dodaj role i funkcje Wybierz role serwera usługi plików i magazynowania usługi plików  >    >    >    >    >  **usługi vss agent usługi**.|

## <a name="back-up-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych

1. Skonfiguruj serwer [MABS i](backup-azure-microsoft-azure-backup.md) [magazyn](backup-mabs-add-storage.md). Podczas konfigurowania magazynu użyj następujących wytycznych dotyczących pojemności magazynu.
   - Średni rozmiar maszyny wirtualnej — 100 GB
   - Liczba maszyn wirtualnych na serwer USŁUGI MABS — 800
   - Całkowity rozmiar 800 maszyn wirtualnych — 80 TB
   - Wymagane miejsce na magazyn kopii zapasowych — 80 TB

2. Skonfiguruj agenta ochrony usługi MABS na serwerze funkcji Hyper-V lub w węzłach klastra funkcji Hyper-V.

3. W konsoli administratora usługi MABS wybierz pozycję **Ochrona Utwórz**  >  **grupę ochrony,** aby otworzyć **kreatora Tworzenie nowej grupy** ochrony.

4. Na stronie **Wybierz członków grupy** wybierz maszyny wirtualne, które chcesz chronić za pomocą serwerów hostów funkcji Hyper-V, na których się znajdują. Zalecane jest umieszczenie wszystkich maszyn wirtualnych, które będą miały te same zasady ochrony, w jednej grupie ochrony. W celu efektywnego wykorzystania miejsca włącz wspólną lokalizację. Przekazywanie danych do wspólnej lokalizacji umożliwia umieszczanie danych pochodzących z różnych grup ochrony w tym samym magazynie dyskowym lub taśmowym, tak aby różne źródła danych miały jedną replikę i wolumin punktu odzyskiwania.

5. Na stronie **Wybierz metodę ochrony danych** podaj nazwę grupy ochrony. Wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku** oraz opcję **Chcę uzyskać ochronę online** , aby tworzyć kopie zapasowe danych na platformie Azure za pomocą usługi Kopia zapasowa Azure.

6. W **Short-Term Cele przechowywania** określ, jak długo mają być  >  zachowywane dane dysku. W **ustawieniach Częstotliwość** synchronizacji określ, jak często mają być uruchamiane przyrostowe kopie zapasowe danych. Zamiast określać interwał tworzenia przyrostowych kopii zapasowych, możesz włączyć opcję **Bezpośrednio przed punktem odzyskiwania**. Po włączeniu tego ustawienia usługi MABS będą uruchamiać ekspresowe pełne kopie zapasowe tuż przed każdym zaplanowanym punktem odzyskiwania.

    > [!NOTE]
    >
    >W przypadku ochrony obciążeń aplikacji punkty odzyskiwania są tworzone zgodnie z opcją Częstotliwość synchronizacji, przy założeniu, że aplikacja obsługuje przyrostowe kopie zapasowe. Jeśli tak się nie stanie, wówczas mabs uruchamia ekspresową pełną kopię zapasową, a nie przyrostową kopię zapasową, i tworzy punkty odzyskiwania zgodnie z harmonogramem ekspresowej kopii zapasowej.<br></br>Proces tworzenia kopii zapasowej nie zawiera kopii zapasowych punktów kontrolnych skojarzonych z maszynami wirtualnych.

7. Na stronie **Przeglądanie alokacji dysku** przejrzyj miejsce na dysku w puli magazynów przydzielone grupie ochrony.

   **Łączny rozmiar danych** to rozmiar danych, których kopię zapasową chcesz kopii zapasowej, a miejsce na dysku do aprowizowania w u usługi **MABS** to miejsce zalecane przez usługę MABS dla grupy ochrony. MaBS wybiera idealny wolumin kopii zapasowej, na podstawie ustawień. Opcje wielkości kopii zapasowej można jednak edytować w obszarze **Szczegóły przydziału dysku**. W przypadku obciążeń wybierz z menu rozwijanego preferowany magazyn. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Zaniona aprowizowana ilość miejsca w magazynie, którą sugeruje dodanie do woluminu usługi MABS, umożliwia bezproblemowe tworzenie kopii zapasowych w przyszłości.

8. Na stronie **Wybierz metodę tworzenia repliki** określ sposób, w jaki ma zostać wykonana początkowa replikacja danych w grupie ochrony. W przypadku wybrania opcji **Automatycznie replikuj za pośrednictwem sieci** zalecamy wybranie czasu poza szczytem. W przypadku dużych ilości danych lub mniej niż optymalne warunki sieciowe rozważ wybranie opcji **Ręcznie,** co wymaga replikowania danych w trybie offline przy użyciu nośników wymiennych.

9. Na stronie **Opcje sprawdzania spójności** wybierz odpowiedni sposób automatyzacji sprawdzania spójności. Można włączyć uruchamianie sprawdzania tylko wtedy, gdy dane są niespójne, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony i wybierając polecenie **Przeprowadź sprawdzanie spójności**.

    Po utworzeniu grupy ochrony następuje replikacja początkowa danych zgodnie z wybraną metodą. Po replikacji początkowej tworzone są poszczególne kopie zapasowe zgodnie z ustawieniami grupy ochrony. Jeśli musisz odzyskać dane kopii zapasowej, pamiętaj o następujących uwagach:

## <a name="back-up-replica-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych repliki

Jeśli usługa MABS jest uruchomiona w systemie Windows Server 2012 R2 lub większym, można utworzyć kopię zapasową maszyn wirtualnych repliki. Jest to przydatne z kilku powodów:

**Ogranicza wpływ tworzenia kopii zapasowej na obciążenie pracą** — wykonywanie kopii zapasowej maszyny wirtualnej powoduje pewne nadmiarowe obciążenie podczas tworzenia migawki. Odciążając proces tworzenia kopii zapasowej do dodatkowej lokacji zdalnej, operacja tworzenia kopii zapasowej nie ma już wpływu na uruchomione obciążenie. Ma to zastosowanie tylko do wdrożeń, w których kopia zapasowa jest przechowywana w lokalizacji zdalnej. Na przykład możesz korzystać z codziennych kopii zapasowych i przechowywać dane lokalnie w celu zapewnienia szybkiego przywracania, ale co miesiąc lub co kwartał tworzyć kopie zapasowe z maszyn wirtualnych replik przechowywanych zdalnie w celu przechowywania długoterminowego.

**Oszczędza przepustowość** — w typowych wdrożeniach w zdalnym oddziale lub siedzibie firmy potrzebne jest zapewnienie odpowiedniej przepustowości do transferu danych kopii zapasowych między lokacjami. Jeśli tworzysz strategię replikacji i pracy awaryjnej, oprócz opracowania strategii wykonywania kopii zapasowej danych możesz zmniejszyć ilość nadmiarowych danych przesyłanych w sieci. Tworzenie kopii zapasowej danych maszyny wirtualnej repliki zamiast podstawowej pozwala zaoszczędzić na kosztach wysyłania danych kopii zapasowej za pośrednictwem sieci.

**Umożliwia wykonywanie kopii zapasowych dostawcy usług hostingowych** — hostowanego centrum danych można użyć jako lokalizacji repliki, co powoduje, że pomocnicze centrum danych nie jest potrzebne. W takim przypadku umowa SLA hosta wymaga spójnej kopii zapasowej maszyn wirtualnych repliki.

Maszyna wirtualna repliki jest wyłączona do momentu zainicjowania trybu failover, a usługa VSS nie może zagwarantować kopii zapasowej maszyny wirtualnej repliki spójnej na poziomie aplikacji. Dlatego kopia zapasowa repliki maszyny wirtualnej będzie tylko spójna na awariach. Jeśli nie można zagwarantować spójności na poziomie awarii, tworzenie kopii zapasowej zakończy się niepowodzeniem, a taka sytuacja może wystąpić w kilku przypadkach:

- Maszyna wirtualna repliki nie jest w dobrej kondycji i jest w stanie krytycznym.

- Maszyna wirtualna repliki jest ponownie synchronizowana (w stanie ponownej synchronizacji w toku lub w stanie konieczności wykonania ponownej synchronizacji).

- Początkowa replikacja maszyny wirtualnej między lokalizacją główną a pomocniczą jest w toku lub jest oczekiwana.

- Dzienniki hrl są stosowane do maszyny wirtualnej repliki lub poprzednia akcja zastosowania dzienników hrl na dysku wirtualnym nie powiodła się lub została anulowana bądź przerwana.

- Trwa migracja lub praca w trybie failover maszyny wirtualnej repliki

## <a name="recover-backed-up-virtual-machines"></a>Odzyskiwanie kopii zapasowych maszyn wirtualnych

Gdy możesz odzyskać kopię zapasową maszyny wirtualnej, użyj Kreatora odzyskiwania, aby wybrać maszynę wirtualną i określony punkt odzyskiwania. Aby otworzyć Kreatora odzyskiwania i odzyskać maszynę wirtualną:

1. W konsoli administratora usługi MABS wpisz nazwę maszyny wirtualnej lub rozwiń listę chronionych elementów, przejdź do pozycji Wszystkie chronione dane **funkcji HyperV** i wybierz maszynę wirtualną, którą chcesz odzyskać.

2. W **okienku Punkty odzyskiwania** dla w kalendarzu wybierz dowolną datę, aby wyświetlić dostępne punkty odzyskiwania. Następnie w okienku **Ścieżka** wybierz punkt odzyskiwania, którego chcesz użyć w Kreatorze odzyskiwania.

3. W menu **Akcje** wybierz pozycję **Odzyskaj,** aby otworzyć Kreatora odzyskiwania.

    Wybrana maszyna wirtualna i punkt odzyskiwania są wyświetlane na ekranie **Przegląd wybranego elementu do odzyskania**. Wybierz opcję **Dalej**.

4. Na **ekranie Wybierz typ odzyskiwania** wybierz miejsce, do którego chcesz przywrócić dane, a następnie wybierz przycisk **Dalej.**

    - **Odzyskiwanie do oryginalnego wystąpienia:** po odzyskaniu do oryginalnego wystąpienia oryginalny dysk VHD i wszystkie skojarzone punkty kontrolne są usuwane. Usługa MABS odzyskuje wirtualny dysk twardy i inne pliki konfiguracji do oryginalnej lokalizacji przy użyciu funkcji Hyper-V vss writer. Po zakończeniu procesu odzyskiwania maszyny wirtualne nadal zapewniają dużą dostępność.
        Aby odzyskiwanie mogło się odbyć, musi być dostępna grupa zasobów. Jeśli grupa nie jest dostępna, wykonaj odzyskiwanie do lokalizacji alternatywnej, a następnie przełącz maszynę wirtualną w tryb wysokiej dostępności.

    - **Odzyskaj** jako maszynę wirtualną na dowolnym hoście: mabs obsługuje odzyskiwanie lokalizacji alternatywnej (ALR), które zapewnia bezproblemowe odzyskiwanie chronionej maszyny wirtualnej funkcji Hyper-V na innym hoście funkcji Hyper-V, niezależnie od architektury procesora. Maszyny wirtualne funkcji Hyper-V odzyskiwane do węzła klastra nie będą wysoce dostępne. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.
    
        >[!NOTE]
        >W przypadku wybrania oryginalnego hosta zachowanie jest takie samo jak w przypadku odzyskiwania **do oryginalnego wystąpienia**. Oryginalny wirtualny dysk twardy i wszystkie skojarzone punkty kontrolne zostaną usunięte.

    - Kopiowanie do folderu **sieciowego:** program MABS obsługuje odzyskiwanie na poziomie elementu (ILR), które umożliwia odzyskiwanie na poziomie elementu plików, folderów, woluminów i wirtualnych dysków twardych (VHD) z kopii zapasowej na poziomie hosta maszyn wirtualnych funkcji Hyper-V do udziału sieciowego lub woluminu na serwerze chronionym przez usługę MABS. Agent ochrony MABS nie musi być zainstalowany wewnątrz gościa, aby przeprowadzić odzyskiwanie na poziomie elementu. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.

5. W **konfigurować opcje odzyskiwania** Określ opcje odzyskiwania i wybierz przycisk **Dalej:**

    - Jeśli przywracasz maszynę wirtualną za pośrednictwem niskiej przepustowości, wybierz pozycję **Modyfikuj,** aby włączyć **ograniczanie przepustowości sieci.** Po włączeniu opcji ograniczania przepustowości możesz określić przepustowość, którą chcesz udostępnić, i czas, kiedy ta przepustowość jest dostępna.
    - Wybierz **pozycję Włącz odzyskiwanie oparte na sieci SAN** przy użyciu migawek sprzętowych, jeśli skonfigurowano sieć.
    - Wybierz pozycję **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**, a następnie wprowadź adresy e-mail, jeśli chcesz, aby powiadomienia e-mail były wysyłane po zakończeniu procesu odzyskiwania.

6. Na ekranie Podsumowanie upewnij się, że wszystkie szczegóły są poprawne. Jeśli szczegóły nie są poprawne lub chcesz wprowadzić zmianę, wybierz pozycję **Wstecz.** Jeśli ustawienia ci się podobają, wybierz pozycję **Odzyskaj,** aby rozpocząć proces odzyskiwania.

7. Ekran **Stan odzyskiwania** zawiera informacje o zadaniu odzyskiwania.

## <a name="next-steps"></a>Następne kroki

[Odzyskiwanie danych z usługi Azure Backup Server](./backup-azure-alternate-dpm-server.md)

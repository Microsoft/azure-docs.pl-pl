---
title: Przewodnik rozwiązywania problemów z wydajnością udziałów plików platformy Azure
description: Rozwiązywanie znanych problemów z wydajnością przy użyciu udziałów plików platformy Azure. Odkryj potencjalne przyczyny i powiązane obejścia w przypadku napotkania tych problemów.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 9f858549f36d196c6412aec549d0ab2e2d864145
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417675"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Rozwiązywanie problemów z wydajnością udziałów plików platformy Azure

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure. Zapewnia potencjalne przyczyny i obejścia w przypadku wystąpienia tych problemów.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Duże opóźnienia, niska przepływność i ogólne problemy z wydajnością

### <a name="cause-1-share-was-throttled"></a>Przyczyna 1: udział został ograniczony

Żądania są ograniczane, gdy osiągnięto limity operacji we/wy na sekundę (IOPS), ruch przychodzący lub wychodzący dla udziału plików. Aby zrozumieć limity dla udziałów plików w warstwach Standardowa i Premium, zobacz [elementy docelowe udziałów plików i skalowania plików](./storage-files-scale-targets.md#azure-file-share-scale-targets).

Aby potwierdzić, że Twój udział jest ograniczany, możesz uzyskać dostęp do metryk platformy Azure w portalu i korzystać z nich.

1. W Azure Portal przejdź do konta magazynu.

1. W okienku po lewej stronie w obszarze **monitorowanie** wybierz pozycję **metryki**.

1. Wybierz pozycję **plik** jako przestrzeń nazw metryki dla zakresu konta magazynu.

1. Wybierz **transakcje** jako metrykę.

1. Dodaj filtr dla **typu odpowiedzi**, a następnie sprawdź, czy jakieś żądania zostały ograniczone. 

    W przypadku standardowych udziałów plików następujące typy odpowiedzi są rejestrowane, jeśli żądanie jest ograniczone:

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    W przypadku udziałów plików w warstwie Premium następujące typy odpowiedzi są rejestrowane, jeśli żądanie jest ograniczone:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    Aby dowiedzieć się więcej na temat każdego typu odpowiedzi, zobacz [Dimension Metrics](./storage-files-monitoring-reference.md#metrics-dimensions).

    ![Zrzut ekranu przedstawiający opcje metryk dla udziałów plików w warstwie Premium, z uwzględnieniem filtru właściwości "typ odpowiedzi".](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > Aby odebrać alert, zobacz sekcję ["jak utworzyć alert, jeśli udział plików jest ograniczany"](#how-to-create-an-alert-if-a-file-share-is-throttled) w dalszej części tego artykułu.

### <a name="solution"></a>Rozwiązanie

- Jeśli używasz standardowego udziału plików, Włącz [duże udziały plików](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) na koncie magazynu. Duże udziały plików obsługują do 10 000 operacji we/wy na udział.
- W przypadku korzystania z udziału plików w warstwie Premium Zwiększ rozmiar udostępnianego udziału plików, aby zwiększyć limit operacji we/wy. Aby dowiedzieć się więcej, zobacz [Opis udostępniania udziałów plików w warstwie Premium](./understanding-billing.md#provisioned-model).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Przyczyna 2: duże obciążenie metadanych lub przestrzeni nazw

Jeśli większość żądań jest skoncentrowana na metadanych (takich jak CloseFile, QueryInfo lub querydirectory), opóźnienie będzie gorszyć niż operacje odczytu/zapisu.

Aby określić, czy większość żądań jest skoncentrowana na metadanych, Zacznij od wykonania następujących kroków 1-4 zgodnie z wcześniejszym opisem w przyczynach 1. W kroku 5 zamiast dodawania filtru dla **typu odpowiedzi** Dodaj filtr właściwości dla **nazwy interfejsu API**.

![Zrzut ekranu przedstawiający opcje metryk dla udziałów plików w warstwie Premium, z uwzględnieniem filtru właściwości "nazwa interfejsu API".](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Obejście

- Sprawdź, czy można zmodyfikować aplikację, aby zmniejszyć liczbę operacji na metadanych.
- Dodaj wirtualny dysk twardy (VHD) w udziale plików i zainstaluj dysk VHD za pośrednictwem protokołu SMB z poziomu klienta, aby wykonać operacje na plikach na danych. Ta metoda działa w przypadku jednego składnika zapisywania i wielu scenariuszy czytelników i umożliwia wykonywanie operacji na metadanych lokalnych. Instalator oferuje wydajność podobną do lokalnej bezpośrednio dołączonego magazynu.

### <a name="cause-3-single-threaded-application"></a>Przyczyna 3: aplikacja jednowątkowa

Jeśli używana aplikacja jest jednowątkowa, ta konfiguracja może spowodować znacznie mniejszą przepustowość operacji we/wy niż maksymalna możliwa przepływność, w zależności od rozmiaru udostępnionego udziału.

### <a name="solution"></a>Rozwiązanie

- Zwiększenie równoległości aplikacji przez zwiększenie liczby wątków.
- Przejdź do aplikacji, w których możliwa jest równoległość. Na przykład w przypadku operacji kopiowania można użyć programu AzCopy lub RoboCopy z klientów systemu Windows lub polecenia **Parallel** z poziomu klientów z systemem Linux.

## <a name="very-high-latency-for-requests"></a>Bardzo duże opóźnienie dla żądań

### <a name="cause"></a>Przyczyna

Maszyna wirtualna klienta (VM) może znajdować się w innym regionie niż udział plików. Innym powodem wysokiego opóźnienia może być spowodowanie opóźnień spowodowanych przez klienta lub sieć.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyny wirtualnej znajdującej się w tym samym regionie co udział plików.
- W przypadku konta magazynu Przejrzyj metryki transakcji **SuccessE2ELatency** i  **SuccessServerLatency** za pośrednictwem **Azure monitor** w Azure Portal. Wysoka różnica między wartościami metryk SuccessE2ELatency i SuccessServerLatency to wskazanie opóźnienia, które prawdopodobnie jest spowodowane przez sieć lub klienta. Zobacz [metryki transakcji](storage-files-monitoring-reference.md#transaction-metrics) w temacie Informacje o danych monitorowania Azure Files.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nie może osiągnąć maksymalnej przepływności obsługiwanej przez sieć

### <a name="cause"></a>Przyczyna
Jedną z potencjalnych przyczyn jest brak obsługi protokołu SMB w przypadku standardowych udziałów plików. Obecnie Azure Files obsługuje tylko jeden kanał, więc istnieje tylko jedno połączenie z maszyny wirtualnej klienta do serwera. To pojedyncze połączenie jest oznaczane pojedynczym rdzeniem na maszynie wirtualnej klienta, dzięki czemu maksymalna przepływność osiągalna z maszyny wirtualnej jest powiązana z jednym rdzeniem.

### <a name="workaround"></a>Obejście

- W przypadku udziałów plików w warstwie Premium [Włącz Wielokanałowość SMB na koncie FileStorage](storage-files-enable-smb-multichannel.md).
- Uzyskanie maszyny wirtualnej z większym rdzeniem może pomóc w zwiększeniu przepływności.
- Uruchomienie aplikacji klienckiej z wielu maszyn wirtualnych spowoduje zwiększenie przepływności.
- Jeśli to możliwe, Użyj interfejsów API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Przepływność na klientach systemu Linux jest znacznie mniejsza niż w przypadku klientów z systemem Windows

### <a name="cause"></a>Przyczyna

Jest to znany problem z implementacją klienta SMB w systemie Linux.

### <a name="workaround"></a>Obejście

- Rozłożenie obciążenia na wiele maszyn wirtualnych.
- Na tej samej maszynie wirtualnej Użyj wielu punktów instalacji z opcją **nosharesock** i rozłożyć obciążenie na te punkty instalacji.
- W systemie Linux spróbuj zainstalować z opcją **nostrictsync** , aby uniknąć wymuszania opróżniania SMB dla każdego wywołania **fsync** . W przypadku Azure Files ta opcja nie zakłóca spójności danych, ale może spowodować powstanie starych metadanych plików na listach katalogów (polecenie **ls-l** ). Bezpośrednie badanie metadanych plików przy użyciu polecenia **stat** zwróci najbardziej aktualne metadane pliku.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Duże opóźnienia dla obciążeń intensywnie korzystających z metadanych obejmujących liczne operacje otwierania/zamykania

### <a name="cause"></a>Przyczyna

Brak obsługi dzierżaw katalogów.

### <a name="workaround"></a>Obejście

- Jeśli to możliwe, Unikaj używania nadmiernego, otwierającego/zamykającego uchwytu w tym samym katalogu w krótkim czasie.
- W przypadku maszyn wirtualnych z systemem Linux Zwiększ limit czasu pamięci podręcznej wpisów w katalogu, określając **actimeo = \<sec>** jako opcję instalacji. Domyślnie limit czasu to 1 sekunda, więc większa wartość, taka jak 3 lub 5 sekund, może pomóc.
- W przypadku maszyn wirtualnych z systemem CentOS Linux lub Red Hat Enterprise Linux (RHEL) Uaktualnij system do wersji CentOS Linux 8,2 lub RHEL 8,2. W przypadku innych maszyn wirtualnych z systemem Linux Uaktualnij jądro do wersji 5,0 lub nowszej.

## <a name="low-iops-on-centos-linux-or-rhel"></a>Niska liczba operacji we/wy na CentOS Linux lub RHEL

### <a name="cause"></a>Przyczyna

Głębokość we/wy o wartości większej niż 1 nie jest obsługiwana w systemie CentOS Linux lub RHEL.

### <a name="workaround"></a>Obejście

- Uaktualnij system do wersji CentOS Linux 8 lub RHEL 8.
- Zmień na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Powolne kopiowanie plików do i z udziałów plików platformy Azure w systemie Linux

Jeśli masz wolne kopiowanie plików, zapoznaj się z sekcją "wolne kopiowanie plików do i z udziałów plików platformy Azure w systemie Linux" w [przewodniku rozwiązywania problemów z systemem Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Zakłócenia lub wzorzec powoduje piłokształtny dla operacji we/wy na sekundę

### <a name="cause"></a>Przyczyna

Aplikacja kliencka ciągle przekracza liczbę operacji wejścia/wyjścia na sekundę. Obecnie nie ma żadnych problemów z przekazywaniem danych po stronie usługi. Jeśli klient przekroczy liczbę operacji wejścia/wyjścia na sekundę, zostanie on ograniczony przez usługę. Ograniczanie wydajności może spowodować, że klient napotkał zakłócenia lub powoduje piłokształtny IOPS. W takim przypadku średnia liczba operacji we/wy osiągniętych przez klienta może być mniejsza niż liczba operacji we/wy na sekundę.

### <a name="workaround"></a>Obejście
- Zmniejsz obciążenie żądaniami z aplikacji klienckiej, aby udział nie został ograniczony.
- Zwiększ limit przydziału udziału, aby udział nie został ograniczony.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadmierne wywołania DirectoryOpen/DirectoryClose

### <a name="cause"></a>Przyczyna

Jeśli liczba wywołań **DirectoryOpen/DirectoryClose** jest między najpopularniejszymi WYWOŁANIAMI interfejsu API i nie oczekujesz, że klient nie przyniesie wielu wywołań, przyczyną problemu może być oprogramowanie antywirusowe zainstalowane na maszynie wirtualnej klienta platformy Azure.

### <a name="workaround"></a>Obejście

- Rozwiązanie tego problemu jest dostępne w [aktualizacji platformy w kwietniu dla systemu Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Tworzenie pliku jest wolniejsze niż oczekiwano

### <a name="cause"></a>Przyczyna

Obciążenia, które opierają się na tworzeniu dużej liczby plików, nie będą miały znaczącej różnicy wydajności między udziałami plików w warstwie Premium a standardowymi udziałami plików.

### <a name="workaround"></a>Obejście

- Brak.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niska wydajność z Windows 8.1 lub serwera 2012 R2

### <a name="cause"></a>Przyczyna

Większe niż oczekiwane opóźnienie dostępu do udziałów plików platformy Azure dla obciążeń intensywnie korzystających z operacji we/wy.

### <a name="workaround"></a>Obejście

- Zainstaluj dostępną [poprawkę](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>Opcja wielokanałowe protokołu SMB nie jest widoczna w obszarze Ustawienia udziału plików. 

### <a name="cause"></a>Przyczyna

Subskrypcja nie jest zarejestrowana dla tej funkcji lub region i typ konta nie są obsługiwane.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że subskrypcja została zarejestrowana na potrzeby funkcji wielokanałowe protokołu SMB. Zobacz [wprowadzenie](storage-files-enable-smb-multichannel.md#getting-started) — upewnij się, że rodzaj konta to FileStorage (konto plików w warstwie Premium) na stronie Przegląd konta. 

## <a name="smb-multichannel-is-not-being-triggered"></a>Nie jest wyzwalany wielokanałowe protokół SMB.

### <a name="cause"></a>Przyczyna

Ostatnie zmiany ustawień konfiguracji wielokanałowego protokołu SMB bez ponownej instalacji.

### <a name="solution"></a>Rozwiązanie
 
-   Po wprowadzeniu zmian w ustawieniach konfiguracji wielokanałowej klienta SMB systemu Windows lub konta SMB należy odinstalować udział, poczekać na 60 sekund i ponownie zainstalować udział w celu wyzwolenia wielokanałowości.
-   W przypadku systemu operacyjnego Windows klienta Wygeneruj obciążenie we/wy o wysokiej głębokości kolejki powiedz głębokość kolejki = 8, na przykład kopiując plik do wyzwalania wielokanałowe protokołu SMB.  W przypadku systemu operacyjnego serwera jest wyzwalana funkcja wielokanałowe protokołu SMB z głębokość kolejki = 1, co oznacza, że tylko po rozpoczęciu wszystkich operacji we/wy do udziału.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Duże opóźnienie w witrynach sieci Web hostowanych w udziałach plików 

### <a name="cause"></a>Przyczyna  

Powiadomienia o zmianach plików o dużej liczbie w udziałach plików mogą powodować znaczne duże opóźnienia. Zwykle jest to wykonywane w przypadku witryn sieci Web hostowanych w udziałach plików z głębokiej zagnieżdżonej struktury katalogów. Typowym scenariuszem jest hostowana aplikacja sieci Web IIS, w której jest skonfigurowane powiadomienie o zmianie pliku dla każdego katalogu w konfiguracji domyślnej. Każda zmiana ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) w udziale, który jest zarejestrowany przez klienta SMB do wypychania powiadomienia o zmianach z usługi plików do klienta, który pobiera zasoby systemowe, i występuje problem z liczbą zmian. Może to spowodować ograniczenie przepustowości i w związku z tym spowodować większe opóźnienia po stronie klienta. 

Aby potwierdzić, możesz użyć metryk platformy Azure w portalu — 

1. W Azure Portal przejdź do konta magazynu. 
1. W menu po lewej stronie w obszarze monitorowanie wybierz pozycję metryki. 
1. Wybierz pozycję plik jako przestrzeń nazw metryki dla zakresu konta magazynu. 
1. Wybierz transakcje jako metrykę. 
1. Dodaj filtr dla elementu responsetype i sprawdź, czy jakieś żądania mają kod odpowiedzi SuccessWithThrottling (dla protokołu SMB) lub ClientThrottlingError (dla REST).

### <a name="solution"></a>Rozwiązanie 

- Jeśli powiadomienie o zmianie pliku nie jest używane, wyłącz powiadomienie o zmianie pliku (preferowany).
    - [Wyłącz powiadomienie o zmianie pliku](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) przez aktualizację FCNMode. 
    - Zaktualizuj interwał sondowania procesu roboczego usług IIS (W3WP) do 0 przez ustawienie `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` w rejestrze i ponownie uruchom proces W3wp. Aby dowiedzieć się więcej na temat tego ustawienia, zobacz [Common Registry Keys, które są używane przez wiele części usług IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Zwiększ częstotliwość interwału sondowania powiadomień o zmianach pliku, aby zmniejszyć ilość woluminu.
    - Zaktualizuj interwał sondowania procesu roboczego W3WP na wyższą wartość (np. 10mins lub 30mins) na podstawie wymagań. Ustaw `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [w rejestrze](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) i ponownie uruchom proces W3wp.
- Jeśli zmapowany katalog fizyczny witryny sieci Web ma zagnieżdżoną strukturę katalogów, możesz spróbować ograniczyć zakres powiadomień o zmianach pliku, aby zmniejszyć ilość powiadomień. Domyślnie usługi IIS używają konfiguracji z Web.config plików w katalogu fizycznym, do którego zamapowany jest katalog wirtualny, a także w dowolnych katalogach podrzędnych w katalogu fizycznym. Jeśli nie chcesz używać plików Web.config w katalogach podrzędnych, określ wartość false dla atrybutu wartość allowsubdirconfig w katalogu wirtualnym. Więcej szczegółów można znaleźć [tutaj](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 
    - Ustaw ustawienie "wartość allowsubdirconfig" katalogu wirtualnego usług IIS w Web.Config na *wartość false* , aby wykluczyć mapowane katalogi fizyczne obiektów podrzędnych z zakresu.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczany

1. Przejdź do swojego **konta magazynu** w **Azure Portal**.
2. W sekcji **monitorowanie** kliknij pozycję **alerty**, a następnie kliknij pozycję **+ Nowa reguła alertów**.
3. Kliknij pozycję **Edytuj zasób**, wybierz **Typ zasobu pliku** dla konta magazynu, a następnie kliknij pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to `contoso` , wybierz `contoso/file` zasób.
4. Kliknij przycisk **Dodaj warunek** , aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych przez konto magazynu, wybierz metrykę **transakcji** .
6. W bloku **Konfigurowanie logiki sygnału** kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **Typ odpowiedzi**.
7. Kliknij listę rozwijaną **wartości wymiaru** i wybierz odpowiednie typy odpowiedzi dla danego udziału plików.

    W przypadku standardowych udziałów plików wybierz następujące typy odpowiedzi:

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    W przypadku udziałów plików w warstwie Premium wybierz następujące typy odpowiedzi:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Jeśli typy odpowiedzi nie są wyświetlane na liście rozwijanej **wartości wymiaru** , oznacza to, że zasób nie został ograniczony. Aby dodać wartości wymiaru, obok listy rozwijanej **wartości wymiaru** wybierz pozycję **Dodaj wartość niestandardową**, wprowadź typ respone (na przykład **SuccessWithThrottling**), wybierz pozycję **OK**, a następnie powtórz te kroki, aby dodać wszystkie odpowiednie typy odpowiedzi dla udziału plików.

8. W przypadku **udziałów plików w warstwie Premium** kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **udział plików**. W przypadku **standardowych udziałów plików** przejdź do **kroku #10**.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, wymiar **udziału plików** nie będzie wyświetlać udziałów plików, ponieważ metryki dla udziałów nie są dostępne dla standardowych udziałów plików. Alerty dotyczące ograniczania przepustowości dla standardowych udziałów plików będą wyzwalane, jeśli jakikolwiek udział plików w ramach konta magazynu zostanie ograniczony, a alert nie określi, który udział plików został ograniczony. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecenie ma mieć jeden udział plików na konto magazynu.

9. Kliknij listę rozwijaną **wartości wymiaru** i wybierz udziały plików, dla których chcesz utworzyć alert.
10. Zdefiniuj **Parametry alertu** (wartość progowa, operator, stopień szczegółowości agregacji i częstotliwość oceny), a następnie kliknij pozycję **gotowe**.

    > [!TIP]
    > Jeśli jest używany próg statyczny, wykres metryk może pomóc w ustaleniu rozsądnej wartości progowej, jeśli udział plików jest obecnie ograniczany. Jeśli używasz progu dynamicznego, wykres metryki wyświetli obliczone progi na podstawie ostatnich danych.

11. Kliknij pozycję **Dodaj grupy akcji** , aby dodać do alertu **grupę akcji** (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
12. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
13. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Tworzenie alertów, jeśli udział plików w warstwie Premium jest trendem w kierunku ograniczenia przepustowości

1. W Azure Portal przejdź do konta magazynu.
2. W sekcji **monitorowanie** wybierz pozycję **alerty**, a następnie wybierz pozycję **Nowa reguła alertu**.
3. Wybierz pozycję **Edytuj zasób**, wybierz **Typ zasobu pliku** dla konta magazynu, a następnie wybierz pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to *contoso*, wybierz zasób contoso/File.
4. Wybierz pozycję **Wybierz warunek** , aby dodać warunek.
5. Na liście sygnałów, które są obsługiwane dla konta magazynu, wybierz metrykę **ruchu** wychodzącego.

   > [!NOTE]
   > Należy utworzyć trzy osobne alerty, aby otrzymywać alerty, gdy wartości przychodzące, wychodzące lub transakcje przekroczą ustawione progi. Wynika to z faktu, że alert jest wyzwalany tylko wtedy, gdy wszystkie warunki są spełnione. Jeśli na przykład umieścisz wszystkie warunki w jednym alercie, będziesz otrzymywać alerty tylko wtedy, gdy ruch przychodzący, wychodzący i transakcje przekraczają ich wartości progowe.

6. Przewiń w dół. Z listy rozwijanej **Nazwa wymiaru** wybierz pozycję **udział plików**.
7. Z listy rozwijanej **wartości wymiaru** wybierz udział plików lub udziały, w których chcesz utworzyć alert.
8. Zdefiniuj parametry alertu, wybierając wartości w **operatorze**, **wartość progowa**, **stopień szczegółowości agregacji** **, a następnie** wybierz pozycję **gotowe**.

   Metryki ruch wychodzący, ruch przychodzący i transakcje są wyrażane na minutę, chociaż są obsługiwane ruch wychodzący, ruch przychodzący i operacje we/wy na sekundę. W związku z tym na przykład, jeśli przychodzący ruch wychodzący jest 90 &nbsp; mebibytes na sekundę (MIB/s) i chcesz, aby próg miał wartość 80 &nbsp; procent zainicjowanych danych wychodzących, wybierz następujące parametry alertu: 
   - Dla **wartości progowej**: *75497472* 
   - For — **operator**: *większe niż lub równe*
   - Dla **typu agregacji**: *średnia*
   
   W zależności od tego, jak zakłócenia ma być alert, można również wybrać wartości dla **szczegółowości agregacji** oraz **częstotliwość obliczania**. Na przykład jeśli chcesz, aby Twój alert zaobserwuj średnią ruch przychodzący w okresie wynoszącym 1 godzinę, i chcesz, aby reguła alertów była uruchamiana co godzinę, wybierz następujące pozycje:
   - W celu uzyskania **stopnia szczegółowości agregacji**: *1 godzina*
   - Aby uzyskać **częstotliwość oceny**: *1 godzina*

9. Wybierz pozycję **Dodaj grupy akcji**, a następnie Dodaj grupę akcji (na przykład wiadomości E-mail lub SMS) do alertu przez wybranie istniejącej grupy akcji lub utworzenie nowej.
10. Wprowadź szczegóły alertu, takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
11. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    > [!NOTE]
    > - Aby otrzymywać powiadomienia o tym, że udział plików w warstwie Premium jest bliski ograniczenia *ze względu na zainicjowanie obsługi* ruchu przychodzącego, postępuj zgodnie z powyższymi instrukcjami, ale z następującymi zmianami:
    >    - W kroku 5 **Wybierz metrykę** transferu danych przychodzących zamiast **ruchu** wychodzącego.
    >
    > - Aby otrzymywać powiadomienia o tym, że udział plików w warstwie Premium jest bliski ograniczania *z powodu zainicjowanych operacji wejścia/wyjścia*, postępuj zgodnie z powyższymi instrukcjami, ale z następującymi zmianami:
    >    - W kroku 5 Wybierz metrykę **transakcji** zamiast **ruchu** wychodzącego.
    >    - W kroku 10 jedyną opcją dla opcji **typ agregacji** jest *Suma*. W związku z tym wartość progowa zależy od wybranej szczegółowości agregacji. Na przykład jeśli wartość progowa ma wynosić 80 &nbsp; % liczby operacji we/wy na sekundę, a wybrano *1 godzinę* dla **stopnia szczegółowości agregacji**, **wartość progowa** będzie liczba operacji we/wy na sekundę (w bajtach) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Zobacz też
- [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Często zadawane pytania dotyczące usługi Azure Files](storage-files-faq.md)

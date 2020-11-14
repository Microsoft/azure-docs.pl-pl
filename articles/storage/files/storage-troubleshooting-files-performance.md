---
title: Przewodnik rozwiązywania problemów z wydajnością udziałów plików platformy Azure
description: Rozwiązywanie znanych problemów z wydajnością przy użyciu udziałów plików platformy Azure. Odkryj potencjalne przyczyny i powiązane obejścia w przypadku napotkania tych problemów.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 3e6490babb5a4e68c1ecd931251ea4eb99d6c3f5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630145"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Rozwiązywanie problemów z wydajnością udziałów plików platformy Azure

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure. Zapewnia potencjalne przyczyny i obejścia w przypadku wystąpienia tych problemów.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Duże opóźnienia, niska przepływność i ogólne problemy z wydajnością

### <a name="cause-1-share-was-throttled"></a>Przyczyna 1: udział został ograniczony

Żądania są ograniczane, gdy osiągnięto limity operacji we/wy na sekundę (IOPS), ruch przychodzący lub wychodzący dla udziału plików. Aby zrozumieć limity dla udziałów plików w warstwach Standardowa i Premium, zobacz [elementy docelowe udziałów plików i skalowania plików](./storage-files-scale-targets.md#file-share-and-file-scale-targets).

Aby potwierdzić, że Twój udział jest ograniczany, możesz uzyskać dostęp do metryk platformy Azure w portalu i korzystać z nich.

1. W Azure Portal przejdź do konta magazynu.

1. W okienku po lewej stronie w obszarze **monitorowanie** wybierz pozycję **metryki**.

1. Wybierz pozycję **plik** jako przestrzeń nazw metryki dla zakresu konta magazynu.

1. Wybierz **transakcje** jako metrykę.

1. Dodaj filtr dla **typu odpowiedzi** , a następnie sprawdź, czy jakieś żądania mają jeden z następujących kodów odpowiedzi:
   * **SuccessWithThrottling** : dla bloku komunikatów serwera (SMB)
   * **ClientThrottlingError** : dla REST

   ![Zrzut ekranu przedstawiający opcje metryk dla udziałów plików w warstwie Premium, z uwzględnieniem filtru właściwości "typ odpowiedzi".](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > Aby odebrać alert, zobacz sekcję ["jak utworzyć alert, jeśli udział plików jest ograniczany"](#how-to-create-an-alert-if-a-file-share-is-throttled) w dalszej części tego artykułu.

### <a name="solution"></a>Rozwiązanie

- Jeśli używasz standardowego udziału plików, Włącz [duże udziały plików](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) na koncie magazynu. Duże udziały plików obsługują do 10 000 operacji we/wy na udział.
- W przypadku korzystania z udziału plików w warstwie Premium Zwiększ rozmiar udostępnianego udziału plików, aby zwiększyć limit operacji we/wy. Aby dowiedzieć się więcej, zobacz sekcję "informacje o aprowizacji dla udziałów plików w warstwie Premium" w [przewodniku planowania Azure Files](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

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

Maszyna wirtualna klienta (VM) może znajdować się w innym regionie niż udział plików.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyny wirtualnej znajdującej się w tym samym regionie co udział plików.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nie może osiągnąć maksymalnej przepływności obsługiwanej przez sieć

### <a name="cause"></a>Przyczyna
Jedną z potencjalnych przyczyn jest brak obsługi protokołu SMB w wielu kanałach. Obecnie Azure Files obsługuje tylko jeden kanał, więc istnieje tylko jedno połączenie z maszyny wirtualnej klienta do serwera. To pojedyncze połączenie jest oznaczane pojedynczym rdzeniem na maszynie wirtualnej klienta, dzięki czemu maksymalna przepływność osiągalna z maszyny wirtualnej jest powiązana z jednym rdzeniem.

### <a name="workaround"></a>Obejście

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

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczany

1. W Azure Portal przejdź do konta magazynu.
1. W sekcji **monitorowanie** wybierz pozycję **alerty** , a następnie wybierz pozycję **Nowa reguła alertu**.
1. Wybierz pozycję **Edytuj zasób** , wybierz **Typ zasobu pliku** dla konta magazynu, a następnie wybierz pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to *contoso* , wybierz zasób contoso/File.
1. Wybierz pozycję **Wybierz warunek** , aby dodać warunek.
1. Na liście sygnałów, które są obsługiwane dla konta magazynu, wybierz metrykę **transakcji** .
1. W okienku **Konfigurowanie logiki sygnału** na liście rozwijanej **Nazwa wymiaru** wybierz pozycję **Typ odpowiedzi**.
1. Z listy rozwijanej **wartości wymiaru** wybierz pozycję **SuccessWithThrottling** (dla protokołu SMB) lub **ClientThrottlingError** (dla opcji REST).

   > [!NOTE]
   > Jeśli nie zostanie wyświetlona wartość **SuccessWithThrottling** ani **ClientThrottlingError** wartości wymiaru, oznacza to, że zasób nie został ograniczony. Aby dodać wartość wymiaru, obok listy rozwijanej **wartości wymiaru** wybierz pozycję **Dodaj wartość niestandardową** , wprowadź **SuccessWithThrottling** lub **ClientThrottlingError** , wybierz pozycję **OK** , a następnie powtórz krok 7.

1. Z listy rozwijanej **Nazwa wymiaru** wybierz pozycję **udział plików**.
1. Z listy rozwijanej **wartości wymiaru** wybierz udział plików lub udziały, w których chcesz utworzyć alert.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, zaznacz **wszystkie bieżące i przyszłe wartości**. Lista rozwijana wartości wymiarów nie ma listy udziałów plików, ponieważ metryki dla udziałów nie są dostępne dla standardowych udziałów plików. Alerty dotyczące ograniczania przepustowości dla standardowych udziałów plików są wyzwalane, jeśli jakikolwiek udział plików w ramach konta magazynu zostanie ograniczony, a alert nie wskazuje, który udział plików został ograniczony. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecamy użycie jednego udziału plików na konto magazynu.

1. Zdefiniuj parametry alertu przez wprowadzenie **wartości progowej** , **operatora** , **stopnia szczegółowości agregacji** i **częstotliwości oceny** , a następnie wybierz pozycję **gotowe**.

    > [!TIP]
    > Jeśli używasz progu statycznego, wykres metryk może pomóc w określeniu rozsądnej wartości progowej, jeśli udział plików jest obecnie ograniczany. Jeśli jest używany próg dynamiczny, wykres metryki wyświetla obliczone progi na podstawie ostatnich danych.

1. Wybierz pozycję **Wybierz grupę akcji** , a następnie Dodaj grupę akcji (na przykład wiadomości E-mail lub SMS) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
1. Wprowadź szczegóły alertu, takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
1. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Tworzenie alertów, jeśli udział plików w warstwie Premium jest trendem w kierunku ograniczenia przepustowości

1. W Azure Portal przejdź do konta magazynu.
1. W sekcji **monitorowanie** wybierz pozycję **alerty** , a następnie wybierz pozycję **Nowa reguła alertu**.
1. Wybierz pozycję **Edytuj zasób** , wybierz **Typ zasobu pliku** dla konta magazynu, a następnie wybierz pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to *contoso* , wybierz zasób contoso/File.
1. Wybierz pozycję **Wybierz warunek** , aby dodać warunek.
1. Na liście sygnałów, które są obsługiwane dla konta magazynu, wybierz metrykę **ruchu** wychodzącego.

   > [!NOTE]
   > Należy utworzyć trzy osobne alerty, aby otrzymywać alerty, gdy wartości przychodzące, wychodzące lub transakcje przekroczą ustawione progi. Wynika to z faktu, że alert jest wyzwalany tylko wtedy, gdy wszystkie warunki są spełnione. Jeśli na przykład umieścisz wszystkie warunki w jednym alercie, będziesz otrzymywać alerty tylko wtedy, gdy ruch przychodzący, wychodzący i transakcje przekraczają ich wartości progowe.

1. Przewiń w dół. Z listy rozwijanej **Nazwa wymiaru** wybierz pozycję **udział plików**.
1. Z listy rozwijanej **wartości wymiaru** wybierz udział plików lub udziały, w których chcesz utworzyć alert.
1. Zdefiniuj parametry alertu, wybierając wartości w **operatorze** , **wartość progowa** , **stopień szczegółowości agregacji** **, a następnie** wybierz pozycję **gotowe**.

   Metryki ruch wychodzący, ruch przychodzący i transakcje są wyrażane na minutę, chociaż są obsługiwane ruch wychodzący, ruch przychodzący i operacje we/wy na sekundę. W związku z tym na przykład, jeśli przychodzący ruch wychodzący jest 90 &nbsp; mebibytes na sekundę (MIB/s) i chcesz, aby próg miał wartość 80 &nbsp; procent zainicjowanych danych wychodzących, wybierz następujące parametry alertu: 
   - Dla **wartości progowej** : *75497472* 
   - For — **operator** : *większe niż lub równe*
   - Dla **typu agregacji** : *średnia*
   
   W zależności od tego, jak zakłócenia ma być alert, można również wybrać wartości dla **szczegółowości agregacji** oraz **częstotliwość obliczania**. Na przykład jeśli chcesz, aby Twój alert zaobserwuj średnią ruch przychodzący w okresie wynoszącym 1 godzinę, i chcesz, aby reguła alertów była uruchamiana co godzinę, wybierz następujące pozycje:
   - W celu uzyskania **stopnia szczegółowości agregacji** : *1 godzina*
   - Aby uzyskać **częstotliwość oceny** : *1 godzina*

1. Wybierz pozycję **Wybierz grupę akcji** , a następnie Dodaj grupę akcji (na przykład wiadomości E-mail lub SMS) do alertu przez wybranie istniejącej grupy akcji lub utworzenie nowej.
1. Wprowadź szczegóły alertu, takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
1. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    > [!NOTE]
    > - Aby otrzymywać powiadomienia o tym, że udział plików w warstwie Premium jest bliski ograniczenia *ze względu na zainicjowanie obsługi* ruchu przychodzącego, postępuj zgodnie z powyższymi instrukcjami, ale z następującymi zmianami:
    >    - W kroku 5 **Wybierz metrykę** transferu danych przychodzących zamiast **ruchu** wychodzącego.
    >
    > - Aby otrzymywać powiadomienia o tym, że udział plików w warstwie Premium jest bliski ograniczania *z powodu zainicjowanych operacji wejścia/wyjścia* , postępuj zgodnie z powyższymi instrukcjami, ale z następującymi zmianami:
    >    - W kroku 5 Wybierz metrykę **transakcji** zamiast **ruchu** wychodzącego.
    >    - W kroku 10 jedyną opcją dla opcji **typ agregacji** jest *Suma*. W związku z tym wartość progowa zależy od wybranej szczegółowości agregacji. Na przykład jeśli wartość progowa ma wynosić 80 &nbsp; % liczby operacji we/wy na sekundę, a wybrano *1 godzinę* dla **stopnia szczegółowości agregacji** , **wartość progowa** będzie liczba operacji we/wy na sekundę (w bajtach) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Zobacz też
- [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Często zadawane pytania dotyczące usługi Azure Files](storage-files-faq.md)

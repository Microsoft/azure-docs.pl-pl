---
title: Przewodnik rozwiązywania problemów dotyczących wydajności Azure Files
description: Rozwiązywanie znanych problemów z wydajnością przy użyciu udziałów plików platformy Azure. Odkryj potencjalne przyczyny i powiązane obejścia w przypadku napotkania tych problemów.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 1c0d7e5c7c021f8cdad8980bd7659d819b85f899
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905018"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Rozwiązywanie problemów z wydajnością Azure Files

W tym artykule wymieniono niektóre typowe problemy związane z udziałami plików platformy Azure. Zapewnia potencjalne przyczyny i obejścia w przypadku napotkania tych problemów.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Duże opóźnienia, niska przepływność i ogólne problemy z wydajnością

### <a name="cause-1-share-experiencing-throttling"></a>Przyczyna 1: udostępnianie z ograniczeniami

Domyślny limit przydziału dla udziału w warstwie Premium to 100 GiB, który 100 zapewnia liczbę operacji wejścia/wyjścia na sekundę (z możliwością przekroczenia do 300 przez godzinę). Aby uzyskać więcej informacji o aprowizacji i jej relacji z wieloma operacjami we/wy, zobacz sekcję udostępniane [udziały](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) w przewodniku planowania.

Aby potwierdzić, że Twój udział jest ograniczany, możesz skorzystać z metryk platformy Azure w portalu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** , a następnie wyszukaj **metryki**.

1. Wybierz pozycję **Metryki**.

1. Wybierz swoje konto magazynu jako zasób.

1. Wybierz pozycję **plik** jako przestrzeń nazw metryki.

1. Wybierz **transakcje** jako metrykę.

1. Dodaj filtr dla elementu **responsetype** i sprawdź, czy jakieś żądania mają kod odpowiedzi **SuccessWithThrottling** (dla protokołu SMB) lub **ClientThrottlingError** (dla REST).

![Opcje metryk dla udziałów plików Premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Aby otrzymać Alert, jeśli udział plików jest ograniczany, zobacz [jak utworzyć alert, jeśli udział plików jest ograniczany](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Rozwiązanie

- Zwiększ pojemność udostępniania udziałów, określając wyższy limit przydziału w udziale.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Przyczyna 2: duże obciążenie metadanych/przestrzeni nazw

Jeśli większość żądań jest skoncentrowana na metadanych, (na przykład onfile/OpenFile/CloseFile/QueryInfo/querydirectory), opóźnienie będzie gorszyć w porównaniu do operacji odczytu i zapisu.

Aby sprawdzić, czy większość żądań jest skoncentrowana na metadanych, można użyć tych samych kroków jak powyżej. Oprócz dodawania filtru dla elementu **responsetype**Dodaj filtr dla **nazwy interfejsu API**.

![Filtrowanie nazw interfejsów API w metrykach](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Obejście

- Sprawdź, czy aplikacja może zostać zmodyfikowana, aby zmniejszyć liczbę operacji na metadanych.
- Dodaj wirtualny dysk twardy w udziale plików i zainstaluj dysk VHD za pośrednictwem protokołu SMB z poziomu klienta, aby wykonywać operacje na plikach na danych. Ta metoda działa w przypadku jednego składnika zapisywania i wielu scenariuszy czytelników oraz umożliwia lokalne wykonywanie operacji na metadanych, oferując wydajność podobną do magazynu lokalnego dołączonego bezpośrednio.

### <a name="cause-3-single-threaded-application"></a>Przyczyna 3: aplikacja jednowątkowa

Jeśli aplikacja używana przez klienta jest jednowątkowa, może to znacząco obniżyć liczbę operacji we/wy na sekundę na podstawie rozmiaru udostępnionego udziału.

### <a name="solution"></a>Rozwiązanie

- Zwiększenie równoległości aplikacji przez zwiększenie liczby wątków.
- Przejdź do aplikacji, w których możliwa jest równoległość. Na przykład w przypadku operacji kopiowania klienci mogą korzystać z AzCopy lub RoboCopy z klientów systemu Windows lub z polecenia **Parallel** na klientach z systemem Linux.

## <a name="very-high-latency-for-requests"></a>Bardzo duże opóźnienie dla żądań

### <a name="cause"></a>Przyczyna

Maszyna wirtualna klienta może znajdować się w innym regionie niż udział plików.

### <a name="solution"></a>Rozwiązanie

- Uruchom aplikację z maszyny wirtualnej znajdującej się w tym samym regionie co udział plików.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nie może osiągnąć maksymalnej przepływności obsługiwanej przez sieć

Jedną z potencjalnych przyczyn tego problemu jest brak obsługi wielu kanałów SMB. Obecnie udziały plików platformy Azure obsługują tylko jeden kanał, więc istnieje tylko jedno połączenie z maszyny wirtualnej klienta do serwera. To pojedyncze połączenie jest oznaczane pojedynczym rdzeniem na maszynie wirtualnej klienta, dzięki czemu maksymalna przepływność osiągalna z maszyny wirtualnej jest powiązana z jednym rdzeniem.

### <a name="workaround"></a>Obejście

- Uzyskanie maszyny wirtualnej z większym rdzeniem może pomóc w zwiększeniu przepływności.
- Uruchomienie aplikacji klienckiej z wielu maszyn wirtualnych spowoduje zwiększenie przepływności.

- Jeśli to możliwe, Użyj interfejsów API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Przepływność na klientach z systemem Linux jest znacznie mniejsza w porównaniu z klientami systemu Windows.

### <a name="cause"></a>Przyczyna

Jest to znany problem z implementacją klienta SMB w systemie Linux.

### <a name="workaround"></a>Obejście

- Rozłożenie obciążenia na wiele maszyn wirtualnych.
- Na tej samej maszynie wirtualnej Użyj wielu punktów instalacji z opcją **nosharesock** i rozłożyć obciążenie na te punkty instalacji.
- W systemie Linux spróbuj zainstalować przy użyciu opcji **nostrictsync** , aby uniknąć wymuszania opróżniania SMB dla każdego wywołania **fsync** . W przypadku Azure Files ta opcja nie zakłóca spójności danych, ale może spowodować powstanie starych metadanych plików na liście katalogów (polecenie**ls-l** ). Bezpośrednie zapytanie o metadane pliku (**stat** polecenie) zwróci najbardziej aktualne metadane pliku.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Duże opóźnienia w przypadku dużych obciążeń metadanych obejmujących liczne operacje otwierania/zamykania.

### <a name="cause"></a>Przyczyna

Brak obsługi dzierżaw katalogów.

### <a name="workaround"></a>Obejście

- Jeśli to możliwe, unikaj nadmiernego dojścia otwierającego/zamykającego w tym samym katalogu w krótkim czasie.
- W przypadku maszyn wirtualnych z systemem Linux Zwiększ limit czasu pamięci podręcznej wpisów w katalogu, określając **actimeo = \<sec> ** jako opcję instalacji. Domyślnie jest to jedna sekunda, więc większa wartość, taka jak trzy lub pięć, może pomóc.
- W przypadku maszyn wirtualnych z systemem Linux Uaktualnij jądro do wersji 4,20 lub nowszej.

## <a name="low-iops-on-centosrhel"></a>Niska liczba operacji we/wy na sekundę w CentOS/RHEL

### <a name="cause"></a>Przyczyna

Głębokość we/wy większa niż 1 nie jest obsługiwana w przypadku CentOS/RHEL.

### <a name="workaround"></a>Obejście

- Uaktualnij do wersji CentOS 8/RHEL 8.
- Zmień na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Kopiowanie plików do i z Azure Files w systemie Linux

Jeśli występują wolne kopiowanie plików do i z Azure Files, zapoznaj się z sekcją " [wolne kopiowanie plików do i z Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) " w przewodniku rozwiązywania problemów z systemem Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Wzorzec "wahania/Piła-Tooth" dla operacji we/wy na sekundę

### <a name="cause"></a>Przyczyna

Aplikacja kliencka stale przekracza liczbę operacji wejścia/wyjścia na sekundę. Obecnie nie ma możliwości obciążania żądania po stronie usługi, więc jeśli klient przekroczy liczbę operacji wejścia/wyjścia na sekundę, zostanie on ograniczony przez usługę. Ograniczanie wydajności może spowodować, że klient napotyka wzorzec IOPS "wahania/Piła-Tooth". W takim przypadku średnia liczba operacji we/wy osiągniętych przez klienta może być mniejsza niż liczba operacji we/wy na sekundę.

### <a name="workaround"></a>Obejście

- Zmniejsz obciążenie żądaniami z aplikacji klienckiej, aby udział nie został ograniczony.
- Zwiększ limit przydziału udziału, aby udział nie został ograniczony.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadmierne wywołania DirectoryOpen/DirectoryClose

### <a name="cause"></a>Przyczyna

Jeśli liczba wywołań DirectoryOpen/DirectoryClose jest między najpopularniejszymi wywołaniami interfejsu API i nie oczekuje się, że klient nie będzie mógł nawiązywać wielu wywołań, może to być problem z oprogramowaniem antywirusowym zainstalowanym na maszynie wirtualnej klienta platformy Azure.

### <a name="workaround"></a>Obejście

- Rozwiązanie tego problemu jest dostępne w [aktualizacji platformy w kwietniu dla systemu Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Tworzenie pliku jest wolniejsze niż oczekiwano

### <a name="cause"></a>Przyczyna

Obciążenia, które opierają się na tworzeniu dużej liczby plików, nie będą widzieć znaczącej różnicy między wydajnością udziałów plików w warstwie Premium a standardowymi udziałami plików.

### <a name="workaround"></a>Obejście

- Brak.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niska wydajność z Windows 8.1 lub serwera 2012 R2

### <a name="cause"></a>Przyczyna

Większe niż oczekiwane opóźnienie dostępu Azure Files do obciążeń intensywnie korzystających z operacji we/wy.

### <a name="workaround"></a>Obejście

- Zainstaluj dostępną [poprawkę](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczany

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **Monitoruj**. 

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**.

3. Kliknij pozycję **Wybierz** , aby wybrać **konto magazynu/zasób pliku** zawierający udział plików, na którym chcesz utworzyć alert, a następnie kliknij pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to contoso, wybierz zasób contoso/File.

4. Kliknij przycisk **Dodaj** , aby dodać warunek.

5. Zostanie wyświetlona lista sygnałów obsługiwanych przez konto magazynu, wybierz metrykę **transakcji** .

6. W bloku **Konfigurowanie logiki sygnału** przejdź do wymiaru **Typ odpowiedzi** , kliknij listę rozwijaną **wartości wymiaru** i wybierz pozycję **SuccessWithThrottling** (dla protokołu SMB) lub **ClientThrottlingError** (dla opcji REST). 

  > [!NOTE]
  > Jeśli wartość wymiaru SuccessWithThrottling lub ClientThrottlingError nie znajduje się na liście, oznacza to, że zasób nie został ograniczony.  Aby dodać wartość wymiaru, kliknij **+** obok listy rozwijanej **wartości wymiaru** , wpisz **SuccessWithThrottling** lub **ClientThrottlingError**, kliknij przycisk **OK** , a następnie powtórz krok #6.

7. Przejdź do wymiaru **udział plików** , kliknij listę rozwijaną **wartości wymiaru** i wybierz udziały plików, dla których chcesz utworzyć alert. 

  > [!NOTE]
  > Jeśli udział plików jest standardowym udziałem plików, listy rozwijane wartości wymiarów będą puste, ponieważ metryki dla udziałów nie są dostępne w przypadku plików w warstwie Standardowa. Alerty dotyczące ograniczania przepustowości dla standardowych udziałów plików będą wyzwalane, jeśli jakikolwiek udział plików w ramach konta magazynu zostanie ograniczony, a alert nie określi, który udział plików został ograniczony. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecenie ma mieć jeden udział plików na konto magazynu. 

8. Zdefiniuj **Parametry alertu** (próg, operator, stopień szczegółowości i częstotliwość agregacji), które są używane do obliczania reguły alertu metryki, a następnie kliknij przycisk **gotowe**.

  > [!TIP]
  > Jeśli jest używany próg statyczny, wykres metryk może pomóc w ustaleniu rozsądnego progu, jeśli udział plików jest obecnie ograniczany. Jeśli używasz progu dynamicznego, wykres metryki wyświetli obliczone progi na podstawie ostatnich danych.

9. Dodaj **grupę akcji** (wiadomości e-mail, wiadomości SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

10. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.

11. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

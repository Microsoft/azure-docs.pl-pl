---
title: Inspekcja pakietów przy użyciu usługi Azure Network Watcher | Microsoft Docs
description: W tym artykule opisano sposób użycia usługi Network Watcher do przeprowadzenia głębokiej inspekcji pakietów zebranych z maszyny wirtualnej
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011173"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspekcja pakietów przy użyciu usługi Azure Network Watcher

Korzystając z funkcji przechwytywania pakietów Network Watcher, można inicjować i zarządzać sesjami przechwytywania na maszynach wirtualnych platformy Azure z portalu, programu PowerShell, interfejsu wiersza polecenia i programowo za pośrednictwem zestawu SDK i interfejsu API REST. Przechwytywanie pakietów umożliwia rozwiązanie scenariuszy, które wymagają danych na poziomie pakietu, dostarczając informacje w formacie, który można łatwo użyć. Korzystając z dostępnych bezpłatnie narzędzi do inspekcji danych, możesz sprawdzić, czy komunikacja jest wysyłana do i z maszyn wirtualnych oraz uzyskać wgląd w ruch sieciowy. Oto przykładowe zastosowania danych przechwytywania pakietów: Badanie problemów z siecią lub aplikacją, wykrywanie nieprawidłowego użycia sieci i próby włamania lub zachowanie zgodności z przepisami. W tym artykule pokazano, jak otworzyć plik przechwytywania pakietu dostarczony przez Network Watcher przy użyciu popularnego narzędzia typu open source. Podajemy również przykłady pokazujące, jak obliczyć opóźnienie połączenia, zidentyfikować nietypowy ruch i sprawdzić statystykę sieci.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł przechodzi przez niektóre wstępnie skonfigurowane scenariusze dotyczące funkcji przechwytywania pakietów, która została wcześniej uruchomiona. Te scenariusze ilustrują możliwości, do których można uzyskać dostęp, przeglądając przechwycenie pakietu. W tym scenariuszu program [Wireshark](https://www.wireshark.org/) jest wykorzystywany do inspekcji przechwytywania pakietów.

W tym scenariuszu założono, że uruchomiono już funkcję przechwytywania pakietów na maszynie wirtualnej. Aby dowiedzieć się, jak utworzyć funkcję przechwytywania pakietów, przejdź do [strony Zarządzanie pakietami przechwycenia pakietu przy użyciu portalu](network-watcher-packet-capture-manage-portal.md) lub REST, odwiedzając [Zarządzanie przechwytywaniem pakietów przy użyciu interfejsu API REST](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu:

* Przejrzyj przechwycenie pakietu

## <a name="calculate-network-latency"></a>Oblicz opóźnienie sieci

W tym scenariuszu pokazujemy, jak wyświetlić początkowy czas błądzenia (RTT) konwersacji Transmission Control Protocol (TCP) występujący między dwoma punktami końcowymi.

Po nawiązaniu połączenia TCP pierwsze trzy pakiety wysyłane w ramach połączenia są zgodne ze wzorcem często określanym jako uzgadnianie trzykrotne. Badając pierwsze dwa pakiety wysyłane w ramach tego uzgadniania, początkowe żądanie od klienta i odpowiedź z serwera, możemy obliczyć opóźnienie, gdy to połączenie zostało nawiązane. To opóźnienie jest określane jako czas błądzenia (RTT). Aby uzyskać więcej informacji na temat protokołu TCP i uzgadniania 3-kierunkowego, zapoznaj się z poniższym zasobem. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Krok 1

Uruchom aplikację WireShark

### <a name="step-2"></a>Krok 2

Załaduj plik **Cap** z przechwytywania pakietu. Ten plik można znaleźć w obiekcie blob, który został zapisany lokalnie na maszynie wirtualnej, w zależności od tego, jak został on skonfigurowany.

### <a name="step-3"></a>Krok 3

Aby wyświetlić początkowy czas błądzenia (RTT) w konwersacjach TCP, będziemy przeglądać tylko pierwsze dwa pakiety, które są objęte uzgadnianiem protokołu TCP. Będziemy używać pierwszych dwóch pakietów w uzgadnianiu trójwymiarowym, które są pakietami [SYN], [SYN, ACK]. Są one nazwane dla flag ustawionych w nagłówku TCP. Ostatni pakiet z uzgadniania, pakiet [ACK] nie będzie używany w tym scenariuszu. Pakiet [SYN] jest wysyłany przez klienta. Po otrzymaniu serwer wysyła pakiet [ACK] jako potwierdzenie odebrania SYN z klienta. Korzystając z faktu, że odpowiedź serwera wymaga bardzo małego obciążenia, obliczamy czas RTT przez odjęcie czasu, przez który pakiet [SYN, ACK] został odebrany przez klienta przez klienta.

Przy użyciu programu WireShark ta wartość jest obliczana dla nas.

Aby łatwiej wyświetlić pierwsze dwa pakiety w ramach uzgadniania TCP, firma Microsoft będzie korzystać z funkcji filtrowania dostępnej w programie WireShark.

Aby zastosować filtr w programie WireShark, rozwiń segment "Transmission Control Protocol" pakietu [SYN] w przechwyceniu i sprawdź flagi ustawione w nagłówku TCP.

Ponieważ chcemy przefiltrować wszystkie pakiety [SYN] i [SYN, ACK], w obszarze flagi upewnij się, że bit syn został ustawiony na 1, a następnie kliknij prawym przyciskiem myszy bit syn-> Zastosuj jako wybrany filtr->.

![Rysunek 7][7]

### <a name="step-4"></a>Krok 4

Po przefiltrowaniu okna tak, aby były widoczne tylko pakiety z zestawem bitowym [SYN], możesz łatwo wybrać konwersacje, które interesują Cię, aby wyświetlić początkowy czas RTT. Prosty sposób wyświetlania RTT w programie WireShark wystarczy kliknąć listę rozwijaną z analizą "SEQ/ACK". Zobaczysz wyświetlony czas RTT. W takim przypadku RTT miał 0,0022114 sekund lub 2,211 MS.

![Rysunek 8.][8]

## <a name="unwanted-protocols"></a>Niechciane protokoły

W wystąpieniu maszyny wirtualnej wdrożonej na platformie Azure można uruchamiać wiele aplikacji. Wiele z tych aplikacji komunikuje się za pośrednictwem sieci, prawdopodobnie bez jawnego uprawnienia. Korzystając z funkcji przechwytywania pakietów do przechowywania komunikacji sieciowej, możemy zbadać, jak działa aplikacja w sieci i wyszukać wszelkie problemy.

W tym przykładzie przeglądamy poprzedni uruchomioną funkcję przechwytywania pakietów dla niechcianych protokołów, które mogą wskazywać nieautoryzowaną komunikację z aplikacji uruchomionej na komputerze.

### <a name="step-1"></a>Krok 1

Korzystanie z tego samego przechwycenia w poprzednim scenariuszu kliknij  >  **hierarchię protokołu** statystyk

![menu hierarchii protokołów][2]

Zostanie wyświetlone okno hierarchia protokołów. Ten widok zawiera listę wszystkich protokołów, które były używane podczas sesji przechwytywania oraz liczbę pakietów przesyłanych i odbieranych przy użyciu protokołów. Ten widok może być przydatny do znajdowania niechcianego ruchu sieciowego na maszynach wirtualnych lub w sieci.

![Hierarchia protokołu otwarta][3]

Jak widać na poniższym zrzucie ekranu, nastąpił ruch przy użyciu protokołu BitTorrent, który jest używany do udostępniania plików równorzędnych. Jako administrator nie oczekuje się, że na tych maszynach wirtualnych jest wyświetlany ruch BitTorrent. Teraz znasz ten ruch, możesz usunąć równorzędne oprogramowanie, które zostało zainstalowane na tej maszynie wirtualnej, lub zablokować ruch przy użyciu sieciowych grup zabezpieczeń lub zapory. Ponadto można wybrać uruchamianie przechwytywania pakietów zgodnie z harmonogramem, dzięki czemu można regularnie przejrzeć użycie protokołu na maszynach wirtualnych. Przykład sposobu automatyzowania zadań sieciowych na platformie Azure można znaleźć w temacie [monitorowanie zasobów sieciowych za pomocą usługi Azure Automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Znajdowanie najważniejszych miejsc docelowych i portów

Informacje o typach ruchu, punktach końcowych i portach przekazywanych przez program są ważne podczas monitorowania lub rozwiązywania problemów z aplikacjami i zasobami w sieci. Korzystając z powyższego pliku przechwytywania pakietów, możemy szybko poznać najpopularniejsze miejsca docelowe komunikacji z maszyną wirtualną oraz używane porty.

### <a name="step-1"></a>Krok 1

Przy użyciu tego samego przechwycenia w poprzednim scenariuszu kliknij pozycję **statystyki**  >  **IPv4**  >  **miejsca docelowe i porty**

![okno przechwytywania pakietów][4]

### <a name="step-2"></a>Krok 2

Po przeszukaniu wyników zostanie wyświetlona linia z wieloma połączeniami na porcie 111. Najbardziej używanym portem był 3389, który jest pulpitem zdalnym, a pozostałe są portami dynamicznymi RPC.

Ten ruch może oznaczać brak nic, ale jest portem używanym do wielu połączeń i jest nieznany dla administratora.

![Rysunek 5][5]

### <a name="step-3"></a>Krok 3

Po ustaleniu portu poza miejscem możemy odfiltrować przechwycenie w oparciu o port.

Filtr w tym scenariuszu będzie:

```
tcp.port == 111
```

Wprowadzimy tekst filtru z powyżej w polu tekstowym filtru i trafimy klawisz ENTER.

![Rysunek 6.][6]

Wyniki można zobaczyć, że cały ruch pochodzi z lokalnej maszyny wirtualnej w tej samej podsieci. Jeśli nadal nie rozumiemy, dlaczego ten ruch występuje, możemy dalej sprawdzić pakiety, aby określić, dlaczego te wywołania są nawiązywane na porcie 111. Korzystając z tych informacji, możemy podjąć odpowiednie działania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych funkcjach diagnostycznych Network Watcher, odwiedzając [usługę Azure Network monitoring — Omówienie](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














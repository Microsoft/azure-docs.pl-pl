---
title: Przewodnik dotyczący wydajności usługi Azure SignalR Service
description: Przegląd wydajności i testów porównawczych usługi Azure Signal Service. Kluczowe metryki, które należy wziąć pod uwagę podczas planowania pojemności.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74157669"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Przewodnik dotyczący wydajności usługi Azure SignalR Service

Jedną z najważniejszych zalet korzystania z usługi Azure Signal Service jest łatwość skalowania aplikacji sygnalizujących. W scenariuszu o dużej skali wydajność jest ważnym czynnikiem. 

W tym przewodniku wprowadzimy czynniki wpływające na wydajność aplikacji sygnalizującej. Opiszemy typową wydajność w różnych scenariuszach przypadków użycia. Na końcu wprowadzimy środowisko i narzędzia, których można użyć do wygenerowania raportu o wydajności.

## <a name="term-definitions"></a>Definicje terminów

*Przychodzące*: komunikat przychodzący do usługi Azure Signal Service.

*Wychodzące*: komunikat wychodzący z usługi Azure Signal Service.

*Przepustowość*: łączny rozmiar wszystkich komunikatów w 1 s.

*Tryb domyślny*: domyślny tryb roboczy podczas tworzenia wystąpienia usługi Azure Signal instance. Usługa Azure Signal oczekuje, aby serwer aplikacji ustanowił połączenie z nim przed zaakceptowaniem wszelkich połączeń klienta.

*Tryb bezserwerowy*: tryb, w którym usługa sygnałów platformy Azure akceptuje tylko połączenia klienckie. Brak połączenia z serwerem.

## <a name="overview"></a>Omówienie

Usługa Azure Signal definiuje siedem warstw standardowych dla różnych pojemności wydajności. Ten przewodnik odpowiada na następujące pytania:

-   Jaka jest typowa wydajność usługi sygnału Azure dla każdej warstwy?

-   Czy usługa Azure sygnalizująca spełnia wymagania dotyczące przepływności komunikatów (na przykład wysyłania komunikatów 100 000 na sekundę)?

-   Jakie warstwy są odpowiednie dla mojego scenariusza? Lub jak wybrać odpowiednią warstwę?

-   Jakiego rodzaju serwer aplikacji (rozmiar maszyny wirtualnej) jest odpowiedni dla mnie? Ile z nich należy wdrożyć?

Aby odpowiedzieć na te pytania, ten przewodnik najpierw zawiera ogólne wyjaśnienie czynników wpływających na wydajność. Następnie ilustruje maksymalną liczbę komunikatów przychodzących i wychodzących dla każdej warstwy dla typowych przypadków użycia: **echo**, **emisja**, **Wyślij do grupy**i **Wyślij do połączenia** (obsługa komunikacji równorzędnej między elementami równorzędnymi).

Ten przewodnik nie może obejmować wszystkich scenariuszy (i różnych przypadków użycia, rozmiarów komunikatów, wzorców wysyłania komunikatów itp.). Udostępnia ona kilka metod, które ułatwiają:

- Oceń przybliżone wymagania dotyczące komunikatów przychodzących lub wychodzących.
- Znajdź odpowiednie warstwy, sprawdzając tabelę wydajności.

## <a name="performance-insight"></a>Szczegółowe informacje o wydajności

W tej sekcji opisano metodologie oceny wydajności, a następnie wymieniono wszystkie czynniki wpływające na wydajność. Na końcu udostępnia metody, które ułatwiają ocenę wymagań dotyczących wydajności.

### <a name="methodology"></a>Metodologia

*Przepływność* i *opóźnienie* są dwoma typowymi aspektami sprawdzania wydajności. W przypadku usługi Azure Signal każda warstwa SKU ma własne zasady ograniczania przepływności. Zasady definiują *maksymalną dozwoloną przepływność (przepustowość ruchu przychodzącego i wychodzącego)* jako maksymalną osiągniętą przepływność, gdy 99% komunikatów ma opóźnienie mniejsze niż 1 sekunda.

Opóźnienie to przedział czasu z połączenia wysyłającego komunikat w celu odebrania komunikatu odpowiedzi z usługi Azure Signal. Podejmijmy **echo** jako przykład. Każde połączenie z klientem dodaje sygnaturę czasową w komunikacie. Centrum serwera aplikacji wysyła oryginalny komunikat z powrotem do klienta programu. Opóźnienie propagacji jest łatwo obliczane przez każde połączenie z klientem. Sygnatura czasowa jest dołączana do każdej wiadomości w **emisji**, **wysyłania do grupy**i **wysyłania do połączenia**.

Aby symulować tysiące współbieżnych połączeń klienta, w wirtualnej sieci prywatnej na platformie Azure są tworzone wiele maszyn wirtualnych. Wszystkie te maszyny wirtualne łączą się z tym samym wystąpieniem usługi Azure Signal instance.

W domyślnym trybie usługi Azure Signal Machines maszyny wirtualne serwera aplikacji są wdrażane w tej samej wirtualnej sieci prywatnej co maszyny wirtualne klienta. Wszystkie maszyny wirtualne klienta i maszyny wirtualne serwera aplikacji są wdrażane w tej samej sieci tego samego regionu, aby uniknąć opóźnień między regionami.

### <a name="performance-factors"></a>Czynniki wydajności

Teoretycznie pojemność usługi Azure Signal Service jest ograniczona przez zasoby obliczeniowe: procesor, pamięć i sieć. Na przykład więcej połączeń z usługą Azure sygnalizujących powoduje, że usługa używa większej ilości pamięci. W przypadku większego ruchu komunikatów (na przykład każdy komunikat jest większy niż 2 048 bajtów) usługa Azure Signal Service musi poświęcać więcej cyklom procesora CPU na przetwarzanie ruchu. W tym przypadku przepustowość sieci platformy Azure nakłada również limit na maksymalny ruch.

Typ transportu jest innym czynnikiem wpływającym na wydajność. Te trzy typy to między innymi typ [protokołu WebSocket](https://en.wikipedia.org/wiki/WebSocket), [serwer — zdarzenie wysłane](https://en.wikipedia.org/wiki/Server-sent_events)i [długotrwałe sondowanie](https://en.wikipedia.org/wiki/Push_technology). 

Protokół WebSocket jest dwukierunkowym protokołem komunikacji dwubajtowej przez pojedyncze połączenie TCP. Serwer — wysłane — zdarzenie jest jednokierunkowym protokołem do wypychania komunikatów z serwera do klienta. Long-Polling wymaga od klientów okresowego sondowania informacji z serwera przez żądanie HTTP. W przypadku tego samego interfejsu API w tych samych warunkach protokół WebSocket ma najlepszą wydajność, to zdarzenie wysłane przez serwer jest wolniejsze, a Long-Polling jest najwolniejsze. Usługa Azure Signal Service zaleca domyślnie protokół WebSocket.

Koszt routingu wiadomości ogranicza także wydajność. Usługa Azure Signal Service odgrywa rolę jako router komunikatów, która kieruje komunikat z zestawu klientów lub serwerów do innych klientów lub serwerów. Inny scenariusz lub interfejs API wymaga innych zasad routingu. 

W przypadku **ECHA**klient wysyła wiadomość do samego siebie, a miejsce docelowe routingu również należy do siebie. Ten wzorzec ma najniższy koszt routingu. W przypadku **emisji**, **wysyłania do grupy**i **wysyłania do połączenia**usługa Azure Signal Service musi wyszukiwać docelowe połączenia za pomocą wewnętrznej rozproszonej struktury danych. To dodatkowe przetwarzanie zużywa więcej przepustowości procesora, pamięci i sieci. W efekcie wydajność jest wolniejsza.

W trybie domyślnym serwer aplikacji może również stać się wąskim gardłem w niektórych scenariuszach. Zestaw SDK usługi Azure Signal ma wywoływać centrum, podczas gdy utrzymuje połączenie na żywo z każdym klientem za pośrednictwem sygnałów pulsu.

W trybie bezserwerowym klient wysyła komunikat przy użyciu protokołu HTTP POST, który nie jest tak wydajny jak protokół WebSocket.

Innym czynnikiem jest protokół: JSON i [MessagePack](https://msgpack.org/index.html). MessagePack jest mniejsza w rozmiarze i dostarczana szybciej niż JSON. MessagePack może nie zwiększyć wydajności, chociaż. Wydajność usługi Azure Signal Service nie jest uwzględniana w protokołach, ponieważ nie dekoduje ładunku komunikatów podczas przekazywania komunikatów z klientów do serwerów lub odwrotnie.

Podsumowując, następujące czynniki wpływają na wydajność ruchu przychodzącego i wychodzącego:

-   Warstwa SKU (procesor CPU/pamięć)

-   Liczba połączeń

-   Rozmiar komunikatu

-   Szybkość wysyłania komunikatów

-   Typ transportu (protokół WebSocket, wysłane przez serwer — zdarzenie lub długie sondowanie)

-   Scenariusz przypadków użycia (koszt routingu)

-   Połączenia serwera aplikacji i usługi (w trybie serwera)


### <a name="finding-a-proper-sku"></a>Znajdowanie właściwej jednostki SKU

Jak można oszacować wydajność ruchu przychodzącego/wychodzącego lub określić, która warstwa jest odpowiednia dla określonego przypadku użycia?

Załóżmy, że serwer aplikacji jest wystarczająco wydajny i nie jest wąskim gardłem wydajności. Następnie sprawdź maksymalną przepustowość ruchu przychodzącego i wychodzącego dla każdej warstwy.

#### <a name="quick-evaluation"></a>Szybka Ocena

Uprość proces oceny przy założeniu, że niektóre ustawienia domyślne: 

- Typ transportu to WebSocket.
- Rozmiar komunikatu wynosi 2 048 bajtów.
- Wiadomość jest wysyłana co 1 sekundę.
- Usługa Azure Signal Service jest w domyślnym trybie.

Każda warstwa ma własną maksymalną przepustowość ruchu przychodzącego i przepustowość wychodzącą. Bezproblemowe środowisko użytkownika nie jest gwarantowane po przekroczeniu limitu połączenia przychodzącego lub wychodzącego.

**Echo** zapewnia maksymalną przepustowość ruchu przychodzącego, ponieważ ma najniższy koszt routingu. **Broadcast** definiuje maksymalną przepustowość komunikatów wychodzących.

*Nie* Przekrocz wartości wyróżnionych w dwóch poniższych tabelach.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Przepustowość ruchu przychodzącego** | **2 MB/s**    | **4 MB/s**    | **10 MB/s**   | **20 MB/s**    | **40 MB/s**    | **100 MB/s**   | **200 MB/s**    |
| Przepustowość wychodząca | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |


|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Przepustowość ruchu przychodzącego  | 4 KB/s   | 4 KB/s   | 4 KB/s    | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s    |
| **Przepustowość wychodząca** | **4 MB/s**    | **8 MB/s**    | **20 MB/s**    | **40 MB/s**    | **80 MB/s**    | **200 MB/s**    | **400 MB/s**   |

*Przepustowość ruchu przychodzącego* i *przepustowość wychodząca* to całkowity rozmiar komunikatów na sekundę.  Poniżej znajdują się następujące formuły:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: liczba połączeń wysyłających wiadomość.

- *outboundConnections*: liczba połączeń odebranych komunikatem.

- *messageSize*: rozmiar pojedynczego komunikatu (średnia wartość). Mały komunikat o rozmiarze mniejszym niż 1 024 bajtów ma wpływ na wydajność podobny do komunikatu o rozmiarze 1 024-bajtowym.

- *sendInterval*: godzina wysłania jednej wiadomości. Zwykle jest to 1 sekundę na komunikat, co oznacza wysłanie jednej wiadomości co sekundę. Mniejszy interwał oznacza wysyłanie więcej komunikatów w danym okresie czasu. Na przykład 0,5 sekund na komunikat oznacza wysyłanie dwóch komunikatów co sekundę.

- *Połączenia*: zatwierdzono maksymalny próg usługi Azure Signal dla każdej warstwy. Jeśli nastąpi dalsze zwiększenie liczby połączeń, będzie to miało wpływ na ograniczenia połączeń.

#### <a name="evaluation-for-complex-use-cases"></a>Ocena złożonych przypadków użycia

##### <a name="bigger-message-size-or-different-sending-rate"></a>Większy rozmiar wiadomości lub inny wskaźnik wysyłania

Rzeczywisty przypadek użycia jest bardziej skomplikowany. Może wysłać komunikat o rozmiarze większym niż 2 048 bajtów lub szybkość wysyłania komunikatów nie może wynosić jeden komunikat na sekundę. Skorzystajmy z Unit100's emisji jako przykładu, aby dowiedzieć się, jak oszacować swoją wydajność.

W poniższej tabeli przedstawiono rzeczywiste przypadki użycia **emisji**. Jednak rozmiar wiadomości, liczba połączeń i szybkość wysyłania komunikatów różnią się od wartości założono w poprzedniej sekcji. Pytanie to, w jaki sposób można wywnioskować dowolne z tych elementów (rozmiar komunikatu, liczbę połączeń lub szybkość wysyłania komunikatów), jeśli wiemy tylko dwa z nich.

| Emisja  | Rozmiar komunikatu | Komunikaty przychodzące na sekundę | Połączenia | Interwały wysyłania |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 sekund                      |
| 2 | 256 KB               | 1                        | 8000       | 5 sekund                      |

Poniższa formuła jest łatwa do wnioskowania na podstawie poprzedniej formuły:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

W przypadku Unit100 maksymalna przepustowość wychodząca to 400 MB z poprzedniej tabeli. Dla rozmiaru komunikatu o rozmiarze 20 KB Maksymalna liczba połączeń wychodzących powinna wynosić 400 MB \* 5/20 KB = 100 000, co odpowiada wartości rzeczywistej.

##### <a name="mixed-use-cases"></a>Mieszane przypadki użycia

Faktyczny przypadek użycia zwykle Miksuje cztery podstawowe przypadki użycia razem: **echo**, **emisja**, **Wyślij do grupy**i **Wyślij do połączenia**. Metodologia używana do oszacowania pojemności:

1. Podziel mieszane przypadki użycia na cztery podstawowe przypadki użycia.
1. Oblicz maksymalną przepustowość ruchu przychodzącego i wychodzącego, korzystając z poprzednich formuł osobno.
1. Aby uzyskać całkowitą maksymalną przepustowość ruchu przychodzącego/wychodzącego, należy zsumować obliczenia przepustowości. 

Następnie wybierz odpowiednią warstwę z maksymalnymi tabelami przepustowości ruchu przychodzącego/wychodzącego.

> [!NOTE]
> W przypadku wysyłania komunikatu do setek lub tysięcy małych grup lub tysięcy klientów wysyłających wiadomości do siebie koszt routingu będzie miał wartość dominującą. Uwzględnij ten wpływ na konto.

W przypadku użycia w przypadku wysyłania komunikatu do klientów upewnij się, że serwer aplikacji *nie* jest wąskim gardłem. W poniższej sekcji "Analiza przypadku" przedstawiono wskazówki dotyczące liczby potrzebnych serwerów aplikacji oraz liczby połączeń serwera, które należy skonfigurować.

## <a name="case-study"></a>Analiza przypadku

W poniższych sekcjach opisano cztery typowe przypadki użycia na potrzeby transportu protokołu WebSocket: **echo**, **emisja**, **Wyślij do grupy**i **Wyślij do połączenia**. W każdym scenariuszu sekcja zawiera listę bieżących, przychodzących i wychodzących zdolności produkcyjnych usługi Azure Signal Service. Wyjaśniono również główne czynniki wpływające na wydajność.

W trybie domyślnym serwer aplikacji tworzy pięć połączeń z serwerem za pomocą usługi Azure Signal Service. Serwer aplikacji domyślnie używa zestawu SDK usługi Azure Signal. W poniższych wynikach testu wydajności połączenia serwera są zwiększane do 15 (lub więcej na potrzeby rozgłaszania i wysyłania komunikatów do dużej grupy).

Różne przypadki użycia mają różne wymagania dotyczące serwerów aplikacji. **Emisja** wymaga niewielkiej liczby serwerów aplikacji. **Echo** lub **Wyślij do połączenia** potrzebuje wielu serwerów aplikacji.

W przypadku wszystkich przypadków użycia domyślnym rozmiarem komunikatu jest 2 048 bajtów, a interwał wysyłania komunikatu wynosi 1 sekundę.

### <a name="default-mode"></a>Tryb domyślny

Klienci, serwery aplikacji sieci Web i usługa Azure Signal są zaliczane w tryb domyślny. Każdy Klient oznacza jedno połączenie.

#### <a name="echo"></a>Echo

Najpierw aplikacja internetowa nawiązuje połączenie z usługą Azure Signal Service. Następnie wielu klientów nawiązuje połączenie z aplikacją sieci Web, która przekierowuje klientów do usługi Azure Signal Service przy użyciu tokenu dostępu i punktu końcowego. Następnie klienci nawiązują połączenia WebSocket z usługą Azure Signal Service.

Gdy wszyscy klienci nawiązują połączenia, zaczynają wysyłać komunikat zawierający sygnaturę czasową do określonego centrum co sekundę. Centrum wysyła komunikat z powrotem do jego oryginalnego klienta. Każdy klient oblicza czas oczekiwania, gdy odbierze komunikat ECHA z powrotem.

Na poniższym diagramie, od 5 do 8 (czerwony wyróżniony ruch) znajdują się w pętli. Pętla jest uruchamiana przez domyślny czas trwania (5 minut) i pobiera statystykę wszystkich opóźnień komunikatów.

![Ruch dla przypadku użycia ECHA](./media/signalr-concept-performance/echo.png)

Zachowanie **ECHA** określa, że maksymalna przepustowość ruchu przychodzącego jest równa maksymalnej przepustowości wychodzącej. Aby uzyskać szczegółowe informacje, zapoznaj się z poniższą tabelą.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Komunikaty przychodzące/wychodzące na sekundę | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Przepustowość ruchu przychodzącego/wychodzącego | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |

W tym przypadku użycia każdy klient wywołuje centrum zdefiniowane na serwerze aplikacji. Koncentrator po prostu wywołuje metodę zdefiniowaną w oryginalnej stronie klienta. To centrum jest najbardziej lekkim centrum na potrzeby **ECHA**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Nawet w przypadku tego prostego centrum, nacisk na ruch na serwerze aplikacji jest widoczny w miarę wzrostu obciążenia **komunikatów przychodzących.** To ciśnienie ruchu wymaga wielu serwerów aplikacji dla dużych warstw jednostki SKU. W poniższej tabeli wymieniono liczbę serwerów aplikacji dla każdej warstwy.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar komunikatu, szybkość wysyłania komunikatów, warstwa SKU oraz procesor CPU/pamięć serwera aplikacji mają wpływ na ogólną wydajność **ECHA**.

#### <a name="broadcast"></a>Emisja

W przypadku **emisji**, gdy aplikacja sieci Web otrzymuje komunikat, emituje do wszystkich klientów. Im więcej klientów jest emitowanych, tym większy jest ruch komunikatów dla wszystkich klientów. Zapoznaj się z poniższym diagramem.

![Ruch dla przypadku użycia emisji](./media/signalr-concept-performance/broadcast.png)

Zostanie wyemitowana niewielka liczba klientów. Przepustowość komunikatów przychodzących jest mała, ale przepustowość wychodząca jest duża. Przepustowość wiadomości wychodzącej zwiększa się wraz ze wzrostem liczby połączeń klienta lub szybkości emisji.

W poniższej tabeli zestawiono maksymalne połączenia klientów, liczbę komunikatów przychodzących/wychodzących oraz przepustowość.

|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Komunikaty przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Komunikaty wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość ruchu przychodzącego  | 4 KB/s   | 4 KB/s   | 4 KB/s    | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s     |
| Przepustowość wychodząca | 4 MB/s   | 8 MB/s   | 20 MB/s   | 40 MB/s   | 80 MB/s   | 200 MB/s   | 400 MB/s   |

Emituje klientów, którzy publikują komunikaty, nie przekraczają czterech. W porównaniu z **echomi** potrzebują mniejszej liczby serwerów aplikacji, ponieważ długość komunikatu przychodzącego jest mała. Dwa serwery aplikacji są wystarczające dla zagadnień związanych z umową SLA i wydajnością. Należy jednak zwiększyć domyślne połączenia serwera, aby uniknąć nierównowagi, szczególnie w przypadku Unit50 i Unit100.

|   Emisja      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększ liczbę domyślnych połączeń serwera z 5 na 40 na każdym serwerze aplikacji, aby uniknąć możliwych niezrównoważonych połączeń serwera z usługą Azure Signal Service.
>
> Numer połączenia klienta, rozmiar komunikatu, szybkość wysyłania komunikatów i warstwa SKU mają wpływ na ogólną wydajność **emisji**.

#### <a name="send-to-group"></a>Wyślij do grupy

Przypadek użycia **wysyłania do grupy** ma podobny wzorzec ruchu do **emisji**. Różnica polega na tym, że gdy klienci nawiązują połączenia WebSocket z usługą Azure Signal Service, muszą dołączyć do grup, zanim będą mogli wysłać komunikat do określonej grupy. Na poniższym diagramie przedstawiono przepływ ruchu.

![Ruch dla przypadku użycia dla wysyłania do grupy](./media/signalr-concept-performance/sendtogroup.png)

Liczba członków i grup grup to dwa czynniki wpływające na wydajność. Aby uprościć analizę, definiujemy dwa rodzaje grup:

- **Mała grupa**: Każda grupa ma 10 połączeń. Numer grupy jest równy (maksymalna liczba połączeń)/10. Na przykład w przypadku Unit1 liczba połączeń wynosi 1 000, a następnie mamy 1000/10 = 100 grupy.

- **Duża grupa**: Numer grupy jest zawsze 10. Liczba członków grupy jest równa (maksymalna liczba połączeń)/10. Na przykład dla Unit1, jeśli liczba połączeń wynosi 1 000, każda grupa ma 1000/10 = 100 członków.

Wartość **Wyślij do grupy umożliwia** kierowanie kosztów routingu do usługi Azure Signal Service, ponieważ ma ona znaleźć docelowe połączenia za pomocą rozproszonej struktury danych. W miarę zwiększania się liczby połączeń wysyłających zwiększa się koszt.

##### <a name="small-group"></a>Mała Grupa

Koszt routingu jest znaczący dla wysyłania komunikatów do wielu małych grup. Obecnie implementacja usługi sygnałów platformy Azure trafi limit kosztów routingu na Unit50. Dodanie większej liczby procesorów i pamięci nie pomoże, więc Unit100 nie można jeszcze bardziej poprawić. Jeśli potrzebujesz większej przepustowości ruchu przychodzącego, skontaktuj się z pomocą techniczną.

|   Wyślij do małej grupy     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000 | 100 000
| Liczba członków grupy        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Liczba grup               | 100   | 200   | 500    | 1000  | 2000  | 5000  | 10 000 
| Komunikaty przychodzące na sekundę  | 200   | 400   | 1000  | 2500  | 4000  | 7 000  | 7 000   |
| Przepustowość ruchu przychodzącego  | 400 KB/s  | 800 KB/s  | 2 MB/s     | 5 MB/s     | 8 MB/s     | 14 MB/s    | 14 MB/s     |
| Komunikaty wychodzące na sekundę | 2000 | 4000 | 10 000 | 25 000 | 40 000 | 70 000 | 70 000  |
| Przepustowość wychodząca | 4 MB/s    | 8 MB/s    | 20 MB/s    | 50 MB/s     | 80 MB/s    | 140 MB/s   | 140 MB/s    |

Wiele połączeń klientów wywołuje centrum, więc numer serwera aplikacji jest również krytyczny dla wydajności. W poniższej tabeli wymieniono sugerowane liczby serwerów aplikacji.

|  Wyślij do małej grupy   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar komunikatu, szybkość wysyłania komunikatów, koszt routingu, warstwa SKU oraz procesor CPU/pamięć serwera aplikacji mają wpływ na ogólną wydajność **wysyłania do małej grupy**.

##### <a name="big-group"></a>Duża grupa

W przypadku **wysyłania do dużych grup**przepustowość wychodząca jest wąskim gardłem, zanim zostanie osiągnięty limit kosztów routingu. W poniższej tabeli przedstawiono maksymalną przepustowość wychodzącą, która jest niemal taka sama jak w przypadku **emisji**.

|    Wyślij do dużej grupy      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000
| Liczba członków grupy        | 100   | 200   | 500    | 1000  | 2000  | 5000   | 10 000 
| Liczba grup               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Komunikaty przychodzące na sekundę  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Przepustowość ruchu przychodzącego  | 80 KB/s   | 40 KB/s   | 40 KB/s    | 20 KB/s    | 40 KB/s    | 40 KB/s     | 40 KB/s     |
| Komunikaty wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość wychodząca | 8 MB/s    | 8 MB/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

Liczba połączeń wysyłanych nie przekracza 40. Obciążenie serwera aplikacji jest małe, więc Sugerowana liczba aplikacji sieci Web jest mała.

|  Wyślij do dużej grupy  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększ liczbę domyślnych połączeń serwera z 5 na 40 na każdym serwerze aplikacji, aby uniknąć możliwych niezrównoważonych połączeń serwera z usługą Azure Signal Service.
> 
> Numer połączenia klienta, rozmiar komunikatu, szybkość wysyłania komunikatów, koszt routingu i warstwa SKU mają wpływ na ogólną wydajność **wysyłania do dużych grup**.

#### <a name="send-to-connection"></a>Wyślij do połączenia

Gdy klienci nawiązują połączenia z usługą Azure Signal Agent, w przypadku użycia w przypadku **połączenia Wyślij do** , każdy klient wywołuje specjalne centrum w celu uzyskania własnego identyfikatora połączenia. Wzorzec wydajności zbiera wszystkie identyfikatory połączeń, losowo je umieszcza i ponownie przypisuje je wszystkim klientom jako docelowy adres. Klienci wysyłają komunikat do połączenia docelowego do momentu zakończenia testu wydajności.

![Ruch dla przypadku użycia dla wysyłania do klienta](./media/signalr-concept-performance/sendtoclient.png)

Koszt routingu dla **wysyłania do połączenia** jest podobny do kosztów **wysyłania do małych grup**.

W miarę wzrostu liczby połączeń, koszt routingu ogranicza ogólną wydajność. Unit50 osiągnął limit. W związku z tym Unit100 nie można poprawić.

Poniższa tabela zawiera podsumowanie statystyczne po wielu zaokrągleniach uruchomienia testu " **Wyślij do połączenia** ".

|   Wyślij do połączenia   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Połączenia                        | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Komunikaty przychodzące/wychodzące na sekundę | 1000 | 2000 | 5000 | 8000  | 9000  | 20 000 | 20 000 |
| Przepustowość ruchu przychodzącego/wychodzącego | 2 MB/s    | 4 MB/s    | 10 MB/s   | 16 MB/s    | 18 MB/s    | 40 MB/s       | 40 MB/s       |

Ten przypadek użycia wymaga wysokiego obciążenia po stronie serwera aplikacji. Zapoznaj się z sugerowaną liczbą serwerów aplikacji w poniższej tabeli.

|  Wyślij do połączenia  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar komunikatu, szybkość wysyłania komunikatów, koszt routingu, warstwa SKU oraz procesor CPU/pamięć dla serwera aplikacji mają wpływ na ogólną wydajność **wysyłania do połączenia**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET sygnalizujący echo, broadcast i Send do małej grupy

Usługa Azure sygnalizująca zapewnia taką samą pojemność wydajności dla sygnalizującego ASP.NET. 

Test wydajności używa platformy Azure Web Apps ze [standardowego planu usługi S3](https://azure.microsoft.com/pricing/details/app-service/windows/) dla sygnalizującego ASP.NET.

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji sieci Web na potrzeby **ECHA**sygnałów ASP.NET.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji sieci Web dla **emisji**sygnałów ASP.NET.

|  Emisja       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji sieci Web dla sygnałów ASP.NET **wysyłanych do małych grup**.

|  Wyślij do małej grupy     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Tryb bezserwerowy

Klienci i usługa Azure Signal Service są objęte trybem bezserwerowym. Każdy Klient oznacza jedno połączenie. Klient wysyła komunikaty za pomocą interfejsu API REST do innego klienta lub emituje komunikaty do wszystkich.

Wysyłanie komunikatów o wysokiej gęstości za pośrednictwem interfejsu API REST nie jest tak wydajne jak użycie protokołu WebSocket. Wymaga to utworzenia nowego połączenia HTTP za każdym razem, gdy jest to dodatkowy koszt w trybie bezserwerowym.

#### <a name="broadcast-through-rest-api"></a>Rozgłaszanie za poorednictwem interfejsu API REST
Wszyscy klienci nawiązują połączenia WebSocket z usługą Azure Signal Service. Następnie niektórzy klienci rozpoczynają emitowanie za pomocą interfejsu API REST. Wysyłanie komunikatów (ruch przychodzący) odbywa się za pośrednictwem protokołu HTTP POST, co nie jest efektywne w porównaniu z protokołem WebSocket.

|   Rozgłaszanie za poorednictwem interfejsu API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Komunikaty przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Komunikaty wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość ruchu przychodzącego  | 4 KB/s    | 4 KB/s    | 4 KB/s     | 4 KB/s     | 4 KB/s     | 4 KB/s      | 4 KB/s      |
| Przepustowość wychodząca | 4 MB/s    | 8 MB/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

#### <a name="send-to-user-through-rest-api"></a>Wyślij do użytkownika przy użyciu interfejsu API REST
Test porównawczy przypisuje nazwy użytkowników wszystkim klientom przed rozpoczęciem nawiązywania połączenia z usługą Azure Signaler. Gdy klienci nawiązują połączenia WebSocket z usługą Azure Signal Service, zaczynają wysyłać komunikaty do innych użytkowników za pośrednictwem protokołu HTTP POST.

|   Wyślij do użytkownika przy użyciu interfejsu API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Komunikaty przychodzące na sekundę  | 300   | 600   | 900    | 1 300  | 2000  | 10 000  | 18 000  |
| Komunikaty wychodzące na sekundę | 300   | 600   | 900    | 1 300  | 2000  | 10 000  | 18 000 |
| Przepustowość ruchu przychodzącego  | 600 kb/s  | 1,2 MB/s  | 1,8 MB/s   | 2,6 MB/s   | 4 MB/s     | 10 MB/s     | 36 MB/s    |
| Przepustowość wychodząca | 600 kb/s  | 1,2 MB/s  | 1,8 MB/s   | 2,6 MB/s   | 4 MB/s     | 10 MB/s     | 36 MB/s    |

## <a name="performance-test-environments"></a>Środowiska testowe wydajności

W przypadku wszystkich przypadków użycia wymienionych wcześniej przeprowadzono testy wydajnościowe w środowisku platformy Azure. Najwyżej używamy 50 maszyn wirtualnych klienta i 20 maszyn wirtualnych serwera aplikacji. Oto niektóre szczegóły:

- Rozmiar maszyny wirtualnej klienta: StandardDS2V2 (2 vCPU, pamięć 7G)

- Rozmiar maszyny wirtualnej serwera aplikacji: StandardF4sV2 (4 vCPU, pamięć 8G)

- Połączenia serwera zestawu SDK usługi Azure Signal: 15

## <a name="performance-tools"></a>Narzędzia wydajności

Narzędzia do oceny wydajności usługi Azure Signal można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono omówienie wydajności usługi Azure sygnalizującej w typowych scenariuszach przypadków użycia.

Aby uzyskać szczegółowe informacje na temat wewnętrznych usługi i skalować je, przeczytaj następujące przewodniki:

* [Wewnętrzne elementy usługi Azure SignalR](signalr-concept-internals.md)
* [Skalowanie usługi Azure Signal](signalr-howto-scale-multi-instances.md)

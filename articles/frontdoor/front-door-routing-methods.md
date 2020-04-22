---
title: Usługi Azure Front Door — metody wyznaczania tras ruchu | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć różne metody wyznaczania trasy ruchu używane przez drzwi frontowe
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687810"
---
# <a name="front-door-routing-methods"></a>Metody wyznaczania tras drzwi przednich

Usługa Azure Front Door obsługuje różne metody routingu ruchu, aby określić sposób kierowania ruchu HTTP/HTTPS do różnych punktów końcowych usługi. Przy każdej z żądań klienta docierających do drzwi frontowych skonfigurowana metoda routingu jest stosowana w celu upewnienia się, że żądania są przekazywane do najlepszego wystąpienia wewnętrznej bazy danych. 

Istnieją cztery główne pojęcia dotyczące routingu ruchu dostępne w drzwiach frontowych:

* ** [Opóźnienie](#latency):** Routing oparty na opóźnieniach zapewnia, że żądania są wysyłane do najniższych limitów wstecznych opóźnienia dopuszczalne w zakresie czułości. Zasadniczo żądania użytkowników są wysyłane do "najbliższego" zestawu zaplecza w odniesieniu do opóźnienia sieci.
* ** [Priorytet](#priority):** Priorytety można przypisać do różnych zaplecza, jeśli chcesz użyć wewnętrznej bazy danych usługi podstawowej dla całego ruchu i udostępnić kopie zapasowe w przypadku, gdy podstawowe lub zapasowe zaplecze są niedostępne.
* ** [Ważone](#weighted):** Wagi można przypisać do różnych zaplecza, jeśli chcesz rozłożyć ruch na zestaw zaplecza, równomiernie lub zgodnie ze współczynnikami wagi.
* ** [Koligacja sesji](#affinity):** Koligacji sesji dla hostów frontonu lub domen, jeśli chcesz, aby kolejne żądania od użytkownika były wysyłane do tego samego zaplecza, o ile sesja użytkownika jest nadal aktywna, a wystąpienie wewnętrznej bazy danych nadal raportuje w dobrej kondycji na podstawie sond kondycji. 

Wszystkie konfiguracje usługi Front Door obejmują monitorowanie kondycji zaplecza i zautomatyzowane, natychmiastowe przechodzenie do trybu failover w skali globalnej. Aby uzyskać więcej informacji, zobacz [Monitorowanie wewnętrznej bazy danych drzwi przednich](front-door-health-probes.md). Drzwi frontowe można skonfigurować tak, aby działały na podstawie jednej metody routingu i w zależności od potrzeb aplikacji można użyć wielu lub wszystkich tych metod routingu w połączeniu, aby utworzyć optymalną topologię routingu.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Routing ruchu oparty na najniższych opóźnieniach

Wdrażanie zaplecza w dwóch lub więcej lokalizacjach na całym świecie może poprawić szybkość reakcji wielu aplikacji, kierując ruch do lokalizacji, która jest "najbliższa" użytkownikom końcowym. Domyślna metoda routingu ruchu dla konfiguracji drzwi ami frontowymi przekazuje żądania od użytkowników końcowych do najbliższego zaplecza ze środowiska drzwiach frontowych, które odebrało żądanie. W połączeniu z architekturą Anycast usługi Azure Front Door takie podejście zapewnia, że każdy z użytkowników końcowych uzyskać maksymalną wydajność spersonalizowane na podstawie ich lokalizacji.

"Najbliższa" zaplecze niekoniecznie jest najbliższa, mierzona odległością geograficzną. Zamiast tego drzwi frontowe określa najbliższe zaplecze przez pomiar opóźnienia sieci. Dowiedz się więcej o [architekturze wyznaczania tras w drzwiach frontowych](front-door-routing-architecture.md). 

Poniżej znajduje się ogólny przepływ decyzji:

| Dostępne zaplecze | Priorytet | Sygnał opóźnienia (na podstawie sondy kondycji) | Wagi |
|-------------| ----------- | ----------- | ----------- |
| Po pierwsze, należy wybrać wszystkie zaplecze, które są włączone i zwrócone w dobrej kondycji (200 OK) dla sondy kondycji. Powiedzmy, istnieje sześć zaplecza A, B, C, D, E i F, a wśród nich C jest niezdrowe i E jest wyłączona. Tak więc lista dostępnych zaplecza to A, B, D i F.  | Następnie wybierane są najważniejsze zaplecze priorytetowe wśród dostępnych. Powiedzmy, wewnętrznej bazy danych A, B i D mają priorytet 1 i wewnętrznej bazy danych F ma priorytet 2. Tak więc wybrane zaplecze będą A, B i D.| Wybierz zaplecze z zakresem opóźnienia (najmniejsze opóźnienie & czułość opóźnienia w ms określony). Powiedzmy, jeśli A jest 15 ms, B jest 30 ms i D jest 60 ms od środowiska drzwi frontowych, gdzie żądanie wylądował, a czułość opóźnienia wynosi 30 ms, a następnie najniższa pula opóźnienia składa się z wewnętrznej bazy danych A i B, ponieważ D jest poza 30 ms od najbliższej wewnętrznej bazy danych, która jest A. | Wreszcie, drzwi frontowe będzie zaokrąglać ruch wśród ostatecznej wybranej puli zaplecza w stosunku wagi określonej. Powiedzmy, jeśli wewnętrznej bazy danych A ma wagę 5 i wewnętrznej bazy B ma wagę 8, a następnie ruch zostanie rozdzielony w stosunku 5:8 między zaplecza A i B. |

>[!NOTE]
> Domyślnie właściwość czułości opóźnienia jest ustawiona na 0 ms, oznacza to, że zawsze przesyłaj żądanie do najszybszego dostępnego zaplecza.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Routing ruchu oparty na priorytecie

Często organizacja chce zapewnić niezawodność swoich usług, wdrażając jedną lub więcej usług tworzenia kopii zapasowych w przypadku, gdy ich usługa podstawowa ulegnie ulegnie utracie wydajności. W całej branży ta topologia jest również określana jako topologia wdrażania active/standby lub active/pasywne. Metoda routingu ruchu "Priorytet" umożliwia klientom platformy Azure łatwe zaimplementowanie tego wzorca trybu failover.

Domyślne drzwi frontowe zawierają listę równych priorytetów zaplecza. Domyślnie drzwiami frontowymi wysyła ruch tylko do zaplecza najwyższego priorytetu (najniższa wartość priorytetu), czyli podstawowy zestaw zaplecza. Jeśli podstawowe zaplecze nie są dostępne, drzwiami frontowymi kieruje ruch do pomocniczego zestawu zaplecza (druga najniższa wartość priorytetu). Jeśli zarówno podstawowe, jak i pomocnicze zaplecze nie są dostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność wewnętrznej bazy danych jest oparta na skonfigurowanym stanie (włączonym lub wyłączonym) i bieżącym stanie kondycji wewnętrznej bazy danych, zgodnie z sondami kondycji.

### <a name="configuring-priority-for-backends"></a>Konfigurowanie priorytetu zaplecza

Każda z zaplecza w puli wewnętrznej bazy danych w konfiguracji drzwiami frontowymi ma właściwość o nazwie "Priorytet", która może być liczbą z 1 a 5. Za pomocą usługi Azure Front Door można skonfigurować priorytet wewnętrznej bazy danych jawnie przy użyciu tej właściwości dla każdej wewnętrznej bazy danych. Ta właściwość jest wartością od 1 do 5. Niższe wartości reprezentują wyższy priorytet. Zaplecze może współużytkować wartości priorytetu.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważona metoda routingu ruchu
Metoda "ważonego" routingu ruchu umożliwia równomierne rozłożenie ruchu lub użycie wstępnie zdefiniowanej wagi.

W ważonej routingu ruchu routingu przypisać wagę do każdej wewnętrznej bazy danych w konfiguracji drzwi frontowych puli wewnętrznej bazy danych. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr używa domyślnej wagi "50".

Wśród listy dostępnych zaplecza w ramach akceptowanej czułości opóźnienia (jak określono), ruch jest rozmieszczony w mechanizmie okrężnym w stosunku określonych wag. Jeśli czułość opóźnienia jest ustawiona na 0 milisekund, ta właściwość nie zostanie uwzględniona, chyba że istnieją dwie wewnętrznej bazy danych z tym samym opóźnieniem sieci. 

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* **Stopniowe uaktualnianie aplikacji:** Przydziel procent ruchu do trasy do nowej wewnętrznej bazy danych i stopniowo zwiększaj ruch w czasie, aby dostosować go do innych zaplecza.
* **Migracja aplikacji na platformę Azure:** Tworzenie puli wewnętrznej bazy danych z zapleczem platformy Azure i zewnętrznych zaplecza. Dostosuj wagę zaplecza, aby preferować nowe zaplecze. Można stopniowo skonfigurować to, począwszy od wyłączenia nowych zaplecza, a następnie przypisywania im najniższych wag, powoli zwiększając je do poziomów, w których mają większość ruchu. Następnie wreszcie wyłączenie mniej preferowanych zaplecza i usunięcie ich z puli.  
* **Chmura bursting dla dodatkowej pojemności:** szybko rozszerzyć wdrożenia lokalnego w chmurze, umieszczając go za drzwiami frontowymi. Gdy potrzebujesz dodatkowej pojemności w chmurze, można dodać lub włączyć więcej zaplecza i określić, jaka część ruchu przechodzi do każdej wewnętrznej bazy danych.

## <a name="session-affinity"></a><a name = "affinity"></a>Koligacja sesji
Domyślnie bez koligacji sesji, drzwiami frontowymi przekazuje żądania pochodzące z tego samego klienta do różnych zaplecza na podstawie konfiguracji równoważenia obciążenia, szczególnie jako opóźnienia do różnych wewnętrznej bazy danych zmiany lub jeśli różne żądania z tego samego użytkownika ląduje w innym środowisku drzwi frontowych. Jednak w przypadku niektórych aplikacji stanowych lub pewnych innych scenariuszy preferowane jest kierowanie kolejnych żądań pochodzących od danego użytkownika do tego samego zaplecza, które przetworzyło początkowe żądanie. Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz utrzymać sesję użytkownika w ramach jednego zaplecza. Za pomocą plików cookie zarządzanych przez drzwiami frontowymi usługi Azure drzwiami frontowymi mogą kierować kolejny ruch z sesji użytkownika do tej samej wewnętrznej bazy danych do przetwarzania, o ile wewnętrznej bazy danych jest w dobrej kondycji, a sesja użytkownika nie wygasła. 

Koligację sesji można włączyć na poziomie hosta frontonu dla każdej skonfigurowanej domeny (lub poddomeny). Po jej włączeniu usługa Front Door dodaje plik cookie do sesji użytkownika. Funkcja koligacji sesji na podstawie plików cookie umożliwia usłudze Front Door identyfikowanie różnych użytkowników, nawet korzystających z tego samego adresu IP, co z kolei umożliwia bardziej równomierne rozłożenia ruchu na różne zaplecza.

Czas życia pliku cookie jest taki sam jak sesji użytkownika, ponieważ usługa Front Door aktualnie obsługuje tylko pliki cookie sesji. 

> [!NOTE]
> Publiczne serwery proxy mogą kolidować z koligacji sesji. Dzieje się tak, ponieważ ustanowienie sesji wymaga drzwiami frontowymi, aby dodać plik cookie koligacji sesji do odpowiedzi, czego nie można wykonać, jeśli odpowiedź jest buforowalna, ponieważ zakłóciłoby to pliki cookie innych klientów żądających tego samego zasobu. Aby chronić przed tym koligacji sesji **nie** zostanie ustalona, jeśli wewnętrznej bazy danych wysyła buforowalnej odpowiedzi, gdy jest to próba. Jeśli sesja została już ustanowiona, nie ma znaczenia, czy odpowiedź z wewnętrznej bazy danych jest buforowalna.
> Koligacja sesji zostanie ustalona w następujących okolicznościach, **chyba że** odpowiedź ma kod stanu HTTP 304:
> - Odpowiedź ma określone wartości ```Cache-Control``` ustawione dla nagłówka, który uniemożliwia buforowanie, takie jak "private" lub no-store".
> - Odpowiedź zawiera ```Authorization``` nagłówek, który nie wygasł.
> - Odpowiedź ma kod stanu HTTP 302.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

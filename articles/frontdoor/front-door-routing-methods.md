---
title: Moje drzwi platformy Azure — metody routingu ruchu | Microsoft Docs
description: Ten artykuł ułatwia zapoznanie się z różnymi metodami routingu ruchu używanymi przez tylne drzwi
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687810"
---
# <a name="front-door-routing-methods"></a>Metody routingu przed drzwiami

Drzwi frontonu platformy Azure obsługują różne metody routingu ruchu, aby określić sposób kierowania ruchu HTTP/HTTPS do różnych punktów końcowych usługi. W przypadku każdego żądania klientów, który dociera do przodu, skonfigurowana Metoda routingu zostanie zastosowana w celu upewnienia się, że żądania są przekazywane do najlepszego wystąpienia zaplecza. 

Istnieją cztery główne koncepcje dotyczące routingu ruchu dostępnego w drzwiach czołowych:

* ** [Opóźnienie](#latency):** Routing oparty na opóźnieniu gwarantuje, że żądania są wysyłane do najniższych opóźnień zaakceptowanych w zakresie czułości. W zasadzie żądania użytkownika są wysyłane do "najbliższego" zestawu załączeń w odniesieniu do opóźnienia sieci.
* ** [Priorytet](#priority):** Można przypisać priorytety do różnych zaplecza, gdy chcesz użyć podstawowego zaplecze usługi dla całego ruchu, i podać kopie zapasowe w przypadku, gdy podstawowy lub zapasowy frontonu są niedostępne.
* ** [Ważone](#weighted):** Można przypisywać wagi do różnych punktów końcowych, gdy chcesz rozpowszechnić ruch między zestawem założonych punktów końcowych, a nawet zgodnie z współczynnikiem ważenia.
* ** [Koligacja sesji](#affinity):** Koligację sesji dla hostów frontonu lub domen można skonfigurować w taki sposób, aby kolejne żądania od użytkownika były wysyłane do tego samego zaplecza, o ile sesja użytkownika nadal jest aktywna, a wystąpienie zaplecza nadal zgłasza w dobrej kondycji w oparciu o sondy kondycji. 

Wszystkie konfiguracje usługi Front Door obejmują monitorowanie kondycji zaplecza i zautomatyzowane, natychmiastowe przechodzenie do trybu failover w skali globalnej. Aby uzyskać więcej informacji, zobacz [monitorowanie zaplecza front-drzwi](front-door-health-probes.md). Drzwi frontonu można skonfigurować do pracy na podstawie pojedynczej metody routingu i w zależności od potrzeb aplikacji można użyć wielu lub wszystkich tych metod routingu w połączeniu, aby utworzyć optymalną topologię routingu.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Najniższe opóźnienia oparte na routingu ruchu

Wdrożenie zaplecza w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć czas odpowiedzi wielu aplikacji przez kierowanie ruchu do lokalizacji najbliżej użytkowników końcowych. Domyślna metoda routingu ruchu dla konfiguracji z drzwiami wstępnymi przekazuje żądania od użytkowników końcowych do najbliższej zaplecza ze środowiska z drzwiami, które odebrało żądanie. W połączeniu z architekturą emisji dowolnej platformy Azure, takie podejście zapewnia, że każdy użytkownik końcowy otrzymuje maksymalną wydajność spersonalizowaną na podstawie ich lokalizacji.

Zaplecze "najbliższy" nie musi być najbliżej siebie mierzone przez odległość geograficzną. Zamiast tego drzwi przede wszystkim określają Najbliższe punkty końcowe, mierząc opóźnienia sieci. Dowiedz się więcej o [architekturze routingu frontonu](front-door-routing-architecture.md). 

Poniżej znajduje się ogólny przepływ decyzyjny:

| Dostępne punkty końcowe | Priorytet | Sygnał opóźnienia (na podstawie sondy kondycji) | Ważenie |
|-------------| ----------- | ----------- | ----------- |
| Po pierwsze zaznacz wszystkie załączone punkty końcowe, które są włączone i zwracają w dobrej kondycji (200 OK) dla sondy kondycji. Załóżmy, że istnieje sześć zakończonych nieprawidłowych A, B, C, D, E i F, a wśród nich są w złej kondycji, a E jest wyłączone. Dlatego lista dostępnych punktów końcowych to, B, D i F.  | Następnie wybierane są nadmiarowe górne priorytety spośród dostępnych. Załóżmy, zaplecze A, B i D mają priorytet 1, a zaplecze F ma priorytet 2. Dlatego wybrane punkty końcowe będą mieć wartość, B i D.| Wybierz opcję Zastąp z zakresem opóźnień (co najmniej & opóźnienie w określonych MS). Załóżmy, że jeśli jest 15 MS, B to 30 MS, a D jest 60 MS w środowisku z drzwiami przednimi, gdzie załadowane żądanie i czułość opóźnienia wynosi 30 MS, a najniższa Pula opóźnień składa się z zaplecza A i B, ponieważ D jest więcej niż 30 MS, od najbliższej wewnętrznej bazy danych, która jest. | Na koniec drzwiczki z przodu spowodują założenie, że ruch między ostateczną wybraną pulą założeń zostanie określony w stosunku do określonych wag. Załóżmy, że w przypadku zaplecza A ma wagę 5, a zaplecze B ma wagę 8, ruch będzie dystrybuowany w stosunku 5:8 między zakończonymi koniec a i B. |

>[!NOTE]
> Domyślnie właściwość czułość opóźnienia jest ustawiona na wartość 0 MS, czyli zawsze przekazuje żądanie do najszybszych dostępnych zaplecza.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Ruch oparty na priorytetach — Routing

Często organizacja chce zapewnić niezawodność swoich usług, wdrażając co najmniej jedną usługę tworzenia kopii zapasowych na wypadek awarii usługi podstawowej. W branży Ta topologia jest również nazywana topologią wdrożenia Active/standby lub aktywny/pasywny. Metoda routingu ruchu "Priority" pozwala klientom platformy Azure łatwo zaimplementować ten wzorzec trybu failover.

Domyślne drzwi czołowe zawierają listę o równym priorytecie. Domyślnie drzwiczki z przodu wysyłają ruch tylko do frontonu o najwyższym priorytecie (najniższa wartość dla priorytetu), czyli podstawowego zestawu punktów końcowych. Jeśli podstawowe punkty końcowe nie są dostępne, drzwi z przodu kierują ruch do pomocniczego zestawu nadmiarowego (druga najniższa wartość dla priorytetu). Jeśli zarówno podstawowy, jak i pomocniczy punkt końcowy są niedostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność zaplecza jest oparta na skonfigurowanym stanie (włączonym lub wyłączonym) oraz aktualnemu statusowi kondycji zaplecza określonym przez sondy kondycji.

### <a name="configuring-priority-for-backends"></a>Konfigurowanie priorytetu dla frontonów

Każdy z zapleców w puli zaplecza w ramach konfiguracji z drzwiami wstępnymi ma właściwość o nazwie "Priority", która może być liczbą z zakresu od 1 do 5. Dzięki poszczególnym drzwiom platformy Azure można jawnie skonfigurować priorytet zaplecza przy użyciu tej właściwości dla każdego zaplecza. Ta właściwość jest wartością z przedziału od 1 do 5. Niższe wartości reprezentują wyższy priorytet. Nadkończeniey mogą udostępniać wartości priorytetowe.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważony ruch sieciowy — Metoda routingu
"Ważona" Metoda routingu ruchu umożliwia równomierne dystrybuowanie ruchu lub użycie wstępnie zdefiniowanej wagi.

W przypadku metody routingu ruchu ważonego można przypisać wagę do każdego zaplecza w konfiguracji z drzwiami w puli zaplecza. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr używa domyślnej wagi "50".

Na liście dostępnych założeń w ramach zaakceptowanej czułości opóźnienia (jak określono) ruch jest dystrybuowany w mechanizmie okrężnym w stosunku do określonych wag. Jeśli wartość ustawienia czułość opóźnienia jest ustawiona na 0 milisekund, ta właściwość nie zacznie obowiązywać, chyba że istnieją dwa punkty końcowe z tym samym opóźnieniem sieci. 

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* **Stopniowe Uaktualnianie aplikacji**: przydzielanie procentu ruchu, który ma być kierowany do nowego zaplecza, i stopniowe zwiększanie ruchu w czasie, aby zapewnić jego wartość nominalną z innymi założeniami.
* **Migrowanie aplikacji na platformę Azure**: Utwórz pulę zaplecza z platformą Azure i zewnętrznym zapleczem. Dostosuj grubość założenia, aby preferować nowe założenia. Można stopniowo ustawiać te ustawienia, zaczynając od od momentu wyłączenia nowych załączeń, a następnie przypisując im najniższe wagi, spowalniając je na poziomach, w których są one odbierane. Następnie należy wyłączyć mniej preferowane punkty końcowe i usunąć je z puli.  
* Przenoszenie w **chmurze w celu uzyskania dodatkowej pojemności**: szybkie rozszerzanie lokalnego wdrożenia na chmurę przez umieszczenie go za drzwiami przednimi. Jeśli potrzebujesz dodatkowej pojemności w chmurze, możesz dodać lub włączyć więcej zaplecza i określić, jaka część ruchu przechodzi do każdego zaplecza.

## <a name="session-affinity"></a><a name = "affinity"></a>Koligacja sesji
Domyślnie, bez koligacji sesji, do przodu są przekazywane żądania pochodzące z tego samego klienta do różnych punktów końcowych w oparciu o konfigurację równoważenia obciążenia, szczególnie w zależności od tego, jak opóźnienia w różnych zmianach nadchodzących lub różne żądania od tego samego użytkownika są wynoszone w innym środowisku przednim. Jednak w przypadku niektórych aplikacji stanowych lub pewnych innych scenariuszy preferowane jest kierowanie kolejnych żądań pochodzących od danego użytkownika do tego samego zaplecza, które przetworzyło początkowe żądanie. Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz utrzymać sesję użytkownika w ramach jednego zaplecza. Dzięki użyciu plików cookie z funkcji Front-drzwiczki platformy Azure mogą kierować kolejny ruch z sesji użytkownika do tego samego zaplecza na potrzeby przetwarzania, o ile jest w dobrej kondycji, a sesja użytkownika nie wygasła. 

Koligację sesji można włączyć na poziomie hosta frontonu dla każdej skonfigurowanej domeny (lub poddomeny). Po jej włączeniu usługa Front Door dodaje plik cookie do sesji użytkownika. Funkcja koligacji sesji na podstawie plików cookie umożliwia usłudze Front Door identyfikowanie różnych użytkowników, nawet korzystających z tego samego adresu IP, co z kolei umożliwia bardziej równomierne rozłożenia ruchu na różne zaplecza.

Czas życia pliku cookie jest taki sam jak sesji użytkownika, ponieważ usługa Front Door aktualnie obsługuje tylko pliki cookie sesji. 

> [!NOTE]
> Publiczne serwery proxy mogą zakłócać koligację sesji. Wynika to z faktu, że ustanawianie sesji wymaga, aby dodać do odpowiedzi plik cookie koligacji sesji, którego nie można wykonać, jeśli odpowiedź jest buforowana, ponieważ spowodowałoby to zakłócenie plików cookie innych klientów żądających tego samego zasobu. Aby zapewnić ochronę przed tym, koligacja sesji **nie** zostanie ustanowiona, jeśli zaplecze wyśle odpowiedź w pamięci podręcznej, gdy ta próba zostanie podjęta. Jeśli sesja została już ustanowiona, nie ma znaczenia, czy odpowiedź z zaplecza jest buforowana.
> Koligacja sesji zostanie ustanowiona w następujących sytuacjach, **chyba że** odpowiedź ma kod stanu HTTP 304:
> - Odpowiedź ma określone wartości ustawione dla ```Cache-Control``` nagłówka, który uniemożliwia buforowanie, takie jak "Private" lub "No-Store".
> - Odpowiedź zawiera ```Authorization``` nagłówek, który nie wygasł.
> - Odpowiedź ma kod stanu HTTP 302.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

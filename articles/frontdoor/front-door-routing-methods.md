---
title: Moje drzwi platformy Azure — metody routingu ruchu | Microsoft Docs
description: Ten artykuł ułatwia zapoznanie się z różnymi metodami routingu ruchu używanymi przez tylne drzwi
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449141"
---
# <a name="front-door-routing-methods"></a>Metody routingu przed drzwiami

Drzwi frontonu platformy Azure obsługują różne rodzaje metod routingu ruchu, aby określić sposób kierowania ruchu HTTP/HTTPS do różnych punktów końcowych usługi. Gdy klient wysyła żądania do przodu, skonfigurowana Metoda routingu zostanie zastosowana w celu upewnienia się, że żądania są przekazywane do najlepszego wystąpienia zaplecza. 

Dostępne są cztery metody routingu ruchu:

* ** [Opóźnienie](#latency):** Routing oparty na opóźnieniu gwarantuje, że żądania są wysyłane do najniższych opóźnień zaakceptowanych w zakresie czułości. W zasadzie żądania użytkownika są wysyłane do "najbliższego" zestawu zakończonych w odniesieniu do opóźnienia sieci.
* ** [Priorytet](#priority):** Można przypisać priorytety do zaplecza, gdy chcesz skonfigurować podstawowe zaplecze do obsługi całego ruchu. Pomocnicza baza danych może być kopią zapasową w przypadku, gdy pierwotna baza danych stanie się niedostępna.
* ** [Ważone](#weighted):** Można przypisać wagi do zamiar zakończonych niektórym, gdy chcesz rozpowszechnić ruch w zestawie nadmiarowych. Czy chcesz równomiernie dystrybuować lub zgodnie z współczynnikiem wagi.
* ** [Koligacja sesji](#affinity):** Koligację sesji dla hostów lub domen frontonu można skonfigurować w celu zapewnienia, że żądania od tego samego użytkownika końcowego są wysyłane do tego samego zaplecza.

Wszystkie konfiguracje usługi Front Door obejmują monitorowanie kondycji zaplecza i zautomatyzowane, natychmiastowe przechodzenie do trybu failover w skali globalnej. Aby uzyskać więcej informacji, zobacz [monitorowanie zaplecza front-drzwi](front-door-health-probes.md). Drzwi z przodu mogą być zależne od jednej metody routingu. W zależności od potrzeb aplikacji można także połączyć wiele metod routingu, aby utworzyć optymalną topologię routingu.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Najniższe opóźnienia oparte na routingu ruchu

Wdrożenie zaplecza w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć czas odpowiedzi aplikacji przez kierowanie ruchu do miejsca docelowego, które jest najbliżej użytkowników końcowych. Domyślna metoda routingu ruchu dla konfiguracji z drzwiami wstępnymi przekazuje żądania od użytkowników końcowych do najbliższej zaplecza środowiska z drzwiami, które odebrało żądanie. W połączeniu z architekturą emisji dowolnej platformy Azure, takie podejście zapewnia, że każdy użytkownik końcowy otrzymuje maksymalną wydajność spersonalizowaną na podstawie ich lokalizacji.

Zaplecze "najbliższy" nie musi być najbliższe mierzonym przez odległość geograficzną. Zamiast tego drzwi przede wszystkim określają Najbliższe punkty końcowe, mierząc opóźnienia sieci. Dowiedz się więcej o [architekturze routingu frontonu](front-door-routing-architecture.md). 

Poniżej znajduje się ogólny przepływ decyzyjny:

| Dostępne punkty końcowe | Priorytet | Sygnał opóźnienia (na podstawie sondy kondycji) | Ważenie |
|-------------| ----------- | ----------- | ----------- |
| Najpierw zaznacz wszystkie aukcje załączone, które są włączone i zwracają w dobrej kondycji (200 OK) dla sondy kondycji. Jeśli istnieje sześć zakończonych nieprawidłowymi A, B, C, D, E i F, a między nimi C jest zła kondycja, a E jest wyłączone. Lista dostępnych punktów końcowych to, B, D i F.  | Następnie wybierane są nadmiarowe górne priorytety spośród dostępnych. Jeśli zaplecze A, B i D mają priorytet 1, a zaplecze F ma priorytet 2. Następnie wybrane punkty końcowe będą mieć wartość, B i D.| Wybierz opcję Zastąp z zakresem opóźnień (co najmniej & opóźnienie w określonych MS). Jeśli zaplecze A to 15 MS, B to 30 MS i D jest 60 MS z zewnątrz środowiska, w którym zachodzi żądanie, a czułość opóźnienia wynosi 30 MS, najniższa Pula opóźnień składa się z zaplecza A i B, ponieważ D jest więcej niż 30 MS z najbliższego zaplecza, który jest. | Na koniec drzwiczki z przodu spowodują założenie, że ruch między ostateczną wybraną pulą założeń zostanie określony w stosunku do określonych wag. Załóżmy, że w przypadku zaplecza A ma wagę 5, a zaplecze B ma wagę 8, ruch będzie dystrybuowany w stosunku 5:8 między zakończonymi koniec a i B. |

>[!NOTE]
> Domyślnie właściwość czułość opóźnienia jest ustawiona na wartość 0 MS, czyli zawsze przekazuje żądanie do najszybszych dostępnych zaplecza.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Ruch oparty na priorytetach — Routing

Często organizacja chce zapewnić wysoką dostępność usług, wdrażając więcej niż jedną usługę kopii zapasowych na wypadek awarii podstawowej. W branży Ta topologia jest również nazywana topologią wdrożenia Active/standby lub aktywny/pasywny. Metoda routingu ruchu "Priority" pozwala klientom platformy Azure łatwo zaimplementować ten wzorzec trybu failover.

Domyślne drzwi czołowe zawierają listę o równym priorytecie. Domyślnie drzwiczki z przodu wysyłają ruch tylko do frontonu o najwyższym priorytecie (najniższa wartość dla priorytetu), czyli podstawowego zestawu punktów końcowych. Jeśli podstawowe frontony nie są dostępne, drzwi z przodu kierują ruch do pomocniczego zestawu zakończonych niektórymi (druga najniższa wartość dla priorytetu). Jeśli zarówno podstawowy, jak i pomocniczy punkt końcowy są niedostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność zaplecza jest oparta na skonfigurowanym stanie (włączonym lub wyłączonym) oraz aktualnemu statusowi kondycji zaplecza określonym przez sondy kondycji.

### <a name="configuring-priority-for-backends"></a>Konfigurowanie priorytetu dla frontonów

Każdy zaplecze w puli zaplecza konfiguracji drzwi przednich ma właściwość o nazwie "Priority", która może być liczbą z przedziału od 1 do 5. Dzięki poszczególnym drzwiom platformy Azure można jawnie skonfigurować priorytet zaplecza przy użyciu tej właściwości dla każdego zaplecza. Ta właściwość jest wartością z przedziału od 1 do 5. Niższe wartości reprezentują wyższy priorytet. Nadkończeniey mogą udostępniać wartości priorytetowe.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważony ruch sieciowy — Metoda routingu
"Ważona" Metoda routingu ruchu umożliwia równomierne dystrybuowanie ruchu lub użycie wstępnie zdefiniowanej wagi.

W przypadku metody routingu ruchu ważonego można przypisać wagę do każdego zaplecza w konfiguracji z drzwiami w puli zaplecza. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr używa domyślnej wagi "50".

Z listą dostępnych założeń końcowych, które mają akceptowalną czułość opóźnienia, ruch jest dystrybuowany z mechanizmem okrężnym przy użyciu współczynnika określonych wag. Jeśli czułość opóźnienia zostanie ustawiona na wartość 0 milisekund, ta właściwość nie zacznie obowiązywać, chyba że istnieją dwa załączone z tym samym opóźnieniem sieci. 

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* **Stopniowe Uaktualnianie aplikacji**: umożliwia kierowanie ruchu do nowego zaplecza i stopniowe zwiększenie ruchu w czasie, aby zapewnić jego wartość nominalną z innymi zaplecami.
* **Migrowanie aplikacji na platformę Azure**: Utwórz pulę zaplecza z platformą Azure i zewnętrznym zapleczem. Dostosuj grubość założenia, aby preferować nowe założenia. Można stopniowo ustawiać te ustawienia, zaczynając od od momentu wyłączenia nowych załączeń, a następnie przypisując im najniższe wagi, spowalniając je na poziomach, w których są one odbierane. Następnie należy wyłączyć mniej preferowane punkty końcowe i usunąć je z puli.  
* Przenoszenie w **chmurze w celu uzyskania dodatkowej pojemności**: szybkie rozszerzanie lokalnego wdrożenia na chmurę przez umieszczenie go za drzwiami przednimi. Jeśli potrzebujesz dodatkowej pojemności w chmurze, możesz dodać lub włączyć więcej zaplecza i określić, jaka część ruchu przechodzi do każdego zaplecza.

## <a name="session-affinity"></a><a name = "affinity"></a>Koligacja sesji
Domyślnie, bez koligacji sesji, przód i przód przesyłają żądania pochodzące z tego samego klienta do różnych frontonów. Niektóre aplikacje stanowe lub w niektórych scenariuszach, które przetwarzają żądania od tego samego użytkownika, woli tego samego zaplecza, który przetworzył początkowe żądanie. Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz utrzymać sesję użytkownika w ramach jednego zaplecza. Korzystając z zarządzanych plików cookie, drzwi frontonu platformy Azure mogą kierować ruch z sesji użytkownika do tego samego zaplecza w celu przetworzenia.

Koligację sesji można włączyć na poziomie hosta frontonu dla każdej skonfigurowanej domeny (lub poddomeny). Po jej włączeniu usługa Front Door dodaje plik cookie do sesji użytkownika. Funkcja koligacji sesji na podstawie plików cookie umożliwia usłudze Front Door identyfikowanie różnych użytkowników, nawet korzystających z tego samego adresu IP, co z kolei umożliwia bardziej równomierne rozłożenia ruchu na różne zaplecza.

Czas życia pliku cookie jest taki sam jak sesji użytkownika, ponieważ usługa Front Door aktualnie obsługuje tylko pliki cookie sesji. 

> [!NOTE]
> Publiczne serwery proxy mogą zakłócać koligację sesji. Wynika to z faktu, że ustanawianie sesji wymaga, aby dodać do odpowiedzi plik cookie koligacji sesji, którego nie można wykonać, jeśli odpowiedź jest buforowana, ponieważ spowodowałoby to zakłócenie plików cookie innych klientów żądających tego samego zasobu. Aby zapewnić ochronę przed tym, koligacja sesji **nie** zostanie ustanowiona, jeśli zaplecze wyśle odpowiedź w pamięci podręcznej, gdy ta próba zostanie podjęta. Jeśli sesja została już ustanowiona, nie ma znaczenia, czy odpowiedź z zaplecza jest buforowana.
> Koligacja sesji zostanie ustanowiona w następujących sytuacjach, **chyba że** odpowiedź ma kod stanu HTTP 304:
> - Odpowiedź ma określone wartości ustawione dla nagłówka, ```Cache-Control``` który uniemożliwia buforowanie, takie jak "Private" lub "No-Store".
> - Odpowiedź zawiera ```Authorization``` nagłówek, który nie wygasł.
> - Odpowiedź ma kod stanu HTTP 302.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

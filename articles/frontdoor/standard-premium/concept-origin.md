---
title: Źródłowa i źródłowa Grupa na platformie Azure — warstwa standardowa/Premium
description: W tym artykule opisano, czym pochodzenia i grupy pochodzenia znajdują się w konfiguracji drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100081"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Pochodzenie i Grupa pierwotna na platformie Azure — warstwa standardowa/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule omówiono koncepcje działania wdrożenia aplikacji sieci Web z platformą Azure Front-Standard/Premium. Dowiesz się również, co to jest Grupa *źródłowa* i *źródłowa* , w konfiguracji Standard/Premium na platformie Azure.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origin

Pierwotne drzwi platformy Azure w warstwie Standardowa/Premium odnoszą się do nazwy hosta lub publicznego adresu IP aplikacji, która obsługuje żądania klientów. Platforma Azure Front-Standard/Premium obsługuje zarówno zasoby platformy Azure, jak i spoza platformy Azure w grupie pochodzenia. Aplikacja może być również hostowana w lokalnym centrum danych lub u innego dostawcy chmury. Nie należy mylić pochodzenia z warstwą bazy danych ani warstwą magazynowania. Podstawa powinna być wyświetlana jako publiczny punkt końcowy zaplecza aplikacji. Po dodaniu źródła do grupy pierwotnych drzwi platformy Azure w warstwie Standardowa/Premium należy również dodać następujące informacje:

* **Typ źródła:** Typ zasobu, który ma zostać dodany. Drzwi tylne obsługują funkcję autowykrywania aplikacji z zaApp Service, usługi w chmurze lub magazynu. Jeśli chcesz użyć innego zasobu na platformie Azure lub nawet z zapleczem innym niż Azure, wybierz opcję **host niestandardowy**.

    >[!IMPORTANT]
    >W trakcie konfiguracji interfejsy API nie sprawdzają, czy źródło nie jest dostępne ze środowiska ze drzwiami. Upewnij się, że tylne drzwi mogą dotrzeć do źródła.

* **Nazwa hosta subskrypcji i źródła:** Jeśli nie wybrano **niestandardowego hosta** dla typu hosta zaplecza, wybierz zaplecze, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta zaplecza.

* **Nagłówek hosta źródła:** Wartość nagłówka hosta wysyłana do zaplecza dla każdego żądania. Aby uzyskać więcej informacji, zobacz [nagłówek hosta źródła](#hostheader).

* **Priorytet:** Przypisz priorytety do innego źródła, jeśli chcesz używać głównego źródła dla całego ruchu. Ponadto należy podać kopie zapasowe, jeśli pierwotne lub źródłowe kopie zapasowe są niedostępne. Aby uzyskać więcej informacji, zobacz [priorytet](#priority).

* **Waga:** Przypisz wagi do innego źródła, aby rozpowszechnić ruch między zestawem źródeł, nawet lub zgodnie z współczynnikim wagi. Aby uzyskać więcej informacji, zobacz [wagi](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Nagłówek hosta źródła

Żądania, które są przekazywane przez Standard/Premium platformy Azure, do źródła będą zawierać pole nagłówka hosta używane przez źródło do pobierania danego zasobu. Wartość tego pola zazwyczaj pochodzi od identyfikatora URI źródła, który ma nagłówek i port hosta.

Na przykład żądanie wykonane dla elementu `www.contoso.com` będzie miało nagłówek hosta `www.contoso.com` . Jeśli w celu skonfigurowania źródła używasz Azure Portal, wartością domyślną tego pola jest nazwa hosta zaplecza. Jeśli źródło znajduje się `contoso-westus.azurewebsites.net` w Azure Portal, automatycznie wypełniana wartość dla nagłówka hosta źródła `contoso-westus.azurewebsites.net` . Jeśli jednak użyjesz szablonów Azure Resource Manager lub innej metody bez jawnego ustawienia tego pola, drzwi z przodu wyślą nazwę hosta przychodzącego jako wartość dla nagłówka hosta. Jeśli żądanie zostało wykonane dla `www.contoso.com` , a źródło ma `contoso-westus.azurewebsites.net` puste pole nagłówka, drzwi z przodu ustawią nagłówek hosta jako `www.contoso.com` .

Większość zapleców aplikacji (Azure Web Apps, BLOB Storage i Cloud Services) wymaga, aby nagłówek hosta był zgodny z domeną zaplecza. Jednak Host frontonu, który jest przesyłany do zaplecza, będzie używać innej nazwy hosta, takiej jak `www.contoso.net` .

Jeśli źródło wymaga, aby nagłówek hosta był zgodny z nazwą hosta zaplecza, upewnij się, że nagłówek hosta zaplecza zawiera nazwę hosta zaplecza.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Konfigurowanie nagłówka hosta źródła dla źródła

Aby skonfigurować pole **nagłówka hosta źródła** dla źródła w sekcji Grupa pierwotna:

1. Otwórz zasób z drzwiami wstępnymi i wybierz grupę pierwotną do skonfigurowania.

2. Dodaj źródło, jeśli nie zostało to zrobione, lub edytuj istniejące.

3. W polu Nagłówek hosta źródła ustaw wartość niestandardową lub pozostaw to pole puste. Nazwa hosta dla żądania przychodzącego będzie używana jako wartość nagłówka hosta.

## <a name="origin-group"></a>Grupa pierwotna

Grupa pierwotna na platformie Azure — warstwa standardowa/Premium odwołuje się do zestawu źródeł, który odbiera podobny ruch dla swojej aplikacji. Innymi słowy, jest to logiczna Grupa wystąpień aplikacji na świecie, która odbiera ten sam ruch i reaguje na oczekiwane zachowanie. Te źródła można wdrożyć w różnych regionach lub w tym samym regionie. Wszystkie źródła mogą być w trybie wdrożenia aktywny/aktywny lub co jest zdefiniowane jako Konfiguracja aktywna/pasywna.

Grupa pierwotna definiuje sposób oceniania źródeł za pośrednictwem sond kondycji. Definiuje także sposób równoważenia obciążenia między nimi.

### <a name="health-probes"></a>Sondy kondycji

Na platformie Azure — warstwa standardowa/Premium wysyłane są okresowe żądania sondowania HTTP/HTTPS do każdego ze skonfigurowanych źródeł. Żądania sondowania określają bliskość i kondycję każdego źródła do równoważenia obciążenia żądaniami użytkowników końcowych. Ustawienia sondowania kondycji grupy pierwotnej definiują, jak sondować stan kondycji zamiaru aplikacji. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

* **Path**: adres URL używany do żądania sondowania dla wszystkich źródeł w grupie pierwotnej. Na przykład jeśli jeden z źródeł ma `contoso-westus.azurewebsites.net` wartość, a ścieżka zostanie ustawiona na/Probe/test.aspx, a następnie środowiska z przodu, przy założeniu, że protokół jest http, wyśle żądania sondy kondycji do `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protokół**: określa, czy wysyłać żądania sondy kondycji z zewnętrznych drzwi do źródeł przy użyciu protokołu HTTP lub https.

* **Metoda**: Metoda http, która ma być używana do wysyłania sond kondycji. Opcje obejmują GET lub głowy (domyślnie).
    > [!NOTE]
    > W celu obniżenia obciążenia i ponoszenia kosztów na potrzeby punktów końcowych zaleca się używanie żądań głównych dla sond kondycji.

* **Interwał (w sekundach)**: określa częstotliwość sond kondycji do Twoich źródeł lub interwały, w których każde ze środowisk przed drzwiami wysyła sondę.

    >[!NOTE]
    >Aby przyspieszyć przełączanie w tryb failover, ustaw wartość interwał na niższą. Im niższa wartość, tym wyższego natężenia głośności sondy kondycji. Na przykład, jeśli interwał jest ustawiony na 30 sekund z wypowiedzią, 100 czołowe drzwi są globalnie, każde zaplecze otrzyma około 200 żądań sondowania na minutę.

Aby uzyskać więcej informacji, zobacz [sondy kondycji](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia

Ustawienia równoważenia obciążenia dla grupy pierwotnej definiują, jak oceniane są sondy kondycji. Te ustawienia określają, czy źródło jest w dobrej kondycji. Sprawdzają także, jak równoważyć obciążenie ruchu między różnymi źródłami w grupie pierwotnej. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

* **Rozmiar próbki:** Identyfikuje liczbę próbek sond kondycji, które należy wziąć pod uwagę w celu oceny kondycji pochodzenia.

* **Pomyślny rozmiar próbki:** Definiuje rozmiar próbki, jak wspomniano powyżej, liczbę pomyślnych próbek wymaganych do wywołania pozostałej kondycji. Załóżmy na przykład, że interwał sondy kondycji z przodu wynosi 30 sekund, rozmiar próbki wynosi 5, a rozmiar próbki powiódł się 3. Za każdym razem, gdy analizujemy sondy kondycji pochodzenia, analizujemy pięć ostatnich próbek powyżej 150 sekund (5 x 30). Co najmniej trzy pomyślne sondy są wymagane do deklarowania pochodzenia jako dobra kondycja.

* **Czułość opóźnienia (dodatkowe opóźnienie):** Definiuje, czy chcesz, aby usługa Azure Front-Standard/Premium wysłała żądanie do źródła w ramach zakresu czułości pomiaru opóźnienia, lub Prześlij żądanie do najbliższego zaplecza.

Aby uzyskać więcej informacji, zobacz [najmniej metody routingu opartego na opóźnieniu](#latency).

## <a name="routing-methods"></a>Metody routingu

Platforma Azure Front-Standard/Premium obsługuje różne rodzaje metod routingu ruchu, aby określić sposób kierowania ruchu HTTP/HTTPS do różnych punktów końcowych usługi. Gdy klient wysyła żądania do przodu, skonfigurowana Metoda routingu zostanie zastosowana w celu upewnienia się, że żądania są przekazywane do najlepszego wystąpienia zaplecza. 

Dostępne są cztery metody routingu ruchu na platformie Azure — warstwa standardowa/Premium:

* **[Opóźnienie](#latency):** Routing oparty na opóźnieniu gwarantuje, że żądania są wysyłane do najniższych opóźnień zaakceptowanych w zakresie czułości. W zasadzie żądania użytkownika są wysyłane do "najbliższego" zestawu zakończonych w odniesieniu do opóźnienia sieci.
* **[Priorytet](#priority):** Można przypisać priorytety do zaplecza, gdy chcesz skonfigurować podstawowe zaplecze do obsługi całego ruchu. Pomocnicza baza danych może być kopią zapasową w przypadku, gdy pierwotna baza danych stanie się niedostępna.
* **[Ważone](#weighted):** Można przypisać wagi do zamiar zakończonych niektórym, gdy chcesz rozpowszechnić ruch w zestawie nadmiarowych. Czy chcesz równomiernie dystrybuować lub zgodnie z współczynnikiem wagi.

Wszystkie konfiguracje Standard/Premium systemu Azure są dostępne w ramach monitorowania kondycji zaplecza i zautomatyzowanej globalnej pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [monitorowanie zaplecza](concept-health-probes.md). Drzwi z przodu mogą być zależne od jednej metody routingu. W zależności od potrzeb aplikacji można także połączyć wiele metod routingu, aby utworzyć optymalną topologię routingu.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Najniższe opóźnienia oparte na routingu ruchu

Wdrożenie zaplecza w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć czas odpowiedzi aplikacji przez kierowanie ruchu do miejsca docelowego, które jest najbliżej użytkowników końcowych. Domyślna metoda routingu ruchu dla konfiguracji z drzwiami wstępnymi przekazuje żądania od użytkowników końcowych do najbliższej zaplecza środowiska z drzwiami, które odebrało żądanie. W połączeniu z architekturą emisji dowolnej platformy Azure, takie podejście zapewnia, że każdy użytkownik końcowy otrzymuje maksymalną wydajność spersonalizowaną na podstawie ich lokalizacji.

Zaplecze "najbliższy" nie musi być najbliższe mierzonym przez odległość geograficzną. Zamiast tego drzwi przede wszystkim określają Najbliższe punkty końcowe, mierząc opóźnienia sieci.

Poniżej znajduje się ogólny przepływ decyzyjny:

| Dostępne punkty końcowe | Priorytet | Sygnał opóźnienia (na podstawie sondy kondycji) | Ważenie |
|-------------| ----------- | ----------- | ----------- |
| Najpierw zaznacz wszystkie aukcje załączone, które są włączone i zwracają w dobrej kondycji (200 OK) dla sondy kondycji. Jeśli istnieje sześć zakończonych nieprawidłowymi A, B, C, D, E i F, a między nimi C jest zła kondycja, a E jest wyłączone. Lista dostępnych punktów końcowych to, B, D i F.  | Następnie wybierane są nadmiarowe górne priorytety spośród dostępnych. Jeśli zaplecze A, B i D mają priorytet 1, a zaplecze F ma priorytet 2. Następnie wybrane punkty końcowe będą mieć wartość, B i D.| Wybierz opcję Zastąp z zakresem opóźnień (co najmniej & opóźnienie w określonych MS). Jeśli zaplecze A to 15 MS, B to 30 MS i D jest 60 MS z zewnątrz środowiska, w którym zachodzi żądanie, a czułość opóźnienia wynosi 30 MS, najniższa Pula opóźnień składa się z zaplecza A i B, ponieważ D jest więcej niż 30 MS z najbliższego zaplecza, który jest. | Na koniec drzwiczki z przodu spowodują założenie, że ruch między ostateczną wybraną pulą założeń zostanie określony w stosunku do określonych wag. Załóżmy, że w przypadku zaplecza A ma wagę 5, a zaplecze B ma wagę 8, ruch będzie dystrybuowany w stosunku 5:8 między zakończonymi koniec a i B. |

>[!NOTE]
> Domyślnie właściwość czułość opóźnienia jest ustawiona na wartość 0 MS, czyli zawsze przekazuje żądanie do najszybszych dostępnych zaplecza.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Ruch oparty na priorytetach — Routing

Często organizacja chce zapewnić wysoką dostępność usług, wdrażając więcej niż jedną usługę kopii zapasowych na wypadek awarii podstawowej. W branży Ta topologia jest również nazywana topologią wdrożenia Active/standby lub aktywny/pasywny. Metoda routingu ruchu "Priority" pozwala klientom platformy Azure łatwo zaimplementować ten wzorzec trybu failover.

Domyślne drzwi czołowe zawierają listę o równym priorytecie. Domyślnie drzwiczki z przodu wysyłają ruch tylko do frontonu o najwyższym priorytecie (najniższa wartość dla priorytetu), czyli podstawowego zestawu punktów końcowych. Jeśli podstawowe frontony nie są dostępne, drzwi z przodu kierują ruch do pomocniczego zestawu zakończonych niektórymi (druga najniższa wartość dla priorytetu). Jeśli zarówno podstawowy, jak i pomocniczy punkt końcowy są niedostępne, ruch przechodzi do trzeciego i tak dalej. Dostępność zaplecza jest oparta na skonfigurowanym stanie (włączonym lub wyłączonym) oraz aktualnemu statusowi kondycji zaplecza określonym przez sondy kondycji.

#### <a name="configuring-priority-for-backends"></a>Konfigurowanie priorytetu dla frontonów

Każdy zaplecze w puli zaplecza konfiguracji drzwi przednich ma właściwość o nazwie "Priority", która może być liczbą z przedziału od 1 do 5. Dzięki poszczególnym drzwiom platformy Azure można jawnie skonfigurować priorytet zaplecza przy użyciu tej właściwości dla każdego zaplecza. Ta właściwość jest wartością z przedziału od 1 do 5. Niższe wartości reprezentują wyższy priorytet. Nadkończeniey mogą udostępniać wartości priorytetowe.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważony ruch sieciowy — Metoda routingu
"Ważona" Metoda routingu ruchu umożliwia równomierne dystrybuowanie ruchu lub użycie wstępnie zdefiniowanej wagi.

W przypadku metody routingu ruchu ważonego można przypisać wagę do każdego zaplecza w konfiguracji z drzwiami w puli zaplecza. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr używa domyślnej wagi "50".

Z listą dostępnych założeń końcowych, które mają akceptowalną czułość opóźnienia, ruch jest dystrybuowany z mechanizmem okrężnym przy użyciu współczynnika określonych wag. Jeśli czułość opóźnienia zostanie ustawiona na wartość 0 milisekund, ta właściwość nie zacznie obowiązywać, chyba że istnieją dwa załączone z tym samym opóźnieniem sieci. 

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* **Stopniowe Uaktualnianie aplikacji**: umożliwia kierowanie ruchu do nowego zaplecza i stopniowe zwiększenie ruchu w czasie, aby zapewnić jego wartość nominalną z innymi zaplecami.
* **Migrowanie aplikacji na platformę Azure**: Utwórz pulę zaplecza z platformą Azure i zewnętrznym zapleczem. Dostosuj grubość założenia, aby preferować nowe założenia. Można stopniowo ustawiać te ustawienia, zaczynając od od momentu wyłączenia nowych załączeń, a następnie przypisując im najniższe wagi, spowalniając je na poziomach, w których są one odbierane. Następnie należy wyłączyć mniej preferowane punkty końcowe i usunąć je z puli.  
* Przenoszenie w **chmurze w celu uzyskania dodatkowej pojemności**: szybkie rozszerzanie lokalnego wdrożenia na chmurę przez umieszczenie go za drzwiami przednimi. Jeśli potrzebujesz dodatkowej pojemności w chmurze, możesz dodać lub włączyć więcej zaplecza i określić, jaka część ruchu przechodzi do każdego zaplecza.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę czołową/Premium](create-front-door-portal.md)

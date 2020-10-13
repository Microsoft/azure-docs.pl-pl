---
title: plik dołączania
description: plik dołączania
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73034550"
---
Podczas projektowania systemu ważne jest, aby zrozumieć potencjalne zagrożenia dla tego systemu i odpowiednio dodać odpowiednie zabezpieczenia, gdy system został zaprojektowany i opracowany. Ważne jest, aby zaprojektować produkt od samego początku z myślą o bezpieczeństwie, ponieważ zrozumienie, jak osoba atakująca może naruszyć bezpieczeństwo systemu, pozwala upewnić się, że na początku są stosowane odpowiednie środki zaradcze.

## <a name="security-starts-with-a-threat-model"></a>Zabezpieczenia zaczynają się od modelu zagrożeń

Firma Microsoft dysponuje długimi modelami zagrożeń dla swoich produktów i udostępnia publicznie proces modelowania zagrożeń firmy. Środowisko firmy pokazuje, że modelowanie ma nieoczekiwane korzyści wykraczające poza bezpośrednie zrozumienie zagrożeń, których najbardziej dotyczy. Na przykład tworzy również drogi dla otwartej dyskusji z innymi osobami spoza zespołu deweloperów, co może prowadzić do nowych pomysłów i ulepszeń w produkcie.

Celem modelowania zagrożeń jest zrozumienie, w jaki sposób osoba atakująca może naruszyć bezpieczeństwo systemu, a następnie upewnić się, że są odpowiednie środki zaradcze. Modelowanie zagrożeń wymusza, aby zespół projektowy rozważał środki zaradcze, gdy system został zaprojektowany, a nie po wdrożeniu systemu. Ten fakt ma krytyczne znaczenie, ponieważ modernizacja zabezpieczeń ochrony do wyposażono urządzeń w polu jest niewykonalna, podatność na zagrożenia i pozostawia klientów narażonych na ryzyko.

Wiele zespołów programistycznych to doskonałe zadanie przechwytujące wymagania funkcjonalne dla systemu, który przynosi korzyści klientom. Jednak zidentyfikowanie nieoczywistego sposobu, w którym ktoś może nadużycie, jest bardziej trudne. Modelowanie zagrożeń może pomóc zespołom programistycznym zrozumieć, co może zrobić osoba atakująca i dlaczego. Modelowanie zagrożeń to proces strukturalny, który tworzy dyskusje na temat decyzji projektowych dotyczących bezpieczeństwa w systemie, a także wprowadza zmiany w projekcie, które mają wpływ na bezpieczeństwo. Mimo że model zagrożenia jest po prostu dokumentem, Ta dokumentacja stanowi również idealny sposób zapewnienia ciągłości wiedzy, utrzymania poświęconej lekcji oraz szybkiego dołączenia do nowego zespołu. Na koniec modelowanie zagrożeń polega na tym, że można rozważyć inne aspekty zabezpieczeń, takie jak to, jakie zobowiązania w zakresie zabezpieczeń mają być przeznaczone dla klientów. Zobowiązania te są związane z modelem zagrożeń i testowaniem rozwiązań Internet rzeczy (IoT).

### <a name="when-to-do-threat-modeling"></a>Kiedy należy modelować zagrożenie

[Modelowanie zagrożeń](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferuje największą wartość przy włączaniu jej do fazy projektowania. Podczas projektowania masz największą elastyczność, aby wprowadzać zmiany w celu wyeliminowania zagrożeń. Eliminowanie zagrożeń przez projektowanie to żądany wynik. Jest to znacznie prostsze niż Dodawanie środków zaradczych, testowanie ich i zapewnianie aktualności, a ponadto takie eliminacje nie zawsze jest możliwe. Eliminuje to zagrożenie, ponieważ produkt stał się bardziej dojrzały, a z kolei ostatecznie wymaga większej ilości pracy i znacznie trudniejszych kompromisów niż modelowanie zagrożeń wczesne w rozwoju.

### <a name="what-to-consider-for-threat-modeling"></a>Co należy wziąć pod uwagę w przypadku modelowania zagrożeń

Należy zapoznać się z rozwiązaniem jako całość, a także skupić się na następujących obszarach:

* Funkcje zabezpieczeń i ochrony prywatności
* Funkcje, których dotyczą problemy dotyczące zabezpieczeń
* Funkcje, które dotykają granic zaufania

### <a name="who-performs-threat-modeling"></a>Kto wykonuje modelowanie zagrożeń

Modelowanie zagrożeń jest procesem podobnym do innych. Dobrym pomysłem jest traktowanie dokumentu modelu zagrożeń, takiego jak każdy inny składnik rozwiązania, i jego zweryfikowanie. Wiele zespołów programistycznych to doskonałe zadanie przechwytujące wymagania funkcjonalne dla systemu, który przynosi korzyści klientom. Jednak zidentyfikowanie nieoczywistego sposobu, w którym ktoś może nadużycie, jest bardziej trudne. Modelowanie zagrożeń może pomóc zespołom programistycznym zrozumieć, co może zrobić osoba atakująca i dlaczego.

### <a name="how-to-perform-threat-modeling"></a>Jak przeprowadzić modelowanie zagrożeń

Proces modelowania zagrożeń składa się z czterech kroków: kroki są następujące:

* Modelowanie aplikacji
* Wyliczanie zagrożeń
* Eliminowanie zagrożeń
* Weryfikuj środki zaradcze

#### <a name="the-process-steps"></a>Kroki procesu

Trzy zasady, które należy wziąć pod uwagę podczas kompilowania modelu zagrożeń:

1. Utwórz diagram na zewnątrz architektury referencyjnej.

2. Zacznij od początku szerokość. Zapoznaj się z omówieniem i zapoznaj się z systemem jako całością przed głębokiego nałożenia. Takie podejście pomaga upewnić się, że masz głębokie szczegółowe w odpowiednich miejscach.

3. Na dysku procesu nie należy zezwalać na dysk procesu. Jeśli znajdziesz problem w fazie modelowania i chcesz go poznać, przejdź na stronę! Nie musisz wykonywać następujących kroków slavishly.

#### <a name="threats"></a>Zagrożenia

Cztery podstawowe elementy modelu zagrożeń:

* Procesy, takie jak usługi sieci Web, usługi Win32 i * demony Nix. Niektóre złożone jednostki (na przykład bramy polowe i czujniki) mogą być abstrakcyjne jako proces, gdy nie jest możliwe przechodzenie do szczegółów technicznych w tych obszarach.

* Magazyny danych (dane z dowolnego miejsca są przechowywane, takie jak plik konfiguracyjny lub baza danych)

* Przepływ danych (w którym dane są przenoszone między innymi elementami w aplikacji)

* Jednostki zewnętrzne (wszystkie elementy, które współdziałają z systemem, ale nie są pod kontrolą aplikacji, przykłady obejmują użytkowników i źródła satelitarne)

Wszystkie elementy na diagramie architektury podlegają różnym zagrożeniom; w tym artykule opisano krok. Przeczytaj [ponownie modelowanie zagrożeń, krok,](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) aby dowiedzieć się więcej o elementach krok.

Różne elementy diagramu aplikacji podlegają pewnym zagrożeniom:

* Procesy podlegają KROKowi
* Przepływy danych podlegają identyfikatorowi TID
* Magazyny danych podlegają numerom TID i czasami R, gdy magazyny danych są plikami dziennika.
* Jednostki zewnętrzne podlegają SRD

## <a name="security-in-iot"></a>Zabezpieczenia w IoT

Połączone urządzenia specjalne mają znaczną liczbę potencjalnych obszarów interakcji i wzorców interakcji, które należy wziąć pod uwagę w celu zapewnienia platformy do zabezpieczania cyfrowego dostępu do tych urządzeń. Termin "Digital Access" jest używany tutaj do odróżnienia od operacji, które są wykonywane za pośrednictwem bezpośredniej interakcji z urządzeniem, w której zabezpieczenia dostępu są udostępniane za pośrednictwem fizycznej kontroli dostępu. Na przykład umieszczenie urządzenia w pokoju z blokadą drzwi. Gdy nie można odmówić dostępu fizycznego przy użyciu oprogramowania i sprzętu, można podjąć miary, aby zapobiec sytuacji, w której fizyczny dostęp nie prowadzi do zakłócenia systemu.

Podczas eksplorowania wzorców interakcji zapoznaj się z tematem "Sterowanie urządzeniem" i "dane urządzenia", korzystając z tego samego poziomu uwagi. "Sterowanie urządzeniem" może być sklasyfikowane jako wszelkie informacje dostarczane do urządzenia przez każdą ze stron, które mają na celu zmianę lub wpływ na jego zachowanie w stosunku do jego stanu lub stanu środowiska. "Dane urządzenia" mogą być klasyfikowane jako wszelkie informacje, które urządzenie emituje do każdej innej strony o stanie i zaobserwowanym stanie środowiska.

W celu zoptymalizowania najlepszych rozwiązań w zakresie zabezpieczeń zaleca się, aby typowa architektura IoT została podzielona na kilka składników/stref w ramach ćwiczenia modelowania zagrożeń. Te strefy są opisane w pełni w tej sekcji i obejmują:

* Pliku
* Brama pola,
* Bramy chmury i
* Services.

Strefy są szeroką metodą segmentacji rozwiązania. Każda strefa często ma własne wymagania dotyczące danych i uwierzytelniania i autoryzacji. Strefy mogą być również używane do izolacji szkód i ograniczają wpływ niskich stref zaufania na wyższe strefy zaufania.

Każda strefa jest oddzielona granicą zaufania, która jest zapisywana jako czerwona linia kropkowana na poniższym diagramie. Reprezentuje przejście danych/informacje z jednego źródła do drugiego. W trakcie tego przejścia dane/informacje mogą podlegać fałszowaniu, naruszeniu, wyparciu, ujawnianiu informacji, odmowie usługi i podniesienia uprawnień (krok).

![Strefy zabezpieczeń IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Składniki przedstawione w każdej granicy są również poddawane KROKowi, co pozwala na pełny widok modelowania zagrożeń 360. W poniższych sekcjach opisano poszczególne składniki oraz zagadnienia dotyczące zabezpieczeń i rozwiązania, które należy umieścić w miejscu.

W poniższych sekcjach omówiono standardowe składniki zwykle dostępne w tych strefach.

### <a name="the-device-zone"></a>Strefa urządzenia

Środowisko urządzenia to natychmiastowe miejsce fizyczne na urządzeniu, w którym dostęp fizyczny i/lub "sieć lokalna" do urządzenia jest możliwe. Przyjęto, że "sieć lokalna" jest siecią, która jest odrębna i izolowana od — ale potencjalnie jest przyłączona do — publiczna sieć internetowa i obejmuje każdą bezprzewodową technologię radiową, która umożliwia komunikację między urządzeniami równorzędnymi. *Nie obejmuje ona* żadnej technologii wirtualizacji sieci tworzącej iluzję takiej sieci lokalnej, a także nie obejmuje sieci operatorów publicznych, które wymagają, aby dwa urządzenia mogły komunikować się między publicznymi miejscami w sieci, jeśli zostały wprowadzone do lokalizacji komunikacji równorzędnej.

### <a name="the-field-gateway-zone"></a>Strefa bramy pola

Brama pola to urządzenie/urządzenia lub niektóre oprogramowanie komputerowe serwera ogólnego przeznaczenia, które działa jako włącznik komunikacji i, ewentualnie, jako system sterowania urządzeniami i centrum przetwarzania danych urządzeń. Strefa bramy pól obejmuje samą bramę pola i wszystkie urządzenia, które są do niego podłączone. Jak nazwa oznacza, bramy pól działają poza dedykowanymi obiektami przetwarzania danych, są zwykle powiązane z lokalizacją, mogą podlegać fizycznej włamania i mają ograniczoną nadmiarowość operacyjną. Wszystkie, aby powiedzieć, że brama pola jest często jednym z nich, może dotykać i sabotażać, a jednocześnie wiedzą, jak działa.

Brama pola jest różna od zwykłego routera ruchu sieciowego, który miał aktywną rolę w zarządzaniu dostępem i przepływem informacji, co oznacza, że jest to aplikacja przynosząca do jednostki i połączenia sieciowego lub terminalu sesji. Urządzenie NAT lub zapora, w przeciwieństwie, nie kwalifikuje się jako bramy pól, ponieważ nie są to jawne połączenia lub terminale sesji, ale raczej połączenia trasy (lub blokują) lub sesje z nimi wykonywane. Brama pola ma dwa oddzielne obszary powierzchni. Jedna twarze urządzeń, które są dołączone do nich i reprezentuje wewnątrz strefy, a pozostałe — wszystkie strony zewnętrzne i jest krawędzią strefy.

### <a name="the-cloud-gateway-zone"></a>Strefa bramy chmury

Brama chmurowa to system, który umożliwia zdalną komunikację z i do urządzeń lub bram pól w różnych lokacjach w publicznej przestrzeni sieciowej, zazwyczaj w kierunku systemu kontroli i danych opartych na chmurze, a także do Federacji takich systemów. W niektórych przypadkach Brama chmury może natychmiast ułatwić dostęp do specjalnych urządzeń z terminali, takich jak tablety lub telefony. W kontekście omówionym tutaj "Chmura" odwołuje się do dedykowanego systemu przetwarzania danych, który nie jest powiązany z tą samą lokacją jak podłączone urządzenia lub bramy pól. Ponadto w strefie chmury środki operacyjne uniemożliwiają dostęp fizyczny i nie muszą być narażone na infrastrukturę publiczną.  

Brama chmury może być potencjalnie zamapowana na nakładkę wirtualizacji sieci w celu izolowania bramy chmury oraz wszystkich dołączonych urządzeń lub bram pól z dowolnego innego ruchu sieciowego. Sama Brama w chmurze nie jest systemem sterowania urządzeniami ani funkcją przetwarzania ani magazynu dla danych urządzenia; Interfejs tych obiektów z bramą w chmurze. Strefa bramy chmury obejmuje samą bramę w chmurze wraz ze wszystkimi bramami i urządzeniami pól, bezpośrednio lub pośrednio dołączonymi do niej. Granica strefy to odrębny obszar powierzchni, w którym wszystkie strony zewnętrzne komunikują się.

### <a name="the-services-zone"></a>Strefa usług

"Usługa" jest definiowana dla tego kontekstu jako dowolny składnik oprogramowania lub moduł, który jest współpołączony z urządzeniami przez bramę pola lub chmury na potrzeby zbierania i analizowania danych oraz dla poleceń i kontroli. Usługi są mediatorse. Działają one w ramach swojej tożsamości na bramy i inne podsystemy, przechowują i analizują dane, niezależnie generują polecenia na urządzeniach w oparciu o dane szczegółowe lub planuje i uwidaczniają możliwości informacji i kontroli dla autoryzowanych użytkowników końcowych.

### <a name="information-devices-versus-special-purpose-devices"></a>Informacje — urządzenia i urządzenia specjalne

Komputery, telefony i tablety są głównie interaktywnymi urządzeniami informacji. Telefony i tablety są jawnie zoptymalizowane pod kątem maksymalizowania czasu istnienia baterii. Najlepiej wyłączają się częściowo, gdy nie natychmiast współdziałają z osobą lub gdy nie świadczą usług takich jak odtwarzanie muzyki lub kierowanie ich właściciela do określonej lokalizacji. Z punktu widzenia systemów te urządzenia technologii informatycznych działają głównie jako serwery proxy do osób. Są to "elementy uruchamiające osoby", które sugerują akcje i "czujniki osób" zbierające dane wejściowe.

Urządzenia specjalne, od prostych czujników temperatury do złożonych linii produkcyjnych fabryki z tysiącami składników w nich, różnią się od siebie. Te urządzenia są znacznie bardziej ograniczone w celu, a nawet jeśli udostępniają jakiś interfejs użytkownika, są w dużej mierze objęte zakresem lub zintegrowane z zasobami w świecie fizycznym. Mierzą i zgłaszają sytuacje w zakresie środowiska, włączają zawory, servos kontroli, alarmy dźwiękowe, lampki przełączania i wykonują wiele innych zadań. Ułatwiają one wykonywanie zadań, dla których urządzenie informacyjne jest zbyt ogólne, zbyt kosztowne, zbyt duże lub zbyt kruchy. Konkretny cel natychmiast określa swój projekt techniczny, jak również dostępny budżet pieniężny dla operacji produkcyjnych i zaplanowanych okresów istnienia. Kombinacja tych dwóch kluczowych czynników ogranicza dostęp do dostępnego budżetu energii eksploatacyjnej, fizycznego rozmiaru i w ten sposób dostępnego magazynu, mocy obliczeniowej i możliwości zabezpieczeń.

Jeśli coś "się nie stanie" z urządzeniami zautomatyzowanymi lub zdalnymi, na przykład fizyczne wady lub wady logiki kontroli, aby Willful nieautoryzowane wtargnięcie i manipulowanie. Części produkcyjne mogą ulec zniszczeniu, budynki mogą być looted lub spalane, a ludzie mogą być poszkodowani lub nawet z nich. Jest to cała inna Klasa szkód niż ktoś maxing limit skradzionej karty kredytowej. Pasek zabezpieczenia dla urządzeń, które powodują przenoszenie, a także dla danych czujników, które ostatecznie powodują, że operacje, które mają być przenoszone, muszą być wyższe niż w każdym scenariuszu handlu elektronicznego lub bankowości.

### <a name="device-control-and-device-data-interactions"></a>Sterowanie urządzeniami i interakcje danych urządzenia

Połączone urządzenia specjalne mają znaczną liczbę potencjalnych obszarów interakcji i wzorców interakcji, które należy wziąć pod uwagę w celu zapewnienia platformy do zabezpieczania cyfrowego dostępu do tych urządzeń. Termin "Digital Access" jest używany tutaj do odróżnienia od operacji, które są wykonywane za pośrednictwem bezpośredniej interakcji z urządzeniem, w której zabezpieczenia dostępu są udostępniane za pośrednictwem fizycznej kontroli dostępu. Na przykład umieszczenie urządzenia w pokoju z blokadą drzwi. Gdy nie można odmówić dostępu fizycznego przy użyciu oprogramowania i sprzętu, można podjąć miary, aby zapobiec sytuacji, w której fizyczny dostęp nie prowadzi do zakłócenia systemu.

Podczas eksplorowania wzorców interakcji należy zapoznać się z tematem "Sterowanie urządzeniem" i "dane urządzenia" o tym samym poziomie uwagi podczas modelowania zagrożeń. "Sterowanie urządzeniem" może być sklasyfikowane jako wszelkie informacje dostarczane do urządzenia przez każdą ze stron, które mają na celu zmianę lub wpływ na jego zachowanie w stosunku do jego stanu lub stanu środowiska. "Dane urządzenia" mogą być klasyfikowane jako wszelkie informacje, które urządzenie emituje do każdej innej strony o stanie i zaobserwowanym stanie środowiska.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Wykonywanie modelowania zagrożeń dla architektury referencyjnej usługi Azure IoT

Firma Microsoft stosuje strukturę zakreśloną wcześniej do modelowania zagrożeń dla usługi Azure IoT. W poniższej sekcji jest używany konkretny przykład architektury referencyjnej usługi Azure IoT, który pokazuje, jak myśleć o modelowaniu zagrożeń dla IoT oraz jak rozpoznać zidentyfikowane zagrożenia. Ten przykład identyfikuje cztery główne obszary fokusu:

* Urządzenia i źródła danych,
* Transport danych,
* Przetwarzanie urządzeń i zdarzeń oraz
* Prezentacja

![Modelowanie zagrożeń dla usługi Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Na poniższym diagramie przedstawiono uproszczony widok architektury IoT firmy Microsoft przy użyciu modelu diagramu przepływu danych, który jest używany przez Microsoft Threat Modeling Tool:

![Modelowanie zagrożeń dla usługi Azure IoT przy użyciu programu MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Należy pamiętać, że architektura oddziela możliwości urządzeń i bramy. Takie podejście umożliwia użytkownikowi korzystanie z urządzeń bramy, które są bezpieczniejsze: mogą komunikować się z bramą w chmurze przy użyciu bezpiecznych protokołów, co zwykle wymaga większego nakładu na przetwarzanie, które urządzenie natywne — takie jak termostat — może zapewnić własnemu. W strefie usług platformy Azure założono, że brama chmury jest reprezentowana przez usługę IoT Hub platformy Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Urządzenia i źródła danych/transport danych

Ta sekcja zawiera informacje o architekturze opisanej wcześniej za pomocą funkcji modelowania zagrożeń oraz omówienie sposobu rozwiązywania niektórych niezwiązanych z nimi problemów. Ten przykład koncentruje się na podstawowych elementach modelu zagrożenia:

* Procesy (zarówno w kontrolce, jak i elementy zewnętrzne)
* Komunikacja (nazywana również przepływami danych)
* Magazyn (nazywane również magazynami danych)

#### <a name="processes"></a>Procesy

W przypadku każdej kategorii podanej w architekturze usługi Azure IoT ten przykład próbuje zmniejszyć liczbę różnych zagrożeń w różnych etapach dane/informacje istnieją w temacie: proces, komunikacja i magazyn. Poniżej znajduje się przegląd najbardziej typowych dla kategorii "proces", a następnie przegląd sposobu, w jaki te zagrożenia mogą być najlepiej skorygowane:

**Fałszowanie**: osoba atakująca może wyodrębnić materiał klucza kryptograficznego z urządzenia, na poziomie oprogramowania lub sprzętu, a następnie uzyskać dostęp do systemu przy użyciu innego urządzenia fizycznego lub wirtualnego w ramach tożsamości urządzenia, z którego pochodzi klucz materiału. Dobrym ilustracją jest zdalne sterowanie, które może obrócić dowolny telewizor i które są popularnymi narzędziami Prankster.

**Odmowa usługi (D)**: urządzenie może być renderowane bez możliwości działania lub komunikowania się przez zakłócenia przy użyciu częstotliwości radiowych lub przewodów tnących. Na przykład aparat nadzoru z zamierzonym wycinaniem mocy lub połączenia sieciowego nie może raportować danych.

**Manipulowanie (T)**: osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie działające na urządzeniu, co potencjalnie pozwala oprogramowaniu zastąpionemu na korzystanie z autentycznej tożsamości urządzenia, jeśli materiał klucza lub usług kryptograficznych, które są dostępne w przypadku nielegalnego programu. Na przykład osoba atakująca może wykorzystać wyodrębniony materiał klucza do przechwytywania i pomijania danych z urządzenia w ścieżce komunikacji i zamienić ją na fałszywe dane, które są uwierzytelniane za pomocą skradzionego materiału kluczowego.

**Ujawnienie informacji (I)**: Jeśli na urządzeniu jest uruchomione manipulowanie oprogramowaniem, takie manipulowanie oprogramowaniem może potencjalnie spowodować wyciek danych do nieupoważnionych stron. Na przykład osoba atakująca może wykorzystać wyodrębniony materiał klucza, aby wstrzyknąć do ścieżki komunikacji między urządzeniem a kontrolerem lub bramą pola lub bramą w chmurze w celu Siphon informacji.

**Podniesienie uprawnień (E)**: urządzenie, które ma konkretną funkcję, może być wymuszane w inny sposób. Na przykład, zawór, który jest zaprogramowany do otwarcia pół drogi, może zostać zawarty do otwarcia w sposób otwarty.

| **Składnik** | **Zagrożenie** | **Ograniczanie ryzyka** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| Urządzenie |S |Przypisywanie tożsamości do urządzenia i uwierzytelnianie urządzenia |Zastępowanie urządzenia lub części urządzenia innym urządzeniem. Jak wiesz, że rozmawiasz z właściwym urządzeniem? |Uwierzytelnianie urządzenia przy użyciu Transport Layer Security (TLS) lub IPSec. Infrastruktura powinna obsługiwać korzystanie z klucza wstępnego (PSK) na tych urządzeniach, które nie mogą obsługiwać pełnego asymetrycznego kryptografii. Korzystanie z usługi Azure AD i [uwierzytelniania OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Zastosowanie mechanizmów Tamperproof na urządzeniu, na przykład przez zwiększenie możliwości wyodrębnienia kluczy i innych materiałów kryptograficznych z urządzenia. |Ryzyko polega na tym, że ktoś narusza urządzenie (zakłócenia fizyczne). Na pewno to urządzenie nie zostało naruszone. |Najbardziej skutecznym środkiem ograniczającym jest funkcja TPM (Trusted Platform Module), która umożliwia przechowywanie kluczy w specjalnym obwodzie obwodowym, z którego klucze nie mogą być odczytywane, ale mogą być używane tylko w przypadku operacji kryptograficznych, które używają klucza, ale nigdy nie ujawniają klucza. Szyfrowanie pamięci urządzenia. Zarządzanie kluczami dla urządzenia. Podpisywanie kodu. |
|| E |Posiadanie kontroli dostępu do urządzenia. Schemat autoryzacji. |Jeśli urządzenie zezwala na wykonywanie poszczególnych akcji na podstawie poleceń ze źródła zewnętrznego lub nawet czujników z naruszeniem zabezpieczeń, umożliwia atak na wykonywanie operacji, które nie są dostępne w inny sposób. |Posiadanie schematu autoryzacji dla urządzenia |
| Brama pola |S |Uwierzytelnianie bramy pola do bramy chmury (na przykład opartej na certyfikatach, PSK lub na podstawie roszczeń). |Jeśli ktoś może sfałszować bramę pola, może ona przedstawić się jako dowolne urządzenie. |Protokół TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Wszystkie te same zagadnienia dotyczące magazynu kluczy i zaświadczania o urządzeniach ogólnie — najlepszym rozwiązaniem jest użycie modułu TPM. rozszerzenie 6LowPAN dla protokołu IPSec obsługujące sieci bezprzewodowe sensor (WSN). |
|| TRID |Chronić bramę pola przed manipulacją (TPM?) |Ataki wykorzystujące fałszowanie do bramy w chmurze zastanawiają się, że komunikacja z bramą pola może spowodować ujawnienie informacji i manipulowanie danymi |Szyfrowanie pamięci, moduł TPM, uwierzytelnianie. |
|| E |Mechanizm kontroli dostępu dla bramy pola | | |

Poniżej przedstawiono kilka przykładów zagrożeń w tej kategorii:

**Fałszowanie**: osoba atakująca może wyodrębnić materiał klucza kryptograficznego z urządzenia, na poziomie oprogramowania lub sprzętu, a następnie uzyskać dostęp do systemu przy użyciu innego urządzenia fizycznego lub wirtualnego w ramach tożsamości urządzenia, z którego pochodzi klucz materiału.

**Odmowa usługi**: urządzenie może być renderowane z niemożliwością działania lub komunikacji przez zakłócanie częstotliwości radiowych lub przewodów tnących. Na przykład aparat nadzoru z zamierzonym wycinaniem mocy lub połączenia sieciowego nie może raportować danych.

**Manipulowanie**: osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie działające na urządzeniu, co potencjalnie pozwala oprogramowaniu zastąpionemu na korzystanie z autentycznej tożsamości urządzenia, jeśli materiał klucza lub usług kryptograficznych, które udostępniają kluczowe materiały, były dostępne dla nielegalnego programu.

**Manipulowanie**: kamerą nadzorującą, która przedstawia widoczny obraz holu pustego, może być ukierunkowana na zdjęcie takich holu. Czujnik dymu lub ognia może zgłaszać kogoś, co jest jaśniejsze. W obu przypadkach urządzenie może być w pełni wiarygodnie godne zaufania do systemu, ale zgłasza informacje manipulowane.

**Manipulowanie**: osoba atakująca może wykorzystać wyodrębniony materiał klucza do przechwytywania i pomijania danych z urządzenia w ścieżce komunikacji i zamienić ją na fałszywe dane, które są uwierzytelniane za pomocą skradzionego materiału kluczowego.

**Manipulowanie**: osoba atakująca może częściowo lub całkowicie zastąpić oprogramowanie działające na urządzeniu, co potencjalnie pozwala oprogramowaniu zastąpionemu na korzystanie z autentycznej tożsamości urządzenia, jeśli materiał klucza lub usług kryptograficznych, które udostępniają kluczowe materiały, były dostępne dla nielegalnego programu.

**Ujawnienie informacji**: Jeśli na urządzeniu jest uruchomione manipulowanie oprogramowaniem, takie manipulowanie oprogramowaniem może potencjalnie spowodować wyciek danych do nieupoważnionych stron.

**Ujawnienie informacji**: osoba atakująca może wykorzystać wyodrębniony materiał klucza, aby wstrzyknąć do ścieżki komunikacji między urządzeniem a kontrolerem lub bramą w chmurze w celu Siphon informacji.

**Odmowa usługi**: urządzenie można wyłączyć lub włączyć w trybie, w którym komunikacja nie jest możliwa (co jest zamierzone w wielu maszynach przemysłowych).

**Manipulowanie**: urządzenie można skonfigurować tak, aby działało w stanie nieznanym do systemu kontroli (poza znanymi parametrami kalibracji) i w ten sposób dostarczyć dane, które mogą być nieprawidłowo interpretowane

**Podniesienie uprawnień**: urządzenie, które ma konkretną funkcję można wymusić zrobić coś innego. Na przykład, zawór, który jest zaprogramowany do otwarcia pół drogi, może zostać zawarty do otwarcia w sposób otwarty.

**Odmowa usługi**: urządzenie może być w stanie, w którym komunikacja nie jest możliwa.

**Manipulowanie**: urządzenie można skonfigurować w taki sposób, aby działało w stanie nieznany w systemie kontroli (poza znanymi parametrami kalibracji) i w ten sposób zapewnić dane, które mogą być interpretowane nieprawidłowo.

**Fałszowanie/manipulowanie/** odrzucanie: Jeśli nie jest zabezpieczony (w rzadko używanym przypadku zdalnego sterowania przez klienta), atakujący może manipulować stanem urządzenia anonimowo. Dobrym ilustracją jest zdalne sterowanie, które może obrócić dowolny telewizor i które są popularnymi narzędziami Prankster.

#### <a name="communication"></a>Komunikacja

Zagrożenia dotyczące ścieżki komunikacji między urządzeniami, urządzeniami i bramami pól oraz bramą urządzenia i chmury. W poniższej tabeli przedstawiono wskazówki dotyczące otwartych gniazd na urządzeniu/w sieci VPN:

| **Składnik** | **Zagrożenie** | **Ograniczanie ryzyka** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| IoT Hub urządzenia |TID |Wykres TLS (PSK/RSA) do szyfrowania ruchu |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu. W przypadku protokołów niestandardowych należy ustalić sposób ich ochrony. W większości przypadków komunikacja odbywa się z urządzenia do IoT Hub (urządzenie inicjuje połączenie). |
| Urządzenie do urządzenia |TID |Wykres TLS (PSK/RSA) do szyfrowania ruchu sieciowego. |Odczytywanie danych podczas przesyłania między urządzeniami. Manipulowanie danymi. Przeciążanie urządzenia nowymi połączeniami |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP. W przypadku protokołów niestandardowych należy ustalić sposób ich ochrony. Środki zaradcze dla zagrożeń systemu DoS polegają na urządzeniach równorzędnych za pomocą chmury lub bramy polowej i mogą działać tylko jako klienci w sieci. Komunikacja równorzędna może spowodować bezpośrednie połączenie między elementami równorzędnymi po przeprowadzeniu przez niego brokera |
| Urządzenie jednostki zewnętrznej |TID |Silne parowanie jednostki zewnętrznej z urządzeniem |Podsłuchiwanie połączenia z urządzeniem. Zakłócanie komunikacji z urządzeniem |Bezpieczne parowanie jednostki zewnętrznej z urządzeniem NFC/Bluetooth. Kontrolowanie panelu operacyjnego urządzenia (fizycznego) |
| Brama usługi Field Gateway Cloud Gateway |TID |TLS (PSK/RSA) do szyfrowania ruchu sieciowego. |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). W przypadku protokołów niestandardowych należy ustalić sposób ich ochrony. |
| Brama chmury urządzenia |TID |TLS (PSK/RSA) do szyfrowania ruchu sieciowego. |Podsłuchiwanie lub zakłócanie komunikacji między urządzeniem i bramą |Zabezpieczenia na poziomie protokołu (MQTT/AMQP/HTTP/CoAP). W przypadku protokołów niestandardowych należy ustalić sposób ich ochrony. |

Poniżej przedstawiono kilka przykładów zagrożeń w tej kategorii:

**Odmowa usługi**: ograniczone urządzenia są zwykle pod zagrożeniem systemu DOS, gdy aktywnie nasłuchują połączeń przychodzących lub niezamówionych datagramów w sieci, ponieważ osoba atakująca może otworzyć wiele połączeń równolegle, a ich nie obsłużyć lub usługi, albo można zapełnić je niezamówionym ruchem. W obu przypadkach urządzenie może być efektywnie renderowane jako nieobsługiwane w sieci.

**Fałszowanie, ujawnienie informacji**: urządzenia z ograniczeniami i specjalne cele często mają jedno-dla wszystkich obiektów zabezpieczeń, takich jak hasło lub ochrona numerów PIN, lub całkowicie polegają na zaufaniu sieci, co oznacza, że udziela dostępu do informacji, gdy urządzenie jest w tej samej sieci, a sieć jest często chroniona za pomocą klucza współużytkowanego. Oznacza to, że gdy zostanie ujawniony wspólny klucz tajny dla urządzenia lub sieci, istnieje możliwość sterowania urządzeniem lub obserwowania danych emitowanych z urządzenia.  

**Fałszowanie**: osoba atakująca może przechwycić lub częściowo zastępować emisję oraz sfałszować nadawcę (Man w środku).

**Manipulowanie**: osoba atakująca może przechwycić lub częściowo zastąpić emisję i wysłać fałszywe informacje 

**Ujawnienie informacji:** osoba atakująca może eavesdrop się na emisję i uzyskać informacje bez autoryzacji **odmowy usługi:** osoba atakująca może zazakleszczenie sygnału emisji i odmowę dystrybucji informacji

#### <a name="storage"></a>Magazyn

Każde urządzenie i Brama pola mają pewną postać magazynu (tymczasową dla kolejkowania danych, magazynu obrazów systemu operacyjnego).

| **Składnik** | **Zagrożenie** | **Ograniczanie ryzyka** | **Ryzyko** | **Implementacja** |
| --- | --- | --- | --- | --- |
| Magazyn urządzeń |TRID |Szyfrowanie magazynu, podpisywanie dzienników |Odczytywanie danych z magazynu (dane OSOBowe), manipulowanie danymi telemetrycznymi. Manipulowanie danymi kontroli poleceń umieszczonymi w kolejce lub w pamięci podręcznej. Manipulowanie pakietami aktualizacji konfiguracji lub oprogramowania układowego w pamięci podręcznej lub w kolejce lokalnie może prowadzić do naruszenia zabezpieczeń systemów operacyjnych i/lub |Szyfrowanie, kod uwierzytelniania wiadomości (MAC) lub podpis cyfrowy. Gdy jest to możliwe, silna kontrola dostępu za poorednictwem list kontroli dostępu do zasobów (ACL) lub uprawnień. |
| Obraz systemu operacyjnego urządzenia |TRID | |Manipulowanie systemem operacyjnym/Replacing składników systemu operacyjnego |Partycja systemu operacyjnego tylko do odczytu, obraz podpisanego systemu operacyjnego, szyfrowanie |
| Magazyn bramy pól (Kolejkowanie danych) |TRID |Szyfrowanie magazynu, podpisywanie dzienników |Odczytywanie danych z magazynu (dane OSOBowe), manipulowanie danymi telemetrycznymi, manipulowanie w kolejce lub buforowane dane kontroli poleceń. Manipulowanie pakietami aktualizacji konfiguracji lub oprogramowania układowego (przeznaczonych dla urządzeń lub bramą pól) w pamięci podręcznej lub w kolejce lokalnie może prowadzić do naruszenia zabezpieczeń systemów operacyjnych i/lub |Funkcja BitLocker |
| Obraz systemu operacyjnego bramy pola |TRID | |Manipulowanie systemem operacyjnym/Replacing składników systemu operacyjnego |Partycja systemu operacyjnego tylko do odczytu, obraz podpisanego systemu operacyjnego, szyfrowanie |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Przetwarzanie urządzeń i zdarzeń/strefa bramy w chmurze

Brama chmurowa to system, który umożliwia zdalną komunikację z i do urządzeń lub bram pól w różnych lokacjach w publicznej przestrzeni sieciowej, zazwyczaj w kierunku systemu kontroli i danych opartych na chmurze, a także do Federacji takich systemów. W niektórych przypadkach Brama chmury może natychmiast ułatwić dostęp do specjalnych urządzeń z terminali, takich jak tablety lub telefony. W kontekście omówionym tutaj "Chmura" odwołuje się do dedykowanego systemu przetwarzania danych, który nie jest powiązany z tą samą lokacją jak podłączone urządzenia lub bramy pól, i gdzie środki operacyjne uniemożliwiają dostęp fizyczny, ale nie musi to być Infrastruktura publiczna. Brama chmury może być potencjalnie zamapowana na nakładkę wirtualizacji sieci w celu izolowania bramy chmury oraz wszystkich dołączonych urządzeń lub bram pól z dowolnego innego ruchu sieciowego. Sama Brama w chmurze nie jest systemem sterowania urządzeniami ani funkcją przetwarzania ani magazynu dla danych urządzenia; Interfejs tych obiektów z bramą w chmurze. Strefa bramy chmury obejmuje samą bramę w chmurze wraz ze wszystkimi bramami i urządzeniami pól, bezpośrednio lub pośrednio dołączonymi do niej.

Brama w chmurze jest głównie niestandardową wbudowaną częścią oprogramowania działającego jako usługa z uwidocznionymi punktami końcowymi, do których są nawiązywane połączenia między bramą i urządzeniami W związku z tym musi być zaprojektowana z myślą o bezpieczeństwie. Postępuj zgodnie z procesem [SDL](https://www.microsoft.com/sdl) dla projektowania i kompilowania tej usługi.

#### <a name="services-zone"></a>Strefa usług

System kontroli (lub kontroler) to rozwiązanie programowe, które jest interfejsy z urządzeniem lub bramą w chmurze, w celu kontrolowania jednego lub wielu urządzeń i/lub do gromadzenia i/lub do przechowywania i/lub analizowania danych urządzenia do prezentacji lub do kolejnych celów kontroli. Systemy kontroli są jedynymi jednostkami w zakresie tej dyskusji, które mogą natychmiast ułatwić interakcję z użytkownikami. Wyjątki są pośrednimi powierzchniami kontroli fizycznej na urządzeniach, takich jak przełącznik, który umożliwia osobie wyłączenie urządzenia lub zmianę innych właściwości i dla których nie ma funkcjonalnej funkcjonalności, do którego można uzyskać dostęp cyfrowo.

Pośrednie powierzchnie kontroli fizycznej to te, w których logika, która ogranicza funkcję fizycznej kontroli, w taki sposób, że równoważna funkcja może zostać zainicjowana zdalnie lub można uniknąć konfliktów wejściowych z zdalnymi danymi wejściowymi — takie pośrednie powierzchnie kontroli są koncepcyjnie dołączone do lokalnego systemu kontroli, który wykorzystuje te same funkcje, co inne systemy zdalnego sterowania, do których urządzenie może być połączone równolegle. Najważniejsze zagrożenia w chmurze obliczeniowej można znaleźć na stronie [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) .

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Threat Modeling Tool SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Architektura referencyjna Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)

---
title: Przewodnik po platformie Azure dla rozwiązań do konserwacji predykcyjnej — proces nauki dotyczący danych zespołu
description: Kompleksowy opis nauki dotyczącej danych, który umożliwia obsługę predykcyjnych rozwiązań do konserwacji w wielu branżach pionowych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9b6cf2abfa797965d75bacd0b9c5e2eaf02b57a4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308578"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Przewodnik po platformie Azure dla rozwiązań do konserwacji predykcyjnej

## <a name="summary"></a>Podsumowanie

Konserwacja predykcyjna (**PDM**) to popularna aplikacja analizy predykcyjnej, która może pomóc firmom w kilku branżach osiągnąć duże wykorzystanie zasobów i oszczędności w kosztach operacyjnych. Ten przewodnik zawiera szczegółowe wytyczne biznesowe i analityczne oraz najlepsze rozwiązania w celu pomyślnego opracowania i wdrożenia rozwiązań PdM przy użyciu technologii [platformy AI Microsoft Azure](https://azure.microsoft.com/overview/ai-platform) .

W tym przewodniku zawarto informacje dotyczące branżowych scenariuszy handlowych i proces kwalifikowania tych scenariuszy do PdM. Dostępne są również wymagania dotyczące danych i techniki modelowania do kompilowania rozwiązań PdM. Główna Zawartość przewodnika dotyczy procesu nauki o danych — w tym kroki przygotowywania danych, Inżynieria funkcji, tworzenia modeli i operacjonalizacji modelu. Aby uzupełnić te kluczowe pojęcia, ten przewodnik zawiera listę szablonów rozwiązań ułatwiających przyspieszenie opracowywania aplikacji PdM. Przewodnik wskazuje również przydatne zasoby szkoleniowe dla praktykującego lekarza, aby dowiedzieć się więcej na temat danych o sztucznej wiedzy. 

### <a name="data-science-guide-overview-and-target-audience"></a>Omówienie przewodnika nauki o danych i docelowych odbiorców
W pierwszej połowie tego przewodnika opisano typowe problemy biznesowe, korzyści wynikające z wdrożenia PdM w celu rozwiązania tych problemów, a ponadto wymieniono typowe przypadki użycia. Ta zawartość będzie korzystna dla podmiotów podejmujących decyzje biznesowe (BDMs). Druga połowa wyjaśnia naukę danych za PdM i zawiera listę rozwiązań PdM utworzonych przy użyciu zasad opisanych w tym przewodniku. Zawiera również ścieżki szkoleniowe i wskaźniki do materiału szkoleniowego. Takie informacje będą przydatne w przypadku decyzji technicznych (TDMs).

| Rozpocznij od... | Jeśli jesteś... |
|:---------------|:---------------|
| [Przypadek biznesowy dla konserwacji predykcyjnej](#business-case-for-predictive-maintenance) |Producent decyzji biznesowej (BDM) chcący ograniczyć przestoje i koszty operacyjne oraz poprawić wykorzystanie sprzętu |
| [Analiza danych w celu przeprowadzenia konserwacji predykcyjnej](#data-science-for-predictive-maintenance) |Producent decyzji technicznej (TDM) oceniający technologie PdM w celu zrozumienia unikatowych wymagań dotyczących przetwarzania danych i AI do konserwacji predykcyjnej |
| [Szablony rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance)|architekt oprogramowania lub programista AI, który umożliwia szybkie przechodzenie do pokazu lub potwierdzenia koncepcji |
| [Szkoleniowe zasoby na potrzeby konserwacji predykcyjnej](#training-resources-for-predictive-maintenance) | dowolne lub wszystkie powyższe i chcą poznać podstawowe pojęcia związane z nauką, narzędziami i technikami danych.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza
Zawartość BDM nie oczekuje, aby czytelnik miał wcześniej wiedzę o nauce danych. W przypadku zawartości TDM jest przydatna podstawowa znajomość statystyk i analizy danych. Zalecane jest znajomość usług Azure Data and AI, Python, R, XML i JSON. Techniki AI są implementowane w pakietach Python i R. Szablony rozwiązań są implementowane przy użyciu usług platformy Azure, narzędzi programistycznych i zestawów SDK.

## <a name="business-case-for-predictive-maintenance"></a>Przypadek biznesowy dla konserwacji predykcyjnej

Firmy wymagają, aby sprzęt krytyczny działał z najwyższą wydajnością i wykorzystaniem, aby zrealizować zwroty z inwestycji kapitałowych. Te zasoby mogą być takie same jak silniki samolotów, turbiny, windy lub urządzenia chłodnicze, które umożliwiają obniżenie kosztów do codziennych urządzeń, takich jak kopiarki, urządzenia kawowe lub chłodnie wodne.
- Domyślnie większość firm korzysta z _konserwacji naprawczej_, w której części są zastępowane jako i po ich niepowodzeniu. Konserwacja naprawcza gwarantuje całkowite użycie części (w związku z tym nie powoduje marnowania okresu istnienia składników), ale koszt działalności w przypadku przestojów, robocizny i nieplanowanych wymagań konserwacyjnych (poza godzinami lub niewygodnymi lokalizacjami).
- Na następnym poziomie firmy przeprowadzają  _konserwację zapobiegawczą_, gdzie określają przydatną cykl życia częściową i utrzymują lub zastępują przed awarią. Konserwacja zapobiegawcza pozwala uniknąć nieplanowanych i katastrofalnych błędów. Jednak wysokie koszty zaplanowanego przestoju, pod względem użycia składnika w trakcie jego okresu istnienia i robocizny nadal pozostają.
- Celem _konserwacji predykcyjnej_ jest zoptymalizowanie równowagi między konserwacją naprawczą i zapobiegawczą przez włączenie _just in Time_ składników. Takie podejście zastępuje te składniki tylko wtedy, gdy znajdują się w pobliżu błędu. Przez rozszerzenie lifespans składników (w porównaniu do konserwacji zapobiegawczej) i zmniejszenie nieplanowanych kosztów konserwacji i robocizny (dzięki obsłudze naprawczej) firmy mogą uzyskać oszczędności i konkurencyjne korzyści.

## <a name="business-problems-in-pdm"></a>Problemy biznesowe w programie PdM
Firmy mają duże ryzyko dla działania z powodu nieoczekiwanych awarii i mają ograniczone informacje o głównej przyczynie problemów w złożonych systemach. Oto niektóre z najważniejszych pytań dotyczących firmy:

- Wykrywaj anomalie w sprzęcie lub wydajności systemu lub funkcji.
- Przewidywanie, czy element zawartości może zakończyć się niepowodzeniem w najbliższej przyszłości.
- Oszacuj pozostały okres istnienia elementu zawartości.
- Zidentyfikuj główne przyczyny niepowodzenia elementu zawartości.
- Określ czynności konserwacyjne, które należy wykonać, gdy w elemencie zawartości.

Typowe instrukcje celu z PdM są następujące:

- Zmniejszenie ryzyka operacyjnego sprzętu o krytycznym znaczeniu.
- Zwiększ stopień powrotu do zasobów, przechodząc do niepowodzeń przed ich wystąpieniem.
- Kontroluj koszt konserwacji przez włączenie operacji konserwacji just-in-Time.
- Obniż attrition klienta, ulepszaj obraz marki i przeniesiej sprzedaż.
- Obniżenie kosztów spisu przez zredukowanie poziomów zapasów przez przewidywanie punktu zmiany kolejności.
- Odnajdź wzorce połączone z różnymi problemami z konserwacją.
- Podaj wskaźniki KPI (kluczowe wskaźniki wydajności), takie jak wyniki kondycji dla warunków użytkowania.
- Oszacuj pozostałe cykl życia zasobów.
- Zalecamy czasowe działania konserwacyjne.
- Włącz spis just in Time, szacując daty zamówienia na zastępowanie części.

Te instrukcje celu są punktami początkowymi dla:

- analityków _danych_ do analizowania i rozwiązywania określonych problemów predykcyjnych.
- Dzięki _architektom i deweloperom chmury_ można kompleksowo wdrożyć rozwiązanie.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kwalifikujące się problemy związane z konserwacją predykcyjną
Ważne jest, aby podkreślić, że nie wszystkie przypadki użycia lub problemy biznesowe mogą być skutecznie rozwiązywane przez PdM. Istnieją trzy ważne kryteria kwalifikujące, które należy wziąć pod uwagę podczas wyboru problemu:

- Problem musi być predykcyjny z natury. oznacza to, że powinna istnieć wartość docelowa lub wynik do przewidywania. Problem powinien również mieć jasną ścieżkę działania, aby zapobiec błędom podczas ich wykrycia.
- Problem powinien zawierać rekord historii operacyjnej sprzętu, który zawiera _zarówno dobre, jak i złe wyniki_. Zestaw działań podjętych w celu ograniczenia nieprawidłowych wyników powinien również być dostępny w ramach tych rekordów. Raporty o błędach, dzienniki konserwacji dotyczące obniżenia wydajności, naprawy i zastępowania dzienników są również ważne. Ponadto przydatne są naprawy podejmowane w celu ich poprawy i zastępowanie rekordów.
- Zarejestrowana historia powinna być odzwierciedlona w _odpowiednich_ danych, które mają _wystarczającą_ wystarczającą jakość do obsługi przypadku użycia. Aby uzyskać więcej informacji na temat istotności i adekwatności danych, zobacz [wymagania dotyczące danych w przypadku konserwacji predykcyjnej](#data-requirements-for-predictive-maintenance).
- Na koniec firma powinna dysponować ekspertami w dziedzinie domeny, którzy mają zrozumiałe zrozumienie problemu. Należy pamiętać o wewnętrznych procesach i praktykach, aby pomóc analitykowi zrozumieć i zinterpretować dane. Powinny być one również w stanie wprowadzić niezbędne zmiany w istniejących procesach biznesowych, aby ułatwić gromadzenie odpowiednich danych dotyczących problemów, w razie potrzeby.

## <a name="sample-pdm-use-cases"></a>Przykładowe przypadki użycia PdM
Ta sekcja koncentruje się na zbieraniu PdM przypadków użycia z kilku branż, takich jak Aerospace, narzędzia i transport. Każda sekcja rozpoczyna się od problemu biznesowego i omawia korzyści z PdM, odpowiednie dane otaczające problem biznesowy oraz zalety rozwiązania PdM.

| Problem biznesowy | Korzyści z PdM |
|:-----------------|-------------------|
|**Lotnictwo**      |                   |
|_Opóźnienie lotu i anulowanie_ z powodu problemów mechanicznych. Awarie, których nie można naprawić w czasie, mogą spowodować anulowanie lotów i zakłócenie planowania i operacji. |Rozwiązania PdM umożliwiają prognozowanie prawdopodobieństwa, że samolot jest opóźniony lub anulowany z powodu awarii mechanicznej.|
|_Awaria części silnika samolotu_: przemieszczenie części aparatu samolotu to najbardziej typowe zadania konserwacyjne w branży lotniczej. Rozwiązania do konserwacji wymagają starannego zarządzania dostępnością, dostawą i planowaniem zasobów składnika.|Możliwość zbierania informacji o niezawodności składników prowadzi do znacznego obniżenia kosztów inwestycyjnych.|
|**Finanse** |                         |
|_Awaria ATM_ to typowy problem w branży bankowej. Ten problem polega na zgłoszeniu prawdopodobieństwa przerwania transakcji wycofywania środków pieniężnych ATM ze względu na zakleszczenie papieru lub awarię części. W oparciu o przewidywania niepowodzeń transakcji ATMs może być serwisowany aktywnie, aby zapobiec wystąpieniu błędów.| Zamiast zezwalać komputerowi na niepowodzenie w połowie transakcji, poprzednia alternatywą jest zaprogramowanie maszyny w celu odmowy usługi opartej na prognozie.|
|**Efektywności** |                          |
|_Awarie programu wiatru turbin_: turbiny wiatrowe są głównym źródłem energii w krajach/regionach odpowiedzialnych za środowisko i obejmują wysokie koszty. Kluczowy składnik w turbinach wiatru jest silnikiem generatora, którego awaria nieskuteczna renderuje turbinę. Rozwiązanie jest również wysoce kosztowne.|Przewidywanie kluczowych wskaźników wydajności, takich jak MTTF (średni czas do niepowodzenia), może pomóc firmom w zapobieganiu awariom turbiny oraz zapewnić minimalny czas przestoju. Błędy prawdopodobieństwa będą informować techników o monitorowaniu turbin, które prawdopodobnie wkrótce zakończą się niepowodzeniem, i zaplanować system konserwacji oparty na czasie. Modele predykcyjne zapewniają wgląd w różne czynniki, które przyczyniają się do awarii, co ułatwia inżynierom lepsze zrozumienie głównych przyczyn problemów.|
|_Awarie_ wyłącznika: dystrybucja energii elektrycznej do domów i firm wymaga, aby linie zasilania działały przez cały czas w celu zagwarantowania dostarczania energii. Wyłączniki pomagają ograniczyć lub uniknąć uszkodzenia linii zasilającej podczas przeciążania lub niekorzystnych warunków pogodowych. Problemem biznesowym jest przewidywanie awarii wyłącznika.| Rozwiązania PdM pomagają zmniejszyć koszty naprawy i zwiększyć cykl życia sprzętu, takie jak wyłączniki. Ułatwiają one zwiększenie jakości sieci, skracając nieoczekiwane awarie i przerwy w świadczeniu usług.|
|**Transport i logistyka** |    |
|_Awarie drzwi Wind_: duże firmy Wind oferują pełną usługę stosu dla milionów Wind funkcjonalnych na całym świecie. Najważniejsze problemy związane z bezpieczeństwem, niezawodnością i przestojem podnośnika. Te firmy śledzą te i różne inne atrybuty za pomocą czujników, aby ułatwić im Rozwiązywanie problemów i konserwację. W przypadku windy najbardziej widocznym problemem z klientami jest nieprawidłowe działanie drzwi windy. Problem biznesowy w tym przypadku ma na celu dostarczenie aplikacji predykcyjnej bazy wiedzy, która przewiduje potencjalne przyczyny awarii drzwi.| Windy to inwestycje inwestycyjne w przypadku potencjalnie 20-30 roku cykl życia. Dzięki temu każda potencjalna sprzedaż może być wysoce konkurencyjna; w związku z tym oczekiwania dotyczące usługi i pomocy technicznej są wysokie. Konserwacja predykcyjna może zapewnić tym firmom zalety korzystania przez ich konkurentów z oferowanych przez nich produktów i usług.|
|_Awarie kół_: w przypadku awarii kółka dla połowy wszystkich poręczeń i kosztów w branży kolejek globalnych. Awarie kółka również powodują pogorszenie się szyn, czasami powodując przedwczesne przerwanie działania szyny. Przerwy w działaniu szyny prowadzą do katastrofalnych zdarzeń, takich jak deszyny. Aby uniknąć takich wystąpień, szyny monitorują wydajność kół i zastępują je w sposób zapobiegawczy. Problem biznesowy jest przewidywany w przypadku awarii koła.| Konserwacja predykcyjna kół będzie pomocna w przypadku wymiany kół just-in-Time |
|_Awarie drzwi Subway pociąg_: główną przyczyną opóźnień w operacjach Subway są awarie drzwi samochodów. Problemem biznesowym jest przewidywanie awarii drzwi szkolenia.|Wczesna świadomość awarii drzwi lub liczby dni do momentu awarii drzwi, pomoże zoptymalizować harmonogram obsługi drzwi szkolenia.|

W następnej sekcji znajdują się szczegółowe informacje o tym, jak zrealizować zalety PdM omówione powyżej.

## <a name="data-science-for-predictive-maintenance"></a>Analiza danych w celu przeprowadzenia konserwacji predykcyjnej

Ta sekcja zawiera ogólne wytyczne dotyczące zasad i praktycznej analizy danych dla PdM. Ma ona na celu pomoc w TDM, architektze rozwiązań lub deweloperom zrozumienie wymagań wstępnych i procesu tworzenia kompleksowych aplikacji AI dla PdM. W tej sekcji można zapoznać się z przeglądem pokazów i szablonów weryfikacji koncepcji wymienionych w [szablonach rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance). Za pomocą tych zasad i najlepszych rozwiązań można wdrożyć rozwiązanie PdM na platformie Azure.

> [!NOTE]
> Ten przewodnik nie jest przeznaczony do uczenia się w nauce danych czytelnika. Kilka przydatnych źródeł jest dostępnych do dalszej części sekcji dotyczącej [szkoleń dotyczących zasobów związanych z konserwacją predykcyjną](#training-resources-for-predictive-maintenance). [Szablony rozwiązań](#solution-templates-for-predictive-maintenance) wymienione w przewodniku przedstawiają niektóre z tych technik AI dla określonych problemów PDM.

## <a name="data-requirements-for-predictive-maintenance"></a>Wymagania dotyczące danych w przypadku konserwacji predykcyjnej

Sukces uczenia się zależy od (a) jakości, w jaki sposób uczy się i (b) umiejętności. Modele predykcyjne przedstawiają wzorce z danych historycznych i przewidywania przyszłych rezultatów o określonym prawdopodobieństwie w oparciu o te obserwowane wzorce. Dokładność predykcyjna modelu zależy od dokładność, zgodności i jakości danych szkoleniowych i testowych. Nowe dane, które są "oceniane" przy użyciu tego modelu, powinny mieć te same funkcje i schemat jak dane szkoleniowe/testowe. Charakterystyka funkcji (typ, gęstość, dystrybucja itp.) nowych danych powinna być zgodna z zestawami danych szkoleniowych i testowych. Ta sekcja dotyczy takich wymagań dotyczących danych.

### <a name="relevant-data"></a>Odpowiednie dane

Najpierw dane powinny być _istotne dla danego problemu_. Rozważ przypadek użycia _koła_ z opisem powyżej — dane szkoleniowe powinny zawierać funkcje związane z operacjami pokrętła. Jeśli problem był przewidywany w przypadku awarii  _systemu trakcyjnego_, dane szkoleniowe mają obejmować wszystkie różne składniki systemu trakcyjnego. Pierwszy przypadek odwołuje się do określonego składnika, podczas gdy drugi przypadek wskazuje na awarię większego podsystemu. Ogólnym zaleceniem jest zaprojektowanie systemów predykcyjnych dotyczących określonych składników, a nie większych podsystemów, ponieważ ta ostatnia będzie zawierać bardziej rozproszone dane. Ekspert domeny (zobacz [problemy uprawniające do konserwacji predykcyjnej](#qualifying-problems-for-predictive-maintenance)) powinien pomóc w wyborze najbardziej odpowiednich podzbiorów danych do analizy. Odpowiednie źródła danych zostały omówione bardziej szczegółowo w temacie [przygotowanie danych do konserwacji predykcyjnej](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Wystarczające dane
Dwa pytania są często zadawane w odniesieniu do danych historii błędów: (1) "ile zdarzeń awarii jest wymaganych do uczenia modelu?". (2) "ile rekordów jest traktowanych jako" wystarczające "? Brak ostatecznych odpowiedzi, ale tylko reguły elementu kciuk. Dla (1), większa liczba zdarzeń błędów, lepszy model. Dla (2) i dokładna liczba zdarzeń niepowodzeń zależy od danych i kontekstu rozwiązywania problemu. Jednak po stronie odwracania, jeśli maszyna ulegnie awarii zbyt często, firma zamieni ją, co spowoduje zredukowanie wystąpienia awarii. W tym miejscu wskazówki dotyczące eksperta domeny są ważne. Istnieją jednak metody pozwalające sprostać problemom _rzadkich zdarzeń_. Są one omówione w sekcji [Obsługa niezrównoważonych danych](#handling-imbalanced-data).

### <a name="quality-data"></a>Dane jakości
Jakość danych jest krytyczna — każda wartość atrybutu predykcyjnego musi być _dokładna_ w połączeniu z wartością zmiennej docelowej. Jakość danych to dobrze przeprowadzona część statystyk i zarządzania danymi, która z tego przewodnika jest poza zakresem.

> [!NOTE]
> Istnieje kilka zasobów i produktów korporacyjnych, które zapewniają dane dotyczące jakości. Poniżej przedstawiono przykładowe odwołania:
> - Dasu, T, Johnsonem, T, górnictwo i czyszczenie danych, Wiley, 2003.
> - [Analiza danych badawczych, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, ilościowe czyszczenie danych dla dużych baz danych](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, Van der wy, M, wprowadzenie do czyszczenia danych przy użyciu języka R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Przygotowywanie danych do konserwacji predykcyjnej

### <a name="data-sources"></a>Źródła danych

Odpowiednie źródła danych do konserwacji predykcyjnej obejmują, ale nie są ograniczone do:
- Historia niepowodzeń
- Historia konserwacji/naprawy
- Warunki operacyjne maszyny
- Metadane sprzętu

#### <a name="failure-history"></a>Historia niepowodzeń
Zdarzenia błędów są rzadkie w aplikacjach PdM. Jednak podczas kompilowania modeli predykcyjnych algorytm musi poznać normalny wzorzec operacyjny składnika, a także jego wzorce niepowodzeń. Dlatego dane szkoleniowe powinny zawierać wystarczającą liczbę przykładów z obu kategorii. Rekordy konserwacji i historia wymiany części są dobrym źródłem do znajdowania zdarzeń awarii. Korzystając z pomocy dotyczącej niektórych domen, anomalie w danych szkoleniowych można także definiować jako błędy.

#### <a name="maintenancerepair-history"></a>Historia konserwacji/naprawy
Historia konserwacji elementu zawartości zawiera szczegółowe informacje o składnikach, wykonanych czynnościach naprawczych itp. Te zdarzenia mają wzorce obniżenia wydajności. Brak tych ważnych informacji w danych szkoleniowych może prowadzić do mylących wyników modelu. Historię błędów można także znaleźć w historii konserwacji jako specjalne kody błędów lub w kolejności dat dla części. Dodatkowe źródła danych, które mają wpływ na wzorce błędów, powinny być badane i udostępniane przez ekspertów domeny.

#### <a name="machine-operating-conditions"></a>Warunki operacyjne maszyny
Dane przesyłane strumieniowo na podstawie czujnika (lub innego) w działaniu są ważnym źródłem danych. Kluczem założeń w PdM jest to, że stan kondycji maszyny jest zmniejszany w miarę upływu czasu podczas jego rutynowej operacji. Dane powinny zawierać różne czasowo funkcje, które przechwytują ten wzorzec przedawniania i wszelkie anomalie prowadzące do obniżenia wydajności. Nieczasowy aspekt danych jest wymagany dla algorytmu, aby poznać wzorce niepowodzeń i niepowodzeń w czasie. W oparciu o te punkty danych algorytm uczy się przewidzieć, ile więcej jednostek czasu może nadal działać na maszynie, zanim zakończy się niepowodzeniem.

#### <a name="static-feature-data"></a>Statyczne dane funkcji
Funkcjami statycznymi są metadane dotyczące sprzętu. Przykłady to sprzęt Marka, model, Data wyprodukowania, Data rozpoczęcia usługi, lokalizacja systemu oraz inne specyfikacje techniczne.

Przykłady odpowiednich danych dla [przykładowych przypadków użycia PDM](#sample-pdm-use-cases) są podane poniżej:

| Przypadek użycia | Przykłady istotnych danych |
|:---------|---------------------------|
|_Opóźnienie i anulowanie lotu_ | Informacje o trasie lotu w formie etapów lotów i dzienników stron. Dane dotyczące etapu lotu obejmują szczegóły routingu, takie jak Data wyjazdu/przybycia, czas, Port lotniczy, layovers itp. Dziennik stron zawiera serię kodów błędów i obsługi zarejestrowanych przez personel konserwacyjny.|
|_Awaria części silnika samolotu_ | Dane zbierane z czujników w samolocie, które dostarczają informacji na temat stanu różnych części. Rekordy konserwacji pomagają identyfikować, kiedy wystąpiły awarie składników i kiedy zostały zastąpione.|
|_Awaria ATM_ | Odczyty czujników dla każdej transakcji (depozytowe środki pieniężne/sprawdzanie) i opłaty za środki pieniężne. Informacje o pomiarze przerwy między notatkami, grubością notatki, odległością przybycia uwagi, sprawdzaniem atrybutów itp. Rekordy konserwacji, które zawierają kody błędów, informacje o naprawie, podczas ostatniego zapełniania rozdzielacza środków pieniężnych.|
|_Awaria wiatru_ | Czujniki czujników, takie jak temperatura, kierunek wiatru, wygenerowana energia, szybkość generatora itp. Dane są zbierane z wielu turbin wiatrów z Farm wiatrów, które znajdują się w różnych regionach. Zazwyczaj każda turbina będzie miała wiele odczytów czujników przekazujących pomiary w ustalonym przedziale czasu.|
|_Awarie wyłącznika_ | Dzienniki konserwacji, które obejmują działania naprawcze, zapobiegawcze i systematyczne. Dane operacyjne, które obejmują automatyczne i ręczne polecenia wysyłane do wyłączników, takich jak akcje otwierania i zamykania. Metadane urządzenia, takie jak data produkcji, lokalizacja, model itp. Specyfikacje wyłącznika, takie jak poziomy napięcia, geolokalizacja, warunki otoczenia.|
|_Awarie drzwi Wind_| Metadane Wind, takie jak typ windy, Data wyprodukowania, częstotliwość konserwacji, typ budynku itd. Informacje operacyjne, takie jak liczba cykli drzwi, średni czas zamykania drzwi. Historia niepowodzeń z przyczynami.|
|_Awarie kółka_ | Dane czujnika, które mierzą przyspieszenie kół, wystąpienia hamowania, odległość, prędkość itp. Informacje statyczne na kołach, takie jak producent, Data wyprodukowania. Dane niepowodzeń zostały wywnioskowane z bazy danych kolejności części, która śledzi daty i liczby zamówień.|
|_Subway awarie drzwi_ | Czas otwierania i zamykania drzwi, inne dane operacyjne, takie jak bieżący stan drzwi pociągu. Dane statyczne mogą zawierać kolumny Identyfikator zasobu, czas i wartość warunku.|

### <a name="data-types"></a>Typy danych
Mając powyższe źródła danych, dwa główne typy danych obserwowane w domenie PdM są następujące:

- _Dane_ czasowe: Telemetria operacyjna, warunki maszynowe, typy zamówień pracy, kody priorytetów, które będą miały sygnatury czasowe podczas rejestrowania. Niepowodzenie, konserwacja/naprawa oraz historia użycia również sygnatury czasowe skojarzone z poszczególnymi zdarzeniami.
- _Dane statyczne_: funkcje maszyny i funkcje operatora ogólnie są statyczne, ponieważ opisują techniczne specyfikacje maszyn lub atrybutów operatora. Jeśli te funkcje mogą ulec zmianie z upływem czasu, powinny również mieć skojarzone z nimi sygnatury czasowe.

Zmienne predykcyjne i docelowe powinny być wstępnie przetworzone/przekształcone na wartości [liczbowe, kategorii i inne typy danych](https://www.statsdirect.com/help/basics/measurement_scales.htm) w zależności od używanego algorytmu.

### <a name="data-preprocessing"></a>Przetwarzanie wstępne danych
Jako warunek wstępny dla _inżynierii funkcjonalnej_ Przygotuj dane z różnych strumieni, aby utworzyć schemat, z którego można łatwo tworzyć funkcje. Wizualizuj dane najpierw jako tabelę rekordów. Każdy wiersz w tabeli reprezentuje wystąpienie szkoleniowe, a kolumny reprezentują funkcje _predykcyjne_ (nazywane również niezależnymi atrybutami lub zmiennymi). Organizuj dane w taki sposób, aby ostatnie kolumny były _elementem docelowym_ (zmienna zależna). Dla każdego wystąpienia szkoleniowego Przypisz _etykietę_ jako wartość tej kolumny.

W przypadku danych czasowych Podziel okres istnienia danych czujników na jednostki czasu. Każdy rekord powinien należeć do jednostki czasu dla elementu zawartości _i powinien oferować odrębne informacje_. Jednostki czasu są definiowane w zależności od potrzeb firmy w wielu sekundach, minutach, godzinach, dniach, miesiącach i tak dalej. Jednostka czasu nie _musi być taka sama jak częstotliwość zbierania danych_. Jeśli częstotliwość jest wysoka, dane mogą nie zawierać żadnych znaczących różnic między jednostkami. Załóżmy na przykład, że temperatura otoczenia była zbierana co 10 sekund. Użycie tego samego interwału w przypadku danych szkoleniowych powoduje jedynie wyflatenie liczby przykładów bez podawania jakichkolwiek dodatkowych informacji. W takim przypadku lepszym rozwiązaniem jest użycie średniej danych przez 10 minut lub godzinę w oparciu o uzasadnienie biznesowe.

Dla danych statycznych,
- _Rekordy konserwacji_: surowe dane obsługi mają identyfikator zasobu i sygnaturę czasową z informacjami o działaniach konserwacyjnych wykonanych w danym punkcie w czasie. Przekształć działania konserwacyjne w kolumny _kategorii_ , gdzie każdy deskryptor kategorii jednoznacznie mapuje na określoną akcję konserwacji. Schemat dla rekordów konserwacji obejmuje identyfikator zasobu, czas i akcję konserwacji.

- _Rekordy błędów_: przyczyny niepowodzenia lub niepowodzenia mogą być rejestrowane jako określone kody błędów lub zdarzenia błędów zdefiniowane w określonych warunkach firmy. W przypadkach, gdy sprzęt ma wiele kodów błędów, ekspert domeny powinien pomóc zidentyfikować te, które są istotne dla zmiennej docelowej. Użyj pozostałych kodów błędów lub warunków do skonstruowania funkcji _predykcyjnych_ , które są skorelowane z tymi awariami. Schemat dla rekordów błędów będzie zawierać identyfikator elementu zawartości, godzinę, Niepowodzenie lub przyczynę niepowodzenia — jeśli jest dostępny.

- _Metadane maszyn i operatorów_: scalanie maszyn i danych operatora w jeden schemat w celu skojarzenia elementu zawartości z jego operatorem wraz z odpowiednimi atrybutami. Schemat warunków komputera obejmuje identyfikator zasobu, funkcje elementów zawartości, identyfikator operatora i funkcje operatorów.

Inne kroki przetwarzania przed przetwarzaniem danych obejmują _obsługę brakujących wartości_ i _normalizację_ wartości atrybutów. Szczegółowa dyskusja jest poza zakresem tego przewodnika — Zobacz następną sekcję, aby uzyskać przydatne informacje.

W przypadku powyższego wstępnie przetworzonego źródła danych, końcowa transformacja przed funkcją inżynierii funkcji polega na przyłączeniu powyższych tabel na podstawie identyfikatora zasobu i sygnatury czasowej. Ta tabela będzie miała wartości null dla kolumny Failure, gdy maszyna jest w normalnej operacji. Te wartości null mogą być przypisane przez wskaźnik dla normalnej operacji. Ta kolumna niepowodzeń służy do tworzenia _etykiet dla modelu predykcyjnego_. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [technik modelowania do konserwacji predykcyjnej](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Inżynieria cech
Inżynieria funkcji to pierwszy krok przed modelem danych. Jej rola w procesie nauki o danych [została opisana tutaj](./create-features.md). _Funkcja_ jest atrybutem predykcyjnym dla modelu — takim jak temperatura, ciśnienie, wibracje i tak dalej. W przypadku usługi PdM funkcja inżynierii obejmuje abstrakcyjną kondycję maszyny przez dane historyczne zebrane w czasie trwania o zmiennym rozmiarze. W tym sensie różni się od jego elementów równorzędnych, takich jak zdalne monitorowanie, wykrywanie anomalii i wykrywanie awarii. 

### <a name="time-windows"></a>Okna czasu
Zdalne monitorowanie polega na raportowaniu zdarzeń, które _wystąpiły_ od momentu w czasie. Modele wykrywania anomalii szacują (ocenę) przychodzące strumienie danych w celu oflagowania anomalii od momentów w czasie. Wykrywanie awarii klasyfikuje błędy jako określone typy, gdy występują punkty w czasie. Z kolei PdM obejmuje przewidywania błędów w _przyszłych okresach_, na podstawie funkcji, które reprezentują zachowanie maszyny w _okresie historycznym_. W przypadku PdMnia dane funkcji z poszczególnych punktów czasu są zbyt dużo szumów do predykcyjnego. Dlatego dane dla każdej funkcji muszą być _smoothened_ przez agregację punktów danych w oknach czasu.

### <a name="lag-features"></a>Funkcje opóźnienia
Wymagania biznesowe określają, jak daleko model ma być przewidywany w przyszłości. Z kolei ten czas trwania pozwala określić, jak daleko od tyłu model musi wyglądać, aby przetworzyć te przewidywania. Ten okres "wyszukiwania wsteczny" jest nazywany _opóźnieniem_, a funkcje w tym okresie zwłoki są nazywane _funkcjami opóźnienia_. W tej sekcji omówiono funkcje opóźnienia, które mogą być zbudowane ze źródeł danych z sygnaturami czasowymi, oraz tworzenie funkcji ze źródeł danych statycznych. Funkcje opóźnienia są zwykle _numeryczne_ .

> [!IMPORTANT]
> Rozmiar okna jest określany za pośrednictwem eksperymentów i powinien być końcowy przy pomocy eksperta domeny. Takie samo zastrzeżenie obejmuje wybór i definicję funkcji zwłoki, ich agregacje i typ systemu Windows.

#### <a name="rolling-aggregates"></a>Agregacje kroczące
Dla każdego rekordu zasobu, stopniowe okno rozmiaru "W" jest wybierane jako liczba jednostek czasu do obliczenia zagregowanych wartości. Funkcje opóźnienia są następnie obliczane przy użyciu wartości W okresach _przed datą_ tego rekordu. Na rysunku 1 niebieskie linie zawierają wartości czujnika zarejestrowane dla elementu zawartości dla poszczególnych jednostek czasu. Oznaczają kroczącą średnią wartości funkcji w oknie o rozmiarze W = 3. Średnia krocząca jest obliczana na wszystkich rekordach z sygnaturami czasowymi z zakresu t<sub>1</sub> (w kolorze pomarańczowym) do t<sub>2</sub> (zielony). Wartość w parametrze W jest zazwyczaj w ciągu kilku minut lub godzin, w zależności od charakteru danych. Jednak w przypadku niektórych problemów wybranie dużej wartości W (poczekaj 12 miesięcy) może zapewnić pełną historię elementu zawartości do momentu zapisania tego rekordu.

![Rysunek 1. Funkcje agregujące agregowania](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Rysunek 1. Funkcje agregujące agregowania

Przykładami agregacji kroczących w przedziale czasu są miary Count, Average, CUMESUM (skumulowany sum), minimalna/maksymalna wartość. Ponadto często używane są wariancje, odchylenie standardowe i liczba elementów odstających poza N odchyleniami standardowymi. Przykłady wartości zagregowanych, które mogą być stosowane do [przypadków użycia](#sample-pdm-use-cases) w tym przewodniku, są wymienione poniżej. 
- _Opóźnienie lotu_: liczba kodów błędów w ciągu ostatniego dnia/tygodnia.
- _Awaria części silnika samolotu_: Metoda krocząca, odchylenie standardowe i suma w ciągu ostatniego dnia, tydzień itd. Ta Metryka powinna być określona wraz z ekspertem domeny biznesowej.
- _Awarie sieci ATM_: Metoda krocząca, mediana, zakres, odchylenia standardowe, liczba elementów wykraczających poza trzy odchylenia standardowe, górny i dolny CUMESUM.
- _Awarie drzwi Subway pociąg_: liczba zdarzeń w ciągu ostatnich dni, tydzień, dwa tygodnie itd.
- _Awarie wyłącznika_: liczba błędów w ubiegłym tygodniu, rok, trzy lata itd.

Kolejną przydatną techniką w PdM jest przechwytywanie zmian trendów, skoków i zmian na poziomie przy użyciu algorytmów, które wykrywają anomalie w danych.

#### <a name="tumbling-aggregates"></a>Agregaty wirowania
Dla każdego oznaczonego rekordem elementu zawartości jest zdefiniowane okno rozmiaru z _-<sub>k</sub>_ , gdzie _k_ jest liczbą okien rozmiaru _w_. Agregacje są następnie tworzone za _pomocą_ _wirowania systemu Windows_ _w-k, w-<sub>(k-1)</sub>,..., z-<sub>2</sub>, z-<sub>1</sub>_ dla okresów przed sygnaturą czasową rekordu. _k_ może być małą liczbą, aby przechwytywać skutki krótkoterminowe lub dużą liczbę do przechwytywania długoterminowych wzorców obniżenia poziomu. (zobacz rysunek 2).

![Rysunek 2. Funkcje agregujące wirowania](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Rysunek 2. Funkcje agregujące wirowania

Na przykład, funkcje zwłoki dla obudowy wiatru można utworzyć przy użyciu wartości z W zakresie od 1 do k = 3. Oznacza to zwłokę dla każdego z ostatnich trzech miesięcy przy użyciu wartości górnych i dolnych.

### <a name="static-features"></a>Funkcje statyczne

Specyfikacje techniczne sprzętu, takie jak data produkcji, numer modelu, lokalizacja, to przykłady funkcji statycznych. Są one traktowane jako zmienne _kategorii_ do modelowania. Kilka przykładów przypadku użycia wyłącznika to napięcie, bieżące, pojemność zasilania, Typ transformatora i źródło zasilania. W przypadku awarii kół typ kół opon (Stop vs-stal) jest przykładem.

Wysiłki do przygotowania danych omówione do tej pory powinny prowadzić do organizowania danych, jak pokazano poniżej. Dane szkoleniowe, testowe i walidacji powinny mieć ten logiczny schemat (w tym przykładzie pokazano czas w jednostkach dni).

| Identyfikator elementu zawartości | Godzina | \<Feature Columns> | Etykieta |
| ---- | ---- | --- | --- |
| A123 |Dzień 1 | . . . | . |
| A123 |Dzień 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dzień 1 | . . . | . |
| B234 |Dzień 2 | . . . | . |
| ...  |...   | . . . | . |

Ostatnim krokiem w inżynierii funkcji jest **etykietowanie** zmiennej docelowej. Ten proces zależy od techniki modelowania. Z kolei Technika modelowania zależy od problemu biznesowego i rodzaju dostępnych danych. Etykietowanie jest omówione w następnej sekcji.

> [!IMPORTANT]
> Przygotowanie danych i inżynieria funkcji są ważne jako techniki modelowania do przybycia do udanych rozwiązań PdM. Ekspert domeny i lekarz powinien zainwestować znaczący czas w pobycie odpowiednich funkcji i danych dla modelu. Poniżej przedstawiono niewielki przykład z wielu książek dotyczących inżynierii funkcji:
> - Pyle, D. Przygotowywanie danych do wyszukiwania danych (seria Morgan Kaufmann w systemach Zarządzanie danymi), 1999
> - Zheng, A., Casari, A. feature Engineering for Machine Learning: zasady i techniki dla analityków danych, O'Reilly, 2018.
> - Dong, G. Liu, H. (redaktorzy), Inżynieria funkcji dla Machine Learning i analizy danych (Chapman & wyszukiwać danych i serii informacji o produkcie), prasy CRC, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Techniki modelowania do konserwacji predykcyjnej

W tej sekcji omówiono główne techniki modelowania dotyczące problemów z PdM, a także ich specyficzne metody konstrukcji etykiet. Należy zauważyć, że pojedyncza Technika modelowania może być używana w różnych branżach. Technika modelowania jest sparowana z problemem analizy danych, a nie kontekstem danych.

> [!IMPORTANT]
> Wybór etykiet dla przypadków awarii i strategii etykietowania  
> należy określić w konsultacji z ekspertem domeny.

### <a name="binary-classification"></a>Klasyfikacja binarna
Klasyfikacja binarna jest używana do _przewidywania prawdopodobieństwa, że część sprzętu w przyszłości zakończy się niepowodzeniem_ — o nazwie "okres czasu w _przyszłości" X_. X jest określany przez problem biznesowy i dane, które są dostępne w konsultacji z ekspertem domeny. Oto przykłady:
- _minimalny czas realizacji_ wymagany do zastąpienia składników, wdrożenia zasobów konserwacji, przeprowadzenia konserwacji, aby uniknąć problemu, który prawdopodobnie wystąpi w tym okresie.
- _minimalna liczba zdarzeń_ , które mogą wystąpić przed wystąpieniem problemu.

W tej metodzie są identyfikowane dwa typy przykładów szkoleniowych. Pozytywny przykład, _który wskazuje błąd_, z etykietą = 1. Negatywny przykład, który wskazuje na normalne operacje, z etykietą = 0. Zmienna docelowa, dlatego wartości etykiet są _kategorii_. Model powinien identyfikować każdy nowy przykład, co może zakończyć się niepowodzeniem lub działać zwykle w ciągu następnych X jednostek czasu.

#### <a name="label-construction-for-binary-classification"></a>Konstrukcja etykiety dla klasyfikacji binarnej
Poniżej znajduje się pytanie: "co to jest prawdopodobieństwo, że element zawartości zakończy się niepowodzeniem w ciągu następnych X jednostek czasu?" Aby odpowiedzieć na to pytanie, należy oznaczyć rekordy X przed awarią elementu zawartości jako "informacje o niepowodzeniu" (etykieta = 1) i oznaczyć wszystkie pozostałe rekordy jako "normalne" (etykieta = 0). (zobacz rysunek 3).

![Rysunek 3. Etykietowanie klasyfikacji binarnej](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Rysunek 3. Etykietowanie klasyfikacji binarnej

Poniżej wymieniono przykłady strategii etykietowania niektórych przypadków użycia.
- _Opóźnienia lotu_: wartość X można wybrać jako jeden dzień, aby przewidzieć opóźnienia w ciągu następnych 24 godzin. Następnie wszystkie loty, które znajdują się w ciągu 24 godzin przed błędami, są oznaczone jako 1.
- _Niepowodzenia środków pieniężnych ATM_: celem może być określenie prawdopodobieństwa niepowodzenia transakcji w ciągu następnej godziny. W takim przypadku wszystkie transakcje, które wystąpiły w ciągu ostatniej godziny awarii, są oznaczone jako 1. Aby przewidywanie prawdopodobieństwa awarii w przypadku następnych N informacji o walutach, wszystkie uwagi, które zależą od ostatnich N uwagi o błędzie, są oznaczone jako 1.
- _Awarie_ wyłącznika: celem może być przewidywanie awarii następnego wyłącznika. W takim przypadku wybrano opcję X jako jedno polecenie w przyszłości.
- _Awarie drzwi szkolenia_: X można wybrać jako dwa dni.
- _Awarie z turbiny wiatru_: X może być wybrana jako dwa miesiące.

### <a name="regression-for-predictive-maintenance"></a>Regresja do konserwacji predykcyjnej
Modele regresji są używane do _obliczania pozostałego okresu użytkowania (pozostałego czasu eksploatacji) zasobu_. POZOSTAŁEGO czasu eksploatacji jest definiowana jako czas działania zasobu przed wystąpieniem następnego błędu. Każdy przykład szkolenia jest rekordem, który należy _do jednostki czasu_ dla elementu zawartości, gdzie _n_ to wielokrotność. Model powinien obliczyć pozostałego czasu eksploatacji każdego nowego przykładu jako _ciągłej liczby_. Ta liczba oznacza okres pozostały przed awarią.

#### <a name="label-construction-for-regression"></a>Konstrukcja etykiety do regresji
Tutaj znajdziesz: "jaki jest pozostały okres istnienia (pozostałego czasu eksploatacji) sprzętu"? Dla każdego rekordu przed błędem Oblicz etykietę jako liczbę jednostek czasu pozostałej przed następnym błędem. W tej metodzie etykiety są zmiennymi ciągłymi. (Zobacz rysunek 4)

![Rysunek 4. Etykietowanie na potrzeby regresji](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Rysunek 4. Etykietowanie na potrzeby regresji

W przypadku regresji etykietowanie jest wykonywane z odwołaniem do punktu awarii. Obliczenia nie są możliwe bez znajomości czasu, przez jaki zasób został przeżyły przed awarią. Dlatego w przeciwieństwie do klasyfikacji danych binarnych nie można używać zasobów bez żadnych błędów w przypadku modelowania. Ten problem jest najlepiej rozwiązany przez inną technikę statystyczną o nazwie [Analiza przeżycia](https://en.wikipedia.org/wiki/Survival_analysis). Jednak potencjalne komplikacje mogą wystąpić w przypadku zastosowania tej techniki do PdM przypadków użycia, które obejmują czasowo różne dane z częstymi interwałami. Aby uzyskać więcej informacji na temat analizy przeżycia, zobacz ten, który ma ten sam moduł [stronicowania](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasyfikacja wieloklasowa do konserwacji predykcyjnej
Wieloklasowe techniki klasyfikacji mogą być używane w rozwiązaniach PdM dla dwóch scenariuszy:
- Przewidywanie _dwóch przyszłych wyników_: pierwszy wynik jest _zakresem czasu na niepowodzenie_ dla elementu zawartości. Zasób jest przypisany do jednego z wielu możliwych okresów czasu. Drugim wynikiem jest prawdopodobieństwo wystąpienia błędu w przyszłym okresie z powodu _jednego z głównych przyczyn_. Takie przewidywanie umożliwia członkom personelu obsługi monitorowanie objawów i planowanie harmonogramów konserwacji.
- Przewidywanie _najbardziej najprawdopodobniej głównej przyczyny_ danego błędu. Ten wynik zaleca odpowiedni zestaw akcji konserwacji, aby naprawić błąd. Uporządkowana lista głównych przyczyn i zalecane naprawy mogą pomóc technikom w ustalaniu priorytetów ich akcji naprawy po awarii.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukcja etykiety dla klasyfikacji wieloklasowej
Poniżej znajduje się pytanie: "co to jest prawdopodobieństwo, że element zawartości zakończy się niepowodzeniem w następnych jednostkach _nZ_ czasu, gdzie _n_ to liczba okresów?". Aby odpowiedzieć na to pytanie, należy oznaczyć rekordy nZ przed awarią elementu zawartości przy użyciu zasobników czasu (3Z, 2Z, Z). Oznacz wszystkie inne rekordy jako "normalne" (etykieta = 0). W tej metodzie zmienna docelowa zawiera wartości _kategorii_ . (Zobacz rysunek 5).

![Rysunek 5. Etykiety prognoz czasu niepowodzeń dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Rysunek 5. Etykietowanie klasyfikacji wieloklasowej dla prognozowania czasu niepowodzenia

Poniżej znajduje się pytanie: "co to jest prawdopodobieństwo, że element zawartości zakończy się niepowodzeniem w ciągu następnych X jednostek czasu z powodu głównej przyczyny/problemu _P <sub></sub>_?". gdzie _jest_ liczbą możliwych przyczyn głównych. Aby odpowiedzieć na to pytanie, należy oznaczyć rekordy X przed awarią elementu zawartości jako "informacje o niepowodzeniach z powodu wystąpienia głównego: _p <sub></sub>_" (etykieta = _p <sub>i</sub>_). Oznacz wszystkie inne rekordy jako "normalne" (etykieta = 0). W tej metodzie etykiety są kategorii (zobacz rysunek 6).

![Rysunek 6. Główne przyczyny dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Rysunek 6. Etykietowanie klasyfikacji dla wieloklasowego na potrzeby prognozowania przyczyny głównej

Model przypisuje prawdopodobieństwo błędu z powodu każdego _P <sub>i</sub>_ , a także prawdopodobieństwo braku błędu. Te prawdopodobieństwa mogą być uporządkowane według wielkości, aby umożliwić prognozowanie problemów, które najprawdopodobniej wystąpią w przyszłości.

Tutaj znajdziesz: "jakie działania konserwacyjne zalecamy po awarii?". Aby odpowiedzieć na to pytanie, etykieta _nie wymaga, aby można było wybrać przyszły zakres_, ponieważ model nie przewiduje niepowodzenia w przyszłości. Po prostu przewidywalnuje najprawdopodobniej główną przyczynę, gdy wystąpił _błąd_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Szkolenia, sprawdzanie poprawności i metody testowania do konserwacji predykcyjnej
[Proces nauka danych zespołu](./overview.md) zapewnia pełen zakres cyklu uczenia w zakresie testów i testowania. W tej sekcji omówiono aspekty unikatowe dla PdM.

### <a name="cross-validation"></a>Krzyżowe sprawdzanie poprawności
Celem [krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) jest zdefiniowanie zestawu danych do "testowania" modelu w fazie uczenia. Ten zestaw danych jest nazywany _zestawem walidacji_. Ta technika pomaga ograniczyć problemy, takie jak _nadinstalowanie_ , i zapewnia wgląd w sposób, w jaki model będzie uogólniać do niezależnego zestawu danych. Oznacza to, że nieznany zestaw danych, który może być z rzeczywistego problemu. Procedura szkolenia i testowania dla PdM musi uwzględniać różne aspekty czasu, aby lepiej uogólniać niewidoczne dane w przyszłości.

Wiele algorytmów uczenia maszynowego zależy od wielu parametrów, które znacząco zmieniają wydajność modelu. Optymalne wartości tych parametrów nie są obliczane automatycznie podczas uczenia modelu. Powinny być określone przez analityka danych. Istnieje kilka sposobów znajdowania prawidłowych wartości parametrów.

Najbardziej powszechną funkcją jest _złożenie krzyżowego sprawdzania poprawności_ , które dzieli przykłady losowo na zgięcia w _k_ . Dla każdego zestawu wartości parametrów, należy uruchomić algorytm uczenia _k_ . W każdej iteracji Użyj przykładów w bieżącym zgięciu jako zestaw walidacji i pozostałej części przykładów jako zestawu szkoleniowego. Przeszkol algorytm za pośrednictwem przykładów szkoleniowych i Oblicz metryki wydajności przed przykładami walidacji. Na końcu tej pętli Oblicz średnią metryk wydajności _k_ . Dla każdego zestawu wartości parametrów wybierz te, które mają najlepszą średnią wydajność. Zadanie wybierania parametrów jest często eksperymentalne.

W przypadku problemów z PdM dane są rejestrowane w postaci szeregów czasowych zdarzeń, które pochodzą z kilku źródeł danych. Te rekordy mogą być uporządkowane według czasu etykietowania. W związku z tym, jeśli zestaw danych zostanie _losowo_ podzielony na szkolenie i zestaw walidacji, _niektóre przykłady szkoleniowe mogą być późniejsze w czasie niż niektóre przykłady weryfikacji_. Przyszła wydajność wartości parametrów będzie Szacowana na podstawie danych, które dotarły _przed_ przeszkoleniem modelu. Te oszacowania mogą być nadmiernie optymistyczne, zwłaszcza jeśli seria czasowa nie jest stacjonarna i zmienia się wraz z upływem czasu. W efekcie wybrane wartości parametrów mogą być nieoptymalne.

Zalecane jest, aby podzielić przykłady na szkolenia i weryfikacje w sposób _zależny od czasu_ , gdzie wszystkie przykłady sprawdzania poprawności są późniejsze niż wszystkie przykłady szkoleniowe. Dla każdego zestawu wartości parametrów uczenie algorytmu przez zestaw danych szkoleniowych. Zmierz wydajność modelu nad tym samym zestawem walidacji. Wybierz wartości parametrów, które pokazują najlepszą wydajność. Wartości parametrów wybieranych przez podzielenie pociągu/walidacji powodują zwiększenie wydajności modelu w przyszłości niż w przypadku wartości wybranych losowo przez wzajemne sprawdzanie poprawności.

Ostateczny model może być wygenerowany przez szkolenie algorytmu uczenia nad całym danymi szkoleniowymi przy użyciu najlepszych wartości parametrów.

### <a name="testing-for-model-performance"></a>Testowanie wydajności modelu
Po skompilowaniu modelu jest wymagane oszacowanie jego przyszłej wydajności w przypadku nowych danych. Dobrym oszacowaniem jest Metryka wydajności wartości parametrów obliczanych w zestawie walidacji lub średnia Metryka wydajności obliczona na podstawie weryfikacji krzyżowej. Te oszacowania są często nadmiernie optymistyczne. Firma może często mieć pewne dodatkowe wytyczne dotyczące testowania modelu.

Zalecany sposób PdM polega na podzieleniu przykładów na szkolenia, walidację i testy zestawów danych w sposób _zależny od czasu_ . Wszystkie przykłady testów powinny być późniejsze w czasie niż wszystkie przykłady szkoleń i weryfikacji. Po podpisaniu Wygeneruj model i zmierz jego wydajność zgodnie z wcześniejszym opisem.

Gdy szeregi czasowe są nieruchome i łatwe do przewidywania, zarówno metody losowe, jak i zależne od czasu generują podobne oszacowania w przyszłości. Jednak gdy szeregi czasowe nie są nieruchome i/lub trudno jest przewidzieć, zależne od czasu podejście generuje bardziej realistyczne szacunki wydajności w przyszłości.

### <a name="time-dependent-split"></a>Podział zależny od czasu
W tej sekcji opisano najlepsze rozwiązania dotyczące implementowania podziału zależnego od czasu. Poniżej opisano zależność dwukierunkowego podziału między szkolenia i zestawy testów.

Przyjmij strumień zdarzeń z sygnaturami czasowymi, takich jak pomiary z różnych czujników. Zdefiniuj funkcje i etykiety przykładów szkoleniowych i testowych w przypadku ramek czasowych, które zawierają wiele zdarzeń. Na przykład w przypadku klasyfikacji binarnej Utwórz funkcje oparte na przeszłych zdarzeniach, a następnie Utwórz etykiety na podstawie przyszłych zdarzeń w jednostkach "X" czasu w przyszłości (zobacz sekcję dotyczącą [inżynierów funkcji](#feature-engineering) i technik modelowania). W ten sposób przedział czasu etykietowania przykładu jest późniejszy niż przedział czasu jego funkcji.

W przypadku podziału zależnego od czasu należy wybrać _czas odcięcia szkolenia T <sub>c</sub>_ , przy którym ma być uczenie modelu, z parametrami dostrojonymi przy użyciu danych historycznych do T <sub>c</sub>. Aby zapobiec wyciekom przyszłych etykiet, które przekraczają T<sub>c</sub> do danych szkoleniowych, wybierz najnowszą godzinę, aby oznaczyć przykłady szkolenia jako X jednostek przed T<sub>c</sub>. W przykładzie pokazanym na rysunku 7 każdy kwadrat reprezentuje rekord w zestawie danych, gdzie funkcje i etykiety są obliczane zgodnie z powyższym opisem. Na rysunku przedstawiono rekordy, które powinny przejść do szkolenia i zestawy testów dla X = 2 i W = 3:

![Rysunek 7. Podział zależny od czasu dla klasyfikacji binarnej](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Rysunek 7. Podział zależny od czasu dla klasyfikacji binarnej

Zielone kwadraty reprezentują rekordy należące do jednostek czasu, których można użyć do szkolenia. Każdy przykład szkolenia jest generowany przez uwzględnienie ostatnich trzech okresów dla generacji funkcji oraz dwóch przyszłych okresów etykiet przed T<sub>c</sub>. Jeśli jakakolwiek część dwóch przyszłych okresów przekracza T<sub>c</sub>, należy wykluczyć ten przykład z zestawu danych szkoleniowych, ponieważ nie jest założono, że nie jest on widoczny poza t<sub>c</sub>.

Czarne kwadraty reprezentują rekordy końcowego zestawu danych z etykietą, które nie powinny być używane w zestawie danych szkoleniowych z uwzględnieniem powyższego ograniczenia. Te rekordy również nie będą używane w testowaniu danych, ponieważ znajdują się przed T<sub>c</sub>. Ponadto ich etykietki czasowe częściowo zależą od przedziału czasowego uczenia, co nie jest idealne. Dane szkoleniowe i testowe powinny mieć oddzielne ramki czasu etykietowania, aby zapobiec wyciekowi informacji o etykiecie.

Opisana tutaj technika umożliwia zachodzenie między przykładami szkoleń i testowania, które mają sygnatury czasowe blisko T<sub>c</sub>. Rozwiązanie do osiągnięcia większej separacji polega na wykluczeniu przykładów, które znajdują się w jednostkach w czasie T<sub>c</sub> z zestawu testowego. Jednak taki agresywny podział zależy od dostępności danych.

Modele regresji używane do przewidywania pozostałego czasu eksploatacji są bardziej poważnie narażone na problem wycieku. Użycie metody podziału losowego prowadzi do skrajnego dopasowywania. W przypadku problemów z regresją podział powinien być taki, aby rekordy należące do zasobów z błędami przed T<sub>c</sub> przechodzą do zestawu szkoleniowego. Rekordy zasobów, które mają Błędy po odjściu, przejdą do zestawu testów.

Innym najlepszym rozwiązaniem w przypadku dzielenia danych na potrzeby szkolenia i testowania jest użycie podziału według identyfikatora zasobu. Podział powinien być taki, że żaden z zasobów używanych w zestawie szkoleniowym nie jest używany w testowaniu wydajności modelu. Korzystając z tego podejścia, model ma lepszą szansę zapewniania bardziej realistycznych wyników przy użyciu nowych zasobów.

### <a name="handling-imbalanced-data"></a>Obsługa danych niezrównoważonych
W przypadku problemów z klasyfikacją, jeśli istnieje więcej przykładów jednej klasy niż pozostałe, zestaw danych jest uznawany za _niezrównoważony_. W idealnym przypadku wystarczającej liczby przedstawicieli każdej klasy w danych szkoleniowych jest umożliwienie rozróżnienia między różnymi klasami. Jeśli jedna z klas jest mniejsza niż 10% danych, dane są uznawane za niezrównoważone. Poddana Klasa jest nazywana _klasą mniejszości_.

Wiele PdMych problemów odzwierciedla takie niezrównoważone zestawy danych, w których jedna klasa jest poważnie niereprezentowana w porównaniu z inną klasą lub klasami. W niektórych sytuacjach Klasa mniejszości może stanowić tylko 0,001% całkowitej liczby punktów danych. Nierównoważność klasy nie jest unikatowa dla PdM. Inne domeny, w których awarie i anomalie są rzadkimi wystąpieniami, na przykład takie jak wykrywanie oszustw i wtargnięcie sieci. Te błędy składają się na przykłady klas mniejszości.

Ze względu na niezrównoważone dane wydajność większości standardowych algorytmów nauki jest zagrożona, ponieważ chcą zminimalizować ogólną częstotliwość błędów. W przypadku zestawu danych z 99% negatywnych i 1% pozytywnych przykładów model może być pokazywany w taki sposób, aby mieć 99% dokładności przez etykietowanie wszystkich wystąpień jako wartości ujemnych. Ale model będzie źle klasyfikować wszystkie pozytywne przykłady; Dlatego nawet jeśli jego dokładność jest wysoka, algorytm nie jest użyteczny. W związku z tym konwencjonalne metryki oceny, takie jak _ogólna dokładność dla współczynnika błędów_ , są niewystarczające do uczenia się niezrównoważonym. Po obliczeniu niezrównoważonych zestawów danych inne metryki są używane do oceny modelu:
- Dokładność
- Recall
- Wyniki F1
- Skorygowany koszt ROC (Charakterystyka działania odbiornika)

Aby uzyskać więcej informacji na temat tych metryk, zobacz [Obliczanie modelu](#model-evaluation).

Istnieją jednak pewne metody, które pomagają wyeliminować problem nierównowagi klas. Dwa główne są _technikami próbkowania_ i _uczeniem z uwzględnieniem kosztów_.

#### <a name="sampling-methods"></a>Metody próbkowania
Niezrównoważone uczenie obejmuje użycie metod próbkowania w celu zmodyfikowania zestawu danych szkoleniowych. Metody próbkowania nie mają być stosowane do zestawu testów. Mimo że istnieje kilka technik próbkowania, większość prostych _przepróbkuje losowo_ i _Pobiera próbkowanie_.

_Losowe próbkowanie_ obejmuje wybranie losowej próbki z klasy mniejszości, replikowanie tych przykładów i dodawanie ich do zestawu danych szkoleniowych. W związku z tym jest zwiększana liczba przykładów w klasie mniejszości i ostatecznie zrównoważy liczbę przykładów różnych klas. Wadą przekroczenia próbkowania jest to, że wiele wystąpień niektórych przykładów może spowodować, że klasyfikator stanie się zbyt szczegółowy, co prowadzi do nadmiernego dopasowania. Model może zawierać dużą dokładność szkolenia, ale jego wydajność na niewidocznych danych testowych może być nieoptymalna.

Na odwrót, _losowo w obszarze próbkowania_ wybiera losowo losową próbkę z klasy większości i usuwa te przykłady z zestawu danych szkoleniowych. Jednak usunięcie przykładów z klasy większości może spowodować, że Klasyfikator nie będzie mógł pominąć ważnych koncepcji dotyczących klasy większości. _Próbkowanie hybrydowe_ , w przypadku których Klasa mniejszości jest nadmiernie, a większość klasy jest w tym samym czasie, jest to kolejne rozwiązanie.

Istnieje wiele zaawansowanych technik próbkowania. Wybrana technika zależy od właściwości danych i wyników eksperymentów iteracyjnych przez analityka danych.

#### <a name="cost-sensitive-learning"></a>Uczenie z uwzględnieniem kosztów
W PdM awarie, które stanowią klasę mniejszości, są bardziej interesujące niż normalne przykłady. Więc fokus polega głównie na wydajności algorytmu w przypadku awarii. Nieprawidłowe przewidywalność klasy dodatniej jako klasy negatywnej może być droższa więcej niż na odwrót. Ta sytuacja jest często określana jako nierówne straty lub asymetryczne koszty nieprawidłowej klasyfikacji elementów do różnych klas. Idealny klasyfikator powinien zapewnić wysoką dokładność przewidywania w klasie mniejszości, bez naruszania dokładności dla większości klasy.

Istnieje wiele sposobów osiągnięcia tego salda. Aby wyeliminować problem nierównej utraty, należy przypisać wysoki koszt do nieograniczonej klasyfikacji klasy mniejszości i spróbować zminimalizować całkowity koszt. Algorytmy, takie jak _SVMs (obsługują maszyny wektorowe)_ , przyjmują tę metodę z założenia, przez umożliwienie ponoszenia kosztów pozytywnych i negatywnych w trakcie szkolenia. Podobnie, zwiększenie metod, takich jak _drzewa decyzyjne_ , zwykle pokazuje dobrą wydajność dzięki niezrównoważonym danym.

## <a name="model-evaluation"></a>Ocena modelu
Niewłaściwa Klasyfikacja to znaczący problem dotyczący scenariuszy PdM, w których koszt fałszywych alarmów dla firmy jest wysoki. Na przykład decyzja o rozłożeniu samolotu na podstawie nieprawidłowego przewidywania awarii aparatu może zakłócać harmonogramy i plany podróży. Przełączenie maszyny do trybu offline z linii montażowej może prowadzić do utraty przychodów. Więc Ocena modelu z prawidłowymi metrykami wydajności dla nowych danych testowych ma kluczowe znaczenie.

Poniżej przedstawiono typowe metryki wydajności służące do oceny modeli PdM:

- [Dokładność](https://en.wikipedia.org/wiki/Accuracy_and_precision) to najpopularniejsza Metryka używana do opisywania wydajności klasyfikatora. Jednak dokładność jest wrażliwa na dystrybucje danych i stanowi nieskuteczną miarę dla scenariuszy z niezrównoważonymi zestawami danych. Zamiast tego używane są inne metryki. Narzędzia, takie jak niepoprawna [macierz](https://en.wikipedia.org/wiki/Confusion_matrix) , służą do obliczenia i przyczyny dokładności modelu.
- [Precyzja](https://en.wikipedia.org/wiki/Precision_and_recall) modeli PDM odnosi się do liczby fałszywych alarmów. Niższa precyzja modelu ogólnie odnosi się do wyższej częstotliwości fałszywych alarmów.
- Szybkość [odwoływania](https://en.wikipedia.org/wiki/Precision_and_recall) oznacza, ile błędów w zestawie testów zostało prawidłowo zidentyfikowanych przez model. Wyższe stawki przywoływane oznaczają, że model zakończył się pomyślnie.
- Wartość [F1](https://en.wikipedia.org/wiki/F1_score) jest średnią harmoniczną precyzji i odwołania, z wartością przedziału od 0 (najgorszy) do 1 (najlepiej).

W przypadku klasyfikacji binarnej
- [Krzywe operacyjne odbiornika (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) to również popularna Metryka. W ROC Krzywe wydajności modelu są interpretowane na podstawie jednego ze stałych punktów operacyjnych w ROC.
- Jednak w przypadku problemów z PdMą _tabele Decile_ i _podnoszenia_ są bardziej szczegółowe. Skupiają się tylko na klasie pozytywnej (niepowodzeń) i zapewniają bardziej skomplikowany obraz wydajności algorytmu niż krzywe ROC.
  - _Tabele Decile_ są tworzone przy użyciu przykładów testowych w kolejności malejącej prawdopodobieństwa błędów. Uporządkowane próbki są następnie pogrupowane w deciles (10% próbek z największym prawdopodobieństwem, następnie 20%, 30% itd.). Współczynnik (prawdziwie dodatnia)/(Losowa linia bazowa) dla każdego decileu ułatwia oszacowanie wydajności algorytmu dla każdego decileu. Losowa linia bazowa przyjmuje wartości 0,1, 0,2 i tak dalej.
  - Na [wykresach przyrostowych](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) jest wykreślona wartość Decile true dodatnia, a na przykład Losowa dodatnia wartość true dla wszystkich deciles. Pierwszy deciles jest zazwyczaj fokusem wyników, ponieważ pokazują one największe zyski. Pierwszy deciles może być również traktowany jako reprezentatywny dla "zagrożonego", gdy jest używany do PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model operacjonalizacji do konserwacji predykcyjnej

Korzyść, którą realizuje nauka w nauce, jest realizowana tylko wtedy, gdy model przeszkolony zostanie uruchomiony. Oznacza to, że model musi być wdrożony w systemach firmy, aby prognozować na podstawie nowych, wcześniej niewidzianych danych.  Nowe dane muszą być dokładnie zgodne z _podpisem modelu_ przeszkolonego modelu na dwa sposoby:
- wszystkie funkcje muszą być obecne w każdym wystąpieniu logicznym (Wymów wiersz w tabeli) nowych danych.
- nowe dane muszą być wstępnie przetworzone i każda z funkcji wbudowanych w taki sam sposób jak dane szkoleniowe.

Powyższy proces jest określony na wiele sposobów w celach edukacyjnych i branżowych. Jednak wszystkie następujące instrukcje oznaczają tę samą wartość:
- _Ocena nowych danych_ przy użyciu modelu
- _Zastosuj model_ do nowych danych
- _Operacjonalizować_ model
- _Wdróż_ model
- _Uruchom model na_ podstawie nowych danych

Jak wspomniano wcześniej, model operacjonalizacji for PdM różni się od jego elementów równorzędnych. Scenariusze dotyczące wykrywania anomalii i wykrywania niepowodzeń zwykle implementują _ocenianie online_ (nazywane również _ocenianiem w czasie rzeczywistym_). W tym _miejscu model ocenia_ każdy rekord przychodzący i zwraca prognozę. W przypadku wykrywania anomalii prognozą jest wskazanie, że wystąpił anomalia (przykład: jedna Klasa SVM). W przypadku wykrywania awarii może to być typ lub Klasa błędu.

Z kolei PdM obejmuje _ocenianie wsadowe_. Aby zapewnić zgodność z podpisem modelu, funkcje w nowych danych muszą zostać zaprojektowane w taki sam sposób jak dane szkoleniowe. W przypadku dużych zestawów danych, które są typowe dla nowych, funkcje są agregowane w oknach czasu i są oceniane w usłudze Batch. Ocenianie wsadowe jest zazwyczaj wykonywane w systemach rozproszonych, takich jak [Spark](https://spark.apache.org/) lub [Azure Batch](../../batch/batch-service-workflow-features.md). Istnieje kilka alternatyw — obie podoptymalne:
- Aparaty danych przesyłające strumieniowo obsługują agregację w przypadku systemu Windows w pamięci. Można więc zatwierdzić, że obsługują ocenianie online. Jednak te systemy są odpowiednie do gęstych danych w wąskich oknach czasu lub elementów rozrzedzonych przez szersze okna. Mogą one nie skalować się prawidłowo w przypadku dużych okresów, które są widoczne w scenariuszach PdM.
- Jeśli ocenianie wsadowe nie jest dostępne, rozwiązanie to dostosowanie oceny online do obsługi nowych danych w małych partiach w danym momencie.

## <a name="solution-templates-for-predictive-maintenance"></a>Szablony rozwiązań do konserwacji predykcyjnej

Ostatnia sekcja tego przewodnika zawiera listę szablonów rozwiązań PdM, samouczków i eksperymentów wdrożonych na platformie Azure. Te aplikacje PdM można wdrożyć do subskrypcji platformy Azure w ciągu kilku minut. Mogą one służyć jako demonstracje dowodowe, piaskownice do eksperymentowania z alternatywami lub akceleratory dla rzeczywistych implementacji produkcyjnych. Szablony te znajdują się w [Azure AI Gallery](https://gallery.azure.ai) lub w witrynie [GitHub platformy Azure](https://github.com/Azure). Te różne przykłady zostaną przełączone do tego szablonu rozwiązania z upływem czasu.

| # | Tytuł | Opis |
|--:|:------|-------------|
| 2 | [Szablon rozwiązania do konserwacji predykcyjnej platformy Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Szablon rozwiązania typu "open source", który demonstruje modelowanie Azure ML i kompletną infrastrukturę platformy Azure, która może obsługiwać scenariusze konserwacji predykcyjnej w kontekście monitorowania zdalnego usługi IoT. |
| 3 | [Uczenie głębokie dla konserwacji predykcyjnej](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Notes platformy Azure z rozwiązaniem demonstracyjnym dotyczącym korzystania z sieci LSTM (Long Short-Term Memory) (klasy reneuronowychych sieci) do konserwacji predykcyjnej z [wpisem w blogu tego przykładu](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Konserwacja predykcyjna na platformie Azure dla platformy Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z pierwszych szablonów rozwiązań PdM w oparciu o konserwację platformy Azure w wersji 1.0. Ten przewodnik pochodzi z tego projektu. |
| 5 | [Zestaw narzędzi platformy Azure AI dla IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI w IoT Edge przy użyciu TensorFlow; Pakiet Toolkit udostępnia modele uczenia głębokiego w Azure IoT Edge zgodnych kontenerach platformy Docker i uwidacznia te modele jako interfejsy API REST.
| 6 | [Konserwacja predykcyjna usługi Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite komputerów — wstępnie skonfigurowane rozwiązanie. Szablon PdM konserwacji samolotów z IoT Suite. [Inny dokument](/previous-versions/azure/iot-accelerators/about-iot-accelerators) i [Przewodnik](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) dotyczący tego samego projektu. |
| 7 | [Szablon konserwacji predykcyjnej używający SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstracja pozostałego scenariusza użytkowania w oparciu o usługi R. |
| 8 | [Przewodnik modelowania konserwacji predykcyjnej](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkcja zestawu danych obsługi technicznej dla samolotów została zaprojektowana przy użyciu języka R z [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) i [zestawami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) danych oraz programem Azure Notes i [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) na platformie Azure|

## <a name="training-resources-for-predictive-maintenance"></a>Szkoleniowe zasoby na potrzeby konserwacji predykcyjnej

Microsoft Azure oferuje ścieżki szkoleniowe dotyczące podstaw podstawowych koncepcji związanych z technikami PdM, oprócz zawartości i szkoleń dotyczących ogólnych pojęć i rozwiązań AI.

| Zasób szkoleniowy  | Dostępność |
|:-------------------|--------------|
| [Microsoft Docs: rola Analityka danych](https://docs.microsoft.com/learn/roles/data-scientist) | Publiczne |
| [Microsoft Docs: rola inżyniera AI](https://docs.microsoft.com/learn/roles/ai-engineer) | Publiczne |
| [Microsoft Docs: rola inżyniera danych](https://docs.microsoft.com/learn/roles/data-engineer) | Publiczne |
| [Microsoft AI](https://www.microsoft.com/ai/ai-school) | Publiczne |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Publiczne |
| [Microsoft: listy odtwarzania w serwisie YouTube dla sztucznej analizy i analizy](https://www.youtube.com/c/MicrosoftAzure/playlists?view=50&sort=dd&shelf_id=7) | Publiczne |
| [Pokaż Microsoft AI](https://channel9.msdn.com/Shows/AI-Show) | Publiczne |
| [Przegląd platformy AI](https://azure.microsoft.com/overview/ai-platform/) | Publiczne |
| [Laboratorium AI](https://www.microsoft.com/ai/ai-lab) | Publiczne |
| [Microsoft AI](https://www.microsoft.com/AI) | Publiczne |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partnerzy |

Ponadto bezpłatny MOOC (duże ilości otwartych kursów online) w systemach AI są oferowane w trybie online przez instytucje akademickie, takie jak Stanforda i MIT oraz inne firmy edukacyjne.

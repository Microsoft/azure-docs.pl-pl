---
title: Przewodnik po sztucznej inteligencji platformy Azure dotyczący rozwiązań do konserwacji predykcyjnej — proces nauki o danych zespołowych
description: Kompleksowy opis analizy danych, która umożliwia predykcyjne rozwiązania konserwacyjne w wielu branżach pionowych.
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
ms.openlocfilehash: 301e2be0c8b971a0236de6a8b5c8bd5f278c3aee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686753"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Przewodnik dotyczący sztucznej inteligencji platformy Azure dotyczący rozwiązań do konserwacji predykcyjnej

## <a name="summary"></a>Podsumowanie

Konserwacja predykcyjna (**PdM**) to popularne zastosowanie analizy predykcyjnej, która może pomóc firmom w kilku branżach osiągnąć wysokie wykorzystanie aktywów i oszczędności w kosztach operacyjnych. Ten przewodnik zawiera wskazówki biznesowe i analityczne oraz najlepsze praktyki w celu pomyślnego opracowania i wdrożenia rozwiązań PdM przy użyciu technologii [platformy Sztucznej Inteligencji platformy Microsoft Azure.](https://azure.microsoft.com/overview/ai-platform)

Na początek w tym przewodniku przedstawiono scenariusze biznesowe specyficzne dla branży i proces kwalifikowania tych scenariuszy dla pdm. Dostępne są również wymagania dotyczące danych i techniki modelowania w celu tworzenia rozwiązań PdM. Główna zawartość przewodnika dotyczy procesu nauki o danych — w tym etapów przygotowania danych, inżynierii funkcji, tworzenia modelu i operacjonalizacji modelu. Aby uzupełnić te kluczowe pojęcia, w tym przewodniku wymieniono zestaw szablonów rozwiązań, które pomagają przyspieszyć tworzenie aplikacji PdM. Przewodnik wskazuje również przydatne zasoby szkoleniowe dla lekarza, aby dowiedzieć się więcej na temat ai za nauki o danych. 

### <a name="data-science-guide-overview-and-target-audience"></a>Omówienie przewodnika do nauki o danych i grupa docelowa
Pierwsza połowa tego przewodnika opisuje typowe problemy biznesowe, korzyści z wdrożenia pdm w celu rozwiązania tych problemów i wymienia niektóre typowe przypadki użycia. Decydenci biznesowi (BDM) skorzystają z tej zawartości. Druga połowa wyjaśnia analitykę danych za PdM i zawiera listę rozwiązań PdM zbudowanych na podstawie zasad opisanych w tym przewodniku. Zapewnia również ścieżki szkoleniowe i wskazówki do materiałów szkoleniowych. Decydenci techniczni (TDM) uznają tę treść za użyteczną.

| Zacznij od ... | Jeśli jesteś ... |
|:---------------|:---------------|
| [Uzasadnienie biznesowe do konserwacji predykcyjnej](#business-case-for-predictive-maintenance) |decydentów biznesowych (BDM), który chce zmniejszyć przestoje i koszty operacyjne oraz poprawić wykorzystanie sprzętu |
| [Analityka danych do konserwacji predykcyjnej](#data-science-for-predictive-maintenance) |decydent techniczny (TDM) oceniający technologie PdM w celu zrozumienia unikalnych wymagań dotyczących przetwarzania danych i AI w zakresie konserwacji predykcyjnej |
| [Szablony rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance)|architekt oprogramowania lub programista AI, który chce szybko wstać demo lub dowód koncepcji |
| [Zasoby szkoleniowe do konserwacji predykcyjnej](#training-resources-for-predictive-maintenance) | dowolne lub wszystkie powyższe i chcesz poznać podstawowe pojęcia dotyczące nauki o danych, narzędzi i technik.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza
Zawartość BDM nie oczekuje, że czytelnik ma jakąkolwiek wcześniejszą wiedzę na temat nauki o danych. Dla zawartości TDM, podstawowa znajomość statystyk i nauki o danych jest pomocne. Zalecana jest znajomość usług Azure Data i AI, Python, R, XML i JSON. Techniki AI są implementowane w pakietach Python i R. Szablony rozwiązań są implementowane przy użyciu usług platformy Azure, narzędzi programistycznych i zestawów SDK.

## <a name="business-case-for-predictive-maintenance"></a>Uzasadnienie biznesowe do konserwacji predykcyjnej

Firmy wymagają, aby krytyczny sprzęt działał z maksymalną wydajnością i wykorzystaniem, aby zrealizować zwrot z inwestycji kapitałowych. Zasoby te mogą wahać się od silników lotniczych, turbin, wind lub przemysłowych agregatów chłodniczych - które kosztują miliony - aż do codziennych urządzeń, takich jak kserokopiarki, ekspresy do kawy lub chłodnice wody.
- Domyślnie większość firm polega na _konserwacji naprawczej,_ gdzie części są wymieniane w miarę ich awarii. Konserwacja naprawcza zapewnia, że części są używane w całości (w związku z tym nie marnuje trwałości podzespołów), ale kosztuje firmę w czasie przestojów, robocizny i nieplanowanych wymagań konserwacyjnych (poza godzinami pracy lub niewygodnych lokalizacjach).
- Na następnym poziomie firmy praktykują _konserwację zapobiegawczą,_ w przypadku gdy określają żywotność części i utrzymują ją lub zastępują przed awarią. Konserwacja zapobiegawcza pozwala uniknąć nieplanowanych i katastrofalnych awarii. Ale wysokie koszty zaplanowanych przestojów, niepełne wykorzystanie komponentu w okresie użytkowania i robotek nadal pozostają.
- Celem _konserwacji predykcyjnej_ jest optymalizacja równowagi między konserwacją korekcyjną i zapobiegawczą poprzez umożliwienie wymiany komponentów _w samą porę._ Takie podejście zastępuje tylko te składniki, gdy są one blisko awarii. Wydłużając żywotność podzespołów (w porównaniu z konserwacją zapobiegawczą) i zmniejszając nieplanowane koszty konserwacji i robocizny (w stosunku do konserwacji naprawczej), firmy mogą uzyskać oszczędności kosztów i przewagę konkurencyjną.

## <a name="business-problems-in-pdm"></a>Problemy biznesowe w PdM
Firmy stoją w obliczu wysokiego ryzyka operacyjnego z powodu nieoczekiwanych awarii i mają ograniczony wgląd w główną przyczynę problemów w złożonych systemach. Oto niektóre z kluczowych pytań biznesowych:

- Wykrywanie anomalii w wydajności lub funkcjonalności sprzętu lub systemu.
- Przewiduj, czy zasób może zakończyć się niepowodzeniem w najbliższej przyszłości.
- Oszacuj pozostały okres użytkowania środka trwałego.
- Zidentyfikuj główne przyczyny awarii zasobu.
- Określ, jakie działania konserwacyjne należy wykonać, do kiedy, na zasób.

Typowe stwierdzenia dotyczące celów z PdM to:

- Zmniejsz ryzyko operacyjne związane z wyposażeniem o znaczeniu krytycznym.
- Zwiększ stopę zwrotu z zasobów, przewidując błędy przed ich wystąpieniem.
- Kontroluj koszty konserwacji, umożliwiając konserwację just-in-time.
- Niższe wyczerpanie klientów, poprawa wizerunku marki i utrata sprzedaży.
- Obniż koszty zapasów, zmniejszając poziom zapasów, przewidując punkt ponownego zamówienia.
- Odkryj wzorce związane z różnymi problemami z konserwacją.
- Podaj kluczowe wskaźniki wydajności (kluczowe wskaźniki wydajności), takie jak wyniki kondycji dla warunków zasobów.
- Oszacuj pozostały okres użytkowania zasobów.
- Zalecaj terminowe czynności konserwacyjne.
- Włącz zapasy w sam raz, szacując daty zamówień na wymianę części.

Te stwierdzenia celu są punktem wyjścia dla:

- _analityków danych_ do analizy i rozwiązywania konkretnych problemów predykcyjnych.
- _architektów i programistów chmury,_ aby zebrać kompleksowe rozwiązanie.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Problemy z kwalifikacjami do konserwacji predykcyjnej
Ważne jest, aby podkreślić, że nie wszystkie przypadki użycia lub problemy biznesowe mogą być skutecznie rozwiązane przez PdM. Istnieją trzy ważne kryteria kwalifikacyjne, które należy wziąć pod uwagę podczas wyboru problemu:

- Problem musi mieć charakter predykcyjny; oznacza to, że powinien istnieć cel lub wynik do przewidzenia. Problem powinien mieć również jasną ścieżkę działania, aby zapobiec awariom po ich wykryciu.
- Problem powinien mieć zapis historii operacyjnej sprzętu, który zawiera _zarówno dobre, jak i złe wyniki._ Zestaw działań podjętych w celu złagodzenia złych wyników powinny być również dostępne w ramach tych rekordów. Ważne są również raporty o błędach, dzienniki konserwacji dotyczące obniżania wydajności, naprawy i wymiany dzienników. Ponadto przydatne są również naprawy podejmowane w celu ich poprawy, a także zapisy zamienne.
- Zarejestrowana historia powinna zostać odzwierciedlona w _odpowiednich_ danych, które _są wystarczająco_ wysokiej jakości, aby wesprzeć przypadek użycia. Aby uzyskać więcej informacji na temat istotności i wystarczalności danych, zobacz [Wymagania dotyczące danych dotyczące konserwacji predykcyjnej](#data-requirements-for-predictive-maintenance).
- Wreszcie, firma powinna mieć ekspertów domeny, którzy mają jasne zrozumienie problemu. Powinni być świadomi wewnętrznych procesów i praktyk, aby móc pomóc analitykowi zrozumieć i zinterpretować dane. Powinny one również mieć możliwość wprowadzania niezbędnych zmian w istniejących procesach biznesowych, aby w razie potrzeby pomóc w zbierać odpowiednie dane dotyczące problemów.

## <a name="sample-pdm-use-cases"></a>Przykładowe przypadki użycia pdm
Ta sekcja koncentruje się na kolekcji przypadków użycia PdM z kilku branż, takich jak lotnictwo, narzędzia i transport. Każda sekcja zaczyna się od problemu biznesowego i omawia korzyści z PdM, odpowiednie dane dotyczące problemu biznesowego, a na koniec korzyści z rozwiązania PdM.

| Problem biznesowy | Korzyści z PdM |
|:-----------------|-------------------|
|**Lotnictwo**      |                   |
|Opóźnienie lotu i odwołania lotów z powodu problemów _mechanicznych._ Błędy, których nie można naprawić na czas, mogą spowodować anulowanie lotów i zakłócić planowanie i operacje. |Rozwiązania PdM mogą przewidzieć prawdopodobieństwo opóźnienia lub anulowania samolotu z powodu awarii mechanicznych.|
|_Awaria części silnika samolotu:_ Wymiana części silnika samolotu jest jednym z najczęstszych zadań konserwacyjnych w branży lotniczej. Rozwiązania konserwacyjne wymagają starannego zarządzania dostępnością zapasów komponentów, dostawą i planowaniem|Możliwość zebrania informacji na temat niezawodności komponentów prowadzi do znacznego obniżenia kosztów inwestycji.|
|**Finanse** |                         |
|_Awaria bankomatów_ jest częstym problemem w sektorze bankowym. Problem polega na zgłoszeniu prawdopodobieństwa, że transakcja wypłaty gotówki z bankomatu zostanie przerwana z powodu zacięcia papieru lub awarii części w dystrybutorze gotówki. Na podstawie prognoz awarii transakcji bankomaty mogą być obsługiwane proaktywnie, aby zapobiec występowaniu awarii.| Zamiast pozwolić na komputerze nie w połowie transakcji, pożądaną alternatywą jest zaprogramowanie komputera do odmowy usługi na podstawie prognozowania.|
|**Energetyczna** |                          |
|_Awarie turbin wiatrowych_: Turbiny wiatrowe są głównym źródłem energii w krajach/regionach odpowiedzialnych za środowisko i wiążą się z wysokimi kosztami kapitałowymi. Kluczowym elementem turbin wiatrowych jest silnik generatora, którego awaria sprawia, że turbina jest nieskuteczna. Jest to również bardzo drogie, aby naprawić.|Przewidywanie kluczowych wskaźników wydajności, takich jak MTTF (średni czas do awarii) może pomóc firmom energetycznym w zapobieganiu awariom turbin i zapewnić minimalne przestoje. Prawdopodobieństwo awarii będzie informować techników do monitorowania turbin, które mogą nie wkrótce, i harmonogram systemów konserwacji na podstawie czasu. Modele predykcyjne zapewniają wgląd w różne czynniki, które przyczyniają się do awarii, co pomaga technikom lepiej zrozumieć przyczyny problemów.|
|_Awarie wyłącznika:_ Dystrybucja energii elektrycznej do domów i firm wymaga, aby linie energetyczne były przez cały czas działać, aby zagwarantować dostawę energii. Wyłączniki pomagają ograniczyć lub uniknąć uszkodzenia linii energetycznych podczas przeciążania lub niekorzystnych warunków pogodowych. Problem biznesowy polega na przewidywaniu awarii wyłącznika.| Rozwiązania PdM pomagają zmniejszyć koszty napraw i wydłużyć żywotność urządzeń, takich jak wyłączniki. Pomagają one poprawić jakość sieci energetycznej, zmniejszając nieoczekiwane awarie i przerwy w świadczeniu usług.|
|**Transport i logistyka** |    |
|_Awarie drzwi windy_: Duże firmy windy zapewniają pełną obsługę stosu dla milionów funkcjonalnych wind na całym świecie. Bezpieczeństwo, niezawodność i dyspozycyjność windy to główne problemy dla ich klientów. Firmy te śledzą te i różne inne atrybuty za pomocą czujników, aby pomóc im w konserwacji naprawczej i zapobiegawczej. W windzie najbardziej widocznym problemem klienta jest nieprawidłowe działanie drzwi windy. Problem biznesowy w tym przypadku jest zapewnienie bazy wiedzy aplikacji predykcyjnej, która przewiduje potencjalne przyczyny awarii drzwi.| Windy są inwestycjami kapitałowymi dla potencjalnie 20-30 lat życia. Więc każda potencjalna sprzedaż może być bardzo konkurencyjna; stąd oczekiwania dotyczące usług i wsparcia są wysokie. Konserwacja predykcyjna może zapewnić tym firmom przewagę nad konkurentami w zakresie produktów i usług.|
|_Awarie kół_: Awarie kół stanowią połowę wszystkich wykolejeń pociągów i kosztują miliardy dla światowego przemysłu kolejowego. Awarie kół powodują również pogorszenie się szyn, co czasami powoduje przedwczesne pęknięcie szyny. Przerwy kolejowe prowadzą do katastrofalnych zdarzeń, takich jak wykolejenia. Aby uniknąć takich przypadków, koleje monitorują osiągi kół i zastępują je w sposób zapobiegawczy. Problemem biznesowym jest tutaj przewidywanie awarii kół.| Predykcyjna konserwacja kół pomoże w wymianie kół na czas |
|_Awarie drzwi pociągu metra_: Główną przyczyną opóźnień w działalności metra są awarie drzwi wagonów kolejowych. Problemem biznesowym jest tutaj przewidywanie awarii drzwi pociągu.|Wczesna świadomość awarii drzwi lub liczba dni do awarii drzwi pomoże firmie zoptymalizować harmonogramy obsługi drzwi pociągów.|

Następna sekcja dostaje się do szczegółów, jak zrealizować korzyści PdM omówione powyżej.

## <a name="data-science-for-predictive-maintenance"></a>Analityka danych do konserwacji predykcyjnej

Ta sekcja zawiera ogólne wytyczne dotyczące zasad nauki o danych i praktyki pdm. Ma na celu pomóc TDM, architekt rozwiązania lub deweloper zrozumieć wymagania wstępne i proces tworzenia kompleksowych aplikacji AI dla PdM. Możesz przeczytać tę sekcję wraz z recenzją wersji demonstracyjnych i szablonów weryfikacji koncepcji wymienionych w [szablonach rozwiązań do konserwacji predykcyjnej](#solution-templates-for-predictive-maintenance). Następnie można użyć tych zasad i najlepszych rozwiązań, aby zaimplementować rozwiązanie PdM na platformie Azure.

> [!NOTE]
> Ten przewodnik nie jest przeznaczony do nauczania czytelnika Data Science. Kilka przydatnych źródeł są przewidziane do dalszego czytania w sekcji dla [zasobów szkoleniowych dla konserwacji predykcyjnej](#training-resources-for-predictive-maintenance). Szablony [rozwiązań](#solution-templates-for-predictive-maintenance) wymienione w przewodniku pokazują niektóre z tych technik AI dla konkretnych problemów PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Wymagania dotyczące danych w zakresie konserwacji predykcyjnej

Powodzenie każdej nauki zależy od (a) jakości tego, czego się uczy, oraz (b) zdolności ucznia. Modele predykcyjne uczą się wzorców na podstawie danych historycznych i przewidują przyszłe wyniki z pewnym prawdopodobieństwem na podstawie tych obserwowanych wzorców. Dokładność predykcyjna modelu zależy od trafności, wystarczalności i jakości danych szkoleniowych i testowych. Nowe dane, które są "oceniane" przy użyciu tego modelu powinny mieć te same funkcje i schematu jako dane szkolenia/testu. Charakterystyka funkcji (typ, gęstość, rozkład i tak dalej) nowych danych powinny być zgodne z zestawami danych szkoleniowych i testowych. W tej sekcji skupiono się na takich wymaganiach dotyczących danych.

### <a name="relevant-data"></a>Odpowiednie dane

Po pierwsze, dane muszą być _istotne dla problemu_. Należy wziąć pod uwagę przypadek użycia _awarii koła_ omówione powyżej — dane szkoleniowe powinny zawierać funkcje związane z operacjami koła. Jeśli problem polegał na przewidywaniu awarii _układu trakcyjnego,_ dane szkoleniowe muszą obejmować wszystkie różne elementy układu trakcyjnego. Pierwszy przypadek dotyczy określonego składnika, podczas gdy drugi przypadek dotyczy awarii większego podsystemu. Ogólnym zaleceniem jest zaprojektowanie systemów prognozowania konkretnych komponentów, a nie większych podsystemów, ponieważ te ostatnie będą miały bardziej rozproszone dane. Ekspert domeny (zobacz [Problemy z kwalifikacjami do konserwacji predykcyjnej)](#qualifying-problems-for-predictive-maintenance)powinien pomóc w wyborze najbardziej odpowiednich podzbiorów danych do analizy. Odpowiednie źródła danych są szczegółowo omówione w [przygotowaniu danych do konserwacji predykcyjnej.](#data-preparation-for-predictive-maintenance)

### <a name="sufficient-data"></a>Wystarczające dane
Często zadawane są dwa pytania dotyczące danych historii awarii: (1) "Ile zdarzeń awarii jest wymaganych do przeszkolenia modelu?" (2) "Ile rekordów jest uważanych za "wystarczające"?" Nie ma ostatecznych odpowiedzi, ale tylko zasady kciuka. Dla (1), więcej liczby zdarzeń awarii, lepszy model. Dla (2), a dokładna liczba zdarzeń awarii zależy od danych i kontekstu problemu jest rozwiązany. Ale z drugiej strony, jeśli maszyna nie powiedzie się zbyt często, firma zastąpi go, co zmniejszy liczbę wystąpień awarii. Tutaj znowu ważne są wskazówki eksperta od domeny. Istnieją jednak metody radzenia sobie z problemem _rzadkich zdarzeń._ Zostały one omówione w sekcji [Obsługa danych niewzłoszonych](#handling-imbalanced-data).

### <a name="quality-data"></a>Dane dotyczące jakości
Jakość danych jest krytyczna — każda wartość atrybutu predyktora musi być _dokładna_ w połączeniu z wartością zmiennej docelowej. Jakość danych jest dobrze zbadanym obszarem w statystyce i zarządzaniu danymi, a zatem poza zakresem tego przewodnika.

> [!NOTE]
> Istnieje kilka zasobów i produktów dla przedsiębiorstw, aby dostarczać wysokiej jakości dane. Poniżej przedstawiono przykład odniesienia:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003.
> - [Analiza danych badawczych, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Ilościowe czyszczenie danych dla dużych baz danych](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Wprowadzenie do czyszczenia danych z R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Przygotowanie danych do konserwacji predykcyjnej

### <a name="data-sources"></a>Źródła danych

Odpowiednie źródła danych do konserwacji predykcyjnej obejmują między innymi:
- Historia niepowodzeń
- Historia konserwacji/napraw
- Warunki pracy maszyny
- Metadane sprzętu

#### <a name="failure-history"></a>Historia niepowodzeń
Zdarzenia awarii są rzadkie w aplikacjach PdM. Jednak podczas tworzenia modeli prognozowania algorytm musi dowiedzieć się o normalnym wzorze działania składnika, a także jego wzorce awarii. Tak więc dane szkoleniowe powinny zawierać wystarczającą liczbę przykładów z obu kategorii. Rekordy konserwacji i historia wymiany części są dobrym źródłem, aby znaleźć zdarzenia awarii. Za pomocą wiedzy o domenie anomalie w danych szkoleniowych można również zdefiniować jako błędy.

#### <a name="maintenancerepair-history"></a>Historia konserwacji/napraw
Historia konserwacji zasobu zawiera szczegółowe informacje na temat wymienianych komponentów, wykonywanych czynności naprawczych itp. Zdarzenia te rejestrują wzorce degradacji. Brak tych istotnych informacji w danych szkoleniowych może prowadzić do wprowadzających w błąd wyników modelu. Historia awarii można również znaleźć w historii konserwacji jako specjalne kody błędów lub daty zamówień dla części. Dodatkowe źródła danych, które wpływają na wzorce awarii powinny być badane i dostarczane przez ekspertów domeny.

#### <a name="machine-operating-conditions"></a>Warunki pracy maszyny
Ważnym źródłem danych przesyłanych strumieniowo na podstawie czujników (lub innych) danych przesyłanych strumieniowo jest dane przesyłane strumieniowo z pracy urządzenia. Kluczowym założeniem w PdM jest to, że stan kondycji maszyny pogarsza się w czasie podczas rutynowej pracy. Oczekuje się, że dane zawierają funkcje zmieniające czas, które przechwytują ten wzorzec przedawnienia, oraz wszelkie anomalie, które prowadzą do degradacji. Czasowy aspekt danych jest wymagany dla algorytmu, aby dowiedzieć się wzorców awarii i nie awarii w czasie. Na podstawie tych punktów danych algorytm uczy się przewidywać, ile więcej jednostek czasu maszyna może kontynuować pracę, zanim zakończy się niepowodzeniem.

#### <a name="static-feature-data"></a>Statyczne dane obiektowe
Funkcje statyczne są metadanymi sprzętu. Przykładami są produkcja sprzętu, model, data produkcji, data rozpoczęcia eksploatacji, lokalizacja systemu i inne specyfikacje techniczne.

Przykłady istotnych danych dla [przykładowych przypadków użycia PdM](#sample-pdm-use-cases) są przedstawione w tabeli poniżej:

| Przypadek użycia | Przykłady odpowiednich danych |
|:---------|---------------------------|
|_Opóźnienie lotu i odwołania lotu_ | Informacje o trasie lotu w postaci nóg lotu i dzienników stron. Dane dotyczące odcinka lotu obejmują szczegóły dotyczące planu podróży, takie jak data odlotu/przylotu, godzina, lotnisko, przerwy w podróży itp. Dziennik strony zawiera serię kodów błędów i konserwacji zarejestrowanych przez personel obsługi naziemnej.|
|_Awaria części silnika samolotu_ | Dane zebrane z czujników w samolocie, które dostarczają informacji na temat stanu różnych części. Rekordy konserwacji pomagają zidentyfikować, kiedy wystąpiły awarie komponentów i kiedy zostały zastąpione.|
|_Awaria bankomatu_ | Odczyty czujników dla każdej transakcji (deponowanie gotówki / czeku) i wydawanie gotówki. Informacje na temat pomiaru odstępów między notatkami, grubością nuty, odległością przybycia do noty, atrybutami kontrolnymi itp. Rekordy konserwacji, które zawierają kody błędów, informacje o naprawie, ostatni raz dozownik kasowy został ponownie napełniony.|
|_Awaria turbiny wiatrowej_ | Czujniki monitorują warunki turbiny, takie jak temperatura, kierunek wiatru, wytwarzana moc, prędkość generatora itp. Dane są zbierane z wielu turbin wiatrowych z farm wiatrowych zlokalizowanych w różnych regionach. Zazwyczaj każda turbina będzie miała wiele odczytów czujników przekazujących pomiary w ustalonym przedziale czasu.|
|_Awarie wyłącznika_ | Dzienniki konserwacji, które obejmują działania naprawcze, zapobiegawcze i systematyczne. Dane operacyjne, które obejmują automatyczne i ręczne polecenia wysyłane do wyłączników, takie jak akcje otwierania i zamykania. Metadane urządzenia, takie jak data produkcji, lokalizacja, model itp. Dane techniczne wyłącznika, takie jak poziomy napięcia, geolokalizacja, warunki otoczenia.|
|_Awarie drzwi windy_| Metadane windy, takie jak typ windy, data produkcji, częstotliwość konserwacji, typ budynku i tak dalej. Informacje operacyjne, takie jak liczba cykli drzwi, średni czas zamykania drzwi. Historia awarii z przyczyn.|
|_Awarie kół_ | Dane z czujnika, które mierzą przyspieszenie koła, przypadki hamowania, odległość jazdy, prędkość itp. Informacje statyczne na kołach, takich jak producent, data produkcji. Dane o niepowodzeniu wywnioskowane z bazy danych zamówień części, które śledzą daty i ilości zamówień.|
|_Awarie drzwi pociągu metra_ | Godziny otwarcia i zamknięcia drzwi, inne dane operacyjne, takie jak aktualny stan drzwi pociągu. Dane statyczne obejmują kolumny wartości identyfikatora zasobów, czasu i warunku.|

### <a name="data-types"></a>Typy danych
Biorąc pod uwagę powyższe źródła danych, dwa główne typy danych zaobserwowane w domenie PdM to:

- _Dane czasowe:_ Dane operacyjne, warunki pracy, typy zleceń pracy, kody priorytetów, które będą miały sygnatury czasowe w momencie nagrywania. Uszy, konserwacji / naprawy i historii użycia będzie również sygnatury czasowe skojarzone z każdym zdarzeniem.
- _Dane statyczne_: Funkcje maszyny i funkcje operatora są statyczne, ponieważ opisują specyfikacje techniczne maszyn lub atrybutów operatora. Jeśli te funkcje mogą się zmieniać w czasie, powinny one również mieć sygnatury czasowe skojarzone z nimi.

Zmienne predykcyjne i docelowe powinny być wstępnie przetworzone/przekształcone w [numeryczne, kategoryczne i inne typy danych w](https://www.statsdirect.com/help/basics/measurement_scales.htm) zależności od używanego algorytmu.

### <a name="data-preprocessing"></a>Wstępne przetwarzanie danych
Jako warunek wstępny _do funkcji inżynierii,_ przygotować dane z różnych strumieni do tworzenia schematu, z którego jest łatwy do utworzenia funkcji. Wizualizuj dane najpierw jako tabelę rekordów. Każdy wiersz w tabeli reprezentuje wystąpienie szkolenia, a kolumny reprezentują funkcje _predykcyjne_ (nazywane również niezależnymi atrybutami lub zmiennymi). Zorganizuj dane w taki sposób, aby ostatnia kolumna (zmienna zależna) była _docelowa_ (zmienna zależna). Dla każdego wystąpienia szkolenia przypisz _etykietę_ jako wartość tej kolumny.

W przypadku danych czasowych należy podzielić czas trwania danych z czujników na jednostki czasowe. Każdy rekord powinien należeć do jednostki czasu dla zasobu _i powinien oferować różne informacje_. Jednostki czasu są definiowane na podstawie potrzeb biznesowych w wielokrotności sekund, minut, godzin, dni, miesięcy i tak dalej. Jednostka czasu _nie musi być taka sama jak częstotliwość zbierania danych_. Jeśli częstotliwość jest wysoka, dane mogą nie wykazywać znaczącej różnicy między jednostkami. Załóżmy na przykład, że temperatura otoczenia była zbierana co 10 sekund. Przy użyciu tego samego interwału dla danych szkoleniowych tylko zawyża liczbę przykładów bez podawania żadnych dodatkowych informacji. W tym przypadku lepszą strategią byłoby użycie średniej danych w ciągu 10 minut lub godziny na podstawie uzasadnienia biznesowego.

W przypadku danych statycznych
- _Rekordy konserwacji:_ Nieprzetworzone dane konserwacji mają identyfikator zasobu i sygnaturę czasową z informacjami o działaniach konserwacyjnych, które zostały wykonane w danym momencie. Przekształć działania konserwacyjne w _kolumny kategoryczne,_ w których każdy deskryptor kategorii jednoznacznie mapuje do określonej akcji konserwacyjnej. Schemat rekordów konserwacji będzie zawierać identyfikator zasobu, czas i akcję konserwacji.

- _Rekordy awarii:_ Awarie lub przyczyny awarii mogą być rejestrowane jako określone kody błędów lub zdarzenia awarii zdefiniowane przez określone warunki biznesowe. W przypadkach, gdy urządzenie ma wiele kodów błędów, ekspert domeny powinien pomóc zidentyfikować te, które są istotne dla zmiennej docelowej. Użyj pozostałych kodów błędów lub warunków do konstruowania funkcji _predykcyjnych,_ które korelują z tymi błędami. Schemat rekordów awarii będzie zawierać identyfikator zasobu, czas, błąd lub przyczynę awarii - jeśli są dostępne.

- _Metadane maszyny i operatora:_ Scalanie danych maszyny i operatora w jeden schemat w celu skojarzenia zasobu z jego operatorem wraz z ich odpowiednimi atrybutami. Schemat dla warunków komputera obejmowałby identyfikator zasobu, funkcje zasobów, identyfikator operatora i funkcje operatora.

Inne kroki przetwarzania wstępnego danych obejmują _obsługę brakujących wartości_ i _normalizację_ wartości atrybutów. Szczegółowa dyskusja wykracza poza zakres tego przewodnika — zobacz następną sekcję, aby uzyskać przydatne odwołania.

Po powyższych wstępnie przetworzonych źródłach danych ostateczna transformacja przed inżynierią funkcji polega na dołączeniu do powyższych tabel na podstawie identyfikatora zasobu i sygnatury czasowej. Wynikowa tabela będzie miała wartości null dla kolumny awarii, gdy maszyna jest w normalnej pracy. Te wartości null mogą być przypisane przez wskaźnik normalnej pracy. Ta kolumna błędu służy do tworzenia _etykiet dla modelu predykcyjnego_. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [technik modelowania konserwacji predykcyjnej](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Inżynieria funkcji
Inżynieria funkcji jest pierwszym krokiem przed modelowania danych. Jego rola w procesie nauki o danych [jest opisana tutaj](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). _Funkcja_ jest atrybutem predykcyjnym dla modelu , takim jak temperatura, ciśnienie, wibracje i tak dalej. W przypadku pdm inżynieria funkcji polega na abstrakcji kondycji maszyny za dane historyczne zebrane w sporym czasie trwania. W tym sensie różni się od jego rysów, takich jak zdalne monitorowanie, wykrywanie anomalii i wykrywanie awarii. 

### <a name="time-windows"></a>Okna czasowe
Zdalne monitorowanie polega na zgłaszaniu zdarzeń, które zdarzają się w _czasie._ Modele wykrywania anomalii oceniają (wynik) przychodzące strumienie danych do flagi anomalie w punktach w czasie. Wykrywanie awarii klasyfikuje błędy do określonych typów, ponieważ występują one punkty w czasie. Z kolei PdM polega na przewidywaniu awarii w _przyszłym okresie czasu,_ na podstawie funkcji, które reprezentują zachowanie maszyny w _historycznym okresie czasu._ W przypadku pdm dane funkcji z poszczególnych punktów czasu są zbyt hałaśliwe, aby można było ich przewidzieć. Tak więc dane dla każdej funkcji muszą być _wygładzone_ przez agregowanie punktów danych w oknach czasu.

### <a name="lag-features"></a>Funkcje lagów
Wymagania biznesowe określają, jak daleko model musi przewidzieć w przyszłości. Z kolei ten czas trwania pomaga zdefiniować "jak daleko wstecz model musi wyglądać", aby te prognozy. Ten okres "patrząc wstecz" nazywa _się lagi,_ a funkcje zaprojektowane w tym okresie opóźnienia są nazywane _funkcjami opóźnienia._ W tej sekcji omówiono funkcje opóźnień, które mogą być konstruowane ze źródeł danych z sygnaturami czasowymi i tworzenie funkcji ze statycznych źródeł danych. Funkcje opóźnień mają zazwyczaj charakter _numeryczny._

> [!IMPORTANT]
> Rozmiar okna jest określany za pomocą eksperymentów i powinien zostać sfinalizowany za pomocą eksperta domeny. To samo zastrzeżenie dotyczy wyboru i definicji funkcji opóźnienia, ich agregacji i typu okien.

#### <a name="rolling-aggregates"></a>Agregaty kroczące
Dla każdego rekordu zasobu okno kroczące o rozmiarze "W" jest wybierane jako liczba jednostek czasu do obliczenia agregatów. Funkcje opóźnienia są następnie obliczane przy użyciu okresów W _przed datą_ tego rekordu. Na rysunku 1 niebieskie linie pokazują wartości czujników zarejestrowane dla zasobu dla każdej jednostki czasu. Oznaczają one średnią kroczącą wartości operacji w oknie o rozmiarze W=3. Średnia krocząca jest obliczany na wszystkich rekordach z sygnaturami czasowymi w zakresie t<sub>1</sub> (na pomarańczowo) do t<sub>2</sub> (na zielono). Wartość W jest zazwyczaj w minutach lub godzinach w zależności od charakteru danych. Ale w przypadku pewnych problemów, wybranie dużej W (powiedzmy 12 miesięcy) może zapewnić całą historię aktywów do czasu rekordu.

![Rysunek 1. Operacje agregacji toczenia](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Rysunek 1. Operacje agregacji toczenia

Przykładami agregatów kroczących w okresie czasu są miary liczby, średnia, CUMESUM (suma skumulowana), wartości min/max. Ponadto często stosuje się wariancję, odchylenie standardowe i liczbę odstających niż odchylenia standardowe N. Poniżej wymieniono przykłady agregatów, które mogą być stosowane w [przypadkach użycia](#sample-pdm-use-cases) w tym przewodniku. 
- _Opóźnienie lotu_: liczba kodów błędów w ciągu ostatniego dnia/tygodnia.
- _Awaria części silnika samolotu:_ środki toczenia, odchylenie standardowe i suma w ciągu ostatniego dnia, tygodnia itp. Ten wskaźnik należy ustalić wraz z ekspertem domeny biznesowej.
- _Awarie atm:_ środki toczenia, mediana, zakres, odchylenia standardowe, liczba wartości odstających powyżej trzech odchyleń standardowych, górne i dolne CUMESUM.
- _Awarie drzwi pociągu metra_: Liczba zdarzeń w ciągu ostatnich dni, tydzień, dwa tygodnie itp.
- _Awarie wyłącznika:_ Liczba awarii w ciągu ostatniego tygodnia, roku, trzech lat itp.

Inną użyteczną techniką w PdM jest przechwytywanie zmian trendu, skoków i zmian poziomów przy użyciu algorytmów wykrywanych anomalie w danych.

#### <a name="tumbling-aggregates"></a>Kruszywa tumbling
Dla każdego rekordu oznaczonego zasobem zdefiniowane jest okno o rozmiarze _W-<sub>k,</sub> _ gdzie _k_ jest liczbą okien o rozmiarze _W_. Agregaty są następnie tworzone przez _k_ _tumbling windows_ _W-k, W-<sub>(k-1)</sub>, ..., W-<sub>2</sub>, W-<sub>1</sub> _ dla okresów przed sygnaturą czasową rekordu. _k_ może być niewielka liczba do przechwytywania krótkoterminowych efektów, lub duża liczba do przechwytywania długoterminowych wzorców degradacji. (patrz rysunek 2).

![Rysunek 2. Zawirowania obiektów agregujących](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Rysunek 2. Zawirowania obiektów agregujących

Na przykład funkcje opóźnień dla przypadku użycia turbin wiatrowych mogą być tworzone za pomocą W=1 i k=3. Implikują one opóźnienie dla każdego z ostatnich trzech miesięcy przy użyciu górnych i dolnych odstaczy.

### <a name="static-features"></a>Funkcje statyczne

Specyfikacje techniczne urządzeń, takie jak data produkcji, numer modelu, lokalizacja, to tylko niektóre przykłady cech statycznych. Są one traktowane jako _zmienne kategoryczne_ do modelowania. Przykłady przypadku użycia wyłącznika to napięcie, prąd, pojemność mocy, typ transformatora i źródło zasilania. W przypadku awarii kół przykładem jest typ kół opon (stop vs stal).

Omawiane do tej pory wysiłki na rzecz przygotowania danych powinny prowadzić do zorganizowania danych, jak pokazano poniżej. Dane szkoleniowe, testowe i sprawdzania poprawności powinny mieć ten schemat logiczny (w tym przykładzie pokazano czas w jednostkach dni).

| Identyfikator zasobu | Time | \<Kolumny elementów> | Etykieta |
| ---- | ---- | --- | --- |
| A123 |Dzień 1. | . . . | . |
| A123 |Dzień 2. | . . . | . |
| ...  |...   | . . . | . |
| B234 ( B234 ) |Dzień 1. | . . . | . |
| B234 ( B234 ) |Dzień 2. | . . . | . |
| ...  |...   | . . . | . |

Ostatnim krokiem w inżynierii funkcji jest **etykietowanie** zmiennej docelowej. Ten proces jest zależny od techniki modelowania. Z kolei technika modelowania zależy od problemu biznesowego i charakteru dostępnych danych. Etykietowanie jest omówione w następnej sekcji.

> [!IMPORTANT]
> Przygotowanie danych i inżynieria funkcji są tak samo ważne jak techniki modelowania, aby dotrzeć do udanych rozwiązań PdM. Ekspert domeny i praktyk powinien poświęcić dużo czasu na dotarcie do odpowiednich funkcji i danych dla modelu. Poniżej wymieniono małą próbkę z wielu książek na temat inżynierii funkcji:
> - Pyle, D. Data Preparation for Data Mining (Seria Morgan Kaufmann w systemach zarządzania danymi), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (Editors), Feature Engineering for Machine Learning and Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Techniki modelowania do konserwacji predykcyjnej

W tej sekcji omówiono główne techniki modelowania problemów PdM, wraz z ich konkretnymi metodami budowy etykiet. Należy zauważyć, że jedna technika modelowania może być używana w różnych branżach. Technika modelowania jest powiązana z problemem nauki o danych, a nie z kontekstem danych.

> [!IMPORTANT]
> Wybór etykiet dla przypadków awarii i strategii etykietowania  
> należy ustalić w porozumieniu z ekspertem ds.

### <a name="binary-classification"></a>Klasyfikacja binarna
Klasyfikacja binarna służy do _przewidywania prawdopodobieństwa awarii sprzętu w przyszłym okresie -_ _zwanym przyszłym okresem horyzontu X_. X jest określany przez problem biznesowy i dane, które są pod ręką, w porozumieniu z ekspertem domeny. Oto przykłady:
- _minimalny czas realizacji_ wymagany do wymiany składników, wdrażania zasobów konserwacyjnych, wykonywania konserwacji, aby uniknąć problemu, który może wystąpić w tym okresie.
- _minimalna liczba zdarzeń,_ które mogą się zdarzyć, zanim wystąpi problem.

W tej technice zidentyfikowano dwa typy przykładów szkolenia. Pozytywny przykład, który wskazuje błąd , z _etykietą_= 1. Negatywny przykład, który wskazuje normalne operacje, z etykietą = 0. Zmienna docelowa, a tym samym wartości etykiet, są _kategoryczne_. Model powinien zidentyfikować każdy nowy przykład jako prawdopodobne niepowodzenie lub działać normalnie w ciągu następnych jednostek czasu X.

#### <a name="label-construction-for-binary-classification"></a>Konstrukcja etykiety do klasyfikacji binarnej
Pytanie brzmi: "Jakie jest prawdopodobieństwo, że zasób nie powiedzie się w następnych jednostkach X czasu?" Aby odpowiedzieć na to pytanie, etykieta X rejestruje przed awarią zasobu jako "o porażce" (etykieta = 1) i etykieta wszystkich innych rekordów jako "normalne" (etykieta = 0). (patrz rysunek 3).

![Rysunek 3. Etykietowanie dla klasyfikacji binarnej](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Rysunek 3. Etykietowanie dla klasyfikacji binarnej

Poniżej wymieniono przykłady strategii etykietowania w niektórych przypadkach użycia.
- _Opóźnienia lotów_: X można wybrać jako jeden dzień, aby przewidzieć opóźnienia w ciągu najbliższych 24 godzin. Następnie wszystkie loty, które są w ciągu 24 godzin przed awariami są oznaczone jako 1.
- _Awarie z bankomatów_: Celem może być określenie prawdopodobieństwa awarii transakcji w ciągu najbliższej godziny. W takim przypadku wszystkie transakcje, które miały miejsce w ciągu ostatniej godziny od awarii są oznaczone jako 1. Aby przewidzieć prawdopodobieństwo awarii w ciągu następnych n banknotów walutowych wydanych, wszystkie notatki wydane w ciągu ostatnich N notatek awarii są oznaczone jako 1.
- _Awarie wyłącznika:_ Celem może być przewidywanie następnej awarii polecenia wyłącznika. W takim przypadku X jest wybierany jako jedno przyszłe polecenie.
- _Awarie drzwi pociągu_: X można wybrać jako dwa dni.
- _Awarie turbin wiatrowych_: X można wybrać jako dwa miesiące.

### <a name="regression-for-predictive-maintenance"></a>Regresja do konserwacji predykcyjnej
Modele regresji są używane do _obliczania pozostałego okresu użytkowania (RUL) zasobu_. RUL jest zdefiniowany jako czas, przez który środek trwały działa przed wystąpieniem następnego błędu. Każdy przykład szkolenia jest rekordem, który należy do jednostki czasu _nY_ dla zasobu, gdzie _n_ jest wielokrotnością. Model powinien obliczyć RUL każdego nowego przykładu jako _liczbę ciągłą_. Liczba ta oznacza okres pozostały do niepowodzenia.

#### <a name="label-construction-for-regression"></a>Konstrukcja etykiety dla regresji
Pytanie brzmi: "Jaki jest pozostały okres użytkowania (RUL) sprzętu?" Dla każdego rekordu przed niepowodzeniem obliczyć etykietę jako liczbę jednostek czasu pozostałego przed następnym niepowodzeniem. W tej metodzie etykiety są zmiennymi ciągłymi. (Patrz rysunek 4)

![Rysunek 4. Etykietowanie dla regresji](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Rysunek 4. Etykietowanie dla regresji

W przypadku regresji etykietowanie odbywa się w odniesieniu do punktu awarii. Jego obliczenie nie jest możliwe bez wiedzy, jak długo składnik aktywów przetrwał przed awarią. Tak więc w przeciwieństwie do klasyfikacji binarnej zasoby bez żadnych błędów w danych nie mogą być używane do modelowania. Ten problem najlepiej rozwiązać za pomocą innej techniki statystycznej o nazwie [Analiza przetrwania](https://en.wikipedia.org/wiki/Survival_analysis). Ale potencjalne komplikacje mogą pojawić się podczas stosowania tej techniki do przypadków użycia PdM, które obejmują dane różne w czasie z częstymi przerwami. Aby uzyskać więcej informacji na temat analizy przetrwania, zobacz [ten jednostronicowy](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Wieloklasowa klasyfikacja do konserwacji predykcyjnej
Wieloklasowe techniki klasyfikacji mogą być stosowane w rozwiązaniach PdM dla dwóch scenariuszy:
- Przewiduj _dwa przyszłe wyniki:_ Pierwszy wynik to _zakres czasu do awarii_ zasobu. Zasób jest przypisany do jednego z wielu możliwych okresów czasu. Drugim rezultatem jest prawdopodobieństwo niepowodzenia w przyszłym okresie z powodu _jednej z wielu przyczyn._ Ta prognoza umożliwia ekipie obsługi technicznej obserwowanie symptomów i planowanie harmonogramów konserwacji.
- _Przewiduj najbardziej prawdopodobną główną przyczynę_ danego błędu. Ten wynik zaleca odpowiedni zestaw akcji konserwacji, aby naprawić błąd. Lista podstawowych przyczyn i zalecanych napraw w rankingu może pomóc technikom w naliczeniu priorytetów ich akcji naprawczych po awarii.

#### <a name="label-construction-for-multi-class-classification"></a>Konstrukcja etykiet dla klasyfikacji wieloklasowej
Pytanie brzmi: "Jakie jest prawdopodobieństwo, że składnik aktywów zakończy się niepowodzeniem w następnych jednostkach _nZ_ czasu, gdzie _n_ jest liczba okresów?" Aby odpowiedzieć na to pytanie, należy oznaczyć rekordy nZ przed awarią zasobu przy użyciu zasobu czasu (3Z, 2Z, Z). Oznacz wszystkie pozostałe rekordy jako "normalne" (etykieta = 0). W tej metodzie zmienna docelowa zawiera _wartości kategorii._ (Patrz rysunek 5).

![Rysunek 5. Etykiety przewidywania czasu awarii dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Rysunek 5. Etykietowanie wieloklasowej klasyfikacji prognozowania czasu awarii

Pytanie brzmi: "Jakie jest prawdopodobieństwo, że środek trwały nie powiedzie się w następnych jednostkach X czasu z powodu głównej przyczyny / problemu _P<sub>i?"</sub>_ gdzie _i_ jest liczba możliwych przyczyn. Aby odpowiedzieć na to pytanie, etykieta X rejestruje przed awarią zasobu jako "o awarii z powodu głównej przyczyny _P<sub>i</sub>_" (etykieta = _P<sub>i</sub>_). Oznacz wszystkie pozostałe rekordy jako "normalne" (etykieta = 0). W tej metodzie etykiety są również kategoryczne (patrz rysunek 6).

![Rysunek 6. Etykiety prognozowania przyczyn głównych dla klasyfikacji wieloklasowej](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Rysunek 6. Etykietowanie dla klasyfikacji wieloklasowej dla prognozowania przyczyn źródłowych

Model przypisuje prawdopodobieństwo awarii ze względu na każdy _P<sub>i,</sub> _ a także prawdopodobieństwo braku awarii. Prawdopodobieństwa te można uporządkować według wielkości, aby umożliwić przewidywanie problemów, które są najbardziej prawdopodobne w przyszłości.

Pytanie brzmi: "Jakie akcje konserwacyjne polecacie po awarii?" Aby odpowiedzieć na to pytanie, etykietowanie _nie wymaga wybrańczenia przyszłego horyzontu,_ ponieważ model nie przewiduje awarii w przyszłości. To jest po prostu przewidywanie najbardziej prawdopodobną _przyczyną, gdy awaria już się stało_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Metody szkolenia, walidacji i testowania konserwacji predykcyjnej
[Proces nauki o danych zespołu](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) zapewnia pełne pokrycie cyklu train-test-validate modelu. W tej sekcji omówiono aspekty unikatowe dla pdm.

### <a name="cross-validation"></a>Sprawdzanie poprawności krzyżowej
Celem [krzyżowego sprawdzania poprawności](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) jest zdefiniowanie zestawu danych do "testowania" modelu w fazie szkolenia. Ten zestaw danych jest nazywany _zestawem sprawdzania poprawności_. Ta technika pomaga ograniczyć problemy, takie jak _overfitting_ i daje wgląd w jaki model będzie uogólniać do niezależnego zestawu danych. Oznacza to, że nieznany zestaw danych, który może być z prawdziwego problemu. Procedura szkolenia i testowania pdm musi uwzględniać różne aspekty czasu, aby lepiej uogólnić na niewidocznych przyszłych danych.

Wiele algorytmów uczenia maszynowego zależy od wielu hiperparametrów, które mogą znacznie zmienić wydajność modelu. Optymalne wartości tych hiperparametrów nie są obliczane automatycznie podczas szkolenia modelu. Powinny one być określone przez analityka danych. Istnieje kilka sposobów na znalezienie dobrych wartości hiperparametrów.

Najczęstszym z nich jest _k-fold cross-walidacji,_ która dzieli przykłady losowo na _k_ fałdy. Dla każdego zestawu wartości hiperparametrów uruchom algorytm uczenia _się k_ razy. W każdej iteracji użyj przykładów w bieżącym krotnie jako zestaw sprawdzania poprawności, a pozostałe przykłady jako zestaw szkoleniowy. Trenuj algorytm na przykłady szkolenia i obliczyć metryki wydajności za pomocą przykładów sprawdzania poprawności. Na końcu tej pętli obliczyć średnią metryki wydajności _k._ Dla każdego zestawu wartości hiperparametru wybierz te, które mają najlepszą średnią wydajność. Zadanie wyboru hiperparametrów jest często eksperymentalne w przyrodzie.

W pdm problemy dane są rejestrowane jako szereg czasu zdarzeń, które pochodzą z kilku źródeł danych. Zapisy te można zamówić zgodnie z czasem etykietowania. W związku z tym jeśli zestaw danych jest _podzielony losowo_ na zestaw szkolenia i sprawdzania poprawności, _niektóre przykłady szkolenia mogą być później w czasie niż niektóre przykłady sprawdzania poprawności_. Przyszła wydajność wartości hiperparametru zostanie oszacowana na podstawie niektórych danych, które dotarły _przed_ przeszkoleniem modelu. Szacunki te mogą być zbyt optymistyczne, zwłaszcza jeśli szeregi czasowe nie są stacjonarne i ewoluują w czasie. W rezultacie wybrane wartości hiperparametryczne mogą być nieoptymalne.

Zalecanym sposobem jest podzielenie przykładów na szkolenia i sprawdzanie poprawności ustawione w sposób _zależny od czasu,_ gdzie wszystkie przykłady sprawdzania poprawności są później w czasie niż wszystkie przykłady szkolenia. Dla każdego zestawu wartości hiperparametru należy trenować algorytm za pomocą zestawu danych szkoleniowych. Zmierz wydajność modelu za tym samym zestawie sprawdzania poprawności. Wybierz wartości hiperparametryczne, które pokazują najlepszą wydajność. Wartości hiperparametryczne wybrane przez pociąg/sprawdzanie poprawności podziału wynik lepszej wydajności przyszłego modelu niż w przypadku wartości wybranych losowo przez krzyżowe sprawdzanie poprawności.

Ostateczny model można wygenerować przez szkolenie algorytm uczenia się na całych danych szkoleniowych przy użyciu najlepszych wartości hiperparametru.

### <a name="testing-for-model-performance"></a>Testowanie wydajności modelu
Po zbudowaniu modelu wymagane jest oszacowanie jego przyszłej wydajności na nowych danych. Dobrym oszacowaniem jest metryka wydajności wartości hiperparametru obliczonych w zestawie sprawdzania poprawności lub metryka średniej wydajności obliczona na podstawie weryfikacji krzyżowej. Szacunki te są często zbyt optymistyczne. Firma może często mieć dodatkowe wytyczne dotyczące sposobu, w jaki chcieliby przetestować model.

Zalecanym sposobem pdm jest podzielenie przykładów na zestawy danych szkoleniowych, sprawdzania poprawności i testów w sposób _zależny od czasu._ Wszystkie przykłady testów powinny być później w czasie niż wszystkie przykłady szkolenia i sprawdzania poprawności. Po podziale wygeneruj model i zmierz jego wydajność zgodnie z wcześniejszym opisem.

Gdy serie czasowe są nieruchome i łatwe do przewidzenia, zarówno losowe, jak i zależne od czasu podejścia generują podobne szacunki przyszłej wydajności. Ale gdy szeregi czasowe nie są nieruchome i/lub trudne do przewidzenia, zależne od czasu podejście wygeneruje bardziej realistyczne szacunki przyszłej wydajności.

### <a name="time-dependent-split"></a>Podział zależny od czasu
W tej sekcji opisano najlepsze rozwiązania do zaimplementowania podziału zależnego od czasu. Zależny od czasu dwukierunkowy podział między zestawami treningowymi i testowych jest opisany poniżej.

Załóżmy, że strumień zdarzeń sygnatury czasowej, takich jak pomiary z różnych czujników. Zdefiniuj funkcje i etykiety przykładów treningu i testów w ramach czasowych, które zawierają wiele zdarzeń. Na przykład dla klasyfikacji binarnej, tworzenie funkcji na podstawie przeszłych zdarzeń i tworzenie etykiet na podstawie przyszłych zdarzeń w ramach "X" jednostek czasu w przyszłości (zobacz sekcje dotyczące [inżynierii funkcji](#feature-engineering) i modelowania technik). W związku z tym ramy czasowe etykietowania przykładu jest później niż ramy czasowe jego funkcji.

W przypadku podziału zależnego od czasu wybierz _czas odcięcia szkolenia T<sub>c,</sub> _ w którym można trenować model, z hiperparametry dostrojonymi przy użyciu danych historycznych do T<sub>c</sub>. Aby zapobiec wyciekowi przyszłych etykiet, które są poza T<sub>c</sub> do danych szkoleniowych, wybierz ostatni czas, aby oznaczyć przykłady szkolenia jako jednostki X przed T<sub>c</sub>. W przykładzie pokazanym na rysunku 7 każdy kwadrat reprezentuje rekord w zestawie danych, w którym operacje i etykiety są obliczane w sposób opisany powyżej. Rysunek pokazuje rekordy, które powinny przejść do zestawów szkoleniowych i testowych dla X =2 i W =3:

![Rysunek 7. Podział zależny od czasu dla klasyfikacji binarnej](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Rysunek 7. Podział zależny od czasu dla klasyfikacji binarnej

Zielone kwadraty reprezentują rekordy należące do jednostek czasu, które mogą być używane do szkolenia. Każdy przykład szkolenia jest generowany przez uwzględnienie ostatnich trzech okresów dla generowania funkcji i dwóch przyszłych okresów etykietowania przed T<sub>c</sub>. Gdy jakakolwiek część dwóch przyszłych okresów jest poza T<sub>c,</sub>wyklucz ten przykład z zestawu danych szkoleniowych, ponieważ nie przyjmuje się widoczności poza T<sub>c</sub>.

Czarne kwadraty reprezentują rekordy końcowego zestawu danych oznaczonych etykietą, które nie powinny być używane w zestawie danych szkoleniowych, biorąc pod uwagę powyższe ograniczenie. Rekordy te nie będą również wykorzystywane w testach danych, ponieważ są one przed T<sub>c</sub>. Ponadto ich ramy czasowe etykietowania częściowo zależą od ram czasu szkolenia, który nie jest idealny. Dane szkoleniowe i testowe powinny mieć oddzielne ramy czasowe etykietowania, aby zapobiec wyciekowi informacji o etykietach.

Technika omówiona do tej pory pozwala na nakładanie się między szkolenia i testowania przykładów, które mają sygnatury czasowe w pobliżu T<sub>c</sub>. Rozwiązaniem w celu osiągnięcia większej separacji jest wykluczenie przykładów, które znajdują się w jednostkach czasu W T<sub>c</sub> z zestawu testowego. Ale taki agresywny podział zależy od dużej dostępności danych.

Problemy z wyciekami mają bardziej dotkliwie dotyczyć modeli regresji używanych do przewidywania RUL. Zastosowanie metody losowego podziału prowadzi do skrajnego przekładniania. W przypadku problemów z regresją podział powinien być taki, aby rekordy należące do zasobów z awariami przed T<sub>c</sub> przejść do zestawu szkoleniowego. Rekordy zasobów, które mają błędy po odcięciu przejść do zestawu testów.

Inną najlepszą praktyką dzielenia danych do szkolenia i testowania jest użycie podziału według identyfikatora zasobu. Podział powinien być taki, aby żaden z zasobów używanych w zestawie szkoleniowym nie był używany do testowania wydajności modelu. Przy użyciu tego podejścia model ma większe szanse na zapewnienie bardziej realistycznych wyników z nowych zasobów.

### <a name="handling-imbalanced-data"></a>Obsługa danych niezłych, niezbyła się
W przypadku problemów z klasyfikacją, jeśli istnieje więcej przykładów jednej klasy niż innych, zestaw danych jest określany jako _niezrównoważony._ W idealnym przypadku preferowane jest wystarczająca liczba przedstawicieli każdej klasy w danych szkoleniowych, aby umożliwić rozróżnienie między różnymi klasami. Jeśli jedna klasa jest mniejsza niż 10% danych, dane są uważane za niezrównoważone. Niedostatecznie reprezentowana klasa jest nazywana _klasą mniejszościową._

Wiele problemów PdM napotyka takie niezrównoważone zestawy danych, gdzie jedna klasa jest poważnie niedostatecznie reprezentowana w porównaniu do innej klasy lub klas. W niektórych sytuacjach klasa mniejszości może stanowić tylko 0,001% wszystkich punktów danych. Nierównowaga klas nie jest unikatowa dla pdm. Inne domeny, w których awarie i anomalie są rzadkimi zdarzeniami, napotykają podobny problem, na przykład wykrywanie oszustw i włamania do sieci. Te błędy składają się na przykłady klas mniejszości.

W przypadku braku równowagi klas w danych wydajność większości standardowych algorytmów uczenia się jest zagrożona, ponieważ mają one na celu zminimalizowanie ogólnego poziomu błędu. W przypadku zestawu danych z 99% ujemnymi i 1% dodatnimi przykładami model może być pokazany, że ma dokładność 99%, oznaczając wszystkie wystąpienia jako negatywne. Ale model będzie błędnie klasyfikować wszystkie pozytywne przykłady; więc nawet jeśli jego dokładność jest wysoka, algorytm nie jest przydatny. W związku z tym konwencjonalne wskaźniki oceny, takie jak _ogólna dokładność poziomu błędu,_ są niewystarczające dla nierównego uczenia się. W obliczu niezrównoważonych zestawów danych inne metryki są używane do oceny modelu:
- Dokładność
- Kompletność
- Wyniki F1
- Roc skorygowany o koszty (charakterystyka eksploatacyjna odbiornika)

Aby uzyskać więcej informacji na temat tych wskaźników, zobacz [ocena modelu](#model-evaluation).

Istnieją jednak pewne metody, które pomagają rozwiązać problem braku równowagi klasowej. Dwa główne z nich to _techniki pobierania próbek_ i _uczenie się wrażliwe na koszty._

#### <a name="sampling-methods"></a>Metody pobierania próbek
Niezrównoważone uczenie się polega na wykorzystaniu metod próbkowania w celu zmodyfikowania zestawu danych szkoleniowych do zrównoważonego zestawu danych. Metody pobierania próbek nie mają być stosowane do zestawu testowego. Chociaż istnieje kilka technik pobierania próbek, większość prostych do przodu to _losowe przepróbkowanie_ i _pobieranie próbek._

_Losowe przepróbkowanie_ polega na wybraniu losowej próbki z klasy mniejszości, replikowaniu tych przykładów i dodawaniu ich do zestawu danych szkoleniowych. W związku z tym zwiększa się liczba przykładów w klasie mniejszości i ostatecznie zrównoważyć liczbę przykładów różnych klas. Wadą przesadowania jest to, że wiele wystąpień niektórych przykładów może spowodować, że klasyfikator stanie się zbyt specyficzny, co prowadzi do nadmiernego dopasowania. Model może wykazywać wysoką dokładność treningu, ale jego wydajność na niewidocznych danych testowych może być nieoptymalne.

Z drugiej strony _losowe w próbkowaniu_ jest wybranie losowej próbki z klasy większościowej i usunięcie tych przykładów z zestawu danych szkoleniowych. Jednak usunięcie przykładów z klasy większości może spowodować, że klasyfikator przegapić ważne pojęcia odnoszące się do klasy większości. _Próbkowanie hybrydowe,_ w którym klasa mniejszości jest nadmiernie próbkowanie i klasy większości jest niedostateczona próbkowania w tym samym czasie jest innym realnym podejściem.

Istnieje wiele zaawansowanych technik pobierania próbek. Wybrana technika zależy od właściwości danych i wyników eksperymentów iteracyjnych przeprowadzonych przez analityka danych.

#### <a name="cost-sensitive-learning"></a>Kształcenie wrażliwe na koszty
W PdM, niepowodzenia, które stanowią klasę mniejszości są bardziej interesujące niż normalne przykłady. Tak więc koncentruje się głównie na wydajności algorytmu na awariach. Niepoprawnie przewidywanie klasy dodatniej jako klasy ujemnej może kosztować więcej niż odwrotnie. Ta sytuacja jest powszechnie określana jako nierówna strata lub asymetryczny koszt błędnej klasyfikacji elementów do różnych klas. Idealny klasyfikator powinien zapewniać wysoką dokładność przewidywania w klasie mniejszości, bez uszczerbku dla dokładności dla klasy większości.

Istnieje wiele sposobów osiągnięcia tej równowagi. Aby złagodzić problem nierównej straty, należy przypisać wysoki koszt do błędnej klasyfikacji klasy mniejszości i spróbuj zminimalizować całkowity koszt. Algorytmy takie jak _SVMs (Support Vector Machines)_ przyjmują tę metodę z natury, umożliwiając określanie kosztów przykładów dodatnich i negatywnych podczas szkolenia. Podobnie, zwiększenie metod, takich jak _wzmocnione drzewa decyzyjne_ zwykle wykazują dobrą wydajność z nierówne dane.

## <a name="model-evaluation"></a>Ocena modelu
Błędna klasyfikacja jest istotnym problemem dla scenariuszy PdM, w których koszt fałszywych alarmów dla firmy jest wysoki. Na przykład decyzja o uziemienie samolotu na podstawie błędnego przewidywania awarii silnika może zakłócić harmonogramy i plany podróży. Przesunienie maszyny w tryb offline z linii montażowej może prowadzić do utraty przychodów. Dlatego ocena modelu z odpowiednimi metrykami wydajności względem nowych danych testowych ma kluczowe znaczenie.

Poniżej omówiono typowe metryki wydajności używane do oceny modeli PdM:

- [Dokładność](https://en.wikipedia.org/wiki/Accuracy_and_precision) jest najbardziej popularną metryką używaną do opisywania wydajności klasyfikatora. Ale dokładność jest wrażliwa na dystrybucje danych i jest nieskuteczną miarą dla scenariuszy z niezrównoważonych zestawów danych. Zamiast tego są używane inne metryki. Narzędzia, takie jak [macierz pomyłek,](https://en.wikipedia.org/wiki/Confusion_matrix) są używane do obliczania i rozumowania dokładności modelu.
- [Precyzja](https://en.wikipedia.org/wiki/Precision_and_recall) modeli PdM odnosi się do szybkości fałszywych alarmów. Niższa precyzja modelu zazwyczaj odpowiada wyższej szybkości fałszywych alarmów.
- Współczynnik [odwołania](https://en.wikipedia.org/wiki/Precision_and_recall) oznacza, ile błędów w zestawie testów zostało poprawnie zidentyfikowanych przez model. Wyższe wskaźniki wycofywania oznaczają, że model jest skuteczny w identyfikowaniu prawdziwych błędów.
- [Wynik F1](https://en.wikipedia.org/wiki/F1_score) jest harmoniczną średnią precyzji i przypomnieć, z jego wartość od 0 (najgorszy) do 1 (najlepszy).

W przypadku klasyfikacji binarnej
- Popularne są również [krzywe robocze odbiornika (ROC).](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) W przypadku krzywych ROC wydajność modelu jest interpretowana na podstawie jednego stałego punktu roboczego na ROC.
- Ale dla problemów PdM, _decile tabele_ i _wykresy windy_ są bardziej pouczające. Koncentrują się one tylko na dodatniej klasy (błędy) i zapewniają bardziej złożony obraz wydajności algorytmu niż krzywe ROC.
  - _Tabele decylowe_ są tworzone przy użyciu przykładów testów w kolejności malejącej prawdopodobieństwa niepowodzenia. Uporządkowane próbki są następnie pogrupowane w decyle (10% próbek z najwyższym prawdopodobieństwem, następnie 20%, 30% i tak dalej). Stosunek (wskaźnik rzeczywisty dodatni)/(losowa linia bazowa) dla każdego decyla pomaga oszacować wydajność algorytmu przy każdym decylu. Losowa linia bazowa przyjmuje wartości 0,1, 0,2 itd.
  - [Wykresy podnoszenia wykreślają](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) rzeczywisty wskaźnik rzeczywisty dodatni w porównaniu z losową rzeczywistą dodatnią stopą dla wszystkich decyli. Pierwsze decyle są zwykle w centrum uwagi wyników, ponieważ wykazują one największe zyski. Pierwsze decyle mogą być również postrzegane jako reprezentatywne dla "zagrożonych", gdy są stosowane w PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacjonalizacja modelu w celu konserwacji predykcyjnej

Korzyści, które wykonuje nauka o danych, są realizowane tylko wtedy, gdy przeszkolony model jest operacyjny. Oznacza to, że model musi zostać wdrożony w systemach biznesowych, aby prognozy oparte na nowych, wcześniej niewidocznych danych.  Nowe dane muszą być dokładnie zgodne z _podpisem modelu_ przeszkolonego modelu na dwa sposoby:
- wszystkie funkcje muszą być obecne w każdym logicznym wystąpieniu (powiedzmy wiersz w tabeli) nowych danych.
- nowe dane muszą być wstępnie przetworzone, a każda z elementów zaprojektowanych, dokładnie w taki sam sposób jak dane szkoleniowe.

Powyższy proces jest pod wieloma względami w literaturze akademickiej i branżowej. Ale wszystkie poniższe stwierdzenia oznaczają to samo:
- _Zdobywaj nowe dane_ za pomocą modelu
- _Stosowanie modelu_ do nowych danych
- _Operacjonalizacja_ modelu
- _Wdrażanie_ modelu
- _Uruchamianie modelu względem_ nowych danych

Jak wspomniano wcześniej, operacjonalizacja modelu dla PdM różni się od jego odpowiedników. Scenariusze obejmujące wykrywanie anomalii i wykrywanie awarii zazwyczaj implementują _punktację online_ (nazywaną również _punktacją w czasie rzeczywistym)._ W tym miejscu model _ocenia_ każdy rekord przychodzący i zwraca przewidywanie. W przypadku wykrywania anomalii przewidywanie jest wskazaniem, że wystąpiła anomalia (przykład: Jednoklasowa SVM). W przypadku wykrywania awarii będzie to typ lub klasa awarii.

Natomiast PdM obejmuje _punktacji partii_. Aby zapewnić zgodność z podpisem modelu, funkcje w nowych danych muszą być zaprojektowane w taki sam sposób, jak dane szkoleniowe. Dla dużych zestawów danych, które są typowe dla nowych danych, funkcje są agregowane w oknach czasu i oceniane w partii. Ocenianie wsadowe jest zazwyczaj wykonywane w systemach rozproszonych, takich jak [Spark](https://spark.apache.org/) lub [Azure Batch.](https://docs.microsoft.com/azure/batch/batch-api-basics) Istnieje kilka alternatyw - zarówno nieoptymalne:
- Aparaty danych przesyłania strumieniowego obsługują agregację w oknach w pamięci. Można więc argumentować, że wspierają one punktację online. Ale te systemy nadają się do gęstych danych w wąskich oknach czasu lub rzadkich elementów w szerszych oknach. Mogą nie skalować się dobrze dla gęstych danych w szerszych oknach czasowych, jak widać w scenariuszach PdM.
- Jeśli ocenianie partii nie jest dostępne, rozwiązaniem jest dostosowanie oceniania online do obsługi nowych danych w małych partiach naraz.

## <a name="solution-templates-for-predictive-maintenance"></a>Szablony rozwiązań do konserwacji predykcyjnej

Ostatnia sekcja tego przewodnika zawiera listę szablonów rozwiązań PdM, samouczków i eksperymentów zaimplementowanych na platformie Azure. Te aplikacje PdM można wdrożyć w ramach subskrypcji platformy Azure w ciągu kilku minut w niektórych przypadkach. Mogą być używane jako demonstracje weryfikacji koncepcji, piaskownice do eksperymentowania z alternatywami lub akceleratory dla rzeczywistych implementacji produkcyjnych. Te szablony znajdują się w [Galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai) lub [usłudze Azure GitHub.](https://github.com/Azure) Te różne przykłady zostaną zwinięte w tym szablonie rozwiązania w czasie.

| # | Tytuł | Opis |
|--:|:------|-------------|
| 2 | [Szablon rozwiązania usługi Azure predictive maintenance](https://github.com/Azure/AI-PredictiveMaintenance) | Szablon rozwiązania typu open source, który pokazuje modelowanie usługi Azure ML i kompletną infrastrukturę platformy Azure zdolną do obsługi scenariuszy konserwacji predykcyjnej w kontekście zdalnego monitorowania IoT. |
| 3 | [Uczenie głębokie dla konserwacji predykcyjnej](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Usługa Azure Notebook z rozwiązaniem demonstracyjnym wykorzystującym sieci LSTM (Long Short-Term Memory) (klasa powtarzających się sieci neuronowych) do konserwacji predykcyjnej z [wpisem w blogu w tym przykładzie](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Przewodnik modelowania konserwacji predykcyjnej w R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Przewodnik modelowania PdM ze skryptami w R.|
| 5 | [Konserwacja predykcyjna azure dla przemysłu lotniczego](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Jeden z pierwszych szablonów rozwiązań PdM opartych na usłudze Azure ML v1.0 do konserwacji samolotów. Ten przewodnik pochodzi z tego projektu. |
| 6 | [Zestaw narzędzi AI platformy Azure dla usługi IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI w krawędzi IoT przy użyciu TensorFlow; zestaw narzędzi pakiety modeli uczenia głębokiego w kontenerach platformy Azure IoT Edge docker i uwidaczniają te modele jako interfejsy API REST.
| 7 | [Konserwacja predykcyjna usługi Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Komputery pakietu Azure IoT Suite — wstępnie skonfigurowane rozwiązanie. Szablon PdM obsługi samolotu z pakietem IoT Suite. [Inny dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) i [instruktaż](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) związane z tym samym projektem. |
| 8 | [Szablon konserwacji predykcyjnej przy użyciu usług SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Pokaz scenariusza pozostałej żywotności opartego na usługach R. |
| 9 | [Przewodnik po modelowaniu konserwacji predykcyjnej](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funkcja zestawu danych konserwacji samolotów opracowana przy użyciu języka R z [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) i [zestawami danych](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) oraz [notesem](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) i [eksperymentami](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) platformy Azure w usłudze AzureML w wersji 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Zasoby szkoleniowe do konserwacji predykcyjnej

Platforma Microsoft Azure oferuje ścieżki szkoleniowe dla podstawowych koncepcji dotyczących technik PdM, oprócz zawartości i szkoleń dotyczących ogólnych koncepcji i praktyki sztucznej inteligencji.

| Zasób szkoleniowy  | Dostępność |
|:-------------------|--------------|
| [Ścieżka szkoleniowa dla pdm przy użyciu drzew i losowego lasu](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Public | 
| [Ścieżka uczenia się dla pdm przy użyciu uczenia głębokiego](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Public |
| [Deweloper sztucznej inteligencji na platformie Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Public |
| [Szkoła AI firmy Microsoft](https://aischool.microsoft.com/learning-paths) | Public |
| [Uczenie się sztucznej inteligencji platformy Azure z usługi GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Public |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Public |
| [Seminaria internetowe Microsoft AI Na YouTube](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Public |
| [Pokaz AI firmy Microsoft](https://channel9.msdn.com/Shows/AI-Show) | Public |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerzy |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partnerzy |

Ponadto bezpłatne MOOCS (masowe otwarte kursy online) na temat AI są oferowane online przez instytucje akademickie, takie jak Stanford i MIT, oraz inne firmy edukacyjne.

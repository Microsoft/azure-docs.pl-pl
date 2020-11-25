---
title: Etyka i właściwe użycie — Personalizacja
titleSuffix: Azure Cognitive Services
description: Te wytyczne mają na celu ułatwienie implementacji personalizacji w sposób ułatwiający Tworzenie relacji zaufania z firmą i usługą. Pamiętaj, aby zadbać o to, aby przeanalizować i zamierzone wpływ personalizacji na mieszkających osób. Gdy jest to wątpliwe, Wyszukaj wskazówki.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005983"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Wytyczne dotyczące odpowiedzialnej implementacji personalizacji

Aby użytkownicy i społeczeństwo mieli pełny potencjał systemu AI, implementacje muszą zostać zaprojektowane w taki sposób, aby mogli uzyskać zaufanie do tych użytkowników, którzy dodali do swoich aplikacji, a użytkownikom aplikacje skompilowane przy użyciu systemu AI. Te wytyczne mają na celu ułatwienie implementacji personalizacji w sposób ułatwiający Tworzenie relacji zaufania z firmą i usługą. Pamiętaj, aby zadbać o to, aby przeanalizować i zamierzone wpływ personalizacji na mieszkających osób. Gdy jest to wątpliwe, Wyszukaj wskazówki.

Te wytyczne nie są przeznaczone jako porady prawne i należy oddzielnie upewnić się, że aplikacja jest zgodna z szybkim postępem działania w tym obszarze oraz w danym sektorze.

Ponadto podczas projektowania aplikacji przy użyciu narzędzia personalizacji należy wziąć pod uwagę szeroki zestaw obowiązków podczas opracowywania systemu AI skoncentrowanego na danych, w tym etyki, ochrony prywatności, bezpieczeństwa, bezpieczeństwa, dołączania, przejrzystości i odpowiedzialności. Więcej informacji na ten temat można znaleźć w sekcji [zalecane odczytywanie](#recommended-reading) .

Możesz użyć poniższej zawartości jako listy początkowych list kontrolnych i dostosować ją do danego scenariusza. Ten dokument zawiera dwa główne sekcje: pierwszy jest przeznaczony do wyróżniania właściwych zagadnień związanych z użyciem podczas wybierania scenariuszy, funkcji i korzyści dla narzędzia Personalizacja. Druga należy wziąć pod uwagę, że w przypadku tworzenia systemów AI należy uwzględnić zestaw wartości Microsoft, a także zapewnia sugestie z możliwością podejmowania działań i ryzyka związane z używaniem przez niego personalizacji.


## <a name="your-responsibility"></a>Twoja odpowiedzialność

Wszystkie wytyczne dla osoby odpowiedzialnej za implementację wdrażają, że deweloperzy i firmy korzystające z narzędzia do personalizowania są odpowiedzialni za korzystanie z tych algorytmów w społeczeństwie. Jeśli tworzysz aplikację, która będzie wdrażana w organizacji, należy rozpoznać rolę i odpowiedzialność za jej działanie oraz wpływ na ludzi. W przypadku projektowania aplikacji, która ma zostać wdrożona przez inną firmę, należy zapoznać się z udostępnionymi informacjami o tym, kto jest w końcu odpowiedzialny za zachowanie aplikacji, oraz dokument, który to rozumie.

Relacja zaufania jest oparta na koncepcji zrealizowanych zobowiązań — należy wziąć pod uwagę użytkowników, społeczeństwo i strukturę prawną, w których aplikacje działają, aby zidentyfikować jawne i niejawne zobowiązania, które mogą mieć.

Firma Microsoft ciągle wprowadza nakład pracy w narzędzia i dokumenty ułatwiające wykonywanie tych obowiązków. [Prześlij opinię do firmy Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) , jeśli uważasz, że dodatkowe narzędzia, funkcje produktu i dokumenty mogą pomóc zaimplementować te wytyczne dotyczące korzystania z narzędzia Personalizacja.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Czynniki odpowiedzialne za wykonywanie przez siebie personalizacji

Zaimplementowanie personalizowania może mieć doskonałą wartość dla użytkowników i Twojej firmy. Aby zaimplementować osoby odpowiedzialne za pomocą personalizacji, należy zacząć od uwzględnienia następujących wytycznych:

* Wybieranie przypadków użycia w celu zastosowania personalizacji.
* Tworzenie [funkcji zarobkowych](concept-rewards.md).
* Wybieranie [funkcji](concepts-features.md) dotyczących kontekstu i możliwych akcji, które będą używane na potrzeby personalizacji.


## <a name="choosing-use-cases-for-personalizer"></a>Wybieranie przypadków użycia dla personalizacji

Użycie usługi, która uczy się do personalizacji zawartości i interfejsów użytkownika, jest przydatna. Można go również zastosować, jeśli sposób, w jaki Personalizacja tworzy negatywne skutki uboczne w świecie rzeczywistym, w tym, jeśli użytkownicy nie znają personalizacji zawartości.

Przykłady użycia programu personalizacji z podwyższonym prawdopodobieństwem negatywnych efektów ubocznych lub brakiem przejrzystości obejmują scenariusze, w których "wynagrodzenie" zależy od wielu długoterminowych, złożonych czynników, które w przypadku nadmiernego rozłożenia mogą mieć niekorzystne wyniki dla osób indywidualnych. Są one traktowane jako "wynikowe" Opcje lub opcje, które wiążą się z ryzykiem szkody. Na przykład:


* **Finanse**: Personalizowanie ofert dotyczących pożyczek, finansów i produktów ubezpieczeniowych, w przypadku których czynniki ryzyka opierają się na danych, na których nie wiadomo, nie mogą uzyskać ani nie mogą się zakwestionować.
* **Edukacja**: Personalizowanie rang dla kursów szkolnych i instytucji edukacyjnych, w których zalecenia mogą propagować bias i zmniejszać świadomość użytkowników w innych opcjach.
* **Demokracja i projektowi Civic Innovation uczestnictwo**: Personalizowanie zawartości dla użytkowników mających na celu wpływanie opinii jest wynikowe i manipulowane.
* **Ocena nagrody** innych firm: Personalizacja elementów w przypadku, gdy wynagrodzenie jest oparte na drugiej ocenie użytkownika przez inną firmę, a nie z wynagrodzeniem wygenerowanym przez własne zachowanie użytkownika.
* **Nietolerancja do eksploracji**: każda sytuacja, w której zachowanie eksplorowania może spowodować szkody.

Podczas wybierania przypadków użycia dla personalizacji:

* Rozpocznij proces projektowania, biorąc pod uwagę, jak Personalizacja pomaga użytkownikom.
* Należy wziąć pod uwagę negatywne konsekwencje w świecie rzeczywistym, jeśli niektóre elementy nie są klasyfikowane dla użytkowników ze względu na wzorce personalizacji lub eksplorację.
* Należy wziąć pod uwagę, czy przypadek użycia stanowi automatyczne przetwarzanie, które znacząco wpływa na zagadnienia dotyczące danych uregulowane w ramach [Rodo](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) artykułu 22 lub innych przepisów.
* Rozważ samoobsługowe pętle Prophecy. Może się tak zdarzyć, jeśli pozostała Personalizacja pociąga za siebie model, dzięki czemu może później dalej wykluczyć grupę demograficzną z dostępu do odpowiedniej zawartości. Na przykład większość osób w sąsiedztwie o małym dochodowiu nie uzyskuje oferty ubezpieczenia Premium i powoli nikt nie zobaczy oferty, jeśli nie ma wystarczającej eksploracji.
* Zapisuj kopie modeli i zasady uczenia w przypadku, gdy konieczne jest odtworzenie personalizacji w przyszłości. Można to zrobić okresowo lub przez każdy okres odświeżania modelu.
* Należy wziąć pod uwagę poziom eksploracji odpowiedni dla miejsca i sposób używania go jako narzędzia do ograniczenia efektów "komory ECHA".


## <a name="selecting-features-for-personalizer"></a>Wybieranie funkcji dla narzędzia Personalizacja

Personalizowanie zawartości zależy od posiadania użytecznych informacji o zawartości i użytkowniku. Należy pamiętać, że w przypadku niektórych aplikacji i branż niektóre funkcje użytkownika mogą być bezpośrednio lub pośrednio uznawane za dyskryminujące i potencjalnie niedozwolone.

Należy wziąć pod uwagę wpływ tych funkcji:

* Dane **demograficzne użytkownika**: funkcje dotyczące płci, płci, wieku, rasę, religii: te funkcje mogą być niedozwolone w niektórych aplikacjach z przyczyn prawnych i nie mogą być etyczne w celu spersonalizowania ich, ponieważ Personalizacja propaguje generalizacje i odchylenia. Przykładem propagacji bias jest księgowanie zadań dla inżynierów, które nie są widoczne dla starszych lub grup odbiorców opartych na płci.
* **Informacje o ustawieniach regionalnych**: w wielu miejscach świata informacje o lokalizacji (takie jak kod pocztowy, kod pocztowy lub nazwa klubu) mogą być wysoce skorelowane z dochodami, rasę i religii.
* **Postrzeganie atrakcyjności przez użytkownika**: nawet w przypadkach, gdy Twoja aplikacja podejmuje decyzje dźwiękowe, należy wziąć pod uwagę wpływ użytkowników, którzy postrzegali zawartość wyświetlaną w aplikacji w taki sposób, aby były one skorelowane z funkcjami, które byłyby dyskryminujące.
* **Niezamierzona różnica w funkcjach**: istnieją różne rodzaje odchyleń, które mogą być wprowadzane przy użyciu funkcji, które mają wpływ tylko na podzestaw populacji. Wymaga to dodatkowej uwagi, jeśli funkcja jest generowana algorithmically, na przykład podczas korzystania z analizy obrazów do wyodrębnienia elementów w analizie obrazu lub tekstu w celu odnalezienia jednostek w tekście. Zapoznaj się z charakterystyką usług używanych do tworzenia tych funkcji.

Zastosuj następujące praktyki podczas wybierania funkcji do wysłania w kontekstach i akcjach do personalizacji:

* Należy wziąć pod uwagę legalność i etykę przy użyciu pewnych funkcji dla niektórych aplikacji oraz czy nieszkodliwe funkcje mogą być serwerami proxy dla innych użytkowników, którzy chcą lub których należy unikać.
* Być przezroczyste dla użytkowników, których algorytmy i analiza danych są używane do personalizowania widocznych opcji.
* Poproś siebie: Czy wszyscy użytkownicy zadbają i będą zadowoleni, jeśli użyłem tych informacji w celu spersonalizowania ich zawartości? Czy warto się dowiedzieć, jak w jaki sposób podjąć decyzję w celu wyróżnienia lub ukrycia niektórych elementów?
* Należy używać zachowań zamiast danych klasyfikacji lub segmentacji na podstawie innych właściwości. Informacje demograficzne były tradycyjnie wykorzystywane przez detalistów z przyczyn historycznych — atrybuty demograficzne były proste do zebrania i działania przed sprzedażą elektroniczną, ale pytanie, jak ważne informacje demograficzne, są dostępne, gdy użytkownik ma rzeczywiste interakcje, kontekstowe i historyczne dane, które są bardziej ściśle powiązane z preferencjami i tożsamością użytkowników.
* Zastanów się, jak zapobiegać występowaniu "sfałszowanych" funkcji przez złośliwych użytkowników, które w przypadku ataków z dużymi liczbami mogą prowadzić do nauczenia się narzędzia do szkolenia w celu celowo całkowicie zakłóceń, Embarrass i nękania niektórych klas użytkowników.
* Jeśli jest to odpowiednie i możliwe, Zaprojektuj aplikację, tak aby użytkownicy mogli wyrazić zgodę na korzystanie z określonych funkcji osobistych lub zrezygnować z nich. Mogą one być zgrupowane, takie jak "informacje o lokalizacji", "informacje o urządzeniu", "przeszła Historia zakupów" itp.


## <a name="computing-rewards-for-personalizer"></a>Obliczanie nagrody dla personalizacji

Personalizowanie dokłada starań, aby poprawić wybór akcji na podstawie wyniku odnoszącego się do wynagrodzenia przez logikę biznesową aplikacji.

Dobrze skonstruowany wynik będzie pełnić rolę krótkoterminowego serwera proxy w celu prowadzenia działalności biznesowej, który jest powiązany z działalnością organizacji.

Na przykład dzięki odniesieniu do kliknięć usługa narzędzia do personalizacji będzie dokonywać kliknięć na kosztach wszystkich innych, nawet wtedy, gdy kliknięto, rozprasza lub nie wiąże się z wynikami biznesowymi.

Przykładowo witryna wiadomości może chcieć ustawić nagrody powiązane z bardziej zrozumiałą niż kliknięcia, na przykład "czy użytkownik poświęca wystarczająco dużo czasu na odczytanie zawartości?". "Czy klikać odpowiednie artykuły lub odwołania?". Dzięki personalizacji można łatwo powiązać metryki ściśle z korzyściami. Ale należy zachować ostrożność, aby nie wyszukać krótkoterminowej zaangażowania użytkownika z dobrymi wynikami.

### <a name="unintended-consequences-from-reward-scores"></a>Niezamierzone konsekwencje z wyników nagrody
Wyniki nagrody mogą być kompilowane z najlepszymi intencjami, ale nadal mogą tworzyć nieoczekiwane konsekwencje lub nieprzewidziane wyniki, jak program Personalizujer porządkuje zawartość.

Rozważmy następujące przykłady:

* Nagradzanie personalizacji zawartości wideo na wartość procentową oglądanej długości wideo prawdopodobnie spowoduje zaplanowanie krótszych filmów wideo.
* Nagradzanie udziałów mediów społecznościowych, bez tonacji analizy sposobu jej udostępniania lub zawartości, może prowadzić do klasyfikowania zawartości obraźliwej, niemoderowanej lub zapalnej, która ma na celu Incite dużo "zaangażowania", ale dodaje małą wartość.
* Nagradzanie akcji dotyczącej elementów interfejsu użytkownika, których użytkownicy nie mogą zmieniać, może zakłócać użyteczność i przewidywalność interfejsu użytkownika, w którym przyciski Surprisingly zmieniają lokalizację lub przeznaczenie bez ostrzeżenia, co utrudnia pracę niektórych grup użytkowników.

Zaimplementuj następujące najlepsze rozwiązania:

* Uruchamiaj eksperymenty w trybie offline w systemie przy użyciu różnych metod nagradzania, aby zrozumieć wpływ i efekty uboczne.
* Oceń funkcje nagradzania i zadawaj sobie, jak bardzo algorytmie osoba może obginać swoją interpretację i uzyskać do niej niepożądane wyniki.


## <a name="responsible-design-considerations"></a>Odpowiedzialne zagadnienia dotyczące projektowania

Poniżej przedstawiono obszary projektowania dla odpowiedzialnych implementacji systemu AI. Dowiedz się więcej obejmować tej struktury w [przyszłości](https://news.microsoft.com/futurecomputed/).

![Wartości AI z przyszłych obliczonych](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Odpowiedzialność
*Osoby, które projektują i wdrażają systemy AI, muszą być odpowiedzialne za działanie systemów*.

* Utwórz wewnętrzne wytyczne dotyczące implementacji personalizacji, dokumentu i komunikacji z zespołem, kierownikami i dostawcami.
* Wykonaj okresowe przeglądy, w jaki sposób są obliczane wyniki, przeprowadź oceny w trybie offline, aby zobaczyć, jakie funkcje mają wpływ na narzędzia do personalizacji, a następnie użyj wyników, aby wyeliminować niepotrzebne i niepotrzebne funkcje.
* Łatwo Komunikuj się z użytkownikami, w jaki sposób jest używany program Personalizuj, w jakim celu i z danymi.
* Archiwizuj informacje i zasoby, takie jak modele, zasady uczenia i inne dane, które są używane przez program do działania, aby można było odtworzyć wyniki.

### <a name="transparency"></a>Przejrzystość
*Systemy AI powinny być zrozumiałe*. Z personalizacją:

* *Przekaż użytkownikom informacje o sposobie spersonalizowania zawartości.* Na przykład możesz pokazać użytkownikom przycisk z etykietą `Why These Suggestions?` pokazującą, które najważniejsze funkcje użytkownika i akcje odgrywają rolę w wynikach narzędzia Personalizacja.
* Upewnij się, że Twoje warunki użytkowania zawierają informacje o tym, że będziesz używać informacji o użytkownikach i ich zachowania w celu spersonalizowania środowiska.

### <a name="fairness"></a>Sprawiedliwość
*Systemy AI powinny traktować wszystkie osoby uczciwie*.

* Nie używaj personalizacji do przypadków użycia, w których wyniki są długoterminowe, wtórne lub związane z rzeczywistą szkodą.
* Nie należy używać funkcji, które nie są odpowiednie do personalizowania zawartości za pomocą programu, lub które mogą pomóc w propagowaniu niepożądanych pochyleń. Na przykład każda osoba mająca podobne okoliczności finansowe powinna zobaczyć te same spersonalizowane zalecenia dotyczące produktów finansowych.
* Informacje o różnicach, które mogą istnieć w funkcjach, które są źródłem z edytorów, narzędzi do algorytmów lub samych użytkowników.

### <a name="reliability-and-safety"></a>Niezawodność i bezpieczeństwo
*Systemy AI powinny działać niezawodnie i bezpiecznie*. Dla personalizacji:

* *Nie należy podawać akcji do personalizacji, które nie powinny być wybierane*. Na przykład nieodpowiednie filmy powinny zostać odfiltrowane z akcji, aby spersonalizować, jeśli zostanie zaleceń dla użytkownika anonimowego lub w wieku.
* *Zarządzaj modelem personalizacji jako zasobem biznesowym*.  Należy wziąć pod uwagę częstotliwość zapisywania i tworzenia kopii zapasowej modelu oraz zasad uczenia się za pętlą personalizacji, a w przeciwnym razie Traktuj ją jako istotny zasób biznesowy. Odtworzenie przeszłych wyników jest ważne w przypadku samodzielnej inspekcji i mierzenia poprawy.
* *Udostępniaj kanały, aby uzyskać bezpośrednią opinię od użytkowników*. Oprócz sprawdzania bezpieczeństwa przy kodowaniu, aby upewnić się, że tylko odpowiednie odbiorcy widzą odpowiednią zawartość, należy udostępnić mechanizm przesyłania opinii dla użytkowników raportujących zawartość, która może być zaskakujące lub zakłócać. Szczególnie jeśli zawartość pochodzi od użytkowników lub innych firm, należy rozważyć użycie Content Moderator firmy Microsoft lub dodatkowych narzędzi do przeglądania i weryfikowania zawartości.
* *Wykonywanie częstych ocen w trybie offline*. Pomoże to monitorować trendy i upewnić się, że efektywność jest znana.
* *Ustanów proces wykrywania i działania w przypadku złośliwego manipulowania*. Istnieją aktory korzystające z możliwości uczenia maszynowego i systemu AI do uczenia się w ich środowisku w celu przesunięcia wyników do ich celów. Jeśli korzystanie z programu Personalizujer ma wpływ na ważne wybory, upewnij się, że masz odpowiednie środki na wykrycie i złagodzenie tych klas ataków, w tym przegląd ludzki w odpowiednich okolicznościach.

### <a name="security-and-privacy"></a>Zabezpieczenia i ochrona prywatności
*Systemy AI powinny być bezpieczne i uwzględniać ochronę prywatności*. W przypadku korzystania z narzędzia Personalizacja:

* *Poinformuj użytkowników o zbieranych danych i sposobie ich użycia i uzyskaj ich zgodę wcześniej* zgodnie z obowiązującymi przepisami lokalnymi i branżowymi.
* *Zapewnienie ochrony prywatności — Ochrona formantów użytkownika.* W przypadku aplikacji, które przechowują dane osobowe, należy rozważyć udostępnienie przycisku łatwej do znalezienia dla funkcji takich jak:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

W niektórych przypadkach mogą one być legalnie wymagane. Należy rozważyć uwzględnienie kompromisów w modelach reszkoleniowych okresowo, aby nie zawierały śladów usuniętych danych.

### <a name="inclusiveness"></a>Brak wykluczeń
*Zaspokoj szeroką gamę potrzeb ludzkich i środowiska*.
* *Zapewnianie spersonalizowanych środowisk dla interfejsów z obsługą ułatwień dostępu.* Wydajność, która pochodzi z dobrego personalizacji — stosowana w celu zmniejszenia nakładu pracy, przenoszenia i niepotrzebnego powtórzenia w interakcjach — może być szczególnie przydatna dla osób niepełnosprawnych.
* *Dopasuj zachowanie aplikacji do kontekstu*. Można użyć personalizacji do rozróżnienia między intencjami w bot rozmowy, na przykład w przypadku, gdy prawo interpretacji może być kontekstowe, a jeden rozmiar może nie pasować.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proaktywne gotowość do zwiększenia ochrony i zarządzania danymi

Trudno jest przewidzieć konkretne zmiany w kontekstach przepisów, ale ogólnie rzecz biorąc, byłoby to, aby przekroczyć minimalną strukturę prawną w celu zapewnienia respectful korzystania z danych osobowych i zapewnienia przejrzystości i wyboru związanego z podejmowaniem decyzji o algorytmach.


* Należy rozważyć planowanie do sytuacji, w której mogą wystąpić nowe ograniczenia dotyczące danych zbieranych od osób indywidualnych i istnieje potrzeba pokazania sposobu użycia w celu podejmowania decyzji.
* Weź pod uwagę dodatkowe gotowość, w której użytkownicy mogą obejmować marginalne wypełnienia zagrożone, dzieci, użytkowników w przypadku luk w zabezpieczeniach lub użytkowników, którzy mają wpływ na manipulowanie algorytmem.
* Weź pod uwagę rozległy stopień niezadowolenia, w jaki sposób są odtwarzane programy i algorytmy zbierania danych, które wpływają na odbiorców, oraz sposoby zapobiegania sprawdzonym błędom strategicznym.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proaktywne oceny w cyklu życia projektu

Należy rozważyć utworzenie metod dla członków zespołu, użytkowników i właścicieli firmy w celu zgłaszania problemów związanych z właściwym użyciem oraz tworzenie procesu, który ustala priorytet ich rozdzielczości i zapobiega Retaliation.

Każda osoba, która zastanawia się o skutkach ubocznych korzystania z dowolnej technologii, jest ograniczona przez ich perspektywę i środowisko pracy. Rozszerz zakres opinii dostępnych przez nadawanie bardziej różnorodnych głosów członkom zespołów, użytkowników lub rad doradczych; w taki sposób, że jest to możliwe i zachęca się do ich wypowiedzenia. Zapoznaj się z materiałami szkoleniowymi i szkoleniowymi, aby dodatkowo rozszerzyć wiedzę zespołu w tej domenie oraz dodać funkcję do omówienia złożonych i poufnych tematów.

Rozważ zatraktowanie zadań dotyczących odpowiedzialnych zastosowań, podobnie jak inne crosscuttinge zadania w cyklu życia aplikacji, takie jak zadania związane ze środowiskami użytkownika, zabezpieczeniami lub DevOps. Te zadania i ich wymagania nie mogą być zostawiać. W całym cyklu życia aplikacji należy omówić i zweryfikować odpowiednie użycie.

## <a name="questions-and-feedback"></a>Pytania i opinie

Firma Microsoft ciągle wprowadza nakład pracy w narzędzia i dokumenty ułatwiające wykonywanie tych obowiązków. Nasz zespół zaprasza Cię do [przekazywania opinii do firmy Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  , jeśli uważasz, że dodatkowe narzędzia, funkcje produktu i dokumenty ułatwią zaimplementowanie tych wytycznych dla narzędzia personalizacji.

## <a name="recommended-reading"></a>Zalecany odczyt

* Zapoznaj się z sześcioma zasadami firmy Microsoft w zakresie odpowiedzialnego rozwoju systemu AI opublikowanego w książce 2018 stycznia, [przyszłe obliczenia](https://news.microsoft.com/futurecomputed/)
* [Kto jest właścicielem przyszłości?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) Autor Jaron Lanier.
* [Broń zniszczenia Matematycznego](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) przez-Cathy O'Neil
* [Etyka i nauka danych](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) przez DJ Patil, Hilary Mason, Jan Loukides.
* [Kod etyczny etyki](https://www.acm.org/code-of-ethics)
* [Informacje genetyczne Act bezdyskryminacji-GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Zasady FATML dla algorytmów umożliwiających obsługę](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Następne kroki

[Funkcje: Akcja i kontekst](concepts-features.md).

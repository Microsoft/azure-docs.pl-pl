---
title: Wprowadzenie do analizy przestrzennej przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym dokumencie wyjaśniono podstawowe pojęcia i funkcje kontenera analizy przestrzennej przetwarzanie obrazów.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 158a5e5f859749ec2ca20bfa4783fe32cc17ee0e
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964613"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Wprowadzenie do analizy przestrzennej przetwarzanie obrazów

Przetwarzanie obrazów Analiza przestrzenna to nowa funkcja usługi Azure Cognitive Services przetwarzanie obrazów, która ułatwia organizacjom maksymalizowanie wartości miejsc fizycznych przez zrozumienie ruchów i obecności osób w danym obszarze. Pozwala to na pozyskiwanie wideo z kamer CCTV lub nadzoru, uruchamianie operacji AI w celu wyodrębnienia szczegółowych informacji z strumieni wideo oraz generowanie zdarzeń, które mają być używane przez inne systemy. W przypadku danych wejściowych ze strumienia aparatów operacja AI może posłużyć do policzania liczby osób wprowadzających miejsce lub mierzenia zgodności z maską powierzchni i wskazówkami distancing społecznościowych.

## <a name="the-basics-of-spatial-analysis"></a>Podstawy analizy przestrzennej

Obecnie podstawowe operacje związane z analizą przestrzenną są zbudowane na potoku, który pozyskuje wideo, wykrywa osoby w filmie wideo, śledzi je w miarę ich przemieszczania i generuje zdarzenia, gdy ludzie pracują z interesującymi Cię regionami.

## <a name="spatial-analysis-terms"></a>Terminy analizy przestrzennej

| Okres | Definicja |
|------|------------|
| Wykrywanie osób | Ten składnik odpowiada na pytanie "gdzie znajdują się osoby z tego obrazu"? Znajduje ludzie w obrazie i przekazuje pole ograniczenia wskazujące lokalizację każdej osoby do składnika śledzenia osób. |
| Śledzenie osób | Ten składnik łączy wykrywania osób z upływem czasu, gdy ludzie poruszają się przed kamerą. Korzysta ona z logiki czasowej dotyczącej sposobu, w jaki ludzie zwykle przechodzą i podstawowe informacje o ogólnym wyglądzie osób, aby to zrobić. Nie śledzi osób w wielu aparatach. Jeśli osoba już istnieje w polu widzenia z aparatu przez czas dłuższy niż około minuty, a następnie ponownie przejdzie do widoku aparatu, system będzie postrzegał to jako nową osobę. Śledzenie osób nie identyfikuje jednoznacznie użytkowników w różnych kamerach. Nie używa rozpoznawania twarzy ani śledzenia chodu. |
| Wykrywanie maski rozpoznawania | Ten składnik wykrywa lokalizację działania osoby w polu "widok" aparatu i identyfikuje obecność maski czołowej. W tym celu operacja AI skanuje obrazy z filmu wideo; gdy zostanie wykryta, usługa zapewnia ograniczone pole wokół czołowej. Korzystając z możliwości wykrywania obiektów, identyfikuje on obecność masek krawędzi w polu ograniczenia. Wykrywanie maski twarzy nie obejmuje rozróżniania jednej twarzy od innej twarzy, przewidywania ani klasyfikowania atrybutów twarzy ani do rozpoznawania twarzy. |
| Region zainteresowania | Jest to strefa lub linia zdefiniowana w wejściowym wideo w ramach konfiguracji. Gdy osoba współdziała z regionem wideo, system generuje zdarzenie. Na przykład dla operacji PersonCrossingLine wiersz jest zdefiniowany w filmie wideo. Gdy osoba przecina ten wiersz, generowane jest zdarzenie. |
| Zdarzenie | Zdarzenie to podstawowy wynik analizy przestrzennej. Każda operacja emituje określone zdarzenie okresowo (np. raz na minutę) lub w przypadku wystąpienia określonego wyzwalacza. Zdarzenie zawiera informacje o tym, co się stało w wejściowym filmie wideo, ale nie zawiera obrazów ani filmów wideo. Na przykład operacja PeopleCount może emitować zdarzenie zawierające zaktualizowaną liczbę za każdym razem, gdy liczba osób ulegnie zmianie (wyzwalacz) lub co minutę (okresowo). |

## <a name="example-use-cases-for-spatial-analysis"></a>Przykładowe przypadki użycia na potrzeby analizy przestrzennej

Poniżej przedstawiono przykładowe przypadki użycia, które mamy na uwadze w przypadku zaprojektowanych i przetestowanych analiz przestrzennych.

**Zgodność ze społecznością Distancing** — Przestrzeń biurowa zawiera kilka kamer, które wykorzystują analizę przestrzenną do monitorowania zgodności społeczności społecznej, mierząc odległość między osobami. Kierownik ds. infrastruktury może używać map cieplnych, które przedstawiają zagregowane dane statystyczne zgodności społecznej distancing w miarę upływu czasu, aby dostosować obszar roboczy i uprościć distancing społecznościowe.

**Analiza klientów** — w sklepie spożywczym są wykorzystywane kamery wskazane na wyświetlaczach produktów, aby zmierzyć wpływ zmian w ruchu w sklepie. System umożliwia menedżerowi magazynu zidentyfikowanie, które nowe produkty są dyskami z największą zmianą zaangażowania.

**Zarządzanie kolejką** — kamery wskazywane w kolejkach wyewidencjonowania zapewniają alerty menedżerom, gdy czas oczekiwania jest zbyt długi, co pozwala na otwieranie większej liczby wierszy. Dane historyczne dotyczące odrzucenia kolejki dają wgląd w zachowanie konsumenta.

**Zgodność z maską czołową** — sklepy detaliczne mogą korzystać z kamer wskazujących przód w sklepie, aby sprawdzać, czy klienci przeanalizują dane w sklepie, czy są one w stanie zapewnić zgodność z bezpieczeństwem i analizować zagregowane dane statystyczne w celu uzyskania wglądu w trendy dotyczące użycia masek. 

**Kompilowanie &ego analizy** — Kompilowanie pakietu Office korzysta z kamer ukierunkowanych na wejścia, aby mierzyć FootFall oraz jak ludzie korzystają z miejsca pracy. Usługa Insights umożliwia menedżerowi kompilacji dostosowanie usługi i układu w celu lepszego obkorzystania przez użytkowników.

**Minimalne wykrywanie pracowników** — w centrum danych — działanie monitora kamer wokół serwerów. Gdy pracownicy są fizycznie naprawianie poufnego sprzętu, dwie osoby zawsze muszą być obecne podczas naprawy ze względów bezpieczeństwa. Aparaty fotograficzne są używane do weryfikacji, czy te wytyczne są przestrzegane.

**Optymalizacja w miejscu pracy** — w szybkiej restauracji, kamery w kuchni są używane do generowania zagregowanych informacji o przepływie pracy pracownika. Jest on używany przez menedżerów, aby usprawnić procesy i szkolenia dla zespołu.

## <a name="considerations-when-choosing-a-use-case"></a>Zagadnienia dotyczące wybierania przypadku użycia

**Unikaj krytycznego alertu** dotyczącego bezpieczeństwa — Analiza przestrzenna nie została zaprojektowana w celu zapewnienia krytycznego alertu w czasie rzeczywistym. Nie należy polegać na scenariuszach, gdy alerty w czasie rzeczywistym są niezbędne do wyzwalania interwencji, aby zapobiec powstaniu szkody, np. wyłączaniu dużej liczby maszyn, gdy dana osoba jest obecna. Może służyć do zmniejszenia ryzyka przy użyciu statystyk i interwencji w celu zmniejszenia ryzyka związanego z zachowaniem, takich jak ludzie wprowadzający obszar ograniczony/zabroniony.

**Unikaj stosowania decyzji związanych z zatrudnieniem** — Analiza przestrzenna zawiera metryki probabilistyczne dotyczące lokalizacji i przenoszenia osób w obrębie miejsca. Chociaż te dane mogą być przydatne do poprawy procesu agregowania, dane nie są dobrym wskaźnikiem wydajności poszczególnych procesów roboczych i nie powinny być używane do podejmowania decyzji związanych z zatrudnieniem.

**Unikaj stosowania w przypadku decyzji związanych z opieką zdrowotną** — Analiza przestrzenna zapewnia probabilistyczne i częściowe dane związane z ruchem osób. Dane nie są odpowiednie do podejmowania decyzji związanych z kondycją.

**Unikaj używania funkcji chronionych miejsc** — Ochrona prywatności osób przez ocenianie lokalizacji i pozycji aparatu, Ustawianie kątów i regionu zainteresowania, tak aby nie zapomina obszarów chronionych, takich jak restrooms.

**Ostrożnie rozważaj korzystanie z szkół lub udogodnień dla osób starszych** — Analiza przestrzenna nie przetestowano z danymi zawierającymi niewielkie wartości w wieku 18 lub dorosłym w wieku 65. Zalecamy, aby klienci mogli dokładnie oceniać stawki błędów dla ich scenariusza w środowiskach, w których ten wiek jest wyższy.

**Uważnie Rozważ użycie w miejscach publicznych** — Oceń lokalizacje i położenia kamer, dostosowując kąty i regiony zainteresowania, aby zminimalizować liczbę kolekcji z miejsc publicznych. Oświetlenie i Pogoda w miejscach publicznych, takich jak ulice i Parky, znacząco wpłyną na wydajność systemu analizy przestrzennej i niezwykle trudno jest zapewnić skuteczne ujawnienie w miejscach publicznych.

## <a name="spatial-analysis-gating-for-public-preview"></a>Kontroli analizy przestrzennej dla publicznej wersji zapoznawczej

Aby zapewnić, że Analiza przestrzenna jest używana w scenariuszach, dla których została zaprojektowana, firma Microsoft udostępnia tę technologię klientom za poorednictwem procesu aplikacji. Aby uzyskać dostęp do analizy przestrzennej, należy zacząć od wypełnienia formularza pobrania online. [Zacznij tutaj aplikację](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Dostęp do publicznej wersji zapoznawczej analizy przestrzennej jest uzależniony od uznania firmy Microsoft w oparciu o kryteria kwalifikujące, proces przed sprawdzeniem i dostępność do obsługi ograniczonej liczby klientów w ramach tej warunkowej wersji zapoznawczej. W publicznej wersji zapoznawczej szukamy klientów, którzy mają znaczącą relację z firmą Microsoft, chcą pracować z nami na zalecanych przypadkach użycia i dodatkowych scenariuszach, które są w utrzymaniu z naszymi właściwymi zobowiązaniami AI.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Charakterystyki i ograniczenia dotyczące analizy przestrzennej](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)

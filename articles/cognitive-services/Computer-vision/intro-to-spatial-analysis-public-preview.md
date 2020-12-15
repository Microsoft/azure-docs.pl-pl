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
ms.openlocfilehash: e017fac551e3122cc6586b32423ff166462ccad8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513397"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Wprowadzenie do analizy przestrzennej przetwarzanie obrazów

Przetwarzanie obrazów Analiza przestrzenna to nowa funkcja usługi Azure Cognitive Services przetwarzanie obrazów, która ułatwia organizacjom maksymalizowanie wartości miejsc fizycznych przez zrozumienie ruchów i obecności osób w danym obszarze. Pozwala to na pozyskiwanie wideo z kamer CCTV lub nadzoru, uruchamianie umiejętności AI w celu wyodrębnienia szczegółowych informacji z strumieni wideo oraz generowanie zdarzeń, które mają być używane przez inne systemy. Dzięki wejściu ze strumienia aparatów, umiejętność systemu AI może posłużyć do policzania liczby osób wprowadzających miejsce lub mierzenia zgodności z przepisami distancing społecznościowych.

## <a name="the-basics-of-spatial-analysis"></a>Podstawy analizy przestrzennej

Dzisiaj podstawowe umiejętności analizy przestrzennej są oparte na potoku, który pozyskuje wideo, wykrywa osoby w filmie wideo, śledzi osoby w czasie, gdy są one przenoszone w miarę upływu czasu, a także generuje zdarzenia, gdy ludzie pracują z interesującymi Cię regionami.

## <a name="spatial-analysis-terms"></a>Terminy analizy przestrzennej

| Okres | Definicja |
|------|------------|
| Wykrywanie osób | Ten składnik odpowiada na pytanie "gdzie znajdują się osoby z tego obrazu"? Znajduje ludzie w obrazie i przekazuje pole ograniczenia wskazujące lokalizację każdej osoby do składnika śledzenia osób. |
| Śledzenie osób | Ten składnik łączy wykrywania osób z upływem czasu, gdy ludzie poruszają się przed kamerą. Korzysta ona z logiki czasowej dotyczącej sposobu, w jaki ludzie zwykle przechodzą i podstawowe informacje o ogólnym wyglądzie osób, aby to zrobić. Nie może ona śledzić osób w wielu aparatach ani ponownie identyfikować osoby, która zniknęła przez więcej niż około minutę. Śledzenie osób nie używa żadnych znaczników biometrycznych, takich jak rozpoznawanie lub śledzenie chodu. |
| Region zainteresowania | Jest to strefa lub linia zdefiniowana w wejściowym wideo w ramach konfiguracji. Gdy osoba współdziała z regionem wideo, system generuje zdarzenie. Na przykład w przypadku umiejętności PersonCrossingLine linia jest definiowana w filmie wideo. Gdy osoba przecina ten wiersz, generowane jest zdarzenie. |
| Zdarzenie | Zdarzenie to podstawowy wynik analizy przestrzennej. Każda umiejętność emituje określone zdarzenie okresowo (np. raz na minutę) lub w przypadku wystąpienia określonego wyzwalacza. Zdarzenie zawiera informacje o tym, co się stało w wejściowym filmie wideo, ale nie zawiera obrazów ani filmów wideo. Na przykład umiejętność PeopleCount może emitować zdarzenie zawierające zaktualizowaną liczbę za każdym razem, gdy liczba osób ulegnie zmianie (wyzwalacz) lub co minutę (okresowo). |

## <a name="example-use-cases-for-spatial-analysis"></a>Przykładowe przypadki użycia na potrzeby analizy przestrzennej

Poniżej przedstawiono przykładowe przypadki użycia, które mamy na uwadze w przypadku zaprojektowanych i przetestowanych analiz przestrzennych.

**Zgodność ze społecznością Distancing** — Przestrzeń biurowa zawiera kilka kamer, które wykorzystują analizę przestrzenną do monitorowania zgodności społeczności społecznej, mierząc odległość między osobami. Kierownik ds. infrastruktury może używać map cieplnych, które przedstawiają zagregowane dane statystyczne zgodności społecznej distancing w miarę upływu czasu, aby dostosować obszar roboczy i uprościć distancing społecznościowe.

**Analiza klientów** — w sklepie spożywczym są wykorzystywane kamery wskazane na wyświetlaczach produktów, aby zmierzyć wpływ zmian w ruchu w sklepie. System umożliwia menedżerowi magazynu zidentyfikowanie, które nowe produkty są dyskami z największą zmianą zaangażowania.

**Zarządzanie kolejką** — kamery wskazywane w kolejkach wyewidencjonowania zapewniają alerty menedżerom, gdy czas oczekiwania jest zbyt długi, co pozwala na otwieranie większej liczby wierszy. Dane historyczne dotyczące odrzucenia kolejki dają wgląd w zachowanie konsumenta.

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

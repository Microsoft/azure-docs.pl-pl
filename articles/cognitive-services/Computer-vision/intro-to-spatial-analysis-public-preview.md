---
title: Przegląd analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: W tym dokumencie wyjaśniono podstawowe pojęcia i funkcje kontenera analizy przestrzennej przetwarzanie obrazów.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575364"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Przegląd analizy przestrzennej przetwarzanie obrazów

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

## <a name="responsible-use-of-spatial-analysis-technology"></a>Odpowiedzialne użycie technologii analizy przestrzennej

Aby dowiedzieć się, jak korzystać z technologii analizy przestrzennej, zapoznaj się z [uwagami](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)dotyczącymi przejrzystości. Przejrzyste notatki firmy Microsoft mają na celu ułatwienie zrozumienia sposobu działania technologii AI. właściciele systemu wyboru mogą mieć wpływ na wydajność i zachowanie systemu oraz znaczenie dla całego systemu, w tym technologii, osób i środowiska.

## <a name="spatial-analysis-gating-for-public-preview"></a>Kontroli analizy przestrzennej dla publicznej wersji zapoznawczej

Aby zapewnić, że Analiza przestrzenna jest używana w scenariuszach, dla których została zaprojektowana, firma Microsoft udostępnia tę technologię klientom za poorednictwem procesu aplikacji. Aby uzyskać dostęp do analizy przestrzennej, należy zacząć od wypełnienia formularza pobrania online. [Zacznij tutaj aplikację](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Dostęp do publicznej wersji zapoznawczej analizy przestrzennej jest uzależniony od uznania firmy Microsoft w oparciu o kryteria kwalifikujące, proces przed sprawdzeniem i dostępność do obsługi ograniczonej liczby klientów w ramach tej warunkowej wersji zapoznawczej. W publicznej wersji zapoznawczej szukamy klientów, którzy mają znaczącą relację z firmą Microsoft, chcą pracować z nami na zalecanych przypadkach użycia i dodatkowych scenariuszach, które są w utrzymaniu z naszymi właściwymi zobowiązaniami AI.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do kontenera analizy przestrzennej](spatial-analysis-container.md)
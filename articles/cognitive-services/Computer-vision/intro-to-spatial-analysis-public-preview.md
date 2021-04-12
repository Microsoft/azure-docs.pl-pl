---
title: Co to jest Analiza przestrzenna?
titleSuffix: Azure Cognitive Services
description: W tym dokumencie wyjaśniono podstawowe pojęcia i funkcje kontenera analizy przestrzennej przetwarzanie obrazów.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284546"
---
# <a name="what-is-spatial-analysis"></a>Co to jest Analiza przestrzenna?

Usługa analizy przestrzennej pomaga organizacjom maksymalizować wartość ich miejsc fizycznych przez zrozumienie ruchów i obecności osób w danym obszarze. Pozwala to na pozyskiwanie wideo z kamer CCTV lub nadzoru, uruchamianie operacji AI w celu wyodrębnienia szczegółowych informacji z strumieni wideo oraz generowanie zdarzeń, które mają być używane przez inne systemy. W przypadku danych wejściowych ze strumienia aparatów operacja AI może posłużyć do policzania liczby osób wprowadzających miejsce lub mierzenia zgodności z maską powierzchni i wskazówkami distancing społecznościowych.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Wyniki działania

Podstawowe operacje związane z analizą przestrzenną są zbudowane na potoku, który pozyskuje wideo, wykrywa osoby w filmie wideo, śledzi osoby w czasie, gdy poruszają się w miarę upływu czasu, a także generuje zdarzenia, gdy ludzie pracują z interesującymi Cię regionami.

## <a name="spatial-analysis-features"></a>Funkcje analizy przestrzennej

| Cecha | Definicja |
|------|------------|
| **Wykrywanie osób** | Ten składnik odpowiada na pytanie "gdzie znajdują się osoby z tego obrazu?". Znajdowanie osób w obrazie i przekazanie pola ograniczenia wskazujące lokalizację każdej osoby do składnika śledzenia osób. |
| **Śledzenie osób** | Ten składnik łączy wykrywania osób z upływem czasu, gdy ludzie poruszają się przed kamerą. Korzysta ona z logiki czasowej dotyczącej sposobu, w jaki ludzie zwykle przechodzą i podstawowe informacje o ogólnym wyglądzie osób. Nie śledzi osób w wielu aparatach. Jeśli osoba opuszcza pole widzenia dłużej niż około minuty, a następnie ponownie przejdzie do widoku aparatu, system będzie postrzegał to jako nową osobę. Śledzenie osób nie identyfikuje jednoznacznie użytkowników w różnych kamerach. Nie używa rozpoznawania twarzy ani śledzenia chodu. |
| **Wykrywanie maski rozpoznawania** | Ten składnik wykrywa lokalizację działania osoby w polu "widok" aparatu i identyfikuje obecność maski czołowej. Operacja AI skanuje obrazy z filmu wideo; gdy zostanie wykryta, usługa zapewnia ograniczone pole wokół czołowej. Korzystając z możliwości wykrywania obiektów, identyfikuje on obecność masek krawędzi w polu ograniczenia. Wykrywanie maski twarzy nie obejmuje rozróżniania jednej twarzy od innej twarzy, przewidywania ani klasyfikowania atrybutów twarzy ani do rozpoznawania twarzy. |
| **Region zainteresowania** | Jest to zdefiniowana przez użytkownika strefa lub wiersz w wejściowej ramce wideo. Gdy osoba współdziała z tym regionem w filmie wideo, system generuje zdarzenie. Na przykład dla operacji PersonCrossingLine wiersz jest zdefiniowany w filmie wideo. Gdy osoba przecina ten wiersz, generowane jest zdarzenie. |
| **Zdarzenie** | Zdarzenie to podstawowy wynik analizy przestrzennej. Każda operacja emituje określone zdarzenie okresowo (na przykład raz na minutę) lub przy każdym wystąpieniu określonego wyzwalacza. Zdarzenie zawiera informacje o tym, co się stało w wejściowym filmie wideo, ale nie zawiera obrazów ani filmów wideo. Na przykład operacja PeopleCount może emitować zdarzenie zawierające zaktualizowaną liczbę za każdym razem, gdy liczba osób ulegnie zmianie (wyzwalacz) lub co minutę (okresowo). |

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="public-preview-gating"></a>Publiczna wersja zapoznawcza kontroli

Aby zapewnić, że Analiza przestrzenna jest używana w scenariuszach, dla których została zaprojektowana, firma Microsoft udostępnia tę technologię klientom za poorednictwem procesu aplikacji. Aby uzyskać dostęp do analizy przestrzennej, należy zacząć od wypełnienia naszego formularza pobranego w trybie online. [Zacznij tutaj aplikację](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Dostęp do publicznej wersji zapoznawczej analizy przestrzennej jest uzależniony od uznania firmy Microsoft w oparciu o kryteria kwalifikujące, proces przed sprawdzeniem i dostępność do obsługi ograniczonej liczby klientów w ramach tej warunkowej wersji zapoznawczej. W publicznej wersji zapoznawczej szukamy klientów, którzy mają znaczącą relację z firmą Microsoft, chcą pracować z nami na zalecanych przypadkach użycia i dodatkowych scenariuszach, które zachowują się z naszymi odpowiedzialnymi zobowiązaniami AI.

### <a name="follow-a-quickstart"></a>Postępuj zgodnie z przewodnikiem Szybki Start

Po udzieleniu dostępu do analizy przestrzennej postępuj zgodnie z [przewodnikiem Szybki Start](spatial-analysis-container.md) , aby skonfigurować kontener i rozpocząć analizowanie wideo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Odpowiedzialne użycie technologii analizy przestrzennej

Aby dowiedzieć się, jak korzystać z technologii analizy przestrzennej, zapoznaj się z [uwagami](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)dotyczącymi przejrzystości. Przejrzyste notatki firmy Microsoft mają na celu ułatwienie zrozumienia sposobu działania technologii AI. właściciele systemu wyboru mogą mieć wpływ na wydajność i zachowanie systemu oraz znaczenie dla całego systemu, w tym technologii, osób i środowiska.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: kontener analizy przestrzennej](spatial-analysis-container.md)"" "" "" "" "" "
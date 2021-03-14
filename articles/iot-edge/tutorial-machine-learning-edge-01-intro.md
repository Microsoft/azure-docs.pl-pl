---
title: 'Samouczek: szczegółowy przewodnik po Machine Learning na Azure IoT Edge'
description: Ogólny samouczek, który przeprowadzi Cię przez różne zadania niezbędne do utworzenia kompleksowej uczenia maszynowego w ramach scenariusza brzegowego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67cc470b4f7f119b7f5b86bcb82ea284ab662dfe
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463242"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Samouczek: kompleksowe rozwiązanie przy użyciu Azure Machine Learning i IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Często aplikacje IoT chcą korzystać z inteligentnej chmury i inteligentnej krawędzi. W tym samouczku przeprowadzimy Cię przez szkolenie modelu uczenia maszynowego za pomocą danych zebranych z urządzeń IoT w chmurze, wdrożenie tego modelu do IoT Edge i okresowe utrzymywanie i rafinacja modelu.

>[!NOTE]
>Koncepcje tego zestawu samouczków mają zastosowanie do wszystkich wersji IoT Edge, ale przykładowe urządzenie utworzone w celu wypróbowania scenariusza zostanie uruchomione IoT Edge wersji 1,1.

Głównym celem tego samouczka jest wprowadzenie przetwarzania danych IoT za pomocą uczenia maszynowego, w zależności od krawędzi. W przypadku wielu aspektów ogólnego przepływu pracy uczenia maszynowego ten samouczek nie jest przeznaczony do dokładnego wprowadzenia do uczenia maszynowego. W tym przypadku nie próbujemy utworzyć wysoce zoptymalizowanego modelu dla przypadku użycia — wystarczy, aby zilustrować proces tworzenia i używania modelu zdolnego do przetwarzania danych IoT.

W tej części samouczka omówiono następujące zagadnienia:

> [!div class="checklist"]
>
> * Wymagania wstępne do wykonania kolejnych części samouczka.
> * Docelowi odbiorcy samouczka.
> * Przypadek użycia symulowany przez samouczek.
> * Ogólny proces opisany w samouczku do spełnienia przypadku użycia.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz mieć dostęp do subskrypcji platformy Azure, w której masz uprawnienia do tworzenia zasobów. Niektóre z usług używanych w tym samouczku będą naliczane opłaty za platformę Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz zacząć korzystać z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Potrzebna jest również maszyna z zainstalowanym programem PowerShell, gdzie można uruchamiać skrypty, aby skonfigurować maszynę wirtualną platformy Azure jako maszynę deweloperskią.

W tym dokumencie używany jest następujący zestaw narzędzi:

* Usługa Azure IoT Hub do przechwytywania danych

* Azure Notebooks jako nasz główny fronton do przygotowywania danych i uczenia maszynowego. Uruchamianie kodu w języku Python w notesie z podzbiorem przykładowych danych jest doskonałym sposobem na szybkie iteracyjne i interaktywne szybkością oferowaną podczas przygotowywania danych. Notesy Jupyter mogą również służyć do przygotowywania skryptów do uruchamiania na dużą skalę w zapleczu obliczeniowym.

* Azure Machine Learning jako zaplecze dla uczenia maszynowego na dużą skalę i dla generacji obrazów uczenia maszynowego. Azure Machine Learning zaplecza przy użyciu skryptów przygotowanych i przetestowanych w notesach Jupyter.

* Azure IoT Edge dla aplikacji w chmurze dla obrazu uczenia maszynowego

Oczywiście dostępne są inne opcje. W niektórych scenariuszach, na przykład, IoT Central może służyć jako alternatywny kod, aby przechwycić początkowe dane szkoleniowe z urządzeń IoT.

## <a name="target-audience-and-roles"></a>Docelowi odbiorcy i role

Ten zestaw artykułów jest przeznaczony dla deweloperów bez wcześniejszego doświadczenia w programowaniu IoT lub uczeniu maszynowym. Wdrożenie uczenia maszynowego na brzegu wymaga znajomości sposobu łączenia szerokiej gamy technologii. W związku z tym ten samouczek obejmuje cały kompleksowy scenariusz, który pokazuje jeden ze sposobów dołączania tych technologii do rozwiązania IoT. W środowisku rzeczywistym te zadania mogą być dystrybuowane między kilka osób z różnymi specjalizacjami. Na przykład deweloperzy mogą skoncentrować się na każdym urządzeniu lub kodzie w chmurze, a analityków danych zaprojektowali modele analityczne. Aby umożliwić indywidualnym deweloperom pomyślne ukończenie tego samouczka, firma Microsoft udostępniła wskazówki uzupełniające z informacjami i linki do większej ilości informacji, które mamy nadzieję, aby zrozumieć, co jest gotowe, a także dlaczego.

Alternatywnie, możesz połączyć się z współpracownikami z różnych ról, aby postępować wspólnie z samouczkiem, dzięki czemu masz pełną wiedzę i Dowiedz się jako zespół, w jaki sposób dopasowuje się do siebie.

W obu przypadkach, aby ułatwić zorientowanie czytelników, każdy artykuł w tym samouczku wskazuje rolę użytkownika. Role te obejmują:

* Programowanie w chmurze (w tym deweloperów w chmurze pracujących w DevOps pojemności)
* Analiza danych

## <a name="use-case-predictive-maintenance"></a>Przypadek użycia: konserwacja predykcyjna

Ten scenariusz jest oparty na przypadku użycia przedstawionym na konferencji w temacie dotyczących and Health Management (PHM08) w 2008. Celem jest przewidywanie pozostałego okresu użytkowania (pozostałego czasu eksploatacji) zestawu turbofanych aparatów samolotowych. Te dane zostały wygenerowane przy użyciu języka C-MAPS, komercyjnej wersji map (modularnej Aero-Propulsion symulacji systemu). To oprogramowanie zapewnia elastyczne środowisko symulacji aparatu TurboFan, aby wygodnie zasymulować parametry kondycji, kontroli i aparatu.

Dane używane w tym samouczku są pobierane z [zestawu danych symulacji degradacji aparatu TurboFan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Z pliku Readme:

***Scenariusz eksperymentalny***

*Zestawy danych składają się z wielu wieloczynnikowa szeregów czasowych. Każdy zestaw danych jest dalej podzielony na podzbiory szkoleniowe i testowe. Każda seria czasowa jest z innego silnika — to znaczy, że dane mogą być uważane za pochodzące z floty silników tego samego typu. Każdy silnik rozpoczyna się od różnych stopni początkowych zużycia i produkcji, które są nieznane dla użytkownika. Takie zużycie i zróżnicowanie jest uznawane za normalne, tj. nie jest traktowane jako warunek błędu. Istnieją trzy ustawienia operacyjne, które mają znaczny wpływ na wydajność aparatu. Te ustawienia są również zawarte w danych. Dane są zanieczyszczone hałasem czujnika.*

*Aparat działa normalnie na początku każdej szeregu czasowego i opracowuje błąd w pewnym momencie podczas serii. W zestawie szkoleniowym rozmiar błędu wzrasta do momentu awarii systemu. W zestawie testów cykl czasowy kończą się trochę czasu przed awarią systemu. Celem konkursu jest przewidywanie liczby pozostałych cykli operacyjnych przed awarią w zestawie testów, tj. liczbą cykli operacyjnych po ostatnim cyklu, że aparat będzie kontynuował działanie. Zapewniono również wektor wartości pozostałych okresów istnienia (pozostałego czasu eksploatacji) dla danych testowych.*

Ponieważ dane zostały opublikowane na potrzeby konkursu, kilka metod pochodnych modeli uczenia maszynowego została niezależnie opublikowana. Okazało się, że badanie przykładów jest pomocne w zrozumieniu procesu i przyczyn związanych z tworzeniem określonego modelu uczenia maszynowego. Zobacz na przykład:

[Model przewidywania niepowodzeń aparatu samolotu](https://github.com/jancervenka/turbofan_failure) przez jancervenka użytkownika serwisu GitHub.

[Obniżenie wydajności aparatu TurboFan](https://github.com/hankroark/Turbofan-Engine-Degradation) przez hankroark użytkownika usługi GitHub.

## <a name="process"></a>Proces

Na poniższej ilustracji przedstawiono kroki, które należy wykonać w tym samouczku:

![Diagram architektury dla kroków procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Zbierz dane szkoleniowe**: proces rozpoczyna się od zebrania danych szkoleniowych. W niektórych przypadkach dane zostały już zebrane i są dostępne w bazie danych lub w postaci plików danych. W innych przypadkach, szczególnie w przypadku scenariuszy IoT, dane muszą być zbierane z urządzeń IoT i czujników i przechowywane w chmurze.

   Przyjęto założenie, że nie masz kolekcji aparatów TurboFan, więc pliki projektu zawierają prosty symulator urządzenia, który wysyła dane urządzenia NASA do chmury.

1. **Przygotuj dane**. W większości przypadków dane pierwotne zebrane z urządzeń i czujników będą wymagały przygotowania do uczenia maszynowego. Ten krok może wiązać się z czyszczeniem danych, ponownym formatowaniem i przetwarzaniem danych w celu dodania dodatkowych informacji uczenia maszynowego.

   W przypadku naszych danych maszynowych z aparatu samolotowego Przygotowywanie danych obejmuje obliczanie jawnego czasu oczekiwania na awarie dla każdego punktu danych w próbce w oparciu o rzeczywiste obserwacje danych. Te informacje umożliwiają algorytmowi uczenie maszynowego znalezienie korelacji między rzeczywistymi wzorcami danych i oczekiwanym czasem trwania silnika. Ten krok jest wysoce specyficzny dla domeny.

1. **Utwórz model uczenia maszynowego**. Na podstawie przygotowanych danych możemy teraz eksperymentować z różnymi algorytmami uczenia maszynowego i parameterizations, aby szkolić modele i porównać wyniki ze sobą.

   W tym przypadku do testowania porównamy przewidywany wynik obliczony przez model z rzeczywistym wynikiem zaobserwowanym na zestawie silników. W Azure Machine Learning można zarządzać różnymi iteracjami modeli tworzonych w rejestrze modelu.

1. **Wdróż model**. Gdy mamy model, który spełnia nasze kryteria sukcesu, można przejść do wdrożenia. Obejmuje to pakowanie modelu do aplikacji usługi sieci Web, która może być podawana z danymi przy użyciu wywołań REST i wyników analizy zwrotnej. Aplikacja usługi sieci Web jest następnie spakowana w kontener platformy Docker, który z kolei można wdrożyć w chmurze lub jako moduł IoT Edge. W tym przykładzie koncentrujemy się na rozmieszczeniu do IoT Edge.

1. **Zachowaj i Uściślij model**. Nasza służbowa nie jest wykonywana po wdrożeniu modelu. W wielu przypadkach chcemy kontynuować zbieranie danych i okresowe przekazywanie tych danych do chmury. Następnie możemy używać tych danych do ponownego uczenia i udoskonalania modelu, który następnie będzie można wdrożyć ponownie w IoT Edge.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

Ten samouczek jest podzielony na następujące sekcje:

1. Skonfiguruj swoje maszyny deweloperskie i usługi platformy Azure.
2. Generuj dane szkoleniowe dotyczące modułu uczenia maszynowego.
3. Uczenie i wdrażanie modułu uczenia maszynowego.
4. Skonfiguruj urządzenie IoT Edge jako niejawną bramę.
5. Tworzenie i wdrażanie modułów IoT Edge.
6. Wyślij dane do urządzenia IoT Edgeowego.

Przejdź do następnego artykułu, aby skonfigurować maszynę deweloperskią i udostępnić zasoby platformy Azure.

> [!div class="nextstepaction"]
> [Skonfiguruj środowisko do uczenia maszynowego na IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
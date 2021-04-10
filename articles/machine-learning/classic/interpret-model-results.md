---
title: 'ML Studio (klasyczny): Interpretuj wyniki modelu — Azure'
description: Jak wybrać optymalny zestaw parametrów dla algorytmu przy użyciu i wizualizować dane wyjściowe modelu wynikowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100516128"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretowanie wyników modelu w Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Jest to znacznik wyboru, co oznacza, że ten artykuł ma zastosowanie do Machine Learning Studio (klasyczne). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ to jest X, co oznacza, że ten artykuł ma zastosowanie do Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

W tym temacie wyjaśniono, jak wizualizować i interpretować wyniki prognozowania w Azure Machine Learning Studio (klasyczny). Po przeprowadzeniu szkolenia modelu i zakończeniu przewidywania względem niego ("Ocena modelu") należy zrozumieć i zinterpretować wynik przewidywania.

Istnieją cztery główne rodzaje modeli uczenia maszynowego w Azure Machine Learning Studio (klasyczny):

* Klasyfikacja
* Klastrowanie
* Regresja
* Systemy zalecające

Moduły używane do przewidywania w oparciu o te modele są następujące:

* Moduł [modelu oceny][score-model] dla klasyfikacji i regresji
* [Przypisywanie do modułu klastrów][assign-to-clusters] na potrzeby klastrowania
* Zalecenie [Matchbox oceny][score-matchbox-recommender] dla systemów rekomendacji

Dowiedz się, jak [wybierać parametry w celu optymalizacji algorytmów w ml Studio (klasyczny)](algorithm-parameters-optimize.md).

Aby dowiedzieć się, jak oszacować modele, zobacz [jak oszacować wydajność modelu](evaluate-model-performance.md).

Jeśli jesteś nowym do ML Studio (klasyczny), [Dowiedz się, jak utworzyć prosty eksperyment](create-experiment.md).

## <a name="classification"></a>Klasyfikacja
Istnieją dwie podkategorie problemów klasyfikacji:

* Problemy z tylko dwiema klasami (Klasyfikacja dwuklasowa lub binarna)
* Problemy z więcej niż dwoma klasami (Klasyfikacja wieloklasowa)

Azure Machine Learning Studio (klasyczny) zawiera różne moduły do obsłużenia z każdym z tych typów klasyfikacji, ale metody interpretacji ich wyników przewidywania są podobne.

### <a name="two-class-classification"></a>Klasyfikacja dwuklasowa
**Przykładowy eksperyment**

Przykładem problemu klasyfikacji z dwoma klasami jest klasyfikacja dla kwiatów Iris. Zadaniem jest klasyfikowanie kwiatów w formacie Iris na podstawie ich funkcji. Zestaw danych Iris podany w Azure Machine Learning Studio (klasyczny) to podzestaw popularnego [zestawu danych Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) zawierający wystąpienia tylko dwóch gatunków kwiatowych (klasy 0 i 1). Każdy kwiat ma cztery funkcje (długość słupka, Szerokość słupka, Długość płatka i szerokość).

![Zrzut ekranu eksperymentu Iris](./media/interpret-model-results/1.png)

Rysunek 1. Eksperyment problemu klasyfikacji dwóch klas Iris

Wykonano eksperyment w celu rozwiązania tego problemu, jak pokazano na rysunku 1. Model drzewa decyzyjnej z dwoma klasami został przeszkolony i oceniony. Teraz można wizualizować wyniki prognozowania w module [modelu oceny][score-model] , klikając port wyjściowy modułu [oceny][score-model] , a następnie klikając polecenie **Wizualizuj**.

![Moduł modelu oceny](./media/interpret-model-results/1_1.png)

Spowoduje to wyświetlenie wyników oceniania, jak pokazano na rysunku 2.

![Wyniki eksperymentu klasyfikacji dwóch klas z przesłoną](./media/interpret-model-results/2.png)

Rysunek 2. Wizualizowanie modelu wyników w klasyfikacji dwuklasowej

**Interpretacja wyniku**

W tabeli wyników znajduje się sześć kolumn. W lewej czwartej kolumnie znajdują się cztery funkcje. Wyniki prognozowania to dwie kolumny, oceniane etykiety i oceny prawdopodobieństwa. Kolumna oceny prawdopodobieństwa wskazuje prawdopodobieństwo, że kwiat należy do klasy dodatniej (Klasa 1). Na przykład Pierwsza liczba w kolumnie (0,028571) oznacza, że występuje 0,028571 prawdopodobieństwo, że pierwszy kwiat należy do klasy 1. Kolumna etykiety z wynikami pokazuje przewidywaną klasę dla każdego kwiatu. Jest to uzależnione od kolumny wynikowe wyniki. Jeśli wynikowe prawdopodobieństwo kwitnienia jest większe niż 0,5, jest przewidywane jako Klasa 1. W przeciwnym razie jest przewidywana jako klasa 0.

**Publikacja usługi sieci Web**

Po zrozumieniu i ocenie wyników przewidywania eksperyment może być opublikowany jako usługa sieci Web, dzięki czemu można wdrożyć go w różnych aplikacjach i wywołać go w celu uzyskania prognoz klasy dla każdego nowego kwiatu. Aby dowiedzieć się, jak zmienić eksperyment szkoleniowy na eksperyment oceniania i opublikować go jako usługę sieci Web, zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md). Ta procedura umożliwia przeprowadzenie eksperymentu oceniania, jak pokazano na rysunku 3.

![Zrzut ekranu eksperymentu oceniania](./media/interpret-model-results/3.png)

Rysunek 3. Ocenianie eksperymentu problemu klasyfikacji dwóch klas w tęczówki

Teraz musisz ustawić dane wejściowe i wyjściowe dla usługi sieci Web. Dane wejściowe to właściwy port wejściowy [modelu oceny][score-model], czyli dane wejściowe funkcji kwitnienia Iris. Wybór danych wyjściowych zależy od tego, czy interesuje Cię Klasa predykcyjna (etykieta z oceną), prawdopodobieństwo oceny lub oba te elementy. W tym przykładzie zakłada się, że interesują Cię oba elementy. Aby wybrać żądane kolumny wyjściowe, użyj [opcji wybierz kolumny w module zestawu danych][select-columns] . Kliknij pozycję [Wybierz kolumny w zestawie danych][select-columns], kliknij pozycję **Uruchom selektor kolumny** i wybierz pozycję **etykiety z oceną** i **wyniki oceny**. Po ustawieniu portu wyjściowego [wybranych kolumn w zestawie danych][select-columns] i jego ponownym uruchomieniu należy rozpocząć publikowanie eksperymentu oceniania jako usługi sieci Web, klikając pozycję **Publikuj usługę sieci Web**. Końcowy eksperyment wygląda jak rysunek 4.

![Eksperyment klasyfikacji dwóch klas Iris](./media/interpret-model-results/4.png)

Rysunek 4. Końcowy eksperyment oceniania problemu klasyfikacji dwóch klas w ramach przesłony

Po uruchomieniu usługi sieci Web i wprowadzeniu niektórych wartości funkcji wystąpienia testowego wynik zwróci dwie liczby. Pierwsza liczba to etykieta z wynikami, a druga to prawdopodobieństwo wynikowe. Ten kwiat jest przewidywany jako Klasa 1 z prawdopodobieństwem 0,9655.

![Testowanie interpretacji modelu wynikowego](./media/interpret-model-results/4_1.png)

![Wyniki testu oceniania](./media/interpret-model-results/5.png)

Rysunek 5. Wynik usługi sieci Web dla dwóch klas klasyfikacji Iris

### <a name="multi-class-classification"></a>Klasyfikacja wieloklasowa
**Przykładowy eksperyment**

W tym doświadczeniu wykonujesz zadanie rozpoznawania liter jako przykład klasyfikacji wieloklasowej. Klasyfikator próbuje przewidzieć określoną literę% 28class %29 na podstawie niektórych wartości atrybutów ręcznych wyodrębnionych z obrazów ręcznych.

![Przykład rozpoznawania litery](./media/interpret-model-results/5_1.png)

W danych szkoleniowych są wyekstrahowane 16 funkcji z obrazów list odręcznych. 26 listów ma postać 26 klas. Rysunek 6 przedstawia eksperyment, który będzie szkolić model klasyfikacji wieloklasowej do rozpoznawania liter i przewidywania na tym samym zestawie funkcji w zestawie danych testowych.

![Rozpoznawanie liter w klasyfikacji wieloklasowej](./media/interpret-model-results/6.png)

Rysunek 6. Eksperyment problemu klasyfikacji wieloklasowego rozpoznawania litery

Wizualizowanie wyników modułu [modelu wynikowego][score-model] przez kliknięcie portu wyjściowego modułu [oceny wyniku][score-model] , a następnie kliknięcie **wizualizacji**, powinna zostać wyświetlona zawartość, jak pokazano na rysunku 7.

![Wyniki modelu wyników](./media/interpret-model-results/7.png)

Rysunek 7. Wizualizuj model wyników w klasyfikacji wieloklasowej

**Interpretacja wyniku**

16 lewej kolumny reprezentuje wartości funkcji zestawu testów. Kolumny z nazwami, takimi jak oceny prawdopodobieństwa dla klasy "XX", są podobne do kolumny "Punktacja" prawdopodobieństwa w przypadku obu klas. Pokazują prawdopodobieństwo, że odpowiadający wpis znajduje się w określonej klasie. Na przykład dla pierwszego wpisu występuje 0,003571 prawdopodobieństwo, że jest to "A" 0,000451 prawdopodobieństwa, że jest "B" i tak dalej. Ostatnia kolumna (etykiety wynikowe) jest taka sama jak etykieta z wynikami w przypadku dwóch klas. Wybiera klasę z największym prawdopodobieństwem wynikowym jako przewidywalną klasę odpowiadającego wpisu. Na przykład w przypadku pierwszego wpisu etykieta z oceną ma wartość "F", ponieważ ma największe prawdopodobieństwo to "F" (0,916995).

**Publikacja usługi sieci Web**

Możesz również uzyskać etykietę oceny dla każdego wpisu i prawdopodobieństwo wystąpienia etykiety z wynikami. Podstawowa logika polega na znalezieniu największego prawdopodobieństwa wśród wszystkich punktacji wynikowych. W tym celu należy użyć modułu [skryptu Execute języka R][execute-r-script] . Kod R jest przedstawiony na rysunku 8, a wynik eksperymentu przedstawiono na rysunku 9.

![Przykład kodu w języku R](./media/interpret-model-results/8.png)

Rysunek 8. Kod języka R służący do wyodrębniania etykiet z wynikami i skojarzonych prawdopodobieństwa etykiet

![Wynik eksperymentu](./media/interpret-model-results/9.png)

Rysunek 9. Końcowy eksperyment oceniania problemu klasyfikacji wieloklasowej w ramach rozpoznawania liter

Po opublikowaniu i uruchomieniu usługi sieci Web, a następnie wprowadzeniu niektórych wartości funkcji wejściowych, zwracany wynik wygląda jak rysunek 10. Ta litera z listą odręczną i jej wyodrębnioną 16 funkcjami jest przewidywana jako "T" z 0,9715 prawdopodobieństwem.

![Test interpretacji modułu oceny](./media/interpret-model-results/9_1.png)

![Wynik testu](./media/interpret-model-results/10.png)

Rysunek 10. Wynik usługi sieci Web klasyfikacji wieloklasowej

## <a name="regression"></a>Regresja
Problemy z regresją różnią się od błędów klasyfikacji. W przypadku problemu klasyfikacji próbujesz przewidzieć klasy dyskretne, takie jak Klasa, do której należy kwiat. Ale jak widać w poniższym przykładzie problemu z regresją, próbujesz przewidzieć zmienną ciągłą, taką jak cena samochodu.

**Przykładowy eksperyment**

Użyj prognozowania cen samochodów jako przykładu do regresji. Próbujesz przewidzieć cenę samochodu w oparciu o jego funkcje, takie jak marka, typ paliwa, typ treści i koło napędów. Eksperyment jest przedstawiony na rysunku 11.

![Testowanie regresji cen samochodów](./media/interpret-model-results/11.png)

Rysunek 11. Eksperyment dotyczący problemu z regresją cen samochodów

Wizualizowanie modułu [modelu oceny][score-model] , wynik wygląda jak rysunek 12.

![Wyniki oceniania problemu dotyczącego prognozowania cen samochodów](./media/interpret-model-results/12.png)

Rysunek 12. Wynik oceny problemu dotyczącego prognozowania cen samochodów

**Interpretacja wyniku**

Etykiety oceniające są kolumnami wynikowymi w tym wyniku oceniania. Liczby to Przewidywana cena dla każdego samochodu.

**Publikacja usługi sieci Web**

Możesz publikować eksperyment regresji w usłudze sieci Web i wywoływać ją do prognozowania cen samochodów w taki sam sposób jak w przypadku użycia klasyfikacji dwuklasowej.

![Eksperyment oceniający dla problemu z regresją cen samochodów](./media/interpret-model-results/13.png)

Rysunek 13. Ocenianie eksperymentu problemu z regresją cen samochodów

Po uruchomieniu usługi sieci Web zwrócony wynik wygląda jak rysunek 14. Przewidywana cena dla tego samochodu to $15 085,52.

![Test interpretacji modułu oceniania](./media/interpret-model-results/13_1.png)

![Wyniki modułu oceniania](./media/interpret-model-results/14.png)

Rysunek 14. Wynik usługi sieci Web problemu z metodą regresji cen dla samochodów

## <a name="clustering"></a>Klastrowanie
**Przykładowy eksperyment**

Użyjmy ponownie zestawu danych Iris, aby skompilować eksperyment z klastrem. W tym miejscu możesz odfiltrować etykiety klas w zestawie danych, aby miało tylko funkcje i mogą być używane do klastrowania. W tym przypadku użycia tęczówki należy określić liczbę klastrów, które mają być dwa w procesie szkoleniowym, co oznacza, że można umieścić kwiaty w dwóch klasach. Eksperyment jest przedstawiony na rysunku 15.

![Eksperyment dotyczący problemu z klastrowaniem tęczówki](./media/interpret-model-results/15.png)

Rysunek 15. Eksperyment dotyczący problemu z klastrowaniem tęczówki

Klastrowanie różni się od klasyfikacji w tym, że zestaw danych szkoleniowych nie ma samych etykiet w rzeczywistości. Klastrowanie grupuje wystąpienia zestawu danych szkoleniowych do odrębnych klastrów. Podczas procesu szkolenia model etykietuje wpisy, poznanie różnic między ich funkcjami. Następnie przeszkolony model może służyć do dalszego klasyfikowania przyszłych wpisów. Istnieją dwie części wyniku problemu z klastrem. Pierwsza część ma etykietę zestawu danych szkoleniowych, a druga — klasyfikowanie nowego zestawu danych przy użyciu nauczonego modelu.

Pierwsza część wyniku może być wizualizacją, klikając lewy port wyjściowy [modelu klastra uczenia][train-clustering-model] , a następnie klikając polecenie **Wizualizuj**. Wizualizacja jest pokazana na rysunku 16.

![Wynik klastrowania](./media/interpret-model-results/16.png)

Rysunek 16. Wizualizuj wyniki klastrowania dla zestawu danych szkoleniowych

W wyniku drugiej części klaster nowych wpisów z przeszkolonym modelem klastrowania przedstawiono na rysunku 17.

![Wizualizuj wynik klastrowania](./media/interpret-model-results/17.png)

Rysunek 17. Wizualizuj wynik klastrowania na nowym zestawie danych

**Interpretacja wyniku**

Chociaż wyniki dwóch części pochodzą z różnych etapów eksperymentów, wyglądają one tak samo i są interpretowane w taki sam sposób. Pierwsze cztery kolumny są funkcjami. Ostatnią kolumną jest wynik przewidywania. Wpisy przypisane do tego samego numeru są przewidywane jako należące do tego samego klastra, co oznacza, że współdzielą się one w jakiś sposób (w przypadku tego eksperymentu używana jest domyślna Metryka odległości Euclidean). Ponieważ określono liczbę klastrów do 2, wpisy w przypisaniach są oznaczone jako 0 lub 1.

**Publikacja usługi sieci Web**

Eksperyment klastrów można opublikować w usłudze sieci Web i wywołać go w celu przewidywania klastrów w taki sam sposób jak w przypadku użycia klasyfikacji dwuklasowej.

![Eksperyment oceniający dla problemu z klastrowaniem tęczówki](./media/interpret-model-results/18.png)

Rysunek 18. Ocenianie eksperymentu problemu z klastrowaniem tęczówki

Po uruchomieniu usługi sieci Web zwrócony wynik wygląda jak rysunek 19. Ten kwiat jest przewidywany w klastrze 0.

![Test interpretacji modułu oceniania](./media/interpret-model-results/18_1.png)

![Wynik modułu oceniania](./media/interpret-model-results/19.png)

Rysunek 19. Wynik usługi sieci Web dla dwóch klas klasyfikacji Iris

## <a name="recommender-system"></a>System polecania
**Przykładowy eksperyment**

W przypadku systemów zalecanych można użyć problemu z zaleceniem restauracji jako przykładu: można polecać restauracje dla klientów w oparciu o swoją historię klasyfikacji. Dane wejściowe składają się z trzech części:

* Klasyfikacje restauracji od klientów
* Dane funkcji klienta
* Dane funkcji restauracji

Istnieje kilka rzeczy, które można wykonać za pomocą modułu [polecania szkolenia Matchbox][train-matchbox-recommender] w Azure Machine Learning Studio (klasyczny):

* Prognozowanie klasyfikacji dla danego użytkownika i elementu
* Zalecane elementy dla danego użytkownika
* Znajdź użytkowników związanych z danym użytkownikiem
* Znajdź elementy dotyczące danego elementu

Możesz wybrać, co chcesz zrobić, wybierając z czterech opcji w menu **rodzaj prognozcy polecania** . W tym miejscu możesz zapoznać się ze wszystkimi czterema scenariuszami.

![Matchbox](./media/interpret-model-results/19_1.png)

Typowym eksperymentem Azure Machine Learning Studio (klasycznym) dla systemu zalecanym jest wygląd rysunek 20. Aby uzyskać informacje o sposobach korzystania z tych zalecanych modułów systemowych, zobacz polecający [szkolenia Matchbox][train-matchbox-recommender] i [Ocena Matchbox][score-matchbox-recommender].

![Zalecany eksperyment systemu](./media/interpret-model-results/20.png)

Rysunek 20. Zalecany eksperyment systemu

**Interpretacja wyniku**

**Prognozowanie klasyfikacji dla danego użytkownika i elementu**

Wybierając opcję **prognozowanie klasyfikacji** w obszarze **przewidywanie**, należy zadać systemowi zalecanym do przewidywania oceny danego użytkownika i elementu. Wizualizacja danych wyjściowych [Matchbox oceny wyniku][score-matchbox-recommender] jest następująca, jak rysunek 21.

![Wynik wyniku proponowanego systemu — przewidywanie oceny](./media/interpret-model-results/21.png)

Rysunek 21. Wizualizuj wynik wyniku proponowanego systemu — przewidywanie oceny

Pierwsze dwie kolumny to pary elementu User-Item dostarczone przez dane wejściowe. Trzecia kolumna jest przewidywaną klasyfikacją użytkownika dla określonego elementu. Na przykład w pierwszym wierszu U1048 klienta jest przewidywany do oceniania restauracji 135026 jako 2.

**Zalecane elementy dla danego użytkownika**

Wybierając pozycję **element rekomendacja** w obszarze **predykcyjny zalecenia**, prosisz o system polecający, aby zalecać elementy dla danego użytkownika. Ostatni parametr, który należy wybrać w tym scenariuszu, jest *zalecanym wyborem elementu*. Opcja **z elementów ocenianych (na potrzeby oceny modelu)** służy głównie do oceny modelu podczas procesu szkolenia. Na potrzeby tego etapu przewidywania wybieramy **spośród wszystkich elementów**. Wizualizacja danych wyjściowych [Matchbox oceny wyniku][score-matchbox-recommender] jest następująca, jak rysunek 22.

![Wynik wyniku zalecanego systemu — zalecenie dotyczące elementu](./media/interpret-model-results/22.png)

Rysunek 22. Wizualizuj wynik wyniku systemu zalecanego — zalecenie dotyczące elementu

Pierwsza z sześciu kolumn reprezentuje podane identyfikatory użytkowników, aby zalecać elementy dla, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn reprezentuje elementy zalecane dla użytkownika w kolejności malejącej. Na przykład w pierwszym rzędzie najbardziej zalecaną restauracji dla klienta U1048 jest 134986, a następnie 135018, 134975, 135021 i 132862.

**Znajdź użytkowników związanych z danym użytkownikiem**

Wybierając **odpowiednich użytkowników** w obszarze **predykcyjny polecający**, użytkownik jest proszony o wyszukanie pokrewnych użytkowników dla danego użytkownika. Pokrewni użytkownicy są użytkownikami, którzy mają podobne preferencje. Ostatnim parametrem wybieranym w tym scenariuszu jest *wybór związany z użytkownikiem*. Opcja **od użytkowników, którzy oceniali elementy (na potrzeby oceny modelu)** , jest przede wszystkim do oceny modelu w trakcie procesu szkoleniowego. Wybierz **spośród wszystkich użytkowników** tego etapu przewidywania. Wizualizacja danych wyjściowych [Matchbox oceny wyniku][score-matchbox-recommender] jest następująca, jak rysunek 23.

![Wynik oceny zalecanych użytkowników systemu](./media/interpret-model-results/23.png)

Rysunek 23. Wizualizuj wyniki oceny zalecanych użytkowników systemu

W pierwszej z sześciu kolumn są wyświetlane podane identyfikatory użytkowników, które są konieczne do znalezienia powiązanych użytkowników, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn przechowuje przewidywanych użytkowników związanych z przeznaczeniem w kolejności malejącej. Na przykład w pierwszym wierszu najbardziej odpowiednim klientem dla klienta U1048 jest U1051, a następnie U1066, U1044, U1017 i U1072.

**Znajdź elementy dotyczące danego elementu**

Wybierając **powiązane elementy** w obszarze **predykcyjny polecający**, użytkownik jest proszony o wyszukanie pokrewnych elementów dla danego elementu. Elementy pokrewne to elementy, które najprawdopodobniej będą odnosi się do tego samego użytkownika. Ostatnim parametrem, który należy wybrać w tym scenariuszu, jest *wybór powiązanego elementu*. Opcja **z elementów ocenianych (na potrzeby oceny modelu)** służy głównie do oceny modelu podczas procesu szkolenia. Wybieramy **ze wszystkich elementów** dla tego etapu przewidywania. Wizualizacja danych wyjściowych [Matchbox oceny wyniku][score-matchbox-recommender] jest następująca, jak rysunek 24.

![Wynik oceny zalecanych elementów systemu](./media/interpret-model-results/24.png)

Rysunek 24. Wizualizowanie wyników dla polecanych elementów systemu

Pierwsza z sześciu kolumn reprezentuje podane identyfikatory elementów, które są konieczne do znalezienia powiązanych elementów, zgodnie z danymi wejściowymi. Pozostałe pięć kolumn przechowuje przewidywane elementy powiązane elementu w kolejności malejącej w zakresie istotności. Na przykład w pierwszym wierszu najbardziej odpowiedni element dla elementu 135026 to 135074, po którym następują 135035, 132875, 135055 i 134992.

**Publikacja usługi sieci Web**

Proces publikowania tych eksperymentów jako usług sieci Web w celu uzyskania prognoz jest podobny dla każdego z czterech scenariuszy. W tym miejscu zajmiemy się drugim scenariuszem (zalecane elementy dla danego użytkownika). Tę samą procedurę można wykonać z innymi trzema.

Zapisanie przeszkolonego systemu zalecanego jako modelu przeszkolonego i filtrowanie danych wejściowych do kolumny identyfikatora pojedynczego użytkownika zgodnie z żądaniem, można podłączyć eksperyment jak na rysunku 25 i opublikować go jako usługę sieci Web.

![Ocenianie eksperymentu problemu z zaleceniem restauracji](./media/interpret-model-results/25.png)

Rysunek 25. Ocenianie eksperymentu problemu z zaleceniem restauracji

Po uruchomieniu usługi sieci Web zwrócony wynik wygląda jak rysunek 26. Pięć zalecanych dla użytkowników U1048 to 134986, 135018, 134975, 135021 i 132862.

![Przykład usługi systemowej zalecenia](./media/interpret-model-results/25_1.png)

![Przykładowe wyniki eksperymentu](./media/interpret-model-results/26.png)

Rysunek 26. Problem z usługą sieci Web z zaleceniem zalecenia dotyczącego restauracji

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender
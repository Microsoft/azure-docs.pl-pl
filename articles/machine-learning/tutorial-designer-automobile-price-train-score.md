---
title: 'Samouczek: prognozowanie cen samochodu przy użyciu narzędzia Projektant'
titleSuffix: Azure Machine Learning
description: Uczenie modelu uczenia maszynowego do przewidywania cen samochodów przy użyciu regresji liniowej. Ten samouczek jest pierwszą częścią dwuczęściowej serii.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: ca812fc7548e3c70f1faa1e1ed6a34afda3872af
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575979"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Samouczek: Przewidywanie ceny samochodów w projektancie


W tym dwuczęściowym samouczku dowiesz się, jak używać projektanta Azure Machine Learning do uczenia i wdrażania modelu uczenia maszynowego, który przewiduje cenę dowolnego samochodu. Projektant jest narzędziem typu "przeciągnij i upuść", które pozwala tworzyć modele uczenia maszynowego bez pojedynczego wiersza kodu.

W pierwszej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz nowy potok.
> * Importuj dane.
> * Przygotuj dane.
> * Uczenie modelu uczenia maszynowego.
> * Oceń model uczenia maszynowego.

W [drugiej części](tutorial-designer-automobile-price-deploy.md) tego samouczka wdrożono model jako punkt końcowy inferencing w czasie rzeczywistym, aby przewidzieć cenę dowolnego samochodu w oparciu o specyfikacje techniczne, które należy wysłać. 

> [!NOTE]
>Kompletna wersja tego samouczka jest dostępna jako potoku przykładowe.
>
>Aby go znaleźć, przejdź do projektanta w obszarze roboczym. W sekcji **nowe potoku** wybierz pozycję **przykład 1-regresja: Automobile — Prognoza cenowa (podstawowa)**.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

Potoki Azure Machine Learning organizują wiele etapów uczenia maszynowego i przetwarzania danych w ramach jednego zasobu. Potoki pozwalają organizować i ponownie używać złożonych przepływów pracy uczenia maszynowego między projektami i użytkownikami.

Do utworzenia potoku Azure Machine Learning jest wymagany obszar roboczy Azure Machine Learning. W tej sekcji dowiesz się, jak utworzyć oba te zasoby.

### <a name="create-a-new-workspace"></a>Tworzenie nowego obszaru roboczego

Do korzystania z projektanta potrzebny jest obszar roboczy Azure Machine Learning. Obszar roboczy jest zasobem najwyższego poziomu dla Azure Machine Learning, stanowi scentralizowane miejsce do pracy ze wszystkimi artefaktami tworzonymi w Azure Machine Learning. Aby uzyskać instrukcje dotyczące tworzenia obszaru roboczego, zobacz temat [Tworzenie obszarów roboczych Azure Machine Learning i zarządzanie nimi](how-to-manage-workspace.md).

> [!NOTE]
> Jeśli obszar roboczy korzysta z sieci wirtualnej, istnieją dodatkowe czynności konfiguracyjne, które należy wykonać, aby użyć projektanta. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure](how-to-enable-studio-virtual-network.md)

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. Zaloguj się do <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz pozycję **Projektant**.

    ![Zrzut ekranu przedstawiający, jak uzyskać dostęp do projektanta](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Wybierz **łatwe w użyciu wstępnie skompilowane moduły**.

1. W górnej części kanwy wybierz domyślną potoku Nazwa potoku **— utworzony**. Zmień jej nazwę na funkcja *prognozowanie cen na urządzeniu mobilnym*. Nazwa nie musi być unikatowa.

## <a name="set-the-default-compute-target"></a>Ustaw domyślny cel obliczeń

Potok jest uruchamiany w obiekcie docelowym obliczeń, który jest zasobem obliczeniowym dołączonym do obszaru roboczego. Po utworzeniu obiektu docelowego obliczeń można użyć go ponownie do przyszłych przebiegów.

Można ustawić **domyślny obiekt docelowy obliczeń** dla całego potoku, co spowoduje, że każdy moduł domyślnie użyje tego samego obiektu docelowego obliczeń. Można jednak określić cele obliczeń dla poszczególnych modułów.

1. Obok nazwy potoku wybierz zrzut ekranu ikony **koła** ![ zębatego ](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) w górnej części kanwy, aby otworzyć okienko **Ustawienia** .

1. W okienku **Ustawienia** z prawej strony kanwy wybierz pozycję **Wybierz element docelowy obliczeń**.

    Jeśli masz już dostępny element docelowy obliczeń, możesz wybrać go do uruchomienia tego potoku.

    > [!NOTE]
    > Projektant może uruchamiać eksperymenty szkoleniowe dotyczące Azure Machine Learning obliczeń, ale inne elementy docelowe obliczeń nie będą wyświetlane.

1. Wprowadź nazwę zasobu obliczeniowego.

1. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego trwa około 5 minut. Po utworzeniu zasobu można go ponownie wykorzystać i pominąć ten czas oczekiwania na przyszłe uruchomienia.
    >
    > Zasób obliczeniowy jest automatycznie skalowany na zero węzłów, gdy jest bezczynny, aby zaoszczędzić koszt. Gdy używasz go ponownie po opóźnieniu, może wystąpić około pięć minut czasu oczekiwania podczas skalowania kopii zapasowej.

## <a name="import-data"></a>Importowanie danych

Projektant zawiera kilka przykładowych zestawów danych, z którymi można eksperymentować. Na potrzeby tego samouczka Użyj **danych cen samochodów (RAW)**. 

1. Na lewo od kanwy potoku jest paletą zestawów danych i modułów. Wybierz **przykładowe zestawy danych** , aby wyświetlić dostępne przykładowe zestawy danych.

1. Wybierz pozycję zestaw **danych cena samochodów (RAW)** i przeciągnij ją na kanwę.

   ![Przeciągnij dane do kanwy](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Wizualizacja danych

Możesz wizualizować dane, aby zrozumieć zestaw danych, który będzie używany.

1. Kliknij prawym przyciskiem myszy **dane cen samochodów (RAW)** i wybierz polecenie **Wizualizuj**.

1. Wybierz różne kolumny w oknie dane, aby wyświetlić informacje o każdej z nich.

    Każdy wiersz reprezentuje samochód, a zmienne skojarzone z poszczególnymi urządzeniami przenośnymi są wyświetlane jako kolumny. Ten zestaw danych zawiera 205 wierszy i 26 kolumn.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają pewnego przetworzenia przed analizą. Podczas inspekcji zestawu danych mogą znajdować się pewne brakujące wartości. Te brakujące wartości muszą zostać oczyszczone, aby model mógł prawidłowo analizować dane.

### <a name="remove-a-column"></a>Usuwanie kolumny

Podczas uczenia modelu trzeba wykonać coś dotyczące brakujących danych. W tym zestawie danych w kolumnie **znormalizowana strata** brakuje wielu wartości, więc ta kolumna nie zostanie całkowicie wykluczona z modelu.

1. W palecie modułów z lewej strony kanwy rozwiń sekcję **Przekształcanie danych** i Znajdź **pozycję Wybieranie kolumn w zestawie danych** .

1. Przeciągnij moduł **Wybierz kolumny w zestawie danych** na kanwę. Upuść moduł poniżej modułu DataSet.

1. Połącz zestaw danych **cen samochodów (RAW)** z modułem **Wybieranie kolumn w zestawie danych** . Przeciągnij z portu wyjściowego zestawu danych, czyli małego okręgu w dolnej części zestawu danych na kanwie, do portu wejściowego **SELECT kolumn w zestawie danych**, czyli małego okręgu w górnej części modułu.

    > [!TIP]
    > Przepływ danych można utworzyć za pomocą potoku po podłączeniu portu wyjściowego jednego modułu do portu wejściowego innego.
    >

    ![Połącz moduły](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję **Edytuj kolumnę**.

1. Rozwiń listę rozwijaną **nazwy kolumn** obok pozycji **Dołącz**, a następnie wybierz pozycję  **wszystkie kolumny**.

1. Wybierz opcję, **+** Aby dodać nową regułę.

1. Z menu rozwijanego wybierz opcję **Wyklucz** i **nazwy kolumn**.
    
1. Wprowadź *znormalizowane straty* w polu tekstowym.

1. W prawym dolnym rogu wybierz pozycję **Zapisz** , aby zamknąć selektor kolumny.

    ![Wykluczanie kolumny](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** . 

1. W okienku Szczegóły modułu z prawej strony kanwy zaznacz pole tekstowe **komentarz** i wprowadź *wykluczenie normalnych strat*.

    Komentarze będą wyświetlane na grafie, aby ułatwić organizowanie potoku.

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Zestaw danych nadal ma brakujące wartości po usunięciu kolumny **znormalizowanych strat** . Pozostałe brakujące dane można usunąć przy użyciu modułu **czyste brakujące dane** .

> [!TIP]
> Czyszczenie brakujących wartości z danych wejściowych jest wymaganiem wstępnym w przypadku korzystania z większości modułów w projektancie.

1. W palecie modułów z lewej strony kanwy rozwiń sekcję **transformacja danych** i Znajdź **czysty brakujący moduł danych** .

1. Przeciągnij **nieczysty moduł danych** do kanwy potoku. Połącz go z modułem **Wybieranie kolumn w zestawie danych** . 

1. Wybierz **czysty moduł danych** .

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję **Edytuj kolumnę**.

1. W wyświetlonym oknie **kolumny do oczyszczenia** rozwiń menu rozwijane obok pozycji **Dołącz**. Zaznacz, **wszystkie kolumny**

1. Wybierz pozycję **Zapisz**

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję **Usuń cały wiersz** w obszarze **Tryb czyszczenia**.

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pole **komentarz** i wprowadź *Usuń brakujące wiersze wartości*. 

    Potok powinien teraz wyglądać następująco:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png"alt-text="Zaznacz kolumnę":::

## <a name="train-a-machine-learning-model"></a>Trenowanie modelu uczenia maszynowego

Teraz, gdy masz moduły do przetwarzania danych, możesz skonfigurować moduły szkoleniowe.

Ponieważ chcesz przewidzieć cenę, która jest liczbą, możesz użyć algorytmu regresji. W tym przykładzie używany jest model regresji liniowej.

### <a name="split-the-data"></a>Dzielenie danych

Dzielenie danych to typowe zadanie w usłudze Machine Learning. Dane zostaną podzielone na dwa oddzielne zestawy danych. Jeden zestaw danych będzie szkolić model, a drugi przetestuje, jak dobrze działa model.

1. W palecie modułów rozwiń sekcję **Przekształcanie danych** i Znajdź moduł **Split Data** .

1. Przeciągnij moduł **Split Data** na kanwę potoku.

1. Połącz lewy port modułu **czyste brakujące dane** z modułem **Split Data** .

    > [!IMPORTANT]
    > Upewnij się, że lewe porty wyjściowe **czyste brakujące dane** łączą się, aby **podzielić dane**. Lewy port zawiera oczyszczone dane. Prawidłowy port zawiera dane z przekoszykiem.

1. Wybierz moduł **Split Data** .

1. W okienku Szczegóły modułu z prawej strony kanwy Ustaw **ułamek wierszy w pierwszym zestawie danych wyjściowych** na 0,7.

    Ta opcja dzieli na 70 procent danych, aby szkolić model i 30 procent na potrzeby testowania. Zestaw danych 70 procent będzie dostępny przez lewy port wyjściowy. Pozostałe dane będą dostępne przez właściwy port wyjściowy.

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pole **komentarz** i wprowadź *Podziel zestaw danych na zestaw szkoleniowy (0,7) i zestaw testów (0,3)*.

### <a name="train-the-model"></a>Trenowanie modelu

Nauczenie modelu przez nadanie mu zestawu danych, który zawiera cenę. Algorytm tworzy model, który objaśnia relacje między funkcjami a ceną zaprezentowaną przez dane szkoleniowe.

1. W palecie modułów rozwiń węzeł **Machine Learning algorytmy**.
    
    Ta opcja umożliwia wyświetlenie kilku kategorii modułów, których można użyć do zainicjowania algorytmów uczenia.

1. Wybierz **Regression** opcję  >  **regresja liniowa** regresji i przeciągnij ją na kanwę potoku.

1. W palecie modułów rozwiń sekcję **szkolenia modułów** i przeciągnij moduł **uczenie modelu** na kanwę.

1. Połącz dane wyjściowe modułu **regresji liniowej** z lewym wejściem modułu **uczenie modelu** .

1. Połącz Wyjście danych szkoleniowych (lewy port) modułu **Split Data (podział danych** ) z prawym wejściem modułu **uczenie modelu** .
    
    > [!IMPORTANT]
    > Upewnij się, że lewe porty wyjściowe **danych dzielą** łączą się z **modelem uczenia**. Lewy port zawiera zestaw szkoleniowy. Prawidłowy port zawiera zestaw testów.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png"alt-text="Zrzut ekranu przedstawiający poprawną konfigurację modułu uczenie modelu. Moduł regresja liniowa łączy się z lewym portem modułu uczenia modelowego, a moduł podziału danych łączy się z odpowiednim portem modelu uczenia.":::

1. Wybierz moduł **Train Model** (Trenowanie modelu).

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję Edytuj selektor **kolumny** .

1. W oknie dialogowym **etykieta kolumny** rozwiń menu rozwijane i wybierz pozycję **nazwy kolumn**. 

1. W polu tekstowym wpisz Price ( *Cena* ), aby określić wartość, którą model ma przewidzieć.

    >[!IMPORTANT]
    > Upewnij się, że nazwa kolumny została dokładnie wprowadzona. Nie używaj wielkiej litery **.** 

    Potok powinien wyglądać następująco:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png"alt-text="Zrzut ekranu przedstawiający poprawną konfigurację potoku po dodaniu modułu uczenie modelu.":::

### <a name="add-the-score-model-module"></a>Dodawanie modułu modelu oceny

Po nauczeniu modelu przy użyciu 70 procent danych, można użyć go do oceny pozostałych 30 procent, aby zobaczyć, jak dobrze działa model.

1. Wprowadź ciąg " *model oceny* " w polu wyszukiwania, aby znaleźć moduł **modelu oceny** . Przeciągnij moduł do kanwy potoku. 

1. Połącz wyjście modułu **Train Model** (Uczenie modelu) z lewym portem wejściowym modułu **Score Model** (Generowanie wyników przez model). Połącz wyjście danych testowych (prawy port) modułu **Split Data** (Podział danych) z prawym portem wejściowym modułu **Score Model** (Generowanie wyników przez model).

### <a name="add-the-evaluate-model-module"></a>Dodawanie modułu Evaluate Model (Ewaluacja modelu)

Użyj modułu **oceny modelu** , aby oszacować, jak dobrze Model przedstawia test zestawu danych.

1. Wprowadź *wartość Oceń* w polu wyszukiwania, aby znaleźć moduł **Oceń model** . Przeciągnij moduł do kanwy potoku. 

1. Połącz dane wyjściowe modułu z **modelem wynikowym** z lewym wejściem do **oceny modelu**. 

    Końcowy potok powinien wyglądać następująco:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png"alt-text="Zrzut ekranu przedstawiający poprawną konfigurację potoku.":::

## <a name="submit-the-pipeline"></a>Prześlij potok

Teraz, gdy potok jest skonfigurowany, możesz przesłać uruchomienie potoku w celu uczenia modelu uczenia maszynowego. Można przesłać prawidłowy przebieg potoku w dowolnym momencie, który może służyć do przeglądania zmian w potoku podczas opracowywania.

1. W górnej części kanwy wybierz pozycję **Prześlij**.

1. W oknie dialogowym **Konfigurowanie uruchomienia potoku** wybierz pozycję **Utwórz nowy**.

    > [!NOTE]
    > Grupy eksperymentów działają podobnie. W przypadku uruchomienia potoku wiele razy można wybrać ten sam eksperyment dla kolejnych uruchomień.

    1. Wprowadź opisową nazwę dla **nowej nazwy eksperymentu**.

    1. Wybierz pozycję **Prześlij**.
    
    Możesz wyświetlić stan przebiegu i szczegóły w prawym górnym rogu kanwy.
    
    Jeśli jest to pierwsze uruchomienie, ukończenie działania potoku może potrwać do 20 minut. Domyślne ustawienia obliczeń mają minimalny rozmiar węzła równy 0, co oznacza, że projektant musi przydzielić zasoby po stanie bezczynności. Powtarzające się uruchomienia potoku będą trwać krócej od czasu przydziału zasobów obliczeniowych. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby zwiększyć wydajność.

### <a name="view-scored-labels"></a>Wyświetlanie etykiet z wynikami

Po zakończeniu przebiegu można wyświetlić wyniki uruchomienia potoku. Najpierw zapoznaj się z przewidywaniami wygenerowanymi przez model regresji.

1. Kliknij prawym przyciskiem myszy moduł **model oceny** i wybierz polecenie **Wizualizacja** , aby wyświetlić jego dane wyjściowe.

    W tym miejscu możesz zobaczyć przewidywane ceny i rzeczywiste ceny z danych testowych.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png"alt-text="Zrzut ekranu przedstawiający wizualizację danych wyjściowych z wyróżnioną kolumną etykieta z wynikami":::

### <a name="evaluate-models"></a>Oceń modele

Użyj **modelu szacowania** , aby zobaczyć, jak dobrze szkolony model jest wykonywany na testowym zestawie danych.

1. Kliknij prawym przyciskiem myszy moduł **Oceń model** i wybierz polecenie **Wizualizacja** , aby wyświetlić jego dane wyjściowe.

Następujące statystyki są wyświetlane dla modelu:

* **Średni błąd bezwzględny (Mae)**: Średnia liczba błędów bezwzględnych. Jest to różnica między wartością przewidywaną a wartością rzeczywistą.
* **Błąd średnika "pierwiastek" z wartości głównej (RMSE)**: pierwiastek kwadratowy średniej wartości kwadratowych błędów prognoz wykonanych na testowym zestawie danych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik wyznaczania**: znany również jako wartość R kwadratowa, ta Metryka statystyczna wskazuje, jak dobrze model dopasowuje dane.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsza wartość wskazuje, że przewidywania są bliżej rzeczywistych wartości. Dla współczynnika wyznaczania wartość bliższej wartości to 1 (1,0), tym lepsze przewidywania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Pomiń tę sekcję, jeśli chcesz kontynuować w części 2 samouczka [Wdrażanie modeli](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W części drugiej dowiesz się, jak wdrożyć model jako punkt końcowy w czasie rzeczywistym.

> [!div class="nextstepaction"]
> [Kontynuuj Wdrażanie modeli](tutorial-designer-automobile-price-deploy.md)

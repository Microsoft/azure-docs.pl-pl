---
title: 'ML Studio (klasyczny): Szybki Start: Tworzenie eksperymentu nauki o danych — Azure'
description: Ten przewodnik Szybki start z dziedziny uczenia maszynowego przeprowadzi Cię przez łatwy eksperyment dotyczący nauki o danych. Będziemy prognozować cenę samochodu, używając algorytmu regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: d23702a71bababec2e172181c8e75c26241d0460
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348981"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Szybki Start: Tworzenie pierwszego eksperymentu do nauki o danych w Machine Learning Studio (klasyczny)

**dotyczy:** ![ tak ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ nie](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

W tym przewodniku szybki start utworzysz eksperyment uczenia maszynowego w [Azure Machine Learning Studio (klasyczny)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) , który przewiduje cenę samochodu na podstawie różnych zmiennych, takich jak marka i specyfikacje techniczne.

Jeśli dopiero zaczynasz korzystać z uczenia maszynowego, seria filmów wideo zatytułowana [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Analiza danych dla początkujących) stanowi znakomite wprowadzenie do uczenia maszynowego przedstawione przy użyciu codziennego języka i pojęć.

W tym przewodniku Szybki start obowiązuje domyślny przepływ pracy dla eksperymentu:

1. **Tworzenie modelu**
    - [Pobieranie danych]
    - [Przygotowywanie danych]
    - [Definiowanie funkcji]
1. **Trenowanie modelu**
    - [Wybieranie i stosowanie algorytmu]
1. **Generowanie wyników i testowanie modelu**
    - [Przewidywanie nowych cen samochodów]

[Pobieranie danych]: #get-the-data
[Przygotowywanie danych]: #prepare-the-data
[Definiowanie funkcji]: #define-features
[Wybieranie i stosowanie algorytmu]: #choose-and-apply-an-algorithm
[Przewidywanie nowych cen samochodów]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Pobieranie danych

Do przeprowadzenia uczenia maszynowego potrzebne są dane.
Istnieje kilka przykładowych zestawów danych zawartych w programie Studio (klasyczny), których można użyć, lub można importować dane z wielu źródeł. W tym scenariuszu będziemy używać przykładowego zestawu danych **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów), dołączonego do obszaru roboczego.
Zestaw ten zawiera dane różnych modeli samochodów, na przykład informacje dotyczące marki, ceny czy specyfikacji technicznej.

> [!TIP]
> Funkcjonalną kopię poniższego eksperymentu można znaleźć w [galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai). Przejdź do **[pierwszego eksperymentu z nauką o danych — Prognoza cen samochodów](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** i kliknij pozycję **Otwórz w programie Studio** , aby pobrać kopię eksperymentu do obszaru roboczego Machine Learning Studio (klasycznego).

Poniżej przedstawiono procedurę dołączania zestawu danych do eksperymentu.

1. Utwórz nowy eksperyment, klikając pozycję **+ Nowy** u dołu okna Machine Learning Studio (klasycznego). Wybierz pozycję **eksperymentowanie**  >   **pusty eksperyment**.

1. Eksperymentowi zostanie nadana domyślna nazwa, wyświetlana w górnej części obszaru roboczego. Zaznacz ten tekst i wpisz opisową nazwę, na przykład **Prognozowanie cen samochodów**. Nazwa nie musi być unikatowa.

    ![Zmienianie nazwy eksperymentu](./media/create-experiment/rename-experiment.png)

1. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Wpisz **automobile** (samochód) w polu wyszukiwania w górnej części tej palety, aby znaleźć zestaw danych z etykietą **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów). Przeciągnij ten zestaw danych do obszaru roboczego eksperymentu.

    ![Znajdowanie zestawu danych dotyczących samochodów i przeciąganie go do obszaru roboczego eksperymentu](./media/create-experiment/type-automobile.png)

Aby wyświetlić graficzną reprezentację danych, kliknij port wyjściowy w dolnej części zestawu danych dotyczących samochodów, a następnie wybierz pozycję **Visualize** (Wizualizacja).

![Klikanie portu wyjściowego i wybieranie polecenia „Visualize” (Wizualizacja)](./media/create-experiment/select-visualize.png)

> [!TIP]
> Zestawy danych i moduły mają porty wejściowe i wyjściowe oznaczone małymi kołami — porty wejściowe znajdują się u góry, a wyjściowe u dołu.
Aby utworzyć przepływ danych w eksperymencie, połącz port wyjściowy danego modułu z portem wejściowym innego modułu.
W dowolnym momencie można kliknąć port wyjściowy zestawu danych lub modułu, aby wyświetlić dane w określonym punkcie przepływu danych.

W tym zestawie danych poszczególne wiersze reprezentują samochody, a zmienne skojarzone z samochodami są wyświetlane jako kolumny. Cenę będziemy przewidywać w skrajnej prawej kolumnie (kolumna 26 o nazwie „price” [cena]), używając zmiennych dotyczących konkretnego samochodu.

![Wyświetlanie danych samochodów w oknie wizualizacji danych](./media/create-experiment/visualize-auto-data.png)

Aby zamknąć okno wizualizacji, kliknij znak „**x**” w prawym górnym rogu.

## <a name="prepare-the-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają przetworzenia wstępnego przed rozpoczęciem analizy. Zwróć uwagę na to, że w przypadku niektórych wierszy w kolumnach brakuje wartości. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. Usuniemy wszystkie wiersze z brakującymi wartościami. Ponadto w kolumnie **normalized-losses** (znormalizowane straty) występuje wiele przypadków brakujących wartości, dlatego całkowicie wykluczymy tę kolumnę z modelu.

> [!TIP]
> Większość modułów wymaga wyczyszczenia brakujących wartości w danych wejściowych.

Najpierw dodamy moduł, który całkowicie usunie kolumnę **normalized-losses** (znormalizowane straty). Następnie dodamy kolejny moduł usuwający wszystkie wiersze, w których brakuje danych.

1. Wpisz ciąg **select columns** (wybieranie kolumn) w polu wyszukiwania w górnej części palety modułów, aby znaleźć moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych). Następnie przeciągnij go do obszaru roboczego eksperymentu. Ten moduł pozwala wybierać kolumny danych, które mają zostać dołączone do modelu lub wykluczone z niego.

1. Połącz port wyjściowy zestawu danych **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów) z portem wejściowym modułu Select Columns in Dataset (Wybieranie kolumn w zestawie danych).

    ![Dodawanie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) do obszaru roboczego eksperymentu i tworzenie połączenia](./media/create-experiment/type-select-columns.png)

1. Kliknij moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości).

   - Po lewej stronie kliknij pozycję **With rules** (Za pomocą reguł).
   - W obszarze **Begin With** (Rozpocznij od) kliknij pozycję **All columns** (Wszystkie kolumny). Te reguły spowodują przetworzenie wszystkich kolumn (z wyjątkiem tych wykluczonych) przez moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).
   - Z list rozwijanych wybierz pozycje **Exclude** (Wyklucz) i **column names** (nazwy kolumn), a następnie kliknij wewnątrz pola tekstowego. Zostanie wyświetlona lista kolumn. Wybierz pozycję **normalized-losses** (znormalizowane straty), aby dodać ją do pola tekstowego.
   - Kliknij przycisk znacznika wyboru (OK), aby zamknąć selektora kolumn (w prawym dolnym rogu).

     ![Uruchamianie selektora kolumn i wykluczanie kolumny „normalized-losses” (znormalizowane straty)](./media/create-experiment/launch-column-selector.png)

     Zawartość okienka właściwości modułu **Select Columns in Dataset** (Wybieranie kolumn w zestawie danych) określa, że zostaną przetworzone wszystkie kolumny zestawu danych z wyjątkiem kolumny **normalized-losses** (znormalizowane straty).

     ![Zawartość okienka właściwości wskazuje na wykluczenie kolumny „normalized-losses” (znormalizowane straty)](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W tym przypadku kliknij dwukrotnie moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i dodaj komentarz „Wykluczenie kolumny znormalizowanych strat”.

     ![Dwukrotne kliknięcie modułu umożliwia dodanie komentarza](./media/create-experiment/add-comment.png)

1. Przeciągnij moduł [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) do obszaru roboczego eksperymentu i połącz go z modułem [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych). W okienku **Properties** (Właściwości) w obszarze **Cleaning mode** (Tryb czyszczenia) wybierz pozycję **Remove entire row** (Usuń cały wiersz). Te opcje spowodują wyczyszczenie danych przez moduł [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) — zostaną usunięte wiersze, w których brakuje wartości. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.

    ![Ustawianie trybu czyszczenia na wartość „Remove entire row” (Usuń cały wiersz) w module „Clean Missing Data” (Czyszczenie brakujących danych)](./media/create-experiment/set-remove-entire-row.png)

1. Uruchom eksperyment, klikając pozycję **URUCHOM** u dołu strony.

    Po zakończeniu eksperymentu na wszystkich modułach są widoczne zielone znaczniki wyboru. Oznacza to, że działanie modułów zakończyło się pomyślnie. Zwróć również uwagę na informację o **zakończeniu działania** wyświetlaną w prawym górnym rogu.

    ![Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Dlaczego teraz uruchomiliśmy eksperyment? Uruchomienie eksperymentu spowodowało przekazanie definicji kolumn danych z zestawu danych do modułów [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych). Oznacza to, że informacje te będą dostępne dla wszystkich modułów połączonych z modułem [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych).

Teraz mamy czyste dane. Jeśli chcesz wyświetlić oczyszczony zestaw danych, kliknij lewy port wyjściowy modułu [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) i wybierz pozycję **Visualize** (Wizualizacja). Zwróć uwagę na to, że kolumna **normalized-losses** (znormalizowane straty) nie jest już uwzględniana i nie ma brakujących wartości.

Po oczyszczeniu danych można określić, jakie cechy zostaną użyte w modelu predykcyjnym.

## <a name="define-features"></a>Definiowanie funkcji

W uczeniu maszynowym *funkcje* są indywidualnymi właściwościami interesujących rzeczy. W naszym zestawie danych poszczególne wiersze odpowiadają różnym samochodom, a kolumny — cechom tych samochodów.

Znalezienie odpowiedniego zestawu cech, który ma służyć do utworzenia modelu predykcyjnego, wymaga eksperymentowania oraz dysponowania wiedzą na temat bieżącego problemu. Pewne cechy lepiej nadają się do prognozowania danych docelowych. Niektóre cechy są ściśle powiązane z innymi, dlatego można je usunąć. Na przykład dane dotyczące zużycia paliwa w mieście i w trasie mają bliski związek ze sobą, co sprawia, że usunięcie jednej z tych cech nie będzie miało zasadniczego wpływu na prognozę.

Utworzymy model, który korzysta z podzbioru cech zawartych w naszym zestawie danych. W poszukiwaniu lepszych wyników można uruchamiać eksperymenty oparte na różnych podzbiorach cech. Jednak aby rozpocząć, wypróbujmy następujące funkcje:

> Marka, styl nadwozia, koło, rozmiar silnika, w mocy, w szczycie

1. Przeciągnij kolejny moduł [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) do obszaru roboczego eksperymentu. Połącz lewy port wyjściowy modułu [Clean Missing Data][clean-missing-data] (Czyszczenie brakujących danych) z wejściem modułu [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).

    ![Łączenie modułu „Select Columns in Dataset” (Wybieranie kolumn w zestawie danych) z modułem „Clean Missing Data” (Czyszczenie brakujących danych)](./media/create-experiment/connect-clean-to-select.png)

1. Kliknij dwukrotnie moduł i wpisz „Wybieranie cech w celu prognozowania”.

1. Kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości).

1. Kliknij pozycję **With rules** (Za pomocą reguł).

1. W obszarze **Begin With** (Rozpocznij od) kliknij pozycję **No columns** (Brak kolumn). W wierszu filtru wybierz pozycje **Include** (Dołącz) i **column names** (nazwy kolumn), a następnie wybierz nazwy kolumn w polu tekstowym. Ten filtr spowoduje nieprzekazywanie przez moduł żadnych kolumn (cech) z wyjątkiem tych, które zostały wybrane.

1. Kliknij przycisk znacznika wyboru (OK).

    ![Wybieranie kolumn (cech), które mają zostać uwzględnione w prognozie](./media/create-experiment/select-columns-to-include.png)

Ten moduł spowoduje powstanie filtrowanego zestawu danych zawierającego tylko cechy, które chcemy przekazać do algorytmu uczenia. Algorytm ten zostanie użyty w następnym kroku. Możesz później wrócić do tego kroku i wybrać inny zbiór cech.

## <a name="choose-and-apply-an-algorithm"></a>Wybieranie i stosowanie algorytmu

Po przygotowaniu danych można przystąpić do konstruowania modelu predykcyjnego, co obejmuje uczenie i testowanie. Użyjemy danych do nauczenia modelu, a następnie przetestujemy go, aby sprawdzić dokładność przewidywanych cen.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

Algorytmy *klasyfikacji* i *regresji* to dwa typy nadzorowanego uczenia maszynowego. Klasyfikacja przewiduje odpowiedź na podstawie zdefiniowanego zestawu kategorii, takich jak kolory (czerwony, niebieski lub zielony). Regresja służy do prognozowania liczby.

Ponieważ chcemy przewidzieć cenę, która jest liczbą, użyjemy algorytmu regresji. W tym przykładzie użyjemy modelu *regresji liniowej*.


Uczenie modelu polega na przekazaniu mu zestawu danych zawierających cenę. Model skanuje dane i szuka korelacji między cechami samochodu a jego ceną. Następnym krokiem jest przetestowanie modelu — otrzyma on zestaw cech określonych modeli samochodów w celu przewidzenia ich z góry znanych cen. Prognozy zostaną porównane z rzeczywistymi cenami samochodów.

Dane dzielimy na dwa oddzielne zestawy, aby użyć ich do celów szkoleniowych i do testów.

1. Wybierz moduł [Split Data][split] (Podział danych) i przeciągnij go do obszaru roboczego eksperymentu, a następnie połącz go z ostatnim modułem [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych).

1. Wybierz moduł [Split Data][split] (Podział danych), klikając go. Znajdź opcję **Fraction of rows in the first output dataset** (Odsetek wierszy w pierwszym zestawie danych wyjściowych) (w okienku **Właściwości** po prawej stronie obszaru roboczego) i ustaw dla niej wartość 0,75. Dzięki temu 75% danych zostanie użytych do nauczenia modelu, a pozostałe 25% do testów.

    ![Ustawianie parametru podziału modułu „Split Data” (Podział danych) na wartość 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Zmieniając wartość parametru **Random seed** (Inicjator losowy) można uzyskać różne próbki losowe do celów szkoleniowych i testów. Ten parametr umożliwia sterowanie inicjacją pseudolosowego generatora liczb.

1. Uruchom eksperyment. Po uruchomieniu eksperymentu moduły [Select Columns in Dataset][select-columns] (Wybieranie kolumn w zestawie danych) i [Split Data][split] (Podział danych) przekażą definicje kolumn do modułów, które będą dodawane później.  

1. Aby wybrać algorytm uczenia, rozwiń kategorię **Machine Learning** (Uczenie maszynowe) na palecie modułów wyświetlanej z lewej strony obszaru roboczego, a następnie rozwiń węzeł **Initialize Model** (Inicjacja modelu). Zostaną wyświetlone różne kategorie modułów, których można użyć do zainicjowania algorytmów uczenia maszynowego. W tym eksperymencie wybierz moduł [Linear Regression][linear-regression] (Regresja liniowa) z kategorii **Regression** (Regresja) i przeciągnij go do obszaru roboczego eksperymentu. (Możesz również znaleźć go, wpisując „linear regression” [regresja liniowa] w polu wyszukiwania palety).

1. Znajdź moduł [Train Model][train-model] (Uczenie modelu) i przeciągnij go do obszaru roboczego eksperymentu. Połącz wyjście modułu [Linear Regression][linear-regression] (Regresja liniowa) z lewym wejściem modułu [Train Model][train-model] (Uczenie modelu) i połącz wyjście danych szkoleniowych (lewy port) modułu [Split Data][split] (Podział danych) z prawym wejściem modułu [Train Model][train-model] (Uczenie modelu).

    ![Łączenie modułu „Train model” (Uczenie modelu) z modułami „Linear Regression” (Regresja liniowa) i „Split Data” (Podział danych)](./media/create-experiment/connect-train-model.png)

1. Kliknij moduł [Train Model][train-model] (Uczenie modelu), kliknij pozycję **Launch column selector** (Uruchom selektora kolumn) w okienku **Properties** (Właściwości), a następnie wybierz kolumnę **price** (cena). **Price** (Cena) to wartość, która będzie prognozowana przez nasz model.

    Kolumnę **price** (cena) możesz wybrać, przenosząc ją z listy **Available columns** (Dostępne kolumny) do listy **Selected columns** (Wybrane kolumny) w selektorze kolumn.

    ![Wybieranie kolumny cen w module „Train model” (Uczenie modelu)](./media/create-experiment/select-price-column.png)

1. Uruchom eksperyment.

W efekcie powstał nauczony model regresji, który może służyć do generowania wyników na podstawie nowych danych samochodów w celu prognozowania cen.

![Gdy działanie eksperymentu zakończy się, powinien on wyglądać mniej więcej tak](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Przewidywanie nowych cen samochodów

Gdy udało się nauczyć model przy użyciu 75% danych, można wygenerować wyniki dla pozostałych 25% danych, aby sprawdzić poprawność funkcjonowania modelu.

1. Znajdź moduł [Score Model][score-model] (Generowanie wyników przez model) i przeciągnij go do obszaru roboczego eksperymentu. Połącz wyjście modułu [Train Model][train-model] (Uczenie modelu) z lewym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników przez model). Połącz wyjście danych testowych (prawy port) modułu [Split Data][split] (Podział danych) z prawym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników przez model).

    ![Łączenie modułu „Score model” (Generowanie wyników przez model) z modułami „Train Model” (Uczenie modelu) i „Split Data” (Podział danych)](./media/create-experiment/connect-score-model.png)

1. Uruchom eksperyment, aby wyświetlić dane wyjściowe z modułu [Score Model][score-model] (Generowanie wyników przez model). W tym celu kliknij port wyjściowy modułu [Score Model][score-model] i wybierz pozycję **Visualize** (Wizualizacja). Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.  

    ![Dane wyjściowe modułu „Score model” (Generowanie wyników przez model)](./media/create-experiment/score-model-output.png)

1. Na koniec przetestujemy jakość wyników. Wybierz moduł [Evaluate Model][evaluate-model] (Ocena modelu) i przeciągnij go do obszaru roboczego eksperymentu, a następnie połącz wyjście modułu [Score Model][score-model] (Generowanie wyników przez model) z lewym wejściem modułu [Evaluate Model][evaluate-model] (Ocena modelu). Końcowy eksperyment powinien wyglądać następująco:

    ![Eksperyment końcowy](./media/create-experiment/complete-linear-regression-experiment.png)

1. Uruchom eksperyment.

Aby wyświetlić dane wyjściowe z modułu [Evaluate Model][evaluate-model] (Ocena modelu), kliknij port wyjściowy i wybierz pozycję **Visualize** (Wizualizacja).

![Wyniki oceny eksperymentu](./media/create-experiment/evaluation-results.png)

Wyświetlane są następujące statystyki dla modelu:

- **Średni bezwzględny błąd** (MAE, Mean Absolute Error): wartość średnia bezwzględnych błędów (*błąd* odpowiada różnicy między wartością prognozowaną a wartością rzeczywistą).
- **Pierwiastek błędu średniokwadratowego** (RMSE, Root Mean Squared Error): pierwiastek kwadratowy ze średniej kwadratów błędów prognoz dla zestawu danych testowych.
- **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
- **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
- **Współczynnik determinacji**: znany także jako **wartość R-kwadrat** jest miarą statystyczną jakości dopasowania modelu do danych.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsze wartości błędów wskazują na ściślejsze dopasowanie prognoz do rzeczywistych wartości. W przypadku **współczynnika determinacji** prognozy są tym lepsze, im jego wartość jest bliższa jedności (1,0).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start utworzyliśmy prosty eksperyment przy użyciu przykładowego zestawu danych. Aby bardziej szczegółowo zapoznać się z procesem tworzenia i wdrażania modelu, przejdź do samouczka dotyczącego rozwiązań do analizy predykcyjnej.

> [!div class="nextstepaction"]
> [Samouczek: opracowywanie rozwiązania predykcyjnego w programie Studio (klasyczne)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

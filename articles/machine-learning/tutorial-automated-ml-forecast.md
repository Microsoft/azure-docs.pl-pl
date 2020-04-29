---
title: Prognoza popytu na potrzeby udostępniania roweru z automatycznym eksperymentem dotyczącym ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uczenie i wdrażanie modelu prognozowania popytu przy użyciu automatycznej uczenia maszynowego w programie Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77088242"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Samouczek: Prognoza popytu na udostępnianie rowerów przy użyciu automatycznej uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym samouczku użyjesz zautomatyzowanej uczenia maszynowego lub zautomatyzowanej sieci ML w Azure Machine Learning Studio, aby utworzyć model prognozowania szeregów czasowych, aby przewidzieć zapotrzebowanie na wypożyczenie dla usługi udostępniania roweru.

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Utwórz i Załaduj zestaw danych.
> * Konfigurowanie i uruchamianie zautomatyzowanego eksperymentu ML.
> * Poznaj wyniki eksperymentu.
> * Wdróż najlepszy model.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy Azure Machine Learning Enterprise Edition. Jeśli nie masz obszaru roboczego, [Utwórz obszar roboczy Enterprise Edition](how-to-manage-workspace.md). 
    * Automatyczne Uczenie maszynowe w programie Azure Machine Learning Studio jest dostępne tylko dla obszarów roboczych wersji Enterprise Edition. 
* Pobieranie pliku danych [Bike-No. csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Wprowadzenie do programu Azure Machine Learning Studio

Na potrzeby tego samouczka utworzysz zautomatyzowany przebieg eksperymentu ML w Azure Machine Learning Studio, skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych o wszystkich poziomach umiejętności. Program Studio nie jest obsługiwany w przeglądarkach programu Internet Explorer.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz pozycję **Rozpocznij**.

1. W lewym okienku wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

1. Wybierz pozycję **+ Nowy zautomatyzowany przebiegu ml**. 

## <a name="create-and-load-dataset"></a>Utwórz i Załaduj zestaw danych

Przed skonfigurowaniem eksperymentu Przekaż plik danych do obszaru roboczego w formie zestawu danych Azure Machine Learning. Dzięki temu można upewnić się, że dane są sformatowane odpowiednio do eksperymentu.

1. W formularzu **Wybierz zestaw danych** wybierz pozycję **z plików lokalnych** z listy rozwijanej **+ Utwórz zestaw danych** . 

    1. W **podstawowym formularzu informacji** nadaj zestawowi danych nazwę i podaj opcjonalny opis. Typ zestawu danych powinien domyślnie mieć wartość **tabelaryczną**, ponieważ automatyczna wartość ML w Azure Machine Learning Studio obsługuje obecnie tylko tabelaryczne zestawy danych.
    
    1. Wybierz pozycję **dalej** w lewym dolnym rogu

    1. W formularzu **Magazyn danych i wybór plików** wybierz domyślny magazyn danych, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego, **workspaceblobstore (Azure Blob Storage)**. Jest to lokalizacja przechowywania, w której przekażesz plik danych. 

    1. Wybierz pozycję **Przeglądaj**. 
    
    1. Wybierz plik **Bike-No. csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Wybierz pozycję **dalej**

       Po zakończeniu przekazywania ustawienia i formularz podglądu są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **Ustawienia i formularz podglądu** są wypełnione w następujący sposób, a następnie wybierz przycisk **dalej**.
        
        Pole|Opis| Wartość dla samouczka
        ---|---|---
        Format pliku|Definiuje układ i typ danych przechowywanych w pliku.| Lista
        Ogranicznik|Jeden lub więcej znaków do określenia granicy między&nbsp; oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych. |Przecinek
        Kodowanie|Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.| UTF-8
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.| Użyj nagłówków z pierwszego pliku
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.| Brak

    1. Formularz **schematu** umożliwia dalsze Konfigurowanie danych na potrzeby tego eksperymentu. 
    
        1. **Na potrzeby** tego przykładu wybierz opcję ignorowania niektórych i **zarejestrowanych** kolumn. Te kolumny są podziałem kolumny **CNT** , dlatego nie zawierają ich.

        1. Również dla tego przykładu pozostaw wartości domyślne **Właściwości** i **typu**. 
        
        1. Wybierz pozycję **Dalej**.

    1. Na formularzu **Potwierdź szczegóły** Sprawdź, czy informacje są zgodne z informacjami o tym, co zostało wcześniej wypełnione w **podstawowych informacjach** i ustawieniach oraz w formularzach **wersji zapoznawczej** .

    1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.

    1. Wybierz zestaw danych, który zostanie wyświetlony na liście.

    1. Wybierz pozycję **dalej**.

## <a name="configure-experiment-run"></a>Konfigurowanie przebiegu eksperymentu

Po załadowaniu i skonfigurowaniu danych skonfiguruj zdalny cel obliczeń i wybierz kolumnę, dla której chcesz przewidzieć dane.

1. Wypełnij formularz **konfigurowania przebiegu** w następujący sposób:
    1. Wprowadź nazwę eksperymentu:`automl-bikeshare`

    1. Jako kolumnę docelową wybierz pozycję **CNT** , którą chcesz przewidzieć. Ta kolumna wskazuje liczbę wynajmu udziałów w ramach roweru.

    1. Wybierz pozycję **Utwórz nowe obliczenie** i skonfiguruj obiekt docelowy obliczeń. Zautomatyzowana ML obsługuje tylko Azure Machine Learning obliczeń. 

        Pole | Opis | Wartość dla samouczka
        ----|---|---
        Nazwa obliczeniowa |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|rower — obliczenia
        Rozmiar&nbsp;maszyny&nbsp;wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.|Standard_DS12_V2
        Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł.|Minimalna liczba węzłów: 1<br>Maksymalna liczba węzłów: 6
  
        1. Wybierz pozycję **Utwórz** , aby uzyskać obiekt docelowy obliczeń. 

            **Wykonanie tej czynności może zająć kilka minut.** 

        1. Po utworzeniu wybierz nowe miejsce docelowe obliczeń z listy rozwijanej.

    1. Wybierz pozycję **Dalej**.

## <a name="select-task-type-and-settings"></a>Wybierz typ i ustawienia zadania

Ukończ instalację eksperymentu dotyczącego zautomatyzowanej sieci ML, określając typ zadania Uczenie maszynowe i ustawienia konfiguracji.

1. W formularzu **Typ zadania i ustawienia** wybierz opcję **prognozowanie szeregów czasowych** jako typ zadania Uczenie maszynowe.

1. Wybierz **datę** jako **kolumnę czasową** i pozostaw puste **kolumny Grupuj według** . 

    1. Wybierz pozycję **Wyświetl dodatkowe ustawienia konfiguracji** i wypełnij pola w następujący sposób. Te ustawienia służą do lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych.

  
        Dodatkowe&nbsp;konfiguracje|Opis|Wartość&nbsp;dla&nbsp;samouczka
        ------|---------|---
        Metryka podstawowa| Metryka oceny, według której będzie mierzony algorytm uczenia maszynowego.|Znormalizowany błąd średniego poziomu głównego
        Automatyczne cechowania| Włącza przetwarzanie wstępne. Obejmuje to automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych.| Włączanie
        Wyjaśnij najlepszy model (wersja zapoznawcza)| Automatycznie pokazuje wyjaśnienie najlepszego modelu utworzonego za pomocą zautomatyzowanej ML.| Włączanie
        Zablokowane algorytmy | Algorytmy, które mają zostać wykluczone z zadania szkoleniowego| Skrajnie losowe drzewa
        Dodatkowe ustawienia prognozowania| Te ustawienia pomagają poprawić dokładność modelu <br><br> _**Przewidywany horyzont**_ czasowy: długość czasu na przyszłość, którą chcesz przewidzieć <br> _**Spowolnienia celu prognozy:**_ jak daleko z powrotem chcesz skonstruować spowolnienia zmiennej docelowej <br> _**Docelowe okno kroczące**_: Określa rozmiar okna stopniowego, nad którym będą generowane funkcje, takie jak *Maksymalna, minimalna* i *sum*. |Horyzont prognoz: 14 <br> Spowolnienia&nbsp;docelowy&nbsp;prognozy: brak <br> Rozmiar&nbsp;&nbsp;stopniowego&nbsp;okna docelowego: brak
        Kryterium zakończenia| Jeśli kryteria są spełnione, zadanie szkolenia zostanie zatrzymane. |Czas&nbsp;zadania&nbsp;szkoleniowego (godziny): 3 <br> Próg&nbsp;wyniku&nbsp;metryki: brak
        Walidacja | Wybierz typ i liczbę testów.|Typ walidacji:<br>&nbsp;k — złożenie&nbsp;krzyżowego sprawdzania poprawności <br> <br> Liczba walidacji: 5
        Współbieżność| Maksymalna liczba wykonanych równoległych iteracji na iterację| Maksymalna&nbsp;liczba&nbsp;współbieżnych iteracji: 6
        
        Wybierz pozycję **Zapisz**.

## <a name="run-experiment"></a>Uruchom eksperyment

Aby uruchomić eksperyment, wybierz pozycję **Zakończ**. Zostanie otwarty ekran **szczegóły uruchamiania** ze **stanem uruchomienia** znajdującym się u góry obok numeru uruchomienia. Ten stan jest aktualizowany w miarę postępu eksperymentu.

>[!IMPORTANT]
> Przygotowanie eksperymentu trwa do **10-15 minut** .
> Po uruchomieniu usługi zajmiemy **2-3 minut więcej czasu dla każdej iteracji**.  <br> <br>
> W środowisku produkcyjnym najkorzystniej jest nieco powracać w miarę potrzeb. Gdy czekasz, zalecamy rozpoczęcie eksplorowania przetestowanych algorytmów na karcie **modele** po ich zakończeniu. 

##  <a name="explore-models"></a>Eksploruj modele

Przejdź do karty **modele** , aby zobaczyć przetestowane algorytmy (modele). Domyślnie modele są uporządkowane według wyniku metryk po ich zakończeniu. Na potrzeby tego samouczka model, który ocenia najwyższy poziom w oparciu o wybraną **znormalizowaną, średnią wartość błędu kwadratowego** w górnej części listy.

Podczas oczekiwania na zakończenie wszystkich modeli eksperymentów wybierz **nazwę algorytmu** kompletnego modelu, aby poznać jego szczegóły wydajności. 

Poniższy przykład przechodzi przez **Szczegóły modelu** i karty **wizualizacje** , aby wyświetlić właściwości wybranego modelu, metryki i wykresy wydajności. 

![Szczegóły uruchamiania](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Automatyczne Uczenie maszynowe w programie Azure Machine Learning Studio umożliwia wdrożenie najlepszego modelu jako usługi sieci Web w kilku krokach. Wdrożenie to integracja modelu, dzięki czemu można przewidzieć nowe dane i identyfikować potencjalne obszary szans sprzedaży. 

W przypadku tego eksperymentu wdrożenie do usługi sieci Web oznacza, że firma z udziałem roweru ma teraz iteracyjne i skalowalne rozwiązanie sieci Web do prognozowania popytu na dzierżawę. 

Po zakończeniu przebiegu Wróć do strony **szczegóły uruchamiania** i wybierz kartę **modele** .

W tym kontekście eksperymentu **StackEnsemble** jest uznawany za najlepszy model, w oparciu o **znormalizowaną średnią metrykę błędu kwadratowego** .  Wdrażamy ten model, ale zaleca się wdrożenie trwa około 20 minut. Proces wdrażania obejmuje kilka czynności, takich jak rejestrowanie modelu, Generowanie zasobów i konfigurowanie ich dla usługi sieci Web.

1. Wybierz przycisk **Wdróż najlepszy model** w lewym dolnym rogu.

1. Wypełnij okienko **Wdróż model** w następujący sposób:

    Pole| Wartość
    ----|----
    Nazwa wdrożenia| bikeshare — Wdróż
    Opis wdrożenia| wdrożenie popytu na zapotrzebowanie na rowery
    Typ obliczenia | Wybierz wystąpienie obliczeniowe platformy Azure (ACI)
    Włącz uwierzytelnianie| Wyłącz. 
    Używanie niestandardowych zasobów wdrażania| Wyłącz. Wyłączenie zezwala na Autogenerowanie domyślnego pliku sterownika (skrypt oceniania) i pliku środowiska. 
    
    W tym przykładzie używamy ustawień domyślnych, które są dostępne w menu *Zaawansowane* . 

1. Wybierz pozycję **Wdróż**.  

    W górnej części ekranu **uruchamiania** zostanie wyświetlony zielony komunikat o powodzeniu, który stwierdził, że wdrożenie zostało uruchomione pomyślnie. Postęp wdrożenia można znaleźć  
    w **zalecanym okienku modelu** w obszarze **Wdróż stan**.
    
Po pomyślnym wdrożeniu masz działającą usługę sieci Web do generowania prognoz. 

Przejdź do [**następnych kroków**](#next-steps) , aby dowiedzieć się więcej na temat korzystania z nowej usługi sieci Web i testowania prognoz przy użyciu Power BI wbudowanej Azure Machine Learning obsługi.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pliki wdrożeń są większe niż pliki danych i eksperymenty, dzięki czemu są one droższe do przechowywania. Usuń tylko pliki wdrożenia, aby zminimalizować koszty dla konta, lub jeśli chcesz zachować obszar roboczy i pliki eksperymentów. W przeciwnym razie Usuń całą grupę zasobów, jeśli nie planujesz używać żadnego z tych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia z programu Azure Machine Learning Studio, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do [Azure Machine Learning Studio](https://ml.azure.com/). Przejdź do obszaru roboczego i po lewej stronie okienka **elementy zawartości** wybierz pozycję **punkty końcowe**. 

1. Wybierz wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń**. 

1. Wybierz pozycję **Zastosuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zautomatyzowanej ML w Azure Machine Learning Studio, aby utworzyć i wdrożyć model prognozowania szeregów czasowych, który przewiduje przewidywanie popytu na dzierżawę. 

Zapoznaj się z tym artykułem, aby zapoznać się z procedurą tworzenia Power BI obsługiwanego schematu w celu ułatwienia użycia nowo wdrożonej usługi sieci Web:

> [!div class="nextstepaction"]
> [Używanie usługi internetowej](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Ten zestaw danych udostępniania roweru został zmodyfikowany dla tego samouczka. Ten zestaw danych został udostępniony w ramach [konkursu Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) i pierwotnie dostępny za pośrednictwem [Wielkiej Bikeshare](https://www.capitalbikeshare.com/system-data). Można go również znaleźć w [bazie danych UCI Machine Learning](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Źródło: Fanaee-T, Hadi i gama, Joao, Event Labeling desourceers detektorzy i wiedza w tle, postęp w sztucznej analizie (2013): PP. 1-15, Springer Berlin Heidelberg.

---
title: Tworzenie zautomatyzowanych modeli klasyfikacji ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uczenie & wdrażać modele klasyfikacji przy użyciu interfejsu zautomatyzowanego uczenia maszynowego Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.custom: automl
ms.openlocfilehash: 866be6a2449f3b10d200968782b90653e1363906
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359803"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Samouczek: Tworzenie modelu klasyfikacji przy użyciu zautomatyzowanej ML w Azure Machine Learning


W tym samouczku dowiesz się, jak utworzyć prosty model klasyfikacji bez pisania pojedynczego wiersza kodu przy użyciu funkcji automatycznego uczenia maszynowego w programie Azure Machine Learning Studio. Ten model klasyfikacji przewiduje, czy klient zasubskrybuje stały termin złożenia przez instytucję finansową.

Dzięki zautomatyzowanej usłudze Machine Learning można zautomatyzować czasochłonne zadania. Automatyczne Uczenie maszynowe szybko iteruje wiele kombinacji algorytmów i parametrów, aby ułatwić znalezienie najlepszego modelu w oparciu o pomyślną metrykę wybrania.

Aby zapoznać się z przykładem prognozowania szeregów czasowych, zobacz [Samouczek: prognozowanie popytu & AutoML](tutorial-automated-ml-forecast.md).

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Utwórz obszar roboczy Azure Machine Learning.
> * Uruchamianie eksperymentu zautomatyzowanego uczenia maszynowego.
> * Wyświetl szczegóły eksperymentu.
> * Wdrażanie modelu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Pobierz plik danych [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Kolumna **y** wskazuje, czy klient subskrybuje stały termin wpłaty, który jest później zidentyfikowany jako kolumna docelowa dla prognoz w tym samouczku. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Istnieje wiele [sposobów tworzenia obszaru roboczego](how-to-manage-workspace.md). W tym samouczku utworzysz obszar roboczy za pośrednictwem Azure Portalej konsoli internetowej do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 

## <a name="get-started-in-azure-machine-learning-studio"></a>Wprowadzenie do programu Azure Machine Learning Studio

Należy wykonać następujące czynności w celu skonfigurowania i uruchomienia kroków za pośrednictwem Azure Machine Learning Studio at https://ml.azure.com , skonsolidowanego interfejsu sieci Web zawierającego narzędzia uczenia maszynowego w celu przeprowadzenia scenariuszy analizy danych dla lekarzy danych o wszystkich poziomach umiejętności. Program Studio nie jest obsługiwany w przeglądarkach programu Internet Explorer.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz pozycję **Rozpocznij**.

1. W lewym okienku wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

   Ponieważ jest to pierwszy zautomatyzowany eksperyment z ML, zobaczysz pustą listę i linki do dokumentacji.

   ![Strona Wprowadzenie](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Wybierz pozycję **+ Nowy zautomatyzowany przebiegu ml**. 

## <a name="create-and-load-dataset"></a>Utwórz i Załaduj zestaw danych

Przed skonfigurowaniem eksperymentu Przekaż plik danych do obszaru roboczego w formie zestawu danych Azure Machine Learning. Dzięki temu można upewnić się, że dane są sformatowane odpowiednio do eksperymentu.

1. Utwórz nowy zestaw danych, wybierając pozycję **z plików lokalnych** z listy rozwijanej  **+ Utwórz zestaw danych** . 

    1. W **podstawowym formularzu informacji** nadaj zestawowi danych nazwę i podaj opcjonalny opis. Interfejs zautomatyzowanej sieci obsługuje obecnie tylko TabularDatasets, więc typ DataSet powinien domyślnie mieć wartość *tabelaryczną*.

    1. Wybierz pozycję **dalej** w lewym dolnym rogu

    1. W formularzu **Magazyn danych i wybór plików** wybierz domyślny magazyn danych, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego, **workspaceblobstore (Azure Blob Storage)**. Jest to miejsce, w którym przekażesz plik danych w celu udostępnienia go w Twoim obszarze roboczym.

    1. Wybierz pozycję **Przeglądaj**.
    
    1. Wybierz plik **bankmarketing_train.csv** na komputerze lokalnym. Jest to plik pobrany jako [warunek wstępny](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** w lewym dolnym rogu, aby przekazać ją do domyślnego kontenera, który został automatycznie skonfigurowany podczas tworzenia obszaru roboczego.  
    
       Po zakończeniu przekazywania ustawienia i formularz podglądu są wstępnie wypełniane na podstawie typu pliku. 
       
    1. Sprawdź, czy **Ustawienia i formularz podglądu** są wypełnione w następujący sposób, a następnie wybierz przycisk **dalej**.
        
        Pole|Opis| Wartość dla samouczka
        ---|---|---
        Format pliku|Definiuje układ i typ danych przechowywanych w pliku.| Lista
        Ogranicznik|Jeden lub więcej znaków do określenia granicy między &nbsp; oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych. |Przecinek
        Kodowanie|Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.| UTF-8
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.| Wszystkie pliki mają te same nagłówki
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.| Brak

    1. Formularz **schematu** umożliwia dalsze Konfigurowanie danych na potrzeby tego eksperymentu. Na potrzeby tego przykładu wybierz przełącznik przełącznika dla funkcji **day_of_week** , więc nie dodawaj go do tego eksperymentu. Wybierz pozycję **Dalej**.

        ![Konfiguracja karty podglądu](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Na formularzu **Potwierdź szczegóły** Sprawdź, czy informacje są zgodne z informacjami o tym, co zostało wcześniej wypełnione w **podstawowych informacjach, magazynie danych i wyborze plików** oraz w formularzach **podglądu** .
    
    1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.
    
    1. Wybierz zestaw danych, który zostanie wyświetlony na liście.
    
    1. Przejrzyj **Podgląd danych**  , aby upewnić się, że nie doszło do **day_of_week** następnie wybierz przycisk **OK**.

    1. Wybierz pozycję  **dalej**.

## <a name="configure-experiment-run"></a>Konfigurowanie przebiegu eksperymentu

Po załadowaniu i skonfigurowaniu danych możesz skonfigurować eksperyment. Ta konfiguracja obejmuje eksperymentowe zadania projektowe, takie jak, wybranie rozmiaru środowiska obliczeniowego i określenie kolumny, która ma zostać przewidywalna. 

1. Wypełnij formularz **konfigurowania przebiegu** w następujący sposób:
    1. Wprowadź nazwę tego eksperymentu: `my-1st-automl-experiment`

    1. Zaznacz opcję **y** jako kolumnę docelową, którą chcesz przewidzieć. Ta kolumna wskazuje, czy klient subskrybuje termin depozytowy.
    
    1. Wybierz pozycję **Utwórz nowe obliczenie** i skonfiguruj obiekt docelowy obliczeń. Obiekt docelowy obliczeń to lokalne lub oparte na chmurze środowisko zasobów używane do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. W przypadku tego eksperymentu używamy obliczeń opartych na chmurze. 

        Pole | Opis | Wartość dla samouczka
        ----|---|---
        Nazwa obiektu obliczeniowego |Unikatowa nazwa identyfikująca kontekst obliczeniowy.|automl — obliczenia
        &nbsp;Typ maszyny &nbsp; wirtualnej| Wybierz typ maszyny wirtualnej dla obliczenia.|Procesor CPU (centralna jednostka przetwarzania)
        &nbsp;Rozmiar maszyny &nbsp; wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.|Standard_DS12_V2
        Minimalna/Maksymalna liczba węzłów| Aby profilować dane, musisz określić co najmniej jeden węzeł.|Minimalna liczba węzłów: 1<br>Maksymalna liczba węzłów: 6
        Czas bezczynności przed scaleniem w dół | Czas bezczynności przed automatycznym skalowaniem klastra do minimalnej liczby węzłów.|120 (wartość domyślna)
        Ustawienia zaawansowane | Ustawienia umożliwiające skonfigurowanie i autoryzację sieci wirtualnej na potrzeby eksperymentu.| Brak
        1. Wybierz pozycję **Utwórz** , aby uzyskać obiekt docelowy obliczeń. 

            **Wykonanie tej czynności może zająć kilka minut.** 

        1. Po utworzeniu wybierz nowe miejsce docelowe obliczeń z listy rozwijanej.

    1. Wybierz pozycję **Dalej**.

1. Na formularzu **Typ i ustawienia zadania** Ukończ konfigurację eksperymentu zautomatyzowanej sieci, określając typ zadania Uczenie maszynowe i ustawienia konfiguracji.
    
    1.  Wybierz pozycję **Klasyfikacja** jako typ zadania Uczenie maszynowe.

    1. Wybierz pozycję **Wyświetl dodatkowe ustawienia konfiguracji** i wypełnij pola w następujący sposób. Te ustawienia służą do lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych.

        Dodatkowe &nbsp; konfiguracje|Opis|Wartość &nbsp; dla &nbsp; samouczka
        ------|---------|---
        Metryka podstawowa| Metryka oceny, według której będzie mierzony algorytm uczenia maszynowego.|AUC_weighted
        Wyjaśnij najlepszy model| Automatycznie pokazuje wyjaśnienie najlepszego modelu utworzonego za pomocą zautomatyzowanej ML.| Włączanie
        Zablokowane algorytmy | Algorytmy, które mają zostać wykluczone z zadania szkoleniowego| Brak
        Kryterium zakończenia| Jeśli kryteria są spełnione, zadanie szkolenia zostanie zatrzymane. |&nbsp;Czas zadania szkoleniowego &nbsp; (godziny): 1 <br> &nbsp;Próg wyniku metryki &nbsp; : brak
        Walidacja | Wybierz typ i liczbę testów.|Typ walidacji:<br>&nbsp;k — złożenie &nbsp; krzyżowego sprawdzania poprawności <br> <br> Liczba walidacji: 2
        Współbieżność| Maksymalna liczba wykonanych równoległych iteracji na iterację| Maksymalna liczba &nbsp; współbieżnych &nbsp; iteracji: 5
        
        Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Zakończ** , aby uruchomić eksperyment. Po rozpoczęciu przygotowania eksperymentu zostanie otwarty ekran **szczegóły uruchamiania**  ze **stanem Uruchom** na górze.

>[!IMPORTANT]
> Przygotowanie eksperymentu trwa do **10-15 minut** .
> Po uruchomieniu usługi zajmiemy **2-3 minut więcej czasu dla każdej iteracji**.  
> Wybierz pozycję **Odśwież** okresowo, aby zobaczyć stan przebiegu w miarę postępu eksperymentu.
>
> W środowisku produkcyjnym najkorzystniej można wyszukać trochę. Jednak w tym samouczku zalecamy rozpoczęcie eksplorowania przetestowanych algorytmów na karcie **modele** po ich zakończeniu, gdy inne nadal działają. 

##  <a name="explore-models"></a>Eksploruj modele

Przejdź do karty **modele** , aby zobaczyć przetestowane algorytmy (modele). Domyślnie modele są uporządkowane według wyniku metryk po ich zakończeniu. Dla tego samouczka model, który ocenia najwyższy poziom w oparciu o wybraną **AUC_weighted** metrykę, znajduje się w górnej części listy.

Podczas oczekiwania na zakończenie wszystkich modeli eksperymentów wybierz **nazwę algorytmu** kompletnego modelu, aby poznać jego szczegóły wydajności. 

Poniższe informacje umożliwiają nawigowanie po kartach **szczegóły** i **metryki** , aby wyświetlić właściwości, metryki i wykresy wydajności wybranego modelu. 

![Szczegóły przebiegu iteracji](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Wdróż najlepszy model

Zautomatyzowany interfejs uczenia maszynowego umożliwia wdrożenie najlepszego modelu jako usługi sieci Web w kilku krokach. Wdrożenie to integracja modelu, dzięki czemu można przewidzieć nowe dane i identyfikować potencjalne obszary szans sprzedaży. 

W przypadku tego eksperymentu wdrożenie do usługi sieci Web oznacza, że instytucja finansowa ma teraz iteracyjne i skalowalne rozwiązanie sieci Web służące do identyfikowania potencjalnych klientów z krótkoterminowymi wpłatami. 

Sprawdź, czy Twoje uruchomienie eksperymentu zostało zakończone. Aby to zrobić, przejdź z powrotem do strony uruchomienia nadrzędnego, wybierając pozycję **Uruchom 1** w górnej części ekranu. Stan **ukończony** jest pokazywany w lewym górnym rogu ekranu. 

Po zakończeniu eksperymentu na stronie **szczegółów** zostanie wypełniona **Najlepsza sekcja podsumowująca model** . W tym kontekście eksperymentu **VotingEnsemble** jest uznawany za najlepszy model na podstawie metryki **AUC_weighted** .  

Wdrażamy ten model, ale zaleca się wdrożenie trwa około 20 minut. Proces wdrażania obejmuje kilka czynności, takich jak rejestrowanie modelu, Generowanie zasobów i konfigurowanie ich dla usługi sieci Web.

1. Wybierz pozycję **VotingEnsemble** , aby otworzyć stronę specyficzną dla modelu.

1. Wybierz przycisk **Wdróż** w lewym górnym rogu.

1. Wypełnij okienko **Wdróż model** w następujący sposób:

    Pole| Wartość
    ----|----
    Nazwa wdrożenia| My-automl-Deploy
    Opis wdrożenia| Moje pierwsze zautomatyzowane wdrożenie eksperymentu w usłudze Machine Learning
    Typ środowiska obliczeniowego | Wybierz wystąpienie obliczeniowe platformy Azure (ACI)
    Włącz uwierzytelnianie| Wyłącz. 
    Korzystanie z wdrożeń niestandardowych| Wyłącz. Zezwala na Autogenerowanie domyślnego pliku sterownika (skrypt oceniania) i pliku środowiska. 
    
    W tym przykładzie używamy ustawień domyślnych, które są dostępne w menu *Zaawansowane* . 

1. Wybierz pozycję **Wdróż**.  

    W górnej części ekranu **uruchamiania** zostanie wyświetlony zielony komunikat o powodzeniu, a w okienku **Podsumowanie modelu** zostanie wyświetlony komunikat o stanie w obszarze **Wdróż stan**. Należy okresowo wybierać pozycję **Odśwież** , aby sprawdzić stan wdrożenia.
    
Teraz masz działającą usługę sieci Web do generowania prognoz. 

Przejdź do [**następnych kroków**](#next-steps) , aby dowiedzieć się więcej na temat korzystania z nowej usługi sieci Web i testowania prognoz przy użyciu Power BI wbudowanej Azure Machine Learning obsługi.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Pliki wdrożeń są większe niż pliki danych i eksperymenty, dzięki czemu są one droższe do przechowywania. Usuń tylko pliki wdrożenia, aby zminimalizować koszty dla konta, lub jeśli chcesz zachować obszar roboczy i pliki eksperymentów. W przeciwnym razie Usuń całą grupę zasobów, jeśli nie planujesz używać żadnego z tych plików.  

### <a name="delete-the-deployment-instance"></a>Usuwanie wystąpienia wdrożenia

Usuń tylko wystąpienie wdrożenia z Azure Machine Learning na https: \/ /ml.Azure.com/, jeśli chcesz zachować grupę zasobów i obszar roboczy dla innych samouczków i eksploracji. 

1. Przejdź do [Azure Machine Learning](https://ml.azure.com/). Przejdź do obszaru roboczego i po lewej stronie okienka **elementy zawartości** wybierz pozycję **punkty końcowe**. 

1. Wybierz wdrożenie, które chcesz usunąć, a następnie wybierz pozycję **Usuń**. 

1. Wybierz pozycję **Zastosuj**.

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanym uczenia maszynowego użyto zautomatyzowanego interfejsu ML Azure Machine Learning, aby utworzyć i wdrożyć model klasyfikacji. Zobacz następujące artykuły, aby uzyskać więcej informacji i następnych kroków:

> [!div class="nextstepaction"]
> [Używanie usługi internetowej](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Dowiedz się więcej o [automatycznym uczeniu maszynowym](concept-automated-ml.md).
+ Aby uzyskać więcej informacji na temat metryk i wykresów klasyfikacji, zobacz artykuł [Omówienie automatycznego uczenia maszynowego](how-to-understand-automated-ml.md#classification) .
+ Dowiedz się więcej o [cechowania](how-to-configure-auto-features.md#featurization).
+ Dowiedz się więcej na temat [profilowania danych](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Ten zestaw danych marketingu dla banku jest udostępniany w ramach [licencji Creative Commons Attribution (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Wszystkie prawa do poszczególnych treści bazy danych są licencjonowane w ramach [licencji na zawartość bazy danych](https://creativecommons.org/publicdomain/zero/1.0/) i dostępne w witrynie [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ten zestaw danych był początkowo dostępny w [bazie danych Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez i P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing (Podejście oparte na danych do prognozowania powodzenia telemarketingu bankowego). Decision Support Systems, Elsevier, 62:22-31, czerwiec 2014.

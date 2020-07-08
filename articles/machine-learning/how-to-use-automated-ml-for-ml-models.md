---
title: Tworzenie modeli & wdrażanie przy użyciu programu AutoML
titleSuffix: Azure Machine Learning
description: Twórz, Przeglądaj i wdrażaj automatyczne modele uczenia maszynowego za pomocą Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: aniththa
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/20/2020
ms.openlocfilehash: 9871d2ef46a4bbcaa0de7a2aee7d2c91f2bfefab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831917"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Twórz, Przeglądaj i wdrażaj automatyczne modele uczenia maszynowego za pomocą Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane modele uczenia maszynowego bez jednego wiersza kodu w interfejsie Azure Machine Learning Studio. Automatyczne Uczenie maszynowe to proces, w którym jest wybierany najlepszy algorytm uczenia maszynowego dla konkretnych danych. Ten proces umożliwia szybkie generowanie modeli uczenia maszynowego. [Dowiedz się więcej o automatycznym uczeniu maszynowym](concept-automated-ml.md).
 
Aby zapoznać się z kompleksowym przykładem, wypróbuj [Samouczek dotyczący tworzenia modelu klasyfikacji przy użyciu zautomatyzowanego interfejsu ML Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

W przypadku środowiska języka Python można [skonfigurować automatyczne eksperymenty uczenia maszynowego](how-to-configure-auto-train.md) za pomocą zestawu SDK Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Obszar roboczy Azure Machine Learning z typem **wersji Enterprise Edition**. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).  Aby uaktualnić istniejący obszar roboczy do wersji Enterprise, zobacz [uaktualnianie do wersji Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zaloguj się do Azure Machine Learning pod adresem https://ml.azure.com . 

1. Wybierz swoją subskrypcję i obszar roboczy. 

1. Przejdź do okienka po lewej stronie. Wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

[![Okienko nawigacji Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Jeśli po raz pierwszy przeprowadzisz eksperymenty, zobaczysz pustą listę i linki do dokumentacji. 

W przeciwnym razie zostanie wyświetlona lista ostatnich zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, w tym tych utworzonych za pomocą zestawu SDK. 

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz pozycję **+ Nowy zautomatyzowany przebieg** i wypełnij formularz.

1. Wybierz zestaw danych z kontenera magazynu lub Utwórz nowy zestaw danych. Zestawy danych mogą być tworzone na podstawie plików lokalnych, adresów URL sieci Web, magazynów danych lub otwartych zestawów danych platformy Azure. 

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w formie tabelarycznej.
    >* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych z pliku na komputerze lokalnym, wybierz pozycję **Przeglądaj** , a następnie wybierz plik. 

    1. Nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** , aby otworzyć **formularz magazyn danych i wybór pliku**. Na tym formularzu wybierasz miejsce przekazania zestawu danych; domyślny kontener magazynu, który jest automatycznie tworzony razem z obszarem roboczym, lub wybierz kontener magazynu, który ma być używany do eksperymentu. 

    1. Przejrzyj **Ustawienia i Podgląd** w formularzu pod kątem dokładności. Formularz jest inteligentnie wypełniany na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Jeden lub więcej znaków do określenia granicy między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych.
        Encoding| Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.
    
        Wybierz pozycję **Dalej**.

    1. Formularz **schematu** jest inteligentnie wypełniany na podstawie opcji wybranych w formularzu **Ustawienia i Podgląd** . W tym miejscu należy skonfigurować typ danych dla każdej kolumny, sprawdzić nazwy kolumn i wybrać kolumny, które **nie mają być dołączone** do eksperymentu. 
            
        Wybierz pozycję **Dalej.**

    1. Formularz **Potwierdź szczegóły** to podsumowanie informacji poprzednio wypełnionych w **podstawowych informacjach** i ustawieniach oraz w formularzach **wersji zapoznawczej** . Istnieje również możliwość utworzenia profilu danych dla zestawu danych przy użyciu obliczeń z włączoną obsługą profilowania. Dowiedz się więcej na temat [profilowania danych](#profile).

        Wybierz pozycję **Dalej**.
1. Wybierz nowo utworzony zestaw danych, gdy zostanie wyświetlony. Możliwe jest również wyświetlanie podglądu zestawu danych i przykładowych statystyk. 

1. W formularzu **Konfigurowanie przebiegu** wprowadź unikatową nazwę eksperymentu.

1. Wybierz kolumnę docelową; jest to kolumna, dla której chcesz przeprowadzić prognozę.

1. Wybierz obliczenia dla zadania profilowania i szkolenia danych. Lista istniejących obliczeń jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami podanymi w kroku 7.

1. Wybierz pozycję **Utwórz nowe obliczenie** , aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obliczeniowa| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.
    Minimalna/Maksymalna liczba węzłów (w ustawieniach zaawansowanych)| Aby profilować dane, musisz określić co najmniej jeden węzeł. Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów na potrzeby obliczeń AML.
    
    Wybierz pozycję **Utwórz**. Tworzenie nowego obliczenia może potrwać kilka minut.

    >[!NOTE]
    > Nazwa obliczeniowa wskazuje, czy w przypadku obliczeń, które zostały wybrane/utworzone, *włączono profilowanie*. (Szczegółowe informacje znajdują się w sekcji [Profilowanie danych](#profile) ).

    Wybierz pozycję **Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz typ zadania: Klasyfikacja, regresja lub prognozowanie. Aby uzyskać więcej informacji, zobacz [obsługiwane typy zadań](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) .

    1. W przypadku klasyfikacji można również włączyć uczenie głębokie, które jest używane dla featurizations tekstu.

    1. W przypadku prognozowania:
        1. Wybierz kolumnę czasu: Ta kolumna zawiera dane czasu, które mają być używane.

        1. Wybierz horyzont prognoz: wskaż, ile jednostek czasu (min/godzina/dni/tygodnie/miesiące/lata) będzie można przewidzieć na przyszłość. Dalszy model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. Obowiązkowe Wyświetl ustawienia konfiguracji dodawania: dodatkowe ustawienia, których można użyć w celu lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Dodatkowe konfiguracje|Opis
    ------|------
    Metryka podstawowa| Główna Metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#explore-model-metrics).
    Automatyczne cechowania| Wybierz, aby włączyć lub wyłączyć cechowania wykonane przez automatyczne Uczenie maszynowe. Automatyczna cechowania obejmuje automatyczne czyszczenie danych, przygotowanie i transformację do generowania funkcji syntetycznych. Nieobsługiwane w przypadku typu zadania prognozowanie szeregów czasowych. [Dowiedz się więcej o cechowania](how-to-configure-auto-features.md#featurization). 
    Wyjaśnij najlepszy model | Wybierz, aby włączyć lub wyłączyć, aby pokazać wyjaśnienie zalecanego najlepszego modelu
    Zablokowany algorytm| Wybierz algorytmy, które mają zostać wykluczone z zadania szkoleniowego.
    Kryterium zakończenia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zatrzymane. <br> *Czas zadania szkoleniowego (godz.)*: jak długo zezwolić na uruchomienie zadania szkoleniowego. <br> *Próg wyniku metryki*: minimalny wynik metryki dla wszystkich potoków. Dzięki temu w przypadku zdefiniowania metryki docelowej, która ma zostać osiągnięta, nie poświęcasz więcej czasu na zadanie szkoleniowe niż to konieczne.
    Walidacja| Wybierz jedną z opcji krzyżowego sprawdzania poprawności, która ma zostać użyta w zadaniu szkoleniowym. [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności](how-to-configure-cross-validation-data-splits.md#prerequisites).
    Współbieżność| *Maksymalna liczba współbieżnych iteracji*: maksymalną liczbę potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie zostanie uruchomione więcej niż określona liczba iteracji.

1. Obowiązkowe Wyświetl ustawienia cechowania: Jeśli zdecydujesz się włączyć **Automatyczne cechowania** w formularzu **ustawień konfiguracji dodatkowej** , zostaną zastosowane domyślne techniki cechowania. W **widoku cechowania ustawienia** można zmienić te ustawienia domyślne i odpowiednio dostosować. Dowiedz się, jak [dostosować featurizations](#customize-featurization). 

    ![Formularz typu zadania Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Dane statystyczne podsumowania &

Możesz uzyskać szeroką gamę statystyk podsumowujących dla zestawu danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia. W przypadku kolumn nieliczbowych składają się tylko podstawowe dane statystyczne, takie jak minimalna, maksymalna i liczba błędów. W przypadku kolumn liczbowych można także sprawdzić ich statystyczny czas i oszacować quantiles. Profil danych zawiera następujące dane:

>[!NOTE]
> Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

Statystyka|Opis
------|------
Cecha| Nazwa sumowanej kolumny.
Profil| Wizualizacja w wierszu oparta na wywnioskowanym typie. Na przykład ciągi, wartości logiczne i daty będą mieć liczby wartości, podczas gdy miejsca dziesiętne (liczbowe) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.
Dystrybucja typów| Liczba wartości w wierszu dla typów w kolumnie. Wartości null są własnym typem, więc Wizualizacja jest przydatna do wykrywania nieparzystych lub brakujących wartości.
Typ|Wywnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.
Min.| Minimalna wartość kolumny. Puste wpisy są wyświetlane dla funkcji, których typ nie ma własnej kolejności (np. wartości logicznych).
Maks.| Maksymalna wartość kolumny. 
Liczba| Łączna liczba brakujących i nieobecnych wpisów w kolumnie.
Liczba niebrakujących| Liczba wpisów w kolumnie, których nie ma. Puste ciągi i błędy są traktowane jako wartości, więc nie będą wchodzić w skład "niebrakującej liczby".
Quantiles| Przybliżone wartości dla każdego quantileu, aby zapewnić rozkład danych.
Średnia| Średnia arytmetyczna kolumny lub jej średnia.
Odchylenie standardowe| Pomiar wielkości rozproszenia lub zmienności danych tej kolumny.
Wariancja| Mierzona, jak daleko odłożenie danych z tej kolumny pochodzi z wartości średniej. 
Skośność| Mierzona, jak różne dane tej kolumny pochodzą z rozkładu normalnego.
Kurtoza| Mierzona, jak silnie naśladowanie danych tej kolumny jest porównywane z rozkładem normalnym.

## <a name="customize-featurization"></a>Dostosuj cechowania

W formularzu **cechowania** można włączyć/wyłączyć automatyczne cechowania i dostosować ustawienia automatycznej cechowania dla eksperymentu. Aby otworzyć ten formularz, zobacz krok 10 w sekcji [Tworzenie i uruchamianie eksperymentu](#create-and-run-experiment) . 

Poniższa tabela zawiera podsumowanie dostosowań, które są obecnie dostępne za pośrednictwem programu Studio. 

Kolumna| Dostosowywanie
---|---
Dołączono | Określa, które kolumny mają być uwzględnione w szkoleniu.
Typ funkcji| Zmień typ wartości dla zaznaczonej kolumny.
Za pomocą| Wybierz wartość, do której mają być przypisane wartości w danych.

![Formularz typu zadania Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Wybierz pozycję **Zakończ** , aby uruchomić eksperyment. Proces przygotowywania eksperymentu może potrwać do 10 minut. Zadania trenowania mogą zająć kolejne 2–3 minuty dla każdego potoku.

### <a name="view-experiment-details"></a>Wyświetlanie szczegółów eksperymentu

Zostanie otwarty ekran Szczegóły **uruchamiania** na karcie **szczegóły** . Na tym ekranie przedstawiono podsumowanie przebiegu eksperymentu, w tym pasek stanu u góry obok numeru uruchomienia. 

Karta **Modele** zawiera listę utworzonych modeli uporządkowaną według wyników metryk. Domyślnie model, który dla wybranej metryki uzyska najlepszy wynik, znajduje się na początku listy. W miarę wypróbowywania przez zadanie trenowania kolejnych modeli są one dodawane do listy. Umożliwia ona szybkie porównanie metryk modeli wyprodukowanych do tej pory.

[![Pulpit nawigacyjny szczegółów uruchamiania](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Wyświetl szczegóły przebiegu szkoleniowego

Przejdź do szczegółów dowolnego z zakończonych modeli, aby zobaczyć szczegóły przebiegu szkoleniowego, takie jak uruchamianie metryk na karcie **Szczegóły modelu** lub na wykresach wydajności na karcie **wizualizacje** . [Dowiedz się więcej na temat wykresów](how-to-understand-automated-ml.md).

[![Szczegóły iteracji](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Gdy optymalny model jest dostępny, można go wdrożyć jako usługę sieci Web, aby przewidzieć nowe dane.

Automatyczna ML pomaga wdrożyć model bez pisania kodu:

1. Istnieje kilka opcji wdrażania. 

    + Opcja 1: Aby wdrożyć najlepszy model (zgodnie ze zdefiniowanymi kryteriami metryki), wybierz przycisk **Wdróż najlepszy model** na karcie **szczegóły** .

    + Opcja 2: Aby wdrożyć określoną iterację modelu z tego eksperymentu, przejdź do szczegółów modelu, aby otworzyć kartę **Szczegóły modelu** i wybierz pozycję **Wdróż model**.

1. Wypełnij okienko **Wdróż model** .

    Pole| Wartość
    ----|----
    Nazwa| Wprowadź unikatową nazwę wdrożenia.
    Opis| Wprowadź opis, aby lepiej zidentyfikować to wdrożenie.
    Typ obliczenia| Wybierz typ punktu końcowego, który chcesz wdrożyć: *Azure Kubernetes Service (AKS)* lub *Azure Container Instance (ACI)*.
    Nazwa obliczeniowa| *Dotyczy tylko AKS:* Wybierz nazwę klastra AKS, w którym chcesz wdrożyć.
    Włącz uwierzytelnianie | Wybierz, aby zezwalać na uwierzytelnianie oparte na tokenach lub na podstawie klucza.
    Używanie niestandardowych zasobów wdrażania| Włącz tę funkcję, jeśli chcesz przekazać własny skrypt oceniania i plik środowiska. [Dowiedz się więcej o skryptach oceniania](how-to-deploy-and-where.md#script).

    >[!Important]
    > Nazwy plików muszą mieć długość 32 znaków i muszą zaczynać się i kończyć znakiem alfanumerycznym. Może zawierać łączniki, podkreślenia, kropki i znaki alfanumeryczne między. Spacje są niedozwolone.

    Menu *Zaawansowane* oferuje domyślne funkcje wdrażania, takie jak [gromadzenie danych](how-to-enable-app-insights.md) i ustawienia wykorzystania zasobów. Jeśli chcesz przesłonić te ustawienia domyślne, zrób to w menu.

1. Wybierz pozycję **Wdróż**. Wdrożenie może potrwać około 20 minut.
    Po rozpoczęciu wdrożenia zostanie wyświetlona karta **Szczegóły modelu** . Postęp wdrażania można znaleźć w sekcji **wdrażanie stanu** okienka **Właściwości** . 

Teraz masz działającą usługę sieci Web do generowania prognoz! Możesz przetestować przewidywania, wykonując zapytania dotyczące usługi, korzystając z [Power BI wbudowanej Azure Machine Learning obsługi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z usługi sieci Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* Zapoznaj się z [automatycznymi wynikami uczenia maszynowego](how-to-understand-automated-ml.md).
* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i Azure Machine Learning.

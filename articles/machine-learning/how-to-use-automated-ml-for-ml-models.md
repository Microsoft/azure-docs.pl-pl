---
title: Tworzenie modeli & wdrażanie przy użyciu programu AutoML
titleSuffix: Azure Machine Learning
description: Twórz, Przeglądaj i wdrażaj automatyczne modele uczenia maszynowego za pomocą programu Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 1a40fe01240474c2a6df3e028b7d03f3e8bb73fc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879751"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Twórz, Przeglądaj i wdrażaj automatyczne modele uczenia maszynowego za pomocą Azure Machine Learning


W tym artykule dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane modele uczenia maszynowego bez pojedynczego wiersza kodu w Azure Machine Learning Studio.

Automatyczne Uczenie maszynowe to proces, w którym jest wybierany najlepszy algorytm uczenia maszynowego dla konkretnych danych. Ten proces umożliwia szybkie generowanie modeli uczenia maszynowego. [Dowiedz się więcej o automatycznym uczeniu maszynowym](concept-automated-ml.md).
 
Aby zapoznać się z kompleksowym przykładem, wypróbuj [Samouczek dotyczący tworzenia modelu klasyfikacji przy użyciu zautomatyzowanego interfejsu ML Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

W przypadku środowiska języka Python można [skonfigurować automatyczne eksperymenty uczenia maszynowego](how-to-configure-auto-train.md) za pomocą zestawu SDK Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md). 

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zaloguj się do Azure Machine Learning pod adresem https://ml.azure.com . 

1. Wybierz swoją subskrypcję i obszar roboczy. 

1. Przejdź do okienka po lewej stronie. Wybierz pozycję **zautomatyzowany ml** w sekcji **autor** .

[![Okienko nawigacji Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Jeśli po raz pierwszy przeprowadzisz eksperymenty, zobaczysz pustą listę i linki do dokumentacji. 

W przeciwnym razie zostanie wyświetlona lista ostatnich zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, w tym tych utworzonych za pomocą zestawu SDK. 

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz pozycję **+ Nowy zautomatyzowany przebieg** i wypełnij formularz.

1. Wybierz zestaw danych z kontenera magazynu lub Utwórz nowy zestaw danych. Zestawy danych mogą być tworzone na podstawie plików lokalnych, adresów URL sieci Web, magazynów danych lub otwartych zestawów danych platformy Azure. Dowiedz się więcej o [tworzeniu zestawu danych](how-to-create-register-datasets.md).  

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w formie tabelarycznej.
    >* Wartość, która ma zostać przewidywalna (kolumna docelowa), musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych z pliku na komputerze lokalnym, wybierz pozycję **+ Utwórz zestaw danych** , a następnie wybierz pozycję **z pliku lokalnego**. 

    1. W **podstawowym formularzu informacji** nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz pozycję **dalej** , aby otworzyć **formularz magazyn danych i wybór pliku**. Na tym formularzu wybierasz miejsce przekazania zestawu danych; domyślny kontener magazynu, który jest automatycznie tworzony razem z obszarem roboczym, lub wybierz kontener magazynu, który ma być używany do eksperymentu. 
    
        1. Jeśli dane znajdują się za siecią wirtualną, należy włączyć funkcję **pomijania walidacji** , aby upewnić się, że obszar roboczy ma dostęp do danych. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure](how-to-enable-studio-virtual-network.md). 
    
    1. Wybierz pozycję **Przeglądaj** , aby przekazać plik danych dla zestawu danych. 

    1. Przejrzyj **Ustawienia i Podgląd** w formularzu pod kątem dokładności. Formularz jest inteligentnie wypełniany na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Jeden lub więcej znaków do określenia granicy między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innymi strumieniami danych.
        Encoding| Identyfikuje tablicę znaków, która ma być używana do odczytywania zestawu danych.
        Nagłówki kolumn| Wskazuje, w jaki sposób nagłówki zestawu danych (jeśli istnieją) będą traktowane.
        Pomiń wiersze | Wskazuje, ile (jeśli istnieją) wiersze są pomijane w zestawie danych.
    
        Wybierz opcję **Dalej**.

    1. Formularz **schematu** jest inteligentnie wypełniany na podstawie opcji wybranych w formularzu **Ustawienia i Podgląd** . W tym miejscu należy skonfigurować typ danych dla każdej kolumny, sprawdzić nazwy kolumn i wybrać kolumny, które **nie mają być dołączone** do eksperymentu. 
            
        Wybierz pozycję **Dalej**.

    1. Formularz **Potwierdź szczegóły** to podsumowanie informacji poprzednio wypełnionych w **podstawowych informacjach** i ustawieniach oraz w formularzach **wersji zapoznawczej** . Istnieje również możliwość utworzenia profilu danych dla zestawu danych przy użyciu obliczeń z włączoną obsługą profilowania. Dowiedz się więcej na temat [profilowania danych](how-to-connect-data-ui.md#profile).

        Wybierz opcję **Dalej**.
1. Wybierz nowo utworzony zestaw danych, gdy zostanie wyświetlony. Możliwe jest również wyświetlanie podglądu zestawu danych i przykładowych statystyk. 

1. W formularzu **Konfigurowanie przebiegu** wybierz pozycję **Utwórz nową** i wprowadź **samouczek-automl-Deploy** dla nazwy eksperymentu.

1. Wybierz kolumnę docelową; jest to kolumna, dla której chcesz przeprowadzić prognozę.

1. Wybierz obliczenia dla zadania profilowania i szkolenia danych. Lista istniejących obliczeń jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenie, postępuj zgodnie z instrukcjami podanymi w kroku 7.

1. Wybierz pozycję **Utwórz nowe obliczenie** , aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obiektu obliczeniowego| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Priorytet maszyny wirtualnej| Maszyny wirtualne o niskim priorytecie są tańsze, ale nie gwarantują węzłów obliczeniowych. 
    Typ maszyny wirtualnej| Wybierz procesor CPU lub GPU dla typu maszyny wirtualnej.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczenia.
    Minimalna/Maksymalna liczba węzłów| Aby profilować dane, musisz określić co najmniej jeden węzeł. Wprowadź maksymalną liczbę węzłów dla obliczeń. Wartość domyślna to 6 węzłów na potrzeby obliczeń AML.
    Ustawienia zaawansowane | Te ustawienia umożliwiają skonfigurowanie dla eksperymentu konta użytkownika i istniejącej sieci wirtualnej. 
    
    Wybierz przycisk **Utwórz**. Tworzenie nowego obliczenia może potrwać kilka minut.

    >[!NOTE]
    > Nazwa obliczeniowa wskazuje, czy w przypadku obliczeń, które zostały wybrane/utworzone, *włączono profilowanie*. (Szczegółowe informacje znajdują się w sekcji [Profilowanie danych](how-to-connect-data-ui.md#profile) ).

    Wybierz opcję **Dalej**.

1. W formularzu **Typ zadania i ustawienia** wybierz typ zadania: Klasyfikacja, regresja lub prognozowanie. Aby uzyskać więcej informacji, zobacz [obsługiwane typy zadań](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) .

    1. W przypadku **klasyfikacji** można również włączyć uczenie głębokie.
    
        Jeśli uczenie głębokie jest włączone, sprawdzanie poprawności jest ograniczone do _train_validation Split_. [Dowiedz się więcej o opcjach walidacji](how-to-configure-cross-validation-data-splits.md).


    1. Do **prognozowania** można, 
    
        1. Włącz uczenie głębokie.
    
        1. Wybierz *kolumnę czasu*: Ta kolumna zawiera dane czasu, które mają być używane.

        1. Wybierz *horyzont prognoz*: wskaż, ile jednostek czasu (min/godzina/dni/tygodnie/miesiące/lata) będzie można przewidzieć na przyszłość. Dalszy model jest wymagany do przewidywania w przyszłości, tym mniej dokładne stanie się. [Dowiedz się więcej o prognozowaniu i prognozowaniu horyzontu](how-to-auto-train-forecast.md).

1. Obowiązkowe Wyświetl ustawienia konfiguracji dodawania: dodatkowe ustawienia, których można użyć w celu lepszego kontrolowania zadania szkoleniowego. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Dodatkowe konfiguracje|Opis
    ------|------
    Metryka podstawowa| Główna Metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu](how-to-configure-auto-train.md#primary-metric).
    Wyjaśnij najlepszy model | Wybierz, aby włączyć lub wyłączyć, aby wyświetlić wyjaśnienia dla zalecanego najlepszego modelu. <br> Ta funkcja nie jest obecnie dostępna w przypadku [niektórych algorytmów prognozowania](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model). 
    Zablokowany algorytm| Wybierz algorytmy, które mają zostać wykluczone z zadania szkoleniowego. <br><br> Zezwalanie na algorytmy jest dostępne tylko dla [eksperymentów zestawu SDK](how-to-configure-auto-train.md#supported-models). <br> Zobacz [obsługiwane modele dla każdego typu zadania](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels?preserve-view=true&view=azure-ml-py).
    Kryterium zakończenia| Po spełnieniu któregokolwiek z tych kryteriów zadanie szkolenia zostanie zatrzymane. <br> *Czas zadania szkoleniowego (godz.)*: jak długo zezwolić na uruchomienie zadania szkoleniowego. <br> *Próg wyniku metryki*: minimalny wynik metryki dla wszystkich potoków. Dzięki temu w przypadku zdefiniowania metryki docelowej, która ma zostać osiągnięta, nie poświęcasz więcej czasu na zadanie szkoleniowe niż to konieczne.
    Walidacja| Wybierz jedną z opcji krzyżowego sprawdzania poprawności, która ma zostać użyta w zadaniu szkoleniowym. <br> [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności](how-to-configure-cross-validation-data-splits.md#prerequisites).<br> <br>Prognozowanie obsługuje tylko wzajemne sprawdzanie poprawności.
    Współbieżność| *Maksymalna liczba współbieżnych iteracji*: maksymalną liczbę potoków (iteracji) do przetestowania w zadaniu szkoleniowym. Zadanie nie zostanie uruchomione więcej niż określona liczba iteracji. Dowiedz się więcej o tym, jak zautomatyzowanej ML wykonuje [wiele podrzędnych przebiegów w klastrach](how-to-configure-auto-train.md#multiple-child-runs-on-clusters).

1. Obowiązkowe Wyświetl ustawienia cechowania: Jeśli zdecydujesz się włączyć **Automatyczne cechowania** w formularzu **ustawień konfiguracji dodatkowej** , zostaną zastosowane domyślne techniki cechowania. W **widoku cechowania ustawienia** można zmienić te ustawienia domyślne i odpowiednio dostosować. Dowiedz się, jak [dostosować featurizations](#customize-featurization). 

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie typu zadania z nazwami Wyświetl cechowania.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Dostosuj cechowania

W formularzu **cechowania** można włączyć/wyłączyć automatyczne cechowania i dostosować ustawienia automatycznej cechowania dla eksperymentu. Aby otworzyć ten formularz, zobacz krok 10 w sekcji [Tworzenie i uruchamianie eksperymentu](#create-and-run-experiment) . 

Poniższa tabela zawiera podsumowanie dostosowań, które są obecnie dostępne za pośrednictwem programu Studio. 

Kolumna| Dostosowywanie
---|---
Dołączono | Określa, które kolumny mają być uwzględnione w szkoleniu.
Typ funkcji| Zmień typ wartości dla zaznaczonej kolumny.
Za pomocą| Wybierz wartość, do której mają być przypisane wartości w danych.

![Cechowania Azure Machine Learning Studio Custom](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Wybierz pozycję **Zakończ** , aby uruchomić eksperyment. Proces przygotowywania eksperymentu może potrwać do 10 minut. Zadania trenowania mogą zająć kolejne 2–3 minuty dla każdego potoku.

> [!NOTE]
> Algorytmy zautomatyzowanej sieci mają nieodłączną losowość, która może spowodować nieznaczne wahania w końcowym wyniku metryk, na przykład dokładność. Zautomatyzowanej ML wykonuje również operacje na danych, takie jak podzielenie testów pociągowych, podzielone lub krzyżowe sprawdzanie poprawności w razie potrzeby. Dlatego w przypadku uruchamiania eksperymentu z tymi samymi ustawieniami konfiguracji i metryką podstawową wiele razy prawdopodobnie zobaczysz zmiany w przypadku wszystkich eksperymentów końcowych metryk, ze względu na te czynniki. 

### <a name="view-experiment-details"></a>Wyświetlanie szczegółów eksperymentu

Zostanie otwarty ekran Szczegóły **uruchamiania** na karcie **szczegóły** . Na tym ekranie przedstawiono podsumowanie przebiegu eksperymentu, w tym pasek stanu u góry obok numeru uruchomienia. 

Karta **Modele** zawiera listę utworzonych modeli uporządkowaną według wyników metryk. Domyślnie model, który dla wybranej metryki uzyska najlepszy wynik, znajduje się na początku listy. W miarę wypróbowywania przez zadanie trenowania kolejnych modeli są one dodawane do listy. Umożliwia ona szybkie porównanie metryk modeli wyprodukowanych do tej pory.

![Szczegóły uruchamiania](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Wyświetl szczegóły przebiegu szkoleniowego

Przejdź do szczegółów dowolnego z zakończonych modeli, aby zobaczyć szczegóły przebiegu szkoleniowego, na przykład podsumowanie modelu na karcie **model** lub wykresy metryk wydajności na karcie **metryki** . [Dowiedz się więcej na temat wykresów](how-to-understand-automated-ml.md).

[![Szczegóły iteracji](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Po uzyskaniu najlepszego modelu można wdrożyć go w postaci usługi internetowej w celu tworzenia przewidywań na podstawie nowych danych.

Zautomatyzowane uczenie maszynowe ułatwia wdrażanie modelu bez pisania kodu:

1. Istnieje kilka opcji wdrażania. 

    + Opcja 1: wdrożenie najlepszego modelu zgodnie ze zdefiniowanymi kryteriami metryki. 
        1. Po zakończeniu eksperymentu przejdź do strony nadrzędnej uruchomienia, wybierając pozycję **Uruchom 1** w górnej części ekranu. 
        1.  Wybierz model z listy w sekcji **najlepsze podsumowanie modelu** . 
        1. Wybierz pozycję **Wdróż** w lewym górnym rogu okna. 

    + Opcja 2: Aby wdrożyć określoną iterację modelu z tego eksperymentu.
        1. Wybierz odpowiedni model z karty **Modele**
        1. Wybierz pozycję **Wdróż** w lewym górnym rogu okna.

1. Wypełnij okienko **Wdróż model** .

    Pole| Wartość
    ----|----
    Nazwa| Wprowadź unikatową nazwę wdrożenia.
    Opis| Wprowadź opis, aby lepiej zidentyfikować to wdrożenie.
    Typ środowiska obliczeniowego| Wybierz typ punktu końcowego, który chcesz wdrożyć: *Azure Kubernetes Service (AKS)* lub *Azure Container Instance (ACI)*.
    Nazwa obiektu obliczeniowego| *Dotyczy tylko AKS:* Wybierz nazwę klastra AKS, w którym chcesz wdrożyć.
    Włącz uwierzytelnianie | Wybierz, aby zezwalać na uwierzytelnianie oparte na tokenach lub na podstawie klucza.
    Używanie niestandardowych zasobów wdrażania| Włącz tę funkcję, jeśli chcesz przekazać własny skrypt oceniania i plik środowiska. [Dowiedz się więcej o skryptach oceniania](how-to-deploy-and-where.md).

    >[!Important]
    > Nazwy plików muszą mieć długość 32 znaków i muszą zaczynać się i kończyć znakiem alfanumerycznym. Pomiędzy nimi mogą znajdować się łączniki, podkreślenia, kropki i znaki alfanumeryczne. Spacje są niedozwolone.

    W menu *Zaawansowane* znajdują się domyślne funkcje wdrażania takie jak [zbieranie danych](how-to-enable-app-insights.md) i ustawienia wykorzystania zasobów. Jeśli chcesz zastąpić te ustawienia domyślne, skorzystaj z tego menu.

1. Wybierz pozycję **Wdróż**. Wdrażanie może potrwać około 20 minut.
    Po rozpoczęciu wdrażania zostanie wyświetlona karta **Podsumowanie modelu**. Postęp wdrażania jest widoczny w sekcji **Stan wdrożenia**. 

Masz teraz działającą usługę internetową umożliwiającą generowanie przewidywań. Możesz przetestować przewidywania, wykonując zapytania do usługi za pomocą [wbudowanej obsługi usługi Azure Machine Learning w usłudze Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z usługi sieci Web](how-to-consume-web-service.md).
* Zapoznaj się z [automatycznymi wynikami uczenia maszynowego](how-to-understand-automated-ml.md).
* [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym](concept-automated-ml.md) i Azure Machine Learning.
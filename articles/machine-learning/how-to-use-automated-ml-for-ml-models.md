---
title: Używanie rozwiązania AutoML do tworzenia modeli & wdrażania
titleSuffix: Azure Machine Learning
description: Tworzenie, przeglądanie i wdrażanie modeli zautomatyzowanego uczenia maszynowego za pomocą Azure Machine Learning studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 5718e0e3732f57b46500f9d2cdb1165e883ca44f
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575585"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Tworzenie, przeglądanie i wdrażanie modeli zautomatyzowanego uczenia maszynowego za pomocą Azure Machine Learning


Z tego artykułu dowiesz się, jak tworzyć, eksplorować i wdrażać zautomatyzowane modele uczenia maszynowego bez użycia jednego wiersza kodu w Azure Machine Learning studio.

Zautomatyzowane uczenie maszynowe to proces, w którym wybierany jest najlepszy algorytm uczenia maszynowego do użycia dla konkretnych danych. Ten proces umożliwia szybkie generowanie modeli uczenia maszynowego. [Dowiedz się więcej o zautomatyzowanym uczeniu maszynowym.](concept-automated-ml.md)
 
Aby uzyskać end-to-end przykład, wypróbuj samouczek dotyczący tworzenia modelu klasyfikacji przy [użyciu Azure Machine Learning zautomatyzowanego interfejsu uczenia maszynowego firmy .](tutorial-first-experiment-automated-ml.md) 

Aby uzyskać środowisko oparte na kodzie Python, [skonfiguruj eksperymenty zautomatyzowanego uczenia maszynowego](how-to-configure-auto-train.md) przy użyciu Azure Machine Learning SDK.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md) 

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com). 

1. Wybierz swoją subskrypcję i obszar roboczy. 

1. Przejdź do okienka po lewej stronie. Wybierz **pozycję Zautomatyzowane uczenia maszynowego** w **sekcji** Tworzenie.

[![Azure Machine Learning studio nawigacji](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Jeśli przeprowadzasz eksperymenty po raz pierwszy, zobaczysz pustą listę i linki do dokumentacji. 

W przeciwnym razie zostanie wyświetlona lista ostatnich eksperymentów zautomatyzowanego uczenia maszynowego, w tym tych utworzonych za pomocą zestawu SDK. 

## <a name="create-and-run-experiment"></a>Tworzenie i uruchamianie eksperymentu

1. Wybierz **pozycję + Nowy przebieg zautomatyzowanego uczenia maszynowego** i wypełnij formularz.

1. Wybierz zestaw danych z kontenera magazynu lub utwórz nowy zestaw danych. Zestawy danych można tworzyć na podstawie plików lokalnych, internetowych adresów URL, magazynów danych lub otwartych zestawów danych platformy Azure. Dowiedz się więcej o [tworzeniu zestawu danych.](how-to-create-register-datasets.md)  

    >[!Important]
    > Wymagania dotyczące danych szkoleniowych:
    >* Dane muszą być w postaci tabelarowej.
    >* Wartość, którą chcesz przewidzieć (kolumna docelowa), musi być obecna w danych.

    1. Aby utworzyć nowy zestaw danych na podstawie pliku na komputerze lokalnym, wybierz **pozycję +Utwórz** zestaw danych, a następnie wybierz **pozycję Z pliku lokalnego.** 

    1. W **formularzu Informacje podstawowe** nadaj zestawowi danych unikatową nazwę i podaj opcjonalny opis. 

    1. Wybierz **przycisk Dalej,** aby otworzyć **formularz wyboru magazynu** danych i pliku . W tym formularzu możesz wybrać miejsce przekazywania zestawu danych. domyślny kontener magazynu, który jest automatycznie tworzony w obszarze roboczym, lub wybierz kontener magazynu, którego chcesz użyć do eksperymentu. 
    
        1. Jeśli dane są za siecią wirtualną, należy włączyć funkcję **pomijania** walidacji, aby upewnić się, że obszar roboczy może uzyskać dostęp do danych. Aby uzyskać więcej informacji, zobacz Use Azure Machine Learning studio in an Azure virtual network (Używanie usługi [Azure Machine Learning studio sieci wirtualnej platformy Azure).](how-to-enable-studio-virtual-network.md) 
    
    1. Wybierz **pozycję Przeglądaj,** aby przekazać plik danych dla zestawu danych. 

    1. Sprawdź dokładność **w formularzu** Ustawienia i podgląd. Formularz jest wypełniany inteligentnie na podstawie typu pliku. 

        Pole| Opis
        ----|----
        Format pliku| Definiuje układ i typ danych przechowywanych w pliku.
        Ogranicznik| Co najmniej jeden znak określający granicę między oddzielnymi, niezależnymi regionami w postaci zwykłego tekstu lub innych strumieni danych.
        Encoding| Określa bit, który ma być znakem tabeli schematu do użycia do odczytywania zestawu danych.
        Nagłówki kolumn| Wskazuje sposób, w jaki będą traktowane nagłówki zestawu danych(jeśli są).
        Pomijanie wierszy | Wskazuje, ile wierszy (jeśli istnieją) jest pomijanych w zestawie danych.
    
        Wybierz opcję **Dalej**.

    1. Formularz **Schemat** jest wypełniany inteligentnie na podstawie wyborów w **formularzu Ustawienia i podgląd.** W tym miejscu skonfiguruj typ danych dla każdej kolumny, przejrzyj nazwy kolumn i wybierz kolumny, które mają **być nieujmowane** w eksperymencie. 
            
        Wybierz pozycję **Dalej**.

    1. Formularz **Potwierdzanie** szczegółów jest podsumowaniem informacji wcześniej  wypełnionych w formularzach Informacje podstawowe i **Ustawienia oraz podglądu.** Możesz również utworzyć profil danych dla zestawu danych przy użyciu obliczeń z włączonym profilowaniem. Dowiedz się więcej [o profilowaniu danych.](how-to-connect-data-ui.md#profile)

        Wybierz opcję **Dalej**.
1. Wybierz nowo utworzony zestaw danych, gdy zostanie wyświetlony. Możesz również wyświetlić podgląd zestawu danych i przykładowe statystyki. 

1. W **formularzu Konfigurowanie uruchamiania** wybierz pozycję **Utwórz nowy** i wprowadź **wartość Tutorial-automl-deploy** jako nazwę eksperymentu.

1. Wybierz kolumnę docelową; jest to kolumna, na podstawie których chcesz wykonać przewidywania.

1. Wybierz obliczenia dla zadania profilowania i trenowania danych. Lista istniejących zasobów obliczeniowych jest dostępna na liście rozwijanej. Aby utworzyć nowe obliczenia, postępuj zgodnie z instrukcjami w kroku 7.

1. Wybierz **pozycję Utwórz nowe obliczenie,** aby skonfigurować kontekst obliczeniowy dla tego eksperymentu.

    Pole|Opis
    ---|---
    Nazwa obiektu obliczeniowego| Wprowadź unikatową nazwę identyfikującą kontekst obliczeniowy.
    Priorytet maszyny wirtualnej| Maszyny wirtualne o niskim priorytecie są tańsze, ale nie gwarantują węzłów obliczeniowych. 
    Typ maszyny wirtualnej| Wybierz pozycję Procesor CPU lub PROCESOR GPU jako typ maszyny wirtualnej.
    Rozmiar maszyny wirtualnej| Wybierz rozmiar maszyny wirtualnej dla obliczeń.
    Minimalna/maksymalna liczba węzłów| Aby profilować dane, należy określić co najmniej jeden węzeł. Wprowadź maksymalną liczbę węzłów obliczeniowych. Wartość domyślna to 6 węzłów dla obliczeń usługi AML.
    Ustawienia zaawansowane | Te ustawienia umożliwiają skonfigurowanie konta użytkownika i istniejącej sieci wirtualnej dla eksperymentu. 
    
    Wybierz przycisk **Utwórz**. Utworzenie nowego wystąpienia obliczeniowego może potrwać kilka minut.

    >[!NOTE]
    > Nazwa wystąpienia obliczeniowego wskaże, czy wybrane/tworzyć zasoby obliczeniowe *są włączona profilowania.* (Aby uzyskać więcej informacji, [zobacz sekcję Profilowanie](how-to-connect-data-ui.md#profile) danych).

    Wybierz opcję **Dalej**.

1. W **formularzu Typ zadania i ustawienia** wybierz typ zadania: klasyfikacja, regresja lub prognozowanie. Aby [uzyskać więcej informacji, zobacz obsługiwane](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) typy zadań.

    1. W **przypadku klasyfikacji** można również włączyć uczenie głębokie.
    
        Jeśli uczenie głębokie jest włączone, walidacja jest ograniczona do _train_validation podziału._ [Dowiedz się więcej o opcjach walidacji.](how-to-configure-cross-validation-data-splits.md)


    1. W **celu prognozowania** można: 
    
        1. Włączanie uczenia głębokiego.
    
        1. Wybierz *kolumnę czasu:* ta kolumna zawiera dane czasu, które mają być używane.

        1. Wybierz *horyzont prognozy:* wskaż, ile jednostek czasu (minuty/godziny/dni/tygodnie/miesiące/lata) będzie w stanie przewidzieć model w przyszłości. Dalsze przewidywanie modelu w przyszłości jest wymagane, tym mniej dokładny stanie się model. [Dowiedz się więcej o horyzontach prognozowania i prognozowania.](how-to-auto-train-forecast.md)

1. (Opcjonalnie) Wyświetl dodatkowe ustawienia konfiguracji: dodatkowe ustawienia, których można użyć, aby lepiej kontrolować zadanie trenowania. W przeciwnym razie wartości domyślne są stosowane na podstawie wyboru eksperymentu i danych. 

    Dodatkowe konfiguracje|Opis
    ------|------
    Metryka podstawowa| Główna metryka używana do oceniania modelu. [Dowiedz się więcej o metrykach modelu.](how-to-configure-auto-train.md#primary-metric)
    Wyjaśnienie najlepszego modelu | Wybierz opcję włączenia lub wyłączenia, aby wyświetlić wyjaśnienia zalecanego najlepszego modelu. <br> Ta funkcja nie jest obecnie dostępna dla [niektórych algorytmów prognozowania.](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model) 
    Algorytm zablokowany| Wybierz algorytmy, które chcesz wykluczyć z zadania trenowania. <br><br> Zezwalanie na algorytmy jest dostępne tylko dla [eksperymentów zestawu SDK.](how-to-configure-auto-train.md#supported-models) <br> Zobacz obsługiwane [modele dla każdego typu zadania.](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels)
    Kryterium zakończenia| Gdy którekolwiek z tych kryteriów zostanie spełnione, zadanie trenowania zostanie zatrzymane. <br> *Czas zadania szkoleniowego (godziny):* jak długo ma być uruchamiane zadanie trenowania. <br> *Próg oceny metryki:* minimalna ocena metryki dla wszystkich potoków. Dzięki temu, jeśli masz zdefiniowaną metrykę docelową, którą chcesz osiągnąć, nie będziesz poświęcać więcej czasu na zadanie trenowania niż jest to konieczne.
    Walidacja| Wybierz jedną z opcji krzyżowej weryfikacji do użycia w zadaniu trenowania. <br> [Dowiedz się więcej na temat krzyżowego sprawdzania poprawności.](how-to-configure-cross-validation-data-splits.md#prerequisites)<br> <br>Prognozowanie obsługuje tylko k-krotne krzyżowe sprawdzanie poprawności.
    Współbieżność| *Maksymalna liczba współbieżnych iteracji:* maksymalna liczba potoków (iteracji) do testowania w zadaniu trenowania. Zadanie nie będzie uruchamiane więcej niż określona liczba iteracji. Dowiedz się więcej o tym, jak zautomatyzowane mleowanie [wykonuje wiele przebiegów podrzędnych w klastrach.](how-to-configure-auto-train.md#multiple-child-runs-on-clusters)

1. (Opcjonalnie) Wyświetl ustawienia cechowania: jeśli włączysz opcję  Automatyczna **cechowanie** w formularzu Dodatkowe ustawienia konfiguracji, zostaną zastosowane domyślne techniki cechowania. W **ustawieniach Wyświetlania cech** można zmienić te wartości domyślne i odpowiednio je dostosować. Dowiedz się, [jak dostosować cechowanie.](#customize-featurization) 

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie typu zadania z wywołaną oknie dialogowym Wyświetl ustawienia cechowania.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>Dostosowywanie cech

W **formularzu Cechowanie** możesz włączyć/wyłączyć automatyczną cechowanie i dostosować ustawienia automatycznej cechowania eksperymentu. Aby otworzyć ten formularz, zobacz krok 10 w [sekcji Tworzenie i uruchamianie eksperymentu.](#create-and-run-experiment) 

W poniższej tabeli przedstawiono podsumowanie dostosowań dostępnych obecnie za pośrednictwem programu Studio. 

Kolumna| Dostosowywanie
---|---
Dołączono | Określa, które kolumny mają być dołączane do trenowania.
Typ funkcji| Zmień typ wartości dla wybranej kolumny.
Przekieruj za pomocą| Wybierz wartość, za pomocą której chcesz w danych przekieerowyć brakujące wartości.

![Azure Machine Learning studio cechowanie niestandardowe](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Uruchamianie eksperymentu i wyświetlanie wyników

Wybierz **pozycję Zakończ,** aby uruchomić eksperyment. Proces przygotowywania eksperymentu może potrwać do 10 minut. Zadania trenowania mogą zająć kolejne 2–3 minuty dla każdego potoku.

> [!NOTE]
> Algorytmy zautomatyzowanego uczenia maszynowego wykorzystują naturalną losowość, która może powodować niewielkie odchylenia w końcowym wskaźniku metryk zalecanego modelu, na przykład dokładność. Zautomatyzowane uczenia maszynowego wykonuje również operacje na danych, takie jak podział trenowania i testowania, podział trenowania i walidacja krzyżowa, gdy jest to konieczne. Jeśli więc wielokrotnie uruchamiasz eksperyment z tymi samymi ustawieniami konfiguracji i metryką podstawową, prawdopodobnie zobaczysz różnice w końcowym wyniku metryk w każdym eksperymentze z powodu tych czynników. 

### <a name="view-experiment-details"></a>Wyświetlanie szczegółów eksperymentu

Zostanie **otwarty ekran Szczegóły** uruchomienia na **karcie** Szczegóły. Na tym ekranie przedstawiono podsumowanie uruchomienia eksperymentu, w tym pasek stanu u góry obok numeru uruchomienia. 

Karta **Modele** zawiera listę utworzonych modeli uporządkowaną według wyników metryk. Domyślnie model, który dla wybranej metryki uzyska najlepszy wynik, znajduje się na początku listy. W miarę wypróbowywania przez zadanie trenowania kolejnych modeli są one dodawane do listy. Umożliwia ona szybkie porównanie metryk modeli wyprodukowanych do tej pory.

![Szczegóły uruchomienia](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Wyświetlanie szczegółów przebiegów trenowania

Przejdź do szczegółów wszystkich ukończonych modeli, aby wyświetlić szczegóły przebiegów trenowania, takie jak podsumowanie modelu na karcie **Model** lub wykresy metryk wydajności na karcie **Metryki.** Dowiedz się więcej [o wykresach.](how-to-understand-automated-ml.md)

[![Szczegóły iteracji](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations-preview"></a>Wyjaśnienia modelu (wersja zapoznawcza)

Aby lepiej zrozumieć model, możesz zobaczyć, które funkcje danych (nieprzetworzone lub zaprojektowane) wpłynęły na przewidywania modelu, za pomocą pulpitu nawigacyjnego z wyjaśnieniami modelu. 

Pulpit nawigacyjny wyjaśnienia modelu zawiera ogólną analizę wytrenowany model wraz z jego przewidywaniami i wyjaśnieniami. Umożliwia również przechodzenie do szczegółów pojedynczego punktu danych i jego ważności poszczególnych funkcji. [Dowiedz się więcej o wizualizacjach pulpitu nawigacyjnego wyjaśnienia.](how-to-machine-learning-interpretability-aml.md#visualizations)

Aby uzyskać wyjaśnienia dotyczące określonego modelu, 

1. Na **karcie Modele** wybierz model, który chcesz zrozumieć. 
1. Wybierz przycisk **Wyjaśnij model** i podaj obliczenia, których można użyć do wygenerowania wyjaśnień.
1. Sprawdź stan **na karcie Podrzędne** przebiegi. 
1. Po zakończeniu przejdź do karty **Wyjaśnienia (wersja zapoznawcza),** która zawiera pulpit nawigacyjny z wyjaśnieniami. 

    ![Pulpit nawigacyjny wyjaśnienia modelu](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Wdrażanie modelu

Po uzyskaniu najlepszego modelu można wdrożyć go w postaci usługi internetowej w celu tworzenia przewidywań na podstawie nowych danych.

>[!TIP]
> Jeśli chcesz wdrożyć model, który został wygenerowany za pośrednictwem pakietu z zestawem SDK języka Python, musisz zarejestrować `automl` [model w](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) obszarze roboczym. 
>
> Po zarejestrowaniu modelu znajdź go w studio, wybierając pozycję **Modele** w okienku po lewej stronie. Po otwarciu modelu możesz wybrać  przycisk Wdróć w górnej części ekranu, a następnie postępować zgodnie z instrukcjami opisanymi w kroku **2** sekcji **Wdrażanie** modelu.

Zautomatyzowane uczenie maszynowe ułatwia wdrażanie modelu bez pisania kodu:

1. Dostępnych jest kilka opcji wdrażania. 

    + Opcja 1. Wdrożenie najlepszego modelu zgodnie ze zdefiniowanymi kryteriami metryki. 
        1. Po zakończeniu eksperymentu przejdź do nadrzędnej strony uruchamiania, wybierając pozycję **Uruchom 1** w górnej części ekranu. 
        1.  Wybierz model wymieniony w sekcji **Najlepsze podsumowanie** modelu. 
        1. Wybierz **pozycję Deploy** (Wd wdrażaj) w lewym górnym rogu okna. 

    + Opcja 2. Aby wdrożyć określoną iterację modelu z tego eksperymentu.
        1. Wybierz odpowiedni model z karty **Modele**
        1. Wybierz **pozycję Deploy** (Wd wdrażaj) w lewym górnym rogu okna.

1. Wypełnij okienko **Wdrażanie modelu.**

    Pole| Wartość
    ----|----
    Nazwa| Wprowadź unikatową nazwę wdrożenia.
    Opis| Wprowadź opis, aby lepiej zidentyfikować, do czego służy to wdrożenie.
    Typ środowiska obliczeniowego| Wybierz typ punktu końcowego, który chcesz wdrożyć: *Azure Kubernetes Service (AKS)* lub *Azure Container Instance (ACI).*
    Nazwa obiektu obliczeniowego| *Dotyczy tylko aks:* Wybierz nazwę klastra usługi AKS, w który chcesz wdrożyć.
    Włącz uwierzytelnianie | Wybierz opcję , aby zezwolić na uwierzytelnianie oparte na tokenach lub kluczach.
    Korzystanie z niestandardowych zasobów wdrożenia| Włącz tę funkcję, jeśli chcesz przekazać własny skrypt oceniania i plik środowiska. [Dowiedz się więcej o skryptach oceniania](how-to-deploy-and-where.md).

    >[!Important]
    > Nazwy plików muszą być poniżej 32 znaków i muszą zaczynać się i kończyć alfanumeryczne. Pomiędzy nimi mogą znajdować się łączniki, podkreślenia, kropki i znaki alfanumeryczne. Spacje są niedozwolone.

    W menu *Zaawansowane* znajdują się domyślne funkcje wdrażania takie jak [zbieranie danych](how-to-enable-app-insights.md) i ustawienia wykorzystania zasobów. Jeśli chcesz zastąpić te ustawienia domyślne, skorzystaj z tego menu.

1. Wybierz pozycję **Wdróż**. Wdrażanie może potrwać około 20 minut.
    Po rozpoczęciu wdrażania zostanie wyświetlona karta **Podsumowanie modelu**. Postęp wdrażania jest widoczny w sekcji **Stan wdrożenia**. 

Masz teraz działającą usługę internetową umożliwiającą generowanie przewidywań. Możesz przetestować przewidywania, wykonując zapytania do usługi za pomocą [wbudowanej obsługi usługi Azure Machine Learning w usłudze Power BI](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z usługi internetowej.](how-to-consume-web-service.md)
* [Poznaj wyniki zautomatyzowanego uczenia maszynowego.](how-to-understand-automated-ml.md)
* [Dowiedz się więcej o zautomatyzowanym uczeniu](concept-automated-ml.md) maszynowym i Azure Machine Learning.
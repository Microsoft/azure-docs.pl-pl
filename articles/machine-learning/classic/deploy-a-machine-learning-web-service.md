---
title: 'ML Studio (klasyczny): wdrażanie usługi sieci Web — Azure'
description: Jak przekonwertować eksperyment szkoleniowy na eksperyment predykcyjny, przygotować go do wdrożenia, a następnie wdrożyć jako usługę sieci Web Azure Machine Learning Studio (klasyczną).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: c9cfe05b6547cbdc61a1c8cc6223f08900cf09d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345055"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Wdrażanie usługi sieci Web Azure Machine Learning Studio (klasycznej)

**dotyczy:** ![ tak ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ nie](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Azure Machine Learning Studio (klasyczny) umożliwia tworzenie i testowanie rozwiązań analitycznych predykcyjnych. Następnie możesz wdrożyć rozwiązanie jako usługę sieci Web.

Machine Learning Studio (klasyczne) usługi sieci Web zapewniają interfejs między aplikacją a modelem oceniania przepływu pracy Machine Learning Studio (klasycznym). Aplikacja zewnętrzna może komunikować się z modelem oceniania przepływu pracy Machine Learning Studio (klasyczny) w czasie rzeczywistym. Wywołanie usługi sieci Web Machine Learning Studio (klasycznej) zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Machine Learning Studio (klasyczny) usługa sieci Web jest oparta na architekturze REST, popularnej architektury dla projektów programowania w sieci Web.

Azure Machine Learning Studio (klasyczny) ma dwa typy usług sieci Web:

* Request-Response Service (RR): małe opóźnienia i wysoce skalowalna usługa, która ocenia pojedynczy rekord danych.
* Usługa wykonywania wsadowego (BES): asynchroniczna usługa służąca do oceniania partii rekordów danych.

Dane wejściowe dla usługi BES przypominają dane wejściowe używane przez usługę RRS. Główna różnica polega na tym, że usługa BES odczytuje blok rekordów z różnych źródeł, takich jak usługa Azure Blob Storage, usługa Azure Table Storage, usługa Azure SQL Database, usługa HDInsight (zapytanie Hive) i źródła HTTP.

Z punktu widzenia wysokiego poziomu można wdrożyć model w trzech krokach:

* **[Twórz eksperymenty szkoleniowe]** — w programie Studio (klasyczne) można uczenie i testowanie modelu analizy predykcyjnej przy użyciu danych szkoleniowych dostarczanych przez użytkownika przy użyciu dużego zestawu wbudowanych algorytmów uczenia maszynowego.
* **[Przekonwertuj ją na eksperyment predykcyjny]** — po przeszkoleniu modelu z istniejącymi danymi i przygotowaniu się do korzystania z niego do oceny nowych danych możesz przygotować i usprawnić eksperymenty do prognoz.
* **Wdrażanie** go jako **[nowej usługi sieci]** Web lub **[klasycznej usługi sieci Web]** — w przypadku wdrożenia eksperymentu predykcyjnego jako usługi sieci Web platformy Azure użytkownicy mogą wysyłać dane do modelu i odbierać przewidywania modeli.

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkoleniowego

Aby przeprowadzić uczenie modelu analizy predykcyjnej, należy użyć Azure Machine Learning Studio (klasyczny) do utworzenia eksperymentu szkoleniowego, w którym można uwzględnić różne moduły do ładowania danych szkoleniowych, przygotować dane w razie potrzeby, zastosować algorytmy uczenia maszynowego i oszacować wyniki. Możesz wykonywać iterację eksperymentu i próbować użyć różnych algorytmów uczenia maszynowego, aby porównać i oszacować wyniki.

Proces tworzenia eksperymentów szkoleniowych i zarządzania nimi został szczegółowo omówiony w innym miejscu. Więcej informacji można znaleźć w następujących artykułach:

* [Tworzenie prostego eksperymentu w Azure Machine Learning Studio (klasyczny)](create-experiment.md)
* [Opracowywanie rozwiązania predykcyjnego za pomocą Azure Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md)
* [Importowanie danych szkoleniowych do Azure Machine Learning Studio (klasyczne)](import-data.md)
* [Zarządzanie iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu trenowania w eksperyment predykcyjny

Po przeszkoleniu modelu możesz rozpocząć konwersję eksperymentu szkoleniowego na eksperyment predykcyjny, który będzie oceniać nowe dane.

Dzięki konwersji na eksperyment predykcyjny jest gotowy do wdrożenia przeszkolony model jako usługa sieci Web oceniania. Użytkownicy usługi sieci Web mogą wysyłać dane wejściowe do modelu, a Twój model wyśle wyniki przewidywania. Podczas konwertowania na eksperyment predykcyjny należy pamiętać o tym, jak oczekujemy, że model ma być używany przez inne osoby.

Proces konwersji eksperymentu szkoleniowego na eksperyment predykcyjny obejmuje trzy kroki:

1. Zastąp moduły algorytmu uczenia maszynowego modelem szkolonym.
2. Przytnij eksperyment tylko do modułów, które są konieczne do oceny. Eksperyment szkoleniowy obejmuje wiele modułów, które są niezbędne do uczenia się, ale nie są potrzebne, gdy model jest szkolony.
3. Zdefiniuj, w jaki sposób model akceptuje dane od użytkownika usługi sieci Web i jakie dane zostaną zwrócone.

> [!TIP]
> W doświadczeniu szkoleniowym Jesteś w trakcie szkoleń i oceniamy model przy użyciu własnych danych. Po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwracają wyniki prognozowania. Dlatego podczas konwertowania eksperymentu szkoleniowego na eksperyment predykcyjny, aby przygotować go do wdrożenia, należy pamiętać, jak model będzie używany przez inne osoby.

![Konwertuj na eksperyment oceniania](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Przycisk usługi sieci Web
Po uruchomieniu eksperymentu (kliknij pozycję **Uruchom** u dołu kanwy eksperymentu) kliknij przycisk **Konfiguruj usługę sieci Web** (wybierz opcję **predykcyjnej usługi sieci Web** ). **Konfiguracja usługi sieci Web** wykonuje trzy kroki konwertowania eksperymentu szkoleniowego na eksperyment predykcyjny:

1. Model ten jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów (po lewej stronie kanwy eksperymentu). Spowoduje to zamienienie algorytmu uczenia maszynowego i [przeszkolenie modułów modelu][train-model] z zapisanym przeszkolonym modelem.
2. Analizuje eksperyment i usuwa moduły, które były jasno używane tylko do szkolenia i nie są już potrzebne.
3. Wstawia moduły danych wejściowych i _wyjściowych_ _usługi sieci Web_ do domyślnych lokalizacji w Twoim eksperymentie (te moduły akceptują i zwracają dane użytkownika).

Na przykład następujące eksperymenty pociąga za pomocą dwuklasowego modelu drzewa decyzyjnego przy użyciu przykładowych danych spisu:

![Eksperyment szkoleniowy](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduły w tym eksperymentie wykonują zasadniczo cztery różne funkcje:

![Funkcje modułu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Podczas konwertowania tego eksperymentu szkoleniowego na eksperyment predykcyjny niektóre z tych modułów nie są już potrzebne lub są teraz w innym celu:

* **Dane** — dane w tym przykładowym zestawie danych nie są używane podczas oceniania — użytkownik usługi sieci Web dostarczy dane do oceny. Jednak metadane z tego zestawu danych, takie jak typy danych, są używane przez szkolony model. Dlatego należy zachować zestaw danych w eksperymentie predykcyjnym, aby umożliwić mu dostarczenie tych metadanych.

* **Przygotowanie** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, te moduły mogą lub nie muszą być wymagane do przetwarzania danych przychodzących. Przycisk **Konfiguruj usługę sieci Web** nie dotyka tych — musisz zdecydować, jak chcesz je obsłużyć.
  
    Na przykład, w tym przykładzie przykładowy zestaw danych może mieć brakujące wartości, więc dołączono do nich [nieoczyszczony moduł danych][clean-missing-data] . Ponadto przykładowy zestaw danych zawiera kolumny, które nie są konieczne do uczenia modelu. W związku z tym dodano [kolumny SELECT w module DataSet][select-columns] , aby wykluczyć te dodatkowe kolumny z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane na potrzeby oceniania za pomocą usługi sieci Web, nie mają brakujących wartości, możesz usunąć [nieczysty moduł danych][clean-missing-data] . Jednak ponieważ moduł [Wybierz kolumny w zestawie danych][select-columns] ułatwia definiowanie kolumn danych oczekiwanych przez szkolony model, ten moduł musi pozostać.

* **Uczenie** — te moduły są używane do uczenia modelu. Po kliknięciu przycisku **Konfiguruj usługę sieci Web**te moduły są zastępowane jednym modułem zawierającym przeszkolony model. Ten nowy moduł jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów.

* **Wynik** — w tym przykładzie moduł [Split Data][split] jest używany do dzielenia strumienia danych na dane testowe i dane szkoleniowe. W eksperymentie predykcyjnym nie jesteśmy już szkoleniami, więc można usunąć [dane podzielone][split] . Analogicznie, drugi moduł [modelu][score-model] oceny i moduł [oceny modelu][evaluate-model] są używane do porównywania wyników danych testowych, więc te moduły nie są konieczne w przypadku eksperymentu predykcyjnego. Moduł pozostały [model oceny][score-model] jest jednak wymagany do zwrócenia wyniku oceny przez usługę sieci Web.

Oto jak wygląda przykład po kliknięciu przycisku **Skonfiguruj usługę sieci Web**:

![Przekonwertowany eksperyment predykcyjny](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Działanie wykonywane przez **usługę sieci Web** może być wystarczające, aby przygotować eksperyment do wdrożenia jako usługę sieci Web. Jednak może być konieczne wykonanie pewnych dodatkowych czynności związanych z eksperymentem.

#### <a name="adjust-input-and-output-modules"></a>Dostosowywanie modułów wejściowych i wyjściowych
W doświadczeniu szkoleniowym użyto zestawu danych szkoleniowych, a następnie przetworzysz dane w postaci, w której jest wymagany algorytm uczenia maszynowego. Jeśli dane, które mają zostać odebrane przez usługę sieci Web, nie będą potrzebne do tego przetwarzania, można je ominąć: Połącz dane wyjściowe **modułu danych wejściowych usługi sieci Web** z innym modułem w Twoim eksperymentie. Dane użytkownika zostaną teraz odebrane w modelu w tej lokalizacji.

Na przykład **Usługa sieci Web** domyślnie umieszcza moduł **wejściowy usługi sieci Web** w górnej części przepływu danych, jak pokazano na rysunku powyżej. Można jednak ręcznie ustawić dane **wejściowe usługi sieci Web** za pomocą modułów przetwarzania danych:

![Przeniesienie danych wejściowych usługi sieci Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Dane wejściowe dostarczane za pomocą usługi sieci Web będą teraz przekazywane bezpośrednio do modułu modelu wynikowego bez konieczności przetwarzania wstępnego.

Podobnie domyślnie **Konfiguracja usługi** sieci Web powoduje umieszczenie modułu wyjściowego usługi sieci Web u dołu przepływu danych. W tym przykładzie usługa sieci Web zwróci do użytkownika dane wyjściowe modułu [modelu wynikowego][score-model] , który zawiera kompletny wektor danych wejściowych i wyniki oceniania.
Jeśli jednak wolisz zwrócić coś innego, możesz dodać kolejne moduły przed modułem **wyjściowym usługi sieci Web** . 

Aby na przykład zwrócić tylko wyniki oceny, a nie cały wektor danych wejściowych, Dodaj do modułu DataSet ( [Wybieranie kolumn w zestawie danych][select-columns] ) wszystkie kolumny z wyjątkiem wyników oceniania. Następnie Przenieś moduł **wyjściowy usługi sieci Web** do danych wyjściowych modułu [SELECT Columns in DataSet][select-columns] . Eksperyment wygląda następująco:

![Przeniesienie danych wyjściowych usługi sieci Web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie dodatkowych modułów przetwarzania danych
Jeśli eksperyment zawiera więcej modułów, które nie są potrzebne podczas oceniania, można je usunąć. Na przykład ze względu na to, że moduł danych **wejściowych usługi sieci Web** został przeniesiony do punktu po modułach przetwarzania danych, możemy usunąć [nieczysty moduł danych][clean-missing-data] z eksperymentu predykcyjnego.

Nasz eksperyment predykcyjny wygląda teraz następująco:

![Usuwanie dodatkowego modułu](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Dodaj opcjonalne parametry usługi sieci Web
W niektórych przypadkach może być konieczne zezwolenie użytkownikowi usługi sieci Web na zmianę zachowania modułów podczas uzyskiwania dostępu do usługi. *Parametry usługi sieci Web* umożliwiają wykonanie tej czynności.

Typowym przykładem jest skonfigurowanie modułu [importowania danych][import-data] , dzięki czemu użytkownik wdrożonej usługi sieci Web może określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci Web. Lub skonfiguruj moduł [eksportu danych][export-data] , aby można było określić inną lokalizację docelową.

Można definiować parametry usługi sieci Web i kojarzyć je z jednym lub wieloma parametrami modułu, a także określić, czy są one wymagane, czy opcjonalne. Użytkownik usługi sieci Web dostarcza wartości tych parametrów podczas uzyskiwania dostępu do usługi, a akcje modułu są odpowiednio modyfikowane.

Więcej informacji o parametrach usługi sieci Web i sposobach ich użycia można znaleźć w temacie [Using Azure Machine Learning Web Service Parameters][webserviceparameters].

Poniższe kroki opisują wdrożenie eksperymentu predykcyjnego jako nowej usługi sieci Web. Możesz również wdrożyć eksperyment jako klasyczną usługę sieci Web.

## <a name="deploy-it-as-a-new-web-service"></a>Wdróż ją jako nową usługę sieci Web

Teraz, gdy eksperyment predykcyjny został przygotowany, możesz go wdrożyć jako nową usługę sieci Web platformy Azure (Menedżer zasobów). Korzystając z usługi sieci Web, użytkownicy mogą wysyłać dane do modelu, a model zwróci swoje przewidywania.

Aby wdrożyć eksperyment predykcyjny, kliknij pozycję **Uruchom** w dolnej części kanwy eksperymentu. Po zakończeniu eksperymentu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [Nowy]**.  Zostanie otwarta strona wdrożenie portalu usługi sieci Web Machine Learning Studio (klasyczny).

> [!NOTE] 
> Aby wdrożyć nową usługę sieci Web, musisz mieć wystarczające uprawnienia w subskrypcji, w której wdrażana jest usługa sieci Web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Strona wdrożenia portalu usługi sieci Web

Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci Web.
Wybierz plan cenowy. Jeśli masz istniejący plan cenowy, możesz go wybrać, w przeciwnym razie musisz utworzyć nowy plan cenowy dla usługi.

1. Z listy rozwijanej **Plan cen** wybierz istniejący plan lub wybierz opcję **Wybierz nowy plan** .
2. W polu **Nazwa planu**wpisz nazwę, która będzie identyfikować plan na rachunku.
3. Wybierz jedną z **miesięcznych warstw planu**. Plan domyślnie jest dzielony na warstwy planów dla domyślnego regionu, a usługi internetowe są wdrażane dla tego regionu.

Kliknij przycisk **Wdróż** , a strona **szybkiego startu** dla usługi sieci Web zostanie otwarta.

Strona szybkiego startu usługi sieci Web zapewnia dostęp i wskazówki dotyczące najbardziej typowych zadań, które zostaną wykonane po utworzeniu usługi sieci Web. W tym miejscu możesz łatwo uzyskać dostęp do strony testowej i strony użycia.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testowanie nowej usługi sieci Web

Aby przetestować nową usługę sieci Web, kliknij pozycję **Testuj usługę sieci Web** w obszarze Typowe zadania. Na stronie test można testować usługę sieci Web jako usługę Request-Response (RR) lub usługę wykonywania wsadowego (BES).

Na stronie test RR są wyświetlane dane wejściowe, wyjściowe i wszystkie parametry globalne zdefiniowane dla eksperymentu. Aby przetestować usługę sieci Web, można ręcznie wprowadzić odpowiednie wartości dla wejść lub podać plik z wartościami rozdzielanymi przecinkami (CSV) zawierający wartości testowe.

Aby przetestować przy użyciu rekordów RR, w trybie widoku listy wprowadź odpowiednie wartości danych wejściowych i kliknij pozycję **Testuj żądanie-odpowiedź**. Wyniki przewidywania są wyświetlane w kolumnie dane wyjściowe po lewej stronie.

![Wprowadź odpowiednie wartości, aby przetestować usługę sieci Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować BES, kliknij pozycję **Batch**. Na stronie test wsadowy kliknij pozycję Przeglądaj w obszarze dane wejściowe i wybierz plik CSV zawierający odpowiednie przykładowe wartości. Jeśli nie masz pliku CSV i utworzysz eksperyment predykcyjny przy użyciu Machine Learning Studio (klasyczny), możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

Aby pobrać zestaw danych, Otwórz Machine Learning Studio (klasyczny). Otwórz eksperyment predykcyjny i kliknij prawym przyciskiem myszy dane wejściowe dla eksperymentu. Z menu kontekstowego wybierz pozycję **zestaw danych** , a następnie wybierz pozycję **Pobierz**.

![Pobierz zestaw danych z kanwy programu Studio (klasycznej)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij przycisk **Testuj**. Stan zadania wykonywania wsadowego jest wyświetlany w prawej części **testu zadań wsadowych**.

![Testowanie zadania wykonywania wsadowego za pomocą portalu usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stronie **Konfiguracja** można zmienić opis, tytuł, zaktualizować klucz konta magazynu i włączyć przykładowe dane dla usługi sieci Web.

![Konfigurowanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Uzyskaj dostęp do nowej usługi sieci Web

Po wdrożeniu usługi sieci Web z Machine Learning Studio (klasycznej) można wysyłać dane do usługi i programowo odbierać odpowiedzi.

Na stronie **Używanie** znajdują się wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci Web. Na przykład klucz interfejsu API jest dostarczany, aby zezwolić na autoryzowany dostęp do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning Studio (klasycznej), zobacz [jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Zarządzanie nową usługą sieci Web

Możesz zarządzać nowymi usługami sieci Web przy użyciu portalu usług sieci Web Machine Learning Studio (klasycznego). Na [stronie Portal główny](https://services.azureml.net/)kliknij pozycję **usługi sieci Web**. Na stronie usługi sieci Web można usunąć lub skopiować usługę. Aby monitorować określoną usługę, kliknij usługę, a następnie kliknij pozycję **pulpit nawigacyjny**. Aby monitorować zadania wsadowe skojarzone z usługą sieci Web, kliknij pozycję **Dziennik żądań wsadowych**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Wdrażanie nowej usługi sieci Web w wielu regionach

Nową usługę sieci Web można łatwo wdrożyć w wielu regionach bez konieczności stosowania wielu subskrypcji lub obszarów roboczych.

Cennik jest specyficzny dla regionu, dlatego należy zdefiniować plan rozliczeniowy dla każdego regionu, w którym zostanie wdrożona usługa sieci Web.

#### <a name="create-a-plan-in-another-region"></a>Tworzenie planu w innym regionie

1. Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Kliknij opcję **plany** w menu.
3. Na stronie plany na Widok kliknij pozycję **Nowy**.
4. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której będzie znajdować się nowy plan.
5. Z listy rozwijanej **region** wybierz region dla nowego planu. Opcje planu dla wybranego regionu będą wyświetlane w sekcji **Opcje planu** na stronie.
6. Z listy rozwijanej **Grupa zasobów** wybierz grupę zasobów dla planu. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Azure Resource Manager przegląd](../../azure-resource-manager/management/overview.md).
7. W polu **Nazwa planu** wpisz nazwę planu.
8. W obszarze **Opcje planu**kliknij poziom rozliczenia dla nowego planu.
9. Kliknij przycisk **Utwórz**.

#### <a name="deploy-the-web-service-to-another-region"></a>Wdrażanie usługi sieci Web w innym regionie

1. Na stronie Microsoft Azure Machine Learning Web Services kliknij opcję menu **usługi sieci Web** .
2. Wybierz usługę sieci Web wdrażaną w nowym regionie.
3. Kliknij przycisk **Kopiuj**.
4. W polu **nazwa usługi sieci Web**wpisz nową nazwę usługi sieci Web.
5. W polu **Opis usługi sieci Web**wpisz opis usługi sieci Web.
6. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której będzie znajdować się nowa usługa sieci Web.
7. Z listy rozwijanej **Grupa zasobów** wybierz grupę zasobów dla usługi sieci Web. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Azure Resource Manager przegląd](../../azure-resource-manager/management/overview.md).
8. Z listy rozwijanej **region** wybierz region, w którym ma zostać wdrożona usługa sieci Web.
9. Z listy rozwijanej **konto magazynu** wybierz konto magazynu, w którym ma być przechowywana usługa sieci Web.
10. Z listy rozwijanej **Plan cen** wybierz plan w regionie wybranym w kroku 8.
11. Kliknij przycisk **Kopiuj**.

## <a name="deploy-it-as-a-classic-web-service"></a>Wdróż ją jako klasyczną usługę sieci Web

Teraz, gdy eksperyment predykcyjny został wystarczająco przygotowany, możesz go wdrożyć jako klasyczną usługę sieci Web platformy Azure. Korzystając z usługi sieci Web, użytkownicy mogą wysyłać dane do modelu, a model zwróci swoje przewidywania.

Aby wdrożyć eksperyment predykcyjny, kliknij pozycję **Uruchom** w dolnej części kanwy eksperymentu, a następnie kliknij pozycję **Wdróż usługę sieci Web**. Usługa sieci Web została skonfigurowana i zostanie umieszczona na pulpicie nawigacyjnym usługi sieci Web.

![Wdrażanie usługi sieci Web z poziomu programu Studio (wersja klasyczna)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testowanie klasycznej usługi sieci Web

Możesz przetestować usługę sieci Web w portalu usług sieci Web Machine Learning Studio (klasyczny) lub Machine Learning Studio (klasyczny).

Aby przetestować usługę sieci Web odpowiedzi na żądanie, kliknij przycisk **Testuj** na pulpicie nawigacyjnym usługi sieci Web. Zostanie wyświetlone okno dialogowe z monitem o podanie danych wejściowych dla usługi. Są to kolumny oczekiwane przez eksperyment oceniania. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci Web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć link **test** Preview, aby przetestować usługę w portalu usług sieci Web Azure Machine Learning Studio (klasyczny), jak pokazano wcześniej w sekcji Nowa usługa sieci Web.

Aby przetestować usługę wykonywania wsadowego, kliknij link Podgląd **testowy** . Na stronie test wsadowy kliknij pozycję Przeglądaj w obszarze dane wejściowe i wybierz plik CSV zawierający odpowiednie przykładowe wartości. Jeśli nie masz pliku CSV i utworzysz eksperyment predykcyjny przy użyciu Machine Learning Studio (klasyczny), możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

![Testowanie usługi internetowej](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stronie **Konfiguracja** można zmienić nazwę wyświetlaną usługi i podać opis. Nazwa i opis są wyświetlane w [Azure Portal](https://portal.azure.com/) , w którym można zarządzać usługami sieci Web.

Można podać opis danych wejściowych, danych wyjściowych i parametrów usługi sieci Web, wprowadzając ciąg dla każdej kolumny w obszarze **schemat wejściowy**, **schemat danych wyjściowych**i **parametr usługi sieci Web**. Opisy te są używane w przykładowej dokumentacji kodu podanej dla usługi sieci Web.

Możesz włączyć rejestrowanie, aby zdiagnozować wszystkie błędy widoczne podczas uzyskiwania dostępu do usługi sieci Web. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania dla usług sieci web Machine Learning Studio (klasycznych)](web-services-logging.md).

![Włączanie rejestrowania w portalu usług sieci Web](./media/publish-a-machine-learning-web-service/figure-4.png)

Możesz również skonfigurować punkty końcowe usługi sieci Web w portalu usług sieci Web Azure Machine Learning, podobnie jak w przypadku procedury pokazanej wcześniej w sekcji Nowa usługa sieci Web. Opcje są różne, można dodać lub zmienić opis usługi, włączyć rejestrowanie i włączyć przykładowe dane do testowania.

### <a name="access-your-classic-web-service"></a>Uzyskiwanie dostępu do klasycznej usługi sieci Web

Po wdrożeniu usługi sieci Web z Azure Machine Learning Studio (klasycznej) można wysyłać dane do usługi i programowo odbierać odpowiedzi.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci Web. Na przykład klucz interfejsu API jest udostępniany w celu zezwolenia na dostęp do usługi, a strony pomocy interfejsu API są udostępniane, aby ułatwić rozpoczęcie pisania kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning Studio (klasycznej), zobacz [jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Zarządzanie klasyczną usługą sieci Web

Istnieją różne akcje, które można wykonać, aby monitorować usługę sieci Web. Można go zaktualizować i usunąć. Oprócz domyślnego punktu końcowego, który jest tworzony podczas wdrażania, można także dodać kolejne punkty końcowe do klasycznej usługi sieci Web.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznym)](manage-workspace.md) i [zarządzaniem usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning Studio (klasycznego)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web
Możesz wprowadzić zmiany w usłudze sieci Web, takie jak aktualizowanie modelu z dodatkowymi danymi szkoleniowymi, i wdrożyć je ponownie, zastępując oryginalną usługę sieci Web.

Aby zaktualizować usługę sieci Web, Otwórz oryginalny eksperyment predykcyjny, który został użyty do wdrożenia usługi sieci Web i Utwórz edytowalną kopię, klikając pozycję **Zapisz jako**. Wprowadź zmiany, a następnie kliknij przycisk **Wdróż usługę sieci Web**.

Ponieważ ten eksperyment został wdrożony wcześniej, zostanie wyświetlony monit o zastępowanie (klasycznej usługi sieci Web) lub aktualizacji (nowej usługi sieci Web) istniejącej usługi. Kliknięcie przycisku **tak** lub **Aktualizuj** powoduje zatrzymanie istniejącej usługi sieci Web i wdrożenie nowego eksperymentu predykcyjnego w jego miejscu.

> [!NOTE]
> Jeśli wprowadzono zmiany w konfiguracji oryginalnej usługi sieci Web, na przykład wprowadzając nową nazwę wyświetlaną lub opis, należy ponownie wprowadzić te wartości.

Jedną z opcji aktualizowania usługi sieci Web jest możliwość programowego ponownego uczenia modelu. Aby uzyskać więcej informacji, zobacz temat ponowne [uczenie modeli Machine Learning Studio (klasycznych)](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać bardziej szczegółowe informacje techniczne na temat działania wdrożenia, zobacz, [jak model Machine Learning Studio (klasyczny) postępuje od eksperymentu do działającej usługi sieci Web](model-progression-experiment-to-web-service.md).

* Aby uzyskać szczegółowe informacje na temat przygotowywania modelu do wdrożenia, zobacz [jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)](deploy-a-machine-learning-web-service.md).

* Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Zobacz [, jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

<!-- internal links -->
[Tworzenie eksperymentu szkoleniowego]: #create-a-training-experiment
[Przekonwertuj ją na eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Nowa usługa sieci Web]: #deploy-it-as-a-new-web-service
[Klasyczna usługa sieci Web]: #deploy-it-as-a-classic-web-service
[Nowy]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

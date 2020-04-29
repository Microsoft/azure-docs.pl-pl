---
title: Przypadek użycia analizy tonacji
titleSuffix: ML Studio (classic) - Azure
description: Jak utworzyć modele analizy tekstu w Azure Machine Learning Studio (klasyczny) przy użyciu modułów do przetwarzania wstępnego tekstu, N-gramów lub wyznaczania wartości skrótu funkcji
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217878"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Tworzenie modelu analizy tonacji w Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Możesz użyć Azure Machine Learning Studio (klasyczny) do kompilowania i operacjonalizować modeli analizy tekstu. Te modele mogą pomóc w rozwiązywaniu problemów, na przykład klasyfikacji dokumentów lub tonacji analizy.

W eksperymentie analizy tekstu zwykle:

1. Czyszczenie i wstępne przetwarzanie zestawu danych tekstowych
2. Wyodrębnij wektory funkcji liczbowych ze wstępnie przetworzonego tekstu
3. Klasyfikowanie klasyfikacji lub modelu regresji
4. Ocena i weryfikacja modelu
5. Wdrażanie modelu w środowisku produkcyjnym

W ramach tego samouczka nauczysz się, jak przeprowadzimy przez model analizy tonacji, korzystając z zestawu danych usługi Amazon Book Reviews (zobacz ten dokument z badaniami "Biographies, Bollywood, wysięgnik-pudłs and Blends": adaptacja domeny do klasyfikacji tonacji "przez John Blitzer, Mark Dredze i Fernando Pereira; Skojarzenie języków obliczeniowych (ACL), 2007.) Ten zestaw danych składa się z wyników przeglądu (1-2 lub 4-5) i swobodnego tekstu. Celem jest przewidywanie oceny: niska (1-2) lub wysoka (4-5).

Eksperymenty omówione w tym samouczku można znaleźć w Azure AI Gallery:

[Prognozowanie przeglądów książek](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prognozowanie przeglądów książek — eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1. Czyszczenie i wstępne przetwarzanie zestawu danych tekstowych
Rozpoczynamy eksperyment, dzieląc wyniki przeglądu na kategorii niskie i wysokie przedziały, aby sformułować problem jako klasyfikację dwóch klas. Korzystamy z modułów [Edytuj metadane](https://msdn.microsoft.com/library/azure/dn905986.aspx) i [Grupuj kategorii wartości](https://msdn.microsoft.com/library/azure/dn906014.aspx) .

![Utwórz etykietę](./media/text-analytics-module-tutorial/create-label.png)

Następnie wyczyśćmy tekst przy użyciu [wstępnie przetworzonego modułu tekstu](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Czyszczenie redukuje szum w zestawie danych, pomaga znaleźć najważniejsze funkcje i poprawić dokładność modelu końcowego. Firma Microsoft usuwa Stop-słowa — typowe słowa, takie jak "" "lub" a ", a także cyfry, znaki specjalne, zduplikowane znaki, adresy e-mail oraz adresy URL. Konwertujemy również tekst na małe litery, lemmatize słowa i wykrywają granice zdania, które są następnie wskazywane przez symbol "| | |" w wstępnie przetworzonym tekście.

![Wstępne przetwarzanie tekstu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co zrobić, jeśli chcesz użyć niestandardowej listy Stop-słowa? Można przekazać ją jako opcjonalne dane wejściowe. Możesz również użyć niestandardowego wyrażenia regularnej składni języka C#, aby zastąpić podciągi i usunąć słowa według części mowy: rzeczowników, czasowników lub przymiotników.

Po zakończeniu przetwarzania wstępnego dane są dzielone na zestawy uczenia i testowania.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Wyodrębnienie wektorów funkcji liczbowych ze wstępnie przetworzonego tekstu
Aby zbudować model dla danych tekstowych, zwykle trzeba przekonwertować tekst dowolnego formularza na wektory funkcji numerycznych. W tym przykładzie używamy [funkcji Wyodrębnij funkcje N-gramów z modułu tekstowego](https://msdn.microsoft.com/library/azure/mt762916.aspx) do przekształcania danych tekstowych w taki format. Ten moduł pobiera kolumnę słów oddzielonych znakami i oblicza słownik słów lub N-gramy wyrazów, które pojawiają się w zestawie danych. Następnie zlicza, ile razy każdy wyraz lub N-gram pojawia się w każdym rekordzie, i tworzy wektory funkcji z tych liczników. W tym samouczku ustawimy rozmiar N-gram na 2, dlatego nasze wektory funkcji obejmują pojedyncze słowa i kombinacje dwóch kolejnych wyrazów.

![Wyodrębnij N gramów](./media/text-analytics-module-tutorial/extract-ngrams.png)

Stosujemy funkcję TF * IDF (częstotliwość inversa Documents) do liczby N-gramów. Takie podejście dodaje wagę wyrazów, które często pojawiają się w pojedynczym rekordzie, ale są rzadkie w całym zestawie danych. Inne opcje obejmują dane binarne, TF i Graph ważenie.

Takie funkcje tekstowe często mają wysokie wymiary. Na przykład jeśli korpus ma 100 000 unikatowych słów, miejsce funkcji będzie miało 100 000 wymiary lub więcej, jeśli są używane N-gramy. Moduł Wyodrębnij funkcje N-gram oferuje zestaw opcji pozwalających ograniczyć Wymiarowanie. Możesz zdecydować się na wykluczanie wyrazów, które są krótkie lub długie lub zbyt rzadko lub zbyt często, aby mieć znaczącą wartość predykcyjną. W tym samouczku wykluczemy N-gramy, które pojawiają się w mniej niż 5 rekordach lub w więcej niż 80% rekordów.

Ponadto można użyć wyboru funkcji, aby wybrać tylko te funkcje, które są najbardziej skorelowane z celem przewidywania. W celu wybrania funkcji 1000 stosuje się funkcję Chi-kwadratowego wyboru. Można wyświetlić słownictwo wybranych wyrazów lub N-gramów, klikając odpowiednie dane wyjściowe modułu Wyodrębnij N-gramy.

Jako alternatywne podejście do korzystania z funkcji Wyodrębnij N-gram można użyć modułu tworzenia skrótów funkcji. Należy pamiętać, że [Funkcja tworzenia skrótów](https://msdn.microsoft.com/library/azure/dn906018.aspx) nie ma możliwości wyboru funkcji kompilacji ani TF * IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3. uczenie klasyfikacji lub model regresji
Teraz tekst został przekształcony w kolumny funkcji liczbowych. Zestaw danych nadal zawiera kolumny ciągów z poprzednich etapów, dlatego użyjemy opcji wybierz kolumny w zestawie danych, aby je wykluczyć.

Następnie korzystamy z [dwuklasowej regresji logistycznej](https://msdn.microsoft.com/library/azure/dn905994.aspx) , aby przewidzieć nasz cel: wysoki lub niski wynik przeglądu. W tym momencie problem z analizą tekstu został przekształcony w zwykły problem z klasyfikacją. Aby ulepszyć model, można użyć narzędzi dostępnych w Azure Machine Learning Studio (klasyczne). Na przykład można eksperymentować z różnymi klasyfikatorami, aby dowiedzieć się, jak dokładne wyniki dają, lub użyć dostrajania parametrów, aby poprawić dokładność.

![Uczenie i Ocena](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4. Ocena i weryfikacja modelu
Jak można sprawdzić poprawność nauczonego modelu? Szacujemy ją względem zestawu danych testowych i Oceń dokładność. Jednak model zapoznaje słownictwo N-gramów i ich wagi z zestawu danych szkoleniowych. W związku z tym należy używać tego słownika i wag podczas wyodrębniania funkcji z danych testowych, w przeciwieństwie do tworzenia słownika nową. W związku z tym dodamy moduł funkcji Wyodrębnij N-gramy do rozgałęzienia oceniania eksperymentu, łączenia słownika danych wyjściowych z gałęzi szkoleń i ustawiania trybu słownictwa tylko do odczytu. Wyłączamy również filtrowanie N-gramów według częstotliwości, ustawiając minimalną liczbę wystąpień na 1 i maksymalnie 100%, i Wyłącz wybór funkcji.

Po przekształceniu kolumny tekstowej w danych testowych na kolumny funkcji liczbowych wykluczamy kolumny ciągów z poprzednich etapów, takich jak w przypadku gałęzi szkoleniowej. Następnie użyjemy modułu modelu oceny, aby prognozować i oszacować moduł modelu w celu oceny dokładności.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5. Wdrażanie modelu w środowisku produkcyjnym
Model jest prawie gotowy do wdrożenia w środowisku produkcyjnym. Gdy jest wdrażana jako usługa sieci Web, pobiera ciąg tekstowy o dowolnym formacie jako dane wejściowe i zwraca prognozę "High" lub "Low". Używa on słownictwa N-gramowego do przekształcania tekstu do funkcji i przeszkolonego modelu regresji logistycznej w celu dokonania przewidywania od tych funkcji. 

Aby skonfigurować eksperyment predykcyjny, najpierw zapisujemy słownictwo N-gramowe jako zestaw danych i przeszkolony model regresji logistycznej z gałęzi szkoleniowej. Następnie zapiszemy eksperyment za pomocą polecenia "Zapisz jako", aby utworzyć Graf eksperymentu na potrzeby eksperymentu predykcyjnego. Usuniemy moduł Split Data i rozgałęzienia szkoleniowego z eksperymentu. Następnie połączymy wcześniej zapisane słownictwo i model N-gram, aby wyodrębnić odpowiednio funkcje N-gramowe i moduły modelu oceny. Usuwamy również moduł Oceń model.

Przed przetworzeniem wstępnie przetworzonego modułu tekstowego do usuwania kolumny etykieta i usunięcia zaznaczenia opcji "Dołącz kolumnę wynikową do zestawu danych" w module oceny wstawiamy opcję Wybierz kolumny w module DataSet. W ten sposób usługa sieci Web nie żąda etykiety, którą próbuje przewidzieć, i nie echo funkcji wejściowych w odpowiedzi.

![Eksperyment predykcyjny](./media/text-analytics-module-tutorial/predictive-text.png)

Teraz możemy korzystać z eksperymentu, który można opublikować jako usługę sieci Web i wywołać za pomocą interfejsów API żądania lub przetwarzania wsadowego.

## <a name="next-steps"></a>Następne kroki
Informacje o modułach analizy tekstu z [dokumentacji MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).


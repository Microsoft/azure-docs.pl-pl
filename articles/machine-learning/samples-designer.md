---
title: Przykładowe potoki & zestawy danych dla projektanta (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Użyj przykładów w programie Azure Machine Learning Designer, aby przeskakuje i uruchamiać potoki uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: c1d927583ecf0ac5684c607b7d203c3224fe87b1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318831"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer-preview"></a>Przykładowe potoki & zestawy danych dla programu Azure Machine Learning Designer (wersja zapoznawcza)

Skorzystaj z wbudowanych przykładów w programie Azure Machine Learning Designer, aby szybko rozpocząć tworzenie własnych potoków uczenia maszynowego. [Repozytorium usługi GitHub](https://github.com/Azure/MachineLearningDesigner) Azure Machine Learning Designer zawiera szczegółową dokumentację ułatwiającą zrozumienie niektórych typowych scenariuszy uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Azure Machine Learning obszar roboczy z jednostką SKU przedsiębiorstwa.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Użyj potoków przykładowych

Projektant zapisuje kopię przykładowych potoków w obszarze roboczym programu Studio. Możesz edytować potok, aby dostosować go do swoich potrzeb i zapisać jako własny. Użyj ich jako punktu początkowego, aby szybko Rozpocznij pracę projekty.

Poniżej przedstawiono sposób użycia przykładu projektanta:

1. Zaloguj się do <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz pozycję **Projektant**.

1. Wybierz przykładowy potok w sekcji **nowe potoku** .

    Wybierz pozycję **Pokaż więcej próbek** , aby uzyskać pełną listę przykładów.

1. Aby uruchomić potok, najpierw musisz ustawić domyślny cel obliczeń, aby uruchomić potok na.

   1. W okienku **Ustawienia** z prawej strony kanwy wybierz pozycję **Wybierz element docelowy obliczeń**.

   1. W wyświetlonym oknie dialogowym wybierz istniejący element docelowy obliczeń lub Utwórz nowy. Wybierz pozycję **Zapisz**.

   1. Wybierz pozycję **Prześlij** w górnej części kanwy, aby przesłać uruchomienie potoku.

   W zależności od potoku przykładowego i ustawień obliczeń uruchomienie może zająć trochę czasu. Domyślne ustawienia obliczeń mają minimalny rozmiar węzła równy 0, co oznacza, że projektant musi przydzielić zasoby po stanie bezczynności. Powtarzające się uruchomienia potoku będą trwać krócej od czasu przydziału zasobów obliczeniowych. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby zwiększyć wydajność.


1. Po zakończeniu potoku możesz przejrzeć potok i wyświetlić dane wyjściowe dla każdego modułu, aby dowiedzieć się więcej. Wykonaj następujące kroki, aby wyświetlić dane wyjściowe modułu:

   1. Wybierz moduł na kanwie.

   1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję dane **wyjściowe + dzienniki**. Wybierz ![ ikonę wizualizacji ikona Graf, ](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) Aby zobaczyć wyniki każdego modułu. 

   Użyj przykładów jako punktów początkowych dla niektórych najpopularniejszych scenariuszy uczenia maszynowego.

## <a name="regression"></a>Regresja

Zapoznaj się z tymi wbudowanymi przykładami regresji.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 1: regresja — Prognoza cen dla samochodów (podstawowa)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Przewidywanie cen samochodów przy użyciu regresji liniowej. |
| [Przykład 2: regresja — Prognoza cen dla samochodów (Zaawansowane)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Przewidywanie cen samochodów przy użyciu lasu decyzyjnego i podwyższanie drzewa decyzyjnego regresorów. Porównaj modele, aby znaleźć najlepszy algorytm.

## <a name="classification"></a>Klasyfikacja

Zapoznaj się z tymi wbudowanymi przykładami klasyfikacji. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 3: klasyfikacja binarna z wyborem funkcji — prognozowanie dochodu](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Przewidywanie przychodów jako wysokie lub niskie przy użyciu dwuklasowego drzewa decyzyjnego. Użyj korelacji Pearsona, aby wybrać funkcje.
| [Przykład 4: klasyfikacja binarna z niestandardowym skryptem języka Python — przewidywanie ryzyka kredytowego](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasyfikowanie aplikacji kredytowych jako wysokiego lub niskiego ryzyka. Użyj modułu skryptu języka Python do ważenia danych.
| [Przykład 5: klasyfikacja binarna — Prognoza relacji klienta](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Przewidywanie zmian klientów przy użyciu dwuklasowych drzew decyzyjnych. Użyj SMOTE do próbkowania danych.
| [Przykład 7: Klasyfikacja tekstu — zestaw danych witryny Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasyfikowanie typów firmy z artykułów witryny Wikipedia przy użyciu wieloklasowej regresji logistycznej. |
| Przykład 12: Rozpoznawanie litery w klasyfikacji wieloklasowej | Utwórz kompletną liczbę klasyfikatorów binarnych do klasyfikowania pisanych liter. |

## <a name="recommender"></a>Moduł poleceń

Zapoznaj się z tymi wbudowanymi przykładami rekomendacji. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| Przykład 10: rekomendacje — tweety klasyfikacji filmów | Kompiluj aparat polecanego filmu z tytułów i klasyfikacji filmów. |

## <a name="utility"></a>Narzędzie

Dowiedz się więcej o przykładach demonstrujących narzędzia i funkcje uczenia maszynowego. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 6: Używanie niestandardowego skryptu języka R — prognozowanie opóźnień lotów](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Przykład 8: krzyżowe sprawdzanie poprawności dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj operacji krzyżowego sprawdzania poprawności, aby utworzyć klasyfikator binarny dla dorosłych dochodów.
| Przykład 9: ważność funkcji permutacji | Użyj ważności funkcji permutacji, aby obliczyć wyniki ważności dla zestawu danych testowych. 
| Przykład 11: dostrajanie parametrów dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj dopasowywania parametrów modelu, aby znaleźć optymalne parametry do kompilowania klasyfikatora binarnego. |

## <a name="datasets"></a>Zestawy danych

Podczas tworzenia nowego potoku w programie Azure Machine Learning Designer domyślnie są uwzględniane różne przykładowe zestawy danych. Te przykładowe zestawy danych są używane przez potoki przykładowe na stronie głównej projektanta. 

Przykładowe zestawy danych są dostępne w kategorii **przykłady zestawów danych** - **Samples** . Można to znaleźć w palecie modułów z lewej strony kanwy w projektancie. Możesz użyć dowolnego z tych zestawów danych we własnym potoku, przeciągając go do kanwy.

| Nazwa zestawu danych &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Opis zestawu danych |
|-------------|:--------------------|
| Binarny zestaw danych klasyfikacji dochodów z spisu dla dorosłych | Podzbiór bazy danych spisu 1994 przy użyciu pracy dla dorosłych w wieku 16 ze skorygowanym indeksem dochodu > 100.<br/>**Użycie**: klasyfikowanie osób przy użyciu demograficznych w celu przewidywania, czy osoba uzyskuje ponad 50 000 roku.<br/> **Badania pokrewne**: Kohavi, R., Becker, B., (1996). [Machine Learning — repozytorium](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera|
|Dane cen samochodów (RAW)|Informacje na temat samochodów i modeli, w tym Cena, funkcje, takie jak liczba cylindrów i MPG, oraz Ocena ryzyka dla ubezpieczenia.<br/> Ocena ryzyka jest początkowo skojarzona z funkcją autoprice. Następnie jest dostosowywany do rzeczywistego ryzyka w procesie znanym jako symbol aktuarialny. Wartość + 3 wskazuje, że jest to ryzykowne i wartość-3, która prawdopodobnie jest bezpieczna.<br/>**Użycie**: </b> przewidywanie oceny ryzyka według funkcji przy użyciu regresji lub klasyfikacji wieloczynnikowa.<br/>**Powiązane badania**: </b> Schlimmer, J.C. (1987). [Machine Learning — repozytorium](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera. |
| Udostępnione etykiety programu CRM pragnienie |Etykiety z wyzwania KDD 2009 ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)) z przewidywania relacji klientów.|
|Udostępnione etykiety zmian CRM|Etykiety z wyzwania KDD 2009 ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)) z przewidywania relacji klientów.|
|Udostępniony zestaw danych programu CRM | Te dane pochodzą z wyzwania KDD 2009 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Zestaw danych zawiera klientów 50 000 z niemieckiej firmy telekomunikacyjnej pomarańczowa. Każdy klient ma 230 funkcje anonimowe, 190, które są liczbowe i 40 są kategorii. Funkcje są bardzo rozrzedzone. |
|Udostępnione etykiety CRM do sprzedawania|Etykiety z wyzwania KDD 2009 ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) )|
|Dane dotyczące opóźnień lotów|Dane o wydajności w czasie lotu pasażera wykonywane z TranStats zbierania danych z działu transportu USA ([w czasie](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Ten zestaw danych obejmuje czas od kwietnia do 2013 października. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób: <br/>-Zestaw danych został przefiltrowany w celu pokrycia najgorętszym portów lotniczych o 70 <br/>-Anulowane loty zostały oznaczone jako opóźnione o więcej niż 15 minut <br/>-Przekierowane loty zostały odfiltrowane <br/>-Wybrano następujące kolumny: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, anulowane|
|Zestaw danych|Element dataset Statlog (niemieckiej karty kredytowej) ([Statlog + niemiecki + Credit + Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) przy użyciu pliku niemieckiego. Data.<br/>Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno liczbowych, jak i kategorii oraz etykieta binarna (wartość ryzyka kredytowego). Wpisy o wysokim ryzyku kredytowym mają etykietę = 2, niski poziom ryzyka kredytowego ma etykietę = 1. Koszt niewłaściwego klasyfikowania przykładu niskiego ryzyka o wartości 1 oznacza, że kosztem nieznaczącego klasyfikowania przykładu wysokiego ryzyka jest 5.|
|IMDB tytuły filmów|Zestaw danych zawiera informacje o filmach, które zostały ocenione w Tweetach w serwisie Twitter: IMDB, nazwa filmu, gatunek i rok produkcyjny. W zestawie danych znajdują się 17K filmy. Zestaw danych został wprowadzony w papierze "S". Dooms, T. de Pessemier i L. Martens. MovieTweetings: zestaw danych klasyfikacji filmu zebrany z serwisu Twitter. Warsztat na crowdsourcing i ludzi obliczeń dla systemów zalecających, CrowdRec o RecSys 2013. "|
|Klasyfikacje filmów|Zestaw danych to rozszerzona wersja zestawu danych tweetów filmów. Zestaw danych zawiera klasyfikacje 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów w serwisie Twitter. Każde wystąpienie reprezentuje Tweet i jest krotką: identyfikator użytkownika, identyfikator filmu IMDB, klasyfikacja, sygnatura czasowa, liczba ulubionych dla tego tweetu i liczba przesłanych tweetów tego tweetu. Zestaw danych został udostępniony przez. wspomniane, S. Dooms, B. Loni i D. Tikk dla systemów polecających 2014.|
|Zestaw danych pogody|Codzienne obserwacje pogodowe z NOAA ([scalone dane z 201304 do 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Dane pogodowe obejmują obserwacje z stacji pogodowych portów lotniczych, obejmujących okres od kwietnia do października 2013. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> -Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych    <br/> -Stacje pogodowe nieskojarzone z najgorętszym portów lotniczych 70 zostały odfiltrowane    <br/> -Kolumna Date została podzielona na oddzielne kolumny Year, month i Day.    <br/> — Wybrano następujące kolumny: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, Pogodatype, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, recordType, HourlyPrecip, ALTIMETER|
|Zestaw danych witryny Wikipedia SP 500|Dane są wyprowadzane z witryny Wikipedia ( https://www.wikipedia.org/) na podstawie artykułów każdej usługi S&P 500 firmy, przechowywanej jako dane XML.    <br/>Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> -Wyodrębnij zawartość tekstową dla każdej konkretnej firmy    <br/> -Usuń formatowanie wiki    <br/> -Usuń znaki inne niż alfanumeryczne    <br/> — Konwertuj cały tekst na małe litery    <br/> -Znane kategorie firmy zostały dodane    <br/>Należy zauważyć, że w niektórych firmach nie znaleziono artykułu, więc liczba rekordów jest mniejsza niż 500.|


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)


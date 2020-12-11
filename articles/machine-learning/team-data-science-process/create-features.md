---
title: Inżynieria funkcji w nauce danych — proces nauki o danych zespołowych
description: Dowiedz się więcej na temat inżynierii funkcji i jej roli w procesie ulepszania danych uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 7cf97edcb2f2f7a23d5c899194d497deade1f507
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031865"
---
# <a name="feature-engineering-in-data-science"></a>Inżynieria funkcji w dziedzinie analizy danych

Ten artykuł zawiera informacje na temat inżynierii funkcji i jej roli w celu ulepszania danych w usłudze Machine Learning. Dowiedz się więcej na temat przykładów przykładowych pobranych z eksperymentów [Azure Machine Learning Studio (klasycznych)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) . 

* **Inżynieria funkcji**: proces tworzenia nowych funkcji z danych pierwotnych w celu zwiększenia mocy predykcyjnej algorytmu uczenia. Wbudowane funkcje powinny przechwytywać dodatkowe informacje, które nie są łatwo widoczne w oryginalnym zestawie funkcji.
* **Wybór funkcji**: proces wybierania podzbioru kluczy funkcji w celu zmniejszenia liczby problemów szkoleniowych.

Zwykle **Funkcja inżynierii funkcji** jest stosowana najpierw w celu wygenerowania dodatkowych funkcji, a następnie **wybór funkcji** jest gotowy do wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych funkcji.

Inżynieria i wybór funkcji są częścią procesu [modelowania](lifecycle-modeling.md) zespołowej analizy danych (przetwarzania TDSP). Aby dowiedzieć się więcej na temat cyklu życia przetwarzania TDSP i analizy danych, zobacz [co to jest przetwarzania TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>Co to jest funkcja inżynierii?

Dane szkoleniowe składają się z macierzy składającej się z wierszy i kolumn. Każdy wiersz w macierzy jest obserwacją lub rekordem. Kolumny każdego wiersza są funkcjami opisującymi każdy rekord. Funkcje określone w projekcie eksperymentalnym powinny charakteryzować wzorce w danych.

Chociaż wiele pól danych pierwotnych może być używanych bezpośrednio do uczenia modelu, często konieczne jest utworzenie dodatkowych (przetworzonych) funkcji dla rozszerzonego zestawu danych szkoleniowych.

Zaprojektowane funkcje, które rozszerzają szkolenia, zapewniają informacje, które lepiej odróżniają wzorce w danych. Ale ten proces to coś z kompozycji. Decyzje dotyczące dźwięku i produktywności często wymagają znajomości domeny.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Przykład 1: Dodawanie funkcji danych czasowych dla modelu regresji

Użyjmy [prognoz popytu na potrzeby](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) eksperymentowania w Azure Machine Learning Studio (klasyczny), aby zademonstrować, jak inżynierować funkcje zadania regresji. Celem tego eksperymentu jest przewidywanie popytu na wypożyczenie rowerów w określonym miesiącu/dniu/godz.

### <a name="bike-rental-dataset"></a>Zestaw danych wypożyczenia rowerów

[Zestaw danych o wypożyczeniu roweru](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) jest oparty na rzeczywistych danych firmy z udziału rowerowego na podstawie Stany Zjednoczone. Przedstawia liczbę wynajmu rowerów w ciągu określonej godziny dnia przez lata 2011 i 2012. Zawiera 17 379 wierszy i 17 kolumn.

Nieprzetworzony zestaw funkcji zawiera warunki pogodowe (Temperatura/wilgotność/wiatr) i typ dnia (dni wolnych/dni tygodnia). Pole do przewidywania jest liczbą, która reprezentuje wypożyczenia rowerów w określonej godzinie. Liczba zakresów od 1 do 977.

### <a name="create-a-feature-engineering-experiment"></a>Tworzenie eksperymentu inżyniera funkcji

W celu konstruowania efektywnych funkcji w danych szkoleniowych cztery modele regresji są kompilowane przy użyciu tego samego algorytmu, ale z czterema różnymi zestawami danych szkoleniowych. Cztery zestawy danych przedstawiają te same nieprzetworzone dane wejściowe, ale z coraz większą liczbą funkcji. Te funkcje są pogrupowane w cztery kategorie:

1. A = Pogoda + święta + dzień tygodnia + funkcje weekendowe dla przewidywanego dnia
2. B = Liczba rowerów, które były dzierżawione w każdym z ostatnich 12 godzin
3. C = Liczba rowerów, które były dzierżawione w ciągu ostatnich 12 dni w tej samej godzinie
4. D = Liczba rowerów, które były dzierżawione w każdym z ostatnich 12 tygodni w tej samej godzinie i w tym samym dniu

Oprócz zestawu funkcji A, który już istnieje w oryginalnych danych pierwotnych, inne trzy zestawy funkcji są tworzone za pomocą procesu inżynierii funkcji. Zestaw funkcji B przechwytuje najnowsze zapotrzebowanie dla rowerów. Zestaw funkcji C przechwytuje zapotrzebowanie dla rowerów Bikes o określonej godzinie. Zestaw funkcji D przechwytuje zapotrzebowanie dla rowerów w określonej godzinie i konkretny dzień tygodnia. Cztery zbiory danych szkoleniowych obejmują odpowiednio zestaw funkcji A, A + B, A + B + C, a także + B + C + D.

### <a name="feature-engineering-using-studio-classic"></a>Inżynieria funkcji z użyciem programu Studio (klasyczny)

W doświadczeniu Studio (klasycznego) te cztery zbiory danych są tworzone przez cztery gałęzie ze wstępnie przetworzonego zestawu danych wejściowych. Z wyjątkiem gałęzi z lewej strony każda z tych gałęzi zawiera moduł [wykonywania skryptu języka R](/azure/machine-learning/studio-module-reference/execute-r-script) , w którym funkcje pochodne (zestaw funkcji B, C i D) są konstruowane i dołączane do zaimportowanego zestawu danych.

Na poniższej ilustracji przedstawiono skrypt języka R używany do tworzenia zestawu funkcji B w drugiej gałęzi.

![Tworzenie funkcji](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Wyniki

Porównanie wyników wydajności czterech modeli jest podsumowane w poniższej tabeli: 

![Porównanie wyników](./media/create-features/result1.png)

Najlepsze wyniki są wyświetlane na podstawie funkcji A + B + C. Współczynnik błędów zmniejsza się, gdy w danych szkoleniowych uwzględniono dodatkowy zestaw funkcji. Sprawdza domniemanie, że zestaw funkcji B, C zapewnia dodatkowe istotne informacje dla zadania regresji. Jednak dodanie funkcji D nie zapewnia dodatkowego zmniejszenia liczby błędów.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Przykład 2: Tworzenie funkcji wyszukiwania tekstu

Inżynieria funkcji jest szeroko stosowana w zadaniach związanych z górnictwem tekstu, takim jak Klasyfikacja dokumentu i analiza tonacji. Ponieważ pojedyncze fragmenty tekstu pierwotnego zwykle stanowią dane wejściowe, proces inżynierii funkcji jest wymagany do tworzenia funkcji obejmujących częstotliwość wyrazów/fraz.

### <a name="feature-hashing"></a>Mieszanie funkcji

Aby można było wykonać to zadanie, stosowana jest technika " [mieszanie funkcji](/azure/machine-learning/studio-module-reference/feature-hashing) " w celu wydajnego przetworzenia arbitralnych funkcji tekstowych w indeksach. Zamiast kojarzenia każdej funkcji tekstowej (słowa/frazy) z określonym indeksem, ta metoda stosuje funkcję mieszania do funkcji i używa ich wartości skrótu jako indeksów bezpośrednio.

W programie Studio (klasyczny) istnieje moduł [skrótu funkcji](/azure/machine-learning/studio-module-reference/feature-hashing) , który wygodnie tworzy funkcje programu Word/phrase. Na poniższej ilustracji przedstawiono przykład użycia tego modułu. Wejściowy zestaw danych zawiera dwie kolumny: klasyfikację książki od 1 do 5 i rzeczywistą zawartość przeglądu. Celem tego modułu jest pobranie wielu nowych funkcji, które pokazują częstotliwość występowania odpowiednich wyrazów (/phrase) w ramach konkretnego przeglądu książki. Aby użyć tego modułu, wykonaj następujące czynności:

* Najpierw wybierz kolumnę zawierającą tekst wejściowy ("Col2" w tym przykładzie).
* Następnie ustaw wartość "Hashing bitsize" na 8, co oznacza, że zostanie utworzonych 2 ^ 8 = 256 funkcji. Słowa/fazy we wszystkich tekstach zostaną zmieszane do 256 indeksów. Wartość parametru "Hashing bitsize" z zakresu od 1 do 31. Wyrazy, które/phrase, są mniej podobne do tego samego indeksu, jeśli ustawienie ma być większą liczbą.
* Po trzecie ustaw parametr "N-gramy" na 2. Ta wartość pobiera częstotliwość występowania unigrams (funkcję dla każdego pojedynczego wyrazu) i rozgramy (funkcję dla każdej pary sąsiadujących wyrazów) z tekstu wejściowego. Parametr "N-gramy" obejmuje wartości z zakresu od 0 do 10, co wskazuje maksymalną liczbę kolejnych wyrazów, które mają być uwzględnione w funkcji.  

![Moduł "mieszanie funkcji"](./media/create-features/feature-Hashing1.png)

Na poniższej ilustracji przedstawiono, jak wygląda Nowa funkcja.

![Przykład "mieszanie funkcji"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Wniosek
Zaprojektowane i wybrane funkcje zwiększają wydajność procesu szkolenia, który próbuje wyodrębnić informacje o kluczu zawarte w danych. Poprawiają one również możliwości tych modeli w celu dokładnego klasyfikowania danych wejściowych i przewidywania wyników zainteresowania bardziej niezawodnie.

Inżynieria funkcji i wybór mogą również łączyć się, aby zwiększyć możliwości obliczeniowe. Robi to przez zwiększenie i zmniejszenie liczby funkcji wymaganych do kalibracji lub uczenia modelu. W sposób matematyczny wybrane funkcje są minimalnym zestawem niezależnych zmiennych, które wyjaśniają wzorce w danych i przewidywalnie wyników.

Nie zawsze jest konieczna do wykonywania funkcji lub wyboru funkcji. Zależy to od danych, wybranego algorytmu i celu eksperymentu.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć funkcje dla danych w określonych środowiskach, zobacz następujące artykuły:

* [Tworzenie funkcji dla danych w SQL Server](create-features-sql-server.md)
* [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań programu Hive](create-features-hive.md)
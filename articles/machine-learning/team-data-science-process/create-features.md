---
title: Inżynieria funkcji w nauce danych — proces nauki o danych zespołowych
description: W tym artykule wyjaśniono cele inżynierii funkcji i przedstawiono przykłady jej roli w procesie ulepszania danych uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721136"
---
# <a name="feature-engineering-in-data-science"></a>Inżynieria funkcji w dziedzinie analizy danych
W tym artykule wyjaśniono cele Inżynieria funkcji i przedstawiono przykłady jej roli w procesie rozszerzania danych uczenia maszynowego. Przykłady używane do zilustrowania tego procesu są rysowane z Azure Machine Learning Studio. 

To zadanie jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Inżynieria funkcji próbuje zwiększyć predykcyjne algorytmy uczenia, tworząc funkcje z danych pierwotnych, które ułatwiają proces uczenia się. Inżynieria i wybór funkcji są jedną częścią przetwarzania TDSPu, [co jest cyklem życia procesu nauki o danych zespołowych?](overview.md) Inżynieria funkcji i wybór są częścią kroku **opracowywania funkcji** przetwarzania TDSP. 

* **Inżynieria funkcji**: ten proces podejmuje próbę utworzenia dodatkowych odpowiednich funkcji z istniejących pierwotnych funkcji danych i zwiększenia mocy predykcyjnej algorytmu uczenia.
* **wybór funkcji**: ten proces wybiera podzestaw kluczy oryginalnych funkcji danych w próbie zmniejszenia liczby problemów szkoleniowych.

Zwykle **Funkcja inżynierii funkcji** jest stosowana najpierw w celu wygenerowania dodatkowych funkcji, a następnie krok **wyboru funkcji** jest wykonywany w celu wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych funkcji.

Dane szkoleniowe używane w usłudze Machine Learning mogą być często rozszerzane przez wyodrębnienie funkcji z zebranych danych pierwotnych. Przykładem wbudowanej funkcji w kontekście uczenia się, jak sklasyfikować obrazy pisanych cyfrowo, jest tworzona Mapa gęstości bitowej skonstruowana na podstawie danych niesformatowanej dystrybucji. Ta mapa może pomóc w bardziej wydajnej lokalizacji znaków niż zwykłe użycie dystrybucji pierwotnej.

Aby utworzyć funkcje dla danych w określonych środowiskach, zobacz następujące artykuły:

* [Tworzenie funkcji dla danych w SQL Server](create-features-sql-server.md)
* [Tworzenie funkcji dla danych w klastrze usługi Hadoop przy użyciu zapytań programu Hive](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Tworzenie funkcji w ramach inżynierii funkcji danych
Dane szkoleniowe składają się z macierzy składającej się z przykładów (rekordów lub obserwacji przechowywanych w wierszach), z których każdy ma zestaw funkcji (zmienne lub pola przechowywane w kolumnach). W celu scharakteryzowania wzorców w danych należy oczekiwać funkcji określonych w projekcie eksperymentalnym. Mimo że wiele pól danych nieprzetworzonych można bezpośrednio uwzględnić w wybranym zestawie funkcji używanym do uczenia modelu, często zdarza się, że dodatkowe (opracowane) funkcje muszą być zbudowane z funkcji danych pierwotnych w celu wygenerowania rozszerzonego zestawu danych szkoleniowych.

Jakiego rodzaju funkcje należy utworzyć w celu zwiększenia zestawu danych podczas uczenia modelu? Zaprojektowane funkcje, które rozszerzają szkolenia, zapewniają informacje, które lepiej odróżniają wzorce w danych. Nowe funkcje powinny dostarczyć dodatkowe informacje, które nie są wyraźnie przechwycone lub łatwo widoczne w oryginalnym lub istniejącym zestawie funkcji. Ale ten proces to coś z kompozycji. Decyzje dotyczące dźwięku i produktywności często wymagają pewnej znajomości domeny.

Gdy rozpoczyna się od Azure Machine Learning, najłatwiej opanujesz ten proces przy użyciu przykładów dostarczonych w Studio. Poniżej przedstawiono dwa przykłady:

* Przykład regresji [przewidywania liczby czynszów rowerowych](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) w nadzorowanym doświadczeniu, w którym są znane wartości docelowe
* Przykład klasyfikacji wyszukiwania tekstu przy użyciu [funkcji tworzenia skrótów](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Przykład 1: Dodawanie funkcji danych czasowych dla modelu regresji
Użyjmy eksperymentu "prognozowanie popytu dla rowerów" w Azure Machine Learning Studio (klasyczny), aby zademonstrować sposób tworzenia funkcji regresji. Celem tego eksperymentu jest przewidywanie zapotrzebowania dla rowerów, czyli liczby czynszów rowerowych w określonym miesiącu/dniu/godz. Zestaw danych "zestaw danych o wypożyczeniu roweru" jest używany jako pierwotne dane wejściowe. Ten zestaw danych jest oparty na rzeczywistych danych firmy Bikeshare Company, która utrzymuje sieć wynajmu roweru w Waszyngtonie DC w Stany Zjednoczone. Zestaw danych przedstawia liczbę wynajmu roweru w ciągu określonej godziny dnia w latach 2011 i Year 2012 i zawiera 17379 wierszy i 17 kolumn. Nieprzetworzony zestaw funkcji zawiera warunki pogodowe (Temperatura/wilgotność/wiatr) i typ dnia (dni wolnych/dni tygodnia). Pole do przewidywania jest liczbą "CNT", która reprezentuje wypożyczenia rowerów w określonej godzinie, a które obejmują wartości z zakresu od 1 do 977.

W celu konstruowania efektywnych funkcji w danych szkoleniowych cztery modele regresji są kompilowane przy użyciu tego samego algorytmu, ale z czterema różnymi zestawami danych szkoleniowych. Cztery zestawy danych przedstawiają te same nieprzetworzone dane wejściowe, ale z coraz większą liczbą funkcji. Te funkcje są pogrupowane w cztery kategorie:

1. A = Pogoda + święta + dzień tygodnia + funkcje weekendowe dla przewidywanego dnia
2. B = Liczba rowerów, które były dzierżawione w każdym z ostatnich 12 godzin
3. C = Liczba rowerów, które były dzierżawione w ciągu ostatnich 12 dni w tej samej godzinie
4. D = Liczba rowerów, które były dzierżawione w każdym z ostatnich 12 tygodni w tej samej godzinie i w tym samym dniu

Oprócz zestawu funkcji A, który już istnieje w oryginalnych danych pierwotnych, inne trzy zestawy funkcji są tworzone za pomocą procesu inżynierii funkcji. Zestaw funkcji B przechwytuje najnowsze zapotrzebowanie dla rowerów. Zestaw funkcji C przechwytuje zapotrzebowanie dla rowerów Bikes o określonej godzinie. Zestaw funkcji D przechwytuje zapotrzebowanie dla rowerów w określonej godzinie i konkretny dzień tygodnia. Cztery zbiory danych szkoleniowych obejmują odpowiednio zestaw funkcji A, A + B, A + B + C, a także + B + C + D.

W Azure Machine Learning eksperymenty te cztery zbiory danych są tworzone przez cztery gałęzie ze wstępnie przetworzonego zestawu danych wejściowych. Z wyjątkiem gałęzi z lewej strony każda z tych gałęzi zawiera moduł [wykonywania skryptu języka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , w którym funkcje pochodne (zestaw funkcji B, C i D) są odpowiednio skonstruowane i dołączane do zaimportowanego zestawu danych. Na poniższej ilustracji przedstawiono skrypt języka R używany do tworzenia zestawu funkcji B w drugiej gałęzi.

![Tworzenie funkcji](./media/create-features/addFeature-Rscripts.png)

Porównanie wyników wydajności czterech modeli jest podsumowane w poniższej tabeli: 

![Porównanie wyników](./media/create-features/result1.png)

Najlepsze wyniki są wyświetlane na podstawie funkcji A + B + C. Współczynnik błędów zmniejsza się, gdy w danych szkoleniowych uwzględniono dodatkowy zestaw funkcji. Sprawdza domniemanie, że zestaw funkcji B, C zapewnia dodatkowe istotne informacje dla zadania regresji. Jednak dodanie funkcji D nie zapewnia dodatkowego zmniejszenia liczby błędów.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Przykład 2: Tworzenie funkcji podczas wyszukiwania tekstu
Inżynieria funkcji jest szeroko stosowana w zadaniach związanych z wyszukiwaniem tekstu, takimi jak Klasyfikacja dokumentu i analiza tonacji. Na przykład w przypadku klasyfikowania dokumentów do kilku kategorii typowe założenie polega na tym, że wyraz/frazy zawarte w jednej kategorii doc są mniej duże, które mogą wystąpić w innej kategorii dokumentu. Inaczej mówiąc, częstotliwość dystrybucji słów/fraz jest w stanie charakteryzować różne kategorie dokumentów. W aplikacjach do wyszukiwania tekstu, ponieważ poszczególne fragmenty treści tekstu zwykle pełnią rolę danych wejściowych, proces inżynierii funkcji jest wymagany do tworzenia funkcji obejmujących częstotliwość słów/fraz.

Aby można było wykonać to zadanie, stosowana jest technika " **mieszanie funkcji** " w celu wydajnego przetworzenia arbitralnych funkcji tekstowych w indeksach. Zamiast kojarzenia każdej funkcji tekstu (słowa/frazy) z określonym indeksem, ta metoda działa przez zastosowanie funkcji skrótu do funkcji i używanie ich wartości skrótu jako indeksów bezpośrednio.

W Azure Machine Learning istnieje moduł [skrótu funkcji](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) , który wygodnie tworzy funkcje programu Word/phrase. Na poniższej ilustracji przedstawiono przykład użycia tego modułu. Wejściowy zestaw danych zawiera dwie kolumny: klasyfikację książki od 1 do 5 i rzeczywistą zawartość przeglądu. Celem tego modułu [wyznaczania wartości skrótu](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) jest pobranie wielu nowych funkcji, które pokazują częstotliwość występowania odpowiednich wyrazów (/phrase) w ramach określonego przeglądu książki. Aby użyć tego modułu, wykonaj następujące czynności:

* Najpierw wybierz kolumnę zawierającą tekst wejściowy ("Col2" w tym przykładzie).
* Następnie ustaw wartość "Hashing bitsize" na 8, co oznacza, że zostanie utworzonych 2 ^ 8 = 256 funkcji. Słowa/fazy we wszystkich tekstach zostaną zmieszane do 256 indeksów. Wartość parametru "Hashing bitsize" z zakresu od 1 do 31. Wyrazy, które/phrase, są mniej podobne do tego samego indeksu, jeśli ustawienie ma być większą liczbą.
* Po trzecie ustaw parametr "N-gramy" na 2. Ta wartość pobiera częstotliwość występowania unigrams (funkcję dla każdego pojedynczego wyrazu) i rozgramy (funkcję dla każdej pary sąsiadujących wyrazów) z tekstu wejściowego. Parametr "N-gramy" obejmuje wartości z zakresu od 0 do 10, co wskazuje maksymalną liczbę kolejnych wyrazów, które mają być uwzględnione w funkcji.  

![Moduł "mieszanie funkcji"](./media/create-features/feature-Hashing1.png)

Na poniższej ilustracji przedstawiono, jak wygląda Nowa funkcja.

![Przykład "mieszanie funkcji"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Podsumowanie
Zaprojektowane i wybrane funkcje zwiększają wydajność procesu szkolenia, który próbuje wyodrębnić informacje o kluczu zawarte w danych. Poprawiają one również możliwości tych modeli w celu dokładnego klasyfikowania danych wejściowych i przewidywania wyników zainteresowania bardziej niezawodnie. Inżynieria funkcji i wybór mogą również łączyć się, aby zwiększyć możliwości obliczeniowe. Robi to przez zwiększenie i zmniejszenie liczby funkcji wymaganych do kalibracji lub uczenia modelu. Matematycznie mówiąc, funkcje wybrane do uczenia modelu są minimalnym zestawem niezależnych zmiennych, które wyjaśniają wzorce w danych, a następnie przewidywania wyników powiodło się.

Nie zawsze jest konieczna funkcja inżynierii lub wybór funkcji. Czy jest to konieczne, czy nie zależy od danych do rąk lub zebrania, wybranego algorytmu i celu eksperymentu.


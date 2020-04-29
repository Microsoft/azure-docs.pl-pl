---
title: Migrowanie analizy z programu Excel
titleSuffix: ML Studio (classic) - Azure
description: Porównanie modeli regresji liniowej w programie Excel i w Azure Machine Learning Studio (klasyczny)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217792"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrowanie analizy z programu Excel do Azure Machine Learning Studio (wersja klasyczna)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* i *Ben łodzi* są architektami rozwiązań przedsiębiorstwa w centrum danych firmy Microsoft w dziedzinie doskonałości. W tym artykule opisano sposób migrowania istniejącego pakietu analiz regresji do rozwiązania opartego na chmurze przy użyciu Azure Machine Learning Studio (klasycznego).

## <a name="goal"></a>Cel

Nasz projekt został uruchomiony z uwzględnieniem dwóch celów: 

1. Korzystanie z analizy predykcyjnej w celu poprawy dokładności prognoz miesięcznych przychodów w organizacji 
2. Użyj Azure Machine Learning Studio (klasyczny), aby potwierdzić, zoptymalizować, zwiększyć szybkość i skalować nasze wyniki. 

Podobnie jak w przypadku wielu firm, Nasza organizacja przechodzi przez miesięczny proces prognozowania przychodów. Nasz mały zespół analityków biznesowych został przetworzony przy użyciu Azure Machine Learning Studio (klasyczny), aby obsłużyć proces i poprawić dokładność prognoz. Zespół poświęcał kilka miesięcy na zbieranie danych z wielu źródeł i uruchamianie atrybutów danych za pośrednictwem analizy statystycznej atrybuty kluczy, które są istotne dla prognoz sprzedaży usług. Następnym krokiem było rozpoczęcie tworzenia prototypów statystycznych modeli regresji na danych w programie Excel. W ciągu kilku tygodni mamy model regresji programu Excel, który przeprowadził bieżące pola i finanse procesy prognozowania. Stało się to wynik przewidywania linii bazowej. 

Następnie wykonamy następny krok w celu przeprowadzenia analizy predykcyjnej w porównaniu do Studio (klasyczny), aby dowiedzieć się, jak program Studio (klasyczny) może poprawić wydajność predykcyjną.

## <a name="achieving-predictive-performance-parity"></a>Osiąganie nieprzewidywalnej wydajności
Naszym pierwszym priorytetem była osiągnięcie parzystości między modelami regresji Studio (klasyczne) i Excel. Uwzględniając te same dane i ten sam podział na dane szkoleniowe i testowe, chcemy osiągnąć niezależność wydajności predykcyjnej między programami Excel i Studio (klasyczną). Początkowo nie powiodło się. Model programu Excel (model klasyczny). Wystąpił błąd spowodowany brakiem znajomości ustawienia podstawowego narzędzia w programie Studio (klasyczne). Po zsynchronizowaniu z zespołem produktu Studio (klasycznego) firma Microsoft zyskała lepsze zrozumienie ustawienia podstawowego wymaganego dla naszych zestawów danych i osiągania parzystości między tymi dwoma modelami. 

### <a name="create-regression-model-in-excel"></a>Tworzenie modelu regresji w programie Excel
Nasze regresje programu Excel używały standardowego modelu regresji liniowej znalezionego w programie Excel Analysis ToolPak. 

Obliczono wartość *bezwzględnego% błędu* i użyto jej jako miary wydajności dla modelu. Przybycie do modelu roboczego przy użyciu programu Excel zajęło 3 miesiące. Firma Microsoft zdobywa znaczną naukę w programie Studio (klasycznym), która ostatecznie była korzystna w zrozumieniu wymagań.

### <a name="create-comparable-experiment-in-studio-classic"></a>Tworzenie porównywalnego eksperymentu w programie Studio (klasyczny)
Wykonano następujące kroki w celu utworzenia naszego eksperymentu w programie Studio (klasyczny): 

1. Przekazano zestaw danych jako plik CSV do Studio (model klasyczny) (bardzo mały plik)
2. Utworzono nowy eksperyment i użyto modułu [SELECT Columns in DataSet][select-columns] , aby wybrać te same funkcje danych, które są używane w programie Excel. 
3. Użyto modułu [Split Data][split] (z trybem *wyrażenia względnego* ) w celu podzielenia danych na te same szkolenia, które zostały wykonane w programie Excel. 
4. Eksperymentuj z modułem [regresja liniowa][linear-regression] (tylko opcje domyślne), udokumentowane i porównane wyniki z modelem regresji programu Excel

### <a name="review-initial-results"></a>Przejrzyj wyniki początkowe
W pierwszej kolejności model programu Excel jest jasno przeszukiwany model Studio (klasyczny): 

|  | Excel | Studio (klasyczna) |
| --- |:---:|:---:|
| Wydajność | | |
| <ul style="list-style-type: none;"><li>Dostosowany kwadrat R</li></ul> |0,96 |Nie dotyczy |
| <ul style="list-style-type: none;"><li>Współczynnik <br />Określon</li></ul> |Nie dotyczy |0,78<br />(niska dokładność) |
| Średni błąd bezwzględny |$9,5 m |$19.4 m |
| Średni błąd bezwzględny (%) |6,03% |12,2% |

Po przeprowadzeniu naszego procesu i przeprowadzeniu przez deweloperów i analityków danych w zespole Machine Learning szybko podałeś przydatne porady. 

* W przypadku korzystania z modułu [regresja liniowa][linear-regression] w programie Studio (klasyczny) są dostępne dwie metody:
  * Gradient online: może być bardziej odpowiedni w przypadku problemów z dużą skalą
  * Zwykłe najmniejsze kwadraty: jest to metoda, której uważa się większość osób, gdy słyszą regresję liniową. W przypadku małych zestawów danych zwykłego wyboru mogą być bardziej optymalne.
* Rozważ dostosowanie parametru wagi rozważenia w celu zwiększenia wydajności. Domyślnie jest ustawiona 0,001, ale w przypadku naszego małego zestawu danych ustawimy go na 0,005, aby zwiększyć wydajność. 

### <a name="mystery-solved"></a>Odwieczna zagadka!
Po zastosowaniu zaleceń osiągniemy tę samą wydajność bazową w programie Studio (klasyczny), jak w programie Excel: 

|  | Excel | Studio (wersja klasyczna) (wersja wstępna) | Studio (klasyczny) z/najmniej kwadraty |
| --- |:---:|:---:|:---:|
| Wartość oznaczona etykietą |Wartości rzeczywiste (liczbowe) |Ten |Ten |
| Learner |Program Excel — analiza danych > — > regresji |Regresja liniowa. |Regresja liniowa |
| Opcje dowiedzenia |Nie dotyczy |Wartość domyślna |zwykłe najmniejsze kwadraty<br />L2 = 0,005 |
| Zestaw danych |26 wierszy, 3 funkcje, 1 etykieta. Wszystkie wartości numeryczne. |Ten |Ten |
| Podział: szkolenie |Program Excel przeszkolony na pierwszych 18 wierszach, przetestowanych w ciągu ostatnich 8 wierszy. |Ten |Ten |
| Podziel: test |Formuła regresji programu Excel zastosowana do ostatnich 8 wierszy |Ten |Ten |
| **Wydajność** | | | |
| Dostosowany kwadrat R |0,96 |Nie dotyczy | |
| Współczynnik wyznaczania |Nie dotyczy |0,78 |0,952049 |
| Średni błąd bezwzględny |$9,5 m |$19.4 m |$9,5 m |
| Średni błąd bezwzględny (%) |<span style="background-color: 00FF00;">6,03%</span> |12,2% |<span style="background-color: 00FF00;">6,03%</span> |

Ponadto współczynniki programu Excel porównane z wagami funkcji w modelu przeszkolonym przez platformę Azure:

|  | Współczynniki programu Excel | Wagi funkcji platformy Azure |
| --- |:---:|:---:|
| Przechwycenie/odchylenia |19470209,88 |19328500 |
| Funkcja A |0,832653063 |0,834156 |
| Funkcja B |11071967,08 |11007300 |
| Funkcja C |25383318,09 |25140800 |

## <a name="next-steps"></a>Następne kroki
Chcemy korzystać z usługi sieci Web Machine Learning w programie Excel. Nasi analityki biznesowe korzystają z programu Excel, a firma Microsoft potrzebuje sposobu wywoływania usługi sieci Web Machine Learning z wierszem danych programu Excel i zwrócić przewidywaną wartość do programu Excel. 

Chcemy również zoptymalizować nasz model przy użyciu opcji i algorytmów dostępnych w programie Studio (klasyczny).

### <a name="integration-with-excel"></a>Integracja z programem Excel
Nasze rozwiązanie miało operacjonalizować nasz model regresji Machine Learning przez utworzenie usługi sieci Web na podstawie nauczonego modelu. W ciągu kilku minut usługa sieci Web została utworzona i możemy ją wywołać bezpośrednio z programu Excel w celu zwrócenia wartości przewidywanego przychodu. 

Sekcja *pulpit nawigacyjny usług sieci Web* zawiera skoroszyt programu Excel do pobrania. Skoroszyt zawiera wstępnie sformatowane informacje o interfejsie API usługi sieci Web i osadzonych informacjach o schemacie. Po kliknięciu przycisku *Pobierz skoroszyt programu Excel*zostanie otwarty skoroszyt i będzie można zapisać go na komputerze lokalnym. 

![Pobieranie skoroszytu programu Excel z pulpitu nawigacyjnego usług sieci Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Po otwarciu skoroszytu Skopiuj wstępnie zdefiniowane parametry do niebieskiej sekcji parametrów, jak pokazano poniżej. Po wprowadzeniu parametrów program Excel wywoła do usługi sieci Web Machine Learning, a przewidywane etykiety z wynikami zostaną wyświetlone w sekcji zielone wartości przewidywane. Skoroszyt będzie nadal tworzyć prognozy dla parametrów opartych na modelu przeszkolonego dla wszystkich elementów wierszy wprowadzonych w obszarze Parametry. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz Korzystanie [z usługi sieci Web Azure Machine Learning w programie Excel](consuming-from-excel.md). 

![Szablon skoroszytu programu Excel łączący się ze wdrożoną usługą sieci Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optymalizacja i dalsze eksperymenty
Teraz, gdy mamy linię bazową z naszym modelem programu Excel, przeniesionomy ją, aby zoptymalizować model regresji liniowej Machine Learning. Wykorzystano [wybór funkcji oparty na filtrze][filter-based-feature-selection] modułów, aby ulepszyć wybór początkowych elementów danych i pomaga nam w osiągnięciu poprawy wydajności dla 4,6% błędu bezwzględnego. W przypadku przyszłych projektów będziemy używać tej funkcji, która może zaoszczędzić tygodnie w iteracji przez atrybuty danych, aby znaleźć odpowiedni zestaw funkcji do modelowania. 

Następnie planujemy dołączenie dodatkowych algorytmów, takich jak [bayesowskie][bayesian-linear-regression] lub [drzewa decyzyjne][boosted-decision-tree-regression] w naszym doświadczeniu w celu porównania wydajności. 

Jeśli chcesz eksperymentować z regresją, dobry zestaw danych do wypróbowania jest przykładowym zestawem danych regresji wydajności energii, który ma wiele atrybutów liczbowych. Zestaw danych jest dostarczany jako część przykładowych zestawów danych w programie Studio (klasyczny). Możesz użyć różnych modułów szkoleniowych, aby przewidzieć obciążenie ogrzewania lub obciążenie chłodzenia. Wykres poniżej to porównanie wydajności różnej regresji w odniesieniu do zestawu danych efektywności energetycznej przewidywalnego dla zmiennej docelowej obciążenie chłodzenia: 

| Model | Średni błąd bezwzględny | Średni błąd oznaczający pierwiastek | Względny błąd bezwzględny | Względny kwadratowy błąd | Współczynnik wyznaczania |
| --- | --- | --- | --- | --- | --- |
| Drzewo podwyższanych decyzji |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Regresja liniowa (z gradientem) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regresja sieci neuronowej |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Regresja liniowa (zwykła najmniejsza kwadraty) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Wnioski Key
Pouczymy się, że od uruchamiania eksperymentów z programem Excel regresji i Studio (klasyczne). Tworzenie modelu linii bazowej w programie Excel i porównywanie go z modelami przy użyciu Machine Learning [regresji liniowej][linear-regression] pomogła nam uczyć się Studio (klasycznej) i wykrytych szans umożliwiających poprawę wyboru danych i wydajności modelu. 

Stwierdzamy również, że zaleca się użycie [wyboru funkcji opartej na filtrach][filter-based-feature-selection] w celu przyspieszenia przyszłych projektów predykcyjnych. Stosując wybór funkcji dla danych, można utworzyć ulepszony model w programie Studio (klasyczny) z lepszą wydajnością. 

Możliwość przetransferowania prognozowania analizy predykcyjnej z Studio (klasycznej) do programu Excel systemically umożliwia znaczący wzrost możliwości pomyślnego podania wyników do szerokiego grona użytkowników w biznesie. 

## <a name="resources"></a>Zasoby
Oto kilka zasobów ułatwiających współpracę z regresją: 

* Regresja w programie Excel. Jeśli nigdy nie usiłowano regresji w programie Excel, ten samouczek ułatwia:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regresja a prognozowanie. Tyler szachy napisały artykuł dotyczący sposobu prognozowania szeregów czasowych w programie Excel, który zawiera dobry opis regresji liniowej. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Normalne regresje liniowe: wady, problemy i pułapek. Aby zapoznać się z wprowadzeniem i omówieniem regresji: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/


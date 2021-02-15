---
title: 'ML Studio (klasyczny): korzystanie z przykładowych zestawów danych — Azure'
description: Opisy zestawów danych używanych w przykładowych modelach zawartych w Machine Learning Studio (klasyczne). Te przykładowe zestawy danych mogą być używane do eksperymentów.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518440"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Korzystanie z przykładowych zestawów danych w Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[top]: #machine-learning-sample-datasets

Podczas tworzenia nowego obszaru roboczego w Azure Machine Learning Studio (klasyczny) wiele przykładowych zestawów danych i eksperymentów są domyślnie uwzględniane. Wiele z tych przykładowych zestawów danych jest używanych przez modele przykładowe w [Azure AI Gallery](https://gallery.azure.ai/). Inne są uwzględniane jako przykłady różnych typów danych zwykle używanych w uczeniu maszynowym.

Niektóre z tych zestawów danych są dostępne w usłudze Azure Blob Storage. W przypadku tych zestawów danych Poniższa tabela zawiera link bezpośredni. Możesz użyć tych zestawów danych w eksperymentach przy użyciu modułu [Importuj dane][import-data] .

Pozostałe te przykładowe zestawy danych są dostępne w obszarze roboczym w obszarze **zapisane zestawy danych**. Można to znaleźć w palecie modułów z lewej strony kanwy eksperymentu w Machine Learning Studio (klasyczny).
Możesz użyć dowolnego z tych zestawów danych we własnym eksperymentie, przeciągając go do kanwy eksperymentu.

## <a name="datasets"></a>Zestawy danych

<table>

<tr>
  <th>Nazwa zestawu danych</th>
  <th>Opis zestawu danych</th>
</tr>

<tr>
  <td>Binarny zestaw danych klasyfikacji dochodów z spisu dla dorosłych</td>
  <td>
Podzbiór bazy danych spisu 1994 przy użyciu pracy dla dorosłych w wieku 16 ze skorygowanym indeksem dochodu > 100.
<p></p>
<b>Użycie:</b> Klasyfikuj osoby korzystające z demograficznych, aby przewidzieć, czy osoba uzyskuje ponad 50 000 rocznie.
<p></p>
<b>Powiązane badania:</b> Kohavi, R., Becker, B., (1996). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Zestaw danych kodów portów lotniczych</td>
  <td>
Kody lotniska USA.
<p></p>
Ten zestaw danych zawiera jeden wiersz dla każdego lotniska w Stanach Zjednoczonych, podając numer i nazwę identyfikatora lotniska oraz miejscowość i Województwo lokalizacji.
  </td>
</tr>

<tr>
  <td>Dane cen samochodów (RAW)</td>
  <td>
Informacje na temat samochodów i modeli, w tym Cena, funkcje, takie jak liczba cylindrów i MPG, oraz Ocena ryzyka dla ubezpieczenia.
<p></p>
Ocena ryzyka jest początkowo skojarzona z funkcją autoprice. Następnie jest dostosowywany do rzeczywistego ryzyka w procesie znanym jako symbol aktuarialny. Wartość + 3 wskazuje, że jest to ryzykowne i wartość-3, która prawdopodobnie jest bezpieczna.
<p></p>
<b>Użycie:</b> Przewidywanie oceny ryzyka według funkcji przy użyciu regresji lub klasyfikacji wieloczynnikowa. 
<p></p>
<b>Powiązane badania:</b> Schlimmer, J.C. (1987). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Zestaw danych o wypożyczeniu roweru</td>
  <td>
UCI roweru — zestaw danych, który jest oparty na rzeczywistych danych z firmy Bikeshare stolicę, która utrzymuje sieć wynajmu roweru w Waszyngtonie DC.
<p></p>
Zestaw danych ma jeden wiersz dla każdej godziny każdego dnia w 2011 i 2012, łącznie z 17 379 wierszy. Zakres wynajmu rowerów w godzinach wynosi od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obraz RGB bram rozliczeniowych</td>
  <td>
Publicznie dostępny plik obrazu konwertowany na dane CSV.
<p></p>
Kod służący do konwertowania obrazu znajduje się w <strong>podziału kolorów, przy użyciu K-oznacza</strong> stronę szczegółów Model klastrowania.
  </td>
</tr>

<tr>
  <td>Dane darowizny krwi</td>
  <td>
Podzbiór danych z bazy danych dawców krwi centrum usługi transfuzji krwi Hsin-Chu miasto, Tajwan.
<p></p>
Dane ofiarodawcy obejmują miesiące od ostatniej darowizny oraz częstotliwość lub łączną liczbę darowizn, czas od ostatniej oddawania i ilość oddanych krwi.
<p></p>
<b>Użycie:</b> Celem jest przewidywalność poprzez klasyfikację, czy dawca odnosił się w marcu 2007, gdzie 1 oznacza ofiarodawcę w okresie docelowym i 0 a nie jako ofiarodawcę. 
<p></p>
<b>Powiązane badania:</b> Yeh, I.C., (2008). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera
<p></p>
Yeh, I-Cheng, Yang, król-Jang i notowanie, tag-Ming, "odnajdywanie wiedzy na tryb RFM Model przy użyciu sekwencji Bernoulliego" systemy specjalistyczne z aplikacjami, 2008 <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dane raka piersi</td>
  <td>
Jeden z trzech zestawów danych związanych z rakiem, dostarczanych przez Instytut Oncology, często pojawiający się w literaturze uczenia maszynowego. Łączy informacje diagnostyczne z funkcjami analizy laboratoryjnej około 300 próbek tkanek.
<p></p>
<b>Użycie:</b> Klasyfikowanie typu nowotworu w oparciu o 9 atrybutów, niektóre z nich są liniowe, a niektóre z nich są kategorii. 
<p></p>
<b>Powiązane badania:</b> Wohlberg, W.H., ulica, W.N., & Mangasarian, O.L. (1995). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Funkcje raka piersiowego <td>
Zestaw danych zawiera informacje dotyczące 102K podejrzanych regionów (kandydatów) obrazów X-ray, z których każda została opisana przez funkcje 117. Funkcje są zastrzeżone i ich znaczenie nie jest ujawniane przez twórców zestawu danych (opieka firmy Siemens). 
  </td>
</tr>

<tr>
  <td>Informacje o raka piersi</td>
  <td>
Zestaw danych zawiera dodatkowe informacje dotyczące każdego podejrzanego regionu obrazu X-ray. Każdy przykład zawiera informacje (na przykład etykieta, identyfikator pacjenta, współrzędne poprawek względem całego obrazu) dotyczące odpowiedniego numeru wiersza w zestawie danych funkcji raka piersi. Każdy pacjent ma wiele przykładów. W przypadku pacjentów, którzy posiadają raka, niektóre przykłady są pozytywne, a niektóre z nich są negatywne. Dla pacjentów, którzy nie mają raka, wszystkie przykłady są ujemne. Zestaw danych zawiera przykłady 102K. Zestaw danych jest rozrzucany, 0,6% punktów jest dodatnich, reszta jest ujemna. Zestaw danych został udostępniony przez opiekę firmy Siemens.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety programu CRM pragnienie</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety zmian CRM</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Udostępniony zestaw danych programu CRM</td>
  <td>
Te dane pochodzą z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Zestaw danych zawiera klientów 50 000 z niemieckiej firmy telekomunikacyjnej pomarańczowa. Każdy klient ma 230 funkcje anonimowe, 190, które są liczbowe i 40 są kategorii. Funkcje są bardzo rozrzedzone.
  </td>
</tr>

<tr>
  <td>Udostępnione etykiety CRM do sprzedawania</td>
  <td>
Etykiety z wyzwania KDD 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>) z przewidywania relacji klientów.
  </td>
</tr>

<tr>
  <td>Energy-Efficiency dane regresji</td>
  <td>
Kolekcja symulowanych profilów energii w oparciu o 12 różnych kształtów konstrukcyjnych. Budynki są zróżnicowane przez osiem funkcji. Obejmuje to obszary szyb, rozmieszczenie obszarów szyb i orientację.
<p></p>
<b>Użycie:</b> Użycie regresji lub klasyfikacji do przewidywania oceny wydajności energetycznej na podstawie jednej z dwóch rzeczywistych odpowiedzi. W przypadku klasyfikacji wieloklasowej, zmienna odpowiedzi jest zaokrąglana do najbliższej liczby całkowitej. 
<p></p>
<b>Powiązane badania:</b> Xifara, A. & Tsanas, A. (2012). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Dane dotyczące opóźnień lotów</td>
  <td>
Dane o wydajności w czasie lotu pasażera wykonywane z TranStats zbierania danych z działu transportu USA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">w czasie</a>).
<p></p>
Ten zestaw danych obejmuje okres April-October 2013. Przed przekazaniem do Azure Machine Learning Studio (klasyczne) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Zestaw danych został przefiltrowany w celu pokrycia najgorętszym portów lotniczych o 70 w Stanach Zjednoczonych</li>
  <li>Anulowane loty zostały oznaczone jako opóźnione o więcej niż 15 minut</li>
  <li>Przekierowane loty zostały odfiltrowane</li>
  <li>Wybrano następujące kolumny: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, anulowane</li>
</ul>
</td>
</tr>

<tr>
  <td>Wydajność na czas (nieprzetworzone)</td>
  <td>
Rejestry lotów samolotowych i wyjazdów w ramach Stany Zjednoczone od października 2011.
<p></p>
<b>Użycie:</b> Przewidywanie opóźnień lotów. 
<p></p>
<b>Powiązane badania:</b> Z działu USA na transport <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> .
  </td>
</tr>

<tr>
  <td>Las wyzwala dane</td>
  <td>
Zawiera dane pogodowe, takie jak indeksy temperatury i wilgotności i szybkość wiatru. Dane są pobierane z obszaru północno-wschodnie Portugalia, w połączeniu z rejestrami pożarów lasów.
<p></p>
<b>Użycie:</b> Jest to trudne zadanie regresji, gdzie celem jest przewidywanie nagranego obszaru pożarów lasu. 
<p></p>
<b>Powiązane badania:</b> Cortez, P., & Morais, A. (2008). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera
<p></p>
[Cortez i Morais, 2007] P. Cortez i. Morais. Podejście do wyszukiwania danych do przewidywania pożarów lasów przy użyciu danych meteorologicznych. W J. Neves, M. F. Santos i J. Machado EDS., nowe trendy w sztucznej analizie, postępowania z trzynastej konferencji EPIA 2007-portugalski na sztucznej inteligencję, grudzień, Guimarães, Portugalia, PP. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Dostępne w: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> .
  </td>
</tr>

<tr>
  <td>Zestaw danych</td>
  <td>
Element dataset Statlog (niemieckiej karty kredytowej) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + niemiecki + Credit + Data</a>) przy użyciu pliku niemieckiego. Data.
<p></p>
Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno liczbowych, jak i kategorii oraz etykieta binarna (wartość ryzyka kredytowego). Wpisy o wysokim ryzyku kredytowym mają etykietę = 2, niski poziom ryzyka kredytowego ma etykietę = 1. Koszt niewłaściwego klasyfikowania przykładu niskiego ryzyka o wartości 1 oznacza, że kosztem nieznaczącego klasyfikowania przykładu wysokiego ryzyka jest 5.
  </td>
</tr>

<tr>
  <td>IMDB tytuły filmów</td>
  <td>
Zestaw danych zawiera informacje o filmach, które zostały ocenione w Tweetach w serwisie Twitter: IMDB, nazwa filmu, gatunek i rok produkcyjny. W zestawie danych znajdują się 17K filmy. Zestaw danych został wprowadzony w papierze "S". Dooms, T. de Pessemier i L. Martens. MovieTweetings: zestaw danych klasyfikacji filmu zebrany z serwisu Twitter. Warsztat na crowdsourcing i ludzi obliczeń dla systemów zalecających, CrowdRec o RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Przetęczówki dwie dane klasy</td>
  <td>
Jest to prawdopodobnie Najlepsza znana baza danych, która będzie znajdować się w literaturze dotyczącej rozpoznawania wzorców. Zestaw danych jest stosunkowo mały, zawierający 50 przykładów każdej płatnej miary z trzech odmian Iris.
<p></p>
<b>Użycie:</b> Przewidywanie typu Iris z pomiarów.  
<p></p>
<b>Powiązane badania:</b> Fisher, R.A. (1988). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Tweety filmów</td>
  <td>
Zestaw danych to rozszerzona wersja zestawu danych tweetów filmów. Zestaw danych zawiera klasyfikacje 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów w serwisie Twitter. Każde wystąpienie reprezentuje Tweet i jest krotką: identyfikator użytkownika, identyfikator filmu IMDB, klasyfikacja, sygnatura czasowa, liczba ulubionych dla tego tweetu i liczba przesłanych tweetów tego tweetu. Zestaw danych został udostępniony przez. wspomniane, S. Dooms, B. Loni i D. Tikk dla systemów polecających 2014.
  </td>
</tr>

<tr>
  <td>Dane MPG dla różnych samochodów</td>
  <td>
Ten zestaw danych to nieznacznie modyfikowana wersja zestawu danych dostarczana przez bibliotekę StatLib z Carnegie Mellon University. Zestaw danych został użyty w specyfikacji dotyczącej statystycznych związków 1983 amerykańskich.
<p></p>
Dane przedstawiają zużycie paliwa dla różnych samochodów w kilometrach na galon. Zawiera również informacje takie jak liczba cylindrów, przemieszczenie aparatu, możliwość założenia, ogólna waga i przyspieszenie.
<p></p>
<b>Użycie:</b> Przewidywanie zużycia paliwa w oparciu o trzy atrybuty dyskretne i pięć atrybutów ciągłych. 
<p></p>
<b>Powiązane badania:</b> StatLib, Carnegie Mellon University, (1993). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Pima Indians cukrzycą binarny zestaw danych klasyfikacji</td>
  <td>
Podzbiór danych z Narodowego Instytutu cukrzycą oraz baza danych z chorobami trawienia i nerek. Zestaw danych został przefiltrowany, aby skoncentrować się na pacjentach z dziedzictwem indyjskim Pima. Dane obejmują dane medyczne, takie jak glukoza i poziomy inuliny, a także ich czynniki.
<p></p>
<b>Użycie:</b> Przewidywanie, czy podmiot ma cukrzycą (klasyfikację binarną). 
<p></p>
<b>Powiązane badania:</b> Sigillito, V. (1990). Machine Learning <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml — repozytorium</a> Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera </td>
</tr>

<tr>
  <td>Restauracje — dane klienta</td>
  <td>
Zestaw metadanych o klientach, w tym dane demograficzne i preferencje.
<p></p>
<b>Użycie:</b> Użyj tego zestawu danych, w połączeniu z innymi dwoma zbiorami danych restauracji, do uczenia i przetestowania systemu zalecanego. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera.
  </td>
</tr>

<tr>
  <td>Dane funkcji restauracji</td>
  <td>
Zestaw metadanych dotyczących restauracji i ich funkcji, takich jak typ żywności, styl rekomendowanych lokali i lokalizacja.
<p></p>
<b>Użycie:</b> Użyj tego zestawu danych, w połączeniu z innymi dwoma zbiorami danych restauracji, do uczenia i przetestowania systemu zalecanego. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera.
  </td>
</tr>

<tr>
  <td>Klasyfikacje restauracji</td>
  <td>
Zawiera klasyfikacje nadawane przez użytkowników dla restauracji w skali od 0 do 2.
<p></p>
<b>Użycie:</b> Użyj tego zestawu danych, w połączeniu z innymi dwoma zbiorami danych restauracji, do uczenia i przetestowania systemu zalecanego. 
<p></p>
<b>Powiązane badania:</b> Bache, K. i Lichman, M. (2013). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera.
  </td>
</tr>

<tr>
  <td>Stalowy zestaw danych symulowana</td>
  <td>
Ten zestaw danych zawiera serię rekordów ze stali symulowana. Zawiera on atrybuty fizyczne (szerokość, grubość, typ (cewka, arkusz itp.) powstających typów stalowych.
<p></p>
<b>Użycie:</b> Przewidywanie dowolnego z dwóch atrybutów klasy numerycznej; Twardość lub moc. Możesz również analizować korelacje między atrybutami.
<p></p>
Klasy stalowe są zgodne z zestawem Standard zdefiniowanym przez SAE i inne organizacje. Szukasz konkretnej klasy "Klasa" (zmienna klasy) i chcesz zrozumieć wymagane wartości. 
<p></p>
<b>Powiązane badania:</b> Szterling, D. & Buntine, W. (NA). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera
<p></p>
Przydatny Przewodnik dotyczący gatunków stali można znaleźć tutaj: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dane telezakresowe</td>
  <td>
Rejestruj duże pierścienie cząstek gamma i szumy w tle, symulowane przy użyciu procesu Monte Carlo.
<p></p>
Zamiarem symulacji było Poprawa dokładności telezakresów Cherenkovowych w obłożeniu opartym na lądzie. Jest to realizowane przy użyciu metod statystycznych w celu rozróżnienia między żądanymi sygnałami (Cherenkov promieniowanie promieniowania) i hałasem w tle (hadronic natrysków inicjowanych przez cosmicy w górnej atmosferze).
<p></p>
Dane zostały wstępnie przetworzone w celu utworzenia wydłużonego klastra z długą osią na centrum kamer. Charakterystyki tego elipsy (często nazywane parametrami Hillas) należą do parametrów obrazu, które mogą być używane do dyskryminacji.
<p></p>
<b>Użycie:</b> Przewidywanie, czy obraz prysznicu reprezentuje sygnał lub hałas w tle.
<p></p>
<b>Uwagi:</b> Prosta dokładność klasyfikacji nie jest istotna dla tych danych, ponieważ klasyfikowanie zdarzenia w tle jest gorsze niż klasyfikowanie zdarzenia sygnału jako tła. Aby można było porównać różne klasyfikatory, należy użyć grafu ROC. Prawdopodobieństwo przyjęcia zdarzenia w tle jako sygnał musi być poniżej jednego z następujących progów: 0,01, 0,02, 0,05, 0,1 lub 0,2.
<p></p>
Należy również zwrócić uwagę na to, że liczba zdarzeń w tle (h, dla hadronic pryszniców) jest nieszacowana. W rzeczywistych pomiarach Klasa h lub hałas reprezentuje większość zdarzeń. 
<p></p>
<b>Powiązane badania:</b> Bock, R.K. (1995). Machine Learning — repozytorium <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornii, Szkoła informacji </td>
</tr>

<tr>
  <td>Zestaw danych pogody</td>
  <td>
Codzienne obserwacje pogodowe z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">scalone dane z 201304 do 201310</a>).
<p></p>
Dane pogodowe obejmują obserwacje z stacji meteorologicznych lotniska, obejmujące okres April-October 2013. Przed przekazaniem do Azure Machine Learning Studio (klasyczne) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych</li>
  <li>Stacje pogody nie są skojarzone z najgorętszym portów lotniczych 70 zostały odfiltrowane</li>
  <li>Kolumna Date została podzielona na oddzielne kolumny Year, month i Day.</li>
  <li>Wybrano następujące kolumny: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, Pogodatype, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, recordType, HourlyPrecip, ALTIMETER</li>
</ul>
  </td>
</tr>

<tr>
  <td>Zestaw danych witryny Wikipedia SP 500</td>
  <td>
Dane są wyprowadzane z witryny Wikipedia ( <a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a> ) na podstawie artykułów każdej usługi S&P 500 firmy, przechowywanej jako dane XML.
<p></p>
Przed przekazaniem do Azure Machine Learning Studio (klasyczne) zestaw danych został przetworzony w następujący sposób:
<ul>
  <li>Wyodrębnij zawartość tekstową dla każdej konkretnej firmy</li>
  <li>Usuwanie formatowania wiki</li>
  <li>Usuń znaki inne niż alfanumeryczne</li>
  <li>Konwertuj cały tekst na małe litery</li>
  <li>Dodano znane kategorie firmy</li>
</ul>
<p></p>
Należy zauważyć, że w niektórych firmach nie znaleziono artykułu, więc liczba rekordów jest mniejsza niż 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Zestaw danych zawiera dane klienta i informacje o ich odpowiedzi na bezpośrednią kampanię korespondencyjną. Każdy wiersz reprezentuje klienta. Zestaw danych zawiera dziewięć funkcji dotyczących elementów demograficznych użytkownika i przeszłe zachowanie oraz trzy kolumny etykiet (odwiedzin, konwersji i wydatków).  Odwiedzanie jest kolumną binarną, która wskazuje, że klient odwiedził po kampanii marketingowej. Konwersja wskazuje, że klient kupił coś. Wydatki to kwota, która została pobrana.  Zestaw danych został udostępniony przez Jan Hillstrom na potrzeby analizy poczty E-Mail MineThatData i wyzwania wyszukiwania danych.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Przykłady testów w zestawie danych Aktualności RCV1-v2 firmy Reuters. Zestaw danych zawiera 781K artykuły z wiadomościami oraz ich identyfikatory (pierwsza kolumna zestawu danych). Każdy artykuł ma tokeny, stopworded i odszukane. Zestaw danych został udostępniony przez David. D. Lewis przedstawiają.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Przykłady szkoleń w zestawie danych RCV1-v2 Reuters News. Zestaw danych zawiera 23K artykuły z wiadomościami oraz ich identyfikatory (pierwsza kolumna zestawu danych). Każdy artykuł ma tokeny, stopworded i odszukane. Zestaw danych został udostępniony przez David. D. Lewis przedstawiają.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Zestaw danych z KDD 1999 wiedzy narzędzia do odnajdywania i wyszukiwania danych (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Zestaw danych został pobrany i zapisany w usłudze Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) i obejmuje szkolenia i testy zestawów danych. Zestaw danych szkoleniowych zawiera około 126K wierszy i 43 kolumn, łącznie z etykietami. Trzy kolumny są częścią informacji o etykiecie, a 40 kolumn, składających się z funkcji liczbowych i ciągów/kategorii, są dostępne do uczenia modelu. Dane testowe mają około 22.5 K testów z tymi samymi kolumnami 43, co w danych szkoleniowych.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Przypisania tematu dotyczące artykułów z wiadomościami w zestawie danych RCV1-v2 Reuters News. Artykuł z wiadomościami można przypisać do kilku tematów. Każdy wiersz jest w formacie " &lt; tematu o nazwie &gt; &lt; dokumentu &gt; 1". Zestaw danych zawiera 2,6 M przypisań tematu. Zestaw danych został udostępniony przez David. D. Lewis przedstawiają.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Te dane pochodzą z wyzwania testowego oceny wydajności KDD filiżanki 2010 (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">Ocena wydajności ucznia</a>). Używane dane to Algebra_2008_2009 zestawu szkoleniowego (Sygnatura, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebry I 2008-2009. Zestaw danych wyzwania z KDD 2010 filiżanki — wyzwanie dla wyszukiwania danych edukacyjnych. Znajdź ją na <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Zestaw danych został pobrany i zapisany w usłudze Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) i zawiera pliki dziennika z systemu tworzenia uczniów. Podane funkcje obejmują identyfikator problemu i jego Krótki opis, identyfikator ucznia, sygnaturę czasową oraz liczbę prób dokonanych przez studenta przed rozpuszczeniem problemu w odpowiedni sposób. Oryginalny zestaw danych ma 8,9 M rekordów; Ten zestaw danych został przepróbkowany do pierwszych 100 000 wierszy. Zestaw danych ma 23 kolumny oddzielane tabulatorami różnych typów: numeryczne, kategorii i timestamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Stwórz eksperymenty z przykładami](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
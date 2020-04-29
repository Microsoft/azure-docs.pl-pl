---
title: Przykładowe dane dla projektanta
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat przykładowych zestawów danych dostępnych w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: b19c1ed5aa556a2cfb633ac6fbc0cf9f95d0337f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81256981"
---
# <a name="sample-datasets-in-azure-machine-learning-designer-preview"></a>Przykładowe zestawy danych w programie Azure Machine Learning Designer (wersja zapoznawcza)

W przypadku tworzenia nowego potoku w programie Azure Machine Learning Designer (wersja zapoznawcza) domyślnie są uwzględniane różne przykładowe zestawy danych. Te przykładowe zestawy danych są używane przez potoki przykładowe na stronie głównej projektanta. 

Przykładowe zestawy danych są dostępne w kategorii**przykłady** **zestawów danych**-. Można to znaleźć w palecie modułów z lewej strony kanwy w projektancie. Możesz użyć dowolnego z tych zestawów danych we własnym potoku, przeciągając go do kanwy.

## <a name="datasets"></a>Zestawy danych


| Nazwa&nbsp;zestawu danych&nbsp;&nbsp;&nbsp;&nbsp;| Opis zestawu danych |
|-------------|:--------------------|
| Binarny zestaw danych klasyfikacji dochodów z spisu dla dorosłych | Podzbiór bazy danych spisu 1994 przy użyciu pracy dla dorosłych w wieku 16 ze skorygowanym indeksem dochodu > 100.<br/>**Użycie**: klasyfikowanie osób przy użyciu demograficznych w celu przewidywania, czy osoba uzyskuje ponad 50 000 roku.<br/> **Badania pokrewne**: Kohavi, R., Becker, B., (1996). [Machine Learning — repozytorium](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera|
|Dane cen samochodów (RAW)|Informacje na temat samochodów i modeli, w tym Cena, funkcje, takie jak liczba cylindrów i MPG, oraz Ocena ryzyka dla ubezpieczenia.<br/> Ocena ryzyka jest początkowo skojarzona z funkcją autoprice. Następnie jest dostosowywany do rzeczywistego ryzyka w procesie znanym jako symbol aktuarialny. Wartość + 3 wskazuje, że jest to ryzykowne i wartość-3, która prawdopodobnie jest bezpieczna.<br/>**Użycie**:</b> przewidywanie oceny ryzyka według funkcji przy użyciu regresji lub klasyfikacji wieloczynnikowa.<br/>**Powiązane badania**:</b> Schlimmer, J.C. (1987). [Machine Learning — repozytorium](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornii, szkolna informacja i nauka komputera. |
| Udostępnione etykiety programu CRM pragnienie |Etykiety z wyzwania KDD 2009 ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)) z przewidywania relacji klientów.|
|Udostępnione etykiety zmian CRM|Etykiety z wyzwania KDD 2009 ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)) z przewidywania relacji klientów.|
|Udostępniony zestaw danych programu CRM | Te dane pochodzą z wyzwania KDD 2009 ([orange_small_train. Data. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Zestaw danych zawiera klientów 50 000 z niemieckiej firmy telekomunikacyjnej pomarańczowa. Każdy klient ma 230 funkcje anonimowe, 190, które są liczbowe i 40 są kategorii. Funkcje są bardzo rozrzedzone. |
|Udostępnione etykiety CRM do sprzedawania|Etykiety z wyzwania KDD 2009 ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) )|
|Dane dotyczące opóźnień lotów|Dane o wydajności w czasie lotu pasażera wykonywane z TranStats zbierania danych z działu transportu USA ([w czasie](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Ten zestaw danych obejmuje czas od kwietnia do 2013 października. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób: <br/>-Zestaw danych został przefiltrowany w celu pokrycia najgorętszym portów lotniczych o 70 <br/>-Anulowane loty zostały oznaczone jako opóźnione o więcej niż 15 minut <br/>-Przekierowane loty zostały odfiltrowane <br/>-Wybrano następujące kolumny: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, anulowane|
|Zestaw danych|Element dataset Statlog (niemieckiej karty kredytowej) ([Statlog + niemiecki + Credit + Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) przy użyciu pliku niemieckiego. Data.<br/>Zestaw danych klasyfikuje osoby, opisane przez zestaw atrybutów, jako niskie lub wysokie ryzyko kredytowe. Każdy przykład reprezentuje osobę. Istnieje 20 funkcji, zarówno liczbowych, jak i kategorii oraz etykieta binarna (wartość ryzyka kredytowego). Wpisy o wysokim ryzyku kredytowym mają etykietę = 2, niski poziom ryzyka kredytowego ma etykietę = 1. Koszt niewłaściwego klasyfikowania przykładu niskiego ryzyka o wartości 1 oznacza, że kosztem nieznaczącego klasyfikowania przykładu wysokiego ryzyka jest 5.|
|IMDB tytuły filmów|Zestaw danych zawiera informacje o filmach, które zostały ocenione w Tweetach w serwisie Twitter: IMDB, nazwa filmu, gatunek i rok produkcyjny. W zestawie danych znajdują się 17K filmy. Zestaw danych został wprowadzony w papierze "S". Dooms, T. de Pessemier i L. Martens. MovieTweetings: zestaw danych klasyfikacji filmu zebrany z serwisu Twitter. Warsztat na crowdsourcing i ludzi obliczeń dla systemów zalecających, CrowdRec o RecSys 2013. "|
|Klasyfikacje filmów|Zestaw danych to rozszerzona wersja zestawu danych tweetów filmów. Zestaw danych zawiera klasyfikacje 170K dla filmów, wyodrębnione z dobrze zorganizowanych tweetów w serwisie Twitter. Każde wystąpienie reprezentuje Tweet i jest krotką: identyfikator użytkownika, identyfikator filmu IMDB, klasyfikacja, sygnatura czasowa, liczba ulubionych dla tego tweetu i liczba przesłanych tweetów tego tweetu. Zestaw danych został udostępniony przez. wspomniane, S. Dooms, B. Loni i D. Tikk dla systemów polecających 2014.|
|Zestaw danych pogody|Codzienne obserwacje pogodowe z NOAA ([scalone dane z 201304 do 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Dane pogodowe obejmują obserwacje z stacji pogodowych portów lotniczych, obejmujących okres od kwietnia do października 2013. Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> -Identyfikatory stacji pogody zostały zamapowane na odpowiednie identyfikatory portów lotniczych    <br/> -Stacje pogodowe nieskojarzone z najgorętszym portów lotniczych 70 zostały odfiltrowane    <br/> -Kolumna Date została podzielona na oddzielne kolumny Year, month i Day.    <br/> — Wybrano następujące kolumny: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, Pogodatype, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, recordType, HourlyPrecip, ALTIMETER|
|Zestaw danych witryny Wikipedia SP 500|Dane są wyprowadzane z witrynyhttps://www.wikipedia.org/) Wikipedia (na podstawie artykułów każdej usługi S&P 500 firmy, przechowywanej jako dane XML.    <br/>Przed przekazaniem do projektanta zestaw danych został przetworzony w następujący sposób:    <br/> -Wyodrębnij zawartość tekstową dla każdej konkretnej firmy    <br/> -Usuń formatowanie wiki    <br/> -Usuń znaki inne niż alfanumeryczne    <br/> — Konwertuj cały tekst na małe litery    <br/> -Znane kategorie firmy zostały dodane    <br/>Należy zauważyć, że w niektórych firmach nie znaleziono artykułu, więc liczba rekordów jest mniejsza niż 500.|

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)

* Dowiedz się, jak modyfikować istniejące [przykłady projektanta](samples-designer.md) , aby dostosować je do swoich potrzeb.

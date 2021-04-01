---
title: Analiza danych przy użyciu platformy Spark w usłudze Azure HDInsight — proces nauki o danych zespołowych
description: Zestaw narzędzi Spark MLlib zapewnia znaczną możliwość modelowania maszyn w rozproszonym środowisku usługi HDInsight.
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
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314738"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Omówienie analizy danych przy użyciu platformy Spark w usłudze Azure HDInsight

Ten zestaw tematów pokazuje, jak za pomocą usługi HDInsight Spark wykonać typowe zadania analizy danych, takie jak pozyskiwanie danych, Inżynieria funkcji, modelowanie i ocenianie modelu. Używane dane to przykład zestawu danych 2013 o wykorzystaniu NYC i taryfy. Modele skompilowane obejmują regresję logistyczną i liniową, lasy losowe i podwyższające gradienty drzew. W tych tematach pokazano również, jak przechowywać te modele w usłudze Azure Blob Storage (WASB) oraz jak oceny i oszacować wydajność predykcyjną. Bardziej zaawansowane tematy obejmują sposób uczenia modeli przy użyciu sprawdzania poprawności i czyszczenia parametrów funkcji Hyper-Parameter. Ten temat zawiera również odwołania do tematów opisujących sposób konfigurowania klastra Spark, który jest potrzebny do wykonania kroków opisanych w przewodnikach.

## <a name="spark-and-mllib"></a>Spark i MLlib
[Platforma Spark](https://spark.apache.org/) to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększenia wydajności aplikacji do analizy danych Big Data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w obliczeniach uczenia maszynowego i grafu. [MLlib](https://spark.apache.org/mllib/) to skalowalna biblioteka uczenia maszynowego platformy Spark, która zapewnia możliwości modelowania algorytmów w tym środowisku rozproszonym.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to hostowana w systemie Azure oferta platformy Spark typu open source. Obejmuje również obsługę **notesów Jupyter PySpark** w klastrze Spark, które mogą uruchamiać zapytania Spark SQL Interactive na potrzeby przekształcania, filtrowania i wizualizacji danych przechowywanych w obiektach Blob platformy Azure (WASB). PySpark to interfejs API języka Python dla platformy Spark. Fragmenty kodu dostarczające rozwiązania i pokazujące odpowiednie wykresy umożliwiające wizualizację danych w tym miejscu są uruchamiane w notesach Jupyter zainstalowanych w klastrach Spark. Kroki modelowania w tych tematach zawierają kod, który pokazuje, jak nauczyć się, oszacować, zapisać i korzystać z poszczególnych typów modeli.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Konfiguracja: klastry Spark i notesy Jupyter
Kroki instalacji i kod zostały przedstawione w tym przewodniku dotyczącym korzystania z usługi HDInsight Spark 1,6. Jednak dla klastrów usługi HDInsight Spark 1,6 i Spark 2,0 są udostępniane notesy Jupyter. Opis notesów i linków do nich znajduje się w [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod i połączone notesy są ogólne i powinny być wykonywane w dowolnym klastrze Spark. Jeśli nie korzystasz z usługi HDInsight Spark, kroki instalacji i zarządzania klastrami mogą się nieco różnić od tego, co jest widoczne w tym miejscu. Dla wygody poniżej przedstawiono linki do notesów Jupyter dla platformy Spark 1,6 (do uruchomienia w jądrze pySpark serwera Jupyter Notebook) i platformy Spark 2,0 (do uruchomienia w jądrze pySpark3 serwera Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1,6
Te notesy są uruchamiane w jądrze pySpark serwera notesu Jupyter.

- [pySpark-Machine-Learning-Data-nauka-Spark-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): zawiera informacje na temat przeprowadzania eksploracji danych, modelowania i oceniania przy użyciu kilku różnych algorytmów.
- [pySpark-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): zawiera tematy w notesie #1 i projektowanie modeli przy użyciu strojenia parametrów i krzyżowego sprawdzania poprawności.
- [pySpark-Machine-Learning-Data-nauka-Spark-model-zużycie. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): pokazuje, jak operacjonalizować zapisany model przy użyciu języka Python w klastrach usługi HDInsight.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark 2,0
Te notesy są uruchamiane w jądrze pySpark3 serwera notesu Jupyter.

- [Spark 2.0-pySpark3-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja — Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): ten plik zawiera informacje na temat sposobu przeprowadzania eksploracji danych, modelowania i oceniania w klastrach platformy Spark 2,0 przy użyciu usługi NYC Data podróży i [opłat.](#the-nyc-2013-taxi-data) Ten Notes może być dobrym punktem wyjścia do szybkiego eksplorowania kodu dostarczonego dla platformy Spark 2,0. Aby zapoznać się z bardziej szczegółowym notesem, przeanalizujemy dane z NYC taksówki, zobacz następny Notes na tej liście. Zapoznaj się z uwagami poniżej tej listy, która porównuje te notesy.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): w tym pliku pokazano, jak wykonywać operacje przetwarzanie danych (Spark SQL i Dataframe), eksploracja, modelowanie i ocenianie przy użyciu danych o podróży i opłatach za pomocą usługi NYC, które zostały opisane [tutaj](#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): w tym pliku pokazano, jak wykonywać operacje przetwarzanie danych (Spark SQL i Dataframe), eksploracja, modelowanie i ocenianie przy użyciu dobrze znanego zestawu danych wyjściowych w czasie od 2011 do 2012. Zestaw danych linii lotniczych jest zintegrowany z danymi o pogodzie lotniska (na przykład windspeed, temperatura, Wysokość itp.) przed modelem, dzięki czemu funkcje pogodowe mogą zostać uwzględnione w modelu.

<!-- -->

> [!NOTE]
> Zestaw danych linii lotniczych został dodany do notesów platformy Spark 2,0, aby lepiej zilustrować użycie algorytmów klasyfikacji. Zobacz następujące linki, aby uzyskać informacje na temat zestawu danych wychodzącego w czasie transportu lotniczego i zestawu danych pogody:
> 
> - Dane wychodzące z czasu transportu lotniczego: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dane o pogodzie lotniska: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Notesy platformy Spark 2,0 na NYCach i opóźnienia lotów lotniczych mogą trwać 10 minut lub dłużej (w zależności od rozmiaru klastra HDI). Pierwszy Notes na powyższej liście przedstawia wiele aspektów uczenia się, wizualizacji i modelu ML w notesie, który zajmuje mniej czasu na uruchamianie z zestawem danych NYC z próbkami, w którym zostały wstępnie dołączone pliki z taksówką i taryfą: [Spark 2.0-pySpark3-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ten Notes zajmuje dużo czasu na zakończenie (2-3 min) i może być dobrym punktem wyjścia do szybkiego eksplorowania kodu dostarczonego przez nas 2,0.

<!-- -->

Aby uzyskać wskazówki dotyczące operacjonalizacji modelu i użycia modelu platformy Spark 2,0 na potrzeby oceniania, zapoznaj się z [dokumentem dotyczącym platformy spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) , aby zapoznać się z przykładem. Aby użyć tego przykładu na platformie Spark 2,0, Zastąp plik kodu Python [tym plikiem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Wymagania wstępne

Poniższe procedury dotyczą platformy Spark 1,6. W przypadku wersji Spark 2,0 użyj notesów opisanych i połączonych z poprzednio.

1. Wymagana jest subskrypcja platformy Azure. Jeśli jeszcze tego nie masz, zobacz artykuł [Pobieranie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Aby ukończyć ten przewodnik, potrzebny jest klaster Spark 1,6. Aby go utworzyć, zobacz instrukcje podane w artykule [wprowadzenie: tworzenie Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ i wersja klastra są określone z menu **Wybierz typ klastra** .

![Konfiguruj klaster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Aby uzyskać informacje o tym, jak używać Scala zamiast języka Python do wykonywania zadań związanych z kompleksowym procesem nauki o danych, zobacz [naukę danych przy użyciu narzędzia Scala with Spark na platformie Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Dane dotyczące taksówki NYC 2013
Dane o podróży z NYC taksówkami dotyczą około 20 GB skompresowanych plików z wartościami rozdzielanymi przecinkami (CSV) (~ 48 GB nieskompresowanych), składających się z ponad 173 000 000 pojedynczych podróży i opłat za każdą podróż. Każdy rekord rejsu zawiera Dropoff lokalizację i godzinę odbioru oraz czas, liczbę licencji () i Medallion (unikatowy identyfikator taksówki). Dane obejmują wszystkie podróże w roku 2013 i są dostępne w następujących dwóch zestawach danych dla każdego miesiąca:

1. Pliki CSV "trip_data" zawierają szczegóły dotyczące podróży, takie jak liczba pasażerów, punkty pobierania i Dropoff, czas trwania podróży i długość podróży. Oto kilka przykładowych rekordów:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Pliki CSV "trip_fare" zawierają szczegóły dotyczące opłat za każdą podróż, takie jak typ płatności, kwota opłaty, opłata dodatkowa i podatki, porady i opłaty, a także łączną kwotę płatną. Oto kilka przykładowych rekordów:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Pobrano 0,1% próbek tych plików i dołączono \_ \_ Pliki CVS opłaty za przejazd i podróż do jednego zestawu danych, który będzie używany jako zestaw danych wejściowych dla tego przewodnika. Unikatowy klucz do przyłączenia \_ danych podróży i \_ opłaty za podróż są złożone z pól: Medallion, \_ licencja na hakera i \_ Data i godzina pobrania. Każdy rekord zestawu danych zawiera następujące atrybuty reprezentujące podróż NYCą z taksówką:

| Pole | Krótki opis |
| --- | --- |
| medallion |Anonimowe taksówke Medallion (unikatowy identyfikator taksówki) |
| hack_license |Numer licencji przewozowej anonimowe Hackney |
| vendor_id |Identyfikator dostawcy taksówki |
| rate_code |NYC odsetek opłat za taksówkę |
| store_and_fwd_flag |Flaga magazynu i przesyłania dalej |
| pickup_datetime |Data pobrania & czas |
| dropoff_datetime |Dropoff Data & |
| pickup_hour |Pobierz godzinę |
| pickup_week |Pobierz tydzień roku |
| dzień tygodnia |Dzień tygodnia (zakres 1-7) |
| passenger_count |Liczba pasażerów w podróży z taksówką |
| trip_time_in_secs |Czas trwania podróży (w sekundach) |
| trip_distance |Odległość podróży w milach |
| pickup_longitude |Pobierz długość geograficzną |
| pickup_latitude |Pobierz szerokość geograficzną |
| dropoff_longitude |Długość geograficzna Dropoff |
| dropoff_latitude |Dropoff Latitude |
| direct_distance |Bezpośrednia odległość między lokalizacjami odbioru i Dropoff |
| payment_type |Typ płatności (gotówka, karta kredytowa itp.) |
| fare_amount |Kwota taryfy |
| Dodatkowa |Dodatkowa |
| mta_tax |Podatek transportowy MTA |
| tip_amount |Kwota Porada |
| tolls_amount |Kwota opłat |
| total_amount |Suma |
| Przechylony |Przechylone (0/1 dla nie lub tak) |
| tip_class |Klasa TIP (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonywanie kodu z notesu Jupyter w klastrze Spark
Jupyter Notebook można uruchomić z Azure Portal. Znajdź klaster Spark na pulpicie nawigacyjnym i kliknij go, aby wprowadzić stronę zarządzania dla klastra. Aby otworzyć Notes skojarzony z klastrem Spark, kliknij pozycję **pulpity nawigacyjne klastra**  ->  **Jupyter Notebook**.

![Pulpity nawigacyjne klastra](./media/spark-overview/spark-jupyter-on-portal.png)

Możesz również przejść do ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** okna, aby uzyskać dostęp do notesów Jupyter. Zastąp część CLUSTERname tego adresu URL nazwą własnego klastra. Aby uzyskać dostęp do notesów, musisz mieć hasło do konta administratora.

![Przeglądaj notesy Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Wybierz pozycję PySpark, aby wyświetlić katalog zawierający kilka przykładów wstępnie spakowanych notesów korzystających z interfejsu API PySpark. Notesy zawierające przykłady kodu dla tego zestawu platformy Spark są dostępne w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Notesy można przekazać bezpośrednio z witryny [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) do serwera notesu Jupyter w klastrze Spark. Na stronie głównej Jupyter kliknij przycisk **Przekaż** znajdujący się w prawej części ekranu. Otwiera Eksploratora plików. W tym miejscu możesz wkleić adres URL witryny GitHub (nieprzetworzona zawartość) notesu i kliknąć pozycję **Otwórz**.

Na liście plików Jupyter zostanie wyświetlona nazwa pliku z przyciskiem **Przekaż** . Kliknij ten przycisk **Przekaż** . Teraz zaimportowano Notes. Powtórz te kroki, aby przekazać inne notesy z tego przewodnika.

> [!TIP]
> Możesz kliknąć prawym przyciskiem myszy linki w przeglądarce i wybrać pozycję **Kopiuj link** , aby uzyskać adres URL nieprzetworzonej zawartości usługi GitHub. Ten adres URL można wkleić do okna dialogowego Jupyter przekazywania plików Eksploratora.
> 
> 

Co możesz teraz zrobić:

* Zobacz kod, klikając Notes.
* Wykonaj każdą komórkę, naciskając **klawisze SHIFT-ENTER**.
* Uruchom cały Notes, klikając pozycję przebieg **komórki**  ->  .
* Użyj automatycznej wizualizacji zapytań.

> [!TIP]
> Jądro PySpark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL). Można wybrać różne typy wizualizacji (tabela, wykres kołowy, linia, obszar lub Słupek) za pomocą przycisków menu **Typ** w notesie:
>
>

![Krzywa ROC regresji logistycznej dla ogólnego podejścia](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dalej?
Teraz, po skonfigurowaniu z klastrem usługi HDInsight Spark i przekazaniu notesów Jupyter, możesz przystąpić do pracy z tematami odpowiadającymi trzem notesom PySpark. Pokazują one, jak eksplorować dane, a następnie jak tworzyć i korzystać z modeli. Zaawansowany Notes eksploracji i modelowania danych pokazuje, jak uwzględnić wzajemne sprawdzanie poprawności, czyszczenie parametrów funkcji Hyper-i obliczanie modelu.

**Eksplorowanie i modelowanie danych za pomocą platformy Spark:** Eksplorowanie zestawu danych i tworzenie, Ocena i Ocena modeli uczenia maszynowego przez pracę w temacie [Create Binary Modeling and regresji dla danych za pomocą narzędzia Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

**Użycie modelu:** Aby dowiedzieć się, jak oszacować modele klasyfikacji i regresji utworzone w tym temacie, zobacz [ocenę i ocenę modeli uczenia maszynowego opartych na platformie Spark](spark-model-consumption.md).

Czyszczenie z zastosowaniem **krzyżowym i** przeszukiwaniem: zobacz [Zaawansowane Eksploracja i modelowanie danych za pomocą platformy Spark](spark-advanced-data-exploration-modeling.md) , w jaki sposób można przeszkoleć modele przy użyciu weryfikacji krzyżowej i funkcji Hyper-Parameter.
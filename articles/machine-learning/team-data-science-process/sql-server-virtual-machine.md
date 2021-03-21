---
title: Eksplorowanie danych w SQL Server maszynie wirtualnej — proces nauki o danych zespołu
description: Eksploruj i Przetwarzaj dane oraz Generuj funkcje przy użyciu języka Python lub SQL w SQL Server maszynie wirtualnej na platformie Azure.
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
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314618"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Przetwarzanie danych maszyny wirtualnej programu SQL Server na platformie Azure
W tym dokumencie opisano, jak eksplorować dane i generować funkcje dla danych przechowywanych na maszynie wirtualnej SQL Server na platformie Azure. Ten cel może być wykonany przez przetwarzanie danych przy użyciu programu SQL lub języka programowania, takiego jak Python.

> [!NOTE]
> Przykładowe instrukcje SQL w tym dokumencie założono, że dane są w SQL Server. Jeśli tak nie jest, zapoznaj się z mapą procesów analizy danych w chmurze, aby dowiedzieć się, jak przenieść dane do SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Korzystanie z języka SQL
W tej sekcji opisano następujące zadania dotyczące przetwarzanie danych:

1. [Eksploracja danych](#sql-dataexploration)
2. [Generowanie funkcji](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Eksploracja danych
Oto kilka przykładowych skryptów SQL, których można użyć do eksplorowania magazynów danych w SQL Server.

> [!NOTE]
> W przypadku praktycznego przykładu można użyć [zestawu danych NYC taksówki](https://www.andresmh.com/nyctaxitrips/) i odwołać się do IPNB [NYC danych przetwarzanie przy użyciu notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) w celu uzyskania kompleksowego przewodnika.
> 
> 

1. Pobierz liczbę obserwacji dziennie
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobieranie poziomów w kolumnie kategorii
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu dwóch kolumn kategorii 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobieranie dystrybucji dla kolumn liczbowych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generowanie funkcji
W tej sekcji opisano sposoby generowania funkcji przy użyciu programu SQL:  

1. [Generowanie funkcji na podstawie liczby](#sql-countfeature)
2. [Generowanie funkcji pakowania](#sql-binningfeature)
3. [Wdrażanie funkcji z pojedynczej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowych funkcji można je dodać jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które mogą być sprzężone z oryginalną tabelą. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generowanie funkcji na podstawie liczby
W poniższych przykładach pokazano dwa sposoby generowania funkcji count. W pierwszej metodzie jest używana suma warunkowa, a druga metoda używa klauzuli "Where". Te wyniki mogą następnie być sprzężone z oryginalną tabelą (przy użyciu kolumn klucza podstawowego) w celu uzyskania funkcji count wraz z oryginalnymi danymi.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generowanie funkcji pakowania
Poniższy przykład pokazuje, jak generować funkcje binned przez pakowania (przy użyciu pięciu zasobników) kolumny liczbowej, która może być używana jako funkcja:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```

### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Wdrażanie funkcji z pojedynczej kolumny
W tej sekcji pokazano, jak wdrożyć pojedynczą kolumnę w tabeli w celu wygenerowania dodatkowych funkcji. W przykładzie przyjęto założenie, że w tabeli znajduje się kolumna Latitude lub długość geograficzna, z której próbujesz generować funkcje.

Poniżej znajduje się krótki początek danych lokalizacji Latitude/długości geograficznej (od StackOverflow [, jak zmierzyć dokładność długości i szerokości](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)geograficznej?). Wskazówki te są przydatne do zrozumienia przed dołączeniem lokalizacji jako jednej lub kilku funkcji:

* W tym polu jest wyświetlana informacja o tym, czy mamy północ, południe, wschód czy zachód na świecie.
* Cyfra różna od zera informuje nas, że używamy długości geograficznej, a nie szerokości geograficznej.
* Cyfra zawiera pozycję około 1 000 kilometrów. Daje nam to przydatne informacje o tym, co kontynent lub ocean.
* Cyfra (jeden stopień dziesiętny) daje pozycję do 111 kilometrów (60 mil morskich, około 69 kilometrów). Może on informować o stanie, kraju lub regionie, w którym się znajdujesz.
* Pierwsze miejsce dziesiętne jest równe 11,1 km: może odróżnić pozycję jednego miasta od sąsiedniego miasta.
* Drugie miejsce dziesiętne jest równe 1,1 km: może oddzielić jeden Village od następnego.
* Trzecie miejsce dziesiętne ma wartość do 110 m: może identyfikować duże pole rolnicze lub instytucjonalne.
* Czwarte miejsce dziesiętne jest warta do 11 m: może identyfikować Działka ziemi. Jest to porównywalne do typowej dokładności niepoprawionej jednostki GPS bez zakłóceń.
* Piąte miejsce dziesiętne jest równe 1,1 m: odróżnia drzewa od siebie. Dokładność tego poziomu z komercyjnymi jednostkami GPS można osiągnąć tylko przy użyciu korekty różnicowej.
* Szóste miejsce dziesiętne jest równe 0,11 m: można go użyć do bardziej szczegółowego układania struktur w celu projektowania Landscapes, tworzenia dróg. Powinien być wystarczająco dobry, aby można było śledzić ruchy Glaciers i rzek. Można to osiągnąć, pobierając miary painstaking z GPS, takie jak rozwiązane z różnicą GPS.

Informacje o lokalizacji można featurized w następujący sposób, oddzielając informacje o regionie, lokalizacji i miejscowości. Możesz również wywołać punkt końcowy REST, taki jak interfejs API usługi mapy Bing dostępny w miejscu [wyszukiwania lokalizacji przez punkt](/bingmaps/rest-services/locations/find-a-location-by-point) , aby uzyskać informacje o regionie/okręgu.

```sql
select 
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Te funkcje oparte na lokalizacji mogą być dodatkowo używane do generowania dodatkowych funkcji zliczania zgodnie z wcześniejszym opisem. 

> [!TIP]
> Można programowo wstawić rekordy przy użyciu wybranego języka. Może być konieczne wstawienie danych w fragmentach, aby zwiększyć wydajność zapisu (Przykładowo, jak to zrobić za pomocą moduł pyodbc, zobacz [przykład HelloWorld, aby uzyskać dostęp do programu SqlServer przy użyciu języka Python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Kolejną alternatywą jest wstawianie danych w bazie danych przy użyciu [narzędzia bcp](/sql/tools/bcp-utility).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Nawiązywanie połączenia z Azure Machine Learning
Nowo wygenerowana funkcja może zostać dodana jako kolumna do istniejącej tabeli lub zapisana w nowej tabeli i dołączona do oryginalnej tabeli w celu uczenia maszynowego. Funkcje mogą być generowane lub dostępne, jeśli zostały już utworzone, przy użyciu modułu [Importuj dane][import-data] w Azure Machine Learning jak pokazano poniżej:

![Czytelnicy Azure][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Korzystanie z języka programowania, takiego jak Python
Używanie języka Python do eksplorowania danych i generowania funkcji, gdy dane są w SQL Server są podobne do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python zgodnie z opisem w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md). Załaduj dane z bazy danych do ramki danych Pandas w celu przetworzenia. Dokumentuje proces łączenia się z bazą danych i ładowania danych do ramki danych w tej sekcji.

Następujący format parametrów połączenia może służyć do nawiązywania połączenia z bazą danych SQL Server z poziomu języka Python przy użyciu moduł pyodbc (Zastąp wartości ServerName, dbname, username i Password) określonymi wartościami.

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

[Biblioteka Pandas](https://pandas.pydata.org/) w języku Python oferuje bogaty zestaw struktur danych i narzędzi do analizy danych na potrzeby manipulowania danymi na potrzeby programowania w języku Python. Poniższy kod odczytuje wyniki zwrócone z SQL Server Database do ramki danych Pandas:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Teraz możesz współpracować z ramką danych Pandas, jak opisano w artykule [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Przykładowa analiza danych platformy Azure w ramach akcji
Aby zapoznać się z kompleksowym przykładem procesu nauki o danych platformy Azure przy użyciu publicznego zestawu danych, zobacz [proces analizy danych platformy Azure w działaniu](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
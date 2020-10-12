---
title: Tworzenie funkcji w SQL Server przy użyciu programu SQL i języka Python — proces nauki o danych zespołowych
description: Generuj funkcje dla danych przechowywanych na maszynie wirtualnej SQL Server na platformie Azure przy użyciu języka SQL i Python — części procesu nauki o danych zespołowych.
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
ms.openlocfilehash: 0be75b3b0a7b9b5aaec0da1d9f41f67a7108e77a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085314"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Tworzenie funkcji dla danych w programie SQL Server przy użyciu języka SQL i Python
W tym dokumencie pokazano, jak generować funkcje dla danych przechowywanych na SQL Server maszynie wirtualnej na platformie Azure, które pomagają algorytmom bardziej wydajnym dowiedzieć się z danych. Aby wykonać to zadanie, możesz użyć języka SQL lub programowania, takiego jak Python. W tym miejscu pokazano oba podejścia.

To zadanie jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> W przypadku praktycznego przykładu możesz zapoznać się z [zestawem danych NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) i zapoznaj się z IPNB z tytułem [NYC Data przetwarzanie przy użyciu notesu IPython i SQL Server,](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) Aby uzyskać kompleksowy przewodnik.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Dane są przechowywane w SQL Server. Jeśli nie, zobacz [Przenieś dane do Azure SQL Database Azure Machine Learning](move-sql-azure.md) , aby uzyskać instrukcje dotyczące sposobu przenoszenia danych.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generowanie funkcji przy użyciu języka SQL
W tej sekcji opisano sposoby generowania funkcji przy użyciu programu SQL:  

* [Generowanie funkcji na podstawie liczby](#sql-countfeature)
* [Generowanie funkcji pakowania](#sql-binningfeature)
* [Wdrażanie funkcji z pojedynczej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowych funkcji można je dodać jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które mogą być sprzężone z oryginalną tabelą.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generowanie funkcji na podstawie liczby
W tym dokumencie przedstawiono dwa sposoby generowania funkcji count. W pierwszej metodzie jest używana suma warunkowa, a druga metoda używa klauzuli "Where". Te nowe funkcje mogą następnie być sprzężone z oryginalną tabelą (przy użyciu kolumn klucza podstawowego) w celu uzyskania funkcji count wraz z oryginalnymi danymi.

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

Oto krótkie miejsce na dane lokalizacji szerokości geograficznej (StackOverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` ). Oto kilka przydatnych zagadnień, które należy zrozumieć, aby poznać dane lokalizacji przed utworzeniem funkcji z pola:

* Znak wskazuje, czy na świecie mamy północ, południe, wschód czy zachód.
* Niezerowych setek cyfr wskazuje długość geograficzna, a nie Szerokość geograficzna.
* Cyfra zawiera pozycję około 1 000 kilometrów. Zapewnia przydatne informacje o tym, co kontynentem lub na oceanie.
* Cyfra (jeden stopień dziesiętny) daje pozycję do 111 kilometrów (60 mil morskich, około 69 kilometrów). Oznacza to, że jest to bardzo duży stan lub kraj/region, w którym się znajdują.
* Pierwsze miejsce dziesiętne jest równe 11,1 km: może odróżnić pozycję jednego miasta od sąsiedniego miasta.
* Drugie miejsce dziesiętne jest równe 1,1 km: może oddzielić jeden Village od następnego.
* Trzecie miejsce dziesiętne ma wartość do 110 m: może identyfikować duże pole rolnicze lub instytucjonalne.
* Czwarte miejsce dziesiętne jest warta do 11 m: może identyfikować Działka ziemi. Jest to porównywalne do typowej dokładności niepoprawionej jednostki GPS bez zakłóceń.
* Piąte miejsce dziesiętne jest równe 1,1 m: odróżnia drzewa od siebie. Dokładność tego poziomu z komercyjnymi jednostkami GPS można osiągnąć tylko przy użyciu korekty różnicowej.
* Szóste miejsce dziesiętne jest równe 0,11 m: można użyć tego poziomu, aby uzyskać szczegółowe informacje o strukturach, na potrzeby projektowania Landscapes, tworzenia dróg. Powinien być wystarczająco dobry, aby można było śledzić ruchy Glaciers i rzek. Ten cel można osiągnąć, pobierając miary painstaking z GPS, takie jak rozwiązane z różnicą GPS.

Informacje o lokalizacji można featurized, oddzielając informacje o regionie, lokalizacji i miejscowości. Można również wywołać punkt końcowy REST, taki jak interfejs API usługi mapy Bing (zobacz `https://msdn.microsoft.com/library/ff701710.aspx` , aby uzyskać informacje o regionie/okręgu).

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
> Można programowo wstawić rekordy przy użyciu wybranego języka. Może być konieczne wstawienie danych w fragmentach w celu zwiększenia wydajności zapisu. [Oto przykład, jak to zrobić za pomocą moduł pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Kolejną alternatywą jest wstawianie danych w bazie danych za pomocą [narzędzia bcp](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Nawiązywanie połączenia z Azure Machine Learning
Nowo wygenerowana funkcja może zostać dodana jako kolumna do istniejącej tabeli lub zapisana w nowej tabeli i dołączona do oryginalnej tabeli w celu uczenia maszynowego. Funkcje mogą być generowane lub dostępne, jeśli zostały już utworzone, przy użyciu modułu [Importuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) na platformie Azure ml, jak pokazano poniżej:

![Czytelnicy Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Korzystanie z języka programowania, takiego jak Python
Używanie języka Python do generowania funkcji, gdy dane są w SQL Server są podobne do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python. Aby uzyskać porównanie, zobacz [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md). Załaduj dane z bazy danych do ramki danych Pandas, aby przetworzyć ją w dalszej postaci. Proces łączenia się z bazą danych i ładowania danych do ramki danych znajduje się w tej sekcji.

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

Teraz możesz współpracować z ramką danych Pandas, jak opisano w tematach [Tworzenie funkcji dla danych usługi Azure Blob Storage za pomocą Panda](create-features-blob.md).


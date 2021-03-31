---
title: Eksplorowanie danych w SQL Server maszynie wirtualnej — proces nauki o danych zespołu
description: Jak eksplorować dane przechowywane na SQL Server maszynie wirtualnej na platformie Azure przy użyciu programu SQL lub języka programowania, takiego jak Python.
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "86085654"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure

W tym artykule opisano, jak eksplorować dane przechowywane na SQL Server maszynie wirtualnej na platformie Azure. Użyj języka SQL lub Python, aby przeanalizować dane.

To zadanie jest krokiem w [procesie nauki danych zespołu](overview.md).

> [!NOTE]
> Przykładowe instrukcje SQL w tym dokumencie założono, że dane są w SQL Server. Jeśli tak nie jest, zapoznaj się z mapą procesów analizy danych w chmurze, aby dowiedzieć się, jak przenieść dane do SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Eksplorowanie danych SQL za pomocą skryptów SQL
Oto kilka przykładowych skryptów SQL, których można użyć do eksplorowania magazynów danych w SQL Server.

1. Pobierz liczbę obserwacji dziennie
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobieranie poziomów w kolumnie kategorii
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu dwóch kolumn kategorii 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobieranie dystrybucji dla kolumn liczbowych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> W przypadku praktycznego przykładu można użyć [zestawu danych NYC taksówki](https://www.andresmh.com/nyctaxitrips/) i odwołać się do IPNB [NYC danych przetwarzanie przy użyciu notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) w celu uzyskania kompleksowego przewodnika.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Eksplorowanie danych SQL przy użyciu języka Python
Używanie języka Python do eksplorowania danych i generowania funkcji, gdy dane są w SQL Server są podobne do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python, zgodnie z opisem w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md). Załaduj dane z bazy danych do Pandas Dataframe, a następnie można je przetworzyć w dalszej postaci. Dokumentuje proces łączenia się z bazą danych i ładowania danych do ramki Dataframe w tej sekcji.

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

Teraz można korzystać z Pandas Dataframe, jak opisano w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Proces nauki o danych zespołowych w przykładowym działaniu
Aby zapoznać się z kompleksowym przykładem procesu usługi Cortana Analytics przy użyciu publicznego zestawu danych, zobacz [proces nauka danych zespołu w akcji: używanie SQL Server](sql-walkthrough.md).


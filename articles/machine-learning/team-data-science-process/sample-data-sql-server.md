---
title: Przykładowe dane w SQL Server na platformie Azure — proces nauki danych zespołu
description: Przykładowe dane przechowywane w SQL Server na platformie Azure przy użyciu programu SQL lub języka programowania Python, a następnie przenieść je do Azure Machine Learning.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321862"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Przykładowe dane w programie SQL Server na platformie Azure

W tym artykule przedstawiono sposób próbkowania danych przechowywanych w SQL Server na platformie Azure przy użyciu programu SQL lub języka programowania w języku Python. Przedstawiono w nim również sposób przenoszenia danych próbkowanych do Azure Machine Learning przez zapisanie ich w pliku, przekazanie go do obiektu blob platformy Azure, a następnie przeczytanie go do Azure Machine Learning Studio.

Próbkowanie w języku Python używa biblioteki ODBC [moduł pyodbc](https://code.google.com/p/pyodbc/) do nawiązywania połączenia z SQL Server na platformie Azure i biblioteki [Pandas](https://pandas.pydata.org/) w celu wykonania próbkowania.

> [!NOTE]
> Przykładowy kod SQL w tym dokumencie zakłada, że dane są w SQL Server na platformie Azure. Jeśli tak nie jest, zapoznaj się z tematem [przenoszenie danych do SQL Server w](move-sql-server-virtual-machine.md) artykule dotyczącym platformy Azure, aby uzyskać instrukcje dotyczące przenoszenia danych do SQL Server na platformie Azure.
> 
> 

**Dlaczego warto Przykładowo dane?**
Jeśli zestaw danych, który planujesz analizować jest duży, zazwyczaj dobrym pomysłem jest Przepróbkowanie danych w celu zmniejszenia ich do mniejszej, ale reprezentatywnej i większej możliwej do zarządzania wielkości. Próbkowanie ułatwia zrozumienie, eksplorację i inżynierowanie danych. Jej rolą w [procesie nauki danych zespołu (przetwarzania TDSP)](./index.yml) jest umożliwienie szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Korzystanie z języka SQL
W tej sekcji opisano kilka metod korzystania z programu SQL do wykonywania prostych losowych próbkowania dla danych w bazie danych. Wybierz metodę na podstawie rozmiaru danych i jego rozkładu.

Poniższe dwa elementy pokazują, jak używać `newid` programu w SQL Server, aby wykonać próbkowanie. Wybór metody zależy od tego, jak losowo ma być pobierana próbka (pk_id w poniższym przykładowym kodzie jest założono, że jest automatycznie generowanym kluczem podstawowym).

1. Mniej rygorystyczne losowe próbkowanie

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Bardziej losowy przykład 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Można również użyć tej metody do próbkowania danych. Ta opcja może być lepszym rozwiązaniem, jeśli rozmiar danych jest duży (przy założeniu, że dane na różnych stronach nie są skorelowane) i aby zapytanie zostało wykonane w rozsądnym czasie.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Możesz eksplorować i generować funkcje z tego przykładowych danych, przechowując je w nowej tabeli
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Nawiązywanie połączenia z Azure Machine Learning
Możesz bezpośrednio użyć przykładowych zapytań opisanych powyżej w module Azure Machine Learning [Import danych][import-data] , aby w pełni próbkować dane na bieżąco i przełączać je do eksperymentu Azure Machine Learning. Zrzut ekranu przedstawiający użycie modułu czytnika do odczytywania danych próbkowanych jest przedstawiony tutaj:

![kod SQL czytnika][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Korzystanie z języka programowania w języku Python
W tej sekcji przedstawiono użycie [biblioteki moduł pyodbc](https://code.google.com/p/pyodbc/) w celu nawiązania połączenia ODBC z bazą danych programu SQL Server w języku Python. Parametry połączenia z bazą danych są następujące: (zastąp nazwę ServerName, dbname, username i Password swoją konfiguracją):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Biblioteka [Pandas](https://pandas.pydata.org/) w języku Python oferuje bogaty zestaw struktur danych i narzędzi do analizy danych na potrzeby manipulowania danymi na potrzeby programowania w języku Python. Poniższy kod odczytuje przykład 0,1% danych z tabeli w Azure SQL Database do danych Pandas:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Teraz można korzystać z przykładowych danych w ramce danych Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Nawiązywanie połączenia z Azure Machine Learning
Możesz użyć następującego przykładowego kodu, aby zapisać dane z próbką w dół do pliku i przekazać je do obiektu blob platformy Azure. Dane w obiekcie blob mogą być bezpośrednio odczytywane w Azure Machine Learning eksperymentu przy użyciu modułu [Importuj dane][import-data] . Kroki tego procesu są następujące: 

1. Zapisz ramkę danych Pandas do pliku lokalnego

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Przekaż plik lokalny do obiektu blob platformy Azure

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Odczytaj dane z obiektu blob platformy Azure przy użyciu modułu Azure Machine Learning [Importuj dane][import-data] , jak pokazano na poniższym ekranie:

![Obiekt BLOB czytnika][2]

## <a name="the-team-data-science-process-in-action-example"></a>Proces nauki o danych zespołowych w przykładowym działaniu
Aby zapoznać się z przykładem procesu nauki o danych zespołowych przy użyciu publicznego zestawu danych, zobacz [proces analizy danych zespołu w akcji: używanie SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data
---
title: Eksplorowanie danych w magazynie obiektów blob platformy Azure za pomocą pandas — proces nauki o danych zespołu
description: Jak eksplorować dane, które są przechowywane w kontenerze obiektów blob platformy Azure przy użyciu pakietu Pandas Python.
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
ms.openlocfilehash: e429dce497411305964cb1ec5298228dc4093b1f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685951"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Eksplorowanie danych w magazynie obiektów blob platformy Azure za pomocą pandas

W tym artykule opisano, jak eksplorować dane, które są przechowywane w kontenerze obiektów blob platformy Azure przy użyciu pakietu [Pandas](https://pandas.pydata.org/) Python.

To zadanie jest krokiem w [procesie nauki o danych zespołu.](overview.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto magazynu platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Przechowywane dane na koncie magazynu obiektów blob platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Przenoszenie danych do i z usługi Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Ładowanie danych do pandas DataFrame
Aby eksplorować i manipulować zestawem danych, należy go najpierw pobrać ze źródła obiektu blob do pliku lokalnego, który następnie można załadować w pandas DataFrame. Oto kroki, które należy wykonać w przypadku tej procedury:

1. Pobierz dane z obiektu blob platformy Azure z następującym przykładem kodu języka Python przy użyciu usługi obiektów Blob. Zastąp zmienną w poniższym kodzie określonymi wartościami:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Odczyt danych do pandas DataFrame z pobranego pliku.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Teraz możesz przystąpić do eksplorowania danych i generowania funkcji tego zestawu danych.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Przykłady eksploracji danych przy użyciu pand
Oto kilka przykładów sposobów eksplorowania danych przy użyciu pand:

1. Sprawdzanie **liczby wierszy i kolumn**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Sprawdź** pierwsze lub ostatnie kilka **wierszy** w następującym zestawie danych:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Sprawdź **typ danych, który** każda kolumna została zaimportowana, jako przy użyciu następującego przykładowego kodu

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Sprawdź **podstawowe statystyki** kolumn w zestawie danych w następujący sposób

    ```python
    dataframe_blobdata.describe()
    ```

1. Spójrz na liczbę wpisów dla każdej wartości kolumny w następujący sposób

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Zliczanie brakujących wartości** w porównaniu z rzeczywistą liczbą wpisów w każdej kolumnie przy użyciu następującego przykładowego kodu

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Jeśli w danych **brakuje wartości** dla określonej kolumny, możesz je upuścić w następujący sposób:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Innym sposobem zastąpienia brakujących wartości jest funkcja trybu:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Tworzenie wykresu **histogramu** przy użyciu zmiennej liczby pojemników w celu wykreślenia rozkładu zmiennej

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Spójrz na **korelacje** między zmiennymi za pomocą scatterplot lub za pomocą wbudowanej funkcji korelacji

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

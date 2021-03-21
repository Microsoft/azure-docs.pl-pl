---
title: Eksplorowanie danych w usłudze Azure Blob Storage przy użyciu procesu nauki o danych Pandas-Team
description: Jak eksplorować dane przechowywane w kontenerze obiektów blob platformy Azure przy użyciu pakietu języka Python Pandas.
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
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805677"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Eksplorowanie danych w usłudze Azure Blob Storage przy użyciu usługi Pandas

W tym artykule opisano, jak eksplorować dane przechowywane w kontenerze obiektów blob platformy Azure przy użyciu pakietu języka Python [Pandas](https://pandas.pydata.org/) .

To zadanie jest krokiem w [procesie nauki danych zespołu](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Dane są przechowywane na koncie usługi Azure Blob Storage. Jeśli potrzebujesz instrukcji, zobacz temat [przeniesienie danych do i z usługi Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Załaduj dane do Pandas Dataframe
Aby eksplorować zestaw danych i manipulować nim, najpierw należy go pobrać ze źródła obiektów BLOB do pliku lokalnego, który następnie można załadować w Pandas Dataframe. Poniżej przedstawiono kroki, które należy wykonać w przypadku tej procedury:

1. Pobierz dane z obiektu blob platformy Azure przy użyciu poniższego przykładu kodu w języku Python za pomocą Blob service. Zamień zmienną w poniższym kodzie na określone wartości:

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
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Odczytaj dane do Pandas Dataframe z pobranego pliku.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Teraz możesz przystąpić do eksplorowania danych i generowania funkcji w tym zestawie danych.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Przykłady eksploracji danych przy użyciu Pandas
Poniżej przedstawiono kilka przykładów sposobu eksplorowania danych przy użyciu Pandas:

1. Sprawdzanie **liczby wierszy i kolumn**

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Sprawdź** pierwsze lub ostatnie **wiersze** w następującym zestawie danych:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Sprawdź **Typ danych** , które zostały zaimportowane do każdej kolumny przy użyciu następującego przykładowego kodu

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Sprawdź **podstawowe dane statystyczne** kolumn w zestawie danych w następujący sposób:

    ```python
    dataframe_blobdata.describe()
    ```

1. Sprawdź liczbę wpisów dla każdej wartości kolumny w następujący sposób:

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Zlicz brakujące wartości** w porównaniu do rzeczywistej liczby wpisów w każdej kolumnie przy użyciu następującego przykładowego kodu

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Jeśli w danych **brakuje wartości** dla konkretnej kolumny, można je upuścić w następujący sposób:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Innym sposobem zastąpienia brakujących wartości jest funkcja Mode:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Tworzenie wykresu **histogramu** przy użyciu zmiennej liczby pojemników do wykreślania rozkładu zmiennej

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Przyjrzyj się **korelacji** między zmiennymi przy użyciu scatterplot lub wbudowanej funkcji korelacji

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

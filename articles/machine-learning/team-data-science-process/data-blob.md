---
title: Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy — proces nauki danych zespołu
description: Eksploruj dane i Generuj funkcje z danych przechowywanych w usłudze Azure Blob Storage za pomocą zaawansowanej analizy.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93307167"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Przetwarzanie danych obiektów blob platformy Azure za pomocą zaawansowanej analizy
Ten dokument obejmuje Eksplorowanie danych i generowanie funkcji danych przechowywanych w usłudze Azure Blob Storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Załaduj dane do ramki danych Pandas
Aby eksplorować zestaw danych i manipulować nim, należy go pobrać ze źródła obiektów BLOB do pliku lokalnego, który następnie można załadować w Pandasej ramce danych. Poniżej przedstawiono kroki, które należy wykonać w przypadku tej procedury:

1. Pobierz dane z obiektu blob platformy Azure przy użyciu następującego przykładowego kodu w języku Python za pomocą Blob service. Zamień zmienną w poniższym kodzie na określone wartości: 
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```
2. Odczytaj dane do ramki danych Pandas z pobranego pliku.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Teraz możesz przystąpić do eksplorowania danych i generowania funkcji w tym zestawie danych.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Eksploracja danych
Poniżej przedstawiono kilka przykładów sposobu eksplorowania danych przy użyciu Pandas:

1. Sprawdzanie liczby wierszy i kolumn 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Sprawdź pierwsze lub ostatnie wiersze w zestawie danych w następujący sposób:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Sprawdź typ danych, które zostały zaimportowane do każdej kolumny przy użyciu następującego przykładowego kodu
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Sprawdź podstawowe dane statystyczne kolumn w zestawie danych w następujący sposób:
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Sprawdź liczbę wpisów dla każdej wartości kolumny w następujący sposób:
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Zlicz brakujące wartości w porównaniu do rzeczywistej liczby wpisów w każdej kolumnie przy użyciu następującego przykładowego kodu
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Jeśli w danych brakuje wartości dla konkretnej kolumny, można je upuścić w następujący sposób:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   Innym sposobem zastąpienia brakujących wartości jest funkcja Mode:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Tworzenie wykresu histogramu przy użyciu zmiennej liczby pojemników do wykreślania rozkładu zmiennej    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Przyjrzyj się korelacji między zmiennymi przy użyciu scatterplot lub wbudowanej funkcji korelacji
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generowanie funkcji
Można generować funkcje przy użyciu języka Python w następujący sposób:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Generowanie funkcji opartej na wartościach wskaźnika
Funkcje kategorii można utworzyć w następujący sposób:

1. Sprawdź dystrybucję kolumny kategorii:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Generuj wartości wskaźnika dla każdej wartości kolumny
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Dołącz do kolumny wskaźnika z pierwotną ramką danych 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Usuń oryginalną zmienną:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generowanie funkcji pakowania
W celu wygenerowania funkcji Binned wykonaj następujące czynności:

1. Dodawanie sekwencji kolumn do przedziału kolumny liczbowej
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Konwertuj pakowania na sekwencję zmiennych logicznych
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Na koniec Dołącz pierwotne zmienne z powrotem do oryginalnej ramki danych
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Zapisywanie danych z powrotem do obiektu blob platformy Azure i zużywanie w Azure Machine Learning
Po zbadaniu danych i utworzeniu niezbędnych funkcji można przekazać dane (próbkowane lub featurized) do obiektu blob platformy Azure i użyć go w Azure Machine Learning, wykonując następujące czynności: dodatkowe funkcje można utworzyć w Azure Machine Learning Studio (klasyczny). 

1. Zapisz ramkę danych do pliku lokalnego
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Przekaż dane do obiektu blob platformy Azure w następujący sposób:
   
    ```python
    from azure.storage.blob import BlobService
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
3. Teraz dane można odczytać z obiektu BLOB przy użyciu modułu Azure Machine Learning [Import danych][import-data] , jak pokazano na poniższym ekranie:

![Obiekt BLOB czytnika][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
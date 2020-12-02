---
title: Przekształcanie za pomocą usługi Azure Databricks
description: Dowiedz się, jak używać szablonu rozwiązania do przekształcania danych przy użyciu notesu datakostki w Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 1c20508d27d03c00a6842979731fb905bbaa9def
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461244"
---
# <a name="transformation-with-azure-databricks"></a>Przekształcanie za pomocą usługi Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz kompleksowy potok zawierający dane dotyczące **weryfikacji**, **kopiowania** i działania **notesu** w Azure Data Factory.

- **Walidacja** gwarantuje, że źródłowy zestaw danych jest gotowy do użycia na użytek podrzędny przed wywołaniem zadania kopiowania i analizy.

- **Kopiuj dane** duplikują źródłowy zestaw danych do magazynu ujścia, który jest instalowany jako DBFS w notesie Azure Databricks. W ten sposób zestaw danych może być bezpośrednio używany przez platformę Spark.

- **Notes** wyzwala Notes datakostki, który przekształca zestaw danych. Dodaje również zestaw danych do przetworzonego folderu lub usługi Azure Synapse Analytics.

Dla uproszczenia szablon w tym samouczku nie tworzy zaplanowanego wyzwalacza. W razie potrzeby można dodać jeden z nich.

![Diagram potoku](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto magazynu obiektów blob platformy Azure z kontenerem o nazwie `sinkdata` do użycia jako ujścia.

  Zanotuj nazwę konta magazynu, nazwę kontenera i klucz dostępu. Te wartości będą potrzebne później w szablonie.

- Obszar roboczy Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importowanie notesu do transformacji

Aby zaimportować Notes **transformacji** do obszaru roboczego datakostki:

1. Zaloguj się do obszaru roboczego Azure Databricks, a następnie wybierz pozycję **Importuj**.
       ![Polecenie menu do importowania obszaru roboczego ](media/solution-template-Databricks-notebook/import-notebook.png) Ścieżka obszaru roboczego może różnić się od pokazanej, ale zapamiętać ją w późniejszym czasie.
1. Wybierz pozycję **Importuj z: adres URL**. W polu tekstowym wprowadź `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Opcje importowania notesu](media/solution-template-Databricks-notebook/import-from-url.png)

1. Teraz zaktualizujmy Notes **transformacji** przy użyciu informacji o połączeniu z magazynem.

   W zaimportowanym notesie przejdź do **polecenia 5** , jak pokazano w poniższym fragmencie kodu.

   - Zastąp `<storage name>` i `<access key>` własnymi informacjami o połączeniu z magazynem.
   - Użyj konta magazynu z `sinkdata` kontenerem.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generowanie **tokenu dostępu do datakostki** dla Data Factory w celu uzyskania dostępu do datakostki.
   1. W obszarze roboczym datakostki wybierz ikonę profilu użytkownika w prawym górnym rogu.
   1. Wybierz pozycję **Ustawienia użytkownika**.
    ![Polecenie menu dla ustawień użytkownika](media/solution-template-Databricks-notebook/user-setting.png)
   1. Wybierz pozycję **Generuj nowy token** na karcie **tokeny dostępu** .
   1. Wybierz pozycję **Generuj**.

    ![Przycisk "Generuj"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Zapisz token dostępu* do późniejszego użycia podczas tworzenia połączonej usługi datakostki. Token dostępu wygląda podobnie `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Jak używać tego szablonu

1. Przejdź do **przekształcenia przy użyciu szablonu Azure Databricks** i Utwórz nowe połączone usługi dla następujących połączeń.

   ![Ustawienie połączeń](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Połączenie źródłowego obiektu BLOB** — w celu uzyskania dostępu do danych źródłowych.

       W tym ćwiczeniu można użyć publicznego magazynu obiektów blob, który zawiera pliki źródłowe. Aby uzyskać informacje dotyczące konfiguracji, należy zapoznać się z poniższym zrzutem ekranu. Użyj następującego **adresu URL sygnatury dostępu współdzielonego** , aby nawiązać połączenie z magazynem źródłowym (dostęp tylko do odczytu):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Wybór metody uwierzytelniania i adresu URL sygnatury dostępu współdzielonego](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Połączenie docelowego obiektu BLOB** — do przechowywania skopiowanych danych.

       W oknie **Nowa połączona usługa** wybierz obiekt blob magazynu ujścia.

       ![Obiekt blob magazynu ujścia jako nowa połączona usługa](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** — aby nawiązać połączenie z klastrem datakosteks.

        Utwórz połączoną usługę datakostki przy użyciu wcześniej wygenerowanego klucza dostępu. Możesz wybrać *interaktywny klaster* , jeśli go masz. W tym przykładzie zostanie użyta opcja **nowy klaster zadań** .

        ![Wybory dotyczące nawiązywania połączenia z klastrem](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Wybierz pozycję **Użyj tego szablonu**. Zobaczysz utworzony potok.

    ![Tworzenie potoku](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Wprowadzenie i konfiguracja potoku

W nowym potoku większość ustawień jest konfigurowana automatycznie z wartościami domyślnymi. Przejrzyj konfiguracje potoku i wprowadź niezbędne zmiany.

1. W **flagi dostępność** działania **walidacji** Sprawdź, czy wartość zestawu **danych** źródłowych została `SourceAvailabilityDataset` utworzona wcześniej.

   ![Wartość źródłowego zestawu danych](media/solution-template-Databricks-notebook/validation-settings.png)

1. W oknie działanie **Kopiuj dane** **do obiektów BLOB** Sprawdź karty **źródłowe** i **ujścia** . Zmień ustawienia w razie potrzeby.

   - **Karta źródło karty** źródłowej ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Karta ujścia karty **ujścia** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. W **transformacji** działania **notesu** Przejrzyj i zaktualizuj ścieżki oraz ustawienia zgodnie z wymaganiami.

   **Połączona usługa datakostki** powinna być wstępnie wypełniona wartością z poprzedniego kroku, jak pokazano: ![ wartość wypełniono dla połączonej usługi datakostki](media/solution-template-Databricks-notebook/notebook-activity.png)

   Aby sprawdzić ustawienia **notesu** :
  
    1. Wybierz kartę **Ustawienia** . W polu **ścieżka notesu** Sprawdź, czy ścieżka domyślna jest poprawna. Może być konieczne przeszukanie i wybranie odpowiedniej ścieżki notesu.

       ![Ścieżka notesu](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Rozwiń selektor **parametrów podstawowych** i sprawdź, czy parametry są zgodne z tym, co pokazano na poniższym zrzucie ekranu. Te parametry są przesyłane do notesu datacegły z Data Factory.

       ![Parametry podstawowe](media/solution-template-Databricks-notebook/base-parameters.png)

1. Sprawdź, czy **Parametry potoku** są zgodne z tym, co pokazano na poniższym zrzucie ekranu: ![ Parametry potoku](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Nawiąż połączenie z zestawami danych.

    >[!NOTE]
    >W poniższych zestawach danych ścieżka pliku została automatycznie określona w szablonie. Jeśli jakiekolwiek zmiany są wymagane, należy się upewnić, że należy określić ścieżkę dla **kontenera** i **katalogu** w przypadku błędu połączenia.

   - **SourceAvailabilityDataset** — aby sprawdzić, czy dane źródłowe są dostępne.

     ![Wybory dla połączonej usługi i ścieżki pliku dla SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** — Aby uzyskać dostęp do danych źródłowych.

       ![Wybory dla połączonej usługi i ścieżki pliku dla SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** — aby skopiować dane do lokalizacji docelowej ujścia. Użyj następujących wartości:

     - **Połączona usługa**  -  `sinkBlob_LS` utworzona w poprzednim kroku.

     - **Ścieżka pliku**  -  `sinkdata/staged_sink` .

       ![Wybory dla połączonej usługi i ścieżki pliku dla DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Wybierz pozycję **Debuguj** , aby uruchomić potok. Aby uzyskać bardziej szczegółowe dzienniki platformy Spark, można znaleźć łącze do dzienników datakostek.

    ![Połącz z dziennikami datacegłs z danych wyjściowych](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Możesz również zweryfikować plik danych przy użyciu Eksplorator usługi Azure Storage.

    > [!NOTE]
    > W celu skorelowania z uruchomieniami potoku Data Factory ten przykład dołącza identyfikator uruchomienia potoku z fabryki danych do folderu danych wyjściowych. Ułatwia to śledzenie plików generowanych przez poszczególne uruchomienia.
    > ![Identyfikator dołączonego uruchomienia potoku](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

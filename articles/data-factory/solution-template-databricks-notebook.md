---
title: Przekształcanie za pomocą usługi Azure Databricks
description: Dowiedz się, jak użyć szablonu rozwiązania do przekształcania danych przy użyciu notesu Databricks w usłudze Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414845"
---
# <a name="transformation-with-azure-databricks"></a>Przekształcanie za pomocą usługi Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz potok end-to-end, który zawiera **sprawdzanie poprawności,** **kopiowanie danych**i **działania notesu** w usłudze Azure Data Factory.

- **Sprawdzanie poprawności** gwarantuje, że źródłowy zestaw danych jest gotowy do użycia niższego rzędu przed wyzwoleniem zadania kopiowania i analizy.

- **Kopiowanie danych** powiela źródłowy zestaw danych do magazynu ujścia, który jest zainstalowany jako DBFS w notesie usługi Azure Databricks. W ten sposób zestaw danych może być bezpośrednio używane przez spark.

- **Notes** wyzwala notesu Databricks, który przekształca zestaw danych. Dodaje również zestaw danych do przetworzonego folderu lub usługi Azure SQL Data Warehouse.

Dla uproszczenia szablon w tym samouczku nie tworzy zaplanowanego wyzwalacza. W razie potrzeby można go dodać.

![Schemat rurociągu](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto magazynu obiektów Blob platformy `sinkdata` Azure z kontenerem wywoływanym do użycia jako obiekt sink.

  Zanotuj nazwę konta magazynu, nazwę kontenera i klucz dostępu. Te wartości będą potrzebne w dalszej części szablonu.

- Obszar roboczy usługi Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importowanie notesu do transformacji

Aby zaimportować notes **transformacji** do obszaru roboczego Databricks:

1. Zaloguj się do obszaru roboczego usługi Azure Databricks, a następnie wybierz pozycję **Importuj**.
       ![Polecenie Menu do importowania](media/solution-template-Databricks-notebook/import-notebook.png) obszaru roboczego Ścieżka obszaru roboczego może się różnić od pokazanej, ale zapamiętaj ją na później.
1. Wybierz **pozycję Importuj z: URL**. W polu tekstowym `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`wprowadź plik .

   ![Wybór do importowania notesu](media/solution-template-Databricks-notebook/import-from-url.png)

1. Teraz zaktualizujmy notes **transformacji** o informacje o połączeniu magazynu.

   W zaimportowanym notesie przejdź do **polecenia 5,** jak pokazano w poniższym fragmentie kodu.

   - Wymień `<storage name>`i `<access key>` za pomocą własnych informacji o połączeniu pamięci masowej.
   - Użyj konta magazynu `sinkdata` w kontenerze.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generowanie **tokenu dostępu Databricks** dla fabryki danych, aby uzyskać dostęp do Databricks.
   1. W obszarze roboczym Databricks wybierz ikonę profilu użytkownika w prawym górnym rogu.
   1. **Wybierz pozycję Ustawienia użytkownika**.
    ![Polecenie Menu dla ustawień użytkownika](media/solution-template-Databricks-notebook/user-setting.png)
   1. Wybierz **pozycję Generuj nowy token** na karcie **Tokeny dostępu.**
   1. Wybierz **pozycję Generuj**.

    ![Przycisk "Generuj"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Zapisz token dostępu do* późniejszego użycia w tworzeniu usługi połączonej Databricks. Token dostępu wygląda `dapi32db32cbb4w6eee18b7d87e45exxxxxx`mniej więcej tak .

## <a name="how-to-use-this-template"></a>Jak korzystać z tego szablonu

1. Przejdź do szablonu **Transformacja za pomocą usługi Azure Databricks** i utwórz nowe połączone usługi dla następujących połączeń.

   ![Ustawienie połączenia](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Źródło Blob Connection** - aby uzyskać dostęp do danych źródłowych.

       W tym ćwiczeniu można użyć magazynu publicznego obiektu blob, który zawiera pliki źródłowe. Odwołaj się do poniższego zrzutu ekranu dla konfiguracji. Użyj następującego **adresu URL sygnatury dostępu Współdzielonego,** aby połączyć się z magazynem źródłowym (dostęp tylko do odczytu):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Wybór metody uwierzytelniania i adresu URL sygnatury dostępu Współdzielonego](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Docelowe połączenie obiektu blob** — do przechowywania skopiowanych danych.

       W oknie **Nowa usługa połączona** wybierz obiekt blob magazynu ujścia.

       ![Obiekt blob magazynu ujścia jako nowa usługa połączona](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Usługi Azure Databricks** — aby połączyć się z klastrem Databricks.

        Utwórz usługę połączony z usługą Databricks przy użyciu klucza dostępu, który został wygenerowany wcześniej. Jeśli go posiadasz, możesz wybrać *klaster interaktywny.* W tym przykładzie użyto opcji **Nowy klaster zadań.**

        ![Wybór do łączenia się z klastrem](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Wybierz pozycję **Użyj tego szablonu**. Zostanie wyświetlony potok utworzony.

    ![Tworzenie potoku](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Wprowadzenie i konfiguracja rurociągu

W nowym potoku większość ustawień jest konfigurowana automatycznie z wartościami domyślnymi. Przejrzyj konfiguracje potoku i wykonuj niezbędne zmiany.

1. W **flagę** **Dostępność**działania sprawdzania poprawności sprawdź, czy `SourceAvailabilityDataset` wartość **źródłowego zestawu danych** jest ustawiona na wcześniej utworzoną wartość.

   ![Wartość źródłowego zestawu danych](media/solution-template-Databricks-notebook/validation-settings.png)

1. W pliku aktywności **kopiowania danych** **do obiektu blob**zaznacz kartę **Źródło** i **Ujście.** W razie potrzeby zmień ustawienia.

   - **Karta** ![Źródło Karta Źródło karta](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Karta ![ **Zlew** zlewka](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. W transformacji **aktywności** **notesu** przejrzyj i zaktualizuj ścieżki i ustawienia w razie potrzeby.

   **Usługa połączona databricks** powinna być wstępnie wypełniona wartością ![z poprzedniego kroku, jak pokazano na rysunku: Wartość wypełniona dla usługi połączonej Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Aby sprawdzić ustawienia **notesu:**
  
    1. Wybierz kartę **Ustawienia.** W przypadku **ścieżki notesu**sprawdź, czy ścieżka domyślna jest poprawna. Może być konieczne przeglądanie i wybranie właściwej ścieżki notesu.

       ![Ścieżka notesu](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Rozwiń selektor **parametrów podstawowych** i sprawdź, czy parametry są zgodne z tym, co jest pokazane na poniższym zrzucie ekranu. Parametry te są przekazywane do notesu Databricks z fabryki danych.

       ![Parametry podstawowe](media/solution-template-Databricks-notebook/base-parameters.png)

1. Sprawdź, czy **parametry potoku są** zgodne ![z tym, co jest pokazane na poniższym zrzucie ekranu: Parametry potoku](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Połącz się z zestawami danych.

   - **SourceAvailabilityDataset** — aby sprawdzić, czy dane źródłowe są dostępne.

     ![Wybór połączonej usługi i ścieżki plików dla zestawu danych SourceAvailability](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** — aby uzyskać dostęp do danych źródłowych.

       ![Wybór połączonej usługi i ścieżki plików dla zestawu danych SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** — aby skopiować dane do lokalizacji docelowej ujścia. Wprowadź następujące wartości:

     - **Usługa połączona** - , utworzona w poprzednim`sinkBlob_LS`kroku.

     - **Ścieżka pliku** - `sinkdata/staged_sink`

       ![Wybór połączonej usługi i ścieżki plików dla zestawu danych DestinationFiles](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Wybierz **debugowanie,** aby uruchomić potok. Można znaleźć łącze do dzienników Databricks bardziej szczegółowe dzienniki platformy Spark.

    ![Łącze do dzienników Databricks z danych wyjściowych](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Można również zweryfikować plik danych przy użyciu Usługi Azure Storage Explorer.

    > [!NOTE]
    > W celu skorelowania z uruchomień potoku fabryki danych w tym przykładzie dołącza identyfikator uruchomienia potoku z fabryki danych do folderu wyjściowego. Pomaga to śledzić pliki generowane przez każde uruchomienie.
    > ![Identyfikator uruchomienia potoku](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

---
title: Przesyłanie strumieniowe danych z Stream Analytics do Data Lake Storage Gen1 na platformie Azure
description: Dowiedz się, jak używać Azure Data Lake Storage Gen1 jako danych wyjściowych dla zadania Azure Stream Analytics, z prostym scenariuszem, który odczytuje dane z obiektu BLOB usługi Azure Storage.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576221"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Przesyłanie strumieniowe danych z Azure Storage Blob do Azure Data Lake Storage Gen1 przy użyciu Azure Stream Analytics
W tym artykule dowiesz się, jak używać Azure Data Lake Storage Gen1 jako danych wyjściowych dla zadania Azure Stream Analytics. W tym artykule przedstawiono prosty scenariusz, który odczytuje dane z obiektu BLOB usługi Azure Storage (dane wejściowe) i zapisuje dane do Data Lake Storage Gen1 (dane wyjściowe).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Storage**. Kontener obiektów BLOB zostanie użyty z tego konta do wprowadzania danych dla zadania Stream Analytics. W tym samouczku przyjęto założenie, że masz konto magazynu o nazwie **storageforasa** i kontener w ramach konta o nazwie **storageforasacontainer**. Po utworzeniu kontenera Przekaż do niego przykładowy plik danych. 
  
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). Załóżmy, że masz konto Data Lake Storage Gen1 o nazwie **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Zacznij od utworzenia Stream Analytics zadania zawierającego źródło danych wejściowych i wyjściowych. W tym samouczku źródłem jest kontener obiektów blob platformy Azure, a lokalizacja docelowa to Data Lake Storage Gen1.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym okienku kliknij pozycję **Stream Analytics zadania**, a następnie kliknij pozycję **Dodaj**.

    ![Tworzenie zadania Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Tworzenie zadania usługi Stream Analytics")

    > [!NOTE]
    > Upewnij się, że tworzysz zadanie w tym samym regionie co konto magazynu, lub powiążesz się z dodatkowym kosztem przeniesienia danych między regionami.
    >

## <a name="create-a-blob-input-for-the-job"></a>Tworzenie danych wejściowych obiektu BLOB dla zadania

1. Otwórz stronę Stream Analytics zadania, w okienku po lewej stronie kliknij kartę **dane wejściowe** , a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawiający blok zadania Stream Analytics z opcją dane wejściowe i opcję Dodaj strumień wejściowy o nazwie out.](./media/data-lake-store-stream-analytics/create.input.1.png "Dodawanie danych wejściowych do zadania")

2. W bloku **nowe dane wejściowe** podaj następujące wartości.

    ![Zrzut ekranu przedstawiający blok BLOB Storage — nowe dane wejściowe.](./media/data-lake-store-stream-analytics/create.input.2.png "Dodawanie danych wejściowych do zadania")

   * Dla **aliasu wejściowego** wprowadź unikatową nazwę dla danych wejściowych zadania.
   * W obszarze **Typ źródła** wybierz pozycję **strumień danych**.
   * W obszarze **Źródło** wybierz pozycję **Magazyn obiektów BLOB**.
   * W obszarze **subskrypcja** wybierz pozycję **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
   * W polu **konto magazynu** wybierz konto magazynu utworzone w ramach wymagań wstępnych. 
   * W polu **kontener** wybierz kontener utworzony na wybranym koncie magazynu.
   * W obszarze **format serializacji zdarzenia** wybierz opcję **CSV**.
   * Dla **ogranicznika** wybierz pozycję **Tab**.
   * W obszarze **kodowanie** wybierz pozycję **UTF-8**.

     Kliknij pozycję **Utwórz**. Portal doda teraz dane wejściowe i przetestuje połączenie z nim.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Utwórz Data Lake Storage Gen1 dane wyjściowe dla zadania

1. Otwórz stronę Stream Analytics zadania, kliknij kartę dane **wyjściowe** , kliknij przycisk **Dodaj**, a następnie wybierz pozycję **Data Lake Storage Gen1**.

    ![Zrzut ekranu przedstawiający blok zadania Stream Analytics z opcją dane wyjściowe, opcja Dodaj i Data Lake Storage generacji 1 o nazwie.](./media/data-lake-store-stream-analytics/create.output.1.png "Dodawanie danych wyjściowych do zadania")

2. W bloku **nowe dane wyjściowe** podaj następujące wartości.

    ![Zrzut ekranu przedstawiający blok Data Lake Storage Gen 1-New Output z opcją Autoryzuj.](./media/data-lake-store-stream-analytics/create.output.2.png "Dodawanie danych wyjściowych do zadania")

    * Dla **aliasu danych wyjściowych** wprowadź unikatową nazwę dla danych wyjściowych zadania. Jest to przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego konta Data Lake Storage Gen1.
    * Zostanie wyświetlony monit o autoryzację dostępu do konta Data Lake Storage Gen1. Kliknij przycisk **Autoryzuj**.

3. W bloku **nowe dane wyjściowe** Kontynuuj udostępnianie następujących wartości.

    ![Zrzut ekranu przedstawiający blok Data Lake Storage Gen 1-New Output.](./media/data-lake-store-stream-analytics/create.output.3.png "Dodawanie danych wyjściowych do zadania")

   * W polu **nazwa konta** wybierz już utworzone konto Data Lake Storage Gen1, do którego mają być wysyłane dane wyjściowe zadania.
   * W polu **wzorzec prefiksu ścieżki** wprowadź ścieżkę pliku używaną do zapisywania plików w ramach określonego konta Data Lake Storage Gen1.
   * W przypadku **formatu daty** w przypadku użycia tokenu daty w ścieżce prefiksu można wybrać format daty, w którym są zorganizowane pliki.
   * W przypadku **formatu** czasu, jeśli w ścieżce prefiksowej użyto tokenu czasu, określ format czasu, w którym są zorganizowane pliki.
   * W obszarze **format serializacji zdarzenia** wybierz opcję **CSV**.
   * Dla **ogranicznika** wybierz pozycję **Tab**.
   * W obszarze **kodowanie** wybierz pozycję **UTF-8**.
    
     Kliknij pozycję **Utwórz**. Portal dodaje teraz dane wyjściowe i testuje połączenie z nim.
    
## <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Aby uruchomić zadanie Stream Analytics, należy uruchomić zapytanie z karty **zapytanie** . Na potrzeby tego samouczka można uruchomić przykładowe zapytanie, zastępując symbole zastępcze aliasami wejściowymi i wyjściowymi zadań, jak pokazano na poniższym zrzucie ekranu.

    ![Uruchom zapytanie](./media/data-lake-store-stream-analytics/run.query.png "Uruchamianie zapytania")

2. Kliknij przycisk **Zapisz** w górnej części ekranu, a następnie na karcie **Przegląd** kliknij przycisk **Uruchom**. W oknie dialogowym wybierz opcję **czas niestandardowy**, a następnie ustaw bieżącą datę i godzinę.

    ![Ustawianie czasu zadania](./media/data-lake-store-stream-analytics/run.query.2.png "Ustawianie czasu zadania")

    Kliknij przycisk **Uruchom** , aby uruchomić zadanie. Uruchomienie zadania może potrwać kilka minut.

3. Aby wyzwolić zadanie w celu wybrania danych z obiektu BLOB, skopiuj przykładowy plik danych do kontenera obiektów BLOB. Przykładowy plik danych można pobrać z [repozytorium Azure Data Lake git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Na potrzeby tego samouczka skopiujemy plik **vehicle1_09142014.csv**. Aby przekazać dane do kontenera obiektów blob, można użyć różnych klientów, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

4. Na karcie **Przegląd** w obszarze **monitorowanie** Sprawdź, jak dane zostały przetworzone.

    ![Monitorowanie zadania](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorowanie zadania")

5. Na koniec możesz sprawdzić, czy dane wyjściowe zadania są dostępne na koncie Data Lake Storage Gen1. 

    ![Weryfikacja danych wyjściowych](./media/data-lake-store-stream-analytics/run.query.4.png "Weryfikacja danych wyjściowych")

    W okienku Eksplorator danych Zwróć uwagę, że dane wyjściowe są zapisywane w ścieżce do folderu, jak określono w Data Lake Storage Gen1 ustawienia danych wyjściowych ( `streamanalytics/job/output/{date}/{time}` ).  

## <a name="see-also"></a>Zobacz też
* [Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

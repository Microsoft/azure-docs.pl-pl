---
title: Azure Stream Analytics integrację z usługą Azure Machine Learning
description: W tym artykule opisano sposób szybkiego skonfigurowania prostego zadania Azure Stream Analytics, które integruje Azure Machine Learning przy użyciu funkcji zdefiniowanej przez użytkownika.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067090"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Wykonywanie analizy tonacji z użyciem Azure Stream Analytics i Azure Machine Learning Studio (klasyczny)

W tym artykule opisano sposób szybkiego skonfigurowania prostego zadania Azure Stream Analytics, które integruje Azure Machine Learning Studio (klasyczne). Model tonacji Analytics jest Machine Learning używany z Cortana Intelligence Gallery do analizowania danych tekstowych przesyłanych strumieniowo i określania wyniku tonacji w czasie rzeczywistym. Korzystanie z Cortana Intelligence Suite pozwala wykonać to zadanie bez obaw o złożonego tworzenia modelu tonacji Analytics.

> [!TIP]
> Zdecydowanie zaleca się używanie [Azure Machine Learning UDF](machine-learning-udf.md) zamiast Azure Machine Learning Studio (klasycznego) UDF w celu zwiększenia wydajności i niezawodności.

Możesz zastosować informacje z tego artykułu do następujących scenariuszy:

* Analizowanie tonacji w czasie rzeczywistym w przypadku przesyłania strumieniowego danych w usłudze Twitter.
* Analizowanie rekordów rozmów z pracownikami pomocy technicznej.
* Ocena komentarzy na forach, blogach i filmach wideo. 
* Wiele innych scenariuszy analizy predykcyjnej w czasie rzeczywistym.

W rzeczywistym scenariuszu można pobrać dane bezpośrednio ze strumienia danych w usłudze Twitter. Aby uprościć samouczek, jest on zapisywany, aby zadanie usługi Stream Analytics otrzymywało tweety z pliku CSV w usłudze Azure Blob Storage. Można utworzyć własny plik CSV lub użyć przykładowego pliku CSV, jak pokazano na poniższej ilustracji:

![Przykładowe tweety wyświetlane w pliku CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Utworzone zadanie analizy przesyłania strumieniowego stosuje model analizy tonacji jako funkcję zdefiniowaną przez użytkownika (UDF) na przykładowych danych tekstowych z magazynu obiektów BLOB. Dane wyjściowe (wynik analizy tonacji) są zapisywane w tym samym magazynie obiektów BLOB w innym pliku CSV. 

Na poniższej ilustracji przedstawiono tę konfigurację. Zgodnie z powyższym scenariuszem można zastąpić usługę BLOB Storage z danymi przesyłanymi strumieniowo z usługi Azure Event Hubs. Ponadto możesz utworzyć wizualizację tonacji w czasie rzeczywistym [Power BI firmy Microsoft](https://powerbi.microsoft.com/) .    

![Omówienie integracji Machine Learning Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Aktywna subskrypcja platformy Azure.
* Plik CSV zawierający dane. Możesz pobrać plik przedstawiony wcześniej z usługi [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)lub utworzyć własny plik. W tym artykule przyjęto założenie, że używasz pliku z usługi GitHub.

Na wysokim poziomie, aby wykonać zadania przedstawione w tym artykule, wykonaj następujące czynności:

1. Utwórz konto usługi Azure Storage i kontener magazynu obiektów blob, a następnie Przekaż plik wejściowy w formacie CSV do kontenera.
3. Dodaj model analizy tonacji z Cortana Intelligence Gallery do obszaru roboczego Azure Machine Learning Studio (klasyczny) i Wdróż ten model jako usługę sieci Web w obszarze roboczym Machine Learning.
5. Utwórz zadanie Stream Analytics, które wywołuje tę usługę sieci Web jako funkcję w celu określenia tonacji dla danych wejściowych tekstu.
6. Uruchom zadanie Stream Analytics i sprawdź dane wyjściowe.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Tworzenie kontenera magazynu i przekazywanie pliku wejściowego CSV
W tym kroku można użyć dowolnego pliku CSV, takiego jak ten, który jest dostępny w witrynie GitHub.

1. W Azure Portal kliknij pozycję **Utwórz zasób** > **Storage** > magazyn**konto magazynu**.

2. Podaj nazwę (`samldemo` w tym przykładzie). Nazwa może zawierać tylko małe litery i cyfry i musi być unikatowa na platformie Azure. 

3. Określ istniejącą grupę zasobów i określ lokalizację. W przypadku lokalizacji zalecamy, aby wszystkie zasoby utworzone w tym samouczku używały tej samej lokalizacji.

    ![Podaj szczegóły konta magazynu](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. W Azure Portal wybierz konto magazynu. W bloku konto magazynu kliknij pozycję **kontenery** , a następnie kliknij pozycję ** + &nbsp;kontener** , aby utworzyć magazyn obiektów BLOB.

    ![Utwórz kontener usługi BLOB Storage na potrzeby danych wejściowych](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Podaj nazwę kontenera (`azuresamldemoblob` w tym przykładzie) i sprawdź, czy **Typ dostępu** jest ustawiony na wartość **BLOB**. Gdy skończysz, kliknij przycisk **OK**.

    ![Określ szczegóły kontenera obiektów BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. W bloku **kontenery** wybierz nowy kontener, który otwiera blok dla tego kontenera.

7. Kliknij pozycję **Przekaż**.

    ![Przycisk "Przekaż" dla kontenera](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. W bloku **przekazywanie obiektu BLOB** Przekaż pobrany wcześniej plik **sampleinput. csv** . W **polu Typ obiektu BLOB**wybierz pozycję **Blokuj obiekt BLOB** i ustaw rozmiar bloku na 4 MB, co jest wystarczające dla tego samouczka.

9. Kliknij przycisk **Przekaż** w dolnej części bloku.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Dodaj model analizy tonacji z Cortana Intelligence Gallery

Teraz, gdy przykładowe dane są w obiekcie blob, można włączyć model analizy tonacji w Cortana Intelligence Gallery.

1. Przejdź do strony [model analizy predykcyjnej tonacji](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) w Cortana Intelligence Gallery.  

2. Kliknij pozycję **Otwórz w programie Studio**.  
   
   ![Stream Analytics Machine Learning, Otwórz Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Zaloguj się, aby przejść do obszaru roboczego. Wybierz lokalizację.

4. Kliknij pozycję **Uruchom** w dolnej części strony. Przebieg procesu, który trwa około minuty.

   ![Uruchom eksperyment w Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po pomyślnym wykonaniu procesu wybierz pozycję **Wdróż usługę sieci Web** w dolnej części strony.

   ![Wdrażanie eksperymentu w Machine Learning Studio jako usługi sieci Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Aby sprawdzić, czy model tonacji Analytics jest gotowy do użycia, kliknij przycisk **Testuj** . Podaj dane wejściowe tekstu, takie jak "kocham firmę Microsoft". 

   ![eksperyment testowy w Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Jeśli test działa, zobaczysz wynik podobny do następującego przykładu:

   ![wyniki testu w Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. W kolumnie **aplikacje** kliknij link **skoroszytu programu Excel 2010 lub starszego** , aby pobrać skoroszyt programu Excel. Skoroszyt zawiera klucz interfejsu API i adres URL, które będą potrzebne później do skonfigurowania zadania Stream Analytics.

    ![Stream Analytics Machine Learning, krótki przegląd](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Tworzenie zadania Stream Analytics, które korzysta z modelu Machine Learning

Teraz można utworzyć zadanie Stream Analytics, które odczytuje przykładowe tweety z pliku CSV w usłudze BLOB Storage. 

### <a name="create-the-job"></a>Tworzenie zadania

1. Przejdź do [Azure Portal](https://portal.azure.com).  

2. Kliknij pozycję **Utwórz zasób** > **Internet rzeczy** > **Stream Analytics zadanie**. 

3. Nazwij zadanie `azure-sa-ml-demo`, określ subskrypcję, określ istniejącą grupę zasobów lub Utwórz nową, a następnie wybierz lokalizację dla tego zadania.

   ![Określ ustawienia dla nowego zadania Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Konfigurowanie danych wejściowych zadania
Zadanie pobiera dane wejściowe z pliku CSV, który został wcześniej przekazany do usługi BLOB Storage.

1. Po utworzeniu zadania w obszarze **topologia zadania** w bloku zadania kliknij opcję **dane wejściowe** .    

2. W bloku **dane** wejściowe kliknij pozycję **Dodaj strumień wejściowy** >**BLOB Storage**

3. Wypełnij blok **BLOB Storage** następującymi wartościami:

   
   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wejściowy** | Użyj nazwy `datainput` i wybierz **pozycję Wybierz magazyn obiektów blob z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz konto usługi magazynu utworzone wcześniej.  |
   |**Kontener**  | Wybierz utworzony wcześniej kontener (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz plik **CSV**       |

   ![Ustawienia dla nowych danych wejściowych zadania Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Kliknij przycisk **Zapisz**.

### <a name="configure-the-job-output"></a>Konfigurowanie danych wyjściowych zadania
Zadanie wysyła wyniki do tego samego magazynu obiektów blob, w którym pobiera dane wejściowe. 

1. W obszarze **topologia zadania** w bloku zadanie kliknij opcję dane **wyjściowe** .  

2. W bloku dane **wyjściowe** kliknij pozycję **Dodaj** >**Magazyn obiektów BLOB**, a następnie Dodaj dane wyjściowe z aliasem `datamloutput`. 

3. Wypełnij blok **BLOB Storage** następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   |**Alias wyjściowy** | Użyj nazwy `datamloutput` i wybierz **pozycję Wybierz magazyn obiektów blob z subskrypcji**       |
   |**Konto magazynu**  |  Wybierz konto usługi magazynu utworzone wcześniej.  |
   |**Kontener**  | Wybierz utworzony wcześniej kontener (`azuresamldemoblob`)        |
   |**Format serializacji zdarzeń**  |  Wybierz plik **CSV**       |

   ![Ustawienia dla nowych danych wyjściowych zadania Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Kliknij przycisk **Zapisz**.   


### <a name="add-the-machine-learning-function"></a>Dodaj funkcję Machine Learning 
Wcześniej opublikowano model Machine Learning w usłudze sieci Web. W tym scenariuszu, gdy zadanie analizy strumienia zostanie uruchomione, wysyła każdy przykładowy Tweet z danych wejściowych do usługi sieci Web na potrzeby analizy tonacji. Usługa sieci Web Machine Learning zwraca tonacji (`positive`, `neutral`, lub `negative`) i prawdopodobieństwo, że Tweet jest dodatni. 

W tej części samouczka zdefiniujesz funkcję w zadaniu analizy strumienia. Funkcja może zostać wywołana w celu wysłania tweetu do usługi sieci Web i odwracania odpowiedzi. 

1. Upewnij się, że masz adres URL usługi sieci Web i klucz interfejsu API pobrane wcześniej w skoroszycie programu Excel.

2. Przejdź do bloku zadania > **funkcje** > **+ Dodaj** > usługę**Azure**

3. Wypełnij blok **Azure Machine Learning funkcji** następującymi wartościami:

   |Pole  |Wartość  |
   |---------|---------|
   | **Alias funkcji** | Użyj nazwy `sentiment` i wybierz opcję **Podaj Azure Machine Learning ustawienia funkcji ręcznie** , co daje możliwość wprowadzenia adresu URL i klucza.      |
   | **Adres URL**| Wklej adres URL usługi sieci Web.|
   |**Key** | Wklej klucz interfejsu API. |
  
   ![Ustawienia umożliwiające dodanie funkcji Machine Learning do zadania Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Kliknij przycisk **Zapisz**.

### <a name="create-a-query-to-transform-the-data"></a>Tworzenie zapytania do przekształcania danych

Stream Analytics używa deklaratywnego zapytania opartego na języku SQL, aby zbadać dane wejściowe i przetworzyć je. W tej sekcji utworzysz zapytanie, które odczytuje każdy Tweet z danych wejściowych, a następnie wywoła funkcję Machine Learning, aby przeprowadzić analizę tonacji. Zapytanie wysyła następnie wynik do zdefiniowanego w danych wyjściowych (magazynu obiektów BLOB).

1. Wróć do bloku przegląd zadania.

2.  W obszarze **topologia zadania**kliknij pole **zapytania** .

3. Wprowadź następujące zapytanie:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Zapytanie wywołuje funkcję utworzoną wcześniej (`sentiment`) w celu przeprowadzenia analizy tonacji dla każdego tweetu w danych wejściowych. 

4. Kliknij przycisk **Zapisz**, aby zapisać zapytanie.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Teraz możesz uruchomić zadanie Stream Analytics.

### <a name="start-the-job"></a>Uruchamianie zadania
1. Wróć do bloku przegląd zadania.

2. Kliknij przycisk **Rozpocznij** w górnej części bloku.

3. W **zadania uruchamiania**wybierz opcję **niestandardowy**, a następnie wybierz jeden dzień przed przekazaniem pliku CSV do magazynu obiektów BLOB. Gdy skończysz, kliknij przycisk **Uruchom**.  


### <a name="check-the-output"></a>Sprawdzanie danych wyjściowych
1. Pozwól, aby zadanie było uruchamiane przez kilka minut, dopóki nie zobaczysz działania w polu **monitorowanie** . 

2. Jeśli masz narzędzie, którego zwykle używasz do badania zawartości magazynu obiektów blob, użyj tego narzędzia do sprawdzenia `azuresamldemoblob` kontenera. Alternatywnie wykonaj następujące czynności w Azure Portal:

    1. Znajdź w portalu konto `samldemo` magazynu i w ramach konta Znajdź `azuresamldemoblob` kontener. W kontenerze widoczne są dwa pliki: plik zawierający przykładowe tweety i plik CSV wygenerowany przez zadanie Stream Analytics.
    2. Kliknij prawym przyciskiem myszy wygenerowany plik, a następnie wybierz polecenie **Pobierz**. 

   ![Pobieranie danych wyjściowych zadania CSV z magazynu obiektów BLOB](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Otwórz wygenerowany plik CSV. Zobaczysz coś podobne do poniższego przykładu:  
   
   ![Stream Analytics Machine Learning, widok CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Wyświetlanie metryk
Możesz również wyświetlić Azure Machine Learning metryki dotyczące funkcji. Następujące metryki związane z funkcją są wyświetlane w polu **monitorowanie** w bloku zadania:

* **Żądania funkcji** wskazuje liczbę żądań wysłanych do usługi sieci Web Machine Learning.  
* **Zdarzenia funkcji** wskazują liczbę zdarzeń w żądaniu. Domyślnie każde żądanie do usługi sieci Web Machine Learning zawiera maksymalnie 1 000 zdarzeń.  


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrowanie interfejsu API REST i Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)




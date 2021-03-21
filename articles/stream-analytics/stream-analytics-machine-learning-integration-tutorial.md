---
title: Integracja Azure Stream Analytics z Azure Machine Learning Studio (wersja klasyczna)
description: W tym artykule opisano sposób szybkiego skonfigurowania prostego zadania Azure Stream Analytics, które integruje Azure Machine Learning Studio (klasyczne) przy użyciu funkcji zdefiniowanej przez użytkownika.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1ebe62c1b90e09b36dd75b5bda4054cca08d5759
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441214"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Analiza tonacji z użyciem Azure Stream Analytics i Azure Machine Learning Studio (wersja klasyczna)

W tym artykule opisano sposób konfigurowania prostego zadania Azure Stream Analytics, które używa Azure Machine Learning Studio (klasyczne) do analizy tonacji. Używasz modelu analizy tonacji Analytics (klasycznego) z Cortana Intelligence Gallery, aby analizować dane przesyłane strumieniowo i określać wynik tonacji.

> [!TIP]
> Zdecydowanie zaleca się używanie [Azure Machine Learning UDF](machine-learning-udf.md) zamiast Azure Machine Learning Studio (klasycznego) UDF w celu zwiększenia wydajności i niezawodności.

Możesz zastosować informacje z tego artykułu do następujących scenariuszy:

* Analizowanie tonacji w czasie rzeczywistym w przypadku przesyłania strumieniowego danych w usłudze Twitter.
* Analizowanie rekordów rozmów z pracownikami pomocy technicznej.
* Ocena komentarzy na forach, blogach i filmach wideo.
* Wiele innych scenariuszy analizy predykcyjnej w czasie rzeczywistym.

Utworzone zadanie analizy przesyłania strumieniowego stosuje model analizy tonacji jako funkcję zdefiniowaną przez użytkownika (UDF) na przykładowych danych tekstowych z magazynu obiektów BLOB. Dane wyjściowe (wynik analizy tonacji) są zapisywane w tym samym magazynie obiektów BLOB w innym pliku CSV. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Aktywna subskrypcja platformy Azure.

* Plik CSV z danymi w serwisie Twitter. Przykładowy plik można pobrać z witryny [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)lub można utworzyć własny plik. W rzeczywistym scenariuszu można pobrać dane bezpośrednio ze strumienia danych w usłudze Twitter.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Tworzenie kontenera magazynu i przekazywanie pliku wejściowego CSV

W tym kroku przekażesz plik CSV do kontenera magazynu.

1. W Azure Portal wybierz pozycję **Utwórz zasób**  >  **Magazyn**  >  **konto magazynu**.

2. Wypełnij kartę *podstawowe* z poniższymi szczegółami i pozostaw wartości domyślne dla pozostałych pól:

   |Pole  |Wartość  |
   |---------|---------|
   |Subskrypcja|Wybierz subskrypcję.|
   |Grupa zasobów|Wybierz grupę zasobów.|
   |Nazwa konta magazynu|Wprowadź nazwę konta magazynu. Nazwa musi być unikatowa na platformie Azure.|
   |Lokalizacja|Wybierz lokalizację. Wszystkie zasoby powinny używać tej samej lokalizacji.|
   |Rodzaj konta|BlobStorage|

   ![Podaj szczegóły konta magazynu](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Wybierz pozycję **Przejrzyj i utwórz**. Następnie wybierz pozycję **Utwórz** , aby wdrożyć konto magazynu.

4. Po zakończeniu wdrażania przejdź do konta magazynu. W obszarze **Blob service** wybierz pozycję **Kontenery**. Następnie wybierz pozycję **+ kontener** , aby utworzyć nowy kontener.

   ![Utwórz kontener usługi BLOB Storage na potrzeby danych wejściowych](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Podaj nazwę kontenera i sprawdź, czy **poziom dostępu publicznego** jest ustawiony jako **prywatny**. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   ![Określ szczegóły kontenera obiektów BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Przejdź do nowo utworzonego kontenera i wybierz pozycję **Przekaż**. Przekaż pobrany wcześniej plik **sampleinput.csv** .

   ![Przycisk "Przekaż" dla kontenera](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Dodaj model analizy tonacji z Cortana Intelligence Gallery

Teraz, gdy przykładowe dane są w obiekcie blob, można włączyć model analizy tonacji w Cortana Intelligence Gallery.

1. Przejdź do strony [model analizy predykcyjnej tonacji](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) w Cortana Intelligence Gallery.  

2. Wybierz pozycję **Otwórz w programie Studio (klasyczne)**.  
   
   ![Stream Analytics Azure Machine Learning Studio (klasyczny), Open Studio (klasyczny)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Zaloguj się, aby przejść do obszaru roboczego. Wybierz lokalizację.

4. Wybierz pozycję **Uruchom** w dolnej części strony. Przebieg procesu, który trwa około minuty.

   ![Uruchom eksperyment w programie Studio (klasyczny)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Po pomyślnym wykonaniu procesu wybierz pozycję **Wdróż usługę sieci Web** w dolnej części strony.

   ![Wdróż eksperyment w programie Studio (klasyczny) jako usługę sieci Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Aby sprawdzić, czy model tonacji Analytics jest gotowy do użycia, wybierz przycisk **Testuj** . Podaj dane wejściowe tekstu, takie jak "kocham firmę Microsoft".

   ![Eksperyment testowy w programie Studio (klasyczny)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Jeśli test działa, zobaczysz wynik podobny do następującego przykładu:

   ![Wyniki testów w programie Studio (klasyczne)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. W kolumnie **aplikacje** wybierz łącze **skoroszytu programu Excel 2010 lub starszego** , aby pobrać skoroszyt programu Excel. Skoroszyt zawiera klucz interfejsu API i adres URL, które będą potrzebne później do skonfigurowania zadania Stream Analytics.

    ![Stream Analytics Azure Machine Learning Studio (klasyczny), krótki przegląd](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Tworzenie zadania Stream Analytics, które korzysta z modelu Studio (klasycznego)

Teraz można utworzyć zadanie Stream Analytics, które odczytuje przykładowe tweety z pliku CSV w usłudze BLOB Storage.

### <a name="create-the-job"></a>Tworzenie zadania

Przejdź do [Azure Portal](https://portal.azure.com) i utwórz zadanie Stream Analytics. Jeśli nie znasz tego procesu, zobacz [Tworzenie zadania Stream Analytics przy użyciu Azure Portal](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Konfigurowanie danych wejściowych zadania

Zadanie pobiera dane wejściowe z pliku CSV, który został wcześniej przekazany do usługi BLOB Storage.

1. Przejdź do zadania usługi Stream Analytics. W obszarze **topologia zadania** wybierz opcję **dane wejściowe** . Wybierz pozycję **Dodaj strumień wejściowy**  > **BLOB Storage**.

2. Wypełnij **BLOB Storage** szczegóły, korzystając z następujących wartości:

   |Pole  |Wartość  |
   |---------|---------|
   |Alias danych wejściowych|Nadaj nazwę wejściowemu. Należy pamiętać o tym aliasie podczas pisania zapytania.|
   |Subskrypcja|Wybierz subskrypcję.|
   |Konto magazynu|Wybierz konto magazynu utworzone w poprzednim kroku.|
   |Kontener|Wybierz kontener utworzony w poprzednim kroku.|
   |Format serializacji zdarzeń|CSV|

3. Wybierz pozycję **Zapisz**.

### <a name="configure-the-job-output"></a>Konfigurowanie danych wyjściowych zadania

Zadanie wysyła wyniki do tego samego magazynu obiektów blob, w którym pobiera dane wejściowe.

1. Przejdź do zadania usługi Stream Analytics. W obszarze **topologia zadania** wybierz opcję dane **wyjściowe** . Wybierz pozycję **Dodaj**  >  **Magazyn obiektów BLOB**.

2. Wypełnij formularz **BLOB Storage** przy użyciu następujących wartości:

   |Pole  |Wartość  |
   |---------|---------|
   |Alias danych wejściowych|Nadaj nazwę wejściowemu. Należy pamiętać o tym aliasie podczas pisania zapytania.|
   |Subskrypcja|Wybierz subskrypcję.|
   |Konto magazynu|Wybierz konto magazynu utworzone w poprzednim kroku.|
   |Kontener|Wybierz kontener utworzony w poprzednim kroku.|
   |Format serializacji zdarzeń|CSV|

3. Wybierz pozycję **Zapisz**.

### <a name="add-the-studio-classic-function"></a>Dodawanie funkcji Studio (klasycznej)

Wcześniej opublikowano model Studio (klasyczny) w usłudze sieci Web. W tym scenariuszu, gdy zadanie analizy strumienia zostanie uruchomione, wysyła każdy przykładowy Tweet z danych wejściowych do usługi sieci Web na potrzeby analizy tonacji. Usługa sieci Web Studio (klasyczna) zwraca tonacji ( `positive` , `neutral` , lub `negative` ) i prawdopodobieństwo, że Tweet jest dodatni.

W tej sekcji zdefiniujesz funkcję w zadaniu analizy strumienia. Funkcja może zostać wywołana w celu wysłania tweetu do usługi sieci Web i odwracania odpowiedzi.

1. Upewnij się, że masz adres URL usługi sieci Web i klucz interfejsu API pobrane wcześniej w skoroszycie programu Excel.

2. Przejdź do zadania usługi Stream Analytics. Następnie wybierz pozycję **funkcje**  >  **i Dodaj**  >  **ml Studio platformy Azure**

3. Wypełnij formularz **funkcji Azure Machine Learning** przy użyciu następujących wartości:

   |Pole  |Wartość  |
   |---------|---------|
   | Alias funkcji | Użyj nazwy `sentiment` i wybierz opcję **Podaj Azure Machine Learning ustawienia funkcji ręcznie**, co daje możliwość wprowadzenia adresu URL i klucza.      |
   | Adres URL| Wklej adres URL usługi sieci Web.|
   |Klucz | Wklej klucz interfejsu API. |

4. Wybierz pozycję **Zapisz**.

### <a name="create-a-query-to-transform-the-data"></a>Tworzenie zapytania do przekształcania danych

Stream Analytics używa deklaratywnego zapytania opartego na języku SQL, aby zbadać dane wejściowe i przetworzyć je. W tej sekcji utworzysz zapytanie, które odczytuje każdy Tweet z danych wejściowych, a następnie wywołuje funkcję Studio (klasyczną) w celu przeprowadzenia analizy tonacji. Zapytanie wysyła następnie wynik do zdefiniowanego w danych wyjściowych (magazynu obiektów BLOB).

1. Wróć do omówienia zadania Stream Analytics.

2. W obszarze **topologia zadania** wybierz pozycję **zapytanie**.

3. Wprowadź następujące zapytanie:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   Zapytanie wywołuje funkcję utworzoną wcześniej ( `sentiment` ) w celu przeprowadzenia analizy tonacji dla każdego tweetu w danych wejściowych.

4. Wybierz pozycję **Zapisz** , aby zapisać zapytanie.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

Teraz możesz uruchomić zadanie Stream Analytics.

### <a name="start-the-job"></a>Uruchamianie zadania

1. Wróć do omówienia zadania Stream Analytics.

2. Wybierz pozycję **Rozpocznij** w górnej części strony.

3. W obszarze **zadania uruchamiania** wybierz opcję **niestandardowy**, a następnie wybierz jeden dzień przed przekazaniem pliku CSV do magazynu obiektów BLOB. Gdy wszystko będzie gotowe, wybierz pozycję **Uruchom**.  

### <a name="check-the-output"></a>Sprawdzanie danych wyjściowych

1. Pozwól, aby zadanie było uruchamiane przez kilka minut, dopóki nie zobaczysz działania w polu **monitorowanie** .

2. Jeśli masz narzędzie, którego zwykle używasz do badania zawartości magazynu obiektów blob, użyj tego narzędzia do sprawdzenia kontenera. Alternatywnie wykonaj następujące czynności w Azure Portal:

      1. W Azure Portal Znajdź konto magazynu i w ramach konta Znajdź kontener. W kontenerze widoczne są dwa pliki: plik zawierający przykładowe tweety i plik CSV wygenerowany przez zadanie Stream Analytics.
      2. Kliknij prawym przyciskiem myszy wygenerowany plik, a następnie wybierz polecenie **Pobierz**.

3. Otwórz wygenerowany plik CSV. Zobaczysz coś podobne do poniższego przykładu:  

   ![Stream Analytics Azure Machine Learning Studio (klasyczny), widok CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Wyświetlanie metryk

Można również wyświetlić metryki dotyczące funkcji programu Studio (klasycznej). Następujące metryki związane z funkcją są wyświetlane w polu **monitorowanie** w obszarze przegląd zadania:

* **Żądania funkcji** wskazuje liczbę żądań wysyłanych do usługi sieci Web programu Studio (klasycznej).  
* **Zdarzenia funkcji** wskazują liczbę zdarzeń w żądaniu. Domyślnie każde żądanie do usługi sieci Web programu Studio (klasycznej) zawiera maksymalnie 1 000 zdarzeń.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrowanie interfejsu API REST i Machine Learning Studio (klasyczny)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
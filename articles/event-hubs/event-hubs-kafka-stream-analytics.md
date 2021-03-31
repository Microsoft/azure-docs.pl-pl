---
title: Azure Event Hubs — przetwarzanie zdarzeń Apache Kafka
description: 'Samouczek: w tym artykule przedstawiono sposób przetwarzania zdarzeń Kafka, które są pozyskiwane za pośrednictwem centrów zdarzeń przy użyciu Azure Stream Analytics'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 328537eebd05391fc4c8138395a9c10f0a5d072f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319389"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Samouczek: proces Apache Kafka dla zdarzeń Event Hubs za pomocą usługi Stream Analytics 
W tym artykule pokazano, jak przesłać strumieniowo dane do Event Hubs i przetworzyć je z Azure Stream Analytics. Przeprowadzi Cię przez następujące kroki: 

1. Utwórz przestrzeń nazw usługi Event Hubs.
2. Utwórz klienta Kafka, który wysyła komunikaty do centrum zdarzeń.
3. Utwórz zadanie Stream Analytics, które kopiuje dane z centrum zdarzeń do magazynu obiektów blob platformy Azure. 

Nie trzeba zmieniać klientów protokołu ani uruchamiać własnych klastrów, gdy używasz punktu końcowego Kafka uwidocznionego przez centrum zdarzeń. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html) i powyżej. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Usługa Git](https://www.git-scm.com/)
* **Konto usługi Azure Storage**. Jeśli go nie masz, [Utwórz go](../storage/common/storage-account-create.md) przed kontynuowaniem. Zadanie Stream Analytics w tym instruktażu przechowuje dane wyjściowe w usłudze Azure Blob Storage. 


## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Podczas tworzenia warstwy **standardowa** Event Hubs przestrzeń nazw, punkt końcowy Kafka dla przestrzeni nazw jest automatycznie włączany. Zdarzenia z aplikacji korzystających z protokołu Kafka można przesyłać strumieniowo do warstwy Standardowa Event Hubs. Postępuj zgodnie z instrukcjami krok po kroku w temacie [Tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md) , aby utworzyć Event Hubs przestrzeni nazw w warstwie **standardowa** . 

> [!NOTE]
> Event Hubs dla Kafka jest dostępny tylko w warstwach **standardowa** i **dedykowana** . Warstwa **podstawowa** nie obsługuje Kafka na Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Wysyłanie komunikatów za pomocą Kafka w Event Hubs

1. Sklonuj [Event Hubs Azure dla repozytorium Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) na komputerze.
2. Przejdź do folderu: `azure-event-hubs-for-kafka/quickstart/java/producer` . 
4. Zaktualizuj szczegóły konfiguracji producenta w temacie `src/main/resources/producer.config` . Określ **nazwę** i **Parametry połączenia** dla **przestrzeni nazw centrum zdarzeń**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Przejdź do `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` pliku **TestDataReporter. Java** i otwórz go w wybranym edytorze. 
6. Skomentuj następujący wiersz kodu:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Dodaj następujący wiersz kodu zamiast kodu komentarza: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ten kod wysyła dane zdarzenia w formacie **JSON** . Podczas konfigurowania danych wejściowych dla zadania Stream Analytics należy określić kod JSON jako format danych wejściowych. 
7. **Uruchom producenta** i Prześlij strumieniowo do Event Hubs. Na komputerze z systemem Windows w przypadku korzystania z **wiersza poleceniaNode.js** przejdź do `azure-event-hubs-for-kafka/quickstart/java/producer` folderu przed uruchomieniem tych poleceń. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Sprawdź, czy centrum zdarzeń odbiera dane

1. Wybierz **Event Hubs** w obszarze **jednostki**. Upewnij się, że widzisz centrum zdarzeń o nazwie **test**. 

    ![Centrum zdarzeń — test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Potwierdź, że zobaczysz komunikaty przychodzące do centrum zdarzeń. 

    ![Centrum zdarzeń — komunikaty](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Przetwarzanie danych zdarzeń przy użyciu zadania Stream Analytics
W tej sekcji utworzysz zadanie Azure Stream Analytics. Klient Kafka wysyła zdarzenia do centrum zdarzeń. Tworzysz zadanie Stream Analytics, które pobiera dane zdarzeń jako dane wejściowe i wyprowadza je do magazynu obiektów blob platformy Azure. Jeśli nie masz **konta usługi Azure Storage**, [Utwórz je](../storage/common/storage-account-create.md).

Zapytanie w Stream Analytics zadania przejdzie przez dane bez wykonywania żadnej analizy. Można utworzyć zapytanie, które przekształca dane wejściowe w celu wygenerowania danych wyjściowych w innym formacie lub z uzyskaniem szczegółowych informacji.  

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

1. Wybierz pozycję **+ Utwórz zasób** w [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Analiza** w menu **portalu Azure Marketplace** , a następnie wybierz pozycję **Stream Analytics zadanie**. 
3. Na stronie **nowy Stream Analytics** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** zadania. 
    2. Wybierz **subskrypcję**.
    3. Wybierz pozycję **Utwórz nowy** dla **grupy zasobów** , a następnie wprowadź nazwę. Można również **użyć istniejącej** grupy zasobów. 
    4. Wybierz **lokalizację** dla zadania.
    5. Wybierz pozycję **Utwórz** , aby utworzyć zadanie. 

        ![Nowe zadanie Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. W komunikacie powiadomienia wybierz pozycję **Przejdź do zasobu** , aby wyświetlić stronę **zadania Stream Analytics** . 
2. Wybierz pozycję **dane wejściowe** w sekcji **topologia zadania** w menu po lewej stronie.
3. Wybierz pozycję **Dodaj strumień wejściowy**, a następnie wybierz pozycję **centrum zdarzeń**. 

    ![Dodaj centrum zdarzeń jako dane wejściowe](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na stronie Konfiguracja **danych wejściowych centrum zdarzeń** wykonaj następujące czynności: 

    1. Określ **alias** dla danych wejściowych. 
    2. Wybierz swoją **subskrypcję platformy Azure**.
    3. Wybierz utworzoną wcześniej **przestrzeń nazw centrum zdarzeń** . 
    4. Wybierz **test** **centrum zdarzeń**. 
    5. Wybierz pozycję **Zapisz**. 

        ![Konfiguracja danych wejściowych centrum zdarzeń](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

1. Wybierz pozycję dane **wyjściowe** w sekcji **topologia zadania** w menu. 
2. Wybierz pozycję **+ Dodaj** na pasku narzędzi i wybierz pozycję **BLOB Storage**
3. Na stronie Ustawienia wyjściowe magazynu obiektów BLOB wykonaj następujące czynności: 
    1. Określ **alias** dla danych wyjściowych. 
    2. Wybierz swoją **subskrypcję** platformy Azure. 
    3. Wybierz **konto usługi Azure Storage**. 
    4. Wprowadź **nazwę kontenera** , w którym są przechowywane dane wyjściowe z kwerendy Stream Analytics.
    5. Wybierz pozycję **Zapisz**.

        ![Blob Storage konfiguracja wyjściowa](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definiowanie zapytania
Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). W tym instruktażu zdefiniujesz zapytanie, które przekazuje dane bez wykonywania transformacji.

1. Wybierz **zapytanie**.
2. W oknie zapytania Zastąp `[YourOutputAlias]` wartość utworzonym wcześniej aliasem wyjściowym.
3. Zamień na `[YourInputAlias]` utworzony wcześniej alias wejściowy. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Przechwytywanie ekranu pokazuje okno zapytania z wartościami dla zmiennych wejściowych i wyjściowych.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Z menu po lewej stronie wybierz pozycję **Przegląd** . 
2. Wybierz pozycję **Uruchom**. 

    ![Menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na stronie **Uruchamianie zadania** wybierz pozycję **Rozpocznij**. 

    ![Strona uruchamiania zadania](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Poczekaj, aż stan zadania zmieni się z **Rozpoczynanie** na **uruchomione**. 

    ![Stan zadania — uruchomiono](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testowanie scenariusza
1. Uruchom ponownie **producenta Kafka** , aby wysłać zdarzenia do centrum zdarzeń. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Upewnij się, że w **usłudze Azure Blob Storage** są generowane **dane wyjściowe** . W kontenerze zostanie wyświetlony plik JSON z 100 wierszami, które wyglądają jak w następujących przykładowych wierszach: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Zadanie Azure Stream Analytics otrzymało dane wejściowe z centrum zdarzeń i zapisano je w magazynie obiektów blob platformy Azure w tym scenariuszu. 



## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego do Event Hubs bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej na temat Event Hubs Apache Kafka, zobacz [Przewodnik dla deweloperów Apache Kafka dla Event Hubs platformy Azure](apache-kafka-developer-guide.md).
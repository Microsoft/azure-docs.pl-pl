---
title: 'Samouczek: wykrywanie anomalii na danych przesyłanych strumieniowo przy użyciu Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API wykrywania anomalii i Azure Databricks, aby monitorować anomalie w danych.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0982f89d59f2ef9a282a46a93b98801b9df00a40
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368716"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Samouczek: wykrywanie anomalii na danych przesyłanych strumieniowo przy użyciu Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) to szybka, łatwa w obsłudze usługa analizy oparta na Apache Sparkach. Interfejs API wykrywania anomalii, część Cognitive Services platformy Azure, umożliwia monitorowanie danych szeregów czasowych. Ten samouczek służy do uruchamiania wykrywania anomalii na strumieniu danych niemal w czasie rzeczywistym przy użyciu Azure Databricks. Dane usługi Twitter zostaną odebrane przy użyciu Event Hubs platformy Azure i zaimportowane do Azure Databricks za pomocą łącznika Spark Event Hubs. Następnie użyjesz interfejsu API w celu wykrycia anomalii dla danych przesyłanych strumieniowo.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Azure Databricks z Event Hubs i Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks z Event Hubs i Cognitive Services")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter w celu uzyskania dostępu do danych strumienia
> * Tworzenie notesów w usłudze Azure Databricks
> * Dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Utwórz zasób wykrywania anomalii i Pobierz klucz dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Uruchamianie wykrywania anomalii na Tweetach

> [!Note]
> * W tym samouczku przedstawiono podejście do wdrożenia zalecanej [architektury rozwiązania](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) dla interfejsu API wykrywania anomalii.
> * Nie można ukończyć tego samouczka z subskrypcją warstwy Bezpłatna ( `F0` ) dla interfejsu API wykrywania anomalii lub Azure Databricks.

Utwórz [subskrypcję platformy Azure](https://azure.microsoft.com/free/cognitive-services) , jeśli jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

- Przestrzeń nazw i centrum zdarzeń [usługi Azure Event Hubs](../../../event-hubs/event-hubs-create.md) .

- [Parametry połączenia](../../../event-hubs/event-hubs-get-connection-string.md) w celu uzyskania dostępu do przestrzeni nazw Event Hubs. Parametry połączenia powinny mieć podobny format, aby:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.

- Nazwa i klucz zasad dostępu współdzielonego dla Event Hubs.

Aby uzyskać informacje na temat tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Przewodnik Szybki Start](../../../event-hubs/event-hubs-create.md) dotyczący usługi Azure Event Hubs.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy Azure Databricks przy użyciu [Azure Portal](https://portal.azure.com/).

1. W Azure Portal wybierz pozycję **Utwórz**  >  **Analytics**  >  **Azure Databricks** analizy zasobów.

    ![Azure Databricks w portalu](../media/tutorials/azure-databricks-on-portal.png "Datakostki na Azure Portal")

3. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć obszar roboczy usługi Databricks:


    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Wschodnie stany USA 2** lub jeden z innych dostępnych regionów. Dostępność regionów można znaleźć w temacie [usługi platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/) .        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. NIE wybieraj **wersji próbnej**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Wybierz pozycję **Utwórz**.

4. Tworzenie obszaru roboczego trwa kilka minut.

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **nowy klaster**.

    ![Datakostki na platformie Azure](../media/tutorials/databricks-on-azure.png "Datakostki na platformie Azure")

3. Na stronie **nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](../media/tutorials/create-databricks-spark-cluster.png "Tworzenie klastra usługi datakosteks Spark na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

   * Wprowadź nazwę klastra.
   * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **5,2** . NIE wybieraj środowiska uruchomieniowego **5,3** .
   * Upewnij się, że pole wyboru **Zakończ po \_ \_ minutach braku aktywności** jest zaznaczone. Określ czas trwania (w minutach) działania klastra, Jeśli klaster nie jest używany.

     Wybierz pozycję **Utwórz klaster**.
4. Tworzenie klastra trwa kilka minut. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Wykonaj poniższe kroki, aby utworzyć aplikację usługi Twitter i zarejestrować wartości potrzebne do ukończenia tego samouczka.

1. W przeglądarce internetowej przejdź do strony [Twitter Application Management (Zarządzanie aplikacjami usługi Twitter)](https://apps.twitter.com/) i wybierz pozycję **Create New App (Utwórz nową aplikację)**.

    ![Tworzenie aplikacji usługi Twitter](../media/tutorials/databricks-create-twitter-app.png "Tworzenie aplikacji usługi Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

    ![Szczegóły aplikacji usługi Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Szczegóły aplikacji usługi Twitter")

3. Na stronie aplikacji wybierz kartę **Keys and Access Tokens (Klucze i tokeny dostępu)** , a następnie skopiuj wartości pól **Consumer Key (Klucz klienta)** i **Consumer Secret (Wpis tajny klienta)**. Zaznacz również pole **Create my access token (Utwórz mój token dostępu)** , aby wygenerować tokeny dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

    ![Szczegóły aplikacji usługi Twitter 2](../media/tutorials/twitter-app-key-secret.png "Szczegóły aplikacji usługi Twitter")

Zapisz wartości dotyczące aplikacji usługi Twitter. Będą one potrzebne w dalszej części tego samouczka.

## <a name="attach-libraries-to-spark-cluster"></a>Dołączanie bibliotek do klastra Spark

W tym samouczku tweety są wysyłane do usługi Event Hubs za pomocą interfejsów API usługi Twitter. Ponadto dane są odczytywane i zapisywane w usłudze Azure Event Hubs za pomocą [łącznika Event Hubs platformy Apache Spark](https://github.com/Azure/azure-event-hubs-spark). Aby korzystać z tych interfejsów API w ramach klastra, dodaj je jako biblioteki do usługi Azure Databricks, a następnie je skojarz z klastrem Spark. Poniższe instrukcje pokazują, jak dodać biblioteki do folderu **udostępnionego** w obszarze roboczym.

1. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Obszar roboczy** , a następnie kliknij prawym przyciskiem myszy pozycję **Udostępnione**. Z menu kontekstowego wybierz polecenie **Utwórz**  >  **bibliotekę**.

   ![Okno dialogowe Dodawanie biblioteki](../media/tutorials/databricks-add-library-option.png "Okno dialogowe Dodawanie biblioteki")

2. Na stronie Nowa biblioteka dla opcji **Source** SELECT **Maven**. W polu **współrzędne** wprowadź współrzędną dla pakietu, który chcesz dodać. Oto współrzędne Maven bibliotek używanych w tym samouczku:

   * Łącznik Event Hubs platformy Spark — `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Interfejs API usługi Twitter — `org.twitter4j:twitter4j-core:4.0.7`

     ![Podaj współrzędne Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Podaj współrzędne Maven")

3. Wybierz przycisk **Utwórz**.

4. Wybierz folder, do którego dodano bibliotekę, a następnie wybierz nazwę biblioteki.

    ![Wybierz bibliotekę do dodania](../media/tutorials/select-library.png "Wybierz bibliotekę do dodania")

5. Jeśli na stronie Biblioteka nie ma klastra, wybierz pozycję **klastry** i uruchom utworzony klaster. Poczekaj na wyświetlenie stanu "uruchomiona", a następnie wróć do strony biblioteki.
Na stronie Biblioteka wybierz klaster, do którego chcesz użyć biblioteki, a następnie wybierz pozycję **Zainstaluj**. Po pomyślnym skojarzeniu biblioteki z klastrem stan natychmiast zmieni się na **zainstalowane**.

    ![Zainstaluj bibliotekę w klastrze](../media/tutorials/databricks-library-attached.png "Zainstaluj bibliotekę w klastrze")

6. Powtórz te kroki dla pakietu Twitter: `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Pobieranie klucza dostępu usług Cognitive Services

W tym samouczku użyjesz [interfejsów API wykrywania anomalii w usłudze Azure Cognitive Services](../overview.md) do uruchamiania wykrywania anomalii na strumieniu tweetów niemal w czasie rzeczywistym. Przed użyciem interfejsów API należy utworzyć zasób wykrywania anomalii na platformie Azure i pobrać klucz dostępu, aby używać interfejsów API wykrywania anomalii.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

2. Wybierz pozycję **+ Utwórz zasób**.

3. W obszarze Azure Marketplace wybierz pozycję **AI + Machine Learning**  >  **Zobacz wszystko**  >  **Cognitive Services-więcej**  >  **wykrywania anomalii**. Można też użyć [tego linku](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) , aby przejść bezpośrednio do okna dialogowego **Tworzenie** .

    ![Utwórz zasób wykrywania anomalii](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Utwórz zasób wykrywania anomalii")

4. W oknie dialogowym **Tworzenie** podaj następujące wartości:

    |Wartość |Opis  |
    |---------|---------|
    |Nazwa     | Nazwa zasobu wykrywania anomalii.        |
    |Subskrypcja     | Subskrypcja platformy Azure, z którą zostanie skojarzony zasób.        |
    |Lokalizacja     | Lokalizacja platformy Azure.        |
    |Warstwa cenowa     | Warstwa cenowa usługi. Aby uzyskać więcej informacji na temat cennika usługi wykrywania anomalii, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Grupa zasobów     | Określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.        |


     Wybierz przycisk **Utwórz**.

5. Po utworzeniu zasobu na karcie **Przegląd** skopiuj i Zapisz adres URL **punktu końcowego** , jak pokazano na zrzucie ekranu. Następnie wybierz pozycję **Pokaż klucze dostępu**.

    ![Pokaż klucze dostępu](../media/tutorials/cognitive-services-get-access-keys.png "Pokaż klucze dostępu")

6. W obszarze **klucze** wybierz ikonę kopiowania dla klucza, którego chcesz użyć. Zapisz klucz dostępu.

    ![Kopiuj klucze dostępu](../media/tutorials/cognitive-services-copy-access-keys.png "Kopiuj klucze dostępu")

## <a name="create-notebooks-in-databricks"></a>Tworzenie notesów w usłudze Databricks

W tej sekcji w obszarze roboczym usługi Databricks zostaną utworzone dwa notesy o następujących nazwach:

- **SendTweetsToEventHub** — notes producenta służący do pobierania tweetów z usługi Twitter i przesyłania ich w strumieniu do usługi Event Hubs.
- **AnalyzeTweetsFromEventHub** — Notes użytkownika służący do odczytywania tweetów z Event Hubs i uruchamiania wykrywania anomalii.

1. W obszarze roboczym Azure Databricks wybierz pozycję **obszar roboczy** w okienku po lewej stronie. Z listy rozwijanej **Obszar roboczy** wybierz pozycje **Utwórz** i **Notes**.

    ![Tworzenie notesu w kostkach](../media/tutorials/databricks-create-notebook.png "Tworzenie notesu w kostkach")

2. W oknie dialogowym **Tworzenie notesu** wprowadź **SendTweetsToEventHub** jako nazwę, wybierz pozycję **Scala** jako język i wybierz utworzony wcześniej klaster Spark.

    ![Szczegóły notesu](../media/tutorials/databricks-notebook-details.png "Tworzenie notesu w kostkach")

    Wybierz przycisk **Utwórz**.

3. Powtórz te kroki, aby utworzyć notes **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Wysyłanie tweetów do usługi Event Hubs

W notesie **SendTweetsToEventHub** wklej następujący kod i zastąp symbol zastępczy utworzonymi wcześniej wartościami przestrzeni nazw usługi Event Hubs i aplikacji usługi Twitter. Ten Notes wyodrębnia czas tworzenia i liczbę "jak" z tweetów ze słowem kluczowym "Azure" i przesyła strumieniowo te zdarzenia do Event Hubs w czasie rzeczywistym.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }

  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu. Każde zdarzenie w danych wyjściowych jest kombinacją sygnatury czasowej i liczby "podobne" do Event Hubs.

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>Odczytywanie tweetów z usługi Event Hubs

W notesie **AnalyzeTweetsFromEventHub** wklej następujący kod i Zastąp symbol zastępczy wartościami utworzonego wcześniej zasobu wykrywania anomalii. Ten notes umożliwia odczyt tweetów przesłanych strumieniowo do usługi Event Hubs przy użyciu notesu **SendTweetsToEventHub**.

Najpierw Napisz klienta, aby wywołać detektor anomalii.
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu.

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

Następnie przygotuj funkcję agregacji do użycia w przyszłości.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)

  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))

  override def dataType: DataType = BooleanType

  override def deterministic: Boolean = false

  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }

  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }

  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }

  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }


      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)

      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }

    return None
  }
}

```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu.

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

Następnie Załaduj dane z centrum zdarzeń na potrzeby wykrywania anomalii. Zastąp symbol zastępczy wartościami utworzonego wcześniej Event Hubs platformy Azure.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Dane wyjściowe są teraz podobne do poniższej ilustracji. Zwróć uwagę, że data w tabeli może się różnić od daty w tym samouczku, ponieważ dane są w czasie rzeczywistym.
![Ładowanie danych z centrum zdarzeń](../media/tutorials/load-data-from-eventhub.png "Ładowanie danych z centrum zdarzeń")

Dane z usługi Azure Event Hubs zostały przesłane strumieniowo do usługi Azure Databricks niemal w czasie rzeczywistym za pomocą łącznika usługi Event Hubs dla platformy Apache Spark. Aby uzyskać więcej informacji na temat sposobu korzystania z łącznika usługi Event Hubs dla platformy Spark, zobacz [dokumentację łącznika](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Uruchamianie wykrywania anomalii na Tweetach

W tej sekcji zostanie uruchomione wykrywanie anomalii na Tweetach otrzymanych przy użyciu interfejsu API wykrywania anomalii. Fragmenty kodu należy dodać do tego samego notesu **AnalyzeTweetsFromEventHub**.

Aby przeprowadzić wykrywanie anomalii, najpierw należy agregować liczbę metryk według godziny.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window.
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Dane wyjściowe są teraz podobne do następujących fragmentów kodu.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Następnie Pobierz zagregowany wynik danych wyjściowych do różnic. Ponieważ wykrywanie anomalii wymaga dłuższego okna historii, używamy delty, aby zachować dane historii dla punktu, który ma zostać wykryty.
Zastąp ciąg "[PLACEHOLDER: Table Name]" nazwą kwalifikowanej tabeli różnicowej, która ma zostać utworzona (na przykład "tweety"). Zastąp ciąg "[PLACEHOLDER: nazwa folderu dla punktów kontrolnych]" wartością ciągu, która jest unikatowa za każdym razem, gdy uruchamiasz ten kod (na przykład "ETL-from-eventhub-20190605").
Aby dowiedzieć się więcej o usłudze Delta Lake na Azure Databricks, zobacz temat [Delta Lake](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Zastąp ciąg "[symbol zastępczy: Nazwa tabeli]" tą samą nazwą tabeli różnicowej, która została wybrana powyżej.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Dane wyjściowe w następujący sposób:
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Teraz zagregowane dane szeregów czasowych są ciągle pozyskiwane w ramach delty. Następnie można zaplanować zadanie godzinowe w celu wykrycia anomalii najnowszego punktu.
Zastąp ciąg "[symbol zastępczy: Nazwa tabeli]" tą samą nazwą tabeli różnicowej, która została wybrana powyżej.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Wynik w następujący sposób:

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Gotowe. Korzystając z Azure Databricks, dane przesyłane strumieniowo do usługi Azure Event Hubs zostały pomyślnie przesłane przy użyciu łącznika Event Hubs, a następnie uruchamiane jest wykrywanie anomalii na danych przesyłanych strumieniowo w czasie niemal rzeczywistym.
Chociaż w tym samouczku stopień szczegółowości jest co godzinę, zawsze można zmienić stopień szczegółowości, aby sprostać potrzebom.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym Azure Databricks wybierz pozycję **klastry** w okienku po lewej stronie. W przypadku klastra, który chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje** , a następnie wybierz ikonę **zakończenia** , a następnie wybierz pozycję **Potwierdź**.

![Zatrzymaj klaster datakostki](../media/tutorials/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie zatrzymany, pod warunkiem, że podczas tworzenia klastra zaznaczono pole wyboru **Przerwij po \_ \_ minutach braku aktywności** . W takim przypadku nieaktywny klaster zostanie automatycznie zatrzymany po określonym czasie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono użycie usługi Azure Databricks w celu przesłania strumienia danych do usługi Azure Event Hubs oraz odczytania tego strumienia z usługi Event Hubs w czasie rzeczywistym. Przejdź do następnego samouczka, aby dowiedzieć się, jak wywołać interfejs API wykrywania anomalii i wizualizować anomalie przy użyciu Power BI Desktop.

> [!div class="nextstepaction"]
>[Wykrywanie anomalii w usłudze Batch przy użyciu Power BI Desktop](batch-anomaly-detection-powerbi.md)
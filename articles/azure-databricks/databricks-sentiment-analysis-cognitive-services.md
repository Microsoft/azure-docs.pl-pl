---
title: Użyj Azure Databricks do analizy tonacji
description: Dowiedz się, jak używać Azure Databricks z interfejsem API Event Hubs i Cognitive Services do uruchamiania analizy tonacji na danych przesyłanych strumieniowo w czasie niemal rzeczywistym.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 382dff156c088f367200f0dd46c3758193ade189
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75889225"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Samouczek: Analiza tonacji na strumieniu danych wykonywana przy użyciu usługi Azure Databricks

W tym samouczku dowiesz się, jak przy użyciu usługi Azure Databricks przeprowadzać analizę tonacji na strumieniu danych w czasie niemal rzeczywistym. Konfiguracja systemu pozyskiwania danych jest oparta na usłudze Azure Event Hubs. Do przesyłania komunikatów z usługi Event Hubs do usługi Azure Databricks służy łącznik Spark Event Hubs. Na koniec korzystasz z interfejsów API usługi poznawczej do uruchamiania analizy tonacji na danych przesyłanych strumieniowo.

Wykonanie kroków tego samouczka pozwoli Ci przesłać strumieniowo tweety, które zawierają termin „Azure”, i przeprowadzić na nich analizę tonacji.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Azure Databricks z Event Hubs i Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Azure Databricks z Event Hubs i Cognitive Services")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter w celu uzyskania dostępu do danych strumienia
> * Tworzenie notesów w usłudze Azure Databricks
> * Dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Utwórz konto Cognitive Services i Pobierz klucz dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Przeprowadzanie analizy tonacji na tweetach

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall) .

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-azure-portal)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania:
- Przestrzeń nazw usługi Azure Event Hubs.
- Centrum zdarzeń w przestrzeni nazw.
- Parametry połączenia umożliwiające dostęp do przestrzeni nazw usługi Event Hubs. Parametry połączenia powinny mieć mniej więcej taki format: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Nazwa zasad dostępu współdzielonego i klucz zasad dla usługi Event Hubs.

Aby spełnić te wymagania, wystarczy wykonać kroki opisane w artykule [Create an Azure Event Hubs namespace and event hub (Tworzenie przestrzeni nazw i centrum zdarzeń usługi Azure Event Hubs)](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób** > **dane + analiza** > **Azure Databricks**.

    ![Datakostki na Azure Portal](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Datakostki na Azure Portal")

3. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

4. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta w portalu jest wyświetlany kafelek **przesyłanie wdrożenia dla Azure Databricks** po prawej stronie. Aby go zobaczyć, być może trzeba będzie przesunąć pulpit nawigacyjny w prawo. W górnej części ekranu jest również wyświetlany pasek postępu. Postęp można obserwować w dowolnym z tych obszarów.

    ![Kafelek wdrożenia datakostek](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Kafelek wdrożenia datakostek")

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Datakostki na platformie Azure](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Datakostki na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Tworzenie klastra usługi datakosteks Spark na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

   * Wprowadź nazwę klastra.
   * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **6,0** .
   * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności** . Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

   Wybierz pozycję proces roboczy klastra i rozmiar węzła sterownika odpowiednie dla kryteriów technicznych i [budżetu](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).

     Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Wykonaj poniższe instrukcje, aby utworzyć aplikację usługi Twitter i zarejestrować wartości potrzebne do ukończenia tego samouczka.

1. W przeglądarce sieci Web przejdź do usługi [Twitter dla deweloperów](https://developer.twitter.com/en/apps), a następnie wybierz pozycję **Utwórz aplikację**. Może zostać wyświetlony komunikat informujący o konieczności zastosowania konta dewelopera usługi Twitter. Możesz to zrobić bezpłatnie, a po zatwierdzeniu aplikacji powinna zostać wyświetlona wiadomość e-mail z potwierdzeniem. Zatwierdzenie konta dewelopera może potrwać kilka dni.

    ![Potwierdzenie konta dewelopera usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "Potwierdzenie konta dewelopera usługi Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

    ![Szczegóły aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Szczegóły aplikacji usługi Twitter")

    ![Szczegóły aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "Szczegóły aplikacji usługi Twitter")

3. Na stronie aplikacja wybierz kartę **klucze i tokeny** , a następnie skopiuj wartości **klucza interfejsu API klienta** i **klucza tajnego interfejsu API konsumenta**. Ponadto w celu wygenerowania tokenów dostępu wybierz pozycję **Utwórz** w obszarze **klucz dostępu token i dostęp do klucza tajnego tokenu** dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

    ![Szczegóły aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Szczegóły aplikacji usługi Twitter")

Zapisz wartości dotyczące aplikacji usługi Twitter. Będą one potrzebne w dalszej części tego samouczka.

## <a name="attach-libraries-to-spark-cluster"></a>Dołączanie bibliotek do klastra Spark

W tym samouczku tweety są wysyłane do usługi Event Hubs za pomocą interfejsów API usługi Twitter. Ponadto dane są odczytywane i zapisywane w usłudze Azure Event Hubs za pomocą [łącznika Event Hubs platformy Apache Spark](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall). Aby użyć tych interfejsów API jako części klastra, Dodaj je jako biblioteki do Azure Databricks i skojarz je z klastrem Spark. Poniższe instrukcje przedstawiają sposób dodawania biblioteki.

1. W obszarze roboczym Azure Databricks wybierz pozycję **klastry**, a następnie wybierz istniejący klaster Spark. W menu klaster wybierz polecenie **biblioteki** , a następnie kliknij przycisk **Instaluj nowe**.

   ![Okno dialogowe Dodawanie biblioteki](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Dodaj klaster lokalizowania biblioteki")

   ![Okno dialogowe Dodawanie biblioteki](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "Dodaj nową bibliotekę Zainstaluj nowe")

2. Na stronie Nowa biblioteka dla opcji **Source** SELECT **Maven**. W polu **koordynuj**kliknij pozycję **pakiety wyszukiwania** dla pakietu, który chcesz dodać. Oto współrzędne Maven bibliotek używanych w tym samouczku:

   * Łącznik Event Hubs platformy Spark — `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Interfejs API usługi Twitter — `org.twitter4j:twitter4j-core:4.0.7`

     ![Podaj współrzędne Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "Podaj współrzędne Maven")

     ![Podaj współrzędne Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "Przeszukaj współrzędne Maven")

3. Wybierz pozycję **Zainstaluj**.

4. W menu klaster upewnij się, że obie biblioteki są zainstalowane i prawidłowo dołączone.

    ![Sprawdź biblioteki](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "Sprawdź biblioteki")

6. Powtórz te kroki dla pakietu Twitter: `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Pobieranie klucza dostępu usług Cognitive Services

W tym samouczku użyjemy [interfejsów API usługi Azure Cognitive Services analiza tekstu](../cognitive-services/text-analytics/overview.md) do uruchamiania analizy tonacji na strumieniu tweetów niemal w czasie rzeczywistym. Przed użyciem interfejsów API należy utworzyć konto usługi Azure Cognitive Services na platformie Azure i pobrać klucz dostępu, aby użyć interfejsów API analiza tekstu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

2. Wybierz pozycję **+ Utwórz zasób**.

3. W obszarze Azure Marketplace wybierz pozycję **si i usługi Cognitive Services** > **interfejs API analizy tekstu**.

    ![Utwórz konto usługi poznawczej](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Utwórz konto usługi poznawczej")

4. W oknie dialogowym **Tworzenie** podaj następujące wartości:

    ![Utwórz konto usługi poznawczej](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Utwórz konto usługi poznawczej")

   - Wprowadź nazwę konta usług Cognitive Services.
   - Wybierz subskrypcję platformy Azure, w której utworzono konto.
   - Wybierz lokalizację platformy Azure.
   - Wybierz warstwę cenową usługi. Więcej informacji na temat cen usług Cognitive Services zawiera [strona z cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.

     Wybierz przycisk **Utwórz**.

5. Po utworzeniu konta na karcie **Przegląd** wybierz pozycję **Pokaż klucze dostępu**.

    ![Pokaż klucze dostępu](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Pokaż klucze dostępu")

    Ponadto skopiuj część adresu URL punktu końcowego, jak pokazano na zrzucie ekranu. Ten adres URL będzie potrzebny w dalszej części samouczka.

6. W obszarze **Zarządzanie kluczami** wybierz ikonę kopiowania obok klucza, którego chcesz użyć.

    ![Kopiuj klucze dostępu](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Kopiuj klucze dostępu")

7. Zapisz wartości adresu URL punktu końcowego i klucza dostępu, które zostały pobrane w tym kroku. Będą one potrzebne w dalszej części tego samouczka.

## <a name="create-notebooks-in-databricks"></a>Tworzenie notesów w usłudze Databricks

W tej sekcji w obszarze roboczym usługi Databricks zostaną utworzone dwa notesy o następujących nazwach:

- **SendTweetsToEventHub** — notes producenta służący do pobierania tweetów z usługi Twitter i przesyłania ich w strumieniu do usługi Event Hubs.
- **AnalyzeTweetsFromEventHub** — notes użytkownika służący do odczytywania tweetów z usługi Event Hubs i przeprowadzania analizy tonacji.

1. W lewym okienku wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycje **Utwórz** i **Notes**.

    ![Tworzenie notesu w kostkach](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Tworzenie notesu w kostkach")

2. W oknie dialogowym **Tworzenie notesu** wpisz **SendTweetsToEventHub**, jako język wybierz pozycję **Scala** i wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w kostkach](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Tworzenie notesu w kostkach")

    Wybierz przycisk **Utwórz**.

3. Powtórz te kroki, aby utworzyć notes **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Wysyłanie tweetów do usługi Event Hubs

W notesie **SendTweetsToEventHub** wklej następujący kod i zastąp symbole zastępcze utworzonymi wcześniej wartościami przestrzeni nazw usługi Event Hubs i aplikacji usługi Twitter. Ten notes przesyła w czasie rzeczywistym strumień tweetów ze słowem kluczowym „Azure” do usługi Event Hubs.

> [!NOTE]
> Interfejs API usługi Twitter ma pewne ograniczenia żądań i [limity przydziału](https://developer.twitter.com/en/docs/basics/rate-limiting.html). Jeśli w interfejsie API usługi Twitter nie są spełnione ograniczenia dotyczące szybkości standardowej, można wygenerować zawartość tekstową bez użycia interfejsu API usługi Twitter w tym przykładzie. W tym celu należy ustawić zmienną **DataSource** na `test` zamiast `twitter` i wypełnić listę **testSource** z preferowanym wejściem testu.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

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
      while (!finished) {
        val result = twitter.search(query)
        val statuses = result.getTweets()
        var lowestStatusId = Long.MaxValue
        for (status <- statuses.asScala) {
          if(!status.isRetweet()){
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu. Każde zdarzenie w danych wyjściowych jest tweetem zawierającym termin „Azure”, pobieranym do usługi Event Hubs.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Odczytywanie tweetów z usługi Event Hubs

W notesie **AnalyzeTweetsFromEventHub** wklej następujący kod i zastąp symbol zastępczy utworzonymi wcześniej wartościami dotyczącymi usługi Azure Event Hubs. Ten notes umożliwia odczyt tweetów przesłanych strumieniowo do usługi Event Hubs przy użyciu notesu **SendTweetsToEventHub**.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Dane wyjściowe są następujące:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Ponieważ dane wyjściowe są przedstawione w trybie binarnym, przy użyciu następującego fragmentu kodu można je przekształcić w ciąg.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Dane wyjściowe są teraz podobne do następującego fragmentu kodu:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Dane z usługi Azure Event Hubs zostały przesłane strumieniowo do usługi Azure Databricks niemal w czasie rzeczywistym za pomocą łącznika usługi Event Hubs dla platformy Apache Spark. Aby uzyskać więcej informacji na temat sposobu korzystania z łącznika usługi Event Hubs dla platformy Spark, zobacz [dokumentację łącznika](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="run-sentiment-analysis-on-tweets"></a>Przeprowadzanie analizy tonacji na tweetach

W tej sekcji jest przeprowadzana analiza tonacji na tweetach odebranych przy użyciu interfejsu API usługi Twitter. Fragmenty kodu należy dodać do tego samego notesu **AnalyzeTweetsFromEventHub**.

Najpierw dodaj nową komórkę kodu w notesie i wklej poniższy fragment kodu. Ten fragment kodu definiuje typy danych umożliwiające korzystanie z interfejsu API języka i tonacji.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Dodaj nową komórkę kodu i wklej poniższy fragment. Definiuje on obiekt zawierający funkcje umożliwiające wywołanie interfejsu API analizy tekstu w celu wykrycia języka i przeprowadzenia analizy tonacji. Upewnij się, że symbol zastępczy `<PROVIDE ACCESS KEY HERE>` został zastąpiony wartością pobieraną dla konta Cognitive Services.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
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

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Dodaj kolejną komórkę, aby określić funkcję zdefiniowaną przez użytkownika platformy Spark, która określa tonację.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Dodaj ostatnią komórkę kodu w celu przygotowania ramki danych z zawartością tweetu i tonacją skojarzoną z tym tweetem.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

Wartość bliższa **1** w kolumnie **Opinia** sugeruje zadowolenie z korzystania z platformy Azure. Wartość bliższa **0** sugeruje, że użytkownicy napotkali problemy podczas pracy z platformą Microsoft Azure.

Gotowe. Za pomocą usługi Azure Databricks udało się przesłać strumień danych do usługi Azure Event Hubs, pobrać go przy użyciu łącznika usługi Event Hubs, a następnie przeprowadzić analizę tonacji na tym strumieniu w czasie niemal rzeczywistym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**.

![Zatrzymaj klaster datakostki](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie zatrzymany, pod warunkiem, że podczas tworzenia klastra zaznaczono pole wyboru **Przerwij po \_ \_ minutach braku aktywności** . W takim przypadku nieaktywny klaster zostanie automatycznie zatrzymany po określonym czasie.

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono użycie usługi Azure Databricks w celu przesłania strumienia danych do usługi Azure Event Hubs oraz odczytania tego strumienia z usługi Event Hubs w czasie rzeczywistym. W tym samouczku omówiono:
> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter w celu uzyskania dostępu do danych strumienia
> * Tworzenie notesów w usłudze Azure Databricks
> * Dodawanie i dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Tworzenie konta usług Microsoft Cognitive Services i pobieranie klucza dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Przeprowadzanie analizy tonacji na tweetach

W następnym samouczku dowiesz się, jak wykonywać zadania uczenia maszynowego przy użyciu usługi Azure Databricks.

> [!div class="nextstepaction"]
>[Machine Learning przy użyciu Azure Databricks](/azure/databricks/applications/machine-learning/mllib/decision-trees)


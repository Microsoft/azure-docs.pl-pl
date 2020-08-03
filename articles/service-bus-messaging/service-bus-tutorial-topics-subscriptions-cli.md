---
title: 'Samouczek: aktualizowanie asortymentu zapasów detalicznych za pomocą kanałów publikowania/subskrybowania i filtrów tematów za pomocą interfejsu wiersza polecenia platformy Azure'
description: 'Samouczek: w tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z tematu i subskrypcji oraz jak dodawać reguły filtrów i używać ich przy użyciu interfejsu wiersza polecenia platformy Azure'
ms.date: 06/23/2020
ms.topic: tutorial
author: spelluru
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: cdbde84780ed7c64f9ad3f4284a74261d2f4a728
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500474"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Samouczek: aktualizowanie magazynu przy użyciu interfejsu wiersza polecenia oraz tematów/subskrypcji

Usługa Microsoft Azure Service Bus to wielodostępna usługa przesyłania komunikatów w chmurze, która przesyła informacje między aplikacjami i usługami. Operacje asynchroniczne umożliwiają elastyczne przesyłanie komunikatów obsługiwanych przez brokera oraz ustrukturyzowane przesyłanie komunikatów typu „pierwszy na wejściu — pierwszy na wyjściu” (FIFO, first-in, first-out) i zapewniają możliwości publikowania/subskrybowania. W tym samouczku przedstawiono, jak używać tematów i subskrypcji usługi Service Bus w scenariuszu obejmującym magazyn sklepu sieciowego, gdy kanały publikowania/subskrypcji korzystają z interfejsu wiersza polecenia Azure i języka Java.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu interfejsu wiersza polecenia Azure
> * Dodawanie filtrów tematu przy użyciu interfejsu wiersza polecenia Azure
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Przykładem tego scenariusza jest aktualizacja asortymentu magazynu na potrzeby wielu sklepów detalicznych. W tym scenariuszu każdy sklep, lub grupa sklepów, otrzymuje komunikaty o potrzebie aktualizacji swojego asortymentu. W tym samouczku przedstawiono, jak wdrożyć ten scenariusz przy użyciu subskrypcji i filtrów. Najpierw utwórz temat z 3 subskrypcjami, dodaj jakieś reguły i filtry, a następnie wysyłaj i odbieraj komunikaty z tematu i subskrypcji.

![temat](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][] przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć aplikację usługi Service Bus w języku Java, trzeba mieć zainstalowane następujące składniki:

- [Zestaw Java Development Kit](https://aka.ms/azure-jdks), najnowsza wersja.
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Tematy i subskrypcje usługi Service Bus

Każda [subskrypcja tematu](service-bus-messaging-overview.md#topics) może otrzymywać kopie wszystkich komunikatów. Tematy są w pełni protokołowane i semantycznie zgodne z kolejkami usługi Service Bus. Tematy usługi Service Bus obsługują najróżniejsze reguły wyboru z warunkami filtru, z użyciem opcjonalnych akcji, które ustawiają lub modyfikują właściwości komunikatów. Za każdym razem, gdy reguła pasuje, generuje komunikat. Aby dowiedzieć się więcej o regułach, filtrach i akcjach, kliknij ten [link](topic-filters.md).

## <a name="sign-in-to-azure"></a>Logowanie się na platformie Azure

Po zainstalowaniu interfejsu wiersza polecenia otwórz wiersz polecenia i uruchom następujące polecenia, aby zalogować się na platformie Azure. Te kroki nie są konieczne, jeśli używasz usługi Cloud Shell:

1. Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, uruchom następujące polecenie, aby zalogować się na platformie Azure. Ten krok logowania nie jest konieczny, jeśli polecenia są uruchamiane w usłudze Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Ustaw bieżący kontekst subskrypcji na subskrypcję platformy Azure, której chcesz używać:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Używanie interfejsu wiersza polecenia do inicjowania zasobów

Uruchom następujące polecenia w celu zainicjowania zasobów usługi Service Bus. Pamiętaj, aby zastąpić wszystkie elementy zastępcze odpowiednimi wartościami:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Po uruchomieniu ostatniego polecenia skopiuj i wklej parametry połączenia oraz nazwę wybranej kolejki do lokalizacji tymczasowej, np. Notatnika. Będą one potrzebne w kolejnym kroku.

## <a name="create-filter-rules-on-subscriptions"></a>Tworzenie reguł filtrowania dla subskrypcji

Po aprowizowaniu przestrzeni nazw i tematu/subskrypcji i jeśli posiadasz niezbędne poświadczenia, możesz utworzyć reguły filtrowania w subskrypcji, a następnie wysyłać i odbierać komunikaty. Kod można analizować w [tym folderze przykładów usługi GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

1. Upewnij się, że usługa Cloud Shell jest uruchomiona i wyświetla wiersz polecenia Bash.

2. Sklonuj [repozytorium GitHub usługi Service Bus](https://github.com/Azure/azure-service-bus/), wydając następujące polecenie:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Przejdź do folderu przykładów `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Pamiętaj, że w powłoce Bash w poleceniach jest rozróżniana wielkość liter i separatory ścieżki muszą mieć formę ukośników.

3. Wydaj następujące polecenie, aby skompilować aplikację:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Aby uruchomić program, wpisz następujące polecenie. Pamiętaj, aby zastąpić symbole zastępcze parametrami połączenia oraz nazwą tematu uzyskaną w poprzednim kroku:

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Obserwuj 10 komunikatów wysłanych do tematu, a następnie odebranych z poszczególnych subskrypcji:

   ![dane wyjściowe programu](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Uruchom następujące polecenie, aby usunąć grupę zasobów, przestrzeń nazw i wszystkie powiązane zasoby:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Ta sekcja zawiera więcej szczegółów na temat operacji wykonywanych przez przykładowy kod.

### <a name="get-connection-string-and-queue"></a>Pobieranie kolejki i parametrów połączenia

Najpierw w kodzie jest deklarowany zestaw zmiennych, które realizują pozostałe operacje programu:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Parametry połączenia i nazwa tematu są jedynymi wartościami dodawanymi przy użyciu parametrów wiersza polecenia i przekazywanymi do `main()`. Rzeczywiste wykonanie kodu jest wyzwalane w metodzie `run()` i wysyła, a następnie odbiera komunikaty z tematu:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Tworzenie klienta tematu do wysyłania komunikatów

Aby wysyłać i odbierać komunikaty, metoda `sendMessagesToTopic()` tworzy wystąpienie klienta tematu, które używa parametrów połączenia oraz nazwy tematu, a następnie wywołuje inną metodę, która wysyła komunikaty:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Odbieranie komunikatów z poszczególnych subskrypcji

Metoda `receiveAllMessages()` wywołuje metodę `receiveAllMessageFromSubscription()`, która następnie tworzy klienta subskrypcji do jednego wywołania i odbiera komunikaty z poszczególnych subskrypcji:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku zainicjowano zasoby przy użyciu interfejsu wiersza polecenia Azure, a następnie wysłano i odebrano komunikaty z tematu usługi Service Bus i jego subskrypcji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu witryny Azure Portal
> * Dodawanie filtrów tematu przy użyciu kodu platformy .NET
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Więcej przykładów dotyczących wysyłania i odbierania komunikatów znajduje się w temacie [Service Bus samples on GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) (Przykłady dotyczące usługi Service Bus w serwisie GitHub).

Przejdź do następnego samouczka, aby dowiedzieć się więcej o korzystaniu z możliwości publikowania/subskrypcji usługi Service Bus.

> [!div class="nextstepaction"]
> [aktualizowanie magazynu przy użyciu programu PowerShell oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create

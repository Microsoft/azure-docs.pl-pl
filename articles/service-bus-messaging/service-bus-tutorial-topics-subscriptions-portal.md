---
title: Aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji
description: W tym samouczku przedstawiono, jak wysyłać i odbierać komunikaty z tematu i subskrypcji oraz jak dodawać reguły filtru i używać ich za pomocą programu .NET
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: a7b03727c574dcee1cd56144a521f36de6dc48c6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861377"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Samouczek: aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji

Usługa Microsoft Azure Service Bus to wielodostępna usługa przesyłania komunikatów w chmurze, która przesyła informacje między aplikacjami i usługami. Operacje asynchroniczne umożliwiają elastyczne przesyłanie komunikatów obsługiwanych przez brokera oraz ustrukturyzowane przesyłanie komunikatów typu „pierwszy na wejściu — pierwszy na wyjściu” (FIFO, first-in, first-out) i zapewniają możliwości publikowania/subskrybowania. W tym samouczku przedstawiono, jak używać tematów i subskrypcji usługi Service Bus w scenariuszu obejmującym magazyn sklepu sieciowego, gdy kanały publikowania/subskrypcji korzystają z witryny Azure Portal oraz programu .NET.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu witryny Azure Portal
> * Dodawanie filtrów tematu przy użyciu kodu platformy .NET
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Przykładem tego scenariusza jest aktualizacja asortymentu magazynu na potrzeby wielu sklepów detalicznych. W tym scenariuszu każdy sklep, lub grupa sklepów, otrzymuje komunikaty o potrzebie aktualizacji swojego asortymentu. W tym samouczku przedstawiono, jak wdrożyć ten scenariusz przy użyciu subskrypcji i filtrów. Najpierw utwórz temat z 3 subskrypcjami, dodaj jakieś reguły i filtry, a następnie wysyłaj i odbieraj komunikaty z tematu i subskrypcji.

![temat](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne][] konto.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

- [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
- [Zestaw NET Core SDK](https://dotnet.microsoft.com/download), wersja 2.0 lub nowsza.

## <a name="service-bus-topics-and-subscriptions"></a>Tematy i subskrypcje usługi Service Bus

Każda [subskrypcja tematu](service-bus-messaging-overview.md#topics) może otrzymywać kopie wszystkich komunikatów. Tematy są w pełni protokołowane i semantycznie zgodne z kolejkami usługi Service Bus. Tematy usługi Service Bus obsługują najróżniejsze reguły wyboru z warunkami filtru, z użyciem opcjonalnych akcji, które ustawiają lub modyfikują właściwości komunikatów. Za każdym razem, gdy reguła pasuje, generuje komunikat. Aby dowiedzieć się więcej o regułach, filtrach i akcjach, kliknij ten [link](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Tworzenie reguł filtrowania dla subskrypcji

Po aprowizowaniu przestrzeni nazw i tematu/subskrypcji i jeśli posiadasz niezbędne poświadczenia, możesz utworzyć reguły filtrowania w subskrypcji, a następnie wysyłać i odbierać komunikaty. Kod można analizować w [tym folderze przykładów usługi GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

Aby uruchomić kod, wykonaj następujące czynności:

1. W wierszu polecenia lub wierszu polecenia programu PowerShell sklonuj [repozytorium GitHub usługi Service Bus](https://github.com/Azure/azure-service-bus/), uruchamiając następujące polecenie:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Przejdź do folderu przykładów `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Uzyskaj parametry połączenia skopiowane do Notatnika w sekcji Uzyskiwanie poświadczeń zarządzania tego samouczka. Potrzebna Ci będzie również nazwa tematu utworzonego w poprzedniej sekcji.

4. W wierszu polecenia wpisz następujące polecenie:

   ```shell
   dotnet build
   ```

5. Przejdź do folderu `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1`.

6. Wpisz następujące polecenie, aby uruchomić program. Pamiętaj, aby zastąpić `myConnectionString` wcześniej uzyskaną wartością, a `myTopicName` nazwą utworzonego przez siebie tematu:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Postępuj zgodnie z instrukcjami w konsoli, aby najpierw wybrać tworzenie filtru. Tworzenie filtrów obejmuje usuwanie filtrów domyślnych. Gdy używasz programu PowerShell lub interfejsu wiersza polecenia, nie musisz usuwać filtra domyślnego, ale jeśli pracujesz w kodzie, jest to konieczne. Polecenia konsoli 1 i 3 ułatwiają zarządzanie filtrami we wcześniej utworzonych subskrypcjach:

   - Wykonaj 1: aby usunąć filtry domyślne.
   - Wykonaj 2: aby dodać własne filtry.
   - Wykonaj 3: aby opcjonalnie usunąć własne filtry. Pamiętaj, że nie spowoduje to odtworzenia filtrów domyślnych.

     ![Wyświetlanie danych wyjściowych 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Po utworzeniu filtra możesz wysyłać komunikaty. Naciśnij 4 i obserwuj wysyłanie 10 komunikatów do tematu:

    ![Wysyłanie danych wyjściowych](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Naciśnij 5 i obserwuj odbieranie komunikatów. Jeśli 10 komunikatów nie zostało zwróconych, naciśnij „m”, aby wyświetlić menu, następnie naciśnij ponownie 5.

    ![Odbieranie danych wyjściowych](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy przestrzeń nazw i temat nie będą już potrzebne, usuń je. W tym celu wybierz te zasoby w portalu i kliknij przycisk **Usuń**.

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Ta sekcja zawiera więcej szczegółów na temat operacji wykonywanych przez przykładowy kod.

### <a name="get-connection-string-and-topic"></a>Pobieranie parametrów połączenia i tematu

Najpierw w kodzie jest deklarowany zestaw zmiennych, które realizują pozostałe operacje programu.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Parametry połączenia i nazwa tematu są przekazywane za pośrednictwem parametrów wiersza polecenia, jak to pokazano, a następnie odczytywane w metodzie `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Usuwanie filtrów domyślnych

Podczas tworzenia subskrypcji usługa Service Bus tworzy filtr domyślny dla każdej subskrypcji. Ten filtr umożliwia odbieranie wszystkich komunikatów wysłanych do tematu. Jeśli chcesz używać filtrów niestandardowych, możesz usunąć filtr domyślny, jak pokazano w poniższym kodzie:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Tworzenie filtrów

Poniższy kod dodaje filtry niestandardowe zdefiniowane w tym samouczku:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Usuwanie utworzonych filtrów niestandardowych

Jeśli chcesz usunąć wszystkie filtry w ramach subskrypcji, poniższy kod pokazuje, jak to zrobić:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Wysyłanie komunikatów

Wysyłanie komunikatów do tematu przypomina wysyłanie komunikatów do kolejki. W tym przykładzie pokazano, jak wysyłać komunikaty z wykorzystaniem listy zadań i przetwarzania asynchronicznego:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Odbieranie komunikatów

Komunikaty są ponownie odbierane za pośrednictwem listy zadań, a kod korzysta z dzielenia na partie. Umożliwia wysyłanie i odbieranie z wykorzystaniem dzielenia na partie, ale w tym przykładzie pokazano tylko, jak odbierać partie. W rzeczywistości nie należy wychodzić z pętli, ale kontynuować zapętlenie i ustawić większy przedział czasu, np. jedną minutę. Wywołanie receive do brokera pozostaje przez ten czas otwarte i jeśli dotrą komunikaty, zostaną od razu zwrócone i zostanie wysłane nowe wywołanie receive. To pojęcie jest nazywane *długim sondowaniem*. Częstszym rozwiązaniem jest użycie pompy receive opisane w przewodniku [Szybki start](service-bus-quickstart-portal.md) oraz w kilku innych przykładach w repozytorium.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Za pomocą Service Bus można zarządzać [zasobami Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Usługa Service Bus Explorer umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie udostępnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku zainicjowano zasoby przy użyciu witryny Azure Portal, a następnie wysłano i odebrano komunikaty z tematu usługi Service Bus i jego subskrypcji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu witryny Azure Portal
> * Dodawanie filtrów tematu przy użyciu kodu platformy .NET
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Więcej przykładów dotyczących wysyłania i odbierania komunikatów znajduje się w temacie [Service Bus samples on GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) (Przykłady dotyczące usługi Service Bus w serwisie GitHub).

Przejdź do następnego samouczka, aby dowiedzieć się więcej o korzystaniu z możliwości publikowania/subskrypcji usługi Service Bus.

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia za pomocą usługi Event Grid](service-bus-to-event-grid-integration-example.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png

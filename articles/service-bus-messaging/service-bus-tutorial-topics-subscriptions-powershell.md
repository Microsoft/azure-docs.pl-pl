---
title: Samouczek — aktualizowanie asortymentu magazynu sklepu sieciowego przy użyciu kanałów publikowania/subskrypcji i filtrów tematów za pomocą programu Azure PowerShell | Microsoft Docs
description: W tym samouczku przedstawiono, jak wysyłać i odbierać komunikaty z tematu i subskrypcji oraz jak dodawać reguły filtru i używać ich za pomocą programu Azure PowerShell
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: tutorial
ms.openlocfilehash: b1c6390d66d01bbe91500fc3878ad138eea208cc
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341616"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Samouczek: aktualizowanie magazynu przy użyciu programu PowerShell oraz tematów/subskrypcji

Usługa Microsoft Azure Service Bus to wielodostępna usługa przesyłania komunikatów w chmurze, która przesyła informacje między aplikacjami i usługami. Operacje asynchroniczne umożliwiają elastyczne przesyłanie komunikatów obsługiwanych przez brokera oraz ustrukturyzowane przesyłanie komunikatów typu „pierwszy na wejściu — pierwszy na wyjściu” (FIFO, first-in, first-out) i zapewniają możliwości publikowania/subskrybowania. 

W tym samouczku opisano, jak wysyłać i odbierać komunikaty z i do kolejki usługi Service Bus, używając programu PowerShell do utworzenia przestrzeni nazw do przesyłania komunikatów oraz kolejki w ramach tej przestrzeni nazw i uzyskać poświadczenia autoryzacji do tej przestrzeni nazw. Następnie w procedurze przedstawiono, jak wysyłać i odbierać komunikaty z tej kolejki przy użyciu [biblioteki platformy .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu programu Azure PowerShell
> * Dodawanie filtrów tematu przy użyciu programu PowerShell
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Przykładem tego scenariusza jest aktualizacja asortymentu magazynu na potrzeby wielu sklepów detalicznych. W tym scenariuszu każdy sklep, lub grupa sklepów, otrzymuje komunikaty o potrzebie aktualizacji swojego asortymentu. W tym samouczku przedstawiono, jak wdrożyć ten scenariusz przy użyciu subskrypcji i filtrów. Najpierw utwórz temat z 3 subskrypcjami, dodaj jakieś reguły i filtry, a następnie wysyłaj i odbieraj komunikaty z tematu i subskrypcji.

![temat](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto][] .


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

1. [Program Visual Studio 2017 Update 3 (wersja 15.3, 26730.01)](https://www.visualstudio.com/vs) lub nowszy.
2. [Zestaw NET Core SDK](https://www.microsoft.com/net/download/windows), wersja 2.0 lub nowsza.

Ten samouczek wymaga używania najnowszej wersji programu Azure PowerShell. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie i konfigurowanie Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Uruchom następujące polecenia, aby zalogować się na platformie Azure. Te kroki nie są konieczne, jeśli używasz poleceń programu PowerShell w usłudze Cloud Shell: 

1. Zainstaluj moduł programu PowerShell usługi Service Bus:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Ustaw kontekst bieżącej subskrypcji lub wyświetl aktywną subskrypcję:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Inicjowanie zasobów

Po zalogowaniu na platformie Azure uruchom następujące polecenia w celu zainicjowania zasobów usługi Service Bus. Pamiętaj, aby zastąpić wszystkie elementy zastępcze odpowiednimi wartościami:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Po uruchomieniu `Get-AzServiceBusKey` polecenia cmdlet skopiuj i wklej parametry połączenia oraz nazwę wybranej kolejki do lokalizacji tymczasowej, np. Notatnika. Będą one potrzebne w kolejnym kroku.

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

Po utworzeniu przestrzeni nazw i kolejki (i jeśli masz niezbędne poświadczenia) możesz wysyłać i odbierać komunikaty. Kod można analizować w [tym folderze przykładów usługi GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Aby uruchomić kod, wykonaj następujące czynności:

1. Sklonuj [repozytorium GitHub usługi Service Bus](https://github.com/Azure/azure-service-bus/), wydając następujące polecenie:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Otwórz wiersz polecenia programu PowerShell.

3. Przejdź do folderu przykładów `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Jeśli ta czynność nie została jeszcze wykonana, uzyskaj parametry połączenia przy użyciu następującego polecenia cmdlet programu PowerShell. Pamiętaj, aby zastąpić `my-resourcegroup` i `namespace-name` konkretnymi wartościami: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. W wierszu polecenia programu PowerShell wpisz następujące polecenie:

   ```shell
   dotnet build
   ```
6. Przejdź do folderu `\bin\Debug\netcoreapp2.0`.
7. Wpisz następujące polecenie, aby uruchomić program. Pamiętaj, aby zastąpić `myConnectionString` wcześniej uzyskaną wartością, a `myQueueName` nazwą utworzonej przez siebie kolejki:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Obserwuj 10 komunikatów wysłanych do kolejki, a następnie odebranych z niej:

   ![dane wyjściowe programu](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Uruchom następujące polecenie, aby usunąć grupę zasobów, przestrzeń nazw i wszystkie powiązane zasoby:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Ta sekcja zawiera więcej szczegółów na temat operacji wykonywanych przez przykładowy kod. 

### <a name="get-connection-string-and-queue"></a>Pobieranie kolejki i parametrów połączenia

Parametry połączenia i nazwa kolejki są przekazywane do metody `Main()` jako argumenty wiersza polecenia. Element `Main()` deklaruje dwie zmienne ciągu do przechowywania tych wartości:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Metoda `Main()` następnie rozpoczyna asynchroniczną pętlę komunikatów, `MainAsync()`.

### <a name="message-loop"></a>Pętla komunikatów

Metoda `MainAsync()` tworzy klienta kolejki z argumentami wiersza polecenia, wywołuje procedurę obsługi komunikatów o nazwie `RegisterOnMessageHandlerAndReceiveMessages()` i wysyła zestaw komunikatów:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

Metoda `RegisterOnMessageHandlerAndReceiveMessages()` ustawia kilka opcji procedury obsługi komunikatów, a następnie wywołuje metodę `RegisterMessageHandler()` klienta kolejki, która rozpoczyna odbieranie:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Wysyłanie komunikatów

Operacje tworzenia i wysyłania komunikatów są wykonywane w metodzie `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Przetwarzanie komunikatów

Metoda `ProcessMessagesAsync()` potwierdza, przetwarza i kończy odbieranie komunikatów:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku zainicjowano zasoby przy użyciu programu Azure PowerShell, a następnie wysłano i odebrano komunikaty z tematu usługi Service Bus i jego subskrypcji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie tematu usługi Service Bus i co najmniej jednej subskrypcji do tego tematu przy użyciu witryny Azure Portal
> * Dodawanie filtrów tematu przy użyciu kodu platformy .NET
> * Tworzenie dwóch komunikatów z różną zawartością
> * Wysyłanie komunikatów oraz sprawdzanie, czy dotarły do spodziewanych subskrypcji
> * Odbieranie komunikatów z subskrypcji

Więcej przykładów dotyczących wysyłania i odbierania komunikatów znajduje się w temacie [Service Bus samples on GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) (Przykłady dotyczące usługi Service Bus w serwisie GitHub).

Przejdź do następnego samouczka, aby dowiedzieć się więcej o korzystaniu z możliwości publikowania/subskrypcji usługi Service Bus.

> [!div class="nextstepaction"]
> [aktualizowanie magazynu przy użyciu programu PowerShell oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-cli.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalowanie i konfigurowanie programu Azure PowerShell]: /powershell/azure/install-Az-ps

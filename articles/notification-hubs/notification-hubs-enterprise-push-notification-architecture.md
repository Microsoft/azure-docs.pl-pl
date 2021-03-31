---
title: Architektura wypychana Notification Hubs Enterprise
description: Dowiedz się więcej o korzystaniu z usługi Azure Notification Hubs w środowisku przedsiębiorstwa
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89003630"
---
# <a name="enterprise-push-architectural-guidance"></a>Wskazówki dotyczące architektury powiadomień wypychanych w przedsiębiorstwie

Obecnie przedsiębiorstwa są stopniowo przenoszone do tworzenia aplikacji mobilnych dla użytkowników końcowych (zewnętrznych) lub dla pracowników (wewnętrznych). Mają one istniejące systemy zaplecza lub niektóre aplikacje biznesowe, które muszą być zintegrowane z architekturą aplikacji mobilnej. Ten przewodnik zawiera informacje o tym, jak najlepiej wykonać tę integrację, zalecanym rozwiązaniem dla typowych scenariuszy.

Częstym wymaganiem jest wysyłanie powiadomień wypychanych do użytkowników za pomocą ich aplikacji mobilnych w przypadku wystąpienia zainteresowania w systemach zaplecza. Na przykład odbiorca banku, który ma aplikację bankową banku na telefonie iPhone, chce otrzymywać powiadomienia, gdy opłata zostanie wprowadzona powyżej określonej kwoty z konta lub intranetowego scenariusza, w którym pracownik z działu finansowego, który ma aplikację zatwierdzania budżetu na Windows Phone, chce otrzymywać powiadomienia o otrzymaniu żądania zatwierdzenia.

Konto bankowe lub przetwarzanie zatwierdzania jest prawdopodobnie wykonywane w niektórych systemach zaplecza, które muszą inicjować wypychanie do użytkownika. Może istnieć wiele systemów zaplecza, które muszą kompilować ten sam rodzaj logiki do wypychania, gdy zdarzenie wyzwala powiadomienie. Złożoność w tym miejscu polega na integrowaniu kilku systemów zaplecza z jednym systemem wypychania, w którym użytkownicy końcowi mogą mieć subskrypcję różnych powiadomień, a nawet wiele aplikacji mobilnych. Na przykład intranetowe aplikacje mobilne, w których jedna aplikacja mobilna może chcieć otrzymywać powiadomienia z wielu takich systemów zaplecza. Systemy wewnętrznej bazy danych nie znają lub nie muszą znać semantyki wypychania/technologii, więc w tym przypadku powszechnie stosowane jest wprowadzenie składnika, który sonduje systemy zaplecza pod kątem wszelkich interesujących wydarzeń i jest odpowiedzialny za wysyłanie komunikatów wypychanych do klienta.

Lepszym rozwiązaniem jest użycie modelu Azure Service Bus-tematu/subskrypcji, co zmniejsza złożoność podczas skalowalności rozwiązania.

Poniżej znajduje się ogólna architektura rozwiązania (uogólniona wieloma aplikacjami mobilnymi, ale jest jednakowa w przypadku tylko jednej aplikacji mobilnej)

## <a name="architecture"></a>Architektura

![Diagram architektury przedsiębiorstwa przedstawiający przepływ za pomocą zdarzeń, subskrypcji i komunikatów wypychanych.][1]

Kluczowym elementem na tym diagramie architektury jest Azure Service Bus, w którym znajdują się tematy/model programowania subskrypcji (więcej informacji na ten temat znajduje się w [Service Bus programowaniu/podrzędnym]). Odbiorca, który w tym przypadku jest zapleczem mobilnym (zazwyczaj [Usługa mobilna platformy Azure], która inicjuje wypychanie do aplikacji mobilnych) nie odbiera komunikatów bezpośrednio z systemów zaplecza, ale zamiast tego, pośrednia warstwa abstrakcji udostępniana przez [Azure Service Bus], która umożliwia zapleczu mobilnego odbieranie komunikatów z jednego lub kilku systemów zaplecza. Należy utworzyć temat Service Bus dla każdego z systemów zaplecza, na przykład konto, HR, finanse, co jest zasadniczo "tematami", które inicjuje wysyłanie komunikatów jako powiadomień wypychanych. Systemy zaplecza wysyłają komunikaty do tych tematów. Zaplecze mobilne mogą subskrybować jeden lub więcej takich tematów, tworząc subskrypcję Service Bus. Uprawnia do korzystania z zaplecza mobilnego w celu otrzymywania powiadomień z odpowiedniego systemu zaplecza. Zaplecze Mobile w dalszym ciągu nasłuchuje komunikatów w swoich subskrypcjach i zaraz po nadejściu wiadomości zostanie przywrócony i wysłany jako powiadomienie do centrum powiadomień. Centra powiadomień ostatecznie dostarczają komunikat do aplikacji mobilnej. Oto lista kluczowych składników:

1. Systemy zaplecza (systemy LoB/starsze)
   * Tworzy temat Service Bus
   * Wysyła wiadomość
1. Zaplecze mobilne
   * Tworzy subskrypcję usługi
   * Odbiera komunikat (z systemu zaplecza)
   * Wysyła powiadomienie do klientów (za pośrednictwem Centrum powiadomień platformy Azure)
1. Aplikacja mobilna
   * Odbiera i wyświetla powiadomienie

### <a name="benefits"></a>Korzyści

1. Rozdzielenie między odbiornikiem (aplikacją mobilną/usługą za pośrednictwem Centrum powiadomień) i nadawcą (systemy zaplecza) umożliwia integrację dodatkowych systemów zaplecza z minimalnymi zmianami.
1. Ponadto scenariusz wielu aplikacji mobilnych może odbierać zdarzenia z co najmniej jednego systemu zaplecza.  

## <a name="sample"></a>Przykład

### <a name="prerequisites"></a>Wymagania wstępne

Aby zapoznać się z pojęciami oraz typowymi krokami tworzenia &, należy wykonać następujące samouczki:

1. [Service Bus programowanie/procedura podrzędna] — w tym samouczku objaśniono szczegółowe informacje dotyczące pracy z Service Bus tematami/subskrypcjami, jak utworzyć obszar nazw zawierający tematy/subskrypcje, jak wysyłać & odbierać wiadomości z nich.
2. [Notification Hubs — uniwersalny Samouczek systemu Windows] — w tym samouczku wyjaśniono, jak skonfigurować aplikację ze sklepu Windows i użyć Notification Hubs do rejestracji i otrzymywania powiadomień.

### <a name="sample-code"></a>Przykładowy kod

Pełny przykładowy kod jest dostępny w [przykładach centrum powiadomień]. Jest on podzielony na trzy składniki:

1. **EnterprisePushBackendSystem**

    a. Ten projekt używa pakietu NuGet **windowsazure. ServiceBus** i jest oparty na [Service Bus programowaniu/sub].

    b. Ta aplikacja jest prostą aplikacją konsolową języka C# do symulowania systemu LoB, który inicjuje komunikat do dostarczenia do aplikacji mobilnej.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` służy do tworzenia tematu Service Bus.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` służy do wysyłania komunikatów do tego tematu Service Bus. Ten kod po prostu wysyła zestaw losowych komunikatów do tematu okresowo na potrzeby przykładu. Zwykle istnieje system zaplecza, który wysyła komunikaty po wystąpieniu zdarzenia.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Ten projekt używa pakietów NuGet *windowsazure. ServiceBus* i **Microsoft. Web. WebJobs. publish** i jest oparty na [Service Bus programowaniu/sub].

    b. Następująca aplikacja konsolowa jest uruchamiana jako [zadanie WebJob platformy Azure] , ponieważ musi działać w sposób ciągły, aby nasłuchiwać komunikatów z systemów LOB/zaplecza. Ta aplikacja jest częścią zaplecza mobilnego.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` służy do tworzenia subskrypcji Service Bus dla tematu, w którym system zaplecza wysyła komunikaty. W zależności od scenariusza biznesowego ten składnik tworzy co najmniej jedną subskrypcję z odpowiednimi tematami (na przykład niektóre mogą odbierać komunikaty z systemu kadr, niektóre z systemu finansowego itd.)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` służy do odczytywania wiadomości z tematu przy użyciu jej subskrypcji, a jeśli odczyt powiedzie się, należy skierować powiadomienie (w przykładowym scenariuszu wyskakujące powiadomienie systemu Windows Native) do wysłania do aplikacji mobilnej przy użyciu usługi Azure Notification Hubs.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Aby opublikować tę aplikację jako **zadanie WebJob**, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio i wybierz polecenie **Publikuj jako zadanie WebJob**

    ![Zrzut ekranu przedstawiający opcje dostępne po kliknięciu prawym przyciskiem myszy, które są wyświetlane z opcją Publikuj jako zadanie WebJob platformy Azure podane na czerwono.][2]

    f. Wybierz swój profil publikowania i Utwórz nową witrynę sieci Web systemu Azure, jeśli jeszcze nie istnieje, która hostuje ten element WebJob i gdy witryna sieci Web zostanie **opublikowana**.

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Zrzut ekranu przedstawiający przepływ pracy służący do tworzenia witryny na platformie Azure.":::
    Zrzut ekranu okna dialogowego publikowanie w sieci Web z wybraną opcją Microsoft Azure Websites, zieloną strzałką wskazującą okno dialogowe Wybierz istniejącą witrynę sieci Web z nową opcją podaną na czerwono i zieloną strzałkę wskazującą okno dialogowe Tworzenie witryny w Microsoft Azure z nazwą lokacji i Utwórz opcje opisane na czerwono.
    :::image-end:::

    przykład Skonfiguruj zadanie jako "Uruchom w sposób ciągły", aby podczas logowania się do [Azure Portal] powinna wyglądać podobnie do poniższego:

    ![Zrzut ekranu witryny Azure Portal z wyświetlonymi zadaniami Website zaplecza Enterprise push oraz wartościami nazwy, harmonogramu i dzienników pokreślonymi na czerwono.][4]

3. **EnterprisePushMobileApp**

    a. Ta aplikacja jest aplikacją ze sklepu Windows, która odbiera wyskakujące powiadomienia z Zadania WebJob działającego w ramach zaplecza mobilnego i wyświetla je. Ten kod jest oparty na [uniwersalnym samouczku Notification Hubs systemu Windows].  

    b. Upewnij się, że aplikacja jest włączona, aby otrzymywać wyskakujące powiadomienia.

    c. Upewnij się, że następujący kod rejestracji Notification Hubs jest wywoływany podczas uruchamiania aplikacji (po zastępowaniu `HubName` wartości i `DefaultListenSharedAccessSignature` :

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

1. Upewnij się, że zadanie WebJob działa prawidłowo i zaplanowane do ciągłego działania.
2. Uruchom **EnterprisePushMobileApp**, który uruchamia aplikację ze sklepu Windows.
3. Uruchom aplikację konsolową **EnterprisePushBackendSystem** , która symuluje zaplecze obiektów LOB i uruchamia wysyłanie komunikatów, a następnie zobaczysz wyskakujące powiadomienia, które są wyświetlane jak na poniższej ilustracji:

    ![Zrzut ekranu konsoli programu z uruchomioną aplikacją systemową wypychania zaplecza Enterprise i wiadomości, która jest wysyłana przez aplikację.][5]

4. Wiadomości zostały pierwotnie wysłane do tematów Service Bus, które były monitorowane przez Service Bus subskrypcje w zadaniu sieci Web. Po odebraniu komunikatu zostało utworzone i wysłane powiadomienie do aplikacji mobilnej. Możesz przejrzeć dzienniki zadań WebJob, aby potwierdzić przetwarzanie po przejściu do linku dzienniki w [Azure Portal] zadania sieci Web:

    ![Zrzut ekranu przedstawiający okno dialogowe Szczegóły ciągłego Zadania WebJob z komunikatem, który jest wyświetlany w kolorze czerwonym.][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Przykłady centrum powiadomień]: https://github.com/Azure/azure-notificationhubs-samples
[Usługa mobilna platformy Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Service Bus programowanie/procedura podrzędna]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Zadanie WebJob platformy Azure]: ../app-service/webjobs-create.md
[Notification Hubs — uniwersalny Samouczek systemu Windows]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Witryna Azure Portal]: https://portal.azure.com/

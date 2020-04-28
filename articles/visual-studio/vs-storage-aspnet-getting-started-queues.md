---
title: Rozpoczynanie pracy z usługą Azure queue storage przy użyciu programu Visual Studio (ASP.NET)
description: Jak rozpocząć pracę z usługą Azure queue storage w projekcie ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980756"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z usługą Azure queue storage i usługami połączonymi programu Visual Studio (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie

Usługa Azure queue storage udostępnia komunikaty w chmurze między składnikami aplikacji. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek usługi Azure queue storage. Te scenariusze obejmują typowe zadania, takie jak Tworzenie kolejki platformy Azure oraz dodawanie, modyfikowanie, odczytywanie i usuwanie komunikatów w kolejce.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery**, a następnie z menu kontekstowego wybierz pozycję **Dodaj->kontroler**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC 5 — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. W oknie dialogowym **Dodawanie kontrolera** Nadaj nazwę kontrolerowi *QueuesController*i wybierz pozycję **Dodaj**.

    ![Nazwij kontroler MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Dodaj następujące dyrektywy *using* do `QueuesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższe kroki ilustrują sposób tworzenia kolejki:

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`. 

1. Dodaj metodę o nazwie **isqueue** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W metodzie w **kolejce** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz obiekt **CloudQueue** , który reprezentuje odwołanie do żądanej nazwy kolejki. Metoda **CloudQueueClient. GetQueueReference** nie wykonuje żądania w stosunku do magazynu kolejki. Odwołanie jest zwracane niezależnie od tego, czy kolejka istnieje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj metodę **CloudQueue. metodę createifnotexists** , aby utworzyć kolejkę, jeśli jeszcze nie istnieje. Metoda **CloudQueue. metodę createifnotexists** zwraca **wartość true** , jeśli kolejka nie istnieje i została utworzona pomyślnie. W przeciwnym razie zwracana jest **wartość false** .    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Zaktualizuj **ViewBag** z nazwą kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** , w polu Nazwa widoku wprowadź wartość w **kolejce** , a następnie wybierz pozycję **Dodaj**.

1. Otwórz `CreateQueue.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz kolejkę** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Utwórz kolejkę](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak wspomniano wcześniej, Metoda **CloudQueue. metodę createifnotexists** zwraca **wartość true** tylko wtedy, gdy kolejka nie istnieje i została utworzona. W związku z tym, jeśli aplikacja zostanie uruchomiona, gdy istnieje kolejka, metoda zwraca **wartość false**. Aby uruchomić aplikację wielokrotnie, należy ją usunąć przed ponownym uruchomieniem aplikacji. Usuwanie kolejki można wykonać za pomocą metody **CloudQueue. Delete** . Możesz również usunąć kolejkę za pomocą [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Po [utworzeniu kolejki](#create-a-queue)można dodać do niej komunikaty. Ta sekcja przeprowadzi Cię przez proces dodawania komunikatów do *kolejki testu*kolejki. 

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **AddMessage** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W metodzie **AddMessage** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz obiekt **CloudQueueContainer** , który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Utwórz obiekt **CloudQueueMessage** reprezentujący komunikat, który chcesz dodać do kolejki. Obiekt **CloudQueueMessage** można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Wywołaj metodę **CloudQueue. AddMessage** , aby dodać komunikat do kolejki.

    ```csharp
    queue.AddMessage(message);
    ```

1. Utwórz i ustaw kilka właściwości **ViewBag** , które mają być wyświetlane w widoku.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź wartość **AddMessage** dla nazwy widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `AddMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj komunikat** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Dodaj komunikat](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dwie sekcje — [odczytywanie komunikatu z kolejki bez usuwania go](#read-a-message-from-a-queue-without-removing-it) i [odczytywania i usuwania komunikatów z](#read-and-remove-a-message-from-a-queue) kolejki — ilustruje sposób odczytywania komunikatów z kolejki.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Odczytywanie wiadomości z kolejki bez jej usuwania

W tej sekcji pokazano, jak uzyskać wgląd w komunikat w kolejce (Przeczytaj pierwszy komunikat bez usuwania).  

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **PeekMessage** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W metodzie **PeekMessage** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz obiekt **CloudQueueContainer** , który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj metodę **CloudQueue. PeekMessage** , aby odczytać pierwszy komunikat w kolejce bez usuwania go z kolejki. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Zaktualizuj **ViewBag** z dwiema wartościami: nazwą kolejki i komunikatem, który został odczytany. Obiekt **CloudQueueMessage** uwidacznia dwie właściwości do pobrania wartości obiektu: **CloudQueueMessage. AsBytes** i **CloudQueueMessage. AsString**. **AsString** (używane w tym przykładzie) zwraca ciąg, podczas gdy **AsBytes** zwraca tablicę bajtów.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **PeekMessage** w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `PeekMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **wgląd do wiadomości** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Wgląd do wiadomości](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Odczytaj i Usuń komunikat z kolejki

W tej sekcji dowiesz się, jak odczytywać i usuwać wiadomości z kolejki.   

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **ReadMessage** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W metodzie **ReadMessage** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz obiekt **CloudQueueContainer** , który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj metodę **CloudQueue. GetMessage** , aby odczytać pierwszą wiadomość w kolejce. Metoda **CloudQueue. GetMessage** sprawia, że komunikat niewidoczny przez 30 sekund (domyślnie) do dowolnego innego kodu odczytującego komunikaty, dzięki czemu żaden inny kod nie może modyfikować ani usuwać wiadomości podczas przetwarzania. Aby zmienić czas, przez jaki komunikat jest niewidoczny, zmodyfikuj parametr **visibilityTimeout** , który jest przesyłany do metody **CloudQueue. GetMessage** .

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Wywołaj metodę **CloudQueueMessage. Delete** , aby usunąć komunikat z kolejki.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Zaktualizuj **ViewBag** z usuniętym komunikatem i nazwą kolejki.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **ReadMessage** w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `ReadMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz **komunikat Odczytaj/Usuń** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Odczytaj i Usuń komunikat](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

W tej sekcji pokazano, jak uzyskać długość kolejki (liczbę komunikatów). 

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **GetQueueLength** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W metodzie **ReadMessage** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz obiekt **CloudQueueContainer** , który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj metodę **CloudQueue. FetchAttributes** , aby pobrać atrybuty kolejki (łącznie z jej długością). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Uzyskaj dostęp do właściwości **CloudQueue. ApproximateMessageCount** w celu uzyskania długości kolejki.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Zaktualizuj **ViewBag** z nazwą kolejki i jej długością.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **GetQueueLength** w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `GetQueueLengthMessage.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Pobierz długość kolejki** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Pobieranie długości kolejki](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Usuwanie kolejki
W tej sekcji przedstawiono sposób usuwania kolejki. 

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki [konfigurowania środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `QueuesController.cs`.

1. Dodaj metodę o nazwie **DeleteQueue** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. W metodzie **DeleteQueue** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Pobierz obiekt **CloudQueueClient** reprezentuje klienta usługi kolejki.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Pobierz obiekt **CloudQueueContainer** , który reprezentuje odwołanie do kolejki. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Wywołaj metodę **CloudQueue. Delete** , aby usunąć kolejkę reprezentowaną przez obiekt **CloudQueue** .

    ```csharp
    queue.Delete();
    ```

1. Zaktualizuj **ViewBag** z nazwą kolejki i jej długością.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **kolejki**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **DeleteQueue** w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `DeleteQueue.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Pobierz długość kolejki** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Usuń kolejkę](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Rozpoczynanie pracy z usługą Azure Table Storage i usługami połączonymi programu Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)

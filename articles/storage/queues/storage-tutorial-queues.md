---
title: Samouczek — współpraca z kolejkami usługi Azure Storage — Azure Storage
description: Samouczek dotyczący sposobu używania usługa kolejki platformy Azure do tworzenia kolejek oraz wstawiania, pobierania i usuwania komunikatów.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75968196"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Samouczek: współpraca z kolejkami usługi Azure Storage

Usługa Azure queue storage implementuje kolejki oparte na chmurze, aby umożliwić komunikację między składnikami aplikacji rozproszonej. Każda kolejka przechowuje listę komunikatów, które mogą zostać dodane przez składnik nadawcy i przetworzone przez składnik odbiornika. W przypadku kolejki aplikacja może skalować się natychmiast w celu spełnienia wymagań. W tym artykule przedstawiono podstawowe kroki umożliwiające pracę z kolejką usługi Azure Storage.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Storage
> - Tworzymy aplikację.
> - Dodawanie obsługi kodu asynchronicznego
> - Tworzenie kolejki
> - Wstawianie komunikatów do kolejki
> - Dequeueing messages
> - Usuń pustą kolejkę
> - Sprawdzanie argumentów wiersza polecenia
> - Kompilowanie i uruchamianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz bezpłatną kopię wieloplatformowego edytora [Visual Studio Code](https://code.visualstudio.com/download) .
- Pobierz i zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).
- Jeśli nie masz bieżącej subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw utwórz konto usługi Azure Storage. Aby zapoznać się z przewodnikiem krok po kroku dotyczącym tworzenia konta magazynu, zobacz [Tworzenie konta magazynu](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) — Szybki Start.

## <a name="create-the-app"></a>Tworzymy aplikację.

Utwórz aplikację platformy .NET Core o nazwie **QueueApp**. Dla uproszczenia ta aplikacja wysyła i odbiera wiadomości za pomocą kolejki.

1. W oknie konsoli programu (na przykład CMD, PowerShell lub interfejsu wiersza `dotnet new` polecenia platformy Azure) za pomocą narzędzia Utwórz nową aplikację konsolową o nazwie **QueueApp**. To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Przejdź do nowo utworzonego folderu **QueueApp** i skompiluj aplikację, aby sprawdzić, czy wszystko jest prawidłowo.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Powinny być widoczne wyniki podobne do następujących:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Dodawanie obsługi kodu asynchronicznego

Ponieważ aplikacja korzysta z zasobów w chmurze, kod jest uruchamiany asynchronicznie. Jednak **Async** i **await** języka C# nie były Prawidłowymi słowami kluczowymi w metodach **głównych** do momentu C# 7,1. Możesz łatwo przełączyć się na ten kompilator za pomocą flagi w pliku **csproj** .

1. W wierszu polecenia w katalogu projektu wpisz `code .` , aby otworzyć Visual Studio Code w bieżącym katalogu. Pozostaw otwarte okno wiersza polecenia. Będzie więcej poleceń do wykonania później. Jeśli zostanie wyświetlony monit o dodanie zasobów C# wymaganych do kompilowania i debugowania, kliknij przycisk **tak** .

2. Otwórz plik **QueueApp.csproj** w edytorze.

3. Dodaj `<LangVersion>7.1</LangVersion>` do pierwszej **Właściwości** w pliku kompilacji. Upewnij się, że dodajesz tag **LangVersion** tylko wtedy, gdy **TargetFramework** może się różnić w zależności od zainstalowanej wersji platformy .NET.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Zapisz plik **QueueApp. csproj** .

5. Otwórz plik źródłowy **program.cs** i zaktualizuj metodę **Main** , aby działała asynchronicznie. Zastąp wartość **void** wartością zwracaną przez **zadanie asynchroniczne** .

   ```csharp
   static async Task Main(string[] args)
   ```

6. Zapisz plik **program.cs** .

## <a name="create-a-queue"></a>Tworzenie kolejki

1. Zainstaluj pakiety **Microsoft. Azure. Storage. Common** i **Microsoft. Azure. Storage. Queue** w projekcie za pomocą `dotnet add package` polecenia. Wykonaj następujące polecenia dotnet z folderu projektu w oknie konsoli.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. W górnej części pliku **program.cs** Dodaj następujące przestrzenie nazw bezpośrednio po `using System;` instrukcji. Ta aplikacja używa typów z tych przestrzeni nazw do nawiązywania połączenia z usługą Azure Storage i pracy z kolejkami.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Zapisz plik **program.cs** .

### <a name="get-your-connection-string"></a>Pobieranie parametrów połączenia

Biblioteka klienta do nawiązania połączenia używa parametrów połączenia. Parametry połączenia są dostępne w sekcji **Ustawienia** konta magazynu w Azure Portal.

1. W przeglądarce internetowej Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. W witrynie Azure Portal przejdź do swojego konta magazynu.

3. Wybierz pozycję **klucze dostępu**.

4. Kliknij przycisk **Kopiuj** po prawej stronie pola **Parametry połączenia** .

![Parametry połączenia](media/storage-tutorial-queues/get-connection-string.png)

Parametry połączenia mają następujący format:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Dodaj parametry połączenia do aplikacji

Dodaj parametry połączenia do aplikacji, aby mogły uzyskać dostęp do konta magazynu.

1. Przełącz się z powrotem do Visual Studio Code.

2. W klasie **program** Dodaj `private const string connectionString =` członka do przechowywania parametrów połączenia.

3. Po znaku równości wklej wartość ciągu, która została skopiowana wcześniej w Azure Portal. Wartość **ConnectionString** będzie unikatowa dla Twojego konta.

4. Usuń kod "Hello world" z **głównego**. Kod powinien wyglądać podobnie do poniższego, ale z unikatową wartością parametrów połączenia.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Zaktualizuj element **Main** , aby utworzyć obiekt **CloudQueue** , który jest później przesyłany do metod Send i Receive.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Zapisz plik.

## <a name="insert-messages-into-the-queue"></a>Wstawianie komunikatów do kolejki

Utwórz nową metodę wysyłania komunikatu do kolejki. Dodaj następującą metodę do klasy **programu** . Ta metoda pobiera odwołanie do kolejki, a następnie tworzy nową kolejkę, jeśli jeszcze nie istnieje, wywołując [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Następnie dodaje komunikat do kolejki przez wywołanie [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Dodaj następującą metodę **SendMessageAsync** do klasy **programu** .

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Zapisz plik.

Wiadomość musi być w formacie, który można uwzględnić w żądaniu XML z kodowaniem UTF-8, a jego maksymalny rozmiar może wynosić maksymalnie 64 KB. Jeśli komunikat zawiera dane binarne, zalecamy kodowanie Base64 wiadomości.

Domyślnie maksymalny czas wygaśnięcia komunikatu wynosi 7 dni. Można określić dowolną liczbę dodatnią dla komunikatu czas wygaśnięcia. Aby dodać komunikat, który nie wygasa, użyj `Timespan.FromSeconds(-1)` w wywołaniu metody **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Dequeueing messages

Utwórz nową metodę o nazwie **ReceiveMessageAsync**. Ta metoda odbiera komunikat z kolejki przez wywołanie [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Po pomyślnym odebraniu wiadomości należy usunąć ją z kolejki, aby nie została przetworzona więcej niż jeden raz. Po odebraniu wiadomości usuń ją z kolejki, wywołując [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Dodaj następującą metodę **ReceiveMessageAsync** do klasy **programu** .

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Zapisz plik.

## <a name="delete-an-empty-queue"></a>Usuń pustą kolejkę

Jest to najlepsze rozwiązanie na końcu projektu, aby określić, czy nadal potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Jeśli kolejka istnieje, ale jest pusta, poproszenie użytkownika o jej usunięcie.

1. Rozwiń metodę **ReceiveMessageAsync** , aby dołączyć monit o usunięcie pustej kolejki.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Zapisz plik.

## <a name="check-for-command-line-arguments"></a>Sprawdzanie argumentów wiersza polecenia

Jeśli do aplikacji są przekazane wszystkie argumenty wiersza polecenia, założono, że są one komunikatem, który ma zostać dodany do kolejki. Dołącz argumenty ze sobą, aby utworzyć ciąg. Dodaj ten ciąg do kolejki komunikatów, wywołując metodę **SendMessageAsync** , która została dodana wcześniej.

Jeśli nie ma argumentów wiersza polecenia, wykonaj operację pobierania. Wywołaj metodę **ReceiveMessageAsync** , aby pobrać pierwszą wiadomość w kolejce.

Na koniec poczekaj na wprowadzenie danych przez użytkownika przed wyjściem przez wywołanie **konsoli. ReadLine**.

1. Rozwiń metodę **Main** w celu sprawdzenia argumentów wiersza polecenia i poczekaj na wprowadzenie danych przez użytkownika.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Zapisz plik.

## <a name="complete-code"></a>Kompletny kod

Oto kompletna lista kodu dla tego projektu.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. W wierszu polecenia w katalogu projektu uruchom następujące polecenie dotnet, aby skompilować projekt.

   ```console
   dotnet build
   ```

2. Po pomyślnym skompilowaniu projektu uruchom następujące polecenie, aby dodać pierwszy komunikat do kolejki.

   ```console
   dotnet run First queue message
   ```

Powinny być widoczne następujące dane wyjściowe:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Uruchom aplikację bez argumentów wiersza polecenia, aby odebrać i usunąć pierwszą wiadomość w kolejce.

   ```console
   dotnet run
   ```

4. Kontynuuj uruchamianie aplikacji, dopóki nie zostaną usunięte wszystkie komunikaty. Jeśli uruchomisz go jeszcze raz, otrzymasz komunikat informujący, że kolejka jest pusta, oraz monit o usunięcie kolejki.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

1. Tworzenie kolejki
2. Dodawanie i usuwanie komunikatów z kolejki
3. Usuwanie kolejki usługi Azure Storage

Aby uzyskać więcej informacji, zapoznaj się z przewodnikiem Szybki Start dla kolejki platformy Azure.

> [!div class="nextstepaction"]
> [Kolejki — Szybki Start](storage-quickstart-queues-portal.md)

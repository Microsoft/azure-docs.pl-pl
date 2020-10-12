---
title: Samouczek — współpraca z kolejkami usługi Azure Storage w środowisku .NET
description: Samouczek dotyczący sposobu używania usługa kolejki platformy Azure do tworzenia kolejek oraz wstawiania, pobierania i usuwania komunikatów przy użyciu kodu platformy .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 23684dbbc5cb8c2d5fc4880ae8fe1999450928e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400574"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>Samouczek: współpraca z kolejkami usługi Azure Storage na platformie .NET

Usługa Azure queue storage implementuje kolejki oparte na chmurze, aby umożliwić komunikację między składnikami aplikacji rozproszonej. Każda kolejka przechowuje listę komunikatów, które mogą zostać dodane przez składnik nadawcy i przetworzone przez składnik odbiornika. W przypadku kolejki aplikacja może skalować się natychmiast w celu spełnienia wymagań. W tym artykule przedstawiono podstawowe kroki umożliwiające pracę z kolejką usługi Azure Storage.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie konta usługi Azure Storage
> - Tworzenie aplikacji
> - Dodawanie bibliotek klienckich platformy Azure
> - Dodawanie obsługi kodu asynchronicznego
> - Tworzenie kolejki
> - Wstawianie komunikatów do kolejki
> - Dequeueing messages
> - Usuń pustą kolejkę
> - Sprawdzanie argumentów wiersza polecenia
> - Skompiluj i uruchom aplikację

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz bezpłatną kopię wieloplatformowego edytora [Visual Studio Code](https://code.visualstudio.com/download) .
- Pobierz i zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download) w wersji 3,1 lub nowszej.
- Jeśli nie masz bieżącej subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw utwórz konto usługi Azure Storage. Aby zapoznać się z przewodnikiem krok po kroku dotyczącym tworzenia konta magazynu, zobacz [Tworzenie konta magazynu](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) — Szybki Start. Jest to osobny krok wykonywany po utworzeniu bezpłatnego konta platformy Azure w ramach wymagań wstępnych.

## <a name="create-the-app"></a>Tworzenie aplikacji

Utwórz aplikację platformy .NET Core o nazwie **QueueApp**. Dla uproszczenia ta aplikacja wysyła i odbiera wiadomości za pomocą kolejki.

1. W oknie konsoli programu (na przykład CMD, PowerShell lub interfejsu wiersza polecenia platformy Azure) za pomocą narzędzia `dotnet new` Utwórz nową aplikację konsolową o nazwie **QueueApp**. To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program.cs**.

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
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-the-azure-client-libraries"></a>Dodawanie bibliotek klienckich platformy Azure

1. Dodaj biblioteki klienta usługi Azure Storage do projektu za pomocą `dotnet add package` polecenia.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   Wykonaj następujące polecenie w folderze projektu w oknie konsoli.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   Wykonaj następujące polecenia w folderze projektu w oknie konsoli.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Dodaj instrukcje using

1. W wierszu polecenia w katalogu projektu wpisz, `code .` Aby otworzyć Visual Studio Code w bieżącym katalogu. Pozostaw otwarte okno wiersza polecenia. Będzie więcej poleceń do wykonania później. Jeśli zostanie wyświetlony monit o dodanie zasobów C# wymaganych do kompilowania i debugowania, kliknij przycisk **tak** .

1. Otwórz plik źródłowy **program.cs** i Dodaj następujące przestrzenie nazw bezpośrednio po `using System;` instrukcji. Ta aplikacja używa typów z tych przestrzeni nazw do nawiązywania połączenia z usługą Azure Storage i pracy z kolejkami.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Zapisz plik **Program.cs**.

## <a name="add-support-for-asynchronous-code"></a>Dodawanie obsługi kodu asynchronicznego

Ponieważ aplikacja korzysta z zasobów w chmurze, kod jest uruchamiany asynchronicznie.

1. Zaktualizuj metodę **Main** , aby uruchamiała się asynchronicznie. Zastąp wartość **void** wartością zwracaną przez **zadanie asynchroniczne** .

   ```csharp
   static async Task Main(string[] args)
   ```

1. Zapisz plik **Program.cs**.

## <a name="create-a-queue"></a>Tworzenie kolejki

Przed wprowadzeniem jakichkolwiek wywołań do interfejsów API platformy Azure należy uzyskać poświadczenia z Azure Portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Dodaj parametry połączenia do aplikacji

Dodaj parametry połączenia do aplikacji, aby mogły uzyskać dostęp do konta magazynu.

1. Przełącz się z powrotem do Visual Studio Code.

1. W metodzie **Main**  Zastąp `Console.WriteLine("Hello World!");` kod następującym wierszem, który pobiera parametry połączenia ze zmiennej środowiskowej.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Dodaj następujący kod do elementu **Main** , aby utworzyć obiekt kolejki, który jest później przesyłany do metod wysyłania i odbierania.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Zapisz plik.

## <a name="insert-messages-into-the-queue"></a>Wstawianie komunikatów do kolejki

Utwórz nową metodę wysyłania komunikatu do kolejki.

1. Dodaj następującą metodę **InsertMessageAsync** do klasy **programu** .

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   Ta metoda jest przenoszona odwołanie do kolejki. Zostanie utworzona nowa kolejka, jeśli jeszcze nie istnieje, przez wywołanie [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync). Następnie dodaje *newMessage* do kolejki przez wywołanie [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   Ta metoda jest przenoszona odwołanie do kolejki. Zostanie utworzona nowa kolejka, jeśli jeszcze nie istnieje, przez wywołanie [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Następnie dodaje *newMessage* do kolejki przez wywołanie [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Opcjonalne** Domyślnie maksymalna wartość czasu wygaśnięcia dla wiadomości wynosi siedem dni. Można określić dowolną liczbę dodatnią dla komunikatu czas wygaśnięcia. Poniższy fragment kodu dodaje komunikat, który *nigdy nie* wygasa.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

    Aby dodać komunikat, który nie wygasa, użyj `Timespan.FromSeconds(-1)` w wywołaniu metody **SendMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

    Aby dodać komunikat, który nie wygasa, użyj `Timespan.FromSeconds(-1)` w wywołaniu metody **AddMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Zapisz plik.

Komunikat kolejki musi być w formacie zgodnym z żądaniem XML przy użyciu kodowania UTF-8. Komunikat może mieć rozmiar nawet 64 KB. Jeśli komunikat zawiera dane binarne, Koduj komunikat w [formacie base64](/dotnet/api/system.convert.tobase64string) .

## <a name="dequeue-messages"></a>Dequeueing messages

Utwórz nową metodę, aby pobrać komunikat z kolejki. Po pomyślnym odebraniu wiadomości należy usunąć ją z kolejki, aby nie została przetworzona więcej niż jeden raz.

1. Dodaj nową metodę o nazwie **RetrieveNextMessageAsync** do klasy **programu** .

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   Ta metoda odbiera komunikat z kolejki przez wywołanie [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync), przekazanie 1 w pierwszym parametrze w celu pobrania tylko następnej wiadomości w kolejce. Po odebraniu wiadomości usuń ją z kolejki, wywołując [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync).

   Gdy wiadomość jest wysyłana do kolejki z wersją zestawu SDK wcześniejszą niż V12, jest automatycznie zakodowana w formacie base64. Począwszy od V12, że funkcje zostały usunięte. Po pobraniu komunikatu przy użyciu zestawu V12 SDK nie jest automatycznie dekodowany algorytmem Base64. Musisz jawnie [zdekodować zawartość w formacie base64](/dotnet/api/system.convert.frombase64string) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   Ta metoda odbiera komunikat z kolejki przez wywołanie [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Po odebraniu wiadomości usuń ją z kolejki, wywołując [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Zapisz plik.

## <a name="delete-an-empty-queue"></a>Usuń pustą kolejkę

Jest to najlepsze rozwiązanie na końcu projektu, aby określić, czy nadal potrzebujesz utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Jeśli kolejka istnieje, ale jest pusta, poproszenie użytkownika o jej usunięcie.

1. Rozwiń metodę **RetrieveNextMessageAsync** , aby dołączyć monit o usunięcie pustej kolejki.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Zapisz plik.

## <a name="check-for-command-line-arguments"></a>Sprawdzanie argumentów wiersza polecenia

Jeśli do aplikacji są przekazane wszystkie argumenty wiersza polecenia, założono, że są one komunikatem, który ma zostać dodany do kolejki. Dołącz argumenty ze sobą, aby utworzyć ciąg. Dodaj ten ciąg do kolejki komunikatów, wywołując metodę **InsertMessageAsync** , która została dodana wcześniej.

Jeśli nie ma argumentów wiersza polecenia, spróbuj wykonać operację pobierania. Wywołaj metodę **RetrieveNextMessageAsync** , aby pobrać następną wiadomość w kolejce.

Na koniec poczekaj na wprowadzenie danych przez użytkownika przed wyjściem przez wywołanie **konsoli. ReadLine**.

1. Rozwiń metodę **Main** w celu sprawdzenia argumentów wiersza polecenia i poczekaj na wprowadzenie danych przez użytkownika.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Zapisz plik.

## <a name="complete-code"></a>Kompletny kod

Oto kompletna lista kodu dla tego projektu.

   # <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

1. W wierszu polecenia w katalogu projektu uruchom następujące polecenie dotnet, aby skompilować projekt.

   ```console
   dotnet build
   ```

1. Po pomyślnym skompilowaniu projektu uruchom następujące polecenie, aby dodać pierwszy komunikat do kolejki.

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

1. Uruchom aplikację bez argumentów wiersza polecenia, aby odebrać i usunąć pierwszą wiadomość w kolejce.

   ```console
   dotnet run
   ```

1. Kontynuuj uruchamianie aplikacji, dopóki nie zostaną usunięte wszystkie komunikaty. Jeśli uruchomisz go jeszcze raz, otrzymasz komunikat informujący, że kolejka jest pusta, oraz monit o usunięcie kolejki.

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
1. Dodawanie i usuwanie komunikatów z kolejki
1. Usuwanie kolejki usługi Azure Storage

Aby uzyskać więcej informacji, zapoznaj się z przewodnikami szybki start dla kolejek platformy Azure.

> [!div class="nextstepaction"]
> [Kolejki szybkiego startu dla portalu](storage-quickstart-queues-portal.md)

- [Kolejki szybkiego startu dla platformy .NET](storage-quickstart-queues-dotnet.md)
- [Kolejki szybkiego startu dla języka Java](storage-quickstart-queues-java.md)
- [Kolejki szybkiego startu dla języka Python](storage-quickstart-queues-python.md)
- [Kolejki szybkiego startu dla języka JavaScript](storage-quickstart-queues-nodejs.md)

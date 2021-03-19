---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 2d6836b2bf667e4170e67a95dc1daad72a769eb9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183465"
---
### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

W programie Visual Studio utwórz nowy projekt **Aplikacja konsoli (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Dodawanie pakietu NuGet usługi Relay

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj pozycję **Microsoft.Azure.Relay**. W wynikach wyszukiwania wybierz pozycję **Microsoft Azure Relay**. 
3. Wybierz pozycję **Zainstaluj**, aby ukończyć instalację. Zamknij okno dialogowe.

### <a name="write-code-to-send-messages"></a>Pisanie kodu w celu wysyłania komunikatów

1. Na początku pliku Program.cs zastąp istniejące instrukcje `using` następującymi instrukcjami `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Dodaj stałe do klasy `Program` na potrzeby szczegółów połączenia hybrydowego. Zastąp symbole zastępcze w nawiasach wartościami uzyskanymi podczas tworzenia połączenia hybrydowego. Pamiętaj, aby użyć w pełni kwalifikowanej nazwy przestrzeni nazw.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Dodaj następującą metodę do klasy `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
        // Create a new hybrid connection client.
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Initiate the connection.
        var relayConnection = await client.CreateConnectionAsync();
   
        // Run two concurrent loops on the connection. One 
        // reads input from the console and writes it to the connection 
        // with a stream writer. The other reads lines of input from the 
        // connection with a stream reader and writes them to the console. 
        // Entering a blank line shuts down the write task after 
        // sending it to the server. The server then cleanly shuts down
        // the connection, which terminates the read task.
   
        var reads = Task.Run(async () => {
            // Initialize the stream reader over the connection.
            var reader = new StreamReader(relayConnection);
            var writer = Console.Out;
            do
            {
                // Read a full line of UTF-8 text up to newline.
                string line = await reader.ReadLineAsync();
                // If the string is empty or null, you are done.
                if (String.IsNullOrEmpty(line))
                    break;
                // Write to the console.
                await writer.WriteLineAsync(line);
            }
            while (true);
        });
   
        // Read from the console and write to the hybrid connection.
        var writes = Task.Run(async () => {
            var reader = Console.In;
            var writer = new StreamWriter(relayConnection) { AutoFlush = true };
            do
            {
                // Read a line from the console.
                string line = await reader.ReadLineAsync();
                // Write the line out, also when it's empty.
                await writer.WriteLineAsync(line);
                // Quit when the line is empty,
                if (String.IsNullOrEmpty(line))
                    break;
            }
            while (true);
        });
   
        // Wait for both tasks to finish.
        await Task.WhenAll(reads, writes);
        await relayConnection.CloseAsync(CancellationToken.None);
    }
    ```
4. Dodaj następujący wiersz kodu do metody `Main` w klasie `Program`.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Plik Program.cs powinien wyglądać następująco:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
                // Create a new hybrid connection client.
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Initiate the connection.
                var relayConnection = await client.CreateConnectionAsync();
   
                // Run two concurrent loops on the connection. One 
                // reads input from the console and then writes it to the connection 
                // with a stream writer. The other reads lines of input from the 
                // connection with a stream reader and then writes them to the console. 
                // Entering a blank line shuts down the write task after 
                // sending it to the server. The server then cleanly shuts down
                // the connection, which terminates the read task.
   
                var reads = Task.Run(async () => {
                    // Initialize the stream reader over the connection.
                    var reader = new StreamReader(relayConnection);
                    var writer = Console.Out;
                    do
                    {
                        // Read a full line of UTF-8 text up to newline.
                        string line = await reader.ReadLineAsync();
                        // If the string is empty or null, you are done.
                        if (String.IsNullOrEmpty(line))
                            break;
                        // Write to the console.
                        await writer.WriteLineAsync(line);
                    }
                    while (true);
                });
   
                // Read from the console and write to the hybrid connection.
                var writes = Task.Run(async () => {
                    var reader = Console.In;
                    var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                    do
                    {
                        // Read a line from the console.
                        string line = await reader.ReadLineAsync();
                        // Write the line out, also when it's empty.
                        await writer.WriteLineAsync(line);
                        // Quit when the line is empty.
                        if (String.IsNullOrEmpty(line))
                            break;
                    }
                    while (true);
                });
   
                // Wait for both tasks to finish.
                await Task.WhenAll(reads, writes);
                await relayConnection.CloseAsync(CancellationToken.None);
            }
        }
    }
    ```


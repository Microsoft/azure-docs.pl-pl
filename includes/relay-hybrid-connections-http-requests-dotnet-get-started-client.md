---
title: plik dołączany
description: plik dołączany
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ce29cd03de46e1d93d7f1f28f9f5184cd59a57e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79200341"
---
### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

Jeśli opcja „Wymaga autoryzacji klienta” została wyłączona podczas tworzenia usługi Relay, możesz wysyłać żądania na adres połączeń hybrydowych za pomocą dowolnej przeglądarki. Aby uzyskać dostęp do chronionych punktów końcowych, musisz utworzyć i przekazać token w nagłówku `ServiceBusAuthorization` pokazanym w tym miejscu.

W programie Visual Studio utwórz nowy projekt **Aplikacja konsoli (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Dodawanie pakietu NuGet usługi Relay

1. Kliknij prawym przyciskiem myszy nowo utworzony projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
2. Wybierz opcję **Uwzględnij wersję wstępną**. 
3. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj pozycję **Microsoft.Azure.Relay**. W wynikach wyszukiwania wybierz pozycję  **Microsoft Azure Relay**.
4. Wybierz wersję **2.0.0-preview1-20180523**. 
5. Wybierz pozycję **Zainstaluj**, aby ukończyć instalację. Zamknij okno dialogowe.

### <a name="write-code-to-send-requests"></a>Pisanie kodu do wysyłania żądań

1. Na początku pliku Program.cs zastąp istniejące instrukcje `using` następującymi instrukcjami `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
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
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());        Console.ReadLine();
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
    using System.Net.Http;
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
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```


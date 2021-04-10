---
title: Omówienie .NET Standard interfejsów API Azure Relay | Microsoft Docs
description: W tym artykule przedstawiono podsumowanie jednego z najważniejszych informacji o interfejsie API Azure Relay Połączenia hybrydowe .NET Standard.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625135"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Omówienie interfejsu API Azure Relay Połączenia hybrydowe .NET Standard

W tym artykule przedstawiono podsumowanie niektórych kluczowych Azure Relay Połączenia hybrydowe .NET Standard [interfejsów API klienta](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Klasa konstruktora parametrów połączenia przekaźnika

Klasa [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formatuje parametry połączenia specyficzne dla przekaźnika połączenia hybrydowe. Można go użyć do sprawdzenia formatu parametrów połączenia lub utworzenia parametrów połączenia od podstaw. Zapoznaj się z poniższym kodem na przykład:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Parametry połączenia można również przekazać bezpośrednio do `RelayConnectionStringBuilder` metody. Ta operacja umożliwia zweryfikowanie, czy parametry połączenia mają prawidłowy format. Jeśli którykolwiek z parametrów jest nieprawidłowy, Konstruktor generuje `ArgumentException` .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Strumień połączenia hybrydowego

Klasa [HybridConnectionStream][HCStream] jest obiektem podstawowym używanym do wysyłania i odbierania danych z punktu końcowego Azure Relay, bez względu na to, czy pracujesz z [HybridConnectionClient][HCClient], czy [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Pobieranie strumienia połączenia hybrydowego

#### <a name="listener"></a>Odbiornik

Przy użyciu obiektu [HybridConnectionListener][HCListener] można uzyskać `HybridConnectionStream` obiekt w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Klient

Przy użyciu obiektu [HybridConnectionClient][HCClient] można uzyskać `HybridConnectionStream` obiekt w następujący sposób:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Pobieranie danych

Klasa [HybridConnectionStream][HCStream] umożliwia komunikację dwukierunkową. W większości przypadków ciągle otrzymujesz ze strumienia. W przypadku odczytywania tekstu ze strumienia warto również użyć obiektu [StreamReader](/dotnet/api/system.io.streamreader) , który umożliwia łatwiejsze analizowanie danych. Można na przykład odczytywać dane jako tekst, a nie jako `byte[]` .

Poniższy kod odczytuje poszczególne wiersze tekstu ze strumienia do momentu żądania anulowania:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Wysyłanie danych

Po nawiązaniu połączenia można wysłać komunikat do punktu końcowego usługi Relay. Ponieważ obiekt Connection dziedziczy [strumień](/dotnet/api/system.io.stream), Wyślij dane jako `byte[]` . Poniższy przykład pokazuje, jak to zrobić:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Jeśli jednak chcesz wysłać tekst bezpośrednio, bez konieczności kodowania ciągu za każdym razem, możesz otoczyć `hybridConnectionStream` obiekt obiektem [StreamWriter —](/dotnet/api/system.io.streamwriter) .

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:

* [Dokumentacja Microsoft. Azure. Relay](/dotnet/api/microsoft.azure.relay)
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Dostępne interfejsy API przekazywania](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener

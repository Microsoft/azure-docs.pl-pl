---
ms.openlocfilehash: a055cc1b715f93830647c9b13793a59d09db605c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511289"
---
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji w języku C#

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `ManagedIdentitiesQuickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: `Program.cs` .

```console
dotnet new console -o ManagedIdentitiesQuickstartQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i użyj `dotnet build` polecenia , aby skompilować aplikację.

```console
cd ManagedIdentitiesQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>Instalowanie pakietów zestawu SDK

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Korzystanie z pakietów zestawu SDK

Dodaj następujące dyrektywy `using` do funkcji , aby używać tożsamości platformy Azure i zestawów SDK usługi Azure `Program.cs` Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>Tworzenie wartości domyślnejAzureCredential

W tym przewodniku Szybki start będziemy używać wartości [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) To poświadczenie jest odpowiednie dla środowisk produkcyjnych i deweloperskich. Ponieważ jest to potrzebne dla każdej operacji, utwórzmy ją w klasie `Program.cs` . Dodaj następujący kod na początku pliku.

```csharp
     private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-managed-identities"></a>Wydawanie tokenu przy użyciu tożsamości zarządzanych

Teraz dodamy kod, który używa utworzonego poświadczenia, aby wydać token dostępu VoIP. Ten kod wywołamy później.

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

## <a name="send-an-sms-with-managed-identities"></a>Wysyłanie wiadomości SMS przy użyciu tożsamości zarządzanych

Jako kolejny przykład użycia tożsamości zarządzanych dodamy ten kod, który używa tego samego poświadczenia do wysyłania wiadomości SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
     }
```

## <a name="write-the-main-method"></a>Pisanie metody Main

Twoja metoda powinna już mieć metodę Main. Dodajmy kod, który wywoła utworzony wcześniej kod, aby zademonstrować użycie `Program.cs` tożsamości zarządzanych:

```csharp
     static void Main(string[] args)
     {
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

          // We need an instance of the program class to use within this method.
          Program instance = new();

          Console.WriteLine("Retrieving new Access Token, using Managed Identities");
          Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
          Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

          Console.WriteLine("Sending SMS using Managed Identities");

          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Managed Identities");
          Console.WriteLine($"Sms id: {result.MessageId}");
          Console.WriteLine($"Send Result Successful: {result.Successful}");
     }
```

Ostateczny plik `Program.cs` powinien wyglądać tak:

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Managed Identities");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Managed Identities");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "+18445504651", "+14256253982", "Hello from Managed Identities");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>Uruchamianie programu

Teraz powinno być możliwe uruchomienie aplikacji przy użyciu `dotnet run` z folderu aplikacji. Dane wyjściowe powinny wyglądać następująco:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey....
Sending SMS using Managed Identities
Sms id: Outgoing_..._noam
Send Result Successful: True
```

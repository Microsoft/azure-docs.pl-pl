---
title: Nawiązywanie połączenia z interfejsem API Azure Media Services v3 — .NET
description: W tym artykule pokazano, jak nawiązać połączenie z interfejsem API Media Services v3 przy użyciu platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: b9b72e8340722f8ddc97b3769ce22e607974a508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297403"
---
# <a name="connect-to-media-services-v3-api---net"></a>Nawiązywanie połączenia z interfejsem API Media Services v3 — .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano sposób nawiązywania połączenia z zestawem SDK platformy .NET w wersji 3 Azure Media Services przy użyciu metody nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services
- Zainstaluj narzędzie, którego chcesz użyć na potrzeby programowania na platformie .NET. W krokach przedstawionych w tym artykule przedstawiono sposób korzystania z [programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Możesz użyć Visual Studio Code, zobacz [Praca z C#](https://code.visualstudio.com/docs/languages/csharp). Można też użyć innego edytora kodu.

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Uruchom program Visual Studio. 
1. W menu **plik** kliknij pozycję **Nowy**  >  **projekt**. 
1. Utwórz aplikację konsolową **.NET Core** .

Przykładowa aplikacja w tym temacie jest celem `netcoreapp2.0` . Kod używa metody "Async Main", która jest dostępna od języka C# 7,1. Aby uzyskać więcej informacji, zobacz ten [blog](/archive/blogs/benwilli/async-main-is-available-but-hidden) .

## <a name="add-required-nuget-packages"></a>Dodaj wymagane pakiety NuGet

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **NuGet Menedżer pakietów**NuGet  >  **konsola Menedżer**.
2. W oknie **konsola Menedżera pakietów** Użyj polecenia, `Install-Package` Aby dodać następujące pakiety NuGet. Na przykład `Install-Package Microsoft.Azure.Management.Media`.

|Pakiet|Opis|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Aby upewnić się, że używasz najnowszego pakietu Azure Media Services, sprawdź [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteka uwierzytelniania ADAL dla zestawu Azure SDK dla usługi NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Odczytywanie wartości konfiguracyjnych ze zmiennych środowiskowych i lokalnych plików JSON|
|`Microsoft.Extensions.Configuration.Json`|Odczytywanie wartości konfiguracyjnych ze zmiennych środowiskowych i lokalnych plików JSON
|`WindowsAzure.Storage`|Zestaw SDK magazynu|

## <a name="create-and-configure-the-app-settings-file"></a>Tworzenie i konfigurowanie pliku ustawień aplikacji

### <a name="create-appsettingsjson"></a>Utwórz appsettings.jsna

1. Przejdź do **General**  >  **pliku tekstowego**ogólnego.
1. Nadaj mu nazwę "appsettings.json".
1. Ustaw właściwość "Kopiuj do katalogu wyjściowego" pliku JSON na "Kopiuj, jeśli nowszy" (tak, aby aplikacja mogła uzyskać do niej dostęp po opublikowaniu).

### <a name="set-values-in-appsettingsjson"></a>Ustaw wartości w appsettings.jsna

Uruchom `az ams account sp create` polecenie zgodnie z opisem w temacie [interfejsy API dostępu](./access-api-howto.md). Polecenie zwraca kod JSON, który należy skopiować do "appsettings.json".
 
## <a name="add-configuration-file"></a>Dodawanie pliku konfiguracji

Dla wygody Dodaj plik konfiguracji, który jest odpowiedzialny za Odczytywanie wartości z "appsettings.json".

1. Dodaj nową klasę cs do projektu. Nadaj jej nazwę `ConfigWrapper`. 
1. Wklej następujący kod do tego pliku (w tym przykładzie założono, że masz przestrzeń nazw `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Nawiązywanie połączenia z klientem .NET

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W poniższym kodzie funkcja GetCredentialsAsync tworzy obiekt serviceclientcredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracyjnym.

1. Otwórz plik `Program.cs`.
1. Wklej następujący kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo — .NET](stream-files-tutorial-with-api.md) 
- [Samouczek: przesyłanie strumieniowe na żywo z Media Services v3 — .NET](stream-live-tutorial-with-api.md)
- [Samouczek: analizowanie filmów wideo za pomocą Media Services v3 — .NET](analyze-videos-tutorial-with-api.md)
- [Tworzenie danych wejściowych zadania z pliku lokalnego — .NET](job-input-from-local-file-how-to.md)
- [Tworzenie danych wejściowych zadania na podstawie adresu URL protokołu HTTPS — .NET](job-input-from-http-how-to.md)
- [Kodowanie za pomocą transformacji niestandardowej — .NET](customize-encoder-presets-how-to.md)
- [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy — .NET](protect-with-aes128.md)
- [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM — .NET](protect-with-drm.md)
- [Pobieranie klucza podpisywania z istniejących zasad — .NET](get-content-key-policy-dotnet-howto.md)
- [Tworzenie filtrów za pomocą usługi Media Services — .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Zaawansowane przykłady wideo na żądanie usługi Azure Functions v2 z usługą Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja platformy .NET](/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Aby uzyskać więcej przykładów kodu, zobacz repozytorium [przykładów zestawu .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) .

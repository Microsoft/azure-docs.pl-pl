---
title: Pisanie kodu uwierzytelniania aplikacji
titleSuffix: Azure Digital Twins
description: Zobacz jak pisać kod uwierzytelniania w aplikacji klienckiej
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 0438632a36fe14d35210cb5acb8d3a50d0f038b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767829"
---
# <a name="write-client-app-authentication-code"></a>Napisz kod uwierzytelniania aplikacji klienckiej

Po [skonfigurowaniu wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-portal.md)można utworzyć aplikację kliencką, która będzie używana do współpracy z wystąpieniem. Po skonfigurowaniu początkowego projektu klienta w tym artykule opisano **sposób pisania kodu w aplikacji klienckiej w celu uwierzytelnienia go** w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Istnieją dwa podejścia do przykładowego kodu w tym artykule. Możesz użyć tego, który jest odpowiedni dla Ciebie, w zależności od wybranego języka:
* Pierwsza sekcja przykładowego kodu używa zestawu Azure Digital bliźniaczych reprezentacji .NET (C#) SDK. Zestaw SDK jest częścią zestawu Azure SDK dla platformy .NET i znajduje się tutaj: [*Biblioteka kliencka Digital bliźniaczych usługi Azure IoT dla platformy .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Obsługiwane są również zestawy SDK dla [języka Java](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0-beta.1/jar ) i [języka JavaScript](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1), które mogą być używane w podobny sposób.
* Druga sekcja przykładowego kodu dotyczy użytkowników, którzy nie korzystają z podanego zestawu SDK, a zamiast tego używają zestawów SDK generowanych automatycznie w innych językach. Aby uzyskać więcej informacji na temat tej strategii, zobacz [*How to: Create Custom SDK for Azure Digital bliźniaczych reprezentacji with AutoRest*](how-to-create-custom-sdks.md).

Więcej informacji na temat interfejsów API i zestawów SDK dla usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*How to: use Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Wymagania wstępne

Najpierw wykonaj kroki instalacji opisane w temacie [*How to: Set a instance and Authentication*](how-to-set-up-instance-portal.md). Zapewni to, że masz wystąpienie usługi Azure Digital bliźniaczych reprezentacji, użytkownik ma uprawnienia dostępu i ustawił uprawnienia dla aplikacji klienckich. Po skonfigurowaniu tej konfiguracji możesz przystąpić do pisania kodu aplikacji klienta.

Aby można było wykonać operację, potrzebny będzie projekt aplikacji klienckiej, w którym napisano kod. Jeśli nie masz jeszcze skonfigurowanego projektu aplikacji klienckiej, Utwórz podstawowy projekt w wybranym języku, który ma być używany z tym samouczkiem.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Uwierzytelnianie i tworzenie klienta: zestaw SDK platformy .NET (C#)

W tej sekcji przedstawiono przykład w języku C# dotyczący użycia podanego zestawu .NET SDK.

Najpierw należy uwzględnić następujące pakiety w projekcie, aby można było użyć zestawu .NET SDK i narzędzi do uwierzytelniania:
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

W zależności od wybranych narzędzi można dołączyć pakiety przy użyciu Menedżera pakietów programu Visual Studio lub `dotnet` narzędzia wiersza polecenia. 

Potrzebne są również następujące instrukcje using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
Aby uwierzytelnić się za pomocą zestawu .NET SDK, użyj jednej z metod uzyskiwania poświadczeń, które są zdefiniowane w bibliotece [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) . Poniżej przedstawiono dwa, które są często używane (nawet razem w tej samej aplikacji):

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) jest przeznaczony dla aplikacji interaktywnych i może służyć do tworzenia uwierzytelnionego klienta SDK
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) działa doskonale w przypadkach, gdy potrzebne są tożsamości zarządzane (msi), i jest dobrym kandydatem do pracy z Azure Functions

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential, Metoda
Metoda [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) jest przeznaczona dla aplikacji interaktywnych i umożliwia wyświetlenie przeglądarki sieci Web w celu uwierzytelnienia.

Aby użyć poświadczeń interaktywnej przeglądarki do utworzenia uwierzytelnionego klienta zestawu SDK, Dodaj następujący kod:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Identyfikator klienta, identyfikator dzierżawy i adres URL wystąpienia można umieścić bezpośrednio w kodzie jak pokazano powyżej. dobrym pomysłem jest, że kod pobiera te wartości z pliku konfiguracji lub zmiennej środowiskowej.

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential, Metoda
 Metoda [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) działa dobrze w przypadkach, gdy potrzebne są [tożsamości zarządzane (msi)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— na przykład podczas pracy z Azure Functions.
W funkcji platformy Azure można użyć poświadczeń tożsamości zarządzanej, takich jak:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Zapoznaj się z tematem [*jak to zrobić: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md) w celu przeprowadzenia pełniejszego przykładu, w którym objaśniono niektóre ważne opcje konfiguracji w kontekście funkcji.

Ponadto, aby użyć uwierzytelniania w funkcji, pamiętaj, aby:
* [Włączanie tożsamości zarządzanej](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Użyj [zmiennych środowiskowych](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) stosownie do potrzeb
* Przypisz uprawnienia do aplikacji funkcji, która umożliwia jej dostęp do interfejsów API Digital bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat Azure Functions procesów, zobacz [*How to: set up a Azure Function for processing Data*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Uwierzytelnianie za pomocą automatycznie wygenerowanego zestawu SDK

Jeśli nie używasz jednego z dostarczonych zestawów SDK (.NET, Java, JavaScript), możesz wybrać bibliotekę zestawu SDK w wybranym przez siebie języku, zgodnie z opisem w temacie [*How to: Create Custom SDK for Azure Digital bliźniaczych reprezentacji with AutoRest*](how-to-create-custom-sdks.md).

W tej sekcji opisano sposób uwierzytelniania w tym przypadku.

### <a name="prerequisites"></a>Wymagania wstępne

Najpierw należy wykonać kroki w celu utworzenia niestandardowego zestawu SDK z funkcją AutoRest, wykonując kroki opisane w temacie [*How to: Create Custom SDKs for Azure Digital bliźniaczych reprezentacji with AutoRest*](how-to-create-custom-sdks.md).

W tym przykładzie używa się zestawu TypeScript SDK wygenerowanego za pomocą AutoRest. W związku z tym wymaga również:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Przykładowy kod uwierzytelniania minimalnego

Aby uwierzytelnić aplikację przy użyciu usług platformy Azure, możesz użyć następującego minimalnego kodu w aplikacji klienckiej.

Wymagana jest nazwa *identyfikatora aplikacji (klienta)* i *Identyfikator katalogu (dzierżawa)* ze starszej wersji, a także adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta*cyfrowego wystąpienia bliźniaczych reprezentacji platformy Azure. Aby wyświetlić *nazwę hosta*wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . Możesz użyć polecenia, `az account show --query tenantId` Aby zobaczyć identyfikator Twojego *katalogu (dzierżawy)*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Należy pamiętać, że w przypadku, gdy kod powyżej umieszcza identyfikator klienta, identyfikator dzierżawy i adres URL wystąpienia bezpośrednio w kodzie dla uproszczenia, dobrym pomysłem jest, że kod pobiera te wartości z pliku konfiguracji lub zmiennej środowiskowej.

MSAL ma wiele opcji, których można użyć, aby zaimplementować elementy, takie jak buforowanie i inne przepływy uwierzytelniania. Aby uzyskać więcej informacji na ten temat, zobacz [*Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa zabezpieczenia w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)

Lub teraz, gdy uwierzytelnianie jest skonfigurowane, przejdź do w celu utworzenia modeli w wystąpieniu:
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)
---
title: Uwierzytelnianie aplikacji klienckiej
titleSuffix: Azure Digital Twins
description: Zobacz jak uwierzytelnić aplikację kliencką w usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 89de2e86e425e2c585f9a1243f6666a0ae077c10
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84726093"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Uwierzytelnianie aplikacji klienckiej za pomocą usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Po [utworzeniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance.md)można utworzyć aplikację kliencką, która będzie używana do współpracy z wystąpieniem. Po skonfigurowaniu początkowego projektu klienta w tym artykule opisano sposób prawidłowego uwierzytelniania tej aplikacji klienckiej za pomocą wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

W tym celu należy wykonać dwie czynności:
1. Tworzenie rejestracji aplikacji
2. Pisanie kodu uwierzytelniania w aplikacji klienckiej

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Tworzenie rejestracji aplikacji

Aby uwierzytelnić się w usłudze Azure Digital bliźniaczych reprezentacji z poziomu aplikacji klienckiej, należy skonfigurować **rejestrację aplikacji** w [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Ta rejestracja aplikacji to miejsce, w którym można skonfigurować uprawnienia dostępu do [interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-use-apis-sdks.md). Aplikacja kliencka jest uwierzytelniana względem rejestracji aplikacji i w wyniku przyznania skonfigurowanych uprawnień dostępu do interfejsów API.

Aby utworzyć rejestrację aplikacji, należy podać identyfikatory zasobów dla interfejsów API Digital bliźniaczych reprezentacji systemu Azure oraz uprawnienia linii bazowej do interfejsu API. W katalogu roboczym Otwórz nowy plik, a następnie wprowadź Poniższy fragment kodu JSON, aby skonfigurować następujące szczegóły: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Zapisz ten plik jako *manifest.js*.

> [!NOTE] 
> Istnieją pewne miejsca, w których "przyjazny", czytelny dla użytkownika ciąg `https://digitaltwins.azure.net` może być używany dla identyfikatora aplikacji Digital bliźniaczych reprezentacji zasobów platformy Azure zamiast identyfikatora GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Na przykład wiele przykładów w tym zestawie dokumentacji używa uwierzytelniania z biblioteką MSAL, a dla tego można użyć przyjaznego ciągu. Jednak w tym kroku tworzenia rejestracji aplikacji identyfikator GUID identyfikatora jest wymagany, tak jak pokazano powyżej. 

W oknie Cloud Shell kliknij ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Okno Cloud Shell pokazujące wybór opcji przekazywania":::
Przejdź do *manifest.js* po utworzeniu i naciśnij pozycję "Otwórz".

Następnie uruchom następujące polecenie, aby utworzyć rejestrację aplikacji (zastępując symbole zastępcze w razie potrzeby):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Dane wyjściowe tego polecenia wyglądają następująco.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Rejestracja nowej aplikacji usługi AAD":::

Po utworzeniu rejestracji aplikacji Skorzystaj z [tego linku](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , aby przejść do strony Przegląd rejestracji aplikacji usługi AAD w Azure Portal.

Z tego omówienia wybierz właśnie utworzoną rejestrację aplikacji z listy. Spowoduje to otwarcie szczegółowych informacji na stronie podobnej do tej:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: identyfikatory uwierzytelniania":::

Zanotuj *Identyfikator* *aplikacji (klienta)* na stronie **użytkownika** . Te wartości zostaną użyte później do uwierzytelnienia aplikacji klienckiej względem interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

> [!NOTE]
> W zależności od danego scenariusza może zajść potrzeba wprowadzenia dodatkowych zmian w rejestracji aplikacji. Poniżej przedstawiono niektóre typowe wymagania, które należy spełnić:
> * Aktywuj dostęp klienta publicznego
> * Ustawianie określonych adresów URL odpowiedzi dla dostępu do sieci Web i pulpitu
> * Zezwalaj na niejawne przepływy uwierzytelniania OAuth2
> * Jeśli subskrypcja platformy Azure została utworzona przy użyciu konto Microsoft takiej jak Live, Xbox lub Hotmail, musisz ustawić *signInAudience* w rejestracji aplikacji, aby obsługiwała konta osobiste.
> Najprostszym sposobem skonfigurowania tych ustawień jest użycie [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji o tym procesie, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Napisz kod uwierzytelniania aplikacji klienckiej: .NET (C#) SDK

W tej sekcji opisano kod, który należy uwzględnić w aplikacji klienckiej w celu ukończenia procesu uwierzytelniania przy użyciu zestawu SDK platformy .NET (C#).
ZESTAW SDK platformy Azure Digital bliźniaczych reprezentacji C# jest częścią zestawu Azure SDK dla platformy .NET. Znajduje się tutaj: [Biblioteka klienta usługi Azure IoT Digital bliźniaczy dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz już skonfigurowanego projektu aplikacji klienckiej Starter, Utwórz podstawowy projekt platformy .NET do użycia z tym samouczkiem.

Aby można było korzystać z zestawu SDK platformy .NET, w projekcie należy uwzględnić następujące pakiety:
* `Azure.DigitalTwins.Core`(wersja `1.0.0-preview.2` )
* `Azure.Identity`

W zależności od wybranych narzędzi możesz to zrobić za pomocą Menedżera pakietów programu Visual Studio lub `dotnet` narzędzia wiersza polecenia. 

### <a name="authentication-and-client-creation-net"></a>Uwierzytelnianie i tworzenie klienta: .NET

Aby uwierzytelnić się za pomocą zestawu .NET SDK, użyj jednej z metod uzyskiwania poświadczeń, które są zdefiniowane w bibliotece [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) .

Poniżej przedstawiono dwa często używane: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Ta metoda jest przeznaczona dla aplikacji interaktywnych i umożliwia wyświetlenie przeglądarki sieci Web na potrzeby uwierzytelniania.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Ta metoda działa dobrze w przypadkach, gdy potrzebne są [tożsamości zarządzane (msi)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— na przykład podczas pracy z Azure Functions. 

Potrzebne są również następujące instrukcje using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

W funkcji platformy Azure można następnie użyć poświadczeń tożsamości zarządzanej, takich jak:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Zapoznaj się z tematem [jak to zrobić: Konfigurowanie funkcji platformy Azure do przetwarzania danych](how-to-create-azure-function.md) w celu przeprowadzenia pełniejszego przykładu, w którym objaśniono niektóre ważne opcje konfiguracji w kontekście funkcji.

Ponadto, aby użyć uwierzytelniania w funkcji, pamiętaj, aby:
* [Włączanie tożsamości zarządzanej](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Zmienne środowiskowe](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Przypisz uprawnienia do aplikacji funkcji, która umożliwia jej dostęp do interfejsów API Digital bliźniaczych reprezentacji. Zobacz [instrukcje: Konfigurowanie funkcji platformy Azure do przetwarzania danych,](how-to-create-azure-function.md) Aby uzyskać więcej informacji.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Uwierzytelnianie w zestawie SDK wygenerowany automatycznie

Jeśli nie korzystasz z platformy .NET, możesz zdecydować się na utworzenie biblioteki zestawu SDK w wybranym języku, zgodnie z opisem w temacie [How to: Create Custom SDK for Azure Digital bliźniaczych reprezentacji with AutoRest](how-to-create-custom-sdks.md).

W tej sekcji opisano sposób uwierzytelniania w tym przypadku.

### <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie używa się zestawu TypeScript SDK wygenerowanego za pomocą AutoRest. W związku z tym wymaga również:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Przykładowy kod uwierzytelniania minimalnego

Aby uwierzytelnić aplikację platformy .NET za pomocą usług platformy Azure, możesz użyć następującego minimalnego kodu w aplikacji klienckiej.

Wymagana jest nazwa *identyfikatora aplikacji (klienta)* i *Identyfikator katalogu (dzierżawa)* ze starszej wersji, a także adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta*cyfrowego wystąpienia bliźniaczych reprezentacji platformy Azure. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

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

MSAL ma wiele opcji, których można użyć, aby zaimplementować elementy, takie jak buforowanie i inne przepływy uwierzytelniania. Aby uzyskać więcej informacji na ten temat, zobacz [Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa zabezpieczenia w usłudze Azure Digital bliźniaczych reprezentacji:
* [Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure](concepts-security.md)

Lub teraz, gdy uwierzytelnianie jest skonfigurowane, przejdź do w celu utworzenia modeli w wystąpieniu:
* [Instrukcje: Zarządzanie modelem bliźniaczym](how-to-manage-model.md)
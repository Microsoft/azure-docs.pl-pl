---
title: Używanie tożsamości zarządzanej z aplikacją
description: Jak uzyskać dostęp do usług platformy Azure za pomocą zarządzanych tożsamości w usłudze Azure Service Fabric kodzie aplikacji.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: e26a29020f26583f7e4aa16434c7e8647ba9a5a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98871065"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Jak korzystać z zarządzanej tożsamości aplikacji Service Fabric w celu uzyskiwania dostępu do usług platformy Azure

Aplikacje Service Fabric mogą korzystać z zarządzanych tożsamości w celu uzyskiwania dostępu do innych zasobów platformy Azure, które obsługują uwierzytelnianie oparte na Azure Active Directory. Aplikacja może uzyskać [token dostępu](../active-directory/develop/developer-glossary.md#access-token) reprezentujący jego tożsamość, która może być przypisana przez system lub przypisany przez użytkownika, i użyć go jako tokenu "okaziciela", aby uwierzytelnić się w innej usłudze — znanej także jako [chroniony serwer zasobów](../active-directory/develop/developer-glossary.md#resource-server). Token reprezentuje tożsamość przypisaną do aplikacji Service Fabric i zostanie wystawiony tylko dla zasobów platformy Azure (w tym aplikacji SF), które współużytkują tę tożsamość. Zapoznaj się z dokumentacją [zarządzaną tożsamości](../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać szczegółowy opis tożsamości zarządzanych, a także różnice między tożsamościami przypisanymi do systemu i przypisanymi przez użytkownika. W tym artykule odnosimy się do aplikacji Service Fabric z włączoną obsługą tożsamości zarządzanej jako [aplikacji klienckiej](../active-directory/develop/developer-glossary.md#client-application) .

Zapoznaj się z przykładową aplikacją towarzyszącą, która ilustruje użycie przypisanych do systemu i przypisanych przez użytkownika [Service Fabric tożsamości zarządzanych](https://github.com/Azure-Samples/service-fabric-managed-identity) za pomocą Reliable Services i kontenerów.

> [!IMPORTANT]
> Zarządzana tożsamość reprezentuje skojarzenie między zasobem platformy Azure i jednostką usługi w odpowiedniej dzierżawie Azure AD skojarzonym z subskrypcją zawierającą zasób. W związku z tym w kontekście Service Fabric zarządzane tożsamości są obsługiwane tylko w przypadku aplikacji wdrożonych jako zasoby platformy Azure. 

> [!IMPORTANT]
> Przed użyciem zarządzanej tożsamości aplikacji Service Fabric, aplikacja kliencka musi mieć udzielony dostęp do chronionego zasobu. Zapoznaj się z listą [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) , aby sprawdzić pomoc techniczną, a następnie do dokumentacji odpowiedniej usługi, aby uzyskać dostęp do tożsamości do interesujących Cię zasobów. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Wykorzystanie tożsamości zarządzanej przy użyciu platformy Azure. Identity

Zestaw SDK tożsamości platformy Azure obsługuje teraz Service Fabric. Przy użyciu platformy Azure. tożsamość ułatwia pisanie kodu, aby ułatwić korzystanie z Service Fabric tożsamości zarządzanych aplikacji, ponieważ obsługuje pobieranie tokenów, tokenów buforowania i uwierzytelnianie serwera. Podczas uzyskiwania dostępu do większości zasobów platformy Azure pojęcie tokenu jest ukryte.

Obsługa Service Fabric jest dostępna w następujących wersjach dla następujących języków: 
- [C# w wersji 1.3.0](https://www.nuget.org/packages/Azure.Identity). Zobacz [przykład w języku C#](https://github.com/Azure-Samples/service-fabric-managed-identity).
- Środowisko [Python w wersji 1.5.0](https://pypi.org/project/azure-identity/). Zobacz przykład w języku [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Język Java w wersji 1.2.0](/java/api/overview/azure/identity-readme).

Przykład w języku C# dotyczący inicjowania poświadczeń i korzystania z poświadczeń w celu pobrania klucza tajnego z Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API REST
W klastrach obsługujących tożsamość zarządzaną środowisko uruchomieniowe Service Fabric udostępnia punkt końcowy localhost, którego aplikacje mogą używać do uzyskiwania tokenów dostępu. Punkt końcowy jest dostępny w każdym węźle klastra i jest dostępny dla wszystkich jednostek w tym węźle. Autoryzowane obiekty wywołujące mogą uzyskać tokeny dostępu przez wywołanie tego punktu końcowego i przedprezentowanie kodu uwierzytelniania; kod jest generowany przez środowisko uruchomieniowe Service Fabric dla każdej odrębnej aktywacji pakietu kodu usługi i jest powiązany z okresem istnienia procesu obsługującego ten pakiet kodu usługi.

W odniesieniu do środowiska usługi Service Fabric z włączoną obsługą tożsamości zarządzanej jest docelowa następująca zmienna:
- "IDENTITY_ENDPOINT": punkt końcowy localhost odpowiadający tożsamości zarządzanej przez usługę
- "IDENTITY_HEADER": unikatowy kod uwierzytelniania reprezentujący usługę w bieżącym węźle
- "IDENTITY_SERVER_THUMBPRINT": odcisk palca zarządzanego serwera tożsamości usługi Service Fabric

> [!IMPORTANT]
> Kod aplikacji powinien uwzględniać wartość zmiennej środowiskowej "IDENTITY_HEADER" jako poufne dane — nie należy jej rejestrować ani rozpowszechniać w inny sposób. Kod uwierzytelniania nie ma wartości poza węzłem lokalnym lub po zakończeniu procesu obsługującego usługę, ale reprezentuje tożsamość usługi Service Fabric i dlatego powinien być traktowany z tymi samymi środkami ostrożności co sam token dostępu.

Aby uzyskać token, Klient wykonuje następujące czynności:
- tworzy identyfikator URI przez połączenie punktu końcowego tożsamości zarządzanej (IDENTITY_ENDPOINT wartość) z wersją interfejsu API i zasobem (odbiorcy) wymaganym dla tokenu
- tworzy żądanie pobrania http (s) dla określonego identyfikatora URI
- dodaje odpowiedni logiki walidacji certyfikatu serwera
- dodaje kod uwierzytelniania (IDENTITY_HEADER wartość) jako nagłówek żądania
- przesyła żądanie

Pomyślna odpowiedź będzie zawierać ładunek JSON reprezentujący otrzymany token dostępu, a także metadane opisujące go. Odpowiedź zakończona niepowodzeniem również zawiera wyjaśnienie błędu. Dodatkowe szczegóły dotyczące obsługi błędów znajdują się poniżej.

Tokeny dostępu będą buforowane przez Service Fabric na różnych poziomach (węzeł, klaster, usługa dostawcy zasobów), dlatego pomyślna odpowiedź nie musi oznaczać, że token został wystawiony bezpośrednio w odpowiedzi na żądanie aplikacji użytkownika. Tokeny będą przechowywane w pamięci podręcznej przez czas krótszy niż ich okres istnienia, więc aplikacja zostanie zagwarantowana, aby otrzymać prawidłowy token. Zaleca się, aby kod aplikacji buforuje wszystkie tokeny dostępu, które uzyskują. klucz buforowania powinien obejmować odbiorców. 

Przykładowe żądanie:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
gdzie:

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazujący, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Zarządzany punkt końcowy tożsamości dla aplikacji Service Fabric udostępniany za pośrednictwem zmiennej środowiskowej IDENTITY_ENDPOINT. |
| `api-version` | Parametr ciągu zapytania, określający wersję interfejsu API usługi zarządzanego tokenu tożsamości; obecnie jedyną akceptowaną wartością jest `2019-07-01-preview` i może ulec zmiana. |
| `resource` | Parametr ciągu zapytania, wskazujący identyfikator URI aplikacji dla zasobu docelowego. Zostanie to odzwierciedlone jako zbiór `aud` odbiorców dla wystawionego tokenu. Ten przykład żąda tokenu, aby uzyskać dostęp do Azure Key Vault, którego identyfikator URI aplikacji to https: \/ /Vault.Azure.NET/. |
| `Secret` | Pole nagłówka żądania HTTP wymagane przez usługę Service Fabric zarządzanym tokenem tożsamości dla usług Service Fabric do uwierzytelniania obiektu wywołującego. Ta wartość jest dostarczana przez środowisko uruchomieniowe SF za pośrednictwem zmiennej środowiskowej IDENTITY_HEADER. |


Przykładowa odpowiedź:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
gdzie:

| Element | Opis |
| ------- | ----------- |
| `token_type` | Typ tokenu; w tym przypadku token dostępu "Bearer", który oznacza, że prezenter ("Bearer") tego tokenu jest zamierzonym tematem tokenu. |
| `access_token` | Żądany token dostępu. Podczas wywoływania bezpiecznego interfejsu API REST token jest osadzony w `Authorization` polu nagłówka żądania jako token "Bearer", dzięki czemu interfejs API może uwierzytelniać obiekt wywołujący. | 
| `expires_on` | Sygnatura czasowa wygaśnięcia tokenu dostępu; reprezentowane jako liczba sekund od "1970-01-01T0:0: 0Z UTC" i odpowiada na `exp` żądania tokenu. W tym przypadku token wygasa w dniu 2019-08-08T06:10:11 + 00:00 (w dokumencie RFC 3339)|
| `resource` | Zasób, dla którego został wystawiony token dostępu, określony za pomocą `resource` parametru ciągu zapytania żądania; odnosi się do roszczeń "AUD" tokenu. |


## <a name="acquiring-an-access-token-using-c"></a>Uzyskiwanie tokenu dostępu przy użyciu języka C #
Powyższe polecenie zmieni się w języku C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Uzyskiwanie dostępu do Key Vault z aplikacji Service Fabric przy użyciu tożsamości zarządzanej
Ten przykład kompiluje się w powyższej wersji, aby przedstawić dostęp do wpisu tajnego przechowywanego w Key Vault przy użyciu tożsamości zarządzanej.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Obsługa błędów
Pole "kod stanu" w nagłówku odpowiedzi HTTP wskazuje stan powodzenia żądania; stan "200 OK" oznacza powodzenie, a odpowiedź będzie zawierać token dostępu, zgodnie z powyższym opisem. Poniżej przedstawiono krótkie Wyliczenie możliwych odpowiedzi na błędy.

| Kod stanu | Przyczyna błędu | Jak obsłużyć |
| ----------- | ------------ | ------------- |
| nie znaleziono 404. | Nieznany kod uwierzytelniania lub aplikacja nie ma przypisanej tożsamości zarządzanej. | Należy skorygować konfigurację aplikacji lub kod pozyskiwania tokenu. |
| 429 zbyt wiele żądań. |  Osiągnięto limit ograniczania przepustowości, narzucone przez AAD lub SF. | Ponów próbę, używając wykładniczej wycofywania. Zobacz wskazówki poniżej. |
| 4xx Błąd w żądaniu. | Co najmniej jeden z parametrów żądania był niepoprawny. | Nie ponawiaj próby.  Aby uzyskać więcej informacji, zapoznaj się ze szczegółami błędu.  Błędy 4xx są błędy czasu projektowania.|
| 5xx błąd z usługi. | Podsystem tożsamości zarządzanej lub Azure Active Directory zwrócił błąd przejściowy. | Można bezpiecznie ponowić próbę po krótkim czasie. Przed ponowną próbą można napotkać warunek ograniczenia przepustowości (429).|

Jeśli wystąpi błąd, odpowiadająca treść odpowiedzi HTTP zawiera obiekt JSON z szczegółowymi informacjami o błędzie:

| Element | Opis |
| ------- | ----------- |
| kod | Kod błędu. |
| correlationId | Identyfikator korelacji, który może być używany do debugowania. |
| message | Pełny opis błędu. **Opis błędów można zmienić w dowolnym momencie. Nie zależą od samego komunikatu o błędzie.**|

Błąd próbki:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Poniżej znajduje się lista typowych błędów Service Fabric związanych z tożsamościami zarządzanymi:

| Kod | Komunikat | Opis | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Nie znaleziono wpisu tajnego w nagłówkach żądania. | Kod uwierzytelniania nie został dostarczony wraz z żądaniem. | 
| ManagedIdentityNotFound | Nie znaleziono tożsamości zarządzanej dla określonego hosta aplikacji. | Aplikacja nie ma tożsamości lub kod uwierzytelniania jest nieznany. |
| ArgumentNullOrEmpty | Parametr "Resource" nie może mieć wartości null ani być pustym ciągiem. | W żądaniu nie podano zasobu (odbiorcy). |
| InvalidApiVersion | Wersja interfejsu API "" nie jest obsługiwana. Obsługiwana wersja to "2019-07-01-Preview". | W identyfikatorze URI żądania określono brakującą lub nieobsługiwaną wersję interfejsu API. |
| InternalServerError | Wystąpił błąd. | Wystąpił błąd w zarządzanym podsystemie tożsamości, prawdopodobnie poza stosem Service Fabric. Najbardziej prawdopodobną przyczyną jest niepoprawna wartość określona dla zasobu (Sprawdź, czy kończy się znakiem "/"?) | 

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zazwyczaj jedynym powtarzanym kodem błędu jest 429 (zbyt wiele żądań); wewnętrzne błędy serwera/5xx kody błędów mogą być ponawiane, chociaż Przyczyna może być trwała. 

Limity ograniczania mają zastosowanie do liczby wywołań w podsystemie tożsamości zarządzanej — w odniesieniu do zależności "nadrzędnych" (usługi tożsamości zarządzanej platformy Azure lub usługi bezpiecznego tokenu). Service Fabric buforuje tokeny na różnych poziomach potoku, ale z uwzględnieniem rozproszonego charakteru powiązanych składników, wywołujący może wystąpić niespójne odpowiedzi dotyczące ograniczania przepustowości (tj. uzyskać ograniczenia dotyczące jednego węzła/wystąpienia aplikacji, ale nie w innym węźle podczas żądania tokenu dla tej samej tożsamości). Po ustawieniu warunku ograniczania kolejne żądania z tej samej aplikacji mogą kończyć się niepowodzeniem z kodem stanu HTTP 429 (zbyt wiele żądań) do momentu wyczyszczenia warunku.  

Zaleca się, aby żądania nie powiodły się, ponieważ trwa ponawianie próby ograniczenia przy użyciu wykładniczej wycofywania w następujący sposób: 

| Indeks wywołań | Akcja przy odbiorze 429 | 
| --- | --- | 
| 1 | Poczekaj 1 sekunda i ponów próbę |
| 2 | Odczekaj 2 sekundy i ponów próbę |
| 3 | Poczekaj 4 sekundy i ponów próbę |
| 4 | Odczekaj 8 sekund i ponów próbę |
| 4 | Odczekaj 8 sekund i ponów próbę |
| 5 | Odczekaj 16 sekund i ponów próbę |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure
Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) , aby uzyskać listę zasobów, które obsługują usługę Azure AD, oraz ich identyfikatory zasobów.

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
* [Eksplorowanie przykładowej aplikacji przy użyciu Service Fabric tożsamości zarządzanej](https://github.com/Azure-Samples/service-fabric-managed-identity)
---
title: Używanie tożsamości zarządzanej za pomocą aplikacji
description: Jak używać zarządzanych tożsamości w kodzie aplikacji usługi Azure Service Fabric, aby uzyskać dostęp do usług platformy Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 8f1f355d6add16f3b3ec25bc569f9b198a8d6778
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461569"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Jak wykorzystać tożsamość zarządzaną aplikacji sieci szkieletowej usług w celu uzyskania dostępu do usług platformy Azure

Aplikacje sieci szkieletowej usług mogą korzystać z zarządzanych tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, które obsługują uwierzytelnianie oparte na usłudze Azure Active Directory. Aplikacja może uzyskać [token dostępu reprezentujący](../active-directory/develop/developer-glossary.md#access-token) jego tożsamość, który może być przypisany do systemu lub przypisany przez użytkownika, i używać go jako tokenu "nośnika" do uwierzytelniania się w innej usłudze — znanej również jako [serwer chronionych zasobów](../active-directory/develop/developer-glossary.md#resource-server). Token reprezentuje tożsamość przypisaną do aplikacji sieci szkieletowej usług i zostanie wystawiony tylko do zasobów platformy Azure (w tym aplikacji SF), które współużytkują tę tożsamość. Szczegółowe opis tożsamości zarządzanych można znaleźć w dokumentacji [przeglądu tożsamości zarządzanej,](../active-directory/managed-identities-azure-resources/overview.md) a także w rozróżnieniu między tożsamościami przypisanymi przez system i tożsamościami przypisanymi przez użytkownika. Będziemy odwoływać się do aplikacji sieci szkieletowej usługi z obsługą tożsamości zarządzanej jako [aplikacji klienckiej](../active-directory/develop/developer-glossary.md#client-application) w tym artykule.

> [!IMPORTANT]
> Tożsamość zarządzana reprezentuje skojarzenie między zasobem platformy Azure a jednostką usługi w odpowiedniej dzierżawie usługi Azure AD skojarzonej z subskrypcją zawierającą zasób. W związku z tym w kontekście sieci szkieletowej usług tożsamości zarządzane są obsługiwane tylko dla aplikacji wdrożonych jako zasoby platformy Azure. 

> [!IMPORTANT]
> Przed użyciem tożsamości zarządzanej aplikacji sieci szkieletowej usług aplikacja kliencka musi uzyskać dostęp do chronionego zasobu. Zapoznaj się z listą [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) aby sprawdzić dostępność pomocy technicznej, a następnie do dokumentacji odpowiedniej usługi, aby uzyskać określone kroki, aby udzielić dostępu do tożsamości interesujących zasobów. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Pobieranie tokenu dostępu przy użyciu interfejsu API REST
W klastrach włączonych dla tożsamości zarządzanej środowisko uruchomieniowe sieci szkieletowej sieci szkieletowej udostępnia punkt końcowy localhost, którego aplikacje mogą używać do uzyskiwania tokenów dostępu. Punkt końcowy jest dostępny w każdym węźle klastra i jest dostępny dla wszystkich jednostek w tym węźle. Autoryzowani wywoływanie mogą uzyskać tokeny dostępu, wywołując ten punkt końcowy i przedstawiając kod uwierzytelniania; kod jest generowany przez środowisko uruchomieniowe sieci szkieletowej usług dla każdej aktywacji pakietu kodu usługi i jest powiązany z okresem istnienia procesu hostingu tego pakietu kodu usługi.

W szczególności środowisko usługi sieci szkieletowej usług z obsługą tożsamości zarządzanej zostanie rozstawione z następującymi zmiennymi:
- "IDENTITY_ENDPOINT": punkt końcowy localhost odpowiadający tożsamości zarządzanej usługi
- "IDENTITY_HEADER": unikatowy kod uwierzytelniania reprezentujący usługę w bieżącym węźle
- "IDENTITY_SERVER_THUMBPRINT": Odcisk palca serwera tożsamości zarządzanej przez sieci szkieletowe usług

> [!IMPORTANT]
> Kod aplikacji powinien uwzględniać wartość zmiennej środowiskowej "IDENTITY_HEADER" jako dane wrażliwe - nie powinna być rejestrowana ani w inny sposób rozpowszechniana. Kod uwierzytelniania nie ma żadnej wartości poza węzłem lokalnym lub po zakończeniu procesu hostingu usługi, ale reprezentuje tożsamość usługi sieci szkieletowej usług, a więc powinien być traktowany z tymi samymi środkami ostrożności, co sam token dostępu.

Aby uzyskać token, klient wykonuje następujące kroki:
- tworzy identyfikator URI przez łączenie zarządzanego punktu końcowego tożsamości (IDENTITY_ENDPOINT wartość) z wersją interfejsu API i zasobem (odbiorcami) wymaganym dla tokenu
- tworzy żądanie GET http(s) dla określonego identyfikatora URI
- dodaje odpowiednią logikę sprawdzania poprawności certyfikatu serwera
- dodaje kod uwierzytelniania (IDENTITY_HEADER wartość) jako nagłówek do żądania
- składa wniosek

Pomyślna odpowiedź będzie zawierać ładunek JSON reprezentujący wynikowy token dostępu, a także metadane opisujące go. Odpowiedź nie powiodła się będzie również zawierać wyjaśnienie błędu. Poniżej znajdziesz dodatkowe informacje na temat obsługi błędów.

Tokeny dostępu będą buforowane przez sieci szkieletowej usług na różnych poziomach (węzeł, klaster, usługa dostawcy zasobów), więc pomyślna odpowiedź nie musi oznaczać, że token został wystawiony bezpośrednio w odpowiedzi na żądanie aplikacji użytkownika. Tokeny będą buforowane przez mniej niż ich okres istnienia, a więc aplikacja jest gwarantowana, aby otrzymać prawidłowy token. Zaleca się, że kod aplikacji buforuje się wszelkie tokeny dostępu, które uzyskuje; klucz buforowania powinien obejmować (wyprowadzenie) odbiorców. 

Przykładowe żądanie:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
gdzie:

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP, wskazując, że chcesz pobrać dane z punktu końcowego. W takim przypadku token dostępu OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Punkt końcowy tożsamości zarządzanej dla aplikacji sieci szkieletowej usług, pod warunkiem za pośrednictwem zmiennej środowiskowej IDENTITY_ENDPOINT. |
| `api-version` | Parametr ciągu kwerendy, określający wersję interfejsu API usługi tokenu tożsamości zarządzanej; obecnie jedyną akceptowaną `2019-07-01-preview`wartością jest i może ulec zmianie. |
| `resource` | Parametr ciągu kwerendy wskazujący identyfikator URI identyfikatora aplikacji zasobu docelowego. Zostanie to odzwierciedlone jako `aud` (odbiorcy) roszczenie wydanego tokenu. W tym przykładzie żąda tokenu dostępu do usługi Azure Key\/Vault, którego identyfikator URI identyfikatora aplikacji jest https: /vault.azure.net/. |
| `Secret` | Pole nagłówka żądania HTTP, wymagane przez usługę tokenu tożsamości zarządzanej sieci szkieletowej usług sieci szkieletowej usług dla sieci szkieletowej usług do uwierzytelniania wywołującego. Ta wartość jest dostarczana przez środowisko uruchomieniowe SF za pośrednictwem zmiennej środowiskowej IDENTITY_HEADER. |


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
| `token_type` | Typ tokenu; w takim przypadku token dostępu "Nośnik", co oznacza, że prezenter ("okaziciela") tego tokenu jest zamierzonym przedmiotem tokenu. |
| `access_token` | Żądany token dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST `Authorization` token jest osadzony w polu nagłówka żądania jako token "nośnik", umożliwiając interfejsowi API uwierzytelnienie wywołującego. | 
| `expires_on` | Sygnatura czasowa wygaśnięcia tokenu dostępu; reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" i odpowiada `exp` żądaniu tokenu. W takim przypadku token wygasa w dniu 2019-08-08T06:10:11+00:00 (w RFC 3339)|
| `resource` | Zasób, dla którego został wystawiony `resource` token dostępu, określony za pomocą parametru ciągu zapytania żądania; odpowiada twierdzeniu "aud" tokenu. |


## <a name="acquiring-an-access-token-using-c"></a>Pobieranie tokenu dostępu przy użyciu języka C #
Powyższe staje się w języku C#:

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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Uzyskiwanie dostępu do magazynu kluczy z aplikacji sieci szkieletowej usług przy użyciu tożsamości zarządzanej
Ten przykład opiera się na powyższe, aby zademonstrować dostęp do klucza tajnego przechowywanego w magazynie kluczy przy użyciu tożsamości zarządzanej.

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
Pole "kod stanu" nagłówka odpowiedzi HTTP wskazuje stan sukcesu żądania; stan "200 OK" wskazuje na powodzenie, a odpowiedź będzie zawierać token dostępu, jak opisano powyżej. Poniżej przedstawiono krótkie wyliczenie możliwych odpowiedzi na błędy.

| Kod stanu | Przyczyna błędu | Jak sobie radzić |
| ----------- | ------------ | ------------- |
| 404 Nie znaleziono. | Nieznany kod uwierzytelniania lub aplikacji nie przypisano tożsamości zarządzanej. | Sprostowanie kodu instalacji aplikacji lub nabycia tokenu. |
| 429 Zbyt wiele żądań. |  Osiągnięto limit przepustnicy, narzucony przez AAD lub SF. | Ponów próbę przy czym przy próbie przywrócania wykładniczego. Zapoznaj się z poniższymi wskazówkami. |
| 4xx Błąd w żądaniu. | Jeden lub więcej parametrów żądania było niepoprawne. | Nie należy ponowiać próby.  Sprawdź szczegóły błędu, aby uzyskać więcej informacji.  Błędy 4xx to błędy w czasie projektowania.|
| 5xx Błąd z serwisu. | Podsystem tożsamości zarządzanej lub usługi Azure Active Directory zwrócił błąd przejściowy. | Po krótkim czasie można ponowić próbę. Po ponowieniu próby może zostać na trafienie warunku ograniczania przepustowości (429).|

Jeśli wystąpi błąd, odpowiednia treść odpowiedzi HTTP zawiera obiekt JSON ze szczegółami błędu:

| Element | Opis |
| ------- | ----------- |
| kod | Kod błędu. |
| correlationId | Identyfikator korelacji, który może służyć do debugowania. |
| message | Pełny opis błędu. **Opisy błędów mogą ulec zmianie w dowolnym momencie. Nie należy polegać na samym komunikacie o błędzie.**|

Przykładowy błąd:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Poniżej znajduje się lista typowych błędów sieci szkieletowej usług specyficznych dla tożsamości zarządzanych:

| Code | Komunikat | Opis | 
| ----------- | ----- | ----------------- |
| Odnaleźnie SecretHeaderNotfound | Klucz tajny nie został znaleziony w nagłówkach żądań. | Kod uwierzytelniania nie został dostarczony wraz z żądaniem. | 
| ManagedIdentityNotFound (Zarządzany Niedowianie) | Nie znaleziono tożsamości zarządzanej dla określonego hosta aplikacji. | Aplikacja nie ma tożsamości lub kod uwierzytelniania jest nieznany. |
| ArgumentNullOrEmpty | Parametr "resource" nie powinien mieć wartości null ani pustego ciągu. | Zasób (odbiorcy) nie został dostarczony w żądaniu. |
| InvalidApiVersion (Wersja inwersji) | Wersja interfejsu API '' nie jest obsługiwana. Obsługiwana wersja to '2019-07-01-preview'. | Brak lub nieobsługiwała wersji interfejsu API określona w identyfikatorze URI żądania. |
| InternalServerError | Wystąpił błąd. | Wystąpił błąd w podsystemie tożsamości zarządzanej, prawdopodobnie poza stosem sieci szkieletowej usług. Najbardziej prawdopodobną przyczyną jest niepoprawna wartość określona dla zasobu (sprawdź, czy na końcu '/'?) | 

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zazwyczaj tylko ponowialny kod błędu jest 429 (zbyt wiele żądań); wewnętrzne błędy serwera/kody błędów 5xx mogą być ponawiane, chociaż przyczyna może być trwała. 

Limity ograniczania dotyczą liczby wywołań wykonanych do podsystemu tożsamości zarządzanej — w szczególności zależności "nadrzędne" (usługa Managed Identity Azure lub usługa bezpiecznego tokenu). Sieci szkieletowej usług buforuje tokeny na różnych poziomach w potoku, ale biorąc pod uwagę rozproszony charakter zaangażowanych składników, obiektu wywołującego może wystąpić niespójne odpowiedzi ograniczania przepustowości (tj. uzyskać ograniczone w jednym węźle/wystąpieniu aplikacji, ale nie w innym węźle podczas żądania tokenu dla tej samej tożsamości.) Po ustawieniu warunku ograniczania przepustowości kolejne żądania z tej samej aplikacji mogą zakończyć się niepowodzeniem przy użyciu kodu stanu HTTP 429 (zbyt wiele żądań), dopóki warunek nie zostanie wyczyszczony.  

Zaleca się, że żądania nie powiodło się z powodu ograniczania są ponowione z wykładniczym backoff, w następujący sposób: 

| Indeks połączeń | Działanie w sprawie otrzymania 429 | 
| --- | --- | 
| 1 | Odczekaj 1 sekundę i ponów próbę |
| 2 | Odczekaj 2 sekundy i ponów próbę |
| 3 | Odczekaj 4 sekundy i ponów próbę |
| 4 | Odczekaj 8 sekund i ponów próbę |
| 4 | Odczekaj 8 sekund i ponów próbę |
| 5 | Odczekaj 16 sekund i ponów próbę |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure
Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD,](../active-directory/managed-identities-azure-resources/services-support-msi.md) aby uzyskać listę zasobów obsługujących usługę Azure AD i ich odpowiednie identyfikatory zasobów.

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udzielanie dostępu aplikacji sieci szkieletowej usługi Azure do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)

---
title: Uzyskiwanie tokenu dostępu przy użyciu tożsamości zarządzanych na maszynie wirtualnej — Azure AD
description: Instrukcje krok po kroku i przykłady użycia tożsamości zarządzanych dla zasobów platformy Azure na maszynach wirtualnych w celu uzyskania tokenu dostępu OAuth.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee7739d9dbfd34190dc1e856b98fdd21be15743
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364944"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie. 

Ten artykuł zawiera różne przykłady kodu i skryptów służące do pozyskiwania tokenów, a także wskazówki dotyczące ważnych tematów, takich jak obsługa wygaśnięcia tokenu i błędów HTTP. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów Azure PowerShell w tym artykule, pamiętaj o zainstalowaniu najnowszej wersji programu [Azure PowerShell.](/powershell/azure/install-az-ps)


> [!IMPORTANT]
> - We wszystkich przykładowych kodach/skryptach w tym artykule przyjęto założenie, że klient jest uruchomiony na maszynie wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w Azure Portal, aby zdalnie nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej, zobacz Configure [managed identities for Azure resources on a VM using the Azure Portal](qs-configure-portal-windows-vm.md)(Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy użyciu programu Azure Portal) lub jeden z wariantów artykułów (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu Azure SDK). 

> [!IMPORTANT]
> - Granica zabezpieczeń tożsamości zarządzanych dla zasobów platformy Azure to zasób, na którym jest on używany. Cały kod/skrypty uruchomione na maszynie wirtualnej mogą żądać i pobierać tokeny dla wszystkich dostępnych na nim tożsamości zarządzanych. 

## <a name="overview"></a>Omówienie

Aplikacja kliency może zażądać tożsamości zarządzanych dla tokenu dostępu tylko do aplikacji zasobów platformy Azure [w](../develop/developer-glossary.md#access-token) celu uzyskania dostępu do danego zasobu. Token jest oparty [na tożsamościach zarządzanych dla jednostki usługi zasobów platformy Azure.](overview.md#managed-identity-types) W związku z tym klient nie musi się rejestrować, aby uzyskać token dostępu w ramach własnej jednostki usługi. Token jest odpowiedni do użycia jako token okaziciela w wywołaniach [service-to-service wymagających poświadczeń klienta](../develop/v2-oauth2-client-creds-grant-flow.md).

| Link | Opis |
| -------------- | -------------------- |
| [Uzyskiwanie tokenu przy użyciu protokołu HTTP](#get-a-token-using-http) | Szczegóły protokołu dla tożsamości zarządzanych dla punktu końcowego tokenu zasobów platformy Azure |
| [Uzyskiwanie tokenu przy użyciu biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Przykład użycia biblioteki Microsoft.Azure.Services.AppAuthentication z klienta platformy .NET
| [Uzyskiwanie tokenu przy użyciu języka C #](#get-a-token-using-c) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta języka C# |
| [Uzyskiwanie tokenu przy użyciu języka Java](#get-a-token-using-java) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta Java |
| [Uzyskiwanie tokenu przy użyciu go](#get-a-token-using-go) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta go |
| [Uzyskiwanie tokenu przy użyciu Azure PowerShell](#get-a-token-using-azure-powershell) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta programu PowerShell |
| [Uzyskiwanie tokenu przy użyciu programu CURL](#get-a-token-using-curl) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta Bash/CURL |
| Obsługa buforowania tokenów | Wskazówki dotyczące obsługi wygasłych tokenów dostępu |
| [Obsługa błędów](#error-handling) | Wskazówki dotyczące obsługi błędów HTTP zwróconych z tożsamości zarządzanych dla punktu końcowego tokenu zasobów platformy Azure |
| [Identyfikatory zasobów dla usług platformy Azure](#resource-ids-for-azure-services) | Gdzie można uzyskać identyfikatory zasobów dla obsługiwanych usług platformy Azure |

## <a name="get-a-token-using-http"></a>Uzyskiwanie tokenu przy użyciu protokołu HTTP 

Podstawowy interfejs uzyskiwania tokenu dostępu jest oparty na interfejsie REST, dzięki czemu jest dostępny dla dowolnej aplikacji klienckiej uruchomionej na maszynie wirtualnej, która może wysyłać wywołania REST protokołu HTTP. Jest to podobne do modelu programowania usługi Azure AD, z tą różnicą, że klient używa punktu końcowego na maszynie wirtualnej (a nie punktu końcowego usługi Azure AD).

Przykładowe żądanie korzystające z punktu końcowego usługi Azure Instance Metadata Service (IMDS) *(zalecane):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazujący, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Tożsamości zarządzane dla punktu końcowego zasobów platformy Azure dla Instance Metadata Service. |
| `api-version`  | Parametr ciągu zapytania wskazujący wersję interfejsu API dla punktu końcowego IMDS. Użyj interfejsu API w `2018-02-01` wersji lub większej. |
| `resource` | Parametr ciągu zapytania wskazujący identyfikator URI identyfikatora aplikacji zasobu docelowego. Pojawia się również w oświadczenie `aud` (odbiorcy) wystawionego tokenu. W tym przykładzie żąda tokenu dostępu Azure Resource Manager, który ma identyfikator URI identyfikatora aplikacji `https://management.azure.com/` . |
| `Metadata` | Pole nagłówka żądania HTTP wymagane przez tożsamości zarządzane dla zasobów platformy Azure jako środki zaradcze przed atakiem fałszowania żądań po stronie serwera (SSRF). Ta wartość musi być ustawiona na "true", we wszystkich małe literach. |
| `object_id` | (Opcjonalnie) Parametr ciągu zapytania wskazujący, object_id tożsamości zarządzanej, dla których chcesz uzyskać token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu zapytania wskazujący, client_id tożsamości zarządzanej, dla których chcesz uzyskać token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `mi_res_id` | (Opcjonalnie) Parametr ciągu zapytania wskazujący, mi_res_id (identyfikator zasobu platformy Azure) tożsamości zarządzanej, dla których chcesz uzyskać token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika. |

Przykładowa odpowiedź:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Opis |
| ------- | ----------- |
| `access_token` | Żądany token dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST token jest osadzony w polu nagłówka żądania jako token "bearer", co umożliwia interfejsowi API uwierzytelnianie `Authorization` obiektu wywołującego. | 
| `refresh_token` | Nie są używane przez tożsamości zarządzane dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund, przez które token dostępu będzie nadal ważny (przed wygaśnięciem) od czasu wystawienia. Czas wystawiania można znaleźć w oświadczenie `iat` tokenu. |
| `expires_on` | Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada oświadczenia `exp` tokenu). |
| `not_before` | Okres, w którym token dostępu jest obowiązywać i można go zaakceptować. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada oświadczenia `nbf` tokenu). |
| `resource` | Zasób, dla którego zażądano tokenu dostępu, pasuje do `resource` parametru ciągu zapytania żądania. |
| `token_type` | Typ tokenu, który jest tokenem dostępu "Bearer", co oznacza, że zasób może udzielić dostępu do obiektu bearer tego tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Uzyskiwanie tokenu przy użyciu biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET najprostszym sposobem pracy z tożsamościami zarządzanymi dla zasobów platformy Azure jest pakiet Microsoft.Azure.Services.AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze dewelopera przy użyciu konta użytkownika z witryny Visual Studio, interfejsu wiersza polecenia platformy [Azure](/cli/azure)lub zintegrowanego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji na temat opcji tworzenia lokalnego przy użyciu tej biblioteki, zobacz [microsoft.Azure.Services.AppAuthentication reference](/dotnet/api/overview/azure/service-to-service-authentication). W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do pakietów [NuGet Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) do aplikacji.

2.  Dodaj następujący kod do aplikacji:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Aby dowiedzieć się więcej na temat pakietu Microsoft.Azure.Services.AppAuthentication i operacji, które uwidacznia, zobacz przykład [microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) oraz przykładowe aplikacje [App Service i KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)z tożsamościami zarządzanymi dla zasobów platformy Azure dla platformy .NET.

## <a name="get-a-token-using-c"></a>Uzyskiwanie tokenu przy użyciu języka C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Uzyskiwanie tokenu przy użyciu języka Java

Użyj tej [biblioteki JSON,](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) aby pobrać token przy użyciu języka Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Uzyskiwanie tokenu przy użyciu go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Uzyskiwanie tokenu przy użyciu Azure PowerShell

W poniższym przykładzie pokazano, jak używać tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta programu PowerShell w celu:

1. Uzyskiwanie tokenu dostępu.
2. Użyj tokenu dostępu, aby wywołać interfejs API REST Azure Resource Manager i uzyskać informacje o maszynie wirtualnej. Pamiętaj, aby zastąpić identyfikator subskrypcji, nazwę grupy zasobów i nazwę maszyny wirtualnej `<SUBSCRIPTION-ID>` odpowiednio `<RESOURCE-GROUP>` , i `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Przykład sposobu analizowania tokenu dostępu z odpowiedzi:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Uzyskiwanie tokenu przy użyciu narzędzia CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Przykład sposobu analizowania tokenu dostępu z odpowiedzi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Buforowanie tokenów

Mimo że tożsamości zarządzane dla podsystemu zasobów platformy Azure buforują tokeny, zalecamy również zaimplementowanie buforowania tokenów w kodzie. W związku z tym należy przygotować się do scenariuszy, w których zasób wskazuje, że token wygasł. 

Połączenia w sieci z usługą Azure AD mają wynik tylko wtedy, gdy:

- Chybienie w pamięci podręcznej występuje z powodu brak tokenu w tożsamościach zarządzanych dla pamięci podręcznej podsystemu zasobów platformy Azure.
- Token z pamięci podręcznej wygasł.

## <a name="error-handling"></a>Obsługa błędów

Tożsamości zarządzane dla punktu końcowego zasobów platformy Azure sygnalizuje błędy za pośrednictwem pola kodu stanu nagłówka komunikatu odpowiedzi HTTP jako błędy 4xx lub 5xx:

| Kod stanu | Przyczyna błędu | How To Handle |
| ----------- | ------------ | ------------- |
| 404 Nie znaleziono. | Punkt końcowy IMDS jest aktualizowany. | Ponów próbę przy użyciu wykładniczego odgałę roku. Zobacz wskazówki poniżej. |
| 429 Zbyt wiele żądań. |  Osiągnięto limit ograniczenia imds. | Ponów próbę przy użyciu wykładniczego odgałę roku. Zobacz wskazówki poniżej. |
| Błąd 4xx w żądaniu. | Co najmniej jeden z parametrów żądania był nieprawidłowy. | Nie należy ponawiać próby.  Sprawdź szczegóły błędu, aby uzyskać więcej informacji.  Błędy 4xx są błędami czasu projektowania.|
| Błąd przejściowy 5xx z usługi. | Tożsamości zarządzane dla podfolderów zasobów platformy Azure lub Azure Active Directory zwróciły błąd przejściowy. | Po odczekieniu co najmniej 1 sekundy można bezpiecznie ponowić próbę.  Jeśli ponawiasz próby zbyt szybko lub zbyt często, usługa IMDS i/lub Azure AD może zwrócić błąd limitu szybkości (429).|
| timeout | Punkt końcowy IMDS jest aktualizowany. | Ponów próbę przy użyciu wykładniczego odgałę roku. Zobacz wskazówki poniżej. |

Jeśli wystąpi błąd, odpowiednia treść odpowiedzi HTTP zawiera kod JSON ze szczegółami błędu:

| Element | Opis |
| ------- | ----------- |
| error   | Identyfikator błędu. |
| error_description | Pełny opis błędu. **Opisy błędów mogą ulec zmianie w dowolnym momencie. Nie należy pisać kodu, który odgałęzienia na podstawie wartości w opisie błędu.**|

### <a name="http-response-reference"></a>Informacje o odpowiedzi HTTP

Ta sekcja zawiera informacje o możliwych odpowiedziach na błędy. Stan "200 OK" to pomyślna odpowiedź, a token dostępu znajduje się w treści odpowiedzi JSON w elemencie access_token odpowiedzi.

| Kod stanu | Błąd | Opis błędu | Rozwiązanie |
| ----------- | ----- | ----------------- | -------- |
| 400 Nieprawidłowe żądanie | invalid_resource | AADSTS50001: Aplikacja o nazwie nie została znaleziona *\<URI\>* w dzierżawie o nazwie *\<TENANT-ID\>* . Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Być może żądanie uwierzytelnienia zostało wysłane do niewłaściwej dzierżawy.\ | (Tylko system Linux) |
| 400 Nieprawidłowe żądanie | bad_request_102 | Nie określono wymaganego nagłówka metadanych | W `Metadata` żądaniu brakuje pola nagłówka żądania lub jest on niepoprawnie sformatowany. Wartość musi być określona jako `true` , we wszystkich małe literach. Zobacz przykład "Przykładowe żądanie" w poprzedniej sekcji REST.|
| 401 Brak autoryzacji | unknown_source | Nieznane źródło *\<URI\>* | Sprawdź, czy twój adres URI żądania HTTP GET jest poprawnie sformatowany. Część `scheme:host/resource-path` musi być określona jako `http://localhost:50342/oauth2/token` . Zobacz przykład "Przykładowe żądanie" w poprzedniej sekcji REST.|
|           | invalid_request | Żądanie nie ma wymaganego parametru, zawiera nieprawidłową wartość parametru, zawiera parametr więcej niż raz lub w inny sposób jest źle sformułowany. |  |
|           | unauthorized_client | Klient nie ma autoryzacji do żądania tokenu dostępu przy użyciu tej metody. | Spowodowane przez żądanie na maszynie wirtualnej, która nie ma poprawnie skonfigurowanych tożsamości zarządzanych dla zasobów platformy Azure. Jeśli potrzebujesz pomocy dotyczącej konfiguracji maszyny wirtualnej, zobacz Configure [managed identities for Azure resources on a VM using the Azure Portal](qs-configure-portal-windows-vm.md) if you need assistance with VM configuration (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal jeśli potrzebujesz pomocy dotyczącej konfiguracji maszyny wirtualnej. |
|           | Access_denied | Właściciel zasobu lub serwer autoryzacji odrzucił żądanie. |  |
|           | unsupported_response_type | Serwer autoryzacji nie obsługuje uzyskiwania tokenu dostępu przy użyciu tej metody. |  |
|           | invalid_scope | Żądany zakres jest nieprawidłowy, nieznany lub źle sformułowany. |  |
| 500 Wewnętrzny błąd serwera | unknown | Nie można pobrać tokenu z usługi Active Directory. Aby uzyskać szczegółowe informacje, zobacz dzienniki w *\<file path\>* | Sprawdź, czy na maszynie wirtualnej włączono tożsamości zarządzane dla zasobów platformy Azure. Jeśli potrzebujesz pomocy dotyczącej konfiguracji maszyny wirtualnej, zobacz Configure [managed identities for Azure resources on a VM using the Azure Portal](qs-configure-portal-windows-vm.md) if you need assistance with VM configuration (Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal jeśli potrzebujesz pomocy dotyczącej konfiguracji maszyny wirtualnej.<br><br>Sprawdź również, czy twój adres URI żądania HTTP GET jest poprawnie sformatowany, szczególnie ten, który jest określony w ciągu zapytania. Zobacz przykładowe żądanie w poprzedniej sekcji REST lub usługi platformy Azure, które obsługują uwierzytelnianie usługi [Azure AD,](./services-support-managed-identities.md) aby uzyskać listę usług i ich identyfikatorów zasobów.

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zaleca się ponowić próbę, jeśli zostanie wyświetlony kod błędu 404, 429 lub 5xx (zobacz [Obsługa błędów powyżej).](#error-handling)

Limity ograniczania dotyczą liczby wywołań wykonanych do punktu końcowego IMDS. Po przekroczeniu progu ograniczania punkt końcowy IMDS ogranicza wszystkie dalsze żądania, gdy ograniczenie jest w mocy. W tym okresie punkt końcowy IMDS zwróci kod stanu HTTP 429 ("Zbyt wiele żądań"), a żądania nie powiodą się. 

W przypadku ponawiania zalecamy następującą strategię: 

| **Strategia ponawiania prób** | **Ustawienia** | **Wartości** | **Jak to działa** |
| --- | --- | --- | --- |
|ExponentialBackoff |Liczba ponownych prób<br />Minimalna liczba wycofań<br />Maksymalna liczba wycofań<br />Różnica w liczbie wycofań<br />Pierwsze szybkie ponowienie |5<br />0 sek.<br />60 sek.<br />2 sek.<br />fałsz |Próba 1 — opóźnienie 0 sek.<br />Próba 2 — opóźnienie ok. 2 sek.<br />Próba 3 — opóźnienie ok. 6 sek.<br />Próba 4 — opóźnienie ok. 14 sek.<br />Próba 5 — opóźnienie ok. 30 sek. |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [Usługi platformy Azure,](./services-support-managed-identities.md) które obsługują uwierzytelnianie usługi Azure AD, aby uzyskać listę zasobów, które obsługują usługę Azure AD i zostały przetestowane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure i ich odpowiednich identyfikatorów zasobów.


## <a name="next-steps"></a>Następne kroki

- Aby włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie wirtualnej platformy Azure, zobacz Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy [użyciu Azure Portal](qs-configure-portal-windows-vm.md).
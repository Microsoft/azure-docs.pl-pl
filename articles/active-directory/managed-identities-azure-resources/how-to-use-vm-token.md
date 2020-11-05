---
title: Korzystanie z tożsamości zarządzanych na maszynie wirtualnej w celu uzyskania tokenu dostępu — Azure AD
description: Instrukcje krok po kroku i przykłady dotyczące korzystania z tożsamości zarządzanych dla zasobów platformy Azure na maszynach wirtualnych w celu uzyskania tokenu dostępu OAuth.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0769366ad56e1b7431dbfa7c95f1256c509d24fa
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358171"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

Ten artykuł zawiera różne przykłady kodu i skryptów do pozyskiwania tokenów, a także wskazówki dotyczące ważnych tematów, takich jak obsługa wygaśnięcia tokenu i błędów HTTP. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów Azure PowerShell w tym artykule, pamiętaj, aby zainstalować najnowszą wersję [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - We wszystkich przykładowym kodzie/skrypcie w tym artykule przyjęto założenie, że klient działa na maszynie wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w Azure Portal, aby zdalnie nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantów (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu Azure SDK). 

> [!IMPORTANT]
> - Granica zabezpieczeń zarządzanych tożsamości dla zasobów platformy Azure jest zasobem używanym przez program. Wszystkie kod/skrypty uruchomione na maszynie wirtualnej mogą żądać i pobierać tokeny dla wszystkich zarządzanych tożsamości. 

## <a name="overview"></a>Omówienie

Aplikacja kliencka może zażądać tożsamości zarządzanych dla [tokenu dostępu tylko do aplikacji](../develop/developer-glossary.md#access-token) platformy Azure w celu uzyskania dostępu do danego zasobu. Token jest [oparty na tożsamościach zarządzanych dla jednostki usługi Azure Resources](overview.md#managed-identity-types). W związku z tym klient nie musi rejestrować się w celu uzyskania tokenu dostępu w ramach własnej nazwy głównej usługi. Token jest odpowiedni do użycia jako token okaziciela w [wywołaniach między usługami wymagającymi poświadczeń klienta](../develop/v2-oauth2-client-creds-grant-flow.md).

| Łącze | Opis |
| -------------- | -------------------- |
| [Uzyskiwanie tokenu przy użyciu protokołu HTTP](#get-a-token-using-http) | Szczegóły protokołu dla tożsamości zarządzanych dla punktu końcowego tokenu zasobów platformy Azure |
| [Uzyskiwanie tokenu przy użyciu biblioteki Microsoft. Azure. Services. AppAuthentication dla platformy .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Przykład korzystania z biblioteki Microsoft. Azure. Services. AppAuthentication z klienta platformy .NET
| [Uzyskiwanie tokenu przy użyciu języka C #](#get-a-token-using-c) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta C# |
| [Uzyskiwanie tokenu przy użyciu języka Java](#get-a-token-using-java) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta Java |
| [Uzyskiwanie tokenu przy użyciu języka go](#get-a-token-using-go) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z poziomu klienta programu go |
| [Uzyskiwanie tokenu przy użyciu Azure PowerShell](#get-a-token-using-azure-powershell) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z poziomu klienta programu PowerShell |
| [Uzyskiwanie tokenu przy użyciu ZWINIĘCIEa](#get-a-token-using-curl) | Przykład użycia tożsamości zarządzanych dla punktu końcowego REST zasobów platformy Azure z klienta bash/ZWINIĘCIE |
| Obsługa buforowania tokenu | Wskazówki dotyczące obsługi wygasłych tokenów dostępu |
| [Obsługa błędów](#error-handling) | Wskazówki dotyczące obsługi błędów HTTP zwróconych z zarządzanych tożsamości dla punktu końcowego tokenu zasobów platformy Azure |
| [Identyfikatory zasobów dla usług platformy Azure](#resource-ids-for-azure-services) | Gdzie można uzyskać identyfikatory zasobów dla obsługiwanych usług platformy Azure |

## <a name="get-a-token-using-http"></a>Uzyskiwanie tokenu przy użyciu protokołu HTTP 

Podstawowy interfejs do uzyskiwania tokenu dostępu jest oparty na REST, dzięki czemu jest dostępny dla dowolnej aplikacji klienckiej uruchomionej na maszynie wirtualnej, która może wykonywać wywołania REST protokołu HTTP. Jest to podobne do modelu programowania usługi Azure AD, z tą różnicą, że klient używa punktu końcowego na maszynie wirtualnej (vs punktu końcowego usługi Azure AD).

Przykładowe żądanie przy użyciu punktu końcowego Instance Metadata Service platformy Azure ( *zalecane)* :

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazujący, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Zarządzane tożsamości dla punktu końcowego zasobów platformy Azure dla Instance Metadata Service. |
| `api-version`  | Parametr ciągu zapytania, wskazujący wersję interfejsu API dla punktu końcowego IMDS. Użyj interfejsu API w wersji `2018-02-01` lub nowszej. |
| `resource` | Parametr ciągu zapytania, wskazujący identyfikator URI aplikacji dla zasobu docelowego. Pojawia się również w `aud` porozumieniu (odbiorcy) wystawionego tokenu. Ten przykład żąda tokenu w celu uzyskania dostępu do Azure Resource Manager, który ma identyfikator URI aplikacji `https://management.azure.com/` . |
| `Metadata` | Pole nagłówka żądania HTTP wymagane przez zarządzane tożsamości dla zasobów platformy Azure jako środki zaradcze związane z atakiem z fałszerstwem żądania po stronie serwera (SSRF). Ta wartość musi być ustawiona na wartość "true" w przypadku małych liter. |
| `object_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|
| `client_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący client_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|
| `mi_res_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący mi_res_id (identyfikator zasobu platformy Azure) tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika. |

Przykładowe żądanie przy użyciu tożsamości zarządzanych dla punktu końcowego rozszerzenia maszyny wirtualnej Azure Resources *(zaplanowane do wycofania w styczniu 2019)* :

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazujący, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Zarządzane tożsamości dla punktu końcowego zasobów platformy Azure, gdzie 50342 jest portem domyślnym i można skonfigurować. |
| `resource` | Parametr ciągu zapytania, wskazujący identyfikator URI aplikacji dla zasobu docelowego. Pojawia się również w `aud` porozumieniu (odbiorcy) wystawionego tokenu. Ten przykład żąda tokenu w celu uzyskania dostępu do Azure Resource Manager, który ma identyfikator URI aplikacji `https://management.azure.com/` . |
| `Metadata` | Pole nagłówka żądania HTTP wymagane przez zarządzane tożsamości dla zasobów platformy Azure jako środki zaradcze związane z atakiem z fałszerstwem żądania po stronie serwera (SSRF). Ta wartość musi być ustawiona na wartość "true" w przypadku małych liter.|
| `object_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|
| `client_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący client_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|

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
| `access_token` | Żądany token dostępu. Podczas wywoływania bezpiecznego interfejsu API REST token jest osadzony w `Authorization` polu nagłówka żądania jako token "Bearer", dzięki czemu interfejs API może uwierzytelniać obiekt wywołujący. | 
| `refresh_token` | Nieużywane przez zarządzane tożsamości dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund, przez jaką token dostępu pozostaje prawidłowy, przed wygaśnięciem, od momentu wystawienia. Czas wystawienia można znaleźć w ramach `iat` żądania tokenu. |
| `expires_on` | Wartość TimeSpan w przypadku wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `exp` żądania tokenu). |
| `not_before` | Przedział czasu, gdy token dostępu zaczyna obowiązywać i można go zaakceptować. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `nbf` żądania tokenu). |
| `resource` | Zasób, dla którego zażądano tokenu dostępu, który jest zgodny z `resource` parametrem ciągu zapytania żądania. |
| `token_type` | Typ tokenu, który jest tokenem dostępu "Bearer", co oznacza, że zasób może zapewnić dostęp do okaziciela tego tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Uzyskiwanie tokenu przy użyciu biblioteki Microsoft. Azure. Services. AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET Najprostszym sposobem pracy z tożsamościami zarządzanymi dla zasobów platformy Azure jest pakiet Microsoft. Azure. Services. AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, [interfejsu wiersza polecenia platformy Azure](/cli/azure?view=azure-cli-latest)lub zintegrowanego uwierzytelniania Active Directory. Aby uzyskać więcej informacji na temat lokalnych opcji tworzenia w tej bibliotece, zobacz [odwołanie Microsoft. Azure. Services. AppAuthentication](../../key-vault/general/service-to-service-authentication.md). W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do pakietów NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

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
    
Aby dowiedzieć się więcej na temat Microsoft. Azure. Services. AppAuthentication i wykonywanych przez niego operacji, zobacz artykuł [Microsoft. Azure. Services. AppAuthentication](../../key-vault/general/service-to-service-authentication.md) , a [App Service i Magazyn kluczy z tożsamościami zarządzanymi dla przykładu .NET zasobów platformy Azure](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

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

Użyj tej [biblioteki JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) do pobrania tokenu przy użyciu języka Java.

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

## <a name="get-a-token-using-go"></a>Uzyskiwanie tokenu przy użyciu języka go

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

W poniższym przykładzie pokazano, jak używać zarządzanych tożsamości dla punktu końcowego REST zasobów platformy Azure z poziomu klienta programu PowerShell, aby:

1. Uzyskaj token dostępu.
2. Użyj tokenu dostępu, aby wywołać interfejs API REST Azure Resource Manager i uzyskać informacje o maszynie wirtualnej. Pamiętaj, aby zastąpić identyfikator subskrypcji, nazwę grupy zasobów i nazwę maszyny wirtualnej `<SUBSCRIPTION-ID>` odpowiednio dla, `<RESOURCE-GROUP>` i `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Przykład na temat analizowania tokenu dostępu z odpowiedzi:
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

## <a name="get-a-token-using-curl"></a>Uzyskiwanie tokenu przy użyciu ZWINIĘCIEa

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Przykład na temat analizowania tokenu dostępu z odpowiedzi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Buforowanie tokenów

Podczas gdy zarządzane tożsamości dla podsystemu zasobów platformy Azure są używane (tożsamości IMDS/Managed dla rozszerzenia maszyny wirtualnej zasobów platformy Azure) są tokenami pamięci podręcznej, zalecamy także zaimplementowanie buforowania tokenu w kodzie. W związku z tym należy przygotować się do scenariuszy, w których zasób wskazuje, że token wygasł. 

Wywołania usługi Azure AD w sieci szkieletowej tylko wtedy, gdy:
- Chybienia w pamięci podręcznej występuje z powodu braku tokenu w zarządzanych tożsamościach pamięci podręcznej podsystemu zasobów platformy Azure
- token pamięci podręcznej wygasł

## <a name="error-handling"></a>Obsługa błędów

Zarządzane tożsamości punktu końcowego zasobów platformy Azure sygnalizują błędy za pośrednictwem pola kod stanu w nagłówku komunikatu odpowiedzi HTTP, ponieważ 4xx lub 5xx błędy:

| Kod stanu | Przyczyna błędu | Jak obsłużyć |
| ----------- | ------------ | ------------- |
| nie znaleziono 404. | Trwa aktualizowanie punktu końcowego IMDS. | Ponów próbę, używając wykładniczej wycofywania. Zobacz wskazówki poniżej. |
| 429 zbyt wiele żądań. |  Osiągnięto limit dławienia IMDS. | Ponów próbę, używając wykładniczej wycofywania. Zobacz wskazówki poniżej. |
| 4xx Błąd w żądaniu. | Co najmniej jeden z parametrów żądania był niepoprawny. | Nie ponawiaj próby.  Aby uzyskać więcej informacji, zapoznaj się ze szczegółami błędu.  Błędy 4xx są błędy czasu projektowania.|
| 5xx błąd przejściowy z usługi. | Zarządzane tożsamości dla podsystemu zasobów platformy Azure lub Azure Active Directory zwróciło błąd przejściowy. | Można bezpiecznie ponowić próbę po odczekaniu przez co najmniej 1 sekundę.  Jeśli spróbujesz zbyt szybko lub zbyt często, IMDS i/lub usługa Azure AD może zwrócić błąd limitu szybkości (429).|
| timeout | Trwa aktualizowanie punktu końcowego IMDS. | Ponów próbę, używając wykładniczej wycofywania. Zobacz wskazówki poniżej. |

Jeśli wystąpi błąd, odpowiadająca treść odpowiedzi HTTP zawiera kod JSON z szczegółowymi informacjami o błędzie:

| Element | Opis |
| ------- | ----------- |
| Błąd   | Identyfikator błędu. |
| error_description | Pełny opis błędu. **Opis błędów można zmienić w dowolnym momencie. Nie należy pisać kodu, który oddziałuje na podstawie wartości w opisie błędu.**|

### <a name="http-response-reference"></a>Odwołanie do odpowiedzi HTTP

Ta sekcja dokumentuje możliwe odpowiedzi na błędy. Stan "200 OK" jest pomyślną odpowiedzią, a token dostępu jest zawarty w kodzie JSON treści odpowiedzi, w elemencie access_token.

| Kod stanu | Błąd | Opis błędu | Rozwiązanie |
| ----------- | ----- | ----------------- | -------- |
| 400 Nieprawidłowe żądanie | invalid_resource | AADSTS50001: Nazwa aplikacji *\<URI\>* nie została znaleziona w dzierżawie o nazwie *\<TENANT-ID\>* . Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Być może wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy. \ | (Tylko system Linux) |
| 400 Nieprawidłowe żądanie | bad_request_102 | Nie określono wymaganego nagłówka metadanych | `Metadata`W żądaniu brakuje pola nagłówka żądania lub jest ono sformatowane nieprawidłowo. Wartość musi być określona jako `true` , we wszystkich małych przypadkach. Zapoznaj się z przykładem "Przykładowe żądanie" w poprzedniej sekcji REST.|
| 401 Brak autoryzacji | unknown_source | Nieznane Źródło *\<URI\>* | Sprawdź, czy identyfikator URI żądania HTTP GET jest poprawnie sformatowany. `scheme:host/resource-path`Część musi być określona jako `http://localhost:50342/oauth2/token` . Zapoznaj się z przykładem "Przykładowe żądanie" w poprzedniej sekcji REST.|
|           | invalid_request | W żądaniu brakuje wymaganego parametru, zawiera on nieprawidłową wartość parametru, zawiera parametr więcej niż jeden raz lub jest nieprawidłowo sformułowany. |  |
|           | unauthorized_client | Klient nie ma autoryzacji do żądania tokenu dostępu za pomocą tej metody. | Spowodowane przez żądanie, które nie używało lokalnego sprzężenia zwrotnego do wywołania rozszerzenia lub na maszynie wirtualnej, która nie ma prawidłowo skonfigurowanych tożsamości zarządzanych dla zasobów platformy Azure. Aby uzyskać pomoc dotyczącą konfiguracji maszyny wirtualnej [, zobacz Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md) . |
|           | access_denied | Właściciel zasobu lub serwer autoryzacji odrzucił żądanie. |  |
|           | unsupported_response_type | Serwer autoryzacji nie obsługuje uzyskiwania tokenu dostępu przy użyciu tej metody. |  |
|           | invalid_scope | Żądany zakres jest nieprawidłowy, nieznany lub źle sformułowany. |  |
| 500 Wewnętrzny błąd serwera | unknown | Nie można pobrać tokenu z usługi Active Directory. Aby uzyskać szczegółowe informacje, zobacz dzienniki *\<file path\>* | Sprawdź, czy na maszynie wirtualnej została włączona tożsamość zarządzana dla zasobów platformy Azure. Aby uzyskać pomoc dotyczącą konfiguracji maszyny wirtualnej [, zobacz Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md) .<br><br>Sprawdź również, czy identyfikator URI żądania HTTP GET jest poprawnie sformatowany, szczególnie identyfikator URI zasobu określony w ciągu zapytania. Zapoznaj się z sekcją "Przykładowe żądanie" w poprzedniej sekcji REST, aby zapoznać się z przykładem lub [usługami platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](./services-support-managed-identities.md) w celu uzyskania listy usług i odpowiednich identyfikatorów zasobów.

## <a name="retry-guidance"></a>Wskazówki dotyczące ponawiania prób 

Zalecane jest ponowienie próby, jeśli otrzymasz kod błędu 404, 429 lub 5xx (zobacz sekcję [Obsługa błędów](#error-handling) powyżej).

Limity ograniczania stosują się do liczby wywołań w punkcie końcowym IMDS. Gdy zostanie przekroczony próg ograniczania przepustowości, IMDS punkt końcowy ogranicza wszelkie dalsze żądania, gdy ograniczanie jest włączone. W tym okresie punkt końcowy IMDS zwróci kod stanu HTTP 429 ("zbyt wiele żądań"), a żądania nie powiodą się. 

W przypadku ponowienia próby zalecamy następujące strategie: 

| **Strategia ponawiania prób** | **Ustawienia** | **Wartości** | **Jak to działa** |
| --- | --- | --- | --- |
|ExponentialBackoff |Liczba ponownych prób<br />Minimalna liczba wycofań<br />Maksymalna liczba wycofań<br />Różnica w liczbie wycofań<br />Pierwsze szybkie ponowienie |5<br />0 sek.<br />60 sek.<br />2 sek.<br />fałsz |Próba 1 — opóźnienie 0 sek.<br />Próba 2 — opóźnienie ok. 2 sek.<br />Próba 3 — opóźnienie ok. 6 sek.<br />Próba 4 — opóźnienie ok. 14 sek.<br />Próba 5 — opóźnienie ok. 30 sek. |

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](./services-support-managed-identities.md) , aby uzyskać listę zasobów, które obsługują usługę Azure AD i zostały przetestowane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure oraz ich identyfikatorów zasobów.


## <a name="next-steps"></a>Następne kroki

- Aby włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md).
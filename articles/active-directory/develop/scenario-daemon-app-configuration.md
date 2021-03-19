---
title: Skonfiguruj aplikacje demona, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web (Konfiguracja aplikacji)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 3c52d4d80fd3c77cff5e335967fc9d109212ce29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578434"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web.

## <a name="microsoft-libraries-supporting-daemon-apps"></a>Biblioteki firmy Microsoft obsługujące aplikacje demona

Następujące biblioteki firmy Microsoft obsługują aplikacje demona:

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="configure-the-authority"></a>Konfigurowanie urzędu

Aplikacje demona używają uprawnień aplikacji zamiast uprawnień delegowanych. Typ konta, który jest obsługiwany, nie może być kontem w żadnym katalogu organizacyjnym ani żadnym konto Microsoft osobistym (na przykład Skype, Xbox, Outlook.com). Brak administratora dzierżawy do udzielenia zgody na aplikację demona dla konta osobistego firmy Microsoft. Musisz wybrać *konta w mojej organizacji* lub *kontach w dowolnej organizacji*.

Urząd określony w konfiguracji aplikacji powinien być dzierżawcą (określając identyfikator dzierżawy lub nazwę domeny skojarzoną z Twoją organizacją).

Nawet jeśli chcesz udostępnić narzędzie wielodostępne, użyj identyfikatora dzierżawy lub nazwy domeny, a **nie** `common` lub `organizations` z tym przepływem, ponieważ usługa nie może w sposób niezawodny ustalić, która dzierżawa powinna zostać użyta.

## <a name="configure-and-instantiate-the-application"></a>Konfigurowanie i tworzenie wystąpienia aplikacji

W bibliotekach MSAL poświadczenia klienta (klucz tajny lub certyfikat) są przesyłane jako parametr konstrukcji poufnej aplikacji klienckiej.

> [!IMPORTANT]
> Nawet jeśli aplikacja jest aplikacją konsolową, która działa jako usługa, jeśli jest to aplikacja demona, musi być poufną aplikacją kliencką.

### <a name="configuration-file"></a>Plik konfiguracji

Plik konfiguracji definiuje:

- Wystąpienie w chmurze i identyfikator dzierżawy, które razem składają się na *Urząd*.
- Identyfikator klienta, który został uzyskany z rejestracji aplikacji.
- Wpis tajny klienta lub certyfikat.

# <a name="net"></a>[.NET](#tab/dotnet)

Oto przykład definiowania konfiguracji w [*appsettings.js*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) pliku. Ten przykład jest pobierany z programu z przykładu kodu [demona konsoli .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) w witrynie GitHub.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Podajesz `ClientSecret` albo lub `CertificateName` . Te ustawienia są wyłączność.

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Parametry konfiguracji [ przykładu demonaNode.js](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console/) są zlokalizowane w pliku *ENV* :

```Text 
# Credentials
TENANT_ID=Enter_the_Tenant_Info_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

# <a name="python"></a>[Python](#tab/python)

Podczas budowania klienta poufnego przy użyciu kluczy tajnych klienta [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) pliku konfiguracyjnego w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Podczas tworzenia poufnego klienta z certyfikatami [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) pliku konfiguracyjnego w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następująca:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

---

### <a name="instantiate-the-msal-application"></a>Tworzenie wystąpienia aplikacji MSAL

Aby utworzyć wystąpienie aplikacji MSAL, dodać, odwołać lub zaimportować pakiet MSAL (w zależności od języka).

Konstrukcja różni się w zależności od tego, czy są używane klucze tajne klienta czy certyfikaty (lub, jako zaawansowany scenariusz, podpisane potwierdzenia).

#### <a name="reference-the-package"></a>Odwoływanie się do pakietu

Odwołuje się do pakietu MSAL w kodzie aplikacji.

# <a name="net"></a>[.NET](#tab/dotnet)

Dodaj pakiet NuGet [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) do swojej aplikacji, a następnie Dodaj do `using` niego dyrektywę w kodzie.

W programie MSAL.NET poufna aplikacja kliencka jest reprezentowana przez `IConfidentialClientApplication` interfejs.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Po prostu zainstaluj pakiety przez uruchomienie `npm install` w folderze, w którym znajduje się *package.js* pliku. Następnie zaimportuj pakiet **msal-Node** :

```JavaScript 
const msal = require('@azure/msal-node');
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu klucza tajnego klienta

Oto kod służący do tworzenia wystąpienia poufnej aplikacji klienckiej przy użyciu klucza tajnego klienta:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

`Authority`Jest to połączenie wystąpienia chmury i identyfikatora dzierżawy, na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com` lub `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd` . W *appsettings.js* w pliku przedstawionym w sekcji [plik konfiguracyjny](#configuration-file) są one reprezentowane `Instance` odpowiednio przez i `Tenant` wartości.

W przykładowym kodzie, z którego pochodzi poprzedni fragment kodu, `Authority` jest właściwością klasy  [AuthenticationConfig](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70) i jest zdefiniowana jako taka:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript

const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatu klienta

Oto kod służący do kompilowania aplikacji z certyfikatem:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```
# <a name="java"></a>[Java](#tab/java)

W MSAL Java istnieją dwóch konstruktorów, które umożliwiają utworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatów:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

lub

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aplikacja Przykładowa nie implementuje inicjowania z certyfikatami w tej chwili.

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Scenariusz zaawansowany: Tworzenie wystąpienia poufnej aplikacji klienckiej za pomocą potwierdzeń klienta

# <a name="net"></a>[.NET](#tab/dotnet)

Zamiast klucza tajnego klienta lub certyfikatu poufna aplikacja kliencka może również udowodnić swoją tożsamość za pomocą potwierdzeń klientów.

MSAL.NET ma dwie metody, aby zapewnić podpisane potwierdzenia do poufnej aplikacji klienckiej:

- `.WithClientAssertion()`
- `.WithClientClaims()`

W przypadku korzystania z `WithClientAssertion` programu Podaj podpisany token JWT. Ten zaawansowany scenariusz jest szczegółowy w przypadku [potwierdzeń klientów](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

W przypadku korzystania z programu `WithClientClaims` MSAL.NET wyśle podpisane potwierdzenie zawierające oświadczenia oczekiwane przez usługę Azure AD oraz dodatkowe oświadczenia klienta, które chcesz wysłać.
Ten kod pokazuje, jak to zrobić:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Aby uzyskać szczegółowe informacje, zobacz [potwierdzenia klientów](msal-net-client-assertions.md).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Przykładowa aplikacja nie implementuje inicjacji z potwierdzeniami w tym momencie.

# <a name="python"></a>[Python](#tab/python)

W MSAL Python można zapewnić oświadczenia klienta przy użyciu oświadczeń, które będą podpisane przez ten `ConfidentialClientApplication` klucz prywatny.

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Aby uzyskać szczegółowe informacje, zobacz dokumentację MSAL języka Python dla [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

---

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji](./scenario-daemon-acquire-token.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji](./scenario-daemon-acquire-token.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji](./scenario-daemon-acquire-token.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji](./scenario-daemon-acquire-token.md?tabs=python).

---

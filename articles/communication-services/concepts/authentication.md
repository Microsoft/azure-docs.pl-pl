---
title: Uwierzytelnianie w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat różnych sposobów uwierzytelniania aplikacji lub usługi w ramach usług komunikacyjnych.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4d6e02852dcd2d30a764417a4b5e0e012a1d2ab5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571100"
---
# <a name="authenticate-to-azure-communication-services"></a>Uwierzytelnianie w usłudze Azure Communications Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ten artykuł zawiera informacje dotyczące uwierzytelniania klientów w usłudze Azure Communication Services przy użyciu *kluczy dostępu* i *tokenów dostępu użytkowników*. Każdy klient korzystający z usług Azure Communications Services musi zostać uwierzytelniony.

W poniższej tabeli opisano opcje uwierzytelniania obsługiwane przez biblioteki klienta usługi Azure Communication Services:

| Biblioteka kliencka | Klucz dostępu    | Tokeny dostępu użytkowników |
| -------------- | ------------- | ------------------ |
| Administracja | Obsługiwane     | Nieobsługiwane      |
| SMS            | Obsługiwane     | Nieobsługiwane      |
| Czat           | Nieobsługiwane | Obsługiwane          |
| Rozmów        | Nieobsługiwane | Obsługiwane          |

Każdą opcję autoryzacji można krótko opisać poniżej:

- **Uzyskaj dostęp do uwierzytelniania klucza** dla programu SMS i operacji administracyjnych. Uwierzytelnianie klucza dostępu jest odpowiednie dla aplikacji działających w środowisku zaufanej usługi. Aby uwierzytelnić się przy użyciu klucza dostępu, Klient generuje [kod uwierzytelniania wiadomości oparty na wykorzystaniu skrótu (HMAC)](https://en.wikipedia.org/wiki/HMAC) i dołącza go w `Authorization` nagłówku każdego żądania HTTP. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu klucza dostępu](#authenticate-with-an-access-key).
- Uwierzytelnianie **tokenu dostępu użytkownika** na potrzeby rozmowy i wywoływania. Tokeny dostępu użytkowników pozwalają aplikacjom klienckim uwierzytelniać się bezpośrednio w usłudze Azure Communications Services. Te tokeny są generowane w ramach usługi inicjowania obsługi tokenów po stronie serwera, która została utworzona. Są one następnie udostępniane urządzeniom klienckim, które używają tokenu do inicjowania rozmowy i wywoływania bibliotek klienckich. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu tokenu dostępu użytkownika](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Uwierzytelnianie przy użyciu klucza dostępu

Uwierzytelnianie klucza dostępu korzysta z wspólnego klucza tajnego w celu wygenerowania algorytmu HMAC dla każdego żądania HTTP obliczanego przy użyciu algorytmu SHA256 i wysyła je w `Authorization` nagłówku przy użyciu `HMAC-SHA256` schematu.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Biblioteki klienckie usług Azure Communications Services używające uwierzytelniania klucza dostępu należy inicjować przy użyciu parametrów połączenia zasobu. Jeśli nie korzystasz z biblioteki klienckiej, możesz programowo generować te HMAC przy użyciu klucza dostępu zasobu. Aby dowiedzieć się więcej na temat parametrów połączenia, zapoznaj się z [przewodnikiem Szybki Start aprowizacji zasobów](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Podpisz żądanie HTTP

Jeśli nie korzystasz z biblioteki klienckiej do przesyłania żądań HTTP do interfejsów API REST usług Azure Communications Services, musisz programowo utworzyć HMACs dla każdego żądania HTTP. Poniższe kroki opisują sposób konstruowania nagłówka autoryzacji:

1. Określ sygnaturę czasową skoordynowanego czasu uniwersalnego (UTC) dla żądania w `x-ms-date` nagłówku lub w standardowym `Date` nagłówku HTTP. Usługa sprawdza poprawność tego programu w celu ochrony przed niektórymi atakami, w tym atakami metodą powtórzeń.
1. Mieszaj treść żądania HTTP przy użyciu algorytmu SHA256, a następnie Przekaż go przy użyciu żądania, za pośrednictwem `x-ms-content-sha256` nagłówka.
1. Utwórz ciąg, który ma być podpisany, łącząc czasownik HTTP (np. `GET` lub `PUT` ), ścieżkę żądania HTTP i wartości `Date` `Host` nagłówków, i `x-ms-content-sha256` http w następującym formacie:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Generowanie sygnatury HMAC-256 ciągu zakodowanego w formacie UTF-8, który został utworzony w poprzednim kroku. Następnie zakoduj wyniki w formacie base64. Należy pamiętać, że należy również zakodować kodowanie Base64 klucza dostępu. Użyj następującego formatu (pokazane jako pseudo kod):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Określ nagłówek autoryzacji w następujący sposób:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Gdzie `<hmac-sha256-signature>` jest to HMAC obliczony w poprzednim kroku.

## <a name="authenticate-with-a-user-access-token"></a>Uwierzytelnianie za pomocą tokenu dostępu użytkownika

Tokeny dostępu użytkowników pozwalają aplikacjom klienckim uwierzytelniać się bezpośrednio w usłudze Azure Communications Services. Aby to osiągnąć, należy skonfigurować zaufaną usługę, która uwierzytelnia użytkowników aplikacji i wystawia tokeny dostępu użytkowników z biblioteką klienta Administracja. Zapoznaj się z dokumentacją dotyczącą [architektury klienta i serwera](./client-and-server-architecture.md) , aby dowiedzieć się więcej na temat zagadnień związanych z architekturą.

`CommunicationUserCredential`Klasa zawiera logikę do udostępniania poświadczeń tokenu dostępu użytkownika do bibliotek klienckich i zarządzania ich cyklem życia.

### <a name="initialize-the-client-libraries"></a>Inicjowanie bibliotek klienckich

Aby zainicjować biblioteki klienckie usług Azure Communications Services, które wymagają uwierzytelniania tokenu dostępu użytkownika, należy najpierw utworzyć wystąpienie `CommunicationUserCredential` klasy, a następnie użyć jej do zainicjowania klienta interfejsu API.

Poniższe fragmenty kodu pokazują, jak zainicjować bibliotekę klienta programu Chat z tokenem dostępu użytkownika:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Odświeżanie tokenów dostępu użytkowników

Tokeny dostępu użytkowników to krótkoterminowe poświadczenia, które należy wystawić, aby uniemożliwić użytkownikom zakłócenia działania usługi. `CommunicationUserCredential`Konstruktor akceptuje funkcję wywołania zwrotnego Refresh, która umożliwia aktualizowanie tokenów dostępu użytkowników przed ich wygaśnięciem. Tego wywołania zwrotnego należy użyć do pobrania nowego tokenu dostępu użytkownika z zaufanej usługi.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

`refreshProactively`Opcja pozwala określić sposób zarządzania cyklem życia tokenu. Domyślnie, gdy token jest przestarzały, wywołanie zwrotne będzie blokować żądania interfejsu API i próbować je odświeżyć. Gdy `refreshProactively` jest ustawiona na `true` wywołanie zwrotne jest zaplanowane i wykonywane asynchronicznie przed wygaśnięciem tokenu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)

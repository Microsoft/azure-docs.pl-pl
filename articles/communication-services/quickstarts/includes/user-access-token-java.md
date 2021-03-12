---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 0665a4ca80f7cf1b15ba432bfce68b42db36ddaa
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020901"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Wdrożony zasób usług komunikacyjnych i parametry połączenia. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Otwórz terminal lub okno poleceń. Przejdź do katalogu, w którym chcesz utworzyć aplikację Java. Uruchom poniższe polecenie, aby wygenerować projekt Java z szablonu Maven-Archetype-szybkiego startu.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Zauważ, że zadanie "Generuj" utworzyło katalog o takiej samej nazwie jak nazwa `artifactId` . W tym katalogu Katalog Src/Main/Java zawiera kod źródłowy projektu, `src/test/java directory` zawiera źródło testu, a `pom.xml` plik jest modelem obiektu projektu projektu lub pliku pom.

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik **pom.xml** w edytorze tekstów. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do katalogu */src/Main/Java/com/Communication/QuickStart*
1. Otwórz plik *App. Java* w edytorze
1. Zastąp `System.out.println("Hello world!");` instrukcję
1. Dodaj `import` dyrektywy

Użyj następującego kodu, aby rozpocząć:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `CommunicationIdentityClient` z kluczem dostępu i punktem końcowym zasobu. Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `main`:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .httpClient(httpClient)
        .buildClient();
```

Klienta można zainicjować przy użyciu dowolnego niestandardowego klienta HTTP, który implementuje `com.azure.core.http.HttpClient` interfejs. Powyższy kod ilustruje użycie klienta HTTP z sieciami [Azure Core](/java/api/overview/azure/core-http-netty-readme) , który jest dostarczany przez program `azure-core` .

Możesz również podać całe parametry połączenia przy użyciu `connectionString()` funkcji zamiast podawania punktu końcowego i klucza dostępu.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `createUser` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Przechowywanie otrzymanej tożsamości z mapowaniem do użytkowników aplikacji. Można na przykład przechowywać je w bazie danych serwera aplikacji. Tożsamość jest wymagana później, aby można było wystawiać tokeny dostępu.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Wystawianie tokenów dostępu

Użyj `getToken` metody, aby wystawić token dostępu dla istniejącej tożsamości usług komunikacyjnych. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md). Nowe wystąpienie parametru `user` można utworzyć na podstawie ciągu reprezentującego tożsamość usługi komunikacyjnej platformy Azure.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Tworzenie tożsamości i tokenu problemu w jednym wywołaniu

Alternatywnie możesz użyć metody "createUserAndToken", aby utworzyć nowy wpis w katalogu z unikatowym `Id` i wystawianym tokenem dostępu.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Tokeny dostępu to krótkoterminowe poświadczenia, które należy ponownie wydać. Nie może to spowodować zakłócenia środowiska użytkownika aplikacji. `expiresAt`Właściwość wskazuje okres istnienia tokenu dostępu.

## <a name="refresh-access-tokens"></a>Odświeżenie tokenów dostępu

Aby odświeżyć token dostępu, użyj `CommunicationUserIdentifier` obiektu, aby go ponownie wydać:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Odwołaj tokeny dostępu

W niektórych przypadkach można jawnie odwołać tokeny dostępu. Na przykład, gdy użytkownik aplikacji zmieni hasło używane do uwierzytelniania w usłudze. Metoda `revokeTokens` unieważnia wszystkie aktywne tokeny dostępu, które zostały wystawione dla tożsamości.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Usuwanie tożsamości

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny dostępu i uniemożliwia wystawianie tokenów dostępu dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z tożsamością.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Uruchamianie kodu

Przejdź do katalogu zawierającego plik *pom.xml* i skompiluj projekt przy użyciu następującego `mvn` polecenia.

```console
mvn compile
```

Następnie Skompiluj pakiet.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

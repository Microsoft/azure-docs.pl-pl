---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 2daed8edbc433003b72e80de4f5fbfe3539edb31
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816646"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta programu SMS dla usług komunikacyjnych, aby wysyłać wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) w wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu z włączoną funkcją SMS. [Pobierz numer telefonu](../get-phone-number.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom polecenie, `mvn -v` Aby sprawdzić, czy Maven jest zainstalowany.
- Aby wyświetlić numery telefonów skojarzone z zasobem usług komunikacyjnych, zaloguj się do [Azure Portal](https://portal.azure.com/), Znajdź zasób usługi komunikacyjnej i Otwórz kartę **numery telefonów** w okienku nawigacji po lewej stronie.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Otwórz terminal lub okno poleceń i przejdź do katalogu, w którym chcesz utworzyć aplikację Java. Uruchom poniższe polecenie, aby wygenerować projekt Java z szablonu Maven-Archetype-szybkiego startu.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Celem "Generuj" będzie utworzenie katalogu o takiej samej nazwie jak artifactId. W tym katalogu Katalog **src/Main/Java** zawiera kod źródłowy projektu, **Katalog Src/test/Java** zawiera źródło testu, a plik **pom.xml** jest modelem obiektu projektu projektu lub pliku pom.

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik **pom.xml** w edytorze tekstów. Dodaj następujący element zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Dodaj `azure-core-http-netty` zależność do pliku **pom.xml** .

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Otwórz **/src/Main/Java/com/Communication/QuickStart/App.Java** w edytorze tekstów, Dodaj dyrektywy importu i Usuń `System.out.println("Hello world!");` instrukcję:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta programu SMS dla usług Azure Communication Services dla języka Java.

| Nazwa                                                             | Opis                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Ta klasa tworzy SmsClient. Podajesz go za pomocą punktu końcowego, poświadczenia i klienta http. |
| SmsClient                    | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Służy do wysyłania wiadomości SMS.                |
| SendSmsResponse               | Ta klasa zawiera odpowiedź z usługi SMS.                                          |
| CommunicationClientCredential | Ta klasa obsługuje żądania podpisywania.                                                            |
| PhoneNumber                   | Ta klasa zawiera informacje o numerze telefonu

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `SmsClient` z parametrami połączenia. Poniższy kod pobiera punkt końcowy i ciągi poświadczeń dla zasobu ze zmiennych środowiskowych o nazwie `COMMUNICATION_SERVICES_ENDPOINT_STRING` i `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (poświadczenie jest `Key` z Azure Portal. Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `main`:

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

Klienta można zainicjować przy użyciu dowolnego niestandardowego klienta HTTP, który implementuje `com.azure.core.http.HttpClient` interfejs. Powyższy kod ilustruje użycie klienta HTTP z sieciami [Azure Core](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) , który jest dostarczany przez program `azure-core` .

## <a name="send-an-sms-message"></a>Wysyłanie wiadomości SMS

Wyślij wiadomość SMS, wywołując metodę sendMessage. Dodaj ten kod do końca `main` metody:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Należy zastąpić `<leased-phone-number>` numerem telefonu z włączoną obsługą programu SMS skojarzonym z zasobem usługi komunikacyjnej oraz `<to-phone-number>` numerem telefonu, na który chcesz wysłać wiadomość.

`enableDeliveryReport`Parametr jest opcjonalnym parametrem, którego można użyć w celu skonfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Uruchamianie kodu

Przejdź do katalogu zawierającego plik **pom.xml** i skompiluj projekt przy użyciu `mvn` polecenia.

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

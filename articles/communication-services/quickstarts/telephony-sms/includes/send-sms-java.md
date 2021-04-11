---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: cdf1267d53abc2214521f584b6cfb4738b808204
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113209"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu zestawu SDK SMS usług komunikacyjnych w celu wysyłania wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/) w wersji 8 lub nowszej.
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
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Otwórz **/src/Main/Java/com/Communication/QuickStart/App.Java** w edytorze tekstów, Dodaj dyrektywy importu i Usuń `System.out.println("Hello world!");` instrukcję:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SMS SDK usług Azure Communication Services dla języka Java.

| Nazwa                                                             | Opis                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Ta klasa tworzy SmsClient. Podajesz go za pomocą punktu końcowego, poświadczenia i klienta http. |
| SmsClient                    | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Służy do wysyłania wiadomości SMS.                |
| SmsSendOptions               | Ta klasa zawiera opcje dodawania tagów niestandardowych i konfigurowania raportowania dostarczania. Jeśli deliveryReportEnabled ma wartość true, zdarzenie będzie emitowane po pomyślnym dostarczeniu |        
| SmsSendResult                | Ta klasa zawiera wynik z usługi SMS.                                          |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie `SmsClient` z parametrami połączenia. (Poświadczenie jest `Key` z Azure Portal. Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string). Ponadto można zainicjować klienta przy użyciu dowolnego niestandardowego klienta HTTP, który implementuje `com.azure.core.http.HttpClient` interfejs.

Dodaj następujący kod do metody `main`:

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

Możesz również podać całe parametry połączenia przy użyciu funkcji connectionString () zamiast podawania punktu końcowego i klucza dostępu.
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Wyślij wiadomość SMS z systemem 1:1

Aby wysłać wiadomość SMS do pojedynczego adresata, wywołaj `send` metodę z SmsClient za pomocą jednego numeru telefonu odbiorcy. Możesz również przekazać parametry opcjonalne, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

Należy zastąpić `<from-phone-number>` numerem telefonu z włączoną obsługą programu SMS skojarzonym z zasobem usługi komunikacyjnej oraz `<to-phone-number>` numerem telefonu, na który chcesz wysłać wiadomość.

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Wyślij wiadomość SMS z opcją 1: N z opcjami
Aby wysłać wiadomość SMS do listy adresatów, wywołaj `send` metodę z listą numerów telefonów adresatów. Możesz również przekazać parametry opcjonalne, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Należy zamienić `<from-phone-number>` na numer telefonu z włączoną funkcją SMS skojarzoną z zasobem usługi komunikacyjnej oraz `<to-phone-number-1>` `<to-phone-number-2>` z numerami telefonów, do których chcesz wysłać wiadomość.

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

`setDeliveryReportEnabled`Metoda służy do konfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.

`setTag`Metoda służy do zastosowania znacznika do raportu dostarczania.

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

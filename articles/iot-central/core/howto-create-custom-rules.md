---
title: Rozszerzanie Azure IoT Central za pomocą niestandardowych reguł i powiadomień | Microsoft Docs
description: Jako deweloper rozwiązań skonfiguruj aplikację do IoT Central do wysyłania powiadomień e-mail, gdy urządzenie przestanie wysyłać dane telemetryczne. To rozwiązanie używa Azure Stream Analytics, Azure Functions i SendGrid.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: a65d9dbaed4d197c2e0843e73ff3f45b8678017e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864221"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozszerzanie usługi Azure IoT Central o niestandardowe reguły przy użyciu usług Stream Analytics, Azure Functions i SendGrid

W tym przewodniku przedstawiono, jako dewelopera rozwiązań, jak rozszerzyć aplikację IoT Central za pomocą niestandardowych reguł i powiadomień. W przykładzie pokazano wysyłanie powiadomienia do operatora, gdy urządzenie przestanie wysyłać dane telemetryczne. Rozwiązanie używa [zapytania](../../stream-analytics/index.yml) Azure Stream Analytics do wykrywania, kiedy urządzenie przestało wysyłać dane telemetryczne. Zadanie Stream Analytics używa usługi [Azure Functions](../../azure-functions/index.yml) do wysyłania wiadomości e-mail z powiadomieniami przy użyciu [usługi SendGrid.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)

W tym przewodniku pokazano, jak rozszerzyć IoT Central poza to, co już może zrobić z wbudowanymi regułami i akcjami.

Z tego przewodnika dowiesz się, jak:

* Przesyłanie strumieniowe danych telemetrycznych z IoT Central przy użyciu *ciągłego eksportu danych.*
* Utwórz zapytanie Stream Analytics, które wykrywa, kiedy urządzenie przestało wysyłać dane.
* Wyślij powiadomienie e-mail przy użyciu usługi Azure Functions i SendGrid.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central aplikacji

Utwórz aplikację IoT Central w witrynie [Azure IoT Central menedżer aplikacji](https://aka.ms/iotcentral) przy użyciu następujących ustawień:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa (Standard) |
| Szablon aplikacji | Analiza w sklepie — monitorowanie warunków |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj wartość domyślną lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Twoja Azure Active Directory dzierżawy |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region (Region) | Najbliższy region |

Na przykładach i zrzutach ekranu w tym artykule jest **Stany Zjednoczone** region. Wybierz bliską lokalizację i upewnij się, że wszystkie zasoby są tworzyć w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia termostatów, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [Azure Portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **DetectStoppedDevices,** która będzie zawierać inne zasoby, które utworzysz. Utwórz zasoby platformy Azure w tej samej lokalizacji, w IoT Central aplikacji.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [Azure Portal, aby utworzyć Event Hubs nazw](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybieranie nazwy przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="stream-analytics-job"></a>Zadanie usługi Stream Analytics

Użyj [Azure Portal, aby utworzyć zadanie Stream Analytics z](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę zadania |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Środowisko hostingu | Chmura |
| Jednostki przesyłania strumieniowego | 3 |

### <a name="function-app"></a>Aplikacja funkcji

Użyj [Azure Portal, aby utworzyć aplikację funkcji z](https://portal.azure.com/#create/Microsoft.FunctionApp) następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji    | Wybieranie nazwy aplikacji funkcji |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| System operacyjny | Windows |
| Plan hostingu | Plan zużycia |
| Lokalizacja | Wschodnie stany USA |
| Stos środowiska uruchomieniowego | .NET |
| Magazyn | Tworzenie nowego elementu |

### <a name="sendgrid-account-and-api-keys"></a>Konto SendGrid i klucze interfejsu API

Jeśli nie masz konta sendgrid, przed [](https://app.sendgrid.com/) rozpoczęciem utwórz bezpłatne konto.

1. Z menu Sendgrid Dashboard Settings (Ustawienia pulpitu nawigacyjnego usługi Sendgrid) w menu po lewej stronie wybierz pozycję **API Keys (Klucze interfejsu API).**
1. Kliknij **pozycję Utwórz klucz interfejsu API.**
1. Nazwij nowy klucz interfejsu API **AzureFunctionAccess.**
1. Kliknij **pozycję Utwórz & widok.**

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Zrzut ekranu przedstawiający tworzenie klucza interfejsu API SendGrid.":::

Następnie zostanie podany klucz interfejsu API. Zapisz ten ciąg do późniejszego użycia.

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Możesz skonfigurować aplikację IoT Central do ciągłego eksportowania danych telemetrycznych do centrum zdarzeń. W tej sekcji utworzysz centrum zdarzeń do odbierania danych telemetrycznych z IoT Central aplikacji. Centrum zdarzeń dostarcza dane telemetryczne do Stream Analytics do przetwarzania.

1. W obszarze Azure Portal przejdź do przestrzeni nazw usługi Event Hubs i wybierz **pozycję + Centrum zdarzeń**.
1. Nadaj centrum **zdarzeń nazwę centralnaeksuj** i wybierz **pozycję Utwórz**.

Twoja Event Hubs przestrzeń nazw wygląda jak na poniższym zrzucie ekranu: 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Komunikaty dziennika funkcji są wyświetlane na **panelu Dzienniki:**

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Dodawanie Stream Analytics zapytania

To rozwiązanie używa zapytania Stream Analytics do wykrywania, kiedy urządzenie przestaje wysyłać dane telemetryczne przez ponad 120 sekund. Zapytanie używa danych telemetrycznych z centrum zdarzeń jako danych wejściowych. Zadanie wysyła wyniki zapytania do aplikacji funkcji. W tej sekcji skonfigurujesz zadanie Stream Analytics zadań:

1. W obszarze Azure Portal przejdź do zadania Stream Analytics, w  obszarze Topologia zadań wybierz pozycję Dane **wejściowe,** wybierz pozycję + **Dodaj wejście** strumienia, a następnie wybierz pozycję **Centrum zdarzeń.**
1. Skorzystaj z informacji w poniższej tabeli, aby skonfigurować dane wejściowe przy użyciu utworzonego wcześniej centrum zdarzeń, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wejściowych | centraltelemetry |
    | Subskrypcja | Twoja subskrypcja |
    | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń |
    | Nazwa centrum zdarzeń | Użyj istniejącej — **centralexport** |

1. W **obszarze Topologia zadań** wybierz pozycję Dane **wyjściowe,** wybierz pozycję + **Dodaj,** a następnie wybierz **pozycję Funkcja platformy Azure.**
1. Użyj informacji w poniższej tabeli, aby skonfigurować dane wyjściowe, a następnie wybierz pozycję **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wyjściowych | powiadomienie e-mail |
    | Subskrypcja | Twoja subskrypcja |
    | Aplikacja funkcji | Twoja aplikacja funkcji |
    | Funkcja  | HttpTrigger1 |

1. W **obszarze Topologia zadań** wybierz pozycję **Zapytanie** i zastąp istniejące zapytanie następującym kodem SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Wybierz pozycję **Zapisz**.
1. Aby uruchomić zadanie Stream Analytics, wybierz pozycję **Przegląd,** a następnie **pozycję Uruchom,** a następnie pozycję **Teraz,** a następnie **pozycję Uruchom:**

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Zrzut ekranu przedstawiający Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w programie IoT Central 

W [Azure IoT Central menedżera aplikacji](https://aka.ms/iotcentral) przejdź do utworzonej IoT Central aplikacji.

W tej sekcji skonfigurujesz aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń. Aby skonfigurować eksport:

1. Przejdź do strony **Eksport danych,** wybierz **pozycję + Nowy**, a następnie wybierz **Azure Event Hubs**.
1. Skonfiguruj eksport przy użyciu następujących ustawień, a następnie wybierz pozycję **Zapisz:** 

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportuj do Event Hubs |
    | Enabled (Włączony) | Włączone |
    | Typ danych do wyeksportowania | Telemetria |
    | Wzbogacenia | Wprowadź żądany klucz/wartość sposobu zorganizowania wyeksportowanych danych | 
    | Element docelowy | Utwórz nowy i wprowadź informacje dotyczące miejsca, w którym dane zostaną wyeksportowane |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Zrzut ekranu przedstawiający eksportowanie danych.":::

Przed kontynuowaniem poczekaj, aż stan eksportu będzie się zmienił **na** Uruchomiony.

## <a name="test"></a>Testowanie

Aby przetestować rozwiązanie, możesz wyłączyć ciągły eksport danych z usługi IoT Central do symulowanych zatrzymanych urządzeń:

1. W aplikacji IoT Central przejdź do strony **Eksportowanie** danych i wybierz konfigurację Eksportuj **do Event Hubs** eksportu.
1. Ustaw **opcję Włączone** na **Wyłączone,** a następnie wybierz **pozycję Zapisz.**
1. Po co najmniej dwóch  minutach adres e-mail Do otrzyma co najmniej jedną wiadomość e-mail, która wygląda podobnie do następującej przykładowej zawartości:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Uporządkowanie

Aby uporządkować po tym how-to i uniknąć niepotrzebnych kosztów, usuń grupę zasobów **DetectStoppedDevices** w Azure Portal.

Możesz usunąć aplikację IoT Central na **stronie** Zarządzanie w aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku po tym przewodniku opisano, jak:

* Przesyłanie strumieniowe danych telemetrycznych z IoT Central przy użyciu *ciągłego eksportu danych.*
* Utwórz zapytanie Stream Analytics, które wykrywa, kiedy urządzenie przestało wysyłać dane.
* Wyślij powiadomienie e-mail przy użyciu usługi Azure Functions i SendGrid.

Teraz, gdy wiesz już, jak tworzyć niestandardowe reguły i powiadomienia, sugerowanym następnym krokiem jest nauka rozszerzania usługi [Azure IoT Central przy użyciu analizy niestandardowej.](howto-create-custom-analytics.md)

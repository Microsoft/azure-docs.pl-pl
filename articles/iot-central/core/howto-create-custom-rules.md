---
title: Zwiększanie IoT Central platformy Azure przy użyciu reguł niestandardowych i powiadomień | Microsoft Docs
description: Jako deweloper rozwiązań Skonfiguruj aplikację IoT Central do wysyłania powiadomień e-mail, gdy urządzenie przestanie wysyłać dane telemetryczne. To rozwiązanie używa Azure Stream Analytics, Azure Functions i SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 824308b66803d2dfa05383ff06ce97c48626619d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557568"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozszerzanie usługi Azure IoT Central o niestandardowe reguły przy użyciu usług Stream Analytics, Azure Functions i SendGrid

Ten przewodnik zawiera informacje o sposobie rozbudowywania aplikacji IoT Central przy użyciu niestandardowych reguł i powiadomień. W przykładzie pokazano Wysyłanie powiadomienia do operatora, gdy urządzenie przestanie wysyłać dane telemetryczne. Rozwiązanie używa zapytania [Azure Stream Analytics](../../stream-analytics/index.yml) w celu wykrycia, kiedy urządzenie zatrzymało wysyłanie danych telemetrycznych. Zadanie Stream Analytics używa [Azure Functions](../../azure-functions/index.yml) do wysyłania powiadomień e-mail przy użyciu [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

W tym przewodniku opisano sposób, w jaki można rozbudować IoT Central poza tym, co można już zrobić przy użyciu wbudowanych reguł i akcji.

W tym przewodniku krok po kroku dowiesz się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportu danych*.
* Utwórz kwerendę Stream Analytics, która wykrywa, kiedy urządzenie zatrzymało wysyłanie danych.
* Wyślij powiadomienie e-mail przy użyciu usług Azure Functions i SendGrid.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>Aplikacja IoT Central

Utwórz aplikację IoT Central w witrynie programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) przy użyciu następujących ustawień:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa |
| Szablon aplikacji | Analiza warunków w sklepie — monitorowanie |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj domyślny lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawa Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region (Region) | Najbliższy region |

Przykłady i zrzuty ekranu w tym artykule używają regionu **Stany Zjednoczone** . Wybierz lokalizację blisko siebie i upewnij się, że wszystkie zasoby zostały utworzone w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia z termostatem, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [Azure Portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **DetectStoppedDevices** , aby zawierała inne utworzone zasoby. Utwórz zasoby platformy Azure w tej samej lokalizacji, w której znajduje się aplikacja IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [Azure Portal, aby utworzyć przestrzeń nazw Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="stream-analytics-job"></a>Zadanie usługi Stream Analytics

Użyj [Azure Portal, aby utworzyć zadanie Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę zadania |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| Lokalizacja | Wschodnie stany USA |
| Środowisko hostingu | Chmura |
| Jednostki przesyłania strumieniowego | 3 |

### <a name="function-app"></a>Aplikacja funkcji

Użyj [Azure Portal, aby utworzyć aplikację funkcji](https://portal.azure.com/#create/Microsoft.FunctionApp) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa aplikacji    | Wybierz nazwę aplikacji funkcji |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | DetectStoppedDevices |
| System operacyjny | Windows |
| Plan hostingu | Plan zużycia |
| Lokalizacja | Wschodnie stany USA |
| Stos środowiska uruchomieniowego | .NET |
| Magazyn | Tworzenie nowego elementu |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid konta i kluczy interfejsu API

Jeśli nie masz konta SendGrid, przed rozpoczęciem Utwórz [bezpłatne konto](https://app.sendgrid.com/) .

1. Z ustawień pulpitu nawigacyjnego SendGrid w menu po lewej stronie wybierz pozycję **klucze interfejsu API**.
1. Kliknij przycisk **Utwórz klucz interfejsu API.**
1. Nazwij nowy klucz interfejsu API **AzureFunctionAccess.**
1. Kliknij przycisk **utwórz & widok**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Zrzut ekranu przedstawiający klucz interfejsu API tworzenia SendGrid.":::

Następnie zostanie nadany klucz interfejsu API. Zapisz ten ciąg do późniejszego użycia.

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Można skonfigurować aplikację IoT Central, aby ciągle eksportować dane telemetryczne do centrum zdarzeń. W tej sekcji utworzysz centrum zdarzeń, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń dostarcza dane telemetryczne do zadania Stream Analytics do przetworzenia.

1. W Azure Portal przejdź do przestrzeni nazw Event Hubs i wybierz pozycję **+ centrum zdarzeń**.
1. Nazwij **centralexport** centrum zdarzeń, a następnie wybierz pozycję **Utwórz**.

Przestrzeń nazw Event Hubs wygląda następująco: 

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

Komunikaty dziennika funkcji są wyświetlane w panelu **dzienniki** :

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Dodaj zapytanie Stream Analytics

To rozwiązanie używa zapytania Stream Analytics w celu wykrycia, kiedy urządzenie przestanie wysyłać dane telemetryczne przez ponad 120 sekund. Zapytanie używa danych telemetrycznych z centrum zdarzeń jako dane wejściowe. Zadanie wysyła wyniki zapytania do aplikacji funkcji. W tej sekcji skonfigurujesz zadanie Stream Analytics:

1. W Azure Portal przejdź do zadania Stream Analytics, w obszarze **topologia zadania** wybierz pozycję **wejścia**, wybierz pozycję **+ Dodaj dane wejściowe strumienia**, a następnie wybierz pozycję **centrum zdarzeń**.
1. Skorzystaj z informacji podanych w poniższej tabeli, aby skonfigurować dane wejściowe przy użyciu utworzonego wcześniej centrum zdarzeń, a następnie wybierz pozycję **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wejściowych | centraltelemetry |
    | Subskrypcja | Twoja subskrypcja |
    | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń |
    | Nazwa centrum zdarzeń | Użyj istniejącego — **centralexport** |

1. W obszarze **topologia zadania** wybierz pozycję dane **wyjściowe**, wybierz pozycję **+ Dodaj**, a następnie wybierz pozycję **Funkcja platformy Azure**.
1. Skorzystaj z informacji podanych w poniższej tabeli, aby skonfigurować dane wyjściowe, a następnie wybierz pozycję **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Alias danych wyjściowych | emailnotification |
    | Subskrypcja | Twoja subskrypcja |
    | Aplikacja funkcji | Aplikacja funkcji |
    | Funkcja  | HttpTrigger1 |

1. W obszarze **topologia zadania** wybierz pozycję **zapytanie** i Zastąp istniejące zapytanie następującym SQL:

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
1. Aby uruchomić zadanie Stream Analytics, wybierz pozycję **Przegląd**, a następnie pozycję Rozpocznij **, a następnie** pozycję **Rozpocznij**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Zrzut ekranu przedstawiający Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w IoT Central 

W witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) przejdź do utworzonej aplikacji IoT Central.

W tej sekcji skonfigurujesz aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń. Aby skonfigurować eksport:

1. Przejdź do strony **eksport danych** , wybierz pozycję **+ Nowy**, a następnie opcję **Azure Event Hubs**.
1. Aby skonfigurować eksport, użyj następujących ustawień, a następnie wybierz pozycję **Zapisz**: 

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportuj do Event Hubs |
    | Enabled (Włączony) | Włączone |
    | Typ danych do wyeksportowania | Telemetria |
    | Wzbogaceń | Wprowadź żądany klucz/wartość, w jaki sposób mają być zorganizowane eksportowane dane | 
    | Element docelowy | Utwórz nową i wprowadź informacje o tym, gdzie zostaną wyeksportowane dane |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Zrzut ekranu przedstawiający eksport danych.":::

Przed kontynuowaniem Zaczekaj, aż stan eksportu zostanie **uruchomiony** .

## <a name="test"></a>Testowanie

W celu przetestowania rozwiązania można wyłączyć funkcję ciągłego eksportowania danych z IoT Central, aby symulować zatrzymane urządzenia:

1. W aplikacji IoT Central przejdź do strony **eksport danych** i wybierz pozycję **Eksportuj do Event Hubs** konfiguracja eksportu.
1. Ustaw wartość **włączone** na **off** , a następnie wybierz pozycję **Zapisz**.
1. Po upływie co najmniej dwóch minut **adres e-mail otrzymuje** jedną lub więcej wiadomości e-mail, które wyglądają jak następująca Przykładowa zawartość:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Uporządkowanego

Aby uporządkowanego się po tym, jak to zrobić, i uniknąć niepotrzebnych kosztów, Usuń grupę zasobów **DetectStoppedDevices** w Azure Portal.

Aplikację IoT Central można usunąć ze strony **zarządzania** w ramach aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku krok po kroku przedstawiono następujące informacje:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportu danych*.
* Utwórz kwerendę Stream Analytics, która wykrywa, kiedy urządzenie zatrzymało wysyłanie danych.
* Wyślij powiadomienie e-mail przy użyciu usług Azure Functions i SendGrid.

Teraz, gdy wiesz już, jak tworzyć reguły i powiadomienia niestandardowe, sugerowanym następnym krokiem jest zapoznanie się z tematem jak [zwiększyć możliwości usługi Azure IoT Central przy użyciu analizy niestandardowej](howto-create-custom-analytics.md).
